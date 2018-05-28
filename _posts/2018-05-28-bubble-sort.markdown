---
layout: post
title: "Bubble sort"
date: 2018-05-28
categories:
  - algorithm
description:
image: https://picsum.photos/2000/1200?image=678
image-sm: https://picsum.photos/500/300?image=678
---
### The Concepts

Bubble sort is a sorting technique used on a sequence of items/numbers/etc.

Imagine a glass of champagne with bubbles rising from the bottom to the top of the glass :)

Imagine you are the bubble God. Your job is to determine which bubble should rise to the surface before the other.

The divine rule is lighter elements should rise before the heavier ones.

A divine scale is your tool complete these tasks.

Sorting an array with random numbers representing bubbles with different weights is one good way to demonstrate bubble sort.

Given an array of numbers:
`[4, 2, 12, 5, -1, 43, 0, 64, 65, 1, -3]`

Imagine a scale starting from the end of the array weighting the last two elements 1 and -3.

If the element on the right side of the scale is 'lighter'/less than the element on the left, the 'lighter' element will be moved to the right of the scale.

Therefore, the array will now looks like this:
`[4, 2, 12, 5, -1, 43, 0, 64, 65, -3, 1]`

The scale now move further left of the array to 'weight' 65 and -3.

Similarly, since -3 is 'lighter' than 65, -3 is again move to the right.

Therefore, the array will now looks like this:
`[4, 2, 12, 5, -1, 43, 0, 64, -3, 65, 1]`

The 'weighting' process will continue until the scale reached the far left of the array (index 0).

After the first iteration through the array, the 'lightest' element will be at index 0 of the array.

The weighting process will continue through the steps:
`[4, 2, 12, 5, -1, 43, 0, 64, -3, 65, 1]`

`[4, 2, 12, 5, -1, 43, 0, -3, 64, 65, 1]`

`[4, 2, 12, 5, -1, 43, -3, 0, 64, 65, 1]`

`[4, 2, 12, 5, -1, -3, 43, 0, 64, 65, 1]`

`[4, 2, 12, 5, -3, -1, 43, 0, 64, 65, 1]`

`[4, 2, 12, -3, 5, -1, 43, 0, 64, 65, 1]`

`[4, 2, -3, 12, 5, -1, 43, 0, 64, 65, 1]`

`[4, -3, 2, 12, 5, -1, 43, 0, 64, 65, 1]`

Finally, the lightest element is at the beginning (index 0) of the array.
`[-3, 4, 2, 12, 5, -1, 43, 0, 64, 65, 1]`

The scale will now move the end of the array, and repeat the process until next 'lightest' element is put next the 'lightest' element found previously.

### The Code
```javascript
/**
 * [bubleSort do buble sort in an array]
 * @param  {[array]} array [unsorted array]
 * @return {[array]}       [sorted array]
 */
let bubleSort = (array) => {
  for (let i = 0; i < array.length; i++) {
    let rightIndex = array.length - 1;
    let leftIndex = rightIndex - 1;
    for (let x = i; x < array.length; x++) {
      if (array[rightIndex] < array[leftIndex]) {
        swap(array[rightIndex], array[leftIndex], array);
      }
      if (rightIndex !== 0) {
        rightIndex--;
        leftIndex--;
      }
    }
  }
  return array;
};
```

It is important to stop at the correct index (index 0) for the code to work, so:
```javascript
if (rightIndex !== 0) {
  rightIndex--;
  leftIndex--;
}
```

Another thing to notice is for the inner loop, you don't need to go further than index i because beyond that is already the 'lighter' element for sure.
```javascript
for (let x = i; x < array.length; x++)
```

Swap function is defined as below:
```javascript
/**
 * [swap swap left and right values]
 * @param  {[number]} right [right value]
 * @param  {[number]} left  [left value]
 * @param  {[array]} array [array which contains right and left values]
 * @return {[type]}       [no return]
 */
let swap = (right, left, array) => {
  let tempRight = array[array.indexOf(right)];
  let tempIndexRight = array.indexOf(right);
  let tempLeft = array[array.indexOf(left)];
  let tempIndexLeft = array.indexOf(left);
  array[tempIndexRight] = tempLeft;
  array[tempIndexLeft] = tempRight;
};
```

### The Big Oh :O
Bubble sort seems to be O(n^2) in complexity since there are two for loops involved to sort the elements.

That's it for now. Until next time :)
