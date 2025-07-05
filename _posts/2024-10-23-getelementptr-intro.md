---
layout: post
title: "LLVM IR GetElementPtr指令简介"
tag: "note"
---

`getelementptr`是LLVM  IR里的一个用于计算指针偏移量的指令，非常常见但是又非常容易引起误解。
内容参考——[GetElementPtr](https://llvm.org/docs/GetElementPtr.html)，[LLVM IR tutorial](https://llvm.org/devmtg/2019-04/slides/Tutorial-Bridgers-LLVM_IR_tutorial.pdf)

<!--more-->

**getelementptr指令只是计算偏移量，不对指针做解引用。**

语法：

```
<result> = getelementptr <ty>, ptr <ptrval>{, <ty> <idx>}*
<result> = getelementptr inbounds <ty>, ptr <ptrval>{, <ty> <idx>}*
<result> = getelementptr nusw <ty>, ptr <ptrval>{, <ty> <idx>}*
<result> = getelementptr nuw <ty>, ptr <ptrval>{, <ty> <idx>}*
<result> = getelementptr inrange(S,E) <ty>, ptr <ptrval>{, <ty> <idx>}*<result> = getelementptr <ty>, <N x ptr> <ptrval>, <vector index type> <idx>
```

太复杂，先只考虑最简单的情况

`<result> = getelementptr <ty>, ptr <ptrval>{, <ty> <idx>}*`
第一个参数`<ty>` 代表要用什么样的类型解读第二个参数`<ptrval>`，这个类型为聚合类型，即结构体、数组或者向量。

第二个参数`<ptrval>`是一个`value`，是一个指针，作为指针偏移量计算的起始点。
  `{, <ty> <idx>}*`是正则表达式的记号，表示`<ty> <idx>`这个结构可出现0次或多次，其中`<ty>`为整形，`idx`为具体的索引值。

考虑以下样例：
```c
struct complicate{
  int x;
  long y;
  struct insideComplicate{
    int x;
    long y;
  } z;
  char a[10];
};

char accessA(struct complicate* c){
  c->z.y = 3;
  c->a[3] = 'c';
  return c->a[5];
}
```
采用`clang file.c -O2 -o file -c -emit-llvm`编译（`-O2`用以生产更简洁的IR），得到以下IR：

```
%struct.complicate = type { i32, i64, %struct.insideComplicate, [10 x i8] }

%struct.insideComplicate = type { i32, i64 }

define dso_local signext i8 @accessA(ptr nocapture noundef %0) local_unnamed_addr #2 {
  %2 = getelementptr inbounds %struct.complicate, ptr %0, i64 0, i32 2, i32 1
  store i64 3, ptr %2, align 8, !tbaa !13
  %3 = getelementptr inbounds %struct.complicate, ptr %0, i64 0, i32 3, i64 3
  store i8 99, ptr %3, align 1, !tbaa !17
  %4 = getelementptr inbounds %struct.complicate, ptr %0, i64 0, i32 3, i64 5
  %5 = load i8, ptr %4, align 1, !tbaa !17
  ret i8 %5
}
```
`c->z.y` 被解析成

```
getelementptr inbounds %struct.complicate, ptr %0, i64 0, i32 2, i32 1
```

其中`%0`为`c`, 第一组`i64 0`可看作由`%struct.complicate`组成的数组里取第一个元素；第二组`i32 2`表示在`%struct.complicate`这个结构体类型里取第三个元素，**注意——这里的第三个元素仅仅就是第三个元素，与每个元素的字节数无关**。

在`%struct.complicate`中，第一个元素为`i32`，第二个元素为`i64`，第三个元素为`%struct.insideComplicate`，因此第二组`i32 2`取到`%struct.insideComplicate`；第三组`i32 1`对类型`%struct.insideComplicate`取第二个元素`i64`，于是完成`c->z.y`的取地址工作。

剩下两条`getelementptr`指令与源码的对应关系留给读者。