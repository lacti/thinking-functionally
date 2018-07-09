@title[Prelude]

어떻게 하면 프로그래밍을 잘할 수 있을까요?

이건 유도 질문이 아닙니다. 정말 저도 궁금해요.

그런데 누가 그러더라고요.

---
@title[Title]

## Thinking functionally.

네, 저도 사실 잘 모릅니다.

잘 모르니까 이렇게 자신있게 발표도 하는 것 아닐까요?

---
@title[Why]

우리는 주로 절차지향 프로그래밍을 사용합니다. ~~왜냐하면 그걸 학교에서 먼저 배웠거든요.~~  
나중에 고학년이 되면 함수형을 배우기는 합니다만, ~~사실 그 때는 이미 세뇌되어서 별로 눈에 들어오지 않았습니다.~~

기억을 되살려보죠. 그렇다면 절차지향과 함수형은 무엇이 다를까요?

- 함수형은 읽기가 힘들어요!
- 함수형은 너무 수학적인 것 같아요!

---
@title[What is the functional]

사실, 저는 잘 몰라요. 그래서 wiki에게 물어봤습니다.

> Functional programming is a programming paradigm—a style of building the structure and elements of computer programs—that treats computation as the evaluation of mathematical functions and avoids changing-state and mutable data.

*functional 패러다임은 절차지향이 상태를 바꾸는 법에 대해 집중할 때, 함수의 응용에 집중합니다.*

---
@title[Let me see details]

뜬구름을 잡고 있군요. *하나씩 살펴봅시다.*

그런데 정말 이런게 도움이 되기는 할까요?

글쎄요, 똑똑한 사람들이 functional이 하나의 대안이라고 이야기하는 것을 보니 맞는 이야기가 아닐까 싶으니까, **일단 믿음을 가지고 앞으로 나아가봅시다.**

---
@title[Function applications]

함수의 응용이란 무엇일까요? 그리고 그걸 어디에 쓸 수 있을까요?

일단 또 wiki에 물어봅시다.

- function composition
- high order function

함수를 합성할 수 있고, 고차원의 함수가 있다는군요. 대충 다음과 같은 예제입니다.

```javascript
const fooAndBar = a => foo(bar(a)); // composition
const gap = eul => eul(); // high order
const eul = () => byoung(); // high order
```

생각보다 많이 봐오던건데요. 이게 함수형인가요?

---
@title[Generalization]

네, 생각보다 멀리 있지 않아요. ~~왜냐하면 전 세계에 숨어있는 함수형 추종자들이 열심히 밑작업을 하고 있기 때문입니다...!~~

사실 상관없습니다. function, 즉 code도 data와 같이 취급해서 parameter로도 쓰고 return value로도 쓰는거죠. 그것은 우리에게 익숙한 function pointer일 수도 있고, 혹은 script string일 수도 있씁니다.

이걸 하면 무엇이 좋을까요? 좀 더 **general한 프로그래밍** 을 할 수 있습니다.

---
@title[Into the library]

C++ STL의 algorithm은 아주 좋은 예입니다.

- https://en.cppreference.com/w/cpp/utility/functional
- https://en.cppreference.com/w/cpp/algorithm

이들은 추상적인 자료형(type)과 수많은 일반화된 함수들과 그 조합으로 다양하고 복잡한 문제를 처리할 수 있습니다. *굉장히 수학적이지 않나요?*

그런데 type의 이야기가 나와서 말인데, *category* 와 *pattern matching* 은 단순히 *generalize* 뿐만 아니라 *optimize* 에도 많은 도움을 줍니다.

---
@title[Eg. advance]

예를 들어 `random_access_iterator` 와 `forward_iterator` 에 대한 `advance()` 를 볼까요.

- https://en.cppreference.com/w/cpp/iterator
- https://en.cppreference.com/w/cpp/iterator/advance

```cpp
template<class InputIt, class Distance>
void _advance_impl( InputIt& it, Distance n, forward_iterator ) {
  // linear algorithm.
}
template<class InputIt, class Distance>
void _advance_impl( InputIt& it, Distance n, random_access_iterator ) {
  // complexity is constant.
}
template<class InputIt, class Distance>
void advance( InputIt& it, Distance n ) {
  _advance_impl( it, n, std::iterator_traits<it>::iterator_category() );
}
```

---
@title[from TMP to Stateless]

이 함수들은 다른 또 다른 일반화된 알고리즘 함수들과 결합하여 사용될 수 있습니다. *이렇게 분할된 개별 함수들은 각각을 더욱 최적화할 수도 있고, 혹은 category를 좀 더 세분화하여 문제를 보다 더 잘 풀 수도 있습니다.*

