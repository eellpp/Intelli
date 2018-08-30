---
layout: post
title: "Scala Short Learning Notes"
date: 2014-12-22T00:00:00+08:00
tags: scala
---

These are various notes and snippets from various sources that i found useful while learning scala

### Returns are discouraged
In fact, odd problems can occur if you use return statements in Scala because it changes the meaning of your program. For this reason, return statement usage is discouraged.
The return keyword is not “optional” or “inferred”; it changes the meaning of your program, and you should never use it.

<http://tpolecat.github.io/2014/05/09/return.html>

### Important thing in scala collection

Basic : Seq, MaP and Set
In seq we 

- Indexed
- Buffer
- Linear

Where indexed is of type : array, range, vector

Linear is of type : List, Stack, Queue

Buffer  : ArrayBuffer and ListBuffer

For mutable seq where you want to keep changing elements, use the buffer type. Decide list or array based on whether accessing by index or traversing the elements


### When to use listBuffer
If there is list that is constantly changing then use list buffer and later convert to list. List by itself is immutable.
If however you accessing a random element like x(1000) then better use ArrayBuffer which is indexed.

{% highlight bash %}
t.append(123)
t += (6,6,6) 

// remove an element
t -= (6,6,6) 
// remove by index
t.remove(0)
t.remove(0,1)  
x.update(0,9)

#You can also use --= to delete multiple elements that are specified in another collection:

scala> val x = ListBuffer(1, 2, 3, 4, 5, 6, 7, 8, 9)
x: scala.collection.mutable.ListBuffer[Int] = ListBuffer(1, 2, 3, 4, 5, 6, 7, 8, 9)

scala> x --= Seq(1,2,3)
res0: x.type = ListBuffer(4, 5, 6, 7, 8, 9)
{% endhighlight %}



### What type should my API accept as input?

As general as possible. In most cases this will be Traversable <- Seq <- List.
Explanation: We want our API consumers to be able to call our code without needing them to convert types. If our function takes a Traversable, the caller can put almost any type of collection. This is usually sufficient if we just map(), fold(), head(), tail(), drop(), find(), filter() or groupBy(). In case you want to use length(), make it a Seq. If you need to efficiently prepend with ::, use a List.

### What type should my API return?

As specific as possible. Typically you want to return a List, Vector, Stack or Queue.
Explanation: This will not put any constraints on your API consumers but will allow them to eventually process returned data in optimal way and worry less about conversions.

### Vectors

vectors are faster than list for random access of elements . 
List is better when most of usage is around accessing the head or tail of the list
vectors are immutable. The update method gives a new vector that differs in only single element
vectors are implemented as tree

{% highlight bash %}
// Begin with an empty vector.
val vector = scala.collection.immutable.Vector.empty

// Add new value at end of vector.
val vector2 = vector :+ 5

// Add 2 new values at end of vector.
val vector3 = vector2 :+ 10 :+ 20

// Add new value at start of vector.
val vector4 = 100 +: vector3

// Add elements from List of Ints to end of vector.
val v2 = v ++ List(10, 20, 30)

// Vector can contain elements of different types
Vector(1, "string") 
res71: Vector[Any] = Vector(1, sdf)

// Update element at index 1.
val changed = v2.updated(1, "bear")

// output of an expression running for n times
Vector.fill(5){
println("hello")
}

// Numbers in range
@ Vector.range(1,10) 
res73: Vector[Int] = Vector(1, 2, 3, 4, 5, 6, 7, 8, 9)
@ Vector.range(1,10,2) 
res74: Vector[Int] = Vector(1, 3, 5, 7, 9)


// calculate with the index of length 
Vector.tabulate(10){ _*5} 
res75: Vector[Int] = Vector(0, 5, 10, 15, 20, 25, 30, 35, 40, 45)
{% endhighlight %}

### Should I use List or Vector?

You most probably want a Vector, unless your algorithm can be expressed only using ::, head and tail, then use a List.

Scala Vector has a very effective iteration implementation and, comparing to List is faster in traversing linearly (which is weird?), so unless you are planning to do quite some appending, Vector is a better choice. Additionally, Lists don’t work well with parallel algorithms, it’s hard to break them down into pieces and put together in an efficient way.


### Useful factory methods on collections

<http://www.scala-lang.org/docu/files/collections-api/collections_45.html>


