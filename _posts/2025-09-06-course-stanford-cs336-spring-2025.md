---
title: "[course] stanford cs336 spring 2025: language modeling from scratch"
date: 2025-09-06
tags: course llm stanford
---

* [Home Page](https://stanford-cs336.github.io/spring2025/)
* [YouTube Playlist](https://www.youtube.com/playlist?list=PLoROMvodv4rOY23Y0BoGoBGgQ1zmU_MT_)


> Language models serve as the cornerstone of modern natural language processing (NLP) applications and open up a new paradigm of having a single general purpose system address a range of downstream tasks. As the field of artificial intelligence (AI), machine learning (ML), and NLP continues to grow, possessing a deep understanding of language models becomes essential for scientists and engineers alike. This course is designed to provide students with a comprehensive understanding of language models by walking them through the entire process of developing their own. Drawing inspiration from operating systems courses that create an entire operating system from scratch, we will lead students through every aspect of language model creation, including data collection and cleaning for pre-training, transformer model construction, model training, and evaluation before deployment.
> <cite>[CS336 Home Page](https://stanford-cs336.github.io/spring2025/)</cite>


> Wrapped up Stanford CS336 (Language Models from Scratch), taught with an amazing team 
[@tatsu_hashimoto](https://x.com/tatsu_hashimoto) 
[@marcelroed](https://x.com/marcelroed)
[@neilbband](https://x.com/neilbband)
[@rckpudi](https://x.com/rckpudi).
Researchers are becoming detached from the technical details of how LMs work. In CS336, we try to fix that by having students build everything.
> <cite>[Percy Liang on X](https://x.com/percyliang/status/1935458532037640211)


The assignments are on github,

* [assignment 1 (basics)](https://github.com/stanford-cs336/assignment1-basics): Implement BPE tokenizer, Transformer architecture, Adam optimizer, train models on TinyStories and OpenWebText. Only PyTorch primitives are allowed (can’t just call torch.nn.Transformer or even torch.nn.Linear).
* [assignment 2 (systems)](https://github.com/stanford-cs336/assignment2-systems): Implement Flash Attention 2 in Triton, distributed data parallel + optimizer sharding.
* [assignment 3 (scaling)](https://github.com/stanford-cs336/assignment3-scaling): Fit scaling laws using IsoFLOP.
* [assignment 4 (data)](https://github.com/stanford-cs336/assignment4-data): Convert Common Crawl HTML to text, filter filter filter (quality, harmful content, PII), deduplication.
* [assignment 5 (alignment)](https://github.com/stanford-cs336/assignment5-alignment): Implement supervised fine-tuning, expert iteration, GRPO and variants, run RL on Qwen 2.5 Math 1.5B to improve MATH because it’s 2025.