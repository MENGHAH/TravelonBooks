原文链接：https://arxiv.org/abs/2303.18223

# 2. Overview

本章节主要总结了LLM出现的背景和GPT系列模型的演进历程

## 2.1 Background of LLM

LLM主要是指基于Transformer构建的参数量巨大的模型，这种模型的训练也需要巨量的文本数据。LLM发展的基础背景包含三个方面：scaling laws、emergent abilities 和key techniques。

scaling laws：大多数的大模型结构都是基于transformer的结构搭建，但是相对于transformer，LLM扩大了模型的体积、参数量。并且使用更多的数据和更大的计算机对模型进行训练。评价这种类型的模型扩张是否有效主要有两种评价指标：KM scaling law  和 Chinchilla scaling law  。其中KM scaling law 可以反应出模型表现与模型大小、训练数据数量和训练轮数的关系。

