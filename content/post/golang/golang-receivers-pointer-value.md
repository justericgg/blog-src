---
title: "Go: method receivers - pointer 還是 value"
date: 2018-09-09T15:10:43+08:00
draft: true
---

初學 Go 的一些小疑問，查了許多資料，在這邊整理一下，分享給有需要的人

## 一致性 -> 讓所有人都有一致的理解

把一致性放在一開始說是因為開發畢竟是一個團隊的事，減少誤會一直是很重要的事

引用官方 FAQ 的一段話

> If some of the methods of the type must have pointer receivers, the rest should too, so the method set is consistent regardless of how the type is used.

簡單來說就是，如果有一個 method receiver 的 type (常見是一個 struct) 是指定為 pointer，那就應該都要一致的用 pointer，好難解釋，我們看例子

```go

    // 如果你有一個 method 是這樣子定義的
    func (s *MyStruct) pointerMethod() { }

    // 那其他跟這個 MyStruct 相關的 method 就一致的用 pointer receiver
    func (s *MyStruct) anotherPointerMethod() { }
    
```

這樣子在開發中才比較不會讓其他工程師誤會這組 methods 的行為

## 你應該考慮的幾點

在決定要使用 pointer receiver 還是 value receiver 時，應該要考慮到以下幾點

### 需要修改這個 receiver 嗎？

如果 method 是需要修改 receiver 的值，那你應該要使用 pointer，因為在 Go 裡所有的 function 都是會 copy 一份傳入的參數，看下面的例子

```go

    package main

    import "fmt"

    type person struct {
        name string
    }

    func (p person) rename1(newName string) {
        p.name = newName
    }

    func (p *person) rename2(newName string) {
        (*p).name = newName
    }

    func main() {
        p := person{"Eric"}

        p.rename1("James")
        fmt.Println(p.name) // p.name 還是 Eric

        p.rename2("9n")
        fmt.Println(p.name) // p.name 變成 9n 了
    }

```

### Receiver 會不會很大？

如上所言，如果一個傳入的 receiver 很大，copy 一份傳入的參數是很浪費效能的一件事

### Concurrency

因為是 pointer 所以你得注意 concurrency 的情況，所以若沒有以上考量與程式一致性的考量，用 value 相對來說副作用是比較小的

#### 參考資料:

https://golang.org/doc/faq#methods_on_values_or_pointers
https://golang.org/doc/faq#different_method_sets




