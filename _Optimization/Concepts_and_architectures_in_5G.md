---

---

# Introduction

현대의 네트워크가 요구하는 보편적인 조건

1. Massive system capacity
2. Very high data rates
3. Minimal latency
4. Extremely high reliability and availability
5. Energy-efficient and secure operation

이와 같은 조건들을 만족하기 위해선 네트워크의 성능도 중요하지만 **최적화** 이론을 통한 **디자인**과 **관리**가 중요하다.



### Network elasticity

Dynamic하고 Optimal하게 자원과 capcity를 활용하는 것, flexible한 자원할당의 정의는 다음 두가지로 나타낼 수 있다.

1. Capacity의 관점에서 봤을 때
   - `Resource pooling`이 utilization 측면에서 `distributed resources`보다 더 효율적이다.
2. NFV(Network Functional Virtualization)은 distributed logic의 복원력(resilience)을 높여준다.

---

# Software-Defined Networking (SDN)

Traditionally, **the control of packet flows** is realized by nodes forwarding packets to the best destination according to the apcket header information and a set of static rules.

- Routers

  - Accomodate the quality of service on a per-flow basis, mostly agnostic of the states in other parts of the network. 

  - The control of the single packet flow and the interaction between flows are intrinsically linked together.
    - ==As a result, some parts of the network may be overloaded whereas others may be underutilized.==



> SDN allows decoupling of the control from the flow, resulting in two separate domains referred to as the control plane and the data plane, respectively.

&rarr; The possibility to create resource and flow control logic that is based on more information than is contained in individual flows.



As a matter of fact, the logical separation into control plane and data plane was implemented for synchronous digital hierarchy (SDH), that is, connection-oriented networks, already in the mid -1990s. ==The implementation was facilitated by the fact that the routes in such networks already were semistatic, and networks consisted of relatively few nodes.==



### SDN 기반 구현의 장점

1. Traffic characteristics change much more rapidly today than in the past through the fast deployment of new services.
2. Traffic flows depend on an increasingly cloud-based service distribution.
3. With rapidly increasing traffic volumes and big data, resources need to be managed more cost-efficiently.



## Centralized and Distributed Control

- Advantage
  - Ability to apply a control policy taking into account the state of the entire network.

- Disadvantages 
  - Delays in receiving information updates and applying control actions and the risk of overload or failure of a single centralized control function.

**The resilience concern of a centralized logic is addressed by NFV**

&rarr; It can be seen as resource sharing on the platform level. It constitutes an important framework to ensure resilience of the function of the central logic.



At present, network control is typically decentralized and residing in the routers due to its fast reaction time and resilience against failure.

> In SDN, control functions can be separated into fast flow control and routing functions, which are decentralized and implemented locally in routers and switches, and longer-term control strategies residing in a network entity we refer to as an **SDN orchestrator



## Network Function Virtualization (NFV)

NFV is essentially decoupling of software from hardware. It provides a layer between the hardware with its operating system and software applications.

&rarr; **NFV represents the physical implementation of functions as virtual machines or virtual services on general-purpose hardware platforms.**



> SDN과 NFV를 구분하기 위해서
>
> 1. SDN: Decoupling of network logic from data transport
> 2. NFV: Rapid provisioning and control through virtualization



## OpenFlow

OpenFlow specifies the communication between the control plane and the data plane. It implements one of the first standards in SDN **enabling the SDN controller to interact directly with the forwarding nodes - switches and routers - in a network**.

- Control logic triggers queue settings and forwarding tables through OpenFlow.
- Update of packet forwarding tables in switches and routers is a standard operation, where globally optimal routes are determined by the central logic.
- OpenFlow implements the Orchestrator-Forwarder interface for manipulating flow tables and requests traffic statistics and network state information from the nodes to model the network topology and for monitoring.

---

# IT Convergence

## Big Data

We still need to exstract useful information in alomsot real-time. Handling of high frequency data requires new approaches and specially designed algorithms.

## Edge Compouting

Edge computing refers to assigning some of computational burden to the `edge` of the network, separate from clouds and central logic. The edge is typically closer to the data source and edge computing functions often include pre-processing, analytics and transformations, reducing the required transmission bandwidth.

