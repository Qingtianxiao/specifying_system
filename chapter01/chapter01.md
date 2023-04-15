## 1. 总结



## 2. 概念

### 1. Propositional Logic

~~~
包含两个Boolean值以及5种操作：
    a. True and False
    b. /\ and    \/ or    ¬ or    ⇒ implies    ≡ equivalence 
~~~

关于5种操作的说明

~~~
/\: F /\ G equals TRUE iff both F and G equals TRUE
\/: F \/ G equals TRUE iff F or G equals TURE(or both do)
¬ : ¬F equas TRUE iff F equals FALSE
⇒ : F ⇒ G equals TRUE iff F equals FALSE or G equals TRUE(or both)
≡ : F ≡ G equals TRUE iff F and G both equal TRUE or both equal FALSE
~~~

对于第四条，作者给了真值表，在这里贴出来，虽然作者花了笔墨解释，但是还是有点难以理解

![1681525067629](C:\Users\LiJX\AppData\Roaming\Typora\typora-user-images\1681525067629.png)

个人认为[这里](https://math.stackexchange.com/questions/70736/in-classical-logic-why-is-p-rightarrow-q-true-if-p-is-false-and-q-is-tr)给出的示例相对直观，简单的贴个图

![1681525542126](C:\Users\LiJX\AppData\Roaming\Typora\typora-user-images\1681525542126.png)

类似于代数公式，propositional logic公式同样由三部分组成

~~~
values
operators
identifers(e.g. x y z)
~~~

但是，propositional logic公式的value只有TRUE and FALSE；operators只有五个Boolean operators /\ \/ ¬ ⇒ ≡

/\ \/满足交换律、结合律、分配率

关于implies

~~~
(x = 2) ⇒ (x = 2) \/ (y > 7) is true even if we know nothing about numbers.
~~~

这里(x = 2) ⇒ (x = 2) \/ (y > 7) equals FALSE iff (x = 2) [TRUE] 并且 (x = 2) \/ (y > 7)[FALSE]，很明显，当x=2为true时，(x=2)\/(y>7)也只能为true，所以在x、y为任意值时，(x = 2) ⇒ (x = 2) \/ (y > 7)都为true，表达式可以抽象为

~~~
F ⇒ F \/ G is ture, regardless of what the formulas F and G are.
~~~

对于上述抽象公式，这一类公式被定义为tautology，具体描述为

~~~
a tautology of propositional logic is a propositional-logic formula that is true for all possible truth values of its identifiers.
~~~

原文又给了另一个tautology的例子，here is the truth table showing that (F ⇒ G) ≡ (¬F \/ G) is a tautology

![1681527068394](C:\Users\LiJX\AppData\Roaming\Typora\typora-user-images\1681527068394.png)

### 2. Sets

关于符号∈的定义

~~~
x ∈ S means that x is an element of S. We often say is in instead of is an element of
~~~

set可以有有限个元素，也可以有无限个元素

~~~
Two sets are equal iff they have the same elements.
~~~

因此，{0, 1, 2} {2, 1, 0} {0, 0, 1, 2, 2} are the same set

~~~
The empty set, which we write {}, is the unique set that has no elements
~~~

定义在set之间的4种操作，两个操作数是集合，返回的也是集合

~~~
∩ 交集
∪ 并集
⊆ 子集
\ 差集
~~~

示例

~~~
S ∩ T: {1, -1/2, 3} ∩ {1, 2, 3, 5, 7} = {1, 3}
S ∪ T: {1, -1/2} ∪ {1, 5, 7} = {1, -1/2, 5, 7}
S ⊆ T: {1, 3} ⊆ {3, 2, 1}
S \ T: {1, -1/2, 3} \ {1, 5, 7} = {-1/2, 3}
~~~

### 3. Predicate Logic

自认为是产生了set之后，对于Propositional Logic的扩展操作

~~~
∀: for all
∃: there exists
~~~

~~~
∀ x ∈ S : F
	formula F is true for every element x in the set S.
∀ n ∈ Nat : n + 1 > n
	formula n + 1 > n is true for all elements n of the set Nat of natural numbers
~~~

~~~
∃ x ∈ S : F
	formula F is true for at lieast one element x in S.
∃ x ∈ Nat : (n ^ 2) = 2
	there exists a natural number n whose square equals 2. This formula happens to be false.
~~~

~~~
(∃ x ∈ S : F) ≡ ¬(∀ x ∈ S : ¬F) is a tautology of predicate logic, meaning that it is true for all values of the identifiers S and F.
~~~

由于空集中不包含任意元素，所以

~~~
∃ x ∈ {} : F is false for every formula F. this implies that ∀ x ∈ {} : F must be true for every F.
~~~

bounded的概念，如果formulas限定了元素必须在某个集合S中，那么这个公式就是bounded，否则为unbounded.

~~~
(∀ x ∈ S : F) ≡ (∀ x : (x ∈ S) ⇒ F)
(∃ x ∈ S : F) ≡ (∃ x : (x ∈ S) /\ F)
~~~

对于第一个公式，如果等号左侧为TRUE，那么

​	∀ x : (x ∈ S)为TRUE时，F为TRUE，则右侧整体为TRUE

如果等号左侧为FALSE，那么

​	∀ x : (x ∈ S)为TRUE时，F为FALSE，则右侧整体为FALSE

故等号两边等价

对于第二个公式，如果等号左侧为TRUE，那么

​	当 ∃ x : (x ∈ S) 为TRUE时，F为TRUE，整体为TRUE

如果等号左侧为FALSE，那么

​	∃ x : (x ∈ S) 为TRUE时，F为FALSE，整体为FALSE

故等号两边等价

~~~
(∃ x : F) ≡ ¬(∀ x: ¬F) is also a tautology.
~~~

~~~
(∀ x ∈ S : F) /\ (∀ x ∈ S : G) ≡ (∀ x ∈ S : F /\ G)
(∃ x ∈ S : F) \/ (∃ x ∈ S : G) ≡ (∃ x ∈ S : F \/ G)
for any set S and formulas F and G
~~~

~~~
∀ x ∈ S, y ∈ T : F means ∀ x ∈ S : (∀ y ∈ T : F)
∃ w,x,y,z 属于 S : F means ∃ w ∈ S : (∃ x ∈ S : (∃ y ∈ S : (∃ z ∈ S : F)))
~~~

