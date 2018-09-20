---
title: "排序: Bubble Sort (氣泡排序法)"
date: 2018-09-20T19:18:19+08:00
draft: false
---

## Bubble Sort 是什麼
Bubble Sort (氣泡排序法) 是一種很基本的排序演算法，雖然可能它在實際運用上不是那麼實用及有效率，但是學會一些比較基本的排序演算法可以幫助我們日後繼續深入理解其他更有效率的排序法

假如我們有一個數組如下

[8, 1, 5, 2, 4]

我們需要將他由小至大排序成 => [1, 2, 4, 5, 8]

以此為例，該排序法的做法是由左開始一次比對下一個位置的數，若前一個數比下一個數大，則交換這兩個數的位置，一直比較到最後一位會產生出一個最大的數在最右邊的位置，接下來重複以上的動作，直到所有的數都確認排序

## 看圖片好理解
![binary search image](/images/bubble-sort.svg)

* 綠色的是比較的兩個位置
* 黃色的是需要交換位置
* 藍色的是已確認排序的元素

可以看到每個 pass 都會確認出一個排序確定的數字，依序遞減，直到比到都確認為止

## 時間複雜度

直接以上面的例子來說，有五個元素，每次選出一個最大的數到最右邊，要比5次，
每一次裡又要從左到右的一個一個位移比較，也是5次，所以走完全部次數一共是5的平方
(上面這樣說比較好理解，實際上是每次遍歷都會遞減次數，圖中上到下及左到右，但一樣是上到下的次數乘以左到右的次數) 故此算法的時間複雜度是 O(n^2)

## 空間複雜度

這裡沒有需要另外暫存些什麼資料，故為 O(1)

## 實際寫點程式吧

```go

package main

import (
	"fmt"
)

func main() {

	ary := []int{8, 1, 5, 2, 4}

	// 每一次會確定一個最大的排序到最右邊, 外層從最右邊遞減
	for i := len(ary) - 1; i > 0; i-- {

		// 每次比到上一個確定排序的位置
		for j := 0; j < i; j++ {

			if ary[j] > ary[j+1] {
				
				ary[j], ary[j+1] = ary[j+1], ary[j]
			}

		}

	}

	fmt.Println(ary) // [1 2 4 5 8]
}
```

```javascript

let ary = [8, 1, 5, 2, 4];

// 每一次會確定一個最大的排序到最右邊, 外層從最右邊遞減
for (let i = ary.length - 1; i > 0; i--) {
  
  // 每次比到上一個確定排序的位置
  for (let j = 0; j < i; j++) {

    if (ary[j] > ary[j+1]) {
      let temp = ary[j];
      ary[j] = ary[j+1];
      ary[j+1] = temp;
    }

  }  
}

console.log(ary); // [1, 2, 4, 5, 8]
```

## 優化氣泡排序法

因為是排序的第一篇 blog，在這邊小小提到一個簡單的觀念，沒有一定好的排序法，各種排序法會在不同的情境下會有不同的速度，從今天講的 Bubble Sort 來說，我們可以發現當這個需要排序的數組已經是幾乎排好序的情況下，是可以很快的就把這個數組排好序的

舉例來說，如果其中的一次遍歷裡沒有發生任何的交換位置，也就表示後面也不用再繼續遍歷了，因為已經照我們要的排好序了，最好的情況下，時間複雜度會是 O(n)，我們可以把我們的程式再修改一下

```go

package main

import (
	"fmt"
)

func main() {

	ary := []int{8, 1, 5, 2, 4}
	switched := true

	// 每一次會確定一個最大的排序到最右邊, 外層從最右邊遞減
	for i := len(ary) - 1; i > 0; i-- {

        //如果都沒有交換過，表示不需要再比下去了
		if !switched {
			break
        }
        
        switched = false;

		// 每次比到上一個確定排序的位置
		for j := 0; j < i; j++ {

			if ary[j] > ary[j+1] {
			
				switched = true

				ary[j], ary[j+1] = ary[j+1], ary[j]
			}

		}

	}

	fmt.Println(ary) // [1 2 4 5 8]
}
```

```javascript

let ary = [8, 1, 5, 2, 4];
let switched = true;

// 每一次會確定一個最大的排序到最右邊, 外層從最右邊遞減
for (let i = ary.length - 1; i > 0; i--) {
   
  //如果都沒有交換過，表示不需要再比下去了
  if (!switched) break; 
  switched = false;
  
  // 每次比到上一個確定排序的位置
  for (let j = 0; j < i; j++) {

    if (ary[j] > ary[j+1]) {
      
      switched = true;
      
      let temp = ary[j];
      ary[j] = ary[j+1];
      ary[j+1] = temp;
    }

  }  
}

console.log(ary); // [1, 2, 4, 5, 8]
```

## 後記

我的文筆真的不是很好，若有什麼疑問的話，歡迎大家 E-Mail 給我交流

#### 參考資料
https://en.wikipedia.org/wiki/Bubble_sort