그리고 이것을 runtime이 아닌 **compile time** 에 수행할 수 있다면 굉장히 멋진 일이겠지요. 그것이 바로 **zero-cost abstract** 이고 **C++ meta template programming** 이 멋진 이유입니다.

그리고 개별 함수는 굉장히 독립적이기 때문에, 단위 테스트에도 아주 적합하겠죠. 네, 바로 **stateless** 입니다.

---
@title[Stateless and test]

state를 바꾸는 것에만 집중하다보면 object가 너무 커지거나 global state가 추가되는 문제가 발생할 수 있습니다. 이는 상태를 복잡하게 만들어 의도하지 않은 부수적인 문제 *side effect* 를 일으키고, 테스트를 어렵게 합니다.

테스트는 올바른 동작을 보장해는 것에 아주 유용합니다. 하지만 상태가 등장하면 복잡해집니다. 통제해야 하기 때문이죠.  
그렇다면, 우리가 상태가 없는 *stateless* 함수만 작성한다면 세상에 버그가 좀 더 줄어들지 않을까요?

```javascript
const statelessAdder = (a, b) => a + b;
const statefulAdder = a => (lastResult += a);
```

---
@title[Stateless and Concurrency]

상태를 가지지 않은 함수는 굉장히 결정적 *deterministic* 이고 심지어 동시에 여러 thread가 진입해도 안전합니다 *thread-safe*. 그렇다는건 *resumable* 하다는 것이고 이는 *asynchronous* 하게 작성하기도 좋다는 것입니다.

shared state가 상대적으로 적은 backend system을 구성할 때 functional language가 좀 더 환영받는 이유입니다. 덕분에 별다른 고민없이 단순히 많이 실행함으로써 *scalable* 한 시스템을 구축할 수 있습니다.

---
@title[DBMS]

만약 shared state가 필요하다면 어떨까요?

우리는 좋은 방법을 배웠습니다. 함수의 합성이죠! *shared state를 담당하는 function을 설계하면 됩니다.*

- persistency가 보장되고 transactional했으면 좋겠습니다. 네! 그것은 바로 dbms입니다.
- 혹은 memory에서 처리되기를 원하나요? memcached와 같은 memory db가 좋은 해결책이 될 수 있겠군요!

---
@title[Constraints]

한 번에 많은 일을 처리하는 것은 머리가 아픕니다. 적절한 function signature를 공유하고 composition해서 general하게 사용했으면 좋겠어요.

하지만 *hop이 늘어난다는 것은 장애 지점이 늘어난다는 것입니다.* 우리는 보다 안전한 시스템을 원해요. **input과 output domain에 대한 constraints를 정해보는 것은 어떨까요?**

적절한 constraints를 정할 수 있으면 굳이 프로그램을 실행하지 않아도 사전에 많은 문제를 찾아낼 수 있습니다.

```javascript
const monitorHeight = (): int => /* something */;
const displayString = (a: string) => /* something */;
displayString(monitorHeight()); // error!
```

---
@title[Types]

사실 우리는 *data storage class*에 따라 byte, short, int, long 혹은 편의를 위한 string 등의 primitive type을 언어에 의해 제공받습니다.

이는 사전에 정해진 utility type이고 거기에는 적절한 operation set이나 value range같은 특성들이 있습니다. 하지만 다음은 어떤가요?

```javascript
const acceptNewQuest = (questClassId: int) => /* add a new quest */;
const removeOldQuest = (questInstanceId: int) => /* remove an old quest */;
const questClassId = prompt(‘QuestId’);
removeOldQuest(questClassId); // Logical error but compiler can’t know about it.
```

---
@title[Fully-typed]

이 문제를 사전에 알아낼 수 있다면 얼마나 좋을까요?

해결책은 **각각의 type을 새로 선언해주는 것** 입니다. 그리고 각 type이 어떻게 호환되는지 서술해주는 것이죠 *concepts*.

```javascript
const acceptNewQuest = (id: QuestClassId) => /* add a new quest */;
const removeOldQuest = (id: QuestInstanceId) => /* remove an old quest */;
const questClassId = new QuestClassId(prompt(‘QuestId’));
removeOldQuest(questClassId); // Error!
```

---
@title[Prologue: Monad]

그리고 프로그래밍 알고리즘의 서술을 간결하게 작성할 수 있도록 도와주는 STL algorithm의 함수들처럼, 비슷한 역할을 할 수 있도록 도와주는 type도 있습니다. 바로 **monad pattern** 이죠.

monad란 무엇일까요?

---
@title[What is the Monad?]

