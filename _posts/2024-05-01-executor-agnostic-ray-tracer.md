---
layout: post
title:  "Writing a Executor Agnostic Parallel Ray Tracer"
date:   2024-05-01 11:30:00 +0530
categories: jekyll update
---

First of all, Ray Tracer is not a daily use term, so let's talk about that.
The motive of a Ray Tracer is to render real looking 3D images.
The process is as follows:
- We define the world (how objects are there)
- We define from where and with what orientation we are looking at world
- Ray tracer would render the image of how world would look like from that position and orientation

How does it work?
- In real life light rays come from all direction to camera and camera senses
that and makes image of that. However, this process is really inefficient for
ray tracing purposes.
- The reason for that is we need to process all possible light sources
and rays that are not needed.
- So, ray tracer works just opposite. It sends the rays in opposite direction
and rays hit objects and based on nature of objects, it does
reflection/refraction and based on that we determine what would
the color of a specific ray.

I would not go with very internal details of how to write a ray tracer. There
are really good resources for the same on internet.

However, I would be focusing on certain aspect of coding design of ray tracer.
Most of it is going to revolve around concurrency and generic algorithms.

## Already Existing Ray Tracers

There are many already existing really good ray tracers. So, why to go with
something new? Let's talk about those:

- Most of the ray tracers provide where the rendered image would be displayed (like a
  window or something)
- Different ray tracers are programmed to work with specific executors like
  thread pool or GPU or something else.

Well above both of points make sense.

- You need to render your image somewhere. And rendering image on a window
is really different from rendering it in JPG image format.
- You need to execute your algorithm on some executor. And execution on CPU
is very different than execution on GPU.

There are other really good aspects of ray tracer design but today I want to
talk only about above 2 points.

## Why to consider these 2 points?

A single line answer: These choices make your ray tracer a very specific
application.

I am a big fan of saying Data Structures + Algorithms = Programs

And ray tracing is a really good analytical algorithm. But let's think once
- Is my algorithm dependent on QT window / Electron Window / something else?
- Is my algorithm dependent on GPU / CPU / something else?

My point is, it doesn't matter if I render some image on QT window or electron
window or if I execute rendering functions on GPU or CPU, ray tracing algorithm
is still the same.

And thus there should not be 4 different ray tracers according to QT/electorn
and GPU/CPU but one.

## Alex Stepanov to the picture

I would need to bring Alex Stepanov to the picture. For those who don't
know him, he is the creator of STL (Standard Template Library) of C++.
And STL is a brilliant piece of work. Its so brilliant and timeless.

With STL he introduced the notion of generic programming. He wrote the algorithms
in its most generic form. If anyone confused, generic programming has nothing
to do with templates. Generic programming is all about the mathematical structures
lying behind those algorithms and depending on those mathematical properties,
instead of some concrete choices.

And really C++ STL is all about that. That should be motivation when we try
to write some code ourselves.

## Where to render image?

Well let's think of our algorithm.

We would send multiple rays through a viewport
and after those ray interact with world, based on color of those rays we
would decide color of pixel of image.

Also, I don't want to enforce any postcondition on
ordering of which pixel's color would be computed first.
So, that we can color multiple pixels at same time or we can color pixels in
a specific order that needs less computation maybe.

That shows, we can randomly access pixels of image (i.e., any (x, y)).
Also, we don't only need read access but write access.

So, I define a concept of RandomAccessImage as:
```cpp
template <typename Image>
concept RandomAccessImage =
    requires(Image const &img, int x, int y, color_t const &color) {
      { width(img) } -> std::same_as<int>;
      { height(img) } -> std::same_as<int>;
      { pixel_at(img, x, y) } -> std::same_as<color_t>;
    };
```

That means a random access image should provide me 3 functions:
- width
- height
- pixel_at

But still, we don't know how to write:
```cpp
template <typename Image>
concept OutputRandomAccessImage =
    RandomAccessImage<Image> &&
    requires(Image &img_mut, int x, int y, color_t const &color) {
      { set_pixel_at(img_mut, x, y, color) };
    };
```

So, an OutputRandomAccessImage is a RandomAccessImage with write access.

Now any structure that follows these concept is an OutputRandomAccessImage.
Now, we can use C++ ADL to already existing libraries to follow the same
concept. Or we can write a wrapper clas on top of those libraries that
follows same concept.

