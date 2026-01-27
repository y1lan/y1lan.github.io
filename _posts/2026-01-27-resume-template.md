---
layout: post
title: "用Typst做了一个简历模板"
tag: "typst"
---

最近疯狂投简历想找份寒假实习，可惜找不到，但是在一遍遍投简历面试的循环中中积累了一点用typst修改简历的经验。

现在放弃继续投了，索性整理一下学到的做成一个模板。


采用的是typst，基于[neet-cv](https://github.com/kznr02/neet-cv)修改的。

fork之后的地址在[y1lan-neet-cv](https://github.com/y1lan/neet-cv)。

<!--more-->

[neet-cv](https://github.com/kznr02/neet-cv) 原来的风格我就挺喜欢的了，但是各个函数定义和信息的传递方式用的是下标访问，
要一边看着源码一边写，于是我就索性把所有要用到的参数都给定义成命名参数要求显式传递，减少两头看的需求。

把原来一个个section定义的模式改成了手动添加heading以及多条entry的模式，还有对于各个entry的内容定义也换成了用`[]`的输入模式，方便调用者在内容里添加高亮
等语素。

举个例子：

```typst
= 项目经历

#project_entry(
  title: "东方永夜抄",
  tech: "6面boss",
  start: "2003年12月",
  end: "至今",
  link_: "https://thwiki.cc/%E4%B8%9C%E6%96%B9%E6%B0%B8%E5%A4%9C%E6%8A%84",
  content: [
    - 永琳救救我！
  ],
)
```

我觉得这样写起来更灵活？content里就按照一个普通的模块写就好了，灵活选择加什么料。


成品例子：

![resume](/assets/img/result.png)