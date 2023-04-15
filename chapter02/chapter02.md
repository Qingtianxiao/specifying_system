## 1. 总结

## 2. 概念

### 1. Behaviros

~~~
Formally, we define a behavior to be a sequence of states, where a state is an assignment of values to variables. We specify a system by specifying a set of possible behaviors--the ones representing a correct execution of the system.
~~~

### 2. An Hour Clock

对于一个12小时制的时钟系统，变量hr表示时钟显示的值

~~~
A typical behavior of the clock is the sequence
(2.1) [hr = 11] --> [hr = 12] --> [hr = 1] --> [hr = 2] --> ...
of states, where [hr = 11] is a state in  which the variable hr has the value 11.
A pair of successive states, such as [hr = 11] --> [hr = 12], is called a step.
~~~

~~~
HCini := hr ∈ {1, ... , 12}
HCnxt := hr' = IF hr ≠ 12 THEN hr + 1 ELSE 1
~~~

HCnxt is an ordinary mathematical formula, except that it contains primed(hr') as well as unprimed(hr) variables.Such a formula is called an action.

~~~
An action is true or false of a step. A step that satisfies the action HCnxt is called an HCnxt step.
~~~

~~~
the temporal-logic operator □
	The temporal formula □F asserts that formula F is always true. In particular, □HCnxt is the assertion that HCnxt is true for every step in the behavior.
~~~

HCini /\ □HCnxt is true of a behavior iff the initial state satisfies HCini and every step satisfies HCnxt.

在Temperature实验中，需要让时钟系统存在hr' = hr的step。那么仅需要HCnxt是不够的，所以，the formula应该为

~~~
HCnxt \/ (hr' = hr)
~~~

为了方便，上述formula可以简写为

[HCnxt]<sub>hr</sub>

正如□ operator定义一样，我们希望上述的formula一直为true，那么the formula

~~~
□(HCnxt \/ (hr' = hr))
~~~

也即

□([HCnxt]<sub>hr</sub>)

那么，最终的formula为

HCini /\ □([HCnxt]<sub>hr</sub>)

这时候再回过头来聊一下behavior这个概念，behavior是一个状态序列，当formula只是HCini /\ □HCnxt时，那么对于同一个初始状态，其behavior是相同的，感觉没有必要单独聊这个behavior概念；但现在，当formula为HCini /\ □([HCnxt]<sub>hr</sub>)时，可以看出来即便初始状态相同，也可以产生不同的behavior，即产生不同的状态序列，这在原文中是这样体现的：

~~~
in fact, it allows the behavior
[hr = 10] --> [hr = 11] --> [hr = 11] --> [hr = 11] --> ...
that ends with an infinite sequence of stuttering steps(hr' = hr).
~~~

最终，还是总结归纳了整个公式

HC := HCini /\ □([HCnxt]<sub>hr</sub>)

### 3. A Closer Look at the Specification

~~~
A state is an assignment of values to variables, but what variables? The answer is simple: all variables
~~~

上文这句话还是需要稍微斟酌一下的，毕竟在TLA<sup>+</sup>语言中，在描述状态转移方程时，需要将所有变量的状态都要描述出来，尽管有些变量的值并没有发生改变，所以在上文中，也是强调了这一点。

~~~
Formula HC is a temporal formula. A temporal formula is an assertion about behaviors.
We say that a behavior satisfies HC iff HC is a true assertion about the behavior.
~~~

下面这一段原文描述蛮有意思的

~~~
Formula HCini asserts that hr is an integer from 1 through 12, and □HCini asserts that HCini is always true. So, □HCini should be true for any behavior satisfying HC. Another way of saying this is that HC implies □HCini, for any behavior. Thus, the formula HC ⇒ □HCini should be satisfied by every behavior. A temporal formula satisfied by every behavior is called a theorem, so HC ⇒ □HCini should be a theorem.
~~~

### 4. The Specification in TLA<sup>+</sup>

### 5. An alternative Specification

~~~
i % n is the natual number less than n satisfying i = q * n + (i % n) for some natural number q.
~~~

~~~
The Naturals module defines Nat to be the set of natural number, and the assertion that there exists a q in the set Nat satsfying a formula F is written ∃ q ∈ Nat : F. Thus, if i and n are elements of Nat and n > 0, then i % n is the unique number satisfying
	(i % n ∈ 0 .. (n - 1)) /\ (∃ q ∈ Nat :  i = q * n + (i % n))
~~~

above all，可以重写一下hour-clock

HCnxt2 := hr' = (hr % 12) + 1		HC2 := HCini /\ □[HCnxt2]<sub>hr</sub>

这里有一点需要注意的是，两个Action HCnxt和HCnxt2不相等，但是Formula HC和HC2是等价的

