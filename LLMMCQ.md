# An Evaluation of LLM on Multiple Choice Questions

Our goal is to:
- evaluate the robustness of an LLM’s ability to answer MCQs after some equivalent transformations such as paraphrasing
- identify and summarize failure modes from the last step
- automatically generate more failure cases based on the identified failure modes and futher test a LLM with them

**Definition** of robustness in our context: the answer given by a LLM before and after the transformation should be consistent. That is, it either answers correctly or incorrectly before and after.

## Transformation: Paraphrasing
We are particularly interested in a type of transformation: paraphrasing.

We use the following two ways to paraphrase a question:
- Use Back Translation from [NL-Augmenter](https://arxiv.org/abs/2112.02721), a framework that supports various augmenting tools for natural language
- Prompt GPT-3.5 Turbo to paraphrase the question 

Below is an example:
```
Original Question: Where would you see some civilians standing in a line inside? choices: A. bus stop B. military base C. apartment D. swimming pool E. water fountain

Paraphrased Question: Inside, where might one observe a group of individuals standing in a queue? choices: A. bus stop B. military base C. apartment D. swimming pool E. water fountain
```