### Scala Operators

->, ||=, ++=, <=, _._, ::, and :+=.

<http://stackoverflow.com/questions/7888944/what-do-all-of-scalas-symbolic-operators-mean>


### Lazy Val
Lazy val are evaluated once when they are used. They are only evaluated once. 
Sometimes its useful to have a lazy val than a function

lazy  val x = { println(“do something”); “some value”}

The value of x will be evaluated only once when first used.

### Partial functions
val t :PartialFunction[Int,String] = { case i:Int  if i > 10 => i.toString}

This is partial since the function is defined only for few values of i 

{% highlight bash %}
@ t.isDefinedAt(2) 
res10: Boolean = false

t(1) will give an error, so instead to get a None use:

@ t.lift(2) 
res11: Option[String] = None
 
List(1,12) map t.lift 
res14: List[Option[String]] = List(None, Some("12"))

@ List(1,12) collect t 
res15: List[String] = List("12")

@ t.lift(21) map( _.toString) getOrElse "0" 
res30: String = "21"
@ t.lift(2) map( _.toString) getOrElse "0" 
res31: String = "0"
{% endhighlight %}

### Chaining partial functions

The orElse method defined on the PartialFunction trait allows you to chain an arbitrary number of partial functions, creating a composite partial function. The first one, however, will only pass on to the next one if it isn’t defined for the given input.

val handler = fooHandler orElse barHandler orElse bazHandler

Where fooHandler, barHandler etc can come from different traits. So this allows composition 

Infix operations in scala
 1 + 5*2 is same as 1.+(5*2)

### Apply
Every function in Scala can be treated as an object and it works the other way too - every object can be treated as a function, provided it has the apply method. Such objects can be used in the function notation:

{% highlight bash %}
// we will be able to use this object as a function, as well as an object
object Foo {
  var y = 5
  def apply (x: Int) = x + y
}

Foo (1) // using Foo object in function notation 
{% endhighlight %}

This is useful for the factory pattern.

### Package

package create a lexical namespace in which classes are declared. To help segregate code in such a way that it doesn't conflict with one another.

you can use import anywhere inside the client Scala file, not just at the top of the file and correspondingly, will have scoped relevance

By using the underscore , you effectively tell the Scala compiler that all of the members inside BigInteger should be brought into scope.


### Trait Self Annotation

self : Base => 

Consider the examples below where both the traits are providing same functionalities. 

class Base {
  def magic = "bibbity bobbity boo!!"
}

trait Extender extends Base {
  def myMethod = "I can "+magic
}

trait SelfTyper {
  self : Base => 
  
  def myMethod = "I can "+magic
}

But the two are completely different. 
Extender can be mixed in with any class and adds both the "magic" and "myMethod" to the class it is mixed with. 
SelfTyper can only be mixed in with a class that extends Base and SelfTyper only adds the method "myMethod" NOT "magic". 

Why is the "self annotations" useful? Because it allows several provides a way of declaring dependencies. One can think of the self annotation declaration as the phrase "I am useable with" or "I require a".


### implicit

{% highlight bash %}
> val i: Int = 3.5
<console>:5: error: type mismatch;
found   : Double(3.5)
required: Int
val i: Int = 3.5

scala> implicit def doubleToInt(x: Double) = x.toInt
doubleToInt: (Double)Int
  
scala> val i: Int = 3.5
i: Int = 3
{% endhighlight %}
<http://www.artima.com/pins1ed/implicit-conversions-and-parameters.html>

### Use Option not null

Option is an abstract class in Scala and defines two subclasses, Some and None.
Every now and then you encounter a situation where a method needs to return a value or nothing. But in the case of Scala, Option is the recommended way to go when you have a function return an instance of Some or otherwise return None.
You can use Option with pattern matching in Scala, and it also defines methods like map, filter, and flatMap so that you can easily use it in a for-comprehension.

Use get for getting the map values

{% highlight bash %}
scala> val t = Map(("k1","v1") , ("k2","v2"))
scala> t.get("k1")
res4: Option[String] = Some(v1)
scala> t.get("k1").toString.length
res7: Int = 8
{% endhighlight %}

### Convert scala list to java list

