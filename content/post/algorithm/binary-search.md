---
title: "Binary Search (二元搜尋法)"
date: 2018-09-16T13:59:30+08:00
draft: false
---

在上一篇 - Linear Search (線性搜尋法)，文末提到還有更好的搜尋方法，我們今天就來講講
Binary Search (二元搜尋法)

## 必要條件

首先，要進行 Binary Search 的集合必需要是排序過的，至於各種排序的演算法，我們在後面的章節
會再繼續介紹幾個常見的排序方法，並對其效率、優缺點做分析

## 演算方法

不知道大家有沒有玩過一個遊戲，A 從 1~20 個數字裡心裡想一個數字，B 開始猜數字，A 要回答有沒有
猜中，若沒有，A 要回答答案是比 B 猜的數字小還是大，B 就可以往對的方向繼續猜，直到猜到為止

舉例來說 A 心裡想的數字是 2，B 開始猜數字

```

B: 是 10 嗎？
A: 比 10 小
B: 那是 5 嗎？
A: 比 5 小 ?
B: 是 3?
A: 比 3 小
B: 你罵人啊你...
A: 我沒有...
B: 是 1 嗎？
A: 比 1 大
B: 我知道了...
```

Binary Search 就是用這樣的概念去實作，有一個差別是在 A 回答問題後，B 可以從範圍中但選一個數字，
但我們在實作這個演算法時加上一個規則 - 每次都猜範圍內中間那個位字的數字

我們來看一下圖例，比較清楚

![binary search image](/images/binary-sort.svg)

## 來點程式實作

```go

package main

import (
	"fmt"
)

func binarySearch(numbers []int, searchNum int) int {

	// 定義範圍
	start := 0
	end := len(numbers) - 1

	//計算中間查詢位置
	mid := (start + end) / 2

    // start 大於 end 時，表示範圍都找過了，找不到東西了
	for numbers[mid] != searchNum && end > start {

		if searchNum > numbers[mid] {
			start = mid + 1 // mid 比過了不用包含在下一次搜尋，所以加一個位置
		} else {
			end = mid - 1 // mid 比過了不用包含在下一次搜尋，所以減一個位置
		}

		mid = (start + end) / 2

	}

	if numbers[mid] == searchNum {
		return mid
	} else {
		return -1 //找不到就回傳 -1
	}

}

func main() {

	numbers := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

    result1 := binarySearch(numbers, 8)
    result2 := binarySearch(numbers, 11)

    fmt.Println(result1) // 7
    fmt.Println(result2) // -1
}
```

```javascript

function binarySearch(numbers, searchNum) {
  
	// 定義範圍
    let start = 0;
    let end = numbers.length - 1;
  
    // 計算中間查詢位置
    let mid = Math.floor((start + end) / 2); // 無條件捨去
  
    // start 大於 end 時，表示範圍都找過了，找不到東西了
    while (searchNum !== numbers[mid] && end > start) {
        
        if (searchNum > numbers[mid]) {
            start = mid + 1; // mid 比過了不用包含在下一次搜尋，所以加一個位置
        } else {
            end = mid - 1; // mid 比過了不用包含在下一次搜尋，所以減一個位置
        }
      
        mid = Math.floor((start + end) / 2);
    }
  
    if (numbers[mid] === searchNum) {
        return mid;
    } else {
        return -1; // 找不到回傳 -1
    }
  
  
  
}

let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

let result1 = binarySearch(numbers, 8);
let result2 = binarySearch(numbers, 11);

console.log(result1); // 7
console.log(result2); // -1

```

## 時間複雜度 O(logN)

Binary Search 的時間複雜度是 O(logN)

應該不難發現這樣子搜尋比 Linear Search 還要少了很多次的搜尋，那為什麼說它是 O(logN) 呢？
我們來看一下搜尋的步驟:

我們知道 log2(8) = 3，首先當我們的陣列是 [1, 2, 3, 4, 5, 6, 7, 8, 9] (有8個元素)
想像最差的情況，我們來找一個不在這個陣列裡面的數字 - 10，搜尋的步驟如下

```

Step0:
[1, 2, 3, 4, 5, 6, 7, 8, 9]
             *         

Step1:
[6, 7, 8, 9]
    *

Step2:
[8, 9]
 *

Step3:
[9]
 *

找不到 10
```

log2(16) = 4

假設陣列現在加到16個元素 [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17]
我們找要 18

```

Step0:
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17]
                         *

Step1:
[10, 11, 12, 13, 14, 15, 16, 17]
              *

Step2:
[14, 15, 16, 17]
      *

Step3:
[16, 17]
  *

Step4:
[17]
  *

找不到 18
```

我們可以發現從數量從 8 到 16 搜尋的次數從 3個 step 到 4個 step，剛好成對數成長，所以時間複雜度是 O(logN)
，相比 Linear Search 來說，Binary Search 快上許多

ps: 因為第一次的查詢數字是否相等時，還沒有進入迴圈，所以上面例子用 step 0 表示