So, our algorithm depends on the concept of OutputRandomAccessImage not any
specific library or window implementation.

## Where to execute rendering functions?

The above problem was kind of simple and intuitive. However, now is a really
hard problem.

Why do we even want to execute the algorithm of thread pool or GPU? The reason
is quite simple. The algorithm is quite expensive and thus we need to execute
fast. So we want to use powers of our machine to accelerate it.

To my knowledge no already existing ray tracers are designed to be executor agnostic.
So a specific ray tracer is programmed to work on thread pool or GPU or something
else. But there is not unification on that.


But is there some mathematical structure that can help us here. And the answer
is yes.

Let's again think of our algorithm:

- We would send multiple rays through a viewport.
- Each of those rays would interact with world (and not change world).
- And color of each rays would decide color of a specific (different) pixel.

And we have implied no postcondition on ordering of computation of color
of each ray. Thus the computation of color of each ray is independent of other
computations.

Well this brings us to parallelism.

### What is parallelism?

**Parallelism is NOT about executing multiple tasks at same time**.

Parellelism revolves around the idea that if there are multiple tasks independent
of each other. Then they are not going to touch each other at any point. And
thus those tasks are parallel.

This enables the executor to reorder the execution of these tasks to make it
optimal. And executors can potentially execute multiple tasks at same time
because parallelism put no postconditions on ordering of execution of tasks.

A thread of execution providing parallel forward progress guarantees is not
guaranteed to make progress if it has not yet executed any execution step; once
it has executed a step, the implementation will ensure that the thread will
eventually make progress for as long as it has not terminated.

This effectively makes the same forward progress requirements as for a
concurrent forward progress guarantee once the parallel-forward-progress
thread of execution is started, but does not specify a requirement for when to
start this thread of execution; the latter will typically be specified by the
entity that creates this thread of execution. For example, executing tasks
from a set of tasks in an arbitrary order, one after the other, satisfies the
requirements of parallel forward progress for these tasks.

For example a thread pool with single thread provides parallel forward
progress guarantee.

### Parallelism provides WEAKER forward progress guarantee than Concurrency

To understand the same we would need to know what is concurrent forward progress
guarantee.

If a thread of execution provides concurrent forward progress guarantee, it
is guaranteed that thread of execution will execute its first/next step
eventually.

While in parallelism thread of execution is not required to execute its first
step at all. Well this may look weird but this totally makes sense.

In concurrency, its basically saying that tasks can be dependent on each other(not parallel).
This implies one task can potentially block on other tasks. And concurrency
can handle that but parallelism can't. So, its just a mathematical way of
saying tasks can't block on each other (for parallelism).

But weaker forward progress guarantee means more freedom to executors to
execute the tasks in optimal way.

### What do we need?

Let's think of our scenario, our tasks are independent, we don't need
an executor with stronger guarantee that can handle blocking and all.

So, we can take any executor/scheduler that provides parallel forward progress
guarantee. And scheduler that provides concurrent forward progress guarantee
automatically provides forward progress guarantee.

And thus mathematically our algorithm doesn't need to depend on any CPU/GPU
it depends on an executor that provides parallel forward progress guarantee.

### An executor abstraction

Well we need a homogeneous interface to program on the same. That is provided
by [P2300](https://wg21.link/p2300) proposal of C++ for C++26.
Nvidia's [stdexec](https://github.com/NVIDIA/stdexec) is a great implementation for the proposal.

A bounded thread pool is a good example of parallel executor. One of such
forward progress guarantee is provided by libdispatch. (Warning: Don't try to write
thread pool, etc at home). Here is my contribution for [libdispatch executor](https://github.com/NVIDIA/stdexec/pull/1258)
for nvidia that the codebase uses in its example.

However, the algorithm depends on a this scheduler concept with parallel forward
progress guarantee precondition rather than depending on any specific executor.


## Conclusion

Writing programs are great. But thinking programs in terms of Data Structures
and Algorithms are really fun.

Most of the times algorithms depend on some mathematical structures.
We should thrive for discovering those mathematical
structures and use them in code rather than using some specific choice.

Discovering mathematical structures can take time. For example OutputRandomAccessImage
was easy to discover. However, parallel forward progress executor was quite hard one.
So, while and after writing algorithms think that are we depending on something
that is not really necessary for the algorithm and is that property making
algorithm less generic.

Programming is hard, so try to have fun. Its a big journey. Fun and Journey both are important for the life.
