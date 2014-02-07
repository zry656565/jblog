---
layout: post
date: 2014-02-04 21:33:00 UTC
title: Deep Copy in Javascript
categories: en posts
---

*<small>In this article, I will show you how to realize a deep-copy method for javascript.</small>*

In the world of javascript，All "object" variables pass the address not value when assigning. Maybe there is someone asking which variable is a "object". illustrate with examples might be better:

{% highlight javascript %}
typeof(true)    //"boolean"
typeof(1)       //"number"
typeof("1")     //"string"
typeof({})      //"object"
typeof([])      //"object"
typeof(function(){})  //"function"
{% endhighlight %}

So in fact, our main problem is the "object". To non-"object", just do the normal assignment. My thinking of realizing js deep-copy is：

- Traverse all the properties of the object,
- If the property is "object", it requires special treatment.
  - If the "object" is so special to be an Array, then create a new array and copy all the things in it.
  - If the "object" is non-Array, call Deep Copy method recursively.
  - Special attention!! Here I haven't done deep-copy for the objects in arrays. If you need this in your practical application,the following code should to be modified.
- If non-"object", just do the normal assignment.

Here is my realization:

{% highlight javascript %}
Object.prototype.DeepCopy = function () {
  var obj, i;
  obj = {};

  for (attr in this) {
    if (this.hasOwnProperty(attr)) {
      if (typeof(this[attr]) === "object") {
        if (Object.prototype.toString.call(this[attr]) === '[object Array]') {
          obj[attr] = [];
          for (i=0; i<this[attr].length; i++) {
            obj[attr].push(this[attr][i]);
          }
        } else {
          obj[attr] = this[attr].DeepCopy();
        }
      } else {
        obj[attr] = this[attr];
      }
    }
  }
  return obj;
};
{% endhighlight %}

The advantage of implementing DeepCopy on Object.prototype is that all objects inherit this method. The downside is that some libraries will rewrite Object, so conflicts sometimes occur. And specific usage is as follows:

{% highlight javascript %}
Object.prototype.DeepCopy = function () { ... }
var a = { x:1 };
var b = a;
var c = a.DeepCopy();
a.x = 2;
b.x = 3;
console.log(a.x);   //3
console.log(b.x);   //3
console.log(c.x);   //1
{% endhighlight %}

***

**If you publish this article in your blog, please indicate the source：[jerryzou.com](http://jerryzou.com)**
