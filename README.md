# A5: Optimization Human Preference & LLM-as-a-Judge

## Overview

This assignment focuses on aligning a language model with human preference data and evaluating whether the alignment improves the quality of responses. A pre-trained model was fine-tuned using **Direct Preference Optimization (DPO)** so that it prefers more factual responses instead of hallucinated ones.

After training, the base model and the fine-tuned model were compared using prompts from the **AlpacaEval dataset**, and another LLM was used as a judge to decide which response was better.

---

## Dataset Preparation

The dataset **jondurbin/truthy-dpo-v0.11** from Hugging Face was used for training.

Each sample contains:

* **prompt** – the instruction given to the model
* **chosen** – the preferred factual answer
* **rejected** – an incorrect or hallucinated answer

The dataset was loaded using the Hugging Face `datasets` library and prepared so it could be used with the DPO trainer.

---

## Model Training

A pre-trained model (**distilgpt2**) was fine-tuned using **DPOTrainer** from the Hugging Face TRL library.

The training process works by comparing two responses for the same prompt (chosen vs rejected) and adjusting the model so that the preferred response becomes more likely.

Training ran for **254 steps (1 epoch)**.
The training loss gradually decreased during training, starting around **0.65** in the early steps and reaching a final average training loss of **0.0.152412**.

Training statistics:

* Training steps: **254**
* Final training loss: **0.0.152412**

This indicates that the model was able to learn the preference patterns from the dataset.

---

## Model Upload

After training, the model was saved and uploaded to Hugging Face.

Model link:
[https://huggingface.co/prabidhipyakurel/truthy-dpo-distilgpt2](https://huggingface.co/prabidhipyakurel/truthy-dpo-distilgpt2)

---

## Evaluation with AlpacaEval

To evaluate whether the DPO training improved the model, prompts from the **tatsu-lab/alpaca_eval** dataset were used.

The dataset was filtered to the **helpful_base** subset and **15 random prompts** were selected.

For each prompt:

* **Model A** – response from the base model
* **Model B** – response from the DPO trained model

Both responses were then evaluated by an external LLM acting as a judge.

---

## LLM-as-a-Judge

The judge model was given:

* the user instruction
* the response from Model A
* the response from Model B

The judge was instructed to return only one of the following:

* **Model A**
* **Model B**
* **Tie**

This made it easy to automatically record and count the results.

---

## Results

Evaluation results for 15 samples:

| Sample | Winner  |
| ------ | ------- |
| 1      | Model A |
| 2      | Tie     |
| 3      | Model B |
| 4      | Model A |
| 5      | Model A |
| 6      | Model A |
| 7      | Model A |
| 8      | Tie     |
| 9      | Model A |
| 10     | Model B |
| 11     | Tie     |
| 12     | Tie     |
| 13     | Model A |
| 14     | Tie     |
| 15     | Model A |

Summary:

* **Model B Wins:** 2
* **Ties:** 5
* **Total Evaluations:** 15

Win Rate formula:

Win Rate = (Model B Wins + 0.5 × Ties) / Total Evaluations × 100

Calculated win rate:

**Win Rate = 30%**

---

## Discussion

The training process shows that the model successfully learned from the preference dataset, as the training loss decreased from around **0.65 to 0.15**. This indicates that the model was able to distinguish between preferred and rejected responses during training.

However, the evaluation results show that the DPO model only achieved a **30% win rate** against the base model. The base model still won many comparisons, and several cases resulted in ties.

This may be due to the relatively small training dataset and limited training steps. Since the model was only trained for one epoch, the improvement may not be strong enough to consistently outperform the base model. Some prompts in the AlpacaEval dataset may also require knowledge or reasoning patterns that were not strongly represented in the training data.

Even though the improvement was limited, the experiment still demonstrates the process of aligning a model using human preference data and evaluating it with an automated LLM judge.

---

## Conclusion

This assignment demonstrated how **Direct Preference Optimization (DPO)** can be used to align a language model with preferred responses. The trained model was evaluated using prompts from the AlpacaEval dataset and judged by another LLM.

Although the DPO model did not consistently outperform the base model, the workflow shows how preference-based training and automated evaluation can be used together to study model alignment and response quality.
