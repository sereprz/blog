---
title: "Javascript is Weird Part I: Comparing Arrays"
date: 2020-04-04T23:18:19-04:00
draft: false
---

Javascript is weird.

I worked in Python for the last 5 years, and in R for the 3 years before, mostly for data science applications, and I didn't realise how much I was taking for granted until I started playing around with Javascript and spending a lot of time just wondering **why!**

I was pairing on [Conway's Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life) earlier this week and ran into something weird. In our implementation, the game stops when `currentGameState === nextGameState`, where `currentGameState` and `nextGameState` represent respectively (surprise!) the current state of the game's grid and the following one. Except the game didn't stop. Ever.

It turns out, in Javascript `currentGameState === nextGameState` returns `false` even if the two arrays look exactly the same. This was counterintuitive to me but I followed my pairing partner's lead and we wrote a helper function that takes two arrays, `a` and `b`, as input and performs a few tests to determine if they are indeed the same. Here's the function:

```javascript
function arraysEqual(a, b) {
  if (a === b) return true;
  if (a == null || b == null) return false;
  if (a.length != b.length) return false;
  for (let i = 0; i < a.length; ++i) {
    if (typeof a[i] === "object"){
      if (arraysEqual(a[i], b[i]) == false){
        return false
      }
    } else if (a[i] !== b[i]){
      return false
    };
  }
  return true;
}
```

Let's unpack this. 

Step **1**: if `a` and `b` are of the same **type** and hold the same **value** return `true`.

Step **2**. if either `a` or `b` is `null` return `false`.

Step **3**. If `a` and `b` have different lengths return `false`.

Step **4**. Loop through all elements of `a` and compare each of them to the corresponding element in `b`, thus making sure that each element has exactly the same value and the same position in both arrays.

This worked, but I was still very confused as to **why** `a === b` would return `false` even when `a` and `b` were identical arrays, so I checked [MDN Web Docs](https://developer.mozilla.org/en-US/) and found [this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators):

> The identity operator returns true if the operands are strictly equal (see above) with no type conversion.

More confusion followed,  until I found [this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness):

> Note that the distinction between these all have to do with their handling of primitives; none of them compares whether the parameters are conceptually similar in structure. For any non-primitive objects x and y which have the same structure but are distinct objects themselves, all of the above forms will evaluate to false.

Basically, primitive types like *strings* and *numbers* are compared by their value, while objects like *arrays*, *dates*, and *plain objects* are compared by their reference. In other words, our two game states are *arrays* that point to **different** locations in memory, so the comparison `currentGameState === nextGameState` is never `true`.

I still haven't figured out **why** but at least I understand **what** it's doing and it was fun to investigate!

I come across stuff like this in Javascript **all** the time so I'm sure there'll be more posts like this to come.
