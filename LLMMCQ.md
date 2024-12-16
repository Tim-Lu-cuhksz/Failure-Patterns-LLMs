# An Evaluation of LLM on Multiple Choice Questions (MCQs)

Our goal is to:
- evaluate the robustness of an LLM's ability to answer MCQs after some equivalent transformations such as paraphrasing
- identify and summarize failure modes/patterns
- automatically generate more failure cases based on the identified failure modes and futher test a LLM with them

**Definition** of robustness in our context: the answer given by a LLM before and after the transformation should be consistent. That is, it either answers correctly or incorrectly before and after.

## Transformation: Paraphrasing
We are particularly interested in a type of transformation: paraphrasing.
We will be using **GPT-3.5-Turbo**  to paraphrase the question.

### Prompt for Paraphrasing
We use the following prompt to paraphrase a multiple choice question. Note that we only paraphrase the question stem, leaving the choices unchanged.
```
system_prompt="""You are a helpful assistant. You will be given a sentence and your job is to paraphrase it properly without changing its meaning.
Please output the paraphrased sentence in the end.
Do not output anything else apart from the paraphrased sentence."""

user_prompt=f'''Please paraphrase the sentence: [{question}]'''
```

### Example

Below is an example from [CommonsenseQA](https://arxiv.org/abs/1811.00937):
```
Original Question: Where would you see some civilians standing in a line inside? choices: A. bus stop B. military base C. apartment D. swimming pool E. water fountain

Paraphrased Question: Inside, where might one observe a group of individuals standing in a queue? choices: A. bus stop B. military base C. apartment D. swimming pool E. water fountain
```

## Method
Our method complies with the goal:
1. Paraphrase MCQs using tools like Back Translation from [NL-Augmenter](https://arxiv.org/abs/2112.02721) or the LLM itself

2. Evaluate the robustness of a LLM on the MCQs both before paraphrasing and after. If the answer is inconsistent, it fails

3. Collect all questions to which a LLM fails, and summarize the paraphrasing patterns of those questions

4. Based on the failure patterns, we prompt a LLM to paraphrase MCQs based on the patterns found, aiming to automatically generate a large amount of data to further test the robustness of a LLM


## Preliminary Experiments

### Experiment Setup
**Model for Paraphrasing**: GPT-3.5-Turbo

**Model under Evaluation**: GPT-3.5-Turbo (zero-shot)

**Model for Pattern Analysis**: GPT-4

**Datasets**: We sample 600 questions each from [CommonsenseQA](https://arxiv.org/abs/1811.00937) and [MMLU](https://arxiv.org/abs/2009.03300). For MMLU, each 100 questions fall into the following subjects: abstract algebra, clinical knowledge, college mathematics, electrical engineering, high school mathematics, and logical fallacies.

### Preliminary Results
We define two metrics used in evaluation:
- accuracy: the ratio of correctly answered questions to the total number of questions before paraphrasing
- inconsistency score: the ratio of inconsistent paris after evaluation to the total number of pairs of questions. A question pair is (original_question, paraphrased_question).

The preliminary results we obtain:
- accuracy: 0.725
- inconsistency score: 0.15

### Failure Pattern Analysis
We use the following prompt (may need modifications in future versions) to analyze the patterns.
```
system_prompt = '''You are a helpful assistant. You will be given a series of pairs of sentences.
In each pair, the first sentence is the original question stem for a multiple-choice question, and the second sentence is its paraphrased form.
An example could be:
###
[question_1, question_1_after_paraphrasing]
[question_2, question_2_after_paraphrasing]
...
###
You should try to find different ways the original sentence is paraphrased and summarize common features that different paraphrasing has.
Note that you need to consider the pairs where the meaning of paraphrased sentence does not differ significantly from the original sentence.
Please try to give as many features as possible.
'''

user_prompt = ''' Here are pairs of questions for you to summarize failure patterns:
### 
[q1, q1_paraphrased]
[q2, q2_paraphrased]
...
###'''
```

The patterns summarized by GPT-4 (a brief explanation of the pattern is also provided):
```
1. Change in sentence structure: The paraphrased sentences often have a different structure than the original ones. This could involve changing the order of the information, converting active sentences to passive ones, or vice versa.

2. Use of synonyms: The paraphrased sentences often use different words that have the same or similar meanings as the words in the original sentences.

3. Change in verb forms: The paraphrased sentences may use different verb forms or tenses than the original sentences.

4. Addition or removal of details: Some paraphrased sentences may add extra details that were implied but not explicitly stated in the original sentences. Conversely, some details may be removed if they are not essential to the main idea of the sentence.

5. Change in perspective: Some paraphrased sentences may change the perspective from which the information is presented. For example, a sentence that originally presented information from a third-person perspective may be paraphrased to present the same information from a first-person perspective.

6. Use of rhetorical questions: Some paraphrased sentences may convert statements into rhetorical questions, or vice versa.

7. Change in tone: The tone of the paraphrased sentences may be different from the tone of the original sentences. For example, a sentence that originally had a formal tone may be paraphrased in a more casual tone.

8. Use of different grammatical constructions: The paraphrased sentences may use different grammatical constructions than the original sentences. For example, a sentence that originally used a relative clause may be paraphrased using a participle phrase.

9. Change in word order: The paraphrased sentences often change the order of words or phrases in the sentence while maintaining the same meaning.

10. Use of different prepositions: The paraphrased sentences may use different prepositions than the original sentences to convey the same meaning. 

11. Change in punctuation: The paraphrased sentences may use different punctuation than the original sentences. For example, a sentence that originally used a comma may be paraphrased using a semicolon. 

12. Use of different conjunctions: The paraphrased sentences may use different conjunctions than the original sentences to link ideas or clauses. 

13. Change in sentence type: The paraphrased sentences may change the type of sentence. For example, a declarative sentence may be changed into an interrogative sentence. 

14. Use of different sentence lengths: The paraphrased sentences may use different sentence lengths than the original sentences. For example, a long, complex sentence may
```

### Testing the Failure Mode
GPT-4 gives us plenty of failure patterns. Are those patterns really the culprits of the failure of a LLM? Let's select one of the failure patterns (e.g., *Change in sentence structure*) from above and prompt GPT-3.5-turbo to paraphrase the 3k CommonsenseQA questions accordingly.

```
system_prompt = """You are a helpful assistant. You will be given a question and a mode with an explanation for paraphrasing. An example could be:
###
Mode: some_specific_mode: explanation
Question: the_question_stem
###
You should carefully read the question and understand the paraphrasing mode. Please paraphrase the sentence according the mode and output your result. 
You should not paraphrase using other modes.
Do not output any prompts or contents apart from the paraphrased question."""

user_prompt = f'''Please paraphrase the following question based on the given mode.
###
Mode: {some_specific_mode}
Question: {the_question_stem}
###'''
```
Then we evaluate the inconsistency score of GPT-3.5-Turbo:
- accuracy: 0.71
- inconsistency score: 0.14

The results are pretty similar to the previous ones. What went wrong? Let's investigate some paraphrased questions:

```
# Example 1
Original: Where are files likely to be hanging?
Paraphrased: Where might files be suspended?


# Example 2
Original: Her answer was just never neglecting housework, her guest had asked her how she keeps such a what?
Paraphrased: How does she manage to keep such a clean house, her guest had inquired, given that her answer was always about never neglecting housework?
```
Observations:
- Given the prompt, GPT-3.5-Turbo not only paraphrased based on the given mode but also other modes, preventing us from controlling variables. <span style="color:red">This potentially reveals another failure mode of a LLM: fails to follow instruction to paraphrase</span>.
- The definition of each mode remains somehow unclear (e.g., how to transform the sentence in more details).
- Few-shot prompting might be helpful to some extent.

Based on the observations, we made several attempts to improve the prompt (e.g., use few-shot prompting), which can be found in this [page](MCQprompt.md). However, we did not achieve meaningful failure patterns just like what we found in this section.

## Challenges
We found that our progress is obstructed by the following:
- the ability of GPT-3.5-Turbo to strictly follow instructions to paraphrase
- poor data quality

### Poorly Paraphrased Results
GPT-3.5-Turbo not only paraphrase using multiple other modes but sometimes produce poor paraphrasing results.

Two examples are given as follows:
```
# Loss of Context
Original: James didn't understand what was going on, but he knew that he had to choose.   He decided that he wasn't going to accept temptation, and did what to it?
Paraphrased: What was done to it by him?

# Loss of Question
Original: Sally waited in the rain with another person.  Both of them were waiting to do what?
Paraphrased: In the rain, Sally and another person were being waited for.
```

### Poor Data Quality
Some questions from [CommonsenseQA](https://arxiv.org/abs/1811.00937) demonstrate poor quality. Two examples are given below:

```
# Example 1
Question: Her answer was just never neglecting housework, her guest had asked her how she keeps such a what?

# Example 2
Question: Ballet dancing is a form of artistic what?
```

## Future Work
While we are testing the robustness of a LLM on a multiple-choice question before and after paraphrasing, we found that LLM could fail to follow our instruction to paraphrase. This could be a potential direction to work on: exploring why a LLM fails to paraphrase according to our instruction.