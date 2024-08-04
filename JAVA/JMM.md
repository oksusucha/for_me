## 17.4 Memory Model

>A _memory model_ describes, given a program and an execution trace of that program, whether the execution trace is a legal execution of the program. 
>
>The Java programming language memory model works by examining each read in an execution trace and checking that the write observed by that read is valid according to certain rules.

> 메모리 모델은 주어진 프로그램과 해당 프로그램의 실행 추적을 통해 실행 추적이 프로그램의 합법적인 실행인지 여부를 설명합니다.
> 
> Java 프로그래밍 언어 메모리 모델은 실행 추적의 각 읽기를 검사하고 해당 읽기에서 관찰된 쓰기가 특정 규칙에 따라 유효한지 확인하는 방식으로 작동합니다.


> The memory model describes possible behaviors of a program. An implementation is free to produce any code it likes, as long as all resulting executions of a program produce a result that can be predicted by the memory model.

> 메모리 모델은 프로그램의 가능한 동작을 설명합니다. 프로그램의 모든 실행 결과가 메모리 모델에서 예측할 수 있는 결과를 생성하는 한 구현은 원하는 코드를 자유롭게 생성할 수 있습니다.

> This provides a great deal of freedom for the implementor to perform a myriad of code transformations, including the reordering of actions and removal of unnecessary synchronization.

> 이는 구현자가 작업 재정렬 및 ​​불필요한 동기화 제거를 포함하여 수많은 코드 변환을 수행할 수 있는 상당한 자유를 제공합니다.


Examples

> The memory model determines what values can be read at every point in the program. The actions of each thread in isolation must behave as governed by the semantics of that thread, with the exception that the values seen by each read are determined by the memory model. When we refer to this, we say that the program obeys _intra-thread semantics_. Intra-thread semantics are the semantics for single-threaded programs, and allow the complete prediction of the behavior of a thread based on the values seen by read actions within the thread. To determine if the actions of thread _t_ in an execution are legal, we simply evaluate the implementation of thread _t_ as it would be performed in a single-threaded context, as defined in the rest of this specification.

> 메모리 모델은 프로그램의 모든 지점에서 읽을 수 있는 값을 결정합니다.
> 격리된 각 스레드의 작업은 해당 스레드의 의미에 따라 작동해야 합니다. 
> 단, 각 읽기에서 표시되는 값은 메모리 모델에 의해 결정됩니다. 이것을 언급할 때, 우리는 프로그램이 스레드 내 의미론을 따른다고 말합니다. 
> 스레드 내 의미론은 단일 스레드 프로그램의 의미론이며 스레드 내의 읽기 작업에서 확인되는 값을 기반으로 스레드 동작을 완벽하게 예측할 수 있습니다. 
> 실행 시 스레드 t의 작업이 적법한지 확인하려면 이 사양의 나머지 부분에 정의된 대로 단일 스레드 컨텍스트에서 수행되는 스레드 t의 구현을 간단히 평가하면 됩니다.