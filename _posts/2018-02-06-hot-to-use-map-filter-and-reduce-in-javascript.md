---
title: How to use Map, Filter and Reduce in Javascript
layout: post
date: '2018-02-06 03:00:17'
tags: js
card-image: https://cdn-images-1.medium.com/max/785/0*Co9Hk-VtMLfM08KH.png
post-card-type: image
---

## MAP
Map like filter & foreach takes a callback and run it against every element on the array but whats makes it unique is it generate a new array based on your existing array.

Let’s understand map with an example

```
var sample = [1, 2, 3]
let mapped = sample.map(elem => elem * 10)

console.log(mapped);
//Output
[10,20,30]
```

Map ran through every element of the array, multiplied it to 10 and returned the element which will be going to store inside our resulting array.

## FILTER
Whenever you have to filter an array Javascript inbuilt method to filter your array is the right choice to use. Filter let you provide a callback for every element and returns a filtered array.

The main difference between forEach and filter is that forEach just loop over the array and executes the callback but filter executes the callback and **check its return value**.

```
var sample = [1, 2, 3]
var result = sample.filter(elem => elem !== 2)
/* output */
[1, 3]
```

## REDUCE
As the name already suggest reduce method of the array object is used to reduce the array to one single value.

For example if you have to add all the elements of an array you can do something like this.

```
var sample = [1, 2, 3]
var sum = sample.reduce((sum, elem) => sum + elem)

/* output */
console.log(sum)
6
```

reduce takes a callback ( like every function we talked about ). Inside this callback we get two arguments sum & elem. The sum is the last returned value of the reduce function. For example initially the sum value will be 0 then when the callback runs on the first element it will add the elem to the sum and return that value. On second iteration the sum value will be first elem + 0, on third iteration it will be 0 + first elem + second elem.

So that is how reduce works it reduces the array into one single value and returns it upon completion.