![engineering-professor-a-monad-is-just-a-monoid-in-the-category-of-endofunctors-whats-the-problem](https://memegenerator.net/instance/27499682/engineering-professor-a-monad-is-just-a-monoid-in-the-category-of-endofunctors-whats-the-problem)

---
@title[]

무슨 말인지 하나도 모르겠군요. 이걸 어떻게 공부할 수 있을까요?

```
How to learn about Monads:

1. Get a PhD in computer science.
2. Throw it away because you don't need it for this section!
```

잘 설명되어 있는 다음 링크를 읽어봅시다.

- [Functors, applicatives, and monads in pictures](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html)
- [한글: Functors, applicatives, and monads in pictures](https://netpyoung.github.io/external/functors_applicatives_and_monads_in_pictures/)

---
@title[Simple examples]

여기에서 다 설명하기는 힘들어서 가볍게 예시로 확인해보겠습니다.

```haskell
class Monad m where
  (>>=) :: m a -> (a -> m b) -> m b

data Maybe t = Just t | Nothing
  (>>=) :: Maybe a -> (a -> Maybe b) -> Maybe b
  Nothing  >>= _ = Nothing
  (Just x) >>= f = f x

data Either a b = a | b
  (>>=) :: Either a b -> (a -> c) -> (b -> c) -> c
  a >>= f = f a
  b >>= f = f b
```

---
@title[Simple examples, more]

```haskell
data Identity a = a
  a >>= f = f a

data Collection x = [x]
  (>>=) :: Collection x -> (x -> y) -> Collection y
  xs >>= f = concat (map f xs)
```

~~~가볍지도 않고 좀 생소하군요.~~~ 가장 추상화된 최소한의 정의를 조합하여 모든 개념을 구현하기 위한 기반이라고 볼 수 있지 않을까요.

*좀 더 잘 이해하게 되는 날이 오면 이에 대한 설명에 도전해보겠습니다.*

---
@title[Currying and partial application]

함수를 조합하는 과정에서 몇 가지 속성을 간단히 암시하고 넘어갔는데, 그 유명한 *currying* 과 *partial application* 이 있습니다.

- **currying** 은 만약 `f(x, y, z)`가 있다면 이것을 `f(x)(y)(z)`으로 바꿀 수 있도록 해주는 것이고,
- **partial application** 은 `f(x, y, z)`가 있을 때 이것을 `f(1, 2, z)`와 같은 형태로 고정할 수 있게 해주는 것입니다.

function의 signature를 바꿀 수 있는 것으로, function의 응용을 보다 더 잘 할 수 있게 도와줍니다. 물론 여기에 *placeholder* 와 같은 *lazy evalutation* 감초들이 등장하면 보다 더 다양한 응용을 할 수 있게 됩니다.

예를 들어볼까요?

---
@title[Example: sort]

여기 sort함수가 있습니다. 이는 *less operator* 가 필요하군요.

```clojure
bool less(a, b) { return a < b; }
sort(begin, end, less)
```

이것도 괜찮은 것 같아요. less 함수에 인자를 넣는건 sort 함수를 부르는 시점이 아니라 *sort 함수 내에서 정렬을 할 때* 입니다. 즉, *lazy evaluation* 이 필요한 것이지요. 때문에 함수 자체를 넘겨주는 것입니다. 하지만 조금 더 줄여볼까요?

```clojure
sort(begin, end, (a, b) => a < b) # 아예 inline으로 넣어보죠.
sort(begin, end, _1 < _2) # 너무 간단한 형태이니 placeholder를 바로 써봅시다.
sort(begin, end, <) # 너무 간단하면 placeholder를 생략할 수 있습니다.
```

---
@title[Example: bind]

단순히 이런 *anonymous expression* 을 표기하기 위해서만은 아니고 partial application을 지원하기 위한 용도로도 *placeholder* 가 사용될 수 있습니다.

```javascript
type User {
  heal: (from: User, to: User) => void;
}

const heal = bind(User.heal, healer, _1); // user -> void
targets.forEach(heal); // [User] -> (user -> void)
```

---
@title[Pattern matching]

잠깐 언급된 *pattern matching* 역시 우리가 잘 알고 있는 *switch-case* 와 크게 다르지 않습니다. 혹시 생소한 점이 있다면, value 뿐만 아니라 type에 대해서도 case를 나눌 수 있다는 것입니다.

```fsharp
type QuestId = QuestClassId | QuestInstanceId;
get-quest-class = id match
  | QuestClassId => (get-class id)
  | QuestInstanceId => (get-class (class-to-instance-id id))
```

```javascript
getQuestClass(id: QuestClassId | QuestInstanceId) => {
  if (id instanceof QuestClassId) { return getClass(id); }
  else if (id instanceof QuestInstanceId) { return getClass(classToInstanceId(id)) }
}
```

---
@title[Pattern matching: more]

어느 쪽이 좀 더 나은 표현식이라고 할 수 있을까요? 이것은 굉장히 **취향 문제** 가 될 수 있습니다.

개인적인 의견으로는 후자의 경우

- 각 block 사이에 변수 등의 상태가 개입할 수 있다는 점
- 그리고 두 경우에 해당하지 않는 경우의 else가 존재하지 않는 점이 일단 걱정됩니다.
- 그리고 id가 non-null이면서 반드시 QuestClassId나 QuestInstanceId임을 compiler 등에서 보장해주면 좋을 것 같습니다.

---
@title[Language supported]

정말 **취향의 문제** 입니다만, *문제의 여지를 주지 않는 문법이 더 나은 프로그래밍을 작성하는데 도움을 줄 수도 있습니다.*

다소 불편할 수도 있지만, 적어도 **언어가 보장해주는 범위** 내에서 동작하는 프로그래밍을 했을 때 기본적으로 보장되는 부분이 꽤 도움이 되기 때문입니다.

위의 예제에서도, null check나 type check를 매번 사람이 하는 것은 너무 불필요한 일이니까요.

---
@title[Null checking]

*null check* 에 대해서는 *Maybe* 도 꽤 큰 도움을 줍니다. 특히 이는 *function composition* 관점에서 그렇습니다. 예를 들어 다음과 같은 코드가 있습니다.

```javascript
fooAndBar => {
  if (!arg) return;
  foo = step1(arg);
  if (!foo) return;
  return step2(foo);
}
```

만약 *step1* 과 *step2* 내에서 null 처리가 되어있다면 어떨까요? 그러면 코드는 좀 더 단순해집니다.

```javascript
fooAndBar => step2(step1(arg));
```

---
@title[Monad chain]

만약 step이 굉장히 많으면 어떨까요? 혹은 굉장히 유동적으로 변해야 한다면요.  
[step1, step2, step3, step4, step5].apply(arg) 와 같은 마법을 쓸 수 있다면 조금은 편해지지 않을까요?  
이럴 때 **Maybe monad** 는 굉장히 유용합니다.

monad의 마법은 계속 같은 monad를 반환하기 때문에 그 interface가 변하지 않아 계속 연결하여 사용할 수 있다는 것입니다. Maybe monad의 f는 다른 Maybe monad를 반환하므로, 위 코드는 간단하게 Maybe arg에 계속 f를 적용하기만 하면 됩니다. 마치 state machine같죠.

---
@title[Manad chain: more]

```javascript
arg >> step1 >> step2 >> step3 >> step4 >> step5
```

그리고 조금 더 함수를 응용하면 됩니다. 이 step function들을 적당히 미리 합성해두거나 혹은 function collection에 대해 순차적으로 bind를 해주는 macro를 정의하는 것도 좋겠군요.

---
@title[Either]

하지만 예외처리가 필요하다면요? 이럴 때 사용하기 좋은 것이 **Either monad** 입니다. *Either expected error* 를 반환하는 함수가 되는 것이지요.

```haskell
let bar = arg >> step1 >> step2
type Step<T> = Success of T | Failure of Error
type Error = NoInput | NonNumber
```

```haskell
step1 input = 
  if !input then
    Failure NoInput
  else
    Success doSomething
```

---
@title[Either more]

```haskell
step2 input =
  match input with
    | Success s ->
        if input is not number then 
          Failure NonNumber
        else
          Success doOther
    | Failure f -> Failure f
```

---
@title[ROP]

이것이 [railway oriented programming](https://fsharpforfunandprofit.com/rop/)의 시작입니다.

![ROP](https://image.slidesharecdn.com/railway-oriented-programming-slideshare-140312155941-phpapp01/95/railway-oriented-programming-151-638.jpg?cb=1427456657)

---
@title[Epilogue]

프로그래밍을 잘하기 위해서 수많은 방법들이 있고 그 중 더 자신에게 맞는 방법이 있을 것입니다. 하지만 적어도 잘 몰라서 가지 못하는 길이 있다면 너무 아쉬울 수 있습니다.

함수형 패러다임은 오래 발전한 분야이고 현재 많은 언어와 설계에 많은 기여를 하고 있습니다.

이것이 반드시 정답은 아니겠지만, 만약 프로그래밍을 하다가 보다 나은 프로그래밍에 대한 고민이 들 때 즈음,

---
@title[Epilogue]

## Thinking functionally

어떠신가요?
