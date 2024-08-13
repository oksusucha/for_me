public class [ConcurrentModificationException](<[](https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/util/ConcurrentModificationException.html)>)
extends [RuntimeException](https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/lang/RuntimeException.html "class in java.lang")

This exception may be thrown by methods that have detected concurrent modification of an object when such modification is not permissible.

>이 예외는 수정이 허용되지 않는 경우 객체의 동시 수정을 감지한 메서드에 의해 발생할 수 있습니다.

For example, it is not generally permissible for one thread to modify a Collection while another thread is iterating over it. In general, the results of the iteration are undefined under these circumstances. Some Iterator implementations (including those of all the general purpose collection implementations provided by the JRE) may choose to throw this exception if this behavior is detected. Iterators that do this are known as _fail-fast_ iterators, as they fail quickly and cleanly, rather that risking arbitrary, non-deterministic behavior at an undetermined time in the future.

>예를 들어 일반적으로 한 스레드가 컬렉션을 수정하는 동안 다른 스레드가 컬렉션을 반복하는 것은 허용되지 않습니다. 
>일반적으로 이러한 상황에서는 반복 결과가 정의되지 않습니다. 
>일부 Iterator 구현(JRE에서 제공하는 모든 범용 컬렉션 구현 포함)은 이 동작이 감지되면 이 예외를 발생시키도록 선택할 수 있습니다. 
>이를 수행하는 반복자는 미래의 결정되지 않은 시간에 임의적이고 비결정적인 동작을 위험에 빠뜨리는 대신 신속하고 깔끔하게 실패하므로 빠른 실패(fail-fast) 반복자로 알려져 있습니다.

Note that this exception does not always indicate that an object has been concurrently modified by a _different_ thread. If a single thread issues a sequence of method invocations that violates the contract of an object, the object may throw this exception. For example, if a thread modifies a collection directly while it is iterating over the collection with a fail-fast iterator, the iterator will throw this exception.

>이 예외가 항상 다른 스레드에 의해 객체가 동시에 수정되었음을 나타내는 것은 아닙니다. 
>단일 스레드가 개체 계약을 위반하는 일련의 메서드 호출을 실행하는 경우 개체에서 이 예외가 발생할 수 있습니다. 
>예를 들어 스레드가 빠른 실패 반복기를 사용하여 컬렉션을 반복하는 동안 컬렉션을 직접 수정하는 경우 반복기는 이 예외를 발생시킵니다.

Note that fail-fast behavior cannot be guaranteed as it is, generally speaking, impossible to make any hard guarantees in the presence of unsynchronized concurrent modification. Fail-fast operations throw `ConcurrentModificationException` on a best-effort basis. Therefore, it would be wrong to write a program that depended on this exception for its correctness: _`ConcurrentModificationException` should be used only to detect bugs._

>일반적으로 동기화되지 않은 동시 수정이 있는 경우 엄격한 보장이 불가능하므로 빠른 실패 동작을 보장할 수 없습니다. 
>빠른 실패 작업은 최선을 다해 _ConcurrentModificationException_ 을 발생시킵니다. 
>따라서 정확성을 위해 이 예외에 의존하는 프로그램을 작성하는 것은 잘못된 것입니다. _ConcurrentModificationException_ 은 버그를 탐지하는 데에만 사용해야 합니다.

Since:
1.2

See Also:
[Collection](https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/util/Collection.html) [Iterator](https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/util/Iterator.html) [Spliterator](https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/util/Spliterator.html) [ListIterator](https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/util/ListIterator.html) [Vector](https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/util/Vector.html) [LinkedList](https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/util/LinkedList.html) [HashSet](https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/util/HashSet.html) [Hashtable](https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/util/Hashtable.html) [TreeMap](https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/util/TreeMap.html) [AbstractList](https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/util/AbstractList.html) [Serialized Form](https://docs.oracle.com/en/java/javase/22/docs/api/serialized-form.html#java.util.ConcurrentModificationException)

---
자바에서 `ConcurrentModificationException`이라는 예외가 있어.
이 예외는 보통, 하나의 스레드가 컬렉션을 수정하고 있을 때 다른 스레드가 그 컬렉션을 반복하려고 하면 발생해. 

예를 들어, 컬렉션을 돌면서 동시에 그걸 수정하려고 하면 문제가 생길 수 있다는 거지.
그래서 자바에서는 이런 상황을 감지하면 바로 예외를 던져서 프로그램이 엉뚱한 결과를 내지 않게 '빠르게 실패'하게 만들어.

근데 꼭 다른 스레드 때문에 이 예외가 발생하는 건 아니야. 
**같은 스레드 내**에서도 잘못된 순서로 메서드를 호출하면 이 **예외가 발생할 수 있어**.
예를 들어, 반복하는 도중에 그 컬렉션을 직접 수정하면 예외가 터진다고 보면 돼.

그리고 중요한 건, 이 예외가 항상 발생하는 게 아니야. 
동기화되지 않은 상태에서의 동시 수정은 완벽하게 감지할 수 없기 때문에, 자바에서는 최선을 다해 감지하려고 하지만 100% 보장은 못 해. 그래서 이 예외가 발생할 거라고 기대하고 프로그램을 짜면 안 되고, **이 예외는** 단지 코드에 **버그가 있다는 신호**로만 받아들여야 해.
