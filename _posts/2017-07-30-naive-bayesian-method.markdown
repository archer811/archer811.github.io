---
layout: post
title:      "朴素贝叶斯法"
subtitle:   "naive bayesian method"
date:       2017-07-30 12:00:00
author:     "Zhouxj"
header-img: "img/post-bg-nativebys.jpg"
catalog: true
comments: true
tags:
    - 统计学习方法
---

> 《统计学习方法》 第四章
### 朴素贝叶斯法概念
对于给定的训练数据集，首先基于特征条件独立假设学习输入/输出的联合概率分布；然后基于此模型，对给定的输入x，利用贝叶斯定理求出后验概率最大的输出y。<br>
基于贝叶斯定理和特征条件独立假设的分类方法<br>
首先介绍下贝叶斯定理<br>
<img src="//archer811.github.io/img/bayes.png"  width="680" alt="贝叶斯公式"/>
特征条件独立假设<br>
<img src="//archer811.github.io/img/bayes-2.png"  width="680" alt="贝叶斯公式"/>
计算的时候用到了极大似然估计<br>
<img src="//archer811.github.io/img/bayes-3.png"  width="680" alt="贝叶斯公式"/>
和贝叶斯估计<br>
<img src="//archer811.github.io/img/bayes-4.png"  width="680" alt="贝叶斯公式"/>
