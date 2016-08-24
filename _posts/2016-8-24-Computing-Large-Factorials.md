---
layout: post
title: Computing Large Factorials
category: Algorithms and Data Structures
tags: [algorithm, math, complexity]
---

I came across a relatively simple problem the other day regarding calculating extremely large
factorials. Numbers $> 20!$ cannot be stored in a $64-bit$ integer so it is clear we must improvise
in our technique for storing massive numbers.

I know I've had to store numbers in vectors before, which is nice becauase most algorithms dealing
with a vector representation of a number have time complexities in some way related to $O(\log(n))$
since we often have to traverse the array of digits in a number. We can store a number in a `vector<int>`
in C++, where `v[0]` represents the least significant digit in a number, and `v[v.size()-1]` represents
the most significant digit.