 <br>
 <span style="font-size: 40px;">
 <center>
 <b>
Rishabh Dwivedi
</b>
</center>
</span>

 <span style="font-size: 14px;">
 <center>
<a href="https://github.com/RishabhRD"><b>Github (107 followers) </b></a>&nbsp;&nbsp;&nbsp;&nbsp;<a href="https://leetcode.com/RishabhRD"><b>Leetcode (Guardian: 2205 peak rating) </b></a>&nbsp;&nbsp;&nbsp;&nbsp;<b>rishabhdwivedi17@gmail.com </b>&nbsp;&nbsp;&nbsp;&nbsp;<b>+918090372355</b>
</center>
</span>

### Education

- B.Tech in Computer Science, **IIIT Dharwad, CGPA: 9.1**, Graduated: May 2022

  (Awarded with **<ins>Director's Gold Medal for Best Outgoing Student</ins>**)

### Experience
- **Zeta \[SDE-1\] (July 2023 - present)**
  - Created a mircoservice from scratch to monitor high priority users.
  - Worked upon optimizing legacy apis.
  - Reduced API taking 12 second time to 500ms by database optimizations.

- **Goodworker Technologies \[SDE-1\] (Jun 2022 - May 2023)**
  - Implemented various frameworks for goodworker backend's mircorservices.
  - Implemented a task library from scratch that handled scheduling of async tasks.
  - Implemented an Authorization microservice from scratch with **OAuth2.0 spec.**
  - Tech stack: **Typescript, Node.js, rxjs, OAuth2.0, OpenID Connect**

### Open Source & ISO C++ Standard contributions

- **[std::expected](https://github.com/RishabhRD/expected) (Part of C++23 now)**: Implemented the ISO standard
  C++ proposal **[P2505](http://wg21.link/p2505)** in C++20. The implementation
  is also acknowledged in the proposal. The motive of std::expected is to
  introduce a functional way of error handling in C++ standard library.

- **[facebookexperimental/libunifex (Proposed for C++26)](https://github.com/facebookexperimental/libunifex)**: Implemented many unimplemented async
  algorithms proposed in **[P2300](https://wg21.link/p2300)** in **facebook's**
  libunifex library from scratch. Thus also got my **copyright** on those files in
  facebook's codebase. libunifex implements P2300's executors proposal
  (targetting C++26) that proposes methodology of generic execution of tasks
  over scheduler and proposes the idea of structured concurrency.

  PR links: **[into_variant
  algorithm](https://github.com/facebookexperimental/libunifex/pull/350)**,
  **[upon\_\*
  algorithm](https://github.com/facebookexperimental/libunifex/pull/333)**,
  **[bulk
  algorithm](https://github.com/facebookexperimental/libunifex/pull/354)**,
  **[get_completion_scheduler
  algorithm](https://github.com/facebookexperimental/libunifex/pull/415)**

- **[Nvidia/stdexec](https://github.com/NVIDIA/stdexec)**: Implemented libdispatch scheduler
  from scratch for Nvidia's stdexec library.
  It follows the scheduler concept of **[P2300](https://wg21.link/p2300)**
  proposal for C++26. The scheduler schedules task on libdispatch queue and
  have customized implementation of bulk algorithm for utilizing parallelism
  of machine. Profiling shows the scheduler has 97.9% CPU utilization in
  compare to already exisiting static thread pool that has 79% CPU utilization.

  PR links: **[libdispatch scheduler](https://github.com/NVIDIA/stdexec/pull/1258)**

### Publications / Journals

- **[Denial of ARP Spoofing in SDN and NFV enabled Cloud-Fog-Edge Platform](https://link.springer.com/article/10.1007/s10586-021-03328-x) \[<ins>Cluster Computing Journal</ins>\]**

  In this work we are proposing a Denial of ARP spoofing approach to prevent
  internal ARP spoofing attack in SDN and NFV enabled Cloud-Fog-Edge platform.
  Unlike other approaches, DARPSpoof also prevents against DOS, DDOS and
  session hijacking attacks. It also significantly reduces the overhead towards
  SDN Controller in terms of flow rules and Packet-In and Packet-Out traffic.

- **[Fairness and Applications’ transport protocol aware frame aggregation using programmable WLANs](https://link.springer.com/article/10.1007/s11276-022-03153-z) \[<ins>Wireless Networks Journal</ins>\]**

  In this work, we investigate major reasons for fairness issues, and propose a
  Fairness and Applications’ transport protocol aware frame aggregation (FAFA)
  scheme using Pro-APs The FAFA scheme is evaluated against existing schemes
  using extensive simulations with the Network Simulator-3 (NS-3).

### Projects

- **[mraylib](https://github.com/RishabhRD/mraylib):** is a C++23 based ray
  tracing library to produce some real looking images. Unlike other ray
  tracers, the library is executor agnostic and pure algorithmic making it
  generic enough for many usecase. It has **50+ github stars**.

- **[libparse](https://github.com/RishabhRD/libparse):** A functional
  programming compile time string parsing libray. It provides some primitive
  parsers and powerful parser combinators to build higher level parsers. Given
  a string in compile time, it can parse the same in compile time itself. It
  has **20+ github stars**.

- **[rssh](https://github.com/RishabhRD/rssh-server):** rssh allows tunneling
  ssh connections between client and server using an intermediate machine
  making them able to connect even being on different network via the intermediate
  machine.

- **[nvim-lsputils](https://github.com/RishabhRD/nvim-lsputils)**: nvim-lsputils
  is lua plugin for neovim text editor that provide better frontend handler for
  nvim-lsp client. It has **400+ github stars**.

- **[nvim-cheat.sh](https://github.com/RishabhRD/nvim-cheat.sh)**: nvim-cheat.sh
  is lua plugin for neovim text editor that aims at reducing browsing by providing
  way to query programming questions from neovim itself. It has **130+ github stars.**

### Skills

- **Programming Languages**: C++, Haskell, Lua, Typescript, Java, C [with **Design Patterns, Functional programming and OOP**]
- **Scripting**: Bash, sed, awk
- **Networking Research Skills**: SDN, NFV, OpenFlow, OVS, Floodlight(SDN Controller), NS-3, Wireshark, tcpdump, netstat
- **Cloud & Databases**: AWS, DynamoDB, MySQL
