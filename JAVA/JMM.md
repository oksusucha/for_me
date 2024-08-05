## 17.4 Memory Model

>A _memory model_ describes, given a program and an execution trace of that program, whether the execution trace is a legal execution of the program. The Java programming language memory model works by examining each read in an execution trace and checking that the write observed by that read is valid according to certain rules.

> 메모리 모델은 주어진 프로그램과 해당 프로그램의 실행 추적을 통해 실행 추적이 프로그램의 합법적인 실행인지 여부를 설명합니다.
> Java 프로그래밍 언어 메모리 모델은 실행 추적의 각 읽기를 검사하고 해당 읽기에서 관찰된 쓰기가 특정 규칙에 따라 유효한지 확인하는 방식으로 작동합니다.


> The memory model describes possible behaviors of a program. An implementation is free to produce any code it likes, as long as all resulting executions of a program produce a result that can be predicted by the memory model.

> 메모리 모델은 프로그램의 가능한 동작을 설명합니다. 프로그램의 모든 실행 결과가 메모리 모델에서 예측할 수 있는 결과를 생성하는 한 구현은 원하는 코드를 자유롭게 생성할 수 있습니다.

> This provides a great deal of freedom for the implementor to perform a myriad of code transformations, including the reordering of actions and removal of unnecessary synchronization.

> 이는 구현자가 작업 재정렬 및 ​​불필요한 동기화 제거를 포함하여 수많은 코드 변환을 수행할 수 있는 상당한 자유를 제공합니다.


Examples
**Example 17.4-1. Incorrectly Synchronized Programs May Exhibit Surprising Behavior**

The semantics of the Java programming language allow compilers and microprocessors to perform optimizations that can interact with incorrectly synchronized code in ways that can produce behaviors that seem paradoxical. Here are some examples of how incorrectly synchronized programs may exhibit surprising behaviors.

Consider, for example, the example program traces shown in Table 17.4-A. This program uses local variables `r1` and `r2` and shared variables `A` and `B`. Initially, `A == B == 0`.

## Table 17.4-A. Surprising results caused by statement reordering - original code

| Thread 1   | Thread 2   |
| ---------- | ---------- |
| 1: r2 = A; | 3: r1 = B; |
| 2: B = 1;  | 4: A = 2;  |

It may appear that the result `r2 == 2` and `r1 == 1` is impossible. Intuitively, either instruction 1 or instruction 3 should come first in an execution. If instruction 1 comes first, it should not be able to see the write at instruction 4. If instruction 3 comes first, it should not be able to see the write at instruction 2.

If some execution exhibited this behavior, then we would know that instruction 4 came before instruction 1, which came before instruction 2, which came before instruction 3, which came before instruction 4. This is, on the face of it, absurd.

However, compilers are allowed to reorder the instructions in either thread, when this does not affect the execution of that thread in isolation. If instruction 1 is reordered with instruction 2, as shown in the trace in Table 17.4-B, then it is easy to see how the result `r2 == 2` and `r1 == 1` might occur.

## Table 17.4-B. Surprising results caused by statement reordering - valid compiler transformation

| Thread 1       | Thread 2       |
|----------------|----------------|
| B = 1;         | r1 = B;        |
| r2 = A;        | A = 2;         |

To some programmers, this behavior may seem "broken". However, it should be noted that this code is improperly synchronized:

- there is a write in one thread,
- a read of the same variable by another thread,
- and the write and read are not ordered by synchronization.

This situation is an example of a data race (§17.4.5). When code contains a data race, counterintuitive results are often possible.

Several mechanisms can produce the reordering in Table 17.4-B. A Just-In-Time compiler in a Java Virtual Machine implementation may rearrange code, or the processor. In addition, the memory hierarchy of the architecture on which a Java Virtual Machine implementation is run may make it appear as if code is being reordered. In this chapter, we shall refer to anything that can reorder code as a compiler.

Another example of surprising results can be seen in Table 17.4-C. Initially, `p == q` and `p.x == 0`. This program is also incorrectly synchronized; it writes to shared memory without enforcing any ordering between those writes.

## Table 17.4-C. Surprising results caused by forward substitution

| Thread 1       | Thread 2       |
|----------------|----------------|
| r1 = p;        | r6 = p;        |
| r2 = r1.x;     | r6.x = 3;      |
| r3 = q;        |                |
| r4 = r3.x;     |                |
| r5 = r1.x;     |                |

One common compiler optimization involves having the value read for `r2` reused for `r5`: they are both reads of `r1.x` with no intervening write. This situation is shown in Table 17.4-D.

## Table 17.4-D. Surprising results caused by forward substitution

| Thread 1       | Thread 2       |
|----------------|----------------|
| r1 = p;        | r6 = p;        |
| r2 = r1.x;     | r6.x = 3;      |
| r3 = q;        |                |
| r4 = r3.x;     |                |
| r5 = r2;       |                |

Now consider the case where the assignment to `r6.x` in Thread 2 happens between the first read of `r1.x` and the read of `r3.x` in Thread 1. If the compiler decides to reuse the value of `r2` for the `r5`, then `r2` and `r5` will have the value 0, and `r4` will have the value 3. From the perspective of the programmer, the value stored at `p.x` has changed from 0 to 3 and then changed back.>)


> The memory model determines what values can be read at every point in the program. The actions of each thread in isolation must behave as governed by the semantics of that thread, with the exception that the values seen by each read are determined by the memory model. When we refer to this, we say that the program obeys _intra-thread semantics_. Intra-thread semantics are the semantics for single-threaded programs, and allow the complete prediction of the behavior of a thread based on the values seen by read actions within the thread. To determine if the actions of thread _t_ in an execution are legal, we simply evaluate the implementation of thread _t_ as it would be performed in a single-threaded context, as defined in the rest of this specification.

> 메모리 모델은 프로그램의 모든 지점에서 읽을 수 있는 값을 결정합니다.
> 격리된 각 스레드의 작업은 해당 스레드의 의미에 따라 작동해야 합니다. 
> 단, 각 읽기에서 표시되는 값은 메모리 모델에 의해 결정됩니다. 이것을 언급할 때, 우리는 프로그램이 스레드 내 의미론을 따른다고 말합니다. 
> 스레드 내 의미론은 단일 스레드 프로그램의 의미론이며 스레드 내의 읽기 작업에서 확인되는 값을 기반으로 스레드 동작을 완벽하게 예측할 수 있습니다. 
> 실행 시 스레드 t의 작업이 적법한지 확인하려면 이 사양의 나머지 부분에 정의된 대로 단일 스레드 컨텍스트에서 수행되는 스레드 t의 구현을 간단히 평가하면 됩니다.


> Each time the evaluation of thread _t_ generates an inter-thread action, it must match the inter-thread action _a_ of _t_ that comes next in program order. If _a_ is a read, then further evaluation of _t_ uses the value seen by _a_ as determined by the memory model.

> 스레드 t의 평가가 스레드 간 작업을 생성할 때마다 프로그램 순서에서 다음에 오는 t의 스레드 간 작업 a와 일치해야 합니다.
> a가 읽기인 경우 t에 대한 추가 평가에서는 메모리 모델에 의해 결정된 대로 a에 표시되는 값을 사용합니다.

> This section provides the specification of the Java programming language memory model except for issues dealing with final fields, which are described in [§17.5](https://docs.oracle.com/javase/specs/jls/se21/html/jls-17.html#jls-17.5).

> 이 섹션에서는 [§17.5](https://docs.oracle.com/javase/specs/jls/se21/html/jls-17.html#jls-17.5) :: Final Field Semantics 에 설명된 최종 필드를 다루는 문제를 제외하고 Java 프로그래밍 언어 메모리 모델의 사양을 제공합니다.

> The memory model specified herein is not fundamentally based in the object-oriented nature of the Java programming language. For conciseness and simplicity in our examples, we often exhibit code fragments without class or method definitions, or explicit dereferencing. Most examples consist of two or more threads containing statements with access to local variables, shared global variables, or instance fields of an object. We typically use variables names such as r1 or r2 to indicate variables local to a method or thread. Such variables are not accessible by other threads.

> 여기에 지정된 메모리 모델은 기본적으로 Java 프로그래밍 언어의 객체 지향 특성을 기반으로 하지 않습니다.
> 예제의 간결성과 단순성을 위해 클래스나 메서드 정의나 명시적인 역참조 없이 코드 조각을 표시하는 경우가 많습니다.
> 대부분의 예제는 지역 변수, 공유 전역 변수 또는 개체의 인스턴스 필드에 대한 액세스 권한이 있는 명령문을 포함하는 두 개 이상의 스레드로 구성됩니다.
> 일반적으로 r1 또는 r2와 같은 변수 이름을 사용하여 메서드나 스레드에 로컬인 변수를 나타냅니다. 이러한 변수는 다른 스레드에서 액세스할 수 없습니다.


### 요약
- **메모리 모델의 역할**: 자바 메모리 모델은 프로그램과 그 실행 추적이 합법적인지를 결정하며, 각 읽기 작업에 대해 관찰된 쓰기가 특정 규칙에 따라 유효한지 확인합니다.
    
- **구현의 자유**: 메모리 모델은 프로그램의 가능한 동작을 설명하며, 구현자는 메모리 모델이 예측할 수 있는 결과를 생성하는 한, 코드 변환 및 동기화 제거 등 다양한 최적화를 수행할 자유가 있습니다.
    
- **스레드 간 및 스레드 내 동작**: 스레드 내 동작은 단일 스레드 프로그램의 의미론을 따르며, 각 읽기 작업에서 보이는 값은 메모리 모델에 의해 결정됩니다. 이는 프로그램이 스레드 내 의미론을 준수하는지 확인하는 데 사용됩니다.