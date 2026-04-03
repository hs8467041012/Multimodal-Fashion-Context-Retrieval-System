# Multimodal Fashion & Context Retrieval 

This repository implements an **intelligent multimodal search engine** that retrieves the most relevant fashion images from a large dataset using **natural language queries**.  
The system understands:
- **What** a person is wearing (clothing type, attributes)
- **Where** they are (scene/context like office/street/park)
- The **vibe/style** of the outfit (formal/casual/weekend)

It is designed to be **stronger than vanilla CLIP retrieval**, with improvements targeting **compositionality** and **fine-grained fashion understanding**, as required in the assignment.

---

## 🚀 Key Highlights
✅ **Dataset size:** ~45,000 images  
✅ **Efficient indexing:** dataset split into 4 parts and indexed separately  
✅ **Vector Search Engine:** FAISS (Inner Product / Cosine Similarity)  
✅ **Fashion-aligned Embeddings:** FashionCLIP (domain adapted CLIP)  
✅ **Attribute-aware reranking:** BLIP captions → structured attribute extraction  
✅ **Compositional boost:** region-based color matching (top/bottom)  
✅ **Powerful reranker:** BLIP-ITM cross encoder (image-text matching)  

This architecture follows a professional-grade retrieval approach:
> **Retrieve (fast ANN)** → **Rerank (attribute + cross encoder)** → **Top-k results**

---

# 🧠 System Architecture (Two-Stage Retrieval)

### **Stage 1: Candidate Retrieval (Fast)**
- Convert query → **FashionCLIP text embedding**
- Search FAISS index → Top-N candidates (e.g., 200)

### **Stage 2: Multi-Signal Reranking (Accurate)**
Rerank candidates using:
- **Attribute match score**: color + clothing + scene overlap
- **Region match score**: top/bottom color validation for compositional queries
- **Cross encoder BLIP-ITM score**: direct query-image relevance

---

# 📁 Dataset Handling (45K Images Strategy)

Initially, the dataset contained **~45,000 images**.  
To avoid Colab RAM/runtime limitations, the dataset was split into **4 parts**, and **FAISS indexes were generated separately**.

### Dataset Split:
- **Part 1:** 11,000+ images  
- **Part 2:** 11,000+ images  
- **Part 3:** 12,000+ images  
- **Part 4:** 11,000+ images  

Each part generates:
- `faiss_indexX.bin`
- `image_pathsX.pkl`
- `captionsX.pkl`
- `attributesX.pkl`
- `region_metaX.pkl`

Finally, all 4 parts are merged into **one unified FAISS index** and metadata set for full retrieval.

---

# ✅ Technical Requirements Mapping

### ✅ Part A: The Indexer
✔ Feature Extraction:  
- FashionCLIP image embeddings
- BLIP caption generation for semantic metadata  
✔ Vector Storage:  
- FAISS vector database (no filename keyword matching)

### ✅ Part B: The Retriever
✔ Search Logic:  
- Input: natural language query  
- Output: top-k matching images  
✔ Context Awareness:  
Handles multi-attribute queries like:  
- “red tie and white shirt in formal setting”
- “professional business attire inside modern office”

---

# 📂 Repository Structure

### Indexing Notebooks
- `Index_Part1_clean.ipynb`
- `Index_Part2_clean.ipynb`
- `Index_Part3_clean.ipynb`
- `Index_Part4_clean.ipynb`

### Merge Notebook
- `Merge_Indices.ipynb`

### Retriever Notebook
- `Retriever_clean.ipynb`

### Output / Results
- `Index_Part_Output.zip`  
  → contains output index files for each dataset part  
- `Retriever Output.zip`  
  → contains retrieval outputs + results for evaluation queries  
- `FAISS Indices Output.png`  
  → indexing output snapshot  

### Merged Index Files (final)
- `faiss_index.bin`
- `image_paths.pkl`
- `captions.pkl`
- `attributes.pkl`
- `region_meta.pkl`

---

# ⚙️ How to Run (Google Colab)

## Step 1 — Build indices
Run notebooks in this order:
1. `Index_Part1_clean.ipynb`
2. `Index_Part2_clean.ipynb`
3. `Index_Part3_clean.ipynb`
4. `Index_Part4_clean.ipynb`

Each notebook creates one index part.
---

## Step 2 — Merge indices
Run:
- `Merge_Indices.ipynb`
---

## Step 3 — Retrieval
Run:
- `Retriever_clean.ipynb`

The retriever supports **top-k visualization**, reranking, and cross encoder scoring.

---

# 🔍 Evaluation Queries (from assignment)

The retriever was tested on the required evaluation prompts:

1. **Attribute Specific:** `"A person in a bright yellow raincoat."`
2. **Contextual/Place:** `"Professional business attire inside a modern office."`
3. **Complex Semantic:** `"Someone wearing a blue shirt sitting on a park bench."`
4. **Style Inference:** `"Casual weekend outfit for a city walk."`
5. **Compositional:** `"A red tie and a white shirt in a formal setting."`

📌 Outputs and retrieved image grids are saved in:  
✅ `Retriever Output.zip`

---

# 📈 Accuracy & Retrieval Quality (Qualitative)

Since the dataset does not provide structured ground-truth captions for quantitative recall@k evaluation, **performance is evaluated qualitatively** through:
- correctness of clothing type retrieval
- context correctness (scene/setting)
- compositional matching (top/bottom colors)
- style consistency

### Improvements over vanilla CLIP:
✅ FashionCLIP reduces fashion-domain mismatch  
✅ BLIP caption attributes improve multi-attribute understanding  
✅ Region scoring improves compositionality  
✅ BLIP-ITM reranker boosts fine-grained relevance (largest gain)

---

# ⚖️ Trade-offs & Design Choices

### ✅ Why FAISS?
- extremely fast ANN search
- lightweight + easiest to integrate in Colab
- supports large-scale indexing

### ✅ Why dataset split into 4 parts?
- 45k images embedding + captioning in one run is heavy
- splitting avoids OOM errors and runtime timeouts
- allows modular indexing and easy resume

### ✅ Why BLIP Captioning?
- CLIP embeddings alone often miss fine fashion details
- captions provide explicit clothing/color/context signals

### ✅ Why Cross Encoder reranker?
- strongest accuracy improvement
- but slower, so applied only on shortlisted candidates (top-50)

---

# 🔧 Limitations & Future Improvements
- full compositional reasoning is still limited without segmentation
- region color matching uses simple top/bottom split (can be improved using segmentation)
- can add:
  - Human parsing / body segmentation for accurate top/bottom regions
  - Fashion attribute classifiers (sleeve length, fit type, material)
  - quantitative metrics like Recall@K with labeled annotations

---

# ✅ Final Notes
This project follows a production-style retrieval architecture:
**fast retrieval + intelligent reranking**, and directly meets the assignment requirements with additional ML logic improvements for fashion retrieval.

---

📌 **Author:** Himanshu Kumar Sukralia  
📌 **Assignment:** Glance ML Internship — Multimodal Fashion & Context Retrieval