## Security and Integrity

Resilience and robustness are characteristics somewhat connected to security, whereas integrity is more of an operational concern, or more precisely, policy setting and policing. Some protective techniques are based on game theory, where the idea is to formulate optimal strategies using as much known information about the adversary as possible.

## Energy Efficiency

We simply estimate the energy savings by a quantity proportional to the efficiency ratio for other resources.

---

# Building Blocks

## Optical Fiber

Wavelength-division multiplexing(WDM) is a technology for multiplexing a number of optical carrier signals onto an optical fiber by using different wavelengths of laser light. The technique enables bidirectional communications over a single fiber, and facilitates expansion of capacity by proper planning or by upgrading the interfaces at the fiber ends.



## SD-WAN

Software-defined networking in a wide area network (SD-WAN) simplifies the operation of a WAN by decoupling networking hardware from its control logic.

A main application of SD-WAN is to implement high-performance networks using ubiquitous Internet access, partially or completely replacing more expensive connection technologies such as MPLS.

Features of SD-WAN include resilience, security and quality of service (QoS) functions, such as real time detection of outages and automatic switch over to working links.



## Open Source Software

Built-in security is a concern for businesses choosing software. Many may consider free or open-source solutions less secure than proprietary solutions. However, free and open source software communities are contributing to cybersecurity systems, such as Apache Spot for example.

---

# Algorithms and Complexity Classes

We can usually avoid rigorous technical arguments and will be satisfied with an algorithm construction if we have "done our best", or that we have taken "reasonable precuations" to make it as efficient as possible. What we mean by that is - in very imprecise terms - that we try to find a representations as short and efficient as possible for the problem instances.

We will express the worst-case algorithm running times using 'big-Oh' notation.

Complex algorithms are typically broken down into subroutines. 

An algorithm is called *efficient* if it runs (at most) in time O(nk) of the input size n and some k>0, that is, if it is bounded above by a polynomial.



## Optimization Problems

Most problems in network design are *optimization problems*, where we search for an *optimal value*, such as cost. It is useful to think of the optimization problems as *decision problems*, that is, problems for which the output is either *true* or *false* only.



1. Complexity class와 결정 문제(Language L)
   - 컴퓨터 과학에서는 특정 문제들을 해결하는데 필요한 계산의 양을 분류하기 위해 "Complexity Class"라는 개념을 사용함.
   - 결정 문제들의 집합을 Language L이라고 부르며, 이는 컴퓨터에게 '예' 또는 '아니오'라고 대답할 수 있는 질문들의 집합임.
2. P Class
   - P: Polynomial Time
   - P 클래스의 문제는 주어진 입력에 대해 해답을 찾는 데 걸리는 시간이 입력 크기에 따라 '다항식'으로 증가한다.
   - 예를 들어, 크기가 n인 입력에 대해 해답을 찾는 시간이 $n^2$ 또는 $n^3$과 같이 증가한다.
   -  이런 문제는 일반적으로 effective 하게 해결할 수 있는 것으로 간주된다.
3. NP Class(Non-deterministic Polynomial Time)
   - 'NP'는 '비결정론적 다항 시간(Non-deterministic Polynomial Time)'을 의미한다.
   - NP 클래스는 제시된 해답이 맞는지 다항식 시간 내에 검증할 수 있는 문제들의 집합이다.
   - 즉, 해답을 찾는 것은 어려울 수 있지만 제시된 해답이 맞는지 확인하는 것은 비교적 빠르게 할 수 있다.



## Showing Problem Hardness

There are some problems that are at least as hard as every problem in NP. The notion of hardness is based on the concept of polynomial time _reducibility_. 

A problem L is polynomial time reducible to another problem M if there is a function f, computable in polynomial time, such that $ x \in L \Leftrightarrow f(x) \in M$.

The problem M is said to be $NP$ hard if every other problem in L in $NP$ is reducible to M in polynomial time.

A problem is $NP$-complete if it is $NP$-hard and in the class $NP$ itself. 

> In order to show that a problem is NP-complete, we need to have at least one NP-complete problem. Such a problem is _satisfiability_(SAT) of a logical expression. It has been proven that satisfiability is NP-complete



