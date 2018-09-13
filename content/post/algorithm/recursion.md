---
title: "Recursion 遞迴 (Golang)"
date: 2018-09-13T09:50:15+08:00
draft: false
---

今天來講講 Recursion 遞迴，以下範圍只會提到在電腦科學裡的遞迴，讓我們看下去

## 什麼是 Recursion

先來看看 Wiki 怎麼說

> Recursion in computer science is a method of solving a problem where the solution depends on solutions to smaller instances of the same problem (as opposed to iteration). The approach can be applied to many types of problems, and recursion is one of the central ideas of computer science.

好的...所以這在講什麼鬼，我們來用比較生活上的例子來說

有一天小明去湖邊玩耍，不小心手滑了一下，手機掉到湖裡，突然間風雲變色，湖中跑出一個像中年大叔的精靈，
手上拿著好幾支 iPhone，與小明展開了以下的對話

```
湖中精靈: 請問這是你的 iPhone 7 嗎？
小明: 不是耶
湖中精靈: 這是你的 iPhone 8 嗎？
小明: 也不是耶
湖中精靈: 那這是你的 iPhone X 嗎？
小明: 不是唷
湖中精靈: 難不成你的手機是這支 iPhone XS 嗎？
小明: 不是...
湖中精靈: 我沒手機可以問了...
小明: 那你滾吧...
```

話說，這是哪門子的生活上的例子...

所以我們可以從 Wiki 看到，把一個大問題(這是你的手機嗎)，以重複將問題分解成較小的子問題(很笨的一隻一隻手機問)來得到答案的一種解決方法，就叫 Recursion(遞迴)

我們再來看一句 Wiki 上面說的

> Most computer programming languages support recursion by allowing a function to call itself from within its own code. Some functional programming languages do not define any looping constructs but rely solely on recursion to repeatedly call code.

我們在寫程式常常會用一個 function 去呼叫 function 自己本身的方式來達到遞迴的效果，這聽起來有點像 loop
的概念，在某些 functional program 裡甚至沒有 loop 的語法，因為你可以用遞迴來達到一樣的效果

所以小明在這裡扮演的是一個 function (回答手機是不是他的)，湖中精靈就是呼叫這個 function 的 caller (不斷的一直問類似的問題)，
直到沒問題可以問，所以小明就叫精靈不要再問了 (結束條件)

## Recursion 二個重要的元素

我們實作遞迴時，需要滿足以下兩個重要的元素

* 結束的條件
* 變動的輸入

### 結束的條件

你需要為這個遞迴設定一個結束的條件，不然就會一直無限循環下去

比如家務有拖地、澆花、洗碗這三個問題要問，問完了就沒問題要問了

### 變動的輸入

跟結束的條件有點關係，因為不給定變動的輸入就永遠不會跳到可以結束點，一樣會一直無限循環

如果你問完拖地了嗎，又問拖地了嗎，這就非常的鬼打牆

### 你還是會想一直循環

當然寫程式時某些情況下我們會希望一直循環，比如定期檢查一個狀態，但你還是得了解以上兩點，
這樣你才能很清楚的知道你寫的程式會發生什麼樣的行為

## 一個簡單的例子

```go
package main

import (
	"fmt"
)

func recursivePrintNum(num int) {

	if num == 0 {
		fmt.Println("I'm done")
		return
	}

	fmt.Println(num)
	num--
	recursivePrintNum(num)

}

func main() {
	recursivePrintNum(10)
}
```

我們定義了一個 function 叫 recursivePrintNum 傳入一個整數，並依序印出這些整數，直到數字不是 0，
可以看到這個遞迴有滿足上一節提到的 "結束的條件" 與 "變動的輸入"

* 結束的條件: 定義當 num 為 0 的時候跳出 function
* 變動的輸入: 每次印完 num 就將 num 減 1，並呼叫自己

## 一個實際的應用

看完了簡單的例子後，你心中一定會有個疑問，所以我們可以用遞迴做些什麼事

### 用遞迴遍歷樹節點

來看一下流程

1. 建立遍歷樹節點 function 可傳入一個節點
2. 若傳入的節點還包含其他節點，loop 這些節點，並把各節點傳入給這個 function
3. 若傳入的節點沒有其他節點了，跳出這個 function

### 抓取 html 檔案內所有的 img source

我們知道 html 是一個樹節構，所以我們可以用遞迴來取得在節點中想要的資料，來看一下範例

```html
<body>
    <div>
        <img src="a.jpg" alt="">
        <p>
            <img src="b.jpg" alt="">
            <p>Hi</p>
            <p>
                <ul>
                    <li><img src="c.jpg" alt=""></li>
                    <li>I'm li</li>
                </ul>
            </p>
        </p>
    </div>
</body>
```

```go
package main

import (
	"fmt"
	"io/ioutil"
	"log"
	"strings"

	"golang.org/x/net/html"
)

// 這裡會傳入一個 html 的一個節點
func getImageSrc(n *html.Node) {

	// 查看目前這個節點是不是 <img>
	if n.Type == html.ElementNode && n.Data == "img" {

		// 遍歷 <img> 裡的 attributes
		for _, a := range n.Attr {

			//找到 <img> 裡 src 這個 attributes
			if a.Key == "src" {
				fmt.Println(a.Val)
				break
			}
		}
	}

	// 這裡如果有子節點就會執行 getImageSrc 並把這個子節點傳入參數
	// 如果這個子節點還有兄弟節點，就會一個一個將兄弟節點也丟到 getImageSrc 裡
	// 直到沒有子節點為止 getImageSrc 將不再被呼叫
	for c := n.FirstChild; c != nil; c = c.NextSibling {
		getImageSrc(c)
	}
}

func main() {

	//這邊讀入我們的 html 檔案，實際上可能會是從網址抓取
	bs, err := ioutil.ReadFile("index.html")
	if err != nil {
		log.Fatalf("Read file error %v", err)
	}

	//將讀取回來的內容傳換成 html doc 結構
	doc, err := html.Parse(strings.NewReader(string(bs)))
	if err != nil {
		log.Fatalf("Html parsing error %v", err)
	}

	//開始呼叫我們的遞迴 function
	getImageSrc(doc)
}

```

HTML 為了簡單，這邊只從 body tag 開始給範例

執行程式碼後，可以看到我們走訪了整個 html doc，並且把所有 img tag 內的 src 輸出到 console

這裡有個延申閱讀的部分，以上的做法基本上是用深度優先的方式走訪，還有其他的方式就先留給給大家自行上網找答案了

#### 參考資料:

https://en.wikipedia.org/wiki/Recursion_(computer_science)


