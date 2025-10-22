# Prompting Experiments from the Early Days

Needs [llama-cpp](https://github.com/ggml-org/llama.cpp) and [llama-cpp-python](https://github.com/abetlen/llama-cpp-python)

## How to reproduce the experiments

### Version 1

```
python CoT_Dec_PAL_tester_v1.py
```

### Version 2

```
python CoT_Dec_PAL_tester_v2.py
```

### Version 3

```
python CoT_Dec_PAL_tester_v3.py
```

## Results summary

[Please note the following is an LLM translation of part of a chapter written in Spanish]

[...]

For the test of prompting techniques, decoding strategies, and reasoning frameworks, for now on Mixtral-8x7b-instruct-Q5_0, it is decided, first, to compare the performance of 8-shot CoT with the declarative approach studied above in the state of the art (using SymPy as a solver of systems of equations) in versions (i) 8-shot and (ii) 3-shot with 5 additional principles or tips on the formulation of equations (to help the LLM adhere to the correct equation syntax), observing, after its test —see results in [Table 52]—, better accuracy, 59.6 at temperature 0.8 and 68.4 at temperature 0, in CoT than in any of the variants tested with the declarative method, notably contrary to the results of the paper (with code-davinci-002 as LLM) and which indicates that the degree of improvement (if any) offered by a prompting technique or reasoning framework seems dependent on the LLM in use, and its benefits not necessarily extrapolable to other models, which, to begin with, is not ideal, as it would entail the need to discover the most appropriate techniques for each new model that is published.

| No. | Method<br>(shots)<br>(method)<br>(description)                      | Temperature |  Seed | GSM8K Accuracy | Total time (seconds) in local test | Average time per response (seconds) in local test |
| --: | ------------------------------------------------------------------- | ----------: | ----: | -------------: | ---------------------------------: | ------------------------------------------------: |
|   1 | CoT<br>(8-shot)<br>(maj@1)                                          |         0.8 | 1,337 |           59.3 |                          10,360.65 |                                              7.85 |
|   2 | CoT<br>(8-shot)<br>(maj@1)                                          |           0 | 1,337 |       **68.4** |                          13,791.84 |                                             10.46 |
|   3 | Declarative<br>(8-shot from the paper)<br>(maj@1)                   |         0.8 | 1,337 |           46.0 |                          16,778.40 |                                             12.72 |
|   4 | Declarative<br>(8-shot from the paper)<br>(maj@1)                   |           0 | 1,337 |           60.4 |                          17,102.89 |                                             12.97 |
|   5 | Declarative<br>(3-shot)<br>(maj@1)<br>(5 principles from the paper) |         0.8 | 1,337 |           49.0 |                          13,614.17 |                                             10.32 |
|   6 | Declarative<br>(3-shot)<br>(maj@1)<br>(5 principles from the paper) |           0 | 1,337 |            Err |                                  - |                                                 - |

Table 52. Evaluation summary of Mixtral-8x7b-instruct-Q5_0, on the complete test split of the GSM8K dataset, showing accuracy and execution time obtained experimentally under 8-shot maj@1 CoT, 8-shot maj@1 Declarative and 3-shot + 5 principles maj@1 Declarative, under temperatures 0.8 and 0 (greedy decoding).

However, and after inspecting the results, a non-negligible percentage is noticed with either (i) equations in a format not understood by the parsing code (taken from the paper), or (ii) difficulty of the model to understand the instructions on how to formulate the equations, deciding therefore to explore 3 new variants with, incrementally, (i) more initial explanatory instructions, (ii) a code (for example allowing more descriptive variables, such as ‘hours_for_200_revolutions’ instead of others not so descriptive such as ‘h’, or accepting symbols next to the variables, such as ‘[[eq total_money = $20]]’ which are removed internally) that parses the equations adapted to be less demanding with the syntax used, and (iii) a mix of positive and negative few-shot examples, with the new results obtained presented in [Table 53].

| No. | Method<br>(shots)<br>(method)<br>(description)                                                       | Temperature |  Seed | GSM8K Accuracy | Total time (seconds) in local test | Average time per response (seconds) in local test |
| --: | ---------------------------------------------------------------------------------------------------- | ----------: | ----: | -------------: | ---------------------------------: | ------------------------------------------------: |
|   7 | Declarative<br>(4-shot)<br>(maj@1)<br>(9 adapted principles)                                         |         0.8 | 1,337 |           55.3 |                          13,955.58 |                                             10.58 |
|   8 | Declarative<br>(4-shot)<br>(maj@1)<br>(9 adapted principles)                                         |           0 | 1,337 |            N/A |                                N/A |                                               N/A |
|   9 | Declarative<br>(4-shot)<br>(maj@1)<br>(9 adapted principles, adapted code)                           |         0.8 | 1,337 |           56.8 |                          14,091.57 |                                             10.68 |
|  10 | Declarative<br>(4-shot)<br>(maj@1)<br>(9 adapted principles, adapted code)                           |           0 | 1,337 |       **64.3** |                          13,018.75 |                                              9.87 |
|  11 | Declarative<br>(8-shot with 4 positives<br>+ 4 negatives)<br>(maj@1)<br>(8 principles, adapted code) |         0.8 | 1,337 |           57.2 |                          18,245.60 |                                             13.83 |
|  12 | Declarative<br>(8-shot with 4 positives<br>+ 4 negatives)<br>(maj@1)<br>(8 principles, adapted code) |           0 | 1,337 |           63.5 |                          19,815.60 |                                             15.02 |

Table 53. Evaluation summary of Mixtral-8x7b-instruct-Q5_0, on the complete test split of the GSM8K dataset, showing accuracy and execution time obtained experimentally under 4-shot + 9 principles maj@1 Declarative, without and with adapted code, and 8-shot (4 positives and 4 negatives) + 8 principles maj@1 Declarative, with adapted code, and under temperatures 0.8 and 0 (greedy decoding).

From these, an increase (and a certain convergence toward) accuracy up to values close to those obtained by CoT is observed, as the principles, code, and few-shot examples are adapted to the abilities of Mixtral-8x7b-instruct-Q5_0 (as opposed to those presented in the paper, probably tuned to its evaluated model, code-davinci-002), although despite this, the conclusion is noticed and supported that, still, the benefit obtained with the declarative method seems model-dependent and, in no case, surpasses in these experiments the CoT performance on GSM8K, with a graphical summary of the results obtained visible in [Figure 56].

<img width="1063" height="639" alt="Figure 56" src="https://github.com/user-attachments/assets/730d9c6d-c875-4d0d-846f-183f26ae0968" />

Figure 56. Comparison of methods (i) Chain-of-Thought (8-shot in our experiments and 5-shot from the Mixtral of Experts paper, in this case on Mixtral 8x7B without quantization) and (ii) 5 Declarative variants, on the complete test split of the GSM8K dataset, with Mixtral-8x7b-instruct-Q5_0 as the model and at temperatures 0.8 and 0 (greedy decoding).

At this point, the question that naturally arises is ‘are CoT failures the same as failures by the declarative approach, or do they solve different problems?’, which is considered relevant given that, in the first case, we could be facing a limitation of the model regarding its capacity to solve certain problems (due to lack of understanding, or difficulty to model it, among others), but in the second, the model would be able to solve them if the appropriate tool is chosen, deciding for its analysis to compare the answers of both models, with the resulting heatmap presented in [Figure 57].

<p align="center">
    <img width="608" height="511" alt="Figure 57" src="https://github.com/user-attachments/assets/75ca14b4-44c1-4b4b-814f-16fbd1ae3627" />
</p>
Figure 57. Heatmap of hits and failures of Mixtral-8x7b-instruct-Q5_0 for methods 8-shot CoT and 8-shot (4 positives + 4 negatives) + 8 principles Declarative + adapted code, on the complete test split of the GSM8K dataset.

From the heatmap, and notably, an important percentage (254 + 227 = 481 or ~36.5% of the 1319 problems) is observed solved by one or the other of the techniques, but not both, which is considered promising regarding two lines of research, such as (i) the choice of one technique or the other according to the problem (prior to its resolution) or (ii) the choice of one or the other answer, after its resolution by both techniques.

However, after a new question —see ‘are CoT failures with a certain seed the same as CoT failures with a different seed?’—, which also arises naturally, to verify whether it is really the use of diverse techniques —and not several attempts at resolution— which potentially leads to the (correct) solution of a problem, there are discovered (after completing 8-shot CoT on seeds 966, 1337 and 7625) lower results but surprisingly similar to the combination of CoT and declarative methods (with 377 —or ~28.6%—, 391 —or ~29.6%—, and 364 —or ~27.6% of correct answers by one of, but not both, resolutions), with the breakdown of results visible in [Table 54], and their corresponding associated heatmaps in [Figure 58].

| No. |  Seed | Inference technique<br>(shots) | Temperature | GSM8K Accuracy | Total time (seconds) in local test | Average time per response (seconds) in local test |
| --: | ----: | ------------------------------ | ----------: | -------------: | ---------------------------------: | ------------------------------------------------: |
|  13 | 1,337 | CoT<br>(8-shot)                |         0.8 |           59.3 |                          10,360.65 |                                              7.85 |
|  14 | 7,625 | CoT<br>(8-shot)                |         0.8 |           59.9 |                          10,698.97 |                                              8.11 |
|  15 |   966 | CoT<br>(8-shot)                |         0.8 |           58.6 |                          11,767.26 |                                              8.92 |

Table 54. Evaluation summary of Mixtral-8x7b-instruct-Q5_0, on the complete test split of the GSM8K dataset, showing accuracy and execution time obtained experimentally under 8-shot CoT with seeds 966, 1,337 and 7,625, and at temperature 0.8.

<img width="1275" height="359" alt="Figure 58" src="https://github.com/user-attachments/assets/c8c5990b-b80c-4cd7-bb0d-5c2dcd4bed06" />

Figure 58. Heatmap of hits and failures of Mixtral-8x7b-instruct-Q5_0 for 8-shot CoT with seeds 966, 1337 and 7625, and on the complete test split of the GSM8K dataset.

Likewise, and exploring in detail the third major pillar of resolution (see, along with CoT and the declarative method), with the use of code (PAL) as a response method and numerical computation, it is observed, as can be seen in [Table 55], a notable increase in accuracy, of ~14.5% at temperature 0.8 and ~4.7% at temperature 0, although in exchange for a significant computation overhead (~45.5% and ~22.8%) with respect to CoT.

| No. | Inference technique<br>(shots) | Temperature |  Seed | GSM8K Accuracy | Total time (seconds) in local test | Average time per response (seconds) in local test |
| --: | ------------------------------ | ----------: | ----: | -------------: | ---------------------------------: | ------------------------------------------------: |
|  16 | PAL<br>(3-shot)                |         0.8 | 1,337 |           67.9 |                          15,056.83 |                                             11.42 |
|  17 | PAL<br>(3-shot)                |           0 | 1,337 |       **71.6** |                          16,930.36 |                                             12.84 |

Table 55. Evaluation summary of Mixtral-8x7b-instruct-Q5_0, on the complete test split of the GSM8K dataset, showing accuracy and execution time obtained experimentally with 3-shot PAL, and under temperatures 0.8 and 0 (greedy decoding).

In fact, implementing Self-Consistency (SC) over CoT and PAL, with (i) 5 CoT iterations, (ii) 5 PAL iterations, and (iii) 3 CoT iterations and 3 PAL iterations, with the final answer the mode —or sampling according to frequency, in case there is no unique mode—, and as can be seen in [Table 56], it is possible to reach an increase in accuracy of ~21.2% in CoT and ~15.2% in PAL, although in exchange for an overhead of ~373.6% and ~392.8%, respectively, in average response time.

Likewise, and interestingly, the combination CoT and PAL does not seem, in the experiment carried out, to be capable either of increasing the accuracy obtained or of reducing the execution time in comparison to PAL, noting in this case that, given that 3 CoT iterations and 3 PAL iterations are performed, its equivalent to 5 iterations must be computed, or ~55.56 seconds per GSM8K question, for a fair comparison with SC-CoT and SC-PAL.

| No. | Inference technique<br>(shots)<br>(Early Exiting)                                                                         | Temperature |  Seed | GSM8K Accuracy | Total time (seconds) in local test | Average time per response (seconds) in local test |
| --: | ------------------------------------------------------------------------------------------------------------------------- | ----------: | ----: | -------------: | ---------------------------------: | ------------------------------------------------: |
|  18 | CoT + Self-Consistency (with 5 iterations)<br>(8-shot)<br>(without Early Exiting)                                         |         0.8 | 1,337 |           71.9 |                          49,043.72 |                                             37.18 |
|  19 | PAL + Self-Consistency (with 5 iterations)<br>(3-shot)<br>(without Early Exiting)                                         |         0.8 | 1,337 |       **78.2** |                          74,232.28 |                                             56.28 |
|  20 | CoT + PAL + Self-Consistency (with 3 CoT iterations and 3 PAL iterations)<br>(8-shot + 3-shot)<br>(without Early Exiting) |         0.8 | 1,337 |           77.9 |                          87,934.47 |                                             66.67 |

Table 56. Evaluation summary of Mixtral-8x7b-instruct-Q5_0, on the complete test split of the GSM8K dataset, showing accuracy and execution time obtained experimentally under (i) 8-shot CoT and (ii) 3-shot PAL, both with Self-Consistency (with 5 iterations) and (iii) 8-shot CoT + 3-shot PAL with Self-Consistency (with 3 CoT iterations and 3 PAL iterations), all of them without Early Exiting, and with temperatures 0.8.

Lastly, and after promising results exploring a fourth inference technique (after the test of CoT, declarative method and PAL), such as Rephrase (in this case of own implementation but taking inspiration from EchoPrompt and Rephrase and Respond), the combination of CoT (for being computationally less costly compared to PAL) and Rephrase is decided in order to, together with the SC decoding strategy, achieve, as can be seen in [Table 57], an accuracy of up to 82.4% (or an increase of ~14.6%) at temperature 0.8, and 78.3% at temperature 0.1 because, due to the nature of SC, temperature 0 cannot be used, since in such case all the answers would be identical.

Likewise, it is noted how, even with the incorporation of Early Exiting in order to, in case at any time a majority vote is achieved (see for example, after reaching 3 common answers in 5-iter Self-Consistency) move, directly, to the next benchmark question, merely the increase in time required by the re-writing of the question (even with Early Exiting and compared to 5-iter CoT SC without Early Exiting) is greater than 50% (see, 73720.71s versus 49043.72s or ~50.3%), so, ultimately and although techniques such as PAL, Rewrite and especially their combination or use in multiple iterations with SC achieve improvement, significantly (see, from the original 68.4% with CoT to 82.4%, or more than a 20% increase in accuracy, and even without the test of Rewrite, PAL and SC) it is observed that these techniques are ultimately costly and probably more suitable to higher-performance equipment than to the local computer that executes the robot’s brain.

| No. | Inference technique<br>(shots)<br>(Early Exiting)                                                              | Temperature |  Seed | GSM8K Accuracy | Total time (seconds) in local test | Average time per response (seconds) in local test |
| --: | -------------------------------------------------------------------------------------------------------------- | ----------: | ----: | -------------: | ---------------------------------: | ------------------------------------------------: |
|  21 | CoT + Rephrase + 8-shot CoT + Self-Consistency (with 5 iterations)<br>(Early Exiting after 3 common responses) |         0.8 | 1,337 |       **82.4** |                          73,720.71 |                                             55.89 |
|  22 | CoT + Rephrase + 8-shot CoT + Self-Consistency (with 5 iterations)<br>(Early Exiting after 3 common responses) |         0.1 | 1,337 |           78.3 |                          78,604.23 |                                             59.59 |

Table 57. Evaluation summary of Mixtral-8x7b-instruct-Q5_0, on the complete test split of the GSM8K dataset, showing accuracy and execution time obtained experimentally with (i) 8-shot CoT + Rephrase + Self-Consistency (with 5 iterations) and Early Exiting (after 3 common responses) and (ii) 8-shot CoT + Rephrase + Self-Consistency (with 5 iterations) and Early Exiting (after 3 common responses), under temperatures 0.8 and 0.1.

At this point, coinciding with the release of Phi-3 Mini and after a good impression in initial tests in both accuracy and response time, its evaluation is decided under 0-shot CoT, temperatures 0.8 and 0, fp16 and q4 versions, and on two possible prompts —for not finding the one used in the paper— more in order to find an appropriate temperature and quantization, than to continue exploring the best possible combination of prompting, decoding and/or frameworks, with the results obtained present in [Table 58] and [Table 59].

```
Use reasoning to solve the following problem, writing 'The answer is {number}.' at the end. Don't write any other numerical value after 'The answer is {number}.'.\n
```

| No. | Inference technique<br>(shots) | Quantization | Temperature |  Seed | GSM8K Accuracy | Total time (seconds) in local test | Average time per response (seconds) in local test |
| --: | ------------------------------ | ------------ | ----------: | ----: | -------------: | ---------------------------------: | ------------------------------------------------: |
|   1 | CoT<br>(0-shot)                | fp16         |         0.8 | 1,337 |           79.2 |                           9,629.96 |                                              7.30 |
|   2 | CoT<br>(0-shot)                | fp16         |           0 | 1,337 |       **80.3** |                           7,506.68 |                                              5.69 |
|   3 | CoT<br>(0-shot)                | q4           |         0.8 | 1,337 |           72.7 |                           7,143.43 |                                              5.42 |
|   4 | CoT<br>(0-shot)                | q4           |           0 | 1,337 |           75.5 |                           4,837.59 |                                              3.67 |

Table 58. Evaluation summary of Phi-3 Mini fp16 and q4, on the complete test split of the GSM8K dataset, showing accuracy and execution time obtained experimentally with prompt #1 under 0-shot CoT and temperatures 0.8 and 0 (greedy decoding).

```
Solve the following problem, writing 'The answer is {number}.' on the last line without anything else:\n
```

| No. | Inference technique<br>(shots) | Quantization | Temperature |  Seed | GSM8K Accuracy | Total time (seconds) in local test | Average time per response (seconds) in local test |
| --: | ------------------------------ | ------------ | ----------: | ----: | -------------: | ---------------------------------: | ------------------------------------------------: |
|   1 | CoT<br>(0-shot)                | fp16         |         0.8 | 1,337 |           72.0 |                           7,481.85 |                                              5.67 |
|   2 | CoT<br>(0-shot)                | fp16         |           0 | 1,337 |       **75.7** |                           7,961.25 |                                              6.04 |
|   3 | CoT<br>(0-shot)                | q4           |         0.8 | 1,337 |           63.3 |                           5,531.33 |                                              4.19 |
|   4 | CoT<br>(0-shot)                | q4           |           0 | 1,337 |           64.4 |                           5,926.59 |                                              4.49 |

Table 59. Evaluation summary of Phi-3 Mini fp16 and q4, on the complete test split of the GSM8K dataset, showing accuracy and execution time obtained experimentally with prompt #2 under 0-shot CoT and temperatures 0.8 and 0 (greedy decoding).

From the tables, it is observed how, again, temperature 0 seems to report the best results, with the best evaluation obtained with the first of the prompts, fp16 and greedy decoding, with an accuracy of 80.3%, reasonably close to the 82.5% reported in the paper, and even higher than the 78.2% obtained with 3-shot PAL + 5-iter Self-Consistency with Mixtral-8x7b-instruct-Q5_0.

Thus, and given that Self-Consistency seems difficult to implement on the current hardware and for a real-time robot, and after the good performance and small size of Phi-3 Mini in comparison to Mixtral 8x7B, the selection of Phi-3 Mini (and in particular, in q8 version, a compromise between the speed of q4 and the precision of fp16) is decided as the LLM for the robot.

Moreover, given the variability of results obtained, it is concluded that:

- On the one hand, LLMs can be very sensitive to temperature, for example with 15% better GSM8K accuracy with CoT on Mixtral 8x7B at temperature 0 versus 0.8, but only 1.39% with Phi-3 Mini fp16 and 1.74% with Phi-3 Mini q4.

- On the other hand, prompting techniques and reasoning frameworks are also sensitive to the model, with the declarative method reporting better results versus CoT with code-davinci-002 but not being reproduced experimentally with Mixtral 8x7B in this thesis.

- LLMs can also be sensitive to the prompt, for example with variations from 6 to 17% in GSM8K accuracy with only small changes in it on Phi-3 Mini as the model.

- Lastly, the increase in time (for example, ~Nx with N average iterations in Self-Consistency or +50% with CoT and Rewrite versus CoT) is, for now, much greater than the improvement in accuracy provided (for example, 5 times the time for 5% better accuracy, in several SC experiments or 50% more time for 15% better accuracy with Rewrite over CoT and SC, both on Mixtral8x7B).

Thus, and taking into account the time constraints defined for the robot, it is concluded that CoT, PAL, Rewrite, and other techniques not tested in depth due to time constraints, such as Step-Back, Plan-and-Solve, SV, FOBAR, CSV and Least-to-Most, can be useful (although sometimes sensitive) for specific problems but, given their significant overhead, the best decision for the robot could be, as opposed to forcing one of these techniques at every sensor input iteration of the robot, to let it be the one that, by itself, decides which technique to use at each moment, with the hope that, leveraging its database in which to store memories and experiences, it will be able to store —and therefore, to learn—, over time, which prompting technique (in the sense of receiving its own internal thought in the next input iteration) and reasoning framework to apply, according to the type of problem.

In this way, the corresponding prompt fragment, with which the model is notified of the existence of said techniques and the recommendation to choose one of them, dependent on the problem, remains the following:

```
To generate your response to the world, the following mindsets have been recommended, not to be followed strictly —using all of them in the same response—, but to be used as a guide to think about how to approach a problem:

1. Chain-of-Thought: Explicit step-by-step reasoning
2. Program-Aided Language (PAL): Utilizing code for mathematical and logical tasks
3. Rewrite: Restating the problem to ensure comprehension
4. Step-Back: Considering broader principles before addressing specifics
5. Plan-and-Solve: Developing a strategy before implementation
6. Self-Verification: Reviewing answers before finalizing output
7. Forward-Backward Reasoning: Validating solutions against initial conditions
8. Code-based Self-Verification: Employing programmatic assertions for checking
9. Least-to-Most: Breaking complex problems into manageable sub-problems
```

And a few-shot example similarly in the system prompt, the following:

```
Example #2 (combining internal thoughts, first, body control, second, and code execution, third, in a hypothetical scenario to double-check a sum I earlier calculated and shared the result of):

Self-Verification:
I think my answer was correct, but let's double-check.

Plan-and-Solve:
1. Control my body to voice I will verify.
2. Execute the sum function to double-check the result.
3. Stop.

{CONTROL_BODY_START}
{
    "speak": "Wait, let me verify."
}
{CONTROL_BODY_END}

{CODE_EXEC_START}
def sum(a, b):
    return a + b
print(sum(153, 242))
{CODE_EXEC_END}

Now I'll stop and wait for the result.
```
