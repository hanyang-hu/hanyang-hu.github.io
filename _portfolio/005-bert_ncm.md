---
title: "Automatic Sentence Correction with BERT and NCM"
excerpt: "Group project for HS2914 How to Get Humans and Machines to Talk to Each Other.<br/><img src='/images/bert_ncm.png'>"
collection: portfolio
---

Link to the code: [GitHub repository](https://github.com/hanyang-hu/HS2914-auto-correction-project)

Although this is a group project, I will mainly discuss the part that has been done by myself in the post. My part is to implement a simplified version of [this paper](https://aclanthology.org/W19-4420.pdf) that combines large language models with the [noisy channel model](https://web.stanford.edu/~jurafsky/slp3/B.pdf) for automatic sentence correction. Essentially, the LLMs are used as a prior for candidate words, and the noisy channel model are defined to be a likelihood, then the sentence correction problem could be formulated as maximizing the posterior.

Specifically, I choose the [BERT model from Hugging Face](https://huggingface.co/docs/transformers/model_doc/bert) and define the log likelihood as follows:
\\[
    \log P(\text{original\_word}\,|\,\text{candidate}) = 
\begin{align*}
\alpha & \quad \text{if } \text{candidate} = \text{original\_word} \\
-\gamma \cdot \log\left(d(\text{candidate}, \text{original\_word})\right) & \quad \text{otherwise}
\end{align*}
\\]
where $d(\cdot, \cdot)$ refers to the Damerau-Levenshtein edit distance. Intuitively, $\alpha$ controls the preference of the orignal word and $\gamma$ controls the weight of minimzing edit distance (compared to the prior distribution obtained from the LLM).