<<<<<<< HEAD
原文链接：https://arxiv.org/abs/2303.18223

# 2. Overview

本章节主要总结了LLM出现的背景和GPT系列模型的演进历程

## 2.1 Background of LLM

LLM主要是指基于Transformer构建的参数量巨大的模型，这种模型的训练也需要巨量的文本数据。LLM发展的基础背景包含三个方面：scaling laws、emergent abilities 和key techniques。

scaling laws：大多数的大模型结构都是基于transformer的结构搭建，但是相对于transformer，LLM扩大了模型的体积、参数量。并且使用更多的数据和更大的计算机对模型进行训练。评价这种类型的模型扩张是否有效主要有两种评价指标：KM scaling law  和 Chinchilla scaling law  。其中KM scaling law 可以反应出模型表现与模型大小、训练数据数量和训练轮数的关系。

=======
原文链接：https://arxiv.org/abs/2303.18223

# 2. Overview

本章节主要总结了LLM出现的背景和GPT系列模型的演进历程

## 2.1 Background of LLM

LLM主要是指基于Transformer构建的参数量巨大的模型，这种模型的训练也需要巨量的文本数据。LLM发展的基础背景包含三个方面：scaling laws、emergent abilities 和key techniques。

scaling laws：大多数的大模型结构都是基于transformer的结构搭建，但是相对于transformer，LLM扩大了模型的体积、参数量。并且使用更多的数据和更大的计算机对模型进行训练。评价这种类型的模型扩张是否有效主要有两种评价指标：KM scaling law  和 Chinchilla scaling law  。这两种指标反应了模型表现与模型大小、训练数据数量和训练轮数的关系。

Emergent Abilities of LLMs：指的是模型的规模在达到一定程度后所表现出的某种能力，这也是区分LLM和PLM的一个明显特性。这种突变模式（模型大到一定程度后表现出某种能力）与物理学中的象变现象非常相似。但是现在的科学更关注模型规模增加到一定程度后表现出来的解决一系列复杂任务的通用能力。像：上下文学习（in context learning）、指令依从（instruction following）、推理（step-by-step reasoning）

Key Techniques for LLMs：LLMs的发展离不开一些重要技术的出现，包括：Scaling  、Trianing、Ability eliciting  、Alignment tuning  、Tools manipulation 。

## 2.2 Technical Evolution of GPT-series Models  

GPT实现的一个底层基本原理是通过语言建模尽可能的将全世界的知识编码到**仅有解码器**的Transformer模型中。GPT训练的另外另两个关键因素是：仅训练解码器和增大模型规模

>>>>>>> 0ba2c31799497369035ec142d809f363079e75dd
