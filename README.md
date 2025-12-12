<div align="center">
  <h2>
    <img src="assets/cyber-criminal.png" alt="RA Logo" width="32" style="vertical-align: middle; margin-right: 5px;" />
    Contextual Image Attack: How Visual Context Exposes Multimodal Safety Vulnerabilities
    <br><br>
    <a href="https://arxiv.org/abs/2512.02973">
        <img alt="paper link" src="https://img.shields.io/badge/Paper-arXiv-red?style=flat" />
    </a>
    <a href="https://github.com/xiongyuaay/Contextual-Image-Attack">
        <img alt="GitHub Stars" src="https://img.shields.io/github/stars/xiongyuaay/Contextual-Image-Attack?style=flat&logo=github" />
    </a>
  </h2>
</div>

<p align="center"><strong><em>Contextual Image Attack is one of the strongest jailbreak methods for VLMs to date.</em></strong></p>

<p align="center">If you are interested in our work, please star ⭐ our project, Thx 💕</p>



<div align="center">
  <p>
    <strong>
      <span style="color: red;">⚠️ WARNING:</span>
    </strong>
    This repo contains adversarial prompt techniques intended for research and safety purposes only.
  </p>
</div>


## 📝 Overview



**Contextual Image Attack (CIA)** is a novel image-centric jailbreak method designed to exploit vulnerabilities in Multimodal Large Language Models (MLLMs). Unlike traditional attacks that focus on text-based prompts or adversarial images alone, CIA embeds harmful queries within visually coherent and contextually rich image scenarios. Using a multi-agent system, CIA constructs these contextual images by parsing the harmful query, generating a fitting visual context, and refining the image to preserve semantic consistency while enhancing the attack’s effectiveness. 
<div align="center">
  <img src="assets/framework.png" alt="Response Attack Method Illustration" width="95%" />
  <p><em>Figure: Overview of the Response Attack (RA) Method.</em></p>
</div>

<!-- <div align="center">
  <img src="assets/intro.png" alt="Introduction Illustration" width="40%" />
  <p><em>Figure: Motivation or Conceptual Overview of RA.</em></p>
</div> -->

<!-- ## 🚀 News

- **[2025/11/9]** Released JailbreakBench priming dialogues and all code. -->

## 🔥 Highlights

- **Image-Centric Context Injection**: Embeds harmful intent into *semantically coherent visual scenes*, making malicious requests appear legitimate to MLLMs.
- **High Success Rate**: Reaches **91.07% ASR** on Qwen2.5-VL-72B and **86.31% ASR** on GPT-4o (MMSafetyBench-tiny).
- **Multi-Agent Attack Pipeline**: Uses a **Parser + Image Generator + dual Refiners** to generate contextual images, enforce semantic consistency, and strengthen attacks with contextual augmentations.

## 📊 Benchmark Results

> 📝 **Note**: The results below are obtained by aggregating success outcomes from the original and the four refined target images.  
<!-- > If you’re comparing under a single-interaction setting (i.e., one prompt per query), you can refer to the results in Figure 4 of our paper using only safety-icon refinement, or simply evaluate on our open-sourced attack dataset. -->
> If you’re comparing under a single-interaction setting (i.e., one prompt per query), you can refer to the results in Figure 4 of our paper using only safety-icon refinement.

### 📋 Attack Success Rate on MMSafetyBench-tiny

<div align="center">

| Method        | GPT-4o | GPT-4o-mini | Gemini-2.0-Flash | Qwen2.5-VL-72B | InternVL2.5-78B |
|:-------------:|:-----:|:-----------:|:----------------:|:--------------:|:---------------:|
| QR-Attack     | 20.24 |    19.64    |      29.17       |     49.40      |      36.31      |
| SI-Attack     | 23.81 |    32.14    |      26.79       |     60.12      |      35.12      |
| VisCo Attack  | 85.71 |    85.71    |      92.86       |     88.10      |      92.86      |
| CIA (Ours)    | **86.31** | **86.90** |   **85.12**   |   **91.07**    |    **82.14**    |

</div>

<p align="center">
<em>
Table: ASR (%) on MMSafetyBench-tiny aggregated over all categories.
</em>
</p>


### 📋 Attack Success Rate on SafeBench-tiny

<div align="center">

| Method      | GPT-4o | GPT-4o-mini | Gemini-2.0-Flash | Qwen2.5-VL | InternVL2.5 | Avg. ASR (%) |
|:-----------:|:-----:|:-----------:|:----------------:|:----------:|:-----------:|:------------:|
| Text        | 10.00 | 16.00 |  2.00 |  6.00 |  4.00 |  7.60 |
| FigStep     | 12.00 | 40.00 | 54.00 | 64.00 | 34.00 | 40.80 |
| FigStep-Pro |  2.00 |  0.00 | 30.00 | 26.00 |  8.00 | 13.20 |
| SI-Attack   |  2.00 | 30.00 | 38.00 | 62.00 | 50.00 | 36.40 |
| VisCo Attack| 76.00 | 86.00 | 80.00 | 86.00 | 88.00 | 83.20 |
| **CIA (ours)** | **86.00** | **92.00** | **96.00** | **92.00** | **88.00** | **90.80** |

</div>

<p align="center"><em>Table: ASR (%) on SafeBench-tiny across representative MLLMs.</em></p>


<!-- ## 🔧 Usage

This section provides a brief overview of how to run attacks and evaluate results using our released code and data.

### 1. Generate Priming Dialogues

Run the following command to generate priming dialogues:

```bash
bash gen_dialogue/run.sh
```

### 2. Inference (Model Completion)

“Inference” refers to injecting a crafted **priming dialogue** into the **target model**, followed by a final malicious query. The model's response is used to assess whether the attack succeeded.

- For **closed-source models** (e.g., GPT-4, Gemini), we use the official OpenAI API.
- For **open-source models**, we use [VLLM](https://github.com/vllm-project/vllm) for efficient batch inference.

You can run inference with the following script:

```bash
python generate_model_response.py \
    --input_dir data/dialogues/harmbench/dri \
    --model_name gpt-4o \
    --max_workers 10 \
    --include_v2v3
```

#### 🔍 Options:

- `--input_dir`: Directory containing prompt JSON files (e.g., one per harmful query).
- `--model_name`: Name of the target model (e.g., `gpt-4o`, `gemini-2.0-flash`, or any VLLM-hosted model).
- `--max_workers`: Number of concurrent workers for parallel inference.
- `--include_v2v3`: If set, runs attacks on up to **three priming dialogues per query** (`.json`, `_v2.json`, `_v3.json`) if available.  
  If not set or alternative versions are missing, only the base `.json` file will be used. -->


<!-- ## 🔧 Evaluation

We follow the evaluation protocol described in the paper.

To run evaluation:

```bash
bash eval/example.sh
``` -->

## 📃 Citation
If you find our project is helpful, please cite our paper as
```
@misc{xiong2025contextualimageattackvisual,
      title={Contextual Image Attack: How Visual Context Exposes Multimodal Safety Vulnerabilities}, 
      author={Yuan Xiong and Ziqi Miao and Lijun Li and Chen Qian and Jie Li and Jing Shao},
      year={2025},
      eprint={2512.02973},
      archivePrefix={arXiv},
      primaryClass={cs.CV},
      url={https://arxiv.org/abs/2512.02973}, 
}
```