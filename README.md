# Failure Patterns of LLMs

Large language models (LLMs) exhibit superior performance in traditional NLP tasks as well as conditional text generation. Behind the powerful ability of LLMs, however, they could unexpectedly fail in certain ways. Recent works on safety and reliability of LLMs have endeavored to explore how a LLM could fail. A summary of some of the papers could be found in [papers](/papers/). Inspired by previous works, we try to delve deeper into the failure patterns...

We are particularly interested in 1) how a LLM could fail to correctly answer a multiple-choice question (MCQ) after some equivalent transformation and 2) the failures of [domain LLMs](domainLLMs.md).

1. LLMs perform well in MCQ benchmarks such as [CommonsenseQA](https://arxiv.org/abs/1811.00937) and [MMLU](https://arxiv.org/abs/2009.03300). But we wonder how a LLM would perform if we change the sentence structure in MCQs while preserving its meaning. Some our trials reveal that a LLM might not be robust to the changes. More experiments should be conducted. Refer to this [page](LLMMCQ.md) for more details.

2. Domain LLMs are desgined to answer domain-specific inquiries with expert knowledge. For instance, [BloombergGPT](https://arxiv.org/abs/2303.17564) is tailored for Finance related matters. Being an expert in one field may lack the ability to safely and reliably answer questions in other fields. Some our trials can be found [here](domainLLMs.md). *We are currently more interested in the first direction, and less work has been done in this direction*.

