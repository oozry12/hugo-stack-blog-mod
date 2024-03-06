---
title: 'Hugo 内建 highlight 配置踩坑'
date: 2024-01-08 22:21:25 +0800
slug: hugo-highlight
tags: ['hugo','Note']
---

## 溯源
> Hugo uses [Chroma](https://gohugo.io/content-management/syntax-highlighting/) as its code highlighter; it is built in Go and is really, really fast.

既然内建了，秉持极简主义的原则，毕竟够用就好。那就Chroma吧

## 清理原来的
原来`config.toml` 自带有这段代码，也起着高亮的作用。必须先删掉。


```diff
paginate = 6
- pygmentsCodeFences = true 
- pygmentsCodefencesGuessSyntax = true 
- pygmentsUseClasses = true 
```
## 新配置

再加入

```toml {linenos=table,hl_lines=[12,14],linenostart=1}
[markup]
  [markup.highlight]
    anchorLineNos = false
    codeFences = true
    guessSyntax = false
    hl_Lines = ''
    hl_inline = false
    lineAnchors = ''
    lineNoStart = 1
    lineNos = false
    lineNumbersInTable = true
    noClasses = true
    noHl = false
    style = 'monokai'
    tabWidth = 4
```


最终呈现
```diff
baseURL = "https://ooim.xyz"
languageCode = "zh-cn"
theme = "jingzhe"
themesDir = "themes"
title = "或跃在渊"
paginate = 6

enableRobotsTXT = true
paginate = 6
- pygmentsCodeFences = true 
- pygmentsCodefencesGuessSyntax = true 
- pygmentsUseClasses = true 


[markup]
+  [markup.highlight]
+    anchorLineNos = false
+    codeFences = true
+    guessSyntax = false
+    hl_Lines = ''
+    hl_inline = false
+    lineAnchors = ''
+    lineNoStart = 1
+    lineNos = false
+    lineNumbersInTable = true
+    noClasses = true
+    noHl = false
+    style = "gruvbox"
+    tabWidth = 4
```



更多的[`style`](https://xyproto.github.io/splash/docs/all.html)

## Reference
- [官方1](https://gohugo.io/functions/transform/highlight/)
- [官方2](https://gohugo.io/getting-started/configuration-markup/#highlight)