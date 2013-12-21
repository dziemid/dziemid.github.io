---
layout: post
title: "Global Day of CodeRetreat 2013"
date: 2013-12-21 13:50:17 +1100
comments: true
categories: coderetreat
---

On 14th of December I've attended a CodeRetreat in Melbourne.
It was a [Global Day of CodeReterat](http://globalday.coderetreat.org) - the 3rd one I was a part of.
This year I've done something new, rather then facilitating I focused on enabling others to do so.
I have run 3 google hangouts trainings[^trainings] for first time facilitators
(
[#1](https://plus.google.com/events/cpssmqq8u4f8u5qkkk768b629i0)
[#2](https://plus.google.com/events/c4olgo868inn0n0aumkeqefe20g) ) and [hosts](https://plus.google.com/events/cgt5ts5a7nkk9lis8sdu00q0270).
I've also worked closely with the great team organizing CodeRetreat in Melbourne.
It was the first Global CodeRetreat I've attended, rather the facilitated :)


In Melbourne, as always the day was full of fun and learning :) We had a good mixed group
of around 25 developers. People were programming in Ruby, JavaScript, Java, PHP and probably some other languages as well.

CodeRetreat was hosted by REA with food sponsored by ThoughtWorks. REA was hosting us for the second time in the last 2 months -
not so long ago we had a [internal CodeRetreat](http://techblog.realestate.com.au/tdd-in-bash-aka-our-1st-internal-code-retreat-rea/) run there.

As far as our CodeRetreat goes, we followed fairly [standard format](http://coderetreat.org/facilitating/structure-of-a-coderetreat).
We started the first session around 10AM and we had 5 sessions in total. One of the interesting experiments we tried was "multi facilitation".
Our main facilitator was [Ilya Paripsa](https://twitter.com/iparips), however he had 4 other people helping him. So the first session was facilitated by
Ilya and Luke, second session by Luke and Qing and so on. The setup worked reasonably well, with the main benefit of giving more people opportunity to have a go
at facilitating. I wouldn't recommend it as a default arrangement[^default_arrangement], but it might be something to consider when you have many volunteers.

Our main theme of CodeRetreat was Test-Driven development. What exercises have we tried?

1. Warm up.
    - I was pairing in JavaScript.
1. Ping pong pair programming.
    - Paring again in JavaScript.
1. Short methods. No conditions.
    - I was pairing in Ruby, using OO polymorphism to get rid of conditions.
1. [Taking baby steps](http://coderetreat.org/profiles/blogs/taking-baby-steps)
    - I was pairing in Java.
1. Do whatever you like.
   - I was pairing in Ruby, using functional style and a lot of [pry](http://pryrepl.org/)
   - Afterwards, for fun, I've tried to fit our solution into [the tweet](https://twitter.com/dziemid/status/411784405460324352). (Took me 2 tweets).
     {% codeblock lang:ruby %}
     P=Struct.new(:x,:y){
     def v(x,y)P.new(x,y)end;
     def n
        [v(x-1,y-1),v(x,y-1),v(x+1,y-1),v(x-1,y), v(x+1,y),v(x-1,y+1),v(x,y+1),v(x+1,y+1)]
     end}
     def g(a)
        a.map(&:n).flatten.uniq.select{|p|b=(p.n&a).size;a.include?(p)&&b==2||b==3}
     end
     puts g([P.new(0,0));
     {% endcodeblock %}


I am looking forward to the next CodeRetreat, hopefully within next few months :)


[^default_arrangement]: Recommended way is to pair facilitate.
[^trainings]: Thank you [Martin](https://twitter.com/martinklose) and [Jim](https://twitter.com/jthurne) for involving me in this.