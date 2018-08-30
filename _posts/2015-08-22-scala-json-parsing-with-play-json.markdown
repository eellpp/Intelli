---
layout: post
title: "Scala Json Parsing With Play-JSON"
date: 2015-08-22T00:00:00+08:00
tags: scala
---

There are many JSON libraries for scala but here we are using play-json which is part of Play framework but also can be used independently.

I am using [ammonite repl](https://github.com/lihaoyi/Ammonite) to try out the json parsing on console. 

{% highlight bash %}
# load the libraries
load.ivy("com.typesafe.play" %% "play-json" % "2.4.0") 
import play.libs.Json._

var rawJson = """ {"name": "John", "age": 20, "address": "#42 milky way", "tags" : [ "freshman", "scholar" ] } """
{% endhighlight %}

The first step is going from a JSON string to JsValue objects by parsing the json string. The JsValue tree can be parsed by using \ and \\ where 

- \ selects an element in a JsObject, returning a JsValue
- \\ selects an element in the entire tree returning a Seq[JsValue]

{% highlight bash %}
Json.parse(rawJson) \ "name"

# convert from JsonValue to desired typing 
(Json.parse(rawJson) \ "name").as[String]
(Json.parse(rawJson) \ "age").as[Int]
(Json.parse(rawJson) \ "age").asOpt[Int]
(Json.parse(rawJson) \ "name" \ “address” ).as[String]
# get the first tag
(Json.parse(rawJson) \ "tags")(0).as[String]

# if this was an array of people information then we can take each of the name as
(json \\ "name").map(_.as[String])

(Json.parse(rawJson) \ "name") match {
            case JsDefined(name) => println(name)
            case error:JsUndefined => println(error)
            case _ => println("Invalid type")
  }

{% endhighlight %}


If we are unsure about the content of JsValue then we can use asOpt which will return a None if deserializing causes and exception.

{% highlight bash %}
(Json.parse(rawJson) \ "name").asOpt[String]
{% endhighlight %}

If we want a boolean then we can use the validate method which returns JsSuccess and JsError 
Better to use the validate method to check the parsed json

{% highlight bash %}
(Json.parse(rawJson) \ "name").validate[String] match {
  case s: JsSuccess[String] => println("Name: " + s.get)
  case e: JsError => println("Errors: " + JsError.toJson(e).toString())
}
{% endhighlight %}



Convert the JSON structure to scala data model
To read from json we need to define a Reads[T] object for each class which defines how we read each incoming json object for the class.
Reads[T] and Writes[T] objects have to be defined to read and write from json object. If the format for both are same then instead we can use the Format[T] object

{% highlight bash %}
case class Student (name: String, age: Int) 

implicit val student : Reads[Student]  = (
  (JsPath \\ "name").read[String] and
  (JsPath \\ "age").read[Int]
)(Student)

val person = Json.parse(rawJson).as[Student] 
studentsObj.name 
studentsObj.age

#Convert to a list of students

rawJson = """ [ {"name": "John", "age": 22 }, {"name": "Alice", "age": 20 } ] """

val people = Json.parse(rawJson).as[List[Students]] 
#names of students
people.map(_.name)

{% endhighlight %}

#### Examples of reading some other type of data structures
 

{% highlight bash %}
rawJson = """ {"school": "public school" ,"students": [ {"name": "John", "age": 22 }, {"name": "Alice", "age": 20 } ] } """

val json = (Json.parse(rawJson) \ "students")
val nameReads: Reads[List[Students]] = (JsPath \ "students").read[List[Students]]
val nameResult: JsResult[List[Students]] = json.validate[List[Students]](nameReads)

val parseResult = (Json.parse(rawJson) \ "students").read[List[Students]]

val people = (Json.parse(rawJson) \ "students").as[List[Students]] 

{% endhighlight %}



#### Handling null values

{% highlight bash %}
implicit val student : Reads[Student]  = (
  (JsPath \\ "name").readNullable[String] and
  (JsPath \\ "age").readNullable[Int]
)(Student)
With readNullable we get an Option[T] object which means that field itself is optional
{% endhighlight %}


#### Handling missing values

{% highlight bash %}
implicit val student : Reads[Student]  = (
  (JsPath \\ "name").read( Reads.optionNoError[String] ) and
  (JsPath \\ "age").read[Reads.optionNoError[Int]]
)(Student)
{% endhighlight %}

This is cause error if name and age are missing in the json data field

#### Handling validation
The read and readNullable have an implicit parameter which can be used for validation with constraints like email,  maxLength, filter, pattern and more ...

{% highlight bash %}
  (JsPath \\ "name").readNullable[String](minLength[String](10)) and
  (JsPath \\ "email").read[String](email)
{% endhighlight %}

https://www.playframework.com/documentation/2.4.x/api/scala/index.html#play.api.libs.json.ConstraintReads


Reference:

- https://www.playframework.com/documentation/2.5.x/ScalaJson