Scala list and java list are not compatible. When interfacing with java libarary you have to convert it from one form to another
def toJavaList(scalaList: List[BasicNameValuePair]) = { 
java.util.Arrays.asList(scalaList.toArray:_*)					
} 
_* is required to get all all the values of the list

### Use require inside class to enforce 

require(args.size >= 2, "at minimum you should specify action(post, get, delete, options) and url")

### Convert tuple to map key and value

{% highlight bash %}
scala> val t = (("k1","v1") , ("k2","v2"))
t: ((String, String), (String, String)) = ((k1,v1),(k2,v2))

scala> val t = Map(("k1","v1") , ("k2","v2"))
t: scala.collection.immutable.Map[String,String] = Map(k1 -> v1, k2 -> v2)

scala> t("k1")
res3: String = v1
{% endhighlight %}

### Exists
{% highlight bash %}
// checks if string has upper case characters
name.exists(_.isUpper)
"asdf".contains("k")
// for list
(1 to 10).contains(5)
{% endhighlight %}


### Type of constructors

#### primary constructor : the class body is primary constructor
{% highlight bash %}

class Greeter(var message: String) {
    println("A greeter is being instantiated")    
    message = "I was asked to say " + message
    def SayHi() = println(message)
}
val greeter = new Greeter("Hello world!")
greeter.SayHi()
{% endhighlight %}

#### auxiliary constructor: 

{% highlight bash %}
class Greeter(message: String, secondaryMessage: String) {
    def this(message: String) = this(message, "")    
    def SayHi() = println(message + secondaryMessage)
}

val greeter = new Greeter("Hello world!")
greeter.SayHi()
{% endhighlight %}



### Slice

{% highlight bash %}
Slice :use c slice(from, to)
scala> Vector("hello", "world", "this", "is", "was") slice(2,5)
res38: scala.collection.immutable.Vector[String] = Vector(this, is, was)
{% endhighlight %}


### Zip
{% highlight bash %}
List(1,2,3) zip List("one","two","three")
res36: List[(Int, String)] = List((1,one), (2,two), (3,three))
{% endhighlight %}


### dropWhile and takeWhile
Use dropWhile and takeWhile when you want to longest seqeunce from start when the predicate is true
{% highlight bash %}
scala> Vector(1, 2, 3, 4, 5, 6) dropWhile { _ < 4}
res31: scala.collection.immutable.Vector[Int] = Vector(4, 5, 6)

scala> Vector(1, 2, 3, 4, 5, 6) takeWhile { _ < 4}
res32: scala.collection.immutable.Vector[Int] = Vector(1, 2, 3)
{% endhighlight %}

### collect
{% highlight bash %}
collect is used with partial functions as
List("hello","World",42) collect { case s:String => s}
// A instance of Seq, Set or Map is actually a partial function. So,
Seq(0,2,45) collect List("hello","World",42)
{% endhighlight %}


### predicate
A predicate is a anonymous function that takes one or more arguments and returns boolean

filter(predicate)

eg: List(1,2,3,4).filter(p => p % 2 == 0)
or  List(1,2,3,4).filter(_ % 2 == 0)


### Mutable Hashmap
For dictionary use the mutable HashMap

collection.mutable.Map[String, String]


### flatMap

{% highlight bash %}
flatMap, applies the map operation then the flattens the list of list
eg: scala map treats the string as array of characters
scala> "hello".map(x => "." + x)
res90: scala.collection.immutable.IndexedSeq[String] = Vector(.h, .e, .l, .l, .o)

scala> "hello".flatMap(x => "." + x)
res95: String = .h.e.l.l.o

scala> "hello".map(x => "." + x).flatten.mkString
res100: String = .h.e.l.l.o

scala> "hello".map(x => "." + x).toList.mkString
res94: String = .h.e.l.l.o
{% endhighlight %}

### Array To List
a.toList

Count (based on predicate)
Array(1,2,3,4,5,6).count(x =>  x > 3)

Exists (based on predicate)
Array(1,2,3,4,5,6).exists(x => x == 3)


### anonymous function
{% highlight bash %}
val t : Int => Boolean = (x :Int) => x > 5
val t1 = (x :Int) => { x > 5} :Boolean

val t2 = new ( (Int) => Boolean ) {
  def apply(x:Int) = x > 5
}
val t3 = new Function1[Int,Boolean] {
  def apply(x:Int) =  x > 5
}

val t4 = new {
  def apply(x :Int) : Boolean = x > 5
}
{% endhighlight %}

### Scala functions as arguments
{% highlight bash %}
val t : Int => Boolean = (x :Int) => x > 5
def someFunc(predicate:Int => Boolean, x :List[Int]){
  x.filter(t => predicate(t)).foreach(println)
}

someFunc(t,List(1,34, 5,34, 4,23))

val t5 = (x1: Int, x2:Int) => {x1 > x2} :Boolean
def someFunc2(predicate:(Int,Int) => Boolean, x :List[Tuple2[Int,Int]]){
  x.filter(t => predicate(t._1,t._2)).foreach(println)
}
someFunc2(t5,List((1,2),(5,4),(9,3)))
{% endhighlight %}


### Functions vs Method
{% highlight bash %}
//method
def m(i :Int) = i*i
// function
val p = (i :Int) => i*i
m(2)
p(2)
//m cannot be called without arguments
p 
// res2: Int => Int = <function1>
// method can be converted to function
val t = m _
{% endhighlight %}


### Scala curry functions

{% highlight bash %}
def curryFunc(a:Int)(b:Int) = a + b
var t1 = curryFunc(5) _
t1(2)
{% endhighlight %}


### Scala multiple parameters per list

def multipleParametersPerList(num : Int*) = num.sum

multipleParametersPerList(1,2,3) // will give 6


### Prepending and appending to list
var x = 4 :: List(1,2) 

x = x :+ 0 


### For and foreach

{% highlight bash %}
// for returns value (like select C#)
val t = for(i <- List(1,2,3)) yield i*2
val t = for(i <- List(1,3,5) if i < 5) yield i
// foreach returns Unit (void)
List(1,2,3).foreach(i => println(i*2))

for(x <- t if x != "a") yield x
t.filter(x => x != "a").foreach(println)
{% endhighlight %}


### Function Literals
{% highlight bash %}
class person(name :String) {def introduce = println(s" my name is $name")}
object person{def apply(name : String) = new person(name)}
var t = List[person](person("john"),person("lee"),person("kim"))
t foreach(_.introduce)
t foreach( x => x.introduce)
Instead of lambda, function literals can be used
{% endhighlight %}


### Add to list

{% highlight bash %}
var t = List(1,2,3) 
t ::= 4 // will give Kist(4,1,2,3)
// compiler will turn ::= to > t = 4 :: t
{% endhighlight %}


### Traits
Breaking up your application into small, focused traits is a powerful way to create reusable, scalable abstractions and “components.” Complex behaviors can be built up through declarative composition of traits. 

But also splitting objects into too many fine-grained traits can obscure the order of execution in your code!


### Less Exception Handling
Scala encourages a coding style that lessens the need for exceptions and exception handling.


### Map
Map by default are immutable . Import mutable map


### Companion object
Companions must be defined together with class

### Join a list of strings
> strings.mkstring


### traits vs abstract class 
There are two main reasons to use an abstract class in Scala: 
You want to create a base class that requires constructor arguments. 

The code will be called from Java code.


### Pattern Matching

#### Simple 
{% highlight bash %}
for (bool <- bools) { 
	bool match {
		case true => println("heads")
		case false => println("tails")
		case _ => println("something other than heads or tails (yikes!)")
	} 
}
{% endhighlight %}

#### Variable Match
{% highlight bash %}
case i: Int => println("got an Integer: " + i)
{% endhighlight %}

#### Sequence Match
{% highlight bash %}
case List(_, 3, _, _) => println("Four elements, with the 2nd being '3'.")
case List(_*) => println("Any other list with 0 or more elements.") }
{% endhighlight %}

#### Tuples match and guards
{% highlight bash %}
case (thingOne, thingTwo) if thingOne == "Good" => println("A two-tuple starting with 'Good'.")

Case class match
case Person("Alice", 25) => println("Hi Alice!") 
case Person("Bob", 32) => println("Hi Bob!") 
case Person(name, age) =>println("Who are you, " + age + " year-old person named " + name + "?")
{% endhighlight %}

#### Regular Expression match
{% highlight bash %}
val date = """(\d\d\d\d)-(\d\d)-(\d\d)""".r
"2004-01-20" match {
  case date(year, month, day) => s"$year was a good year for PLs."
}
{% endhighlight %}


