---
title: leetcode之路
tags: leetcode
categories: 學習 
---

從easy題開始，陸續更新。
https://leetcode.com/k1319900103/

<!-- more --> 

## 判斷數字是否對稱 
ex. 121 true 
ex. 15451 true
```javascript=
//解法1
var isPalindrome = function(x) {
    let strArr = ('' + x).split('');
    let isPali = true;
    let length = strArr.length;
    
    for(let i=0;i<length/2;i++){
        if(strArr[i] != strArr[(length - i - 1)] && isPali){
            isPali = false;
            break;
        }
    }
    
    return isPali;
};

//解法2
var isPalindrome2 = function(x) {
    if (x < 0) return false;
	
    // reverse the string representation of x
    const reverse = `${x}`.split('').reverse().join('');
    // compare the value regardless of types
    return x == reverse;
};

```
解法1是直覺想到把數字轉成字串陣列後，再一個迴圈去判斷前半段的字元是否等同於對稱位置的字元。若在第i個發現不等價時，就break出迴圈。

解法2則是來自討論區，更直覺地比對。就是把數字轉成陣列後反轉，便可以與原本的數字做判斷。


## 把羅馬數字轉換成阿拉伯數字
ex. VIII 8
ex. MDCCCLXXXIV 
```javascript=
var romanToInt = function (s) {
  symbols = {
    I: 1,
    V: 5,
    X: 10,
    L: 50,
    C: 100,
    D: 500,
    M: 1000,
  };
  value = 0;
  for (let i = 0; i < s.length; i += 1) {
    symbols[s[i]] < symbols[s[i + 1]]
      ? (value -= symbols[s[i]])
      : (value += symbols[s[i]]);
    console.log("value", value);
  }
  console.log(value);
  return value;
};

```
解法是來自討論區。把羅馬數字轉成自串陣列後，當前羅馬數字符號和下一個羅馬數字符號做值的大小比較，當前的值若大於下一個，表示下一個可累加，反之當前的值若小於下一個，表示當前符號與下一個符號有組合值，所以必須先減去，後會由下一個的值累加回來。

## 取出多個字串的共同前墜字元
ex ["flower", "flow", "flight"] fl
```javascript=

var longestCommonPrefix = function (strs) {
  let prefix = "";
  let shortOne = strs.reduce((aStr, bStr) => {
    return aStr.length < bStr.length ? aStr : bStr;
  });

  shortOne.split("").forEach((sItem) => {
    let target = prefix + sItem;
    let isAllHave = strs.every(
      (item) => item.includes(target) && item.indexOf(target) == 0
    );

    if (isAllHave) {
      prefix = target;
    }
  });

  return prefix;
};

```
我先找出字串陣列中最短的字串來做為組合基準，當每個字串都從起始位置都有包含target字串時，共同的字元則累加入prefix。


## 判斷字串的範圍符號是否對稱
ex. '()\[]{}' true
```javascript=
const isValid = (s) => {
  const stack = [];
  for (const c of s) {
    if (c === "{" || c === "[" || c === "("){
        stack.push(c);
    }else {
      const p = stack.pop();
      console.log(c,p)
      if (
        (c === "}" && p !== "{") ||
        (c === "]" && p !== "[") ||
        (c === ")") & (p !== "(")
      )
        return false;
    }
  }
  return stack.length === 0;
};
```
解法是來自討論區。當遇到前字元 { \[ \( 就都先存入stack陣列，當遇到後字元 } \] \) 就比對stack陣列的最後一個是否是它對稱的前字元，而最後字串陣列的迴圈跑完，如果stack陣列有餘數表示沒有成對。

---
