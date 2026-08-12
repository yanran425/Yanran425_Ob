---
coding: UTF-8
title: Ollama
date: 2026-07-18T03:36:19
updated: 2026-08-12T20:20:30
tags: []
share: true
---

# Ollama

## 2.多进程/多线程

- 多进程：多个独立的进程同时执行，每个进程有自己的地址空间，互不干扰。

- 多线程：在单个进程内同时执行多个线程，线程是进程的子集，多个线程共享进程的资源。

![](../assets/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F.png)

## 2.1说明

- 开源的大模型管理平台，提供`client`和`webui`两种管理方式。
- 充分利用本地资源，既可以用CPU也可以使用GPU

## 2.2指令

```
ollama list
ollama run modelName [提示词] --verbose
ollama pull modelName
ollama show modelName

```
