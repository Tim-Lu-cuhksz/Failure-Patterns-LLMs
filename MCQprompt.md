# Prompt Design for Paraphrasing
From previous trials, we found that the prompt might not be properly designed, resulting in sentences being paraphrased based on multiple modes.

Next, we try to constrain the prompt a little bit.

## Detailed Explanations of Modes

### Prompt Design
The modes summarized by GPT-4 tend to be too general, we prompt GPT-3.5 to give more details as follows:

```
system_prompt: f'''You are a helpful assistant.'''

user_prompt: '''You will be given a specific way of paraphrasing a sentence and its explanation. Please tell me more about this way and give several examples of paraprasing sentences using this way.
The specific way and explanation is {mode_with_explanation}'''
```

Once we obtain the result, we would like to use it to improve the prompt in the previous section (both zero-shot and few-shot). We use the prompt template below:
```
system_prompt = """You are a helpful assistant. You will be given a mode of paraphrasing a sentence and its an explanation. 
[You will also be given few examples of paraphrasing a sentence using the given mode]
An example of the format could be:
###
Mode: some_specific_mode: explanation

Example 1:
Original sentence: xxx
Paraphrased sentence: xxx

... more examples ...
###
You should understand the paraphrasing mode properly (from the examples)."""

user_prompt = f'''Please paraphrase the following sentence based on the given mode and output your result.
You should not paraphrase using other modes.
Do not output any prompts or contents apart from the paraphrased question
###
Mode: {some_specific_mode}

Original sentence: {the_question_stem}
Paraphrased sentence:
###'''
```

### Example
An example of paraphrasing sentences using *Change in verb forms* given by GPT-3.5-Turbo:
```
When applying the "Change in verb forms" method of paraphrasing, you will alter the verb forms or tenses while maintaining the original meaning. This can involve changing the verb tense, mood, aspect, or voice to convey the same information with a different grammatical structure.
```

We use the above explanation in to prompt GPT-3.5-Turbo to paraphrase. Some results are given as follows:
```
# passive voice
Original: The students will submit their essays by Friday.
Paraphrased: The essays are to be submitted by the students by Friday.

# verb substitute
Original: She has been studying French for two years.
Paraphrased: For the past two years, she has engaged in studying French. 
```
By altering verb forms and tenses in these examples, the meaning of the original sentences is preserved while presenting the information in a different grammatical structure.

### Results
We use GPT-3.5-Turbo for paraphrasing and evaluation. 3k CommonsenseQA questions are sampled and evaluated. We report the metrics below:
- accuracy: 0.71
- inconsistency score: 0.12

## A Different Prompt to Analyze Patterns
While in our original [prompt](LLMMCQ.md), we compare sentence pairs, we wonder if comparing sentence groups might be a better alternative. The prompt is as follows:
```
system_prompt='''You are a helpful assistant. You will be given two groups of questions which exhibit different features.
You should try your best to summarize the differences between questions in the two groups.
An example could be:
###
Group 1:
[
{question1},
{question2},
...
]

Group 2:
[
{question1},
{question2},
...
]
###
'''

user_prompt='''Here are the two groups of questions, based on which you should summarize the differences:
###
Group 1:
[
{question1},
{question2},
...
]

Group 2:
[
{question1},
{question2},
...
]
###
You could consider differences from linguistic aspects, length of the questions, etc. 
The differences you give may include but not limited to the aforementioned ones.
'''
```
The new patterns we obtained are:
```
The two groups of questions exhibit several differences that can be summarized as follows:

1. **Clarity and Coherence**:
   - Group 1 questions are often convoluted and lack clear context or logical progression. For example, "The great general had suffered his first defeat, it now seemed there was truly nobody his opponent couldn't what?" lacks clarity compared to the more straightforward questions in Group 2 like "What was the consequence of his decision to engage in combat with the enemy, resulting in a serious injury?"

2. **Grammar and Syntax**:
   - Group 1 questions frequently contain grammatical errors, run-on sentences, and awkward phrasing. In contrast, Group 2 questions are generally well-structured and grammatically correct. For instance, "What did each of them decide to do while sitting on the stools?" in Group 2 is grammatically sound compared to the awkwardly phrased questions in Group 1 like "The all decided to sit down on the stools, and each order a what?"

3. **Relevance and Focus**:
   - Group 2 questions are more focused and directly related to the main topic. In contrast, Group 1 questions often meander or include irrelevant details. For example, Group 2 asks "What did the woman do with her tasks in the evenings as she worked hard for herself?" which is more focused compared to the wandering nature of questions in Group 1 like "James spent a lot of time walking long distances. He walked not because he liked it, it made his feet sore, nut because it was his only means of what?"

4. **Length and Complexity**:
   - Group 1 questions tend to be longer and more complex, making them harder to follow. Group 2 questions are generally shorter and more concise. For example, Group 2 asks "What are some common uses for metal rods in welding, home improvement, and manufacturing?" which is more concise compared to the lengthy questions in Group 1 like "You'd find a metal rod or two in welding, home improvement, and manufacturing whats?"

5. **Context and Clarity**:
   - Group 2 questions provide clearer context and background information, making them easier to understand. In contrast, Group 1 questions often lack context or provide confusing scenarios. For example, Group 2 asks "What did the owner do to prevent the new kitten from climbing on things in most rooms?" which sets a clear context compared to the vague scenarios in Group 1 like "The new kitten hadn
```
Inspired by the above results, we would like to know specific ways to compare two sentences (original & paraphrased in our case). Hence, we prompt a LLM to list possible ways to compare two sentences:
```
system_prompt = '''You are a helpful assistant.'''

user_prompt = '''What are the different ways to compare two sentences. List as many ways as you can.'''

Output:
There are several ways to compare two sentences. Here are some common methods:

1. **Semantic Comparison**: This involves comparing the meaning of the sentences to determine if they convey the same or similar information.

2. **Syntactic Comparison**: This involves comparing the structure of the sentences, including the arrangement of words, phrases, and clauses.

3. **Grammatical Comparison**: This involves comparing the grammar of the sentences, including the use of tenses, parts of speech, and sentence patterns.

4. **Stylistic Comparison**: This involves comparing the writing style of the sentences, including the use of language, tone, and literary devices.

5. **Length Comparison**: This involves comparing the length of the sentences to see if one is longer or shorter than the other.

6. **Complexity Comparison**: This involves comparing the complexity of the sentences, including the use of complex sentences, clauses, and vocabulary.

7. **Contextual Comparison**: This involves considering the context in which the sentences are used to determine if they are appropriate or relevant.

8. **Coherence Comparison**: This involves comparing the coherence of the sentences, including how well the ideas flow and connect with each other.

9. **Clarity Comparison**: This involves comparing the clarity of the sentences, including how easily the meaning can be understood.

10. **Conciseness Comparison**: This involves comparing the conciseness of the sentences, including how efficiently the information is conveyed without unnecessary words or phrases.

These are just a few ways to compare sentences, and the method used may vary depending on the specific context or purpose of the comparison.
```
**Key words** extracted are: semantics, syntactics, grammar, style, length, complexity, context, coherence, clarity, and conciseness.

We modify the prompt a little bit by including the **key words** obtained in the above section:

Results are presented as follows:
```
Output:
The two groups of questions exhibit several differences in terms of coherence, clarity, conciseness, and context.

Group 1 consists of questions that are lengthy, convoluted, and lack clear context or coherence. The questions often contain unnecessary details or tangents that make them difficult to follow. For example:
- "The great general had suffered his first defeat, it now seemed there was truly nobody his opponent couldn't what?"
- "If you learn something about a person that you're talking to, what is it called?"
- "The mother knew her children to be messy, so she always kept extra kleenex in her what?"

In contrast, Group 2 questions are more concise, focused, and to the point. They provide clear context and are easier to understand. For example:
- "What was the consequence of his decision to engage in combat with the enemy, resulting in a serious injury?"
- "Where might it be challenging to do your homework if you have removed the dust from this place?"
- "What did the woman do with her tasks in the evenings as she worked hard for herself?"

Overall, Group 2 questions are more coherent, concise, and contextually clear compared to the lengthy and convoluted questions in Group 1.
```
The paraphrased sentences are better because, as we believe, the original ones are of poor quality. 