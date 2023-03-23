---
layout: post
title:  "Design by contract (A systematic approach to software design)"
date:   2023-03-23
categories: jekyll update
---

Design by contract is an approach of software design, where we formally define
the contract for each function and class (in addition to ADT). By strictly
following the contract, we can maintain the correctness of software.

## Contract

Design by contract enforces following requirements to be formally stated by
developer:

- Precondition of function
- Postcondition of function
- Class Invariant of function

Let's talk about these 3 in detail with example.

### Precondition of function

Let's start with an example. Consider the following code:

```cpp
int add(int a, int b){
  return a + b;
}
```

What is the domain of this function? An amateur eye would say, its a set of
pair of int. Let's represent that with (int, int). However, is it true?

A more trained eye would say, its a set of pair of int, such that their
addition does not lead to integer overflow. In programming languages, domain
are not exactly set of tuple of argument of function. Instead, the domain of
function is subset of that set such that it satisfies the precondition of
operation.

But what if someone passes an argument that doesn't satisfy precondition?

- Design by contract states that, it should be considered as bug and software
  should be considered in undefined behavior state from that point of time.
  The best that can happen is software crashes immediately after this (However,
  it is not guaranteed).

- It states that, it is responsibility of caller to pass only those argument to
  function that satisfies the precondition and not the responsibility of callee
  function to check this.

So, now above code transforms to:

```cpp
// Precondition: addition of (a, b) should not lead to integer overflow
int add(int a, int b){
  return a + b;
}
```

Let's denote S with the set defined by function argument data type. In this
case its all (int, int).

Let's denote P with the subset of the S that satisfies the precondition of
operation.

NOTE: Its obvious that passing argument that is element of S, is automatically
a precondition to function. And is checked at compile time in statically typed
language.

This also suggests that its better if we can write complete functions instead
of partial functions.

Complete functions are those functions, such that the
full set defined by function argument data type satisfies the precondition of
operation (i.e., P = S).

Other functions are called partial functions.

In case of complete functions, its easy to see that our type system would
automatically restrict us from passing illegal arguments. However, its actually
not very easy to write complete functions and sometimes impossible to do so
(like the above code example). So, one should pay a good amount of time to
think over this and can be achieved by leaverging the use of type system.

### Postcondition of function

Postcondition of function are the things function guarantees, would be done
if function was able to run successfully.

So, in the above code example, its obvious that postcondition is that
function would return sum of a and b.

So, above code changes like this:

```cpp
// Precondition: addition of (a, b) should not lead to integer overflow
// Postcondition: should return addition of a and b
int add(int a, int b){
  return a + b;
}
```

However, postconditions are not always just about returned value. It consists
of following things:

- Returned value
- Side effects
- Errors

Let's see another example to understand the same:

```cpp
// Comparator type is a function type that takes 2 arguments of same type, same
// as value type of r, and returns a boolean. For readability, implementation
// details and actual type details are hidden.

// Precondition: cmp should follow strict weak ordering (strict trichotomy law)
void sort(RandomAccessRange& r, Comparator cmp){
  // sorting logic here...
}
```

The above code has the postcondition that after successfull completion of
the above function, the range r should be sorted according the order defined
by cmp comparator. This is something not returned from function but is a side
effect.

So, above code transforms to:

```cpp
// Precondition: cmp should follow strict weak ordering (strict trichotomy law)
// Postcondition: r should be sorted according to order defined by cmp
void sort(RandomAccessRange& r, Comparator cmp){
  // sorting logic here...
}
```

But when does error happen? Errors occur when, due to some reason the function
is not able to satisfy the postcondition. And all the possible errors should
also be stated in postconditions.

Its really important to note that:

**Errors are about POSTCONDITIONS not preconditions.** Errors are recoverable
situation, but precondition violation is a bug. (There is concept of safety
that talks about precondition violation, but we would not talk about that here.
Best thing to do is to terminate the program.)

There are many ways to express the errors. We would use exceptions to express
errors in the article as they are pretty common in many languages. Other ways
include: error codes (well known), encoding errors in result type (example:
std::expected [My C++23 implementation](https://github.com/RishabhRD/expected))

Let's look at some examples to this:

```cpp
// Preconditions:
//   - d should respect OS limits for socket

// Postconditons:
//   - Writes all bytes in d to s
//   - In case of timeout, throws TimeOutException
//   - In case of closed socket, throws ClosedSocketException
void writeToSocket(Socket& s, Data& d){
  // logic here...
}
```

Having a this kind of well defined contract for errors helps to avoid defensive
programming with try/catch blocks.

But what is a good error? A good error consists of following information:

- What postconditions were not satisfied?
- Why postconditions were not satisfied?
- Relevant data about error (e.g., how much time socket waited for in above case)

### Class invariants

Class invariants are preconditions AND postconditions for member functions /
methods of a class.

Let's look at an example to understand this. Before that let's have a look at
following definitions for supporting our example:

- std::vector<T> is a dynamic array consisting values of type T.
- zipped_vector<T1, T2> is a collection, that zips 2 vectors, i.e., its like a
  vector of pairs of T1 and T2.

Let's write the zipped_vector class for our purposes:

```cpp
template <typename T1, typename T2>
class zipped_vector {
  private:
  vector<T1> v1;
  vector<T2> v2;

  public:
  zipped_vector(vector<T1> v1, vector<T2> v2){ // Constructor
    this.v1 = std::move(v1);
    this.v2 = std::move(v2);
  }

  void push_back(T1 val1, T2 val2){
    v1.push_back(val1);
    v2.push_back(val2);
  }

  void pop_back(){
    v1.pop_back();
    v2.pop_back();
  }

  int size() const {
    return v1.size();
  }
  // Other member functions here
};
```

From the description of zipped_vector and interface for zipped_vector, its very
obvious to guess that, class invariant of the class is that, v1 and v2 should
always be of same length. (Then only, it would be like array of pairs of 2
vectors' data)

So, above definition changes:

```cpp
// Class invariants:
//   - v1 and v2 should be of same length
template <typename T1, typename T2>
class zipped_vector {
  private:
  vector<T1> v1;
  vector<T2> v2;
  // Rest same as above...
```

Class invariants are automatically preconditions and postconditions of each
member function of class (in addition to their own preconditions and
postconditions).

Thus before every operations and after every operation, v1 and v2 should be of
same length.

However, class invariants also complicates the error handling. Let's understand
how?

But first let's call const member functions, the member functions that doesn't
mutate their data members. Its quite easy to see that const member functions
are not going to violate the class invariants.

But what about the member functions that mutate the data members. Let's look
at push_back function:

```cpp
  // Rest here...
  void push_back(T1 val1, T2 val2){
    v1.push_back(val1);
    v2.push_back(val2);
  }
  // Rest here...
```

Let's deep dive in contract of std::vector's push_back function. According
to it, it can throw MemoryExhaustedException when there is not enough memory.
std::vector's pop_back contract defines that it doesn't throw any exception.

Let's consider the case v1's push_back was successfull, however v2 push_back
failed with exception. Here we can't simply just mention the exception in
postcondition. In this case, this operation can lead to violation of class
invariant and thus need to be handled. So, the code changes like this:

```cpp
  // Rest here...

  // Postconditions:
  //   - pushes val1 to v1 and val2 to v2 on success
  //   - throws MemoryExhaustedException on case of memory exhaust condition
  //     (and no data is pushed)
  void push_back(T1 val1, T2 val2){
    v1.push_back(val1);
    try{
      v2.push_back(val2);
    }catch(MemoryExhaustedException e){
      v1.pop_back();
      throw e;
    }
  }

  // Rest here...
```

There is something to notice here, in this case instead of having defensive
try/catch statement, now try/catch statement is there for contract satisfation
part. This makes software design more systematic and correct.

Class invariants introduce the concept of exception safety. There are 3
kinds of exception guarantees of a member function:

- No exception guarantee
- Basic exception guarantee
- Strong exception guarantee

#### No exception guarantee

This doesn't guarantee anything after exception during execution of member
function. Ofcourse, should not be used in production.

#### Basic exception guarantee

This guarantees that after member function execution, in case of any
exception, the class invariant would be intact.

#### Strong exception guarantee

This guarantees that after member function execution, in case of any exception,
the object state would be reverted back to old state (i.e., state before
calling the member function). Obviously, class invariants would automatically
be intact.

Choice of exception guarantee for a member function depends on usecase of
application. In the above example, push_back satisfies the strong exception
guarantee.

The set member functions that directly access the data member of class is
called **basis operations of class**. Its easy to see that basis should be kept small for
having robust codebase.

For maintaining basic exception guarantee we only need to consider mutating
basis operations of class. We don't need to worry about non-basis operations
and const basis operations. For example:

```cpp
  // Preconditions:
  //   - val1s and val2s should be of same length
  // Postconditions:
  //   - Pushes all content of val1s in v1 in order and val2s in v2 in order
  //   - Throws MemoryExhaustedException in case of memory exhaustion
  void push_back_range(vector<T1> val1s, vector<T2> val2s){
    int const n = val1s.size();
    for(int i = 0; i < n; ++i){
      this.push_back(val1s[i], val2s[i]);
    }
  }
```

It's quite easy to see that the following operation would automatically
maintain the basic exception guarantee because of it does not rely on access to
data members.

It should be noted here that, our push_back function satisfies strong exception
guarantee but it doesn't mean, push_back_range satisfies the same.
push_back_range only satisfies basic exception guarantee. Additional work
should be done, if we want strong exception guarantee for this operation.

If we are unable to clearly state the class invariants, then its a clear
indication that, the class is poorly design and should be rethought.

So, our code looks like this:

```cpp
// Class invariants:
//   - v1 and v2 should be of same length
template <typename T1, typename T2>
class zipped_vector {
  private:
  vector<T1> v1;
  vector<T2> v2;

  public:
  // Precondition: v1_arg, v2_arg should be of same length
  zipped_vector(vector<T1> v1_arg, vector<T2> v2_arg){ // Constructor
    this.v1 = std::move(v1_arg);
    this.v2 = std::move(v2_arg);
  }

  // Postconditions:
  //   - pushes val1 to v1 and val2 to v2 on success
  //   - throws MemoryExhaustedException on case of memory exhaust condition
  //     (and no data is pushed)
  void push_back(T1 val1, T2 val2){
    v1.push_back(val1);
    try{
      v2.push_back(val2);
    }catch(MemoryExhaustedException e){
      v1.pop_back();
      throw e;
    }
  }

  // Preconditions:
  //   - v1, v2 should not be empty
  // Postconditions:
  //   - pops last element of v1 and pops last element of v2
  void pop_back(){
    v1.pop_back();
    v2.pop_back();
  }

  // Postconditions:
  //   - Returns size of v1
  int size() const {
    return v1.size();
  }
  // Other member functions here
};
```

#### Top Down approach to error handling

The above approach was the bottom up approach to error handling. There also
exists a top-down approach for error handling where we weaken our class
invariants and strongen our precondition of member function to put more
responsibility on caller.

In top down approach:

- We weaken our class invariants by making it:
  `invalid_state() || already_existing_invariants()`
- We strongen our every member function's precondition by making it:
  `valid_state() && already_exising_preconditions()`

We explicitly add invalid_state to the class. Invalid state refers to the state
where only to operations are valid: assignment and destruction.

This puts more responsibility to the caller. It greatly simplifies the basic
exception guarantee of operation. Considering the push_back function again,
now it looks like:

```cpp
  // Precondition:
  //   - valid_state()
  // Postconditions:
  //   - pushes val1 to v1 and val2 to v2 on success
  //   - throws MemoryExhaustedException on case of memory exhaust condition
  void push_back(T1 val1, T2 val2){
    v1.push_back(val1);
    v2.push_back(val2);
  }
```

Now, in case of v1's push_back being successfull and v2's push_back being ending
with exception, would lead to invalid_state that is an invariant to class.
Now, it is the responsibility of caller to only call the operation iff
class is in valid_state().

Now our code looks like:

```cpp
// Class invariants:
//   - invalid_state() || (v1 and v2 should be of same length)
template <typename T1, typename T2>
class zipped_vector {
  private:
  vector<T1> v1;
  vector<T2> v2;

  public:
  // Preconditions:
  //   - v1_arg, v2_arg should be of same length
  zipped_vector(vector<T1> v1_arg, vector<T2> v2_arg){ // Constructor
    this.v1 = std::move(v1_arg);
    this.v2 = std::move(v2_arg);
  }

  // Precondition:
  //   - valid_state()
  // Postconditions:
  //   - pushes val1 to v1 and val2 to v2 on success
  //   - throws MemoryExhaustedException on case of memory exhaust condition
  void push_back(T1 val1, T2 val2){
    v1.push_back(val1);
    v2.push_back(val2);
  }

  // Preconditions:
  //   - valid_state()
  //   - v1, v2 should not be empty
  // Postconditions:
  //   - pops last element of v1 and pops last element of v2
  void pop_back(){
    v1.pop_back();
    v2.pop_back();
  }

  // Preconditions:
  //   - valid_state()
  // Postconditions:
  //   - Returns size of v1
  int size() const {
    return v1.size();
  }
  // Other member functions here
};
```

## Conclusion

- Design by contract makes our software design systematic and enforces on
  correctness of application by formally specifying requirements and enforcing
  them.

- This solves the problem of fear of error and defensive try/catch blocks.

- We should think, how many times we care on these contract while developing
  software, if not we should start doing so.

  
