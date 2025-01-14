---
title: "[tif] semhash for semantic deduplication"
date: 2025-01-14
tags: tif deduplication minish-lab
---

I often find myself in a position where I have to deduplicate.
And most of the time, it's a lot of data that needs to be deduplicated.
So I keep my eye out for libraries that might help with this task.
Today I saw one from [MinishLab](https://github.com/MinishLab)
called [semhash](https://github.com/MinishLab/semhash).

> SemHash is a lightweight and flexible tool for deduplicating datasets using semantic similarity. It combines fast embedding generation from [Model2Vec](https://github.com/MinishLab/model2vec) with efficient ANN-based similarity search through [Vicinity](https://github.com/MinishLab/vicinity).

> SemHash supports both single-dataset deduplication (e.g., cleaning up a train set) and multi-dataset deduplication (e.g., ensuring no overlap between a test set and a train set). It works with simple datasets, such as text lists, and more complex ones, like multi-column QA datasets. Additionally, it includes functions to inspect deduplication results, making it easier to understand and refine your data cleaning process.

The two links in the quote above point to other projects from [MinishLab](https://github.com/MinishLab).

> We're a two-person (@pringled and @stephantul) open-source company, with a focus on Natural Language Processing.
We believe that if you make models fast enough, you unlock new possibilities.

Looks like the team is
* [Stéphan Tulkens](https://github.com/stephantul)
* [Thomas van Dongen](https://github.com/Pringled)

I love the library names, the retro gaming art in the github repo, and the focus on unlocking new use cases by just making things really fast. Some of the things they suggest you can do with their software:

> * Ingest the entire English Wikipedia in 5 minutes
* Classify tens of thousands of documents per second on CPU
* Approximately deduplicate extremely large datasets in minutes
* Build the fastest RAG application in the world
* Easily evaluate which ANN algorithm works best for your data

Looking forward to giving some of these a try!

---

source [semhash](https://github.com/MinishLab/semhash)
via [philipp schmid](https://www.linkedin.com/posts/philipp-schmid-a6a2bb196_data-leakage-and-deduplication-are-critical-activity-7284509620935557121-ZFq4)
