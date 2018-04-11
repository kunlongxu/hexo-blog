---
title: shuffle-打乱数组
date: 2018-03-31 20:49:43
tags: 小练习
---

给一个数组，打乱数组顺序，原数组不
<!--more-->
例如：
[11,22,33,44,55]=>[33,22,11,55,44]


```javascript

function shuffle(arr: number[]) {
  let len = arr.length;
  let tmpArr = [...arr];
  function getRandomInt(max, min) {
    return Math.floor(Math.random() * (max - min + 1)) + min;
  }
  let indexArr = [];
  while (indexArr.length < len) {
    let newIndex = getRandomInt(len - 1, 0);
    if (indexArr.some(i => i == newIndex)) continue;
    indexArr.push(newIndex);
  }
  let newArr = [];
  arr.forEach((i, idx) => newArr.push(arr[indexArr[idx]]))
  console.log(arr, newArr)
  return newArr;
}

```