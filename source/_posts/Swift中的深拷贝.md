---
title: Swift中深拷贝的使用
date: 2017-09-15 16:31:02
categories:
- 开发
tags:
- iOS
---

``` Swift
protocol Copyable {
 func copy() -> Copyable
}

//报价详情Formula
class QuoteFormula: HandyJSON,Copyable {
    var name: String = ""
    var price: String = "0"
    required init() {}

    func copy() -> Copyable {
        let formula = QuoteFormula()
        formula.name = name
        formula.price = price
        return formula
    }
}
```

``` Swift
let formulas = [QuoteFormula]()
var tempFormula = [QuoteFormula]()
for i in 0..<formulas.count {
    let formula = formulas[i] as! QuoteFormula
		tempFormula.append(formula)
}
```
