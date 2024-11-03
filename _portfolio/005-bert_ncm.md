---
title: "Automatic Sentence Correction with BERT and NCM"
excerpt: "Group project for HS2914 How to Get Humans and Machines to Talk to Each Other.<br/><img src='/images/bert_ncm.png'>"
collection: portfolio
---

Link to the code: [GitHub repository](https://github.com/hanyang-hu/HS2914-auto-correction-project)

Although this is a group project, I will mainly discuss the part that has been done by myself in the post. My part is to implement a simplified version of [this paper](https://aclanthology.org/W19-4420.pdf) that combines large language models with the [noisy channel model](https://web.stanford.edu/~jurafsky/slp3/B.pdf) for automatic sentence correction. Essentially, the LLMs are used as a prior for candidate words, and the noisy channel model are defined to be a likelihood, then the sentence correction problem could be formulated as maximizing the posterior.

Specifically, I choose the [BERT model from Hugging Face](https://huggingface.co/docs/transformers/model_doc/bert) and define the log likelihood as follows:
$$
\begin{aligned}
\log P(\text{original\_word}\,|\,\text{candidate}) = 
\begin{cases} 
\alpha & \text{if } \text{candidate} = \text{original\_word} \\ 
-\gamma \cdot \log\left(d(\text{candidate}, \text{original\_word})\right) & \text{otherwise}
\end{cases}
\end{aligned}
$$
where $d(\cdot, \cdot)$ refers to the [Damerau-Levenshtein edit distance](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Intuitively, $\alpha$ controls the preference of the orignal word and $\gamma$ controls the weight of minimzing edit distance (compared to the prior distribution obtained from the LLM).

We could intuitively see the effect of these hyperparameters from the examples below: 

1. If ```alpha``` is too low, our approach might overcorrect the sentence.

For example, when ```alpha = 1``` and ```gamma = 8```, we have
> Input: Where should we meat tommorrow?
>
> Output: Where should be met tomorrow?

Modified to ```alpha = 6```, we have 
> Input: Where should we meat tommorrow?
>
> Output: Where should we meet tomorrow?

In this example, the overcorrection of the word "we" is alleviated by setting a higher ```alpha```. 

However, when ```alpha``` is too high, this approach has a tendency to preserve the original word.

For example, when ```alpha = 7```, we have
> Input: The plane tickets is expensive.
>
> Output: The plane tickets is expensive.

Modified to ```alpha = 3```, we have
> Input: The plane tickets is expensive.
>
> Output: The plane ticket is expensive.

Therefore we need to find an appropriate ```alpha```.

2. The higher the ```gamma``` is, the more this approach prefers candidates with lower edit distance.

For example, when ```alpha = 5``` and ```gamma = 1```, we have
> Input: That is so god!
>
> Output: That is so cool!

Modified to ```gamma = 7```, we have
> Input: That is so god!
>
> Output: That is so good!