A variant of the satisfiability problem is the restricted 3-SAT, restricted to clauses with three literals. 

We note that 3-SAT is in NP, for we can construct a nondeterministic polynomial-time algorithm that takes an expression with three variables per clause, gueses an assignment of Boolean values for these variables, and **then evaluates it to see if it equals true by inserting the values in to the clauses. 3-SAT is NP-complete. An interesting fact is that the 2-SAT, expressions with only two variables in each clause, is in P**.



## Algorithms for Hard Problems

Most problems where we want to find a set of links such that a network possesses some specified property, such as path diversity or resilience, are NP-hard.

We have to resort to clever heuristics or approximations. For networks of realistic sizes, the choice of solution method is very important.

For network of realistic sizes, the choice of solution method is very important.

> The additional time required to find a more "accurate" solution may not be worthwhiel spending.



We also need to pay attention to verification of our results.

&rarr; A good approach is to solve a problem using different algorithms and comparing the results.

&rarr; If the results are similar, the likelihood of the solutions being close to the optimum increases, provided taht the design criteria are correctly formulated.



It is desirable that an algorithm is efficient and well defined, so that any input data leads to a defined state and that the algorithm does not “freeze” in an infinite loop. We therefore require that the number of steps the algorithm needs to complete a task is finite.



An algorithm may evaluate an expression directly, iteratively, by calling itself repeatedly with a value previously determined, or recursive, where successive calls to the algorithm determine succeessively smaller problems.

- Recursive Algorithm
  - Base case: 재귀 함수 호출 없이 결정될 수 있는 부분
  - Recursive part: 점차 작아지는 문제 크기로 자기 자신을 어떻게 호출하는지를 명시하는 부분



### Brute force

모든 가능한 경우의 수를 다 고려한 뒤 최적의 방법을 결정함



### Analytical Methods

수학적으로 modeling하는 전통적인 방법. 일반적인 NP-hard 문제에선 적용 불가능함. 하지만, NP-hard에서 몇몇 parameter들을 무한이나 0으로 치환하여 적용 가능 할 때도 있음.



### Approximations

Exact value의 upper bound에 제한을 두어 approximation과 exact value의 거리를 줄이는 방법. 문제가 많이 간단해지기도 함. Limits와 asymptotic가 자주 쓰인다.



### Heuristics

- No guarantee on the error limit
- Serve as a first "best guess"
- Often give reasonable results and are **easy to program**

> Greedy Algorithm
>
> The greedy method is applicable to optimization problems, that is, problems that involve searching through a set of configurations to find one that minimizes or maximizes an objective function defined on these configurations.



### Problem restriction

- Restriction of the search space.
- Possibly some parameters are kept fixed while otehrs are allowed to vary.
  - Reduction of the state space is usually the method for deriving bounds.
  - *Branch-and-bound*[OrchestRAN]



### Divide and conquer

The divide and conquer principle is a method to solve a complex problem by dividing it into subproblems of smaller size, recursively solving each subproblem, and then merging the solution parts to produce a solution to the original problem.

- Decompositional methods
- Dynamic programming



### Randomization

#### Algorithms Types:

- Deterministic Algorithms:
  - 동일한 입력에 대해 예측 가능하고 일관된 행동을 보임
- Randomized Algorithms:
  - 확률적 state transition을 포함함. Random 요소를 포함하기 때문에 동일한 입력에 대해서도 행동이 달라질 수 있음



#### Importance of Randomization:

Randomization은 많은 알고리즘 접근법에서 중요함

- Simulation and Monte Carlo Methods
  - 추정과 검증에 사용되지만, 잘못된 결과를 피하기 위해 신중하게 적용해야 함.
- Metaheuristics:
  - 물리 시스템이나 생물학적 진화를 모방한 수치 최적화 방법으로, Simulated Annealing(지역 탐색), Evolutionary Algorithms(집단 탐색 기법)이 해당함.



#### Quality of Random Numbers

Random number에 따라 학습의 품질이 달라지기 때문에, 재현 가능하고 균일하고 분포된 random number를 생성하는 알고리즘을 개발하는 것이 중요함.

---

