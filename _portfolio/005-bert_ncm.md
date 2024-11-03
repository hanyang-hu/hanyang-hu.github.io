---
title: "Automatic Sentence Correction with BERT and NCM"
excerpt: "Group project for HS2914 How to Get Humans and Machines to Talk to Each Other.<br/><img src='/images/bert_ncm.png'>"
collection: portfolio
---

Link to the code: [GitHub repository](https://github.com/hanyang-hu/HS2914-auto-correction-project)

Although this is a group project, I will mainly discuss the part that has been done by myself in this post: my part is to implement a simplified version of [this paper](https://aclanthology.org/W19-4420.pdf) that combines large language models with the [noisy channel model](https://web.stanford.edu/~jurafsky/slp3/B.pdf) for automatic sentence correction. This is a very simple project, but it is interesting to think of the limitations of this naive approach and appreciate the relevant research that has been conducted in this field to mitigate the issues we discussed. 

Essentially, the LLMs are used as a prior for candidate words, and the noisy channel model are defined to be a likelihood, then the sentence correction problem could be formulated as an optimization problem on the posterior by the Bayes rule. Specifically, I choose the [BERT model from Hugging Face](https://huggingface.co/docs/transformers/model_doc/bert). Here is an illustration of its architecture:
![BERT Architecture](./bert.png)<br/>
which is especially good at "understanding" the meaning of sentences: even if part of the sentence is wrong due to typos, we can mask them and make good predictions as long as the model captures the sentence overall. 

When it comes to the noisy channel model, the log likelihood is defined as follows:
$$
\begin{aligned}
\log P(x\,|\,c) = 
\begin{cases} 
\alpha & \text{if } c = x \\ 
-\gamma \cdot \log\left(d(c, x)\right) & \text{otherwise}
\end{cases}
\end{aligned}
$$
where $c$ refers to the candidate, $x$ refers to the original word, and $d(\cdot, \cdot)$ refers to the [Damerau-Levenshtein edit distance](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Intuitively, $\alpha$ controls the preference of the orignal word and $\gamma$ controls the weight of minimzing edit distance (compared to the prior distribution obtained from the LLM).

We could intuitively see the effect of these hyperparameters from the examples below: 

1. If $\alpha$ is too low, our approach might overcorrect the sentence.

    (a) For example, when $\alpha = 1$ and $\gamma = 8$, we have

        > Input: Where should we meat tommorrow?
        > Output: Where should be met tomorrow?

    Modified to $\alpha = 6$, we have 

        > Input: Where should we meat tommorrow?
        > Output: Where should we meet tomorrow?

    **Remark.** In this example, the overcorrection of the word "we" is alleviated by setting a higher $\alpha$. 

    (b) However, when $\alpha$ is too high, this approach has a tendency to preserve the original word.

    For example, when $\alpha = 7$, we have

        > Input: The plane tickets is expensive.
        > Output: The plane tickets is expensive.

    Modified to $\alpha = 3$, we have

        > Input: The plane tickets is expensive.
        > Output: The plane ticket is expensive.

    Therefore we need to find an appropriate $\alpha$.

2. The higher the $\gamma$ is, the more this approach prefers candidates with lower edit distance.

    For example, when $\alpha = 5$ and $\gamma = 1$, we have

        > Input: That is so god!
        > Output: That is so cool!

    Modified to $\gamma = 7$, we have

        > Input: That is so god!
        > Output: That is so good!

Of course, this very simple approach has many obvious limitations: 

1. Corrected output always has the same number of words as the input, so it cannot handle typos of missing or extra space.

        > Input: This isa dog.
        > Intended Output: This is a dog.
        > Actual Output: This is it.

2. Unable to correct out-of-vocabulary words, such as domain specific words or slang.

        > Input: Backpropagation is important for machine learning.
        > Intended Output: [same as the original sentence since it is correct]
        > Actual Output: Propagation is important for machine learning.

3. Our noisy channel model is primarily based on the edit distance, hence it lacks phonetic analysis, leading to errors with homophones.

        > Input：We are not aloud to do that.
        > Intended Output: We are not allowed to do that.
        > Actual Output: We are not about to do that.

4. Words are corrected from left to right, which may cause the model to commit to an early incorrection.

        > Input: The cats is small.
        > Intended Output: The cats are small.
        > Actual Output: The cast is small.

The paper we reference primarily applies beam search to address problem (4). For the other issues, we might consider: (a) using a transformer architecture, allowing the decoder to generate sentences without constraints on the length; and (b) implementing more informative likelihood models.