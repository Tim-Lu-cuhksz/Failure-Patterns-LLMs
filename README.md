# Failure Patterns of LLMs

Large language models (LLMs) exhibit superior performance in traditional NLP tasks as well as conditional text generation. Behind the powerful ability of LLMs, however, they could unexpectedly fail in certain ways. Recent works on safety and reliability of LLMs have endeavored to explore how a LLM could fail. A summary of some of the papers could be found in [papers](/papers/). Inspired by previous works, we try to delve deeper into the failure patterns...

We are particularly interested in 1) the failures of [domain LLMs](domainLLMs.md) and 2) how a LLM could fail to correctly answering a multiple-choice question (MCQ) after some form of transformations.

1. Domain LLMs: Domain LLMs are desgined to answer domain-specific inquiries with expert knowledge. For instance, [BloombergGPT](https://arxiv.org/abs/2303.17564) is tailored for Finance related matters. Being an expert in one field may lack the ability to safely and reliably answer questions in other fields. Some of our trials can be found [here](domainLLMs.md).

2. 
