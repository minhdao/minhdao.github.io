---
layout: post
title: "Selection sort"
date: 2018-05-29
categories:
  - algorithm
description:
image: https://picsum.photos/2000/1200?image=456
image-sm: https://picsum.photos/500/300?image=456
---
### The Concepts

Selection sort is a technique used to sort a sequence of items/numbers/etc.

This technique is rather simple. Sorting an array of random numbers is one good way to demonstrate selection sort.

Given an array of random numbers as below:

`[4, 2, 12, 5, -1, 43, 0, 64, 65, 1, -3]`

For each iteration, the smallest element is chosen and move to the beginning of the array.

Linear search algorithm can be used to find the smallest element of the array.

In the first iteration, -3 is found to be the smallest elements. Therefore, -3 will be swapped to the first position of the array (index 0).

The array now looks like this:
`[-3, 2, 12, 5, -1, 43, 0, 64, 65, 1, 4]`

For the next iteration, we only need to start at index 1 of the array and look forward to find the next smallest element.

When the next smallest element is found, it will be swapped for index 1 position of the array.

-1 is the next smallest element of the array, so will be swapped to index 1 and the array will look like this:

`[-3, -1, 12, 5, 2, 43, 0, 64, 65, 1, 4]`

We will repeat the same process for all other elements of the array.

### The code
```javascript
/**
 * [selectionSort selection sort algorithm so sort elements of an array]
 * @param  {[array]} array [array numbers to sort]
 * @return {[array]}       [sorted array]
 */
let selectionSort = (array) => {
  let min;
  for (let i = 0; i < array.length; i++) {
    min = linearSearch(i, array);
    if (min < array[i]) {
      array = swap(min, array[i], array);
    }
  }
  return array
};
```
The code will loop through the array starting at index 0. Variable 'i' is used to keep track of where linear search should begins in the array.

```javascript
for (let i = 0; i < array.length; i++)
```
Each iteration will find the smallest element of the array, then we will compare the smallest element with the element at index i.

```javascript
min = linearSearch(i, array);
  if (min < array[i]) {
    array = swap(min, array[i], array);
  }
```
We will swap 'array[i]' for 'min' then repeat the same process with the next index of the array.

Linear search code can be found below:

```javascript
/**
 * [linearSearch linear search to find smallest value inside an array]
 * @param  {[number]} index [starting index]
 * @param  {[array]} array [array to search]
 * @return {[number]}       [smallest value of the array]
 */
let linearSearch = (index, array) => {
  let min = array[index];
  for (let i = index; i < array.length; i++) {
    if (array[i] < min) {
      min = array[i];
    }
  }
  return min;
};
```
Swapping logics can be found below:

```javascript
/**
 * [swap swap values of 2 elements of an array]
 * @param  {[number]} a     [array element value]
 * @param  {[number]} b     [array element value]
 * @param  {[array]} array [array for swapping]
 * @return {[array]}       [swapped array]
 */
let swap = (a, b, array) => {
  let tempA = array[array.indexOf(a)];
  let tempIndexA = array.indexOf(a);
  let tempB = array[array.indexOf(b)];
  let tempIndexB = array.indexOf(b);
  array[tempIndexA] = tempB;
  array[tempIndexB] = tempA;
  return array;
};
```

### The Big Oh :O
Selection sort seems to have the complexity of O(n^2) because 2 loops are needed to sort the array.

That's it for now. Until next time :)
