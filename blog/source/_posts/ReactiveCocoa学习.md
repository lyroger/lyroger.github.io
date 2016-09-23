---
date: 2016-07-06 09:39
status: public
title: ReactiveCocoa学习
category: 框架
---

## 一、signal的释放问题
当signal作为local变量时，如果没有被subscribe，那么方法执行完后，该变量会被dealloc。但如果signal有被subscribe，那么subscriber会持有该signal，直到signal sendCompleted或sendError时，才会解除持有关系，signal才会被dealloc。