## 1. 概述

## 2. 总结

### 1. The first specification

CONSTANT Data

VARIABLES val, rdy, ack

TypeInvariant := (val ∈ Data) /\ (rdy ∈ {0, 1}) /\ (ack ∈ {0, 1})

~~~
A state funcjtion is an oridnary expression (one with no prime or □) that contain variables and constants.
A state predicate is a Boolean-valued state function
An invariant Inv of a specification Spec is a state predicate such that Spec ⇒ □Inv is a theorem
A variable v has type T in a specification Spec iff v ∈ T is an invariant of Spec
~~~

~~~
TypeInvariant := /\ val ∈ Data
			    /\ rdy ∈ {0, 1}
			    /\ ack ∈ {0, 1}
~~~

针对上式，The formula TypeInvariant will not appear as part of the specification. We do not assume that TypeInvariant is an invariant; the specification should imply that it is. In fact, its invariance will be asserted as a theorem.

The initial **predicate** is a straightforward. Initially, val can equal any element of Data. We can start with rdy and ack either both 0 or both 1.

~~~
Init := /\ val ∈ Data
	    /\ rdy ∈ {0, 1}
	    /\ ack = rdy
~~~

~~~
A step of protocol either sends a value or receives a value. We difine seperately the tow actions Send and Rcv that describe the sending and receiving of a value.
So, Next is defined to equal Sned \/ Rcv
~~~

~~~
Action Send is enabled in a state from it is possible to take a Send step. From the sample above, we see that Send is enabled iff rdy equals ack. Usually, the first question we ask about an action is, when is it enabled? 
~~~

这里是上面的答案**So, the definition of an action usually begins with its enabling condition**

~~~
Send := /\ rdy = ack
	    /\ val' ∈ Data
	    /\ rdy' = 1 - rdy
	    /\ UNCHANGED ack
~~~

Send定义的第一个条件rdy = ack，就是契合上文描述的**The definition of an action usually begins with its enabling condition**。

~~~
TLA defines UNCHANGED v to mean that the expression v has the same value in the old and new states.
More precisely, UNCHANGEDv equals v'= v, which v' is the expression obtained from v by priming all its variables.
~~~

接下来看Rcv

~~~
A Rcv step is enabled iff rdy is different from ack; it complements the value of ack and leaves val and rdy unchanged. Both val and rdy are left unchanged iff the pair of value val, rdy is left unchanged.
~~~

~~~
Rcv := /\ rdy ≠ ack
	   /\ ack' = 1 - ack
	   /\ UNCHANGED <val, rdy>
~~~

类似于clock，Spec应该允许stuttering steps

Spec := Init /\ □[Next]<sub><val, rdy, ack></sub>

完整的Spec如下

~~~
Init := /\ val ∈ Data
	    /\ rdy ∈ {0, 1}
	    /\ ack = rdy
	    
Send := /\ rdy = ack
        /\ val' ∈ Data
        /\ rdy' = 1 - rdy
        /\ UNCHANGED ack
        
Rcv := /\ rdy ≠ ack
       /\ ack' = 1 - ack
       /\ UNCHANGED <val, rdy>
       
Next := Send \/ Rcv

加上下面这一句
~~~

Spec := Init \/ □([Next]<sub><val, rdy, ack></sub>)

### 2. Another Specification

对上文Spec的改进，改进原因描述为

~~~
it's not well suited for helping to specify systems that use the interface. Let's rewrite the interface specificition in a form that makes it more convenient to user as part of a larger specification
~~~

下面是"record"这个term提出的背景

~~~
The first problem with the original specification is that it uses three variables to describe a single interface. A system might use several different instances of the interface. To avoid a proliferation(大量的) of variables, we replace the three variables val, rdy, ack with a single variable chan (short for channel). A mathmatician would do this by letting the value of chan be an ordered triple--for example, a state [chan = <-1/2, 0, 1>] might replace the state with val = -1/2, rdy = 0, and ack = 1. But programmers have learned that using tuples like this leads to mistakes; it's easy to forget if the ack line is represented by the second or third component. TLA there for provides records in addition to more conventional mathematical notation.
~~~

类似于结构体

~~~
Let's represent the state of the channel as a record with val, rdy, and ack fields. If r is such a record, then r.val is its val field. The type invariant asserts that the value of chan is an element of the set of all such records r in which r.val is an element of the set Data and r.rdy and r.ack are elements of the set {0, 1}. This set of records is written
~~~

[val : Data, rdy : {0, 1}, ack : {0, 1}]

~~~
The fields of a record are not ordered, so it doesn't matter in what order we write them.
~~~

Send() -> Send(d)的演进

~~~
1. Instead of defining an action Send that sends some unspecified data value, we difine the action Send(d) that sends data value d.
2. The next-state action is satisfied by a Send(d) step, for some d in Data, or a Rcv step.
3. A step is a Send(d) step for some d in Data means that there exists a d in Data such that the step satisfied Send(d)--in other words, that the step is an ∃ d ∈ Data : Send(d) step. So we define:
Next := (∃ d ∈ Data : Send(d)) \/ Rcv
~~~

The Send(d) action asserts that chan' equals the record r shuch that

​	r.val = d		r.rdy = 1 - chan.rdy		r.ack = chan.ack

This record is written in TLA as

​	[val |-> d, rdy |-> 1 - chan.rdy, ack |-> chan.ack]

还有一个enable condition，上文曾经出现过**The definition of an action usually begins with its enabling condition**，也即rdy = ack，所以，Send(d)的完整定义为

~~~
Send(d) :=
		 /\ chan.rdy = chan.ack
		 /\ chan' = [val |-> d, rdy |-> 1 - chan.rdy, ack |-> chan.ack]
~~~

关于chan'还有另外一种描述方式

~~~
we can describe the value of chan' by saying that it is the same as the value of chan except that its val field equal d and its rdy field equas 1 - chan.rdy
[chan EXCEPT !.val = d, !.rdy = 1 - chan.rdy]
~~~

**Think of the ! as standing for the new record that the EXCEPT expression forms by modifying chan. The symbol @ stands for chan.rdy**, so we can write this EXCEPT expression as

~~~
[chan EXCEPT !.val = d, !.rdy = 1 - @]
~~~

The definition of Rcv is straightforward. A value can be received when chan.rdy does not equal chan.ack, and receiving the value complements chan.ack.

~~~
Rcv :=
	 /\ chan.rdy ≠ chan.ack
	 /\ chan' = [chan EXCEPT !.ack = 1 - @]
~~~

**Second specification of  an asynchronous interface.**

~~~
EXTENDS Naturals
CONSTANT Datt
VARIABLE chan

TypeInvariant := chan ∈ [val : Data, rdy : {0, 1}, ack : {0, 1}]

Init := 
	   /\ TypeInvariant
	   /\ chan.ack = chan.rdy
	   
Send(d) := 
		 /\ chan.rdy = chan.ack
		 /\ chan' = [chan EXCEPT !.val = d, !.rdy = 1 - @]
		 
Rcv(d) :=
		/\ chan.rdy ≠ chan.ack
		/\ chan' = [chan EXCEPT !.ack = 1 - @]
		
Next := 
		(∃ d ∈ Data : Send(d)) \/ Rcv
		
Spec :=
		\/ Init
		\/ □[Next]chan
~~~

### 3. Types: A Reminder

**a variable v has type T in specification Spec iff v ∈ T is an invariant of Spec**. Thus, hr has type 1 .. 12 in the specification HC of the hour clock. The assertion does mean that, in every behavior satisfying formula HC, the value of hr is an element of 1 ..  12.

感觉目前只有两句话需要关注一下

1. TLA is an untyped language. 有关这个选择的答案在Section6.2
2. Type correctness is just a name for a certain invariance property.

### 3.4 Definitions

先看定义

~~~
If Ld is a simple identifier like Init or Spec, then the definition Id := exp defines Ld to be synonymous with the expression exp. Replacing Id by exp, or vice-versa(反之亦然)，in any expression does not change the meaning of that expression.
~~~

e.g. the definition **x := a + b** makes x * c equal to (a + b) * c, not to a + b * c.

defines Send(-5) to equal

~~~
/\ chan.rdy = chan.ack
/\ [chan EXCEPT !.val = 5, !.rdy = 1 - @]
~~~

注意了，Send(e) is an expression, for any expression e.

We say that Send is an **operator** that takes a single argument. We define operators that take more than one argument in the obvious way, the general form being

​	(3. 1) Ld(p<sub>1</sub>, ... , p<sub>n</sub>) := exp

where the p<sub>i</sub> are distinct identifiers and exp is an expression. We can consider defined identifiers like Init and Spec to be operators that take no argument, but we generally use operator to mean an operator that takes one or more arguments.

Use the term **symbol** to mean an identifier like Send or an operator symbol like +. Every symbol that is used in a specification must either be a built-in operator of TLA (like ∈) or it must be **declared** or **defined**. Every **declaration** or **definition** has a scope within which the symbol may be used. The sope of a VARIABLE or CONSTANT declaration, and of a definition, is the part of the module that follows it.





