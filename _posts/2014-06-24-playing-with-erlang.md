---
layout: post
title:  "Playing With Erlang"
date:   2014-06-24 21:00:00
tags: 
- erlang
- prolog
---

I have been hearing a lot about [Erlang](http://www.erlang.org/) lately. I always assumed it was some arcane, but fun and interesting language that people in the academia write papers on. Well, turns out I am mostly wrong. Firstly, the softwares like Riak, RabbitMQ, CouchDB and WhatsApp which I more or less use on a daily basis are all written in Erlang. Secondly, the language was developed in Ericsson, a Swedish Telecommunication company.

## First Glance

At a first glance, I was really surprised to see some sample Erlang code. It was not because it looked vastly different from the everyday language I was used to. Rather it was because it looked very similar to Prolog, of which I had taken a short course while in college. In a nutshell, it is a functional(not purely functional), dynamically typed, concurrent and fault tolerant programming language. The latter two definitely are the major reasons why Erlang is gaining tremendous popularity in this age of cloud.

## Getting Started

Erlang can be installed either via downloading the binaries from the [website](http://www.erlang.org/download.html) or by simply using the package manager provided by the OS.

Typing `erl` in the terminal will open the Erlang REPL shell. Here is a hello world [example](/2014/06/22/hello-world/) (hint: it's towards the bottom) that can be tested in the Erlang shell.

For anything more complex, it is easier to write the code to an external `.erl` file and then dynamically loading it in the Erlang shell.

## Examples

Lets make a sample file `test.erl` with the following content.

{% highlight erlang linenos=table %}
-module(test).
-export([len/1]).

len([]) -> 0;
len([_|T]) -> 1 + len(T).
{% endhighlight %}

Before I explain what those lines mean, let us compile and run it.
In your Erlang shell, type `c(test).` to compile the test file. You should get a tuple saying ok. Then, lets find the length of a list using the following syntax. `test:len([1,3,4,5,11,23,111]).`. The shell should output the size of the given list.

{% highlight bash %}
$ erl
Eshell V5.10.4  (abort with ^G)
1> c(test).
{ok,test}
2> test:len([1,3,4,5,11,23,111]).
7
3> 
{% endhighlight %}

Yup, we did not have to loop throught the list at all, and yet it accurately produces the correct result. There is no traditional looping in Erlang. Looping and traversing is done via recursion.

### Explanation

So, here is whats going on.
<ol>
<li>Line 1 declares the module name</li>
<li>Line 2 exposes the len function of the module and we specify that the function takes just one argument</li>
</ol>

Before jumping to Line 3, it is important to know that Erlang uses pattern matching mechanism to bind variables (only those varialbes that start with a capital letter can be bounded). An underscore simply matches anything but does not bind.
<ol>
  <li>So, Line 4 basically means that if the argument is simply an empty list, then return 0</li>
  <li>Now, Line 5 will try to match any other non-empty argument. The first item in the argument list will simply be ignored while the remaining items in the list will be bound to <code>T</code>. Now the function returns 1 added to the length of the remaining items in the list.</li>
</ol>

Fortunately, Erlang compiler is optimized for tail recursion. However, I am sure there are casess where if not careful enough, one might shoot oneself in the foot. 

Here is another example which returns the reverse of a List.
{% highlight erlang %}
-module(test).
-export([rev/1]).

rev(L) -> rev(L, []).
rev([],R) -> R;
rev([H|T],R) -> rev(T,[H|R]).
{% endhighlight %}

Here is how to find the nth element in a list.

{% highlight erlang %}
-module(test).
-export([nth/2]).
nth(N,L) -> nth(N,L,0).
nth(_,[],_) -> nil;
nth(N,[H|T],I) -> if N == I -> H;
           I >= N -> nil;
           true -> nth(N,T,I+1)
          end.
{% endhighlight %}

And this one liner returns the square of each item in the list.
`square(L) -> [X*X || X <- L].` It reads for every item X in the List L, square X and add it to the return list.

Overall, it is pretty intersting and a really different way of writing code than the main stream languages like Java, Javascript or C#. However, one of the most powerful features of Erlang lies in its ability in concurrent programming at the language level. It is very easy to spawn new processes and communicate amongst each other via message passing. Paraphrasing someone from a podcast, "Even if your code is higly unoptimized and its spawning tons of processes, it might actually be a while before you start noticing something wrong".

Here is an excellent [tutorial](http://learnyousomeerlang.com/) if you are interested in learning more about Erlang.




