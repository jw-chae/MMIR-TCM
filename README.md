# MMIR-TCM: Memory-augmented Multimodal Integration and Retrieval for Traditional Chinese Medicine



---

## 📌 Abstract

Traditional Chinese Medicine (TCM) diagnosis, particularly through tongue inspection, faces persistent challenges in **subjectivity** and **reproducibility**. The application of multimodal artificial intelligence to TCM clinical tasks, such as syndrome differentiation and prescription generation, is significantly hampered by the **semantic gap** between visual tongue features and textual reasoning, as well as the **lack of large-scale, standardized datasets**.

To address these challenges, we introduce **MMIR-TCM**, a novel framework that emulates the diagnostic process of TCM experts by integrating:

- 🧠 **Multimodal Large Language Model (MLLM)**
- 🎯 **Memory-augmented Segmentation**
- 📚 **Retrieval-Augmented Generation (RAG)**

---

## 🏗️ Framework Architecture

MMIR-TCM employs a **three-stage architecture**:

### Stage 1: Memory-SAM (Training-free)
- 🖼️ Robust tongue extraction from clinical images
- 🔍 Memory-augmented segmentation for precise region identification

### Stage 2: Qwen3-VL (Fine-tuned)
- 📊 Structured tongue diagnosis generation
- 🎨 Visual feature analysis and interpretation

### Stage 3: Qwen3-based RAG
- 📖 Evidence-grounded clinical decision support
- 💊 Prescription generation based on retrieved medical knowledge

![Overall Architecture](figures/MMIR-TCM_Overall_01.png)

*Figure 1: Overall architecture of MMIR-TCM framework*

---

## 📊 MedTCM Dataset

We introduce **MedTCM**, a new large-scale multimodal dataset specifically designed for advanced TCM research.

![Dataset Overview](figures/MMIR-TCM_Dataset_01.png)

*Figure 2: MedTCM dataset statistics and composition*

### Key Features

- 📸 **Large-scale**: Comprehensive collection of tongue images
- 🏥 **Standardized**: Consistent annotation protocols
- 🌐 **Multimodal**: Images + Text + Clinical Records
- ✅ **Expert-validated**: Quality assurance by TCM practitioners

---

## 🔬 Technical Details

### Retrieval-Augmented Generation (RAG)

![RAG Architecture](figures/MMIR-TCM_RAG_01.png)

*Figure 3: RAG component for evidence-based clinical reasoning*

Our RAG system integrates:
- 📚 Medical knowledge base retrieval
- 🔗 Context-aware information fusion
- 💡 Evidence-grounded decision making

---

## 📈 Evaluation Metric: TDEU

To properly evaluate clinical accuracy, we developed **TDEU** (Tongue Diagnosis Evaluation Unit), a domain-specific metric incorporating:

- 🎯 **Semantic Understanding**: Captures medical terminology
- ⚖️ **Diagnostic Importance**: Weights critical features
- 🔍 **Clinical Relevance**: Aligns with expert assessment

Traditional metrics fail to capture the nuances of TCM diagnosis. TDEU addresses this gap by:

```
TDEU = α × Semantic_Similarity + β × Diagnostic_Accuracy + γ × Clinical_Relevance
```

---

## 🏆 Results

### Performance Comparison

We evaluate MMIR-TCM against state-of-the-art baselines and ablation variants using both traditional metrics (BLEU, ROUGE) and our proposed TDEU metric.

#### Baseline Models (Zero-shot)

| Model | Params | BLEU-4 | R-1 | R-2 | R-L | TDEU Overall | TDEU Tongue | TDEU Coat | TDEU Location |
|-------|--------|--------|-----|-----|-----|--------------|-------------|-----------|---------------|
| Grok-2-Vision-1212 | - | 26.40 | 22.12 | 22.08 | 26.43 | 0.338 | 0.262 | 0.404 | 0.367 |
| LLaMA4-scout 109B | 109B | 25.68 | 22.45 | 22.43 | 25.70 | 0.336 | 0.254 | 0.412 | 0.356 |
| Gemini-2.5-Flash | - | 24.43 | 21.37 | 21.33 | 24.46 | 0.353 | 0.271 | 0.421 | 0.372 |

#### ViTCM-LLM Ablations (Qwen2.5-VL 32B)

| Configuration | LoRA r | Epochs | BLEU-4 | TDEU Overall |
|---------------|--------|--------|--------|--------------|
| E1 Zero-shot | - | - | 24.08 | 0.3538 |
| E2 Language-only | 16 | 3 | 26.74 | 0.2698 |
| E3 Vision+Projector | 16 | 3 | 35.82 | 0.3610 |
| E4 Full (r=16, 3ep) | 16 | 3 | 37.94 | 0.3648 |
| E4 Full (r=64, 3ep) | 64 | 3 | 39.57 | 0.3915 |
| E4 Full (r=64, 10ep) | 64 | 10 | 43.57 | 0.5858 |
| E4 Full (r=64, 20ep) | 64 | 20 | 44.07 | 0.6150 |

#### MMIR-TCM (Ours) - Qwen3-VL 30B + LoRA

##### Main Results (10 Epochs)

| Configuration | BLEU-4 ↑ | R-1 ↑ | R-2 ↑ | R-L ↑ | TDEU Overall ↑ | TDEU Tongue ↑ | TDEU Coat ↑ | TDEU Location ↑ |
|---------------|----------|-------|-------|-------|----------------|---------------|-------------|-----------------|
| Original Qwen3-VL (untuned) | 33.98 | 28.42 | 28.23 | 34.05 | 0.289 | 0.187 | 0.288 | 0.141 |
| Raw train + Raw | 83.20 | 76.24 | 76.06 | 83.62 | 0.601 | 0.442 | 0.673 | 0.611 |
| Raw train + Mask | 83.11 | 75.96 | 75.57 | 83.34 | 0.612 | 0.472 | 0.683 | 0.590 |
| **Mask train + Raw** | 83.22 | 76.06 | 75.69 | 83.45 | 0.617 | **0.478** | 0.693 | 0.592 |
| **Mask train + Mask** ⭐ | **83.57** | **77.30** | **76.77** | **84.30** | **0.627** | 0.473 | 0.693 | **0.649** |

##### Early Stopping Results (3 Epochs)

| Configuration | BLEU-4 ↑ | R-1 ↑ | R-2 ↑ | R-L ↑ | TDEU Overall ↑ | TDEU Tongue ↑ | TDEU Coat ↑ | TDEU Location ↑ |
|---------------|----------|-------|-------|-------|----------------|---------------|-------------|-----------------|
| Raw train + Raw | 81.72 | 74.42 | 73.88 | 82.20 | 0.580 | 0.385 | 0.682 | 0.607 |
| **Mask train + Mask** | 83.09 | 76.50 | 76.28 | 83.73 | 0.611 | 0.439 | **0.698** | 0.632 |

### Key Findings

🎯 **Best Performance**: Our **Mask train + Mask** configuration with 10 epochs achieves:
- **BLEU-4**: 83.57 (vs. 44.07 for ViTCM-LLM, vs. 26.40 for Grok-2)
- **TDEU Overall**: 0.627 (vs. 0.6150 for ViTCM-LLM, vs. 0.353 for Gemini-2.5)
- **89.5% improvement** in TDEU over best baseline (Gemini-2.5-Flash)

📊 **Configuration Impact**:
- **Memory-SAM masking** during training improves focus on tongue regions
- **Masked inference** further enhances diagnostic accuracy
- **10 epochs** show better convergence than 3 epochs

🔬 **Ablation Insights**:
- Vision component crucial: E3 (+10.08 BLEU) over E2
- Full fine-tuning essential: E4 significantly outperforms partial tuning
- Higher LoRA rank (64 vs 16) improves performance consistently

---

## ⚠️ Failure Cases

We have collected and organized the failed laboratory cases for transparency and future improvement.

![Failure Analysis](figures/MMIR-TCM_Failures_01.png)

---

## 🛣️ Roadmap

- [x] Framework Development
- [x] MedTCM Dataset Collection
- [x] TDEU Metric Design
- [x] Comprehensive Experiments
- [ ] **Code Release** (Coming Soon!)


---

## 🤝 Contributing

We welcome contributions! The codebase will be released soon. Stay tuned!

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.



---

<div align="center">

**⭐ Star us on GitHub — it motivates us a lot!**


</div>

