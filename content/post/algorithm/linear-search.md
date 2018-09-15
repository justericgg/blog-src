---
title: "Linear Search (線性搜尋法)"
date: 2018-09-15T16:53:45+08:00
draft: false
---

## 最基本的查詢方法

想像一下有一群人在排隊，你現在的任務是要去找到名字叫 "魏瓔珞" 的人在這排的第幾個，你要怎麼做呢？

最簡單的方法就是從第一個人開始，一個一個問名字，這就是我們今天要講的 Linear Search 了

我們來畫個表格，看一下步驟

Step1:

| 1 | 2 | 3 | 4 | 5 |
| :---: | :---: | :---: | :---: | :---: |
| 愛新覺羅·弘曆 | 富察·傅恆 | 高寧馨 | 魏瓔珞 | 王小明 |
| X | | | |

Step2:

| 1 | 2 | 3 | 4 | 5 |
| :---: | :---: | :---: | :---: | :---: |
| 愛新覺羅·弘曆 | 富察·傅恆 | 高寧馨 | 魏瓔珞 | 王小明 |
| | X | | |

Step3:

| 1 | 2 | 3 | 4 | 5 |
| :---: | :---: | :---: | :---: | :---: |
| 愛新覺羅·弘曆 | 富察·傅恆 | 高寧馨 | 魏瓔珞 | 王小明 |
| | | X | |

Step4:

| 1 | 2 | 3 | 4 | 5 |
| :---: | :---: | :---: | :---: | :---: |
| 愛新覺羅·弘曆 | 富察·傅恆 | 高寧馨 | 魏瓔珞 | 王小明 |
| | | | O |

找到 "魏瓔珞" 了，在第四個位置

## 來點程式碼

```go

package main

import (
	"fmt"
)

func linearSearch(numbers []int, searchNum int) int {

	for index, num := range numbers {
		if num == searchNum {
			return index
		}
	}

	return -1
}

func main() {

	numbers := []int{1, 3, 2, 10, 13, 29, 5}

	fmt.Println(linearSearch(numbers, 13)) // Print 4
	fmt.Println(linearSearch(numbers, 30)) // Print -1
}
```

```javascript

// 回傳 array 的 index，若沒找到的話，回傳 -1
function linearSearch(numbers, searchNum) {

    for (i = 0; i < numbers.length; i++) {
        if (numbers[i] === searchNum) return i;
    }

    return -1;
}

let numbers = [1, 3, 2, 10, 13, 29, 5];
let resultFound = linearSearch(numbers, 13);
let resultNotFound = linearSearch(numbers, 30);

console.log(resultFound); // Print 4
console.log(resultNotFound); // Print -1
```

## 時間複雜度

線性搜尋法是一種非常簡單也好理解的搜尋演算法，反正不管怎樣，我就是一個一個找，直到找到為止，
因此也造成一個很好理解的瓶頸 - 要找的東西越多，就會找的越久，如果要找的東西在第一個位置的話，
那恭喜你運氣超好，但我們得用最差的情況來看待它

## O(n)

拿上面的程式碼例子來說，我們的陣列是 [1, 3, 2, 10, 13, 29, 5]，這裡有7個數字在裡面，若要找到
5 這個數字的位置，搜尋7次可以找到我們要的東西，現在陣列變成8個數字 [1, 3, 2, 10, 13, 29, 7, 5]，
我們得找8次，得出一個結論是 - 最差的情況，陣列裡數字的數量與搜尋的次數成正比，所以這是一個
O(n) 時間複雜度的演算法

## 有沒有更好的方法呢？

有的，下次我們來講講 Binary Search (二元搜尋法)










