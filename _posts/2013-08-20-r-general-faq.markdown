---
layout: post
title: "R General FAQ"
date: 2013-08-20T00:00:00+08:00
tags : R
---

### Pass by Promise
R does not copy the variables when passing them around unless there is change involved. From the R language manuals
<http://cran.at.r-project.org/doc/manuals/R-lang.html#Evaluation>

> R has a form of lazy evaluation of function arguments. Arguments are not evaluated until needed. It is important to realize that in some cases the argument will never be evaluated. Thus, it is bad style to use arguments to functions to cause side-effects. While in C it is common to use the form, foo(x = y) to invoke foo with the value of y and simultaneously to assign the value of y to x this same style should not be used in R. There is no guarantee that the argument will ever be evaluated and hence the assignment may not take place.

> It is possible to access the actual (not default) expressions used as arguments inside the function. The mechanism is implemented via promises. When a function is being evaluated the actual expression used as an argument is stored in the promise together with a pointer to the environment the function was called from. When (if) the argument is evaluated the stored expression is evaluated in the environment that the function was called from. Since only a pointer to the environment is used any changes made to that environment will be in effect during this evaluation. The resulting value is then also stored in a separate spot in the promise. Subsequent evaluations retrieve this stored value (a second evaluation is not carried out). Access to the unevaluated expression is also available using substitute.

> When a function is called, each formal argument is assigned a promise in the local environment of the call with the expression slot containing the actual argument (if it exists) and the environment slot containing the environment of the caller. If no actual argument for a formal argument is given in the call and there is a default expression, it is similarly assigned to the expression slot of the formal argument, but with the environment set to the local environment.

> The process of filling the value slot of a promise by evaluating the contents of the expression slot in the promise’s environment is called forcing the promise. A promise will only be forced once, the value slot content being used directly later on.

This makes it easier to pass large objects like dataframe around functions for various analysis. But once you modify the object, a copy will be made (avoiding side effects). 

### Pass by Reference

Sometimes its the intention to pass a large object across functions and transform this object while avoiding copying of data.  R package data.tablecan be used here which features  := and set() assign by reference to whatever object they are passed.

