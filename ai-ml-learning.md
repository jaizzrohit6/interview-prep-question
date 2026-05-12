# 🤖 java-dev-learns-ai

> A structured, phase-by-phase journey from **Java Backend Developer → AI Engineer**
> Built by a developer with 7+ years of Java/Spring experience at Dell, learning AI/ML from scratch.

![Progress](https://img.shields.io/badge/Status-In%20Progress-yellow?style=flat-square)
![Phase](https://img.shields.io/badge/Current%20Phase-1%20Math%20%2B%20Python-blue?style=flat-square)
![XP](https://img.shields.io/badge/Experience-7%2B%20yrs%20Java%2FSpring-orange?style=flat-square)
![Goal](https://img.shields.io/badge/Goal-AI%20Engineer-green?style=flat-square)

---

## 🎯 Goal

Not an AI researcher. An **AI Engineer** — someone who builds and deploys real AI systems using a strong backend foundation.

---

## 🗺️ Roadmap Overview

| Phase | Topic | Duration | Status |
|-------|-------|----------|--------|
| 0 | Reset Mental Model | 1–2 weeks | ⬜ Not Started |
| 1 | Math + Python Foundations | 3–5 weeks | ⬜ Not Started |
| 2 | Core Machine Learning | 4–6 weeks | ⬜ Not Started |
| 3 | Deep Learning | 4–6 weeks | ⬜ Not Started |
| 4 | NLP + Generative AI | 1–2 months | ⬜ Not Started |
| 5 | AI Engineering | Ongoing | ⬜ Not Started |

> ⏱️ **Total:** ~5–6 months at 2–3 hrs/day

---

## 📁 Repository Structure

```
java-dev-learns-ai/
│
├── phase-0-mental-model/
│   └── notes.md
│
├── phase-1-math-python/
│   ├── linear-algebra/
│   ├── probability/
│   ├── numpy-basics/
│   ├── pandas-basics/
│   └── matplotlib-basics/
│
├── phase-2-core-ml/
│   ├── linear-regression/
│   ├── logistic-regression/
│   ├── decision-trees/
│   ├── k-means-clustering/
│   └── projects/
│       ├── spam-classifier/
│       ├── churn-predictor/
│       └── recommendation-system/
│
├── phase-3-deep-learning/
│   ├── neural-networks-basics/
│   ├── backpropagation/
│   ├── pytorch-intro/
│   └── projects/
│       ├── mnist-image-classifier/
│       └── sentiment-analyzer/
│
├── phase-4-nlp-genai/
│   ├── transformers/
│   ├── embeddings/
│   ├── huggingface/
│   ├── langchain/
│   ├── vector-databases/
│   └── projects/
│       ├── chatbot-with-memory/
│       ├── document-qa-rag/
│       └── resume-analyzer/
│
├── phase-5-ai-engineering/
│   ├── fastapi-model-serving/
│   ├── docker-deployment/
│   ├── spring-boot-ai-integration/
│   ├── observability/
│   └── projects/
│       ├── ai-microservice/
│       ├── rag-api-spring-boot/
│       └── dell-usecase-poc/
│
└── resources/
    ├── books.md
    ├── courses.md
    └── cheatsheets/
```

---

## 🧭 Phase 0 — Reset Your Mental Model

**Duration:** 1–2 weeks

### Key Concepts
- AI vs ML vs Deep Learning
- Training vs Inference
- Supervised vs Unsupervised Learning
- Overfitting, bias-variance tradeoff
- Evaluation metrics: accuracy, precision, recall, F1

### Resources
- 📺 [3Blue1Brown — Neural Networks](https://www.youtube.com/playlist?list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi)
- 🌐 [Google ML Crash Course](https://developers.google.com/machine-learning/crash-course)
- 📺 [StatQuest with Josh Starmer](https://www.youtube.com/@statquest)

---

## 📐 Phase 1 — Math + Python Foundations

**Duration:** 3–5 weeks

### Topics
- Linear Algebra (vectors, matrices, dot product)
- Probability & Statistics (distributions, Bayes' theorem)
- Calculus (derivatives, gradient descent intuition)
- Python: NumPy, Pandas, Matplotlib, Seaborn

### Resources
- 📺 [3Blue1Brown — Essence of Linear Algebra](https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab)
- 🌐 [Kaggle Python Course](https://www.kaggle.com/learn/python)
- 🌐 [Kaggle Pandas Course](https://www.kaggle.com/learn/pandas)
- 📚 *Python Crash Course* — Eric Matthes

### Mini Project
```python
# Load a real dataset, explore and visualize it with Pandas + Matplotlib
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv('dataset.csv')
print(df.describe())
df['target'].hist()
plt.show()
```

---

## 🤖 Phase 2 — Core Machine Learning

**Duration:** 4–6 weeks

### Algorithms
- Linear Regression
- Logistic Regression
- Decision Trees & Random Forests
- K-Means Clustering

### Tool: Scikit-learn

```python
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)
model = RandomForestClassifier(n_estimators=100)
model.fit(X_train, y_train)
print(classification_report(y_test, model.predict(X_test)))
```

### Projects
- [ ] 📧 Spam Classifier
- [ ] 📉 Customer Churn Predictor
- [ ] 🎬 Simple Recommendation System

### Resources
- 🌐 [Kaggle Intro to ML](https://www.kaggle.com/learn/intro-to-machine-learning)
- 🎓 [ML A-Z — Udemy](https://www.udemy.com/course/machinelearning/)
- 📚 *Hands-On ML with Scikit-Learn, Keras & TensorFlow* — Aurélien Géron

---

## 🧠 Phase 3 — Deep Learning

**Duration:** 4–6 weeks

### Topics
- Neural network architecture (layers, neurons, activations)
- Backpropagation & gradient descent
- Optimizers: SGD, Adam
- Regularization: Dropout, Batch Norm

### Framework: PyTorch

```python
import torch.nn as nn

class Net(nn.Module):
    def __init__(self):
        super().__init__()
        self.layers = nn.Sequential(
            nn.Linear(784, 128), nn.ReLU(), nn.Dropout(0.2),
            nn.Linear(128, 64),  nn.ReLU(),
            nn.Linear(64, 10)
        )
    def forward(self, x):
        return self.layers(x)
```

### Projects
- [ ] 🔢 MNIST Image Classifier (>97% accuracy)
- [ ] 🎭 IMDB Sentiment Analyzer

### Resources
- 🌐 [fast.ai — Practical Deep Learning](https://course.fast.ai/)
- 📺 [Andrej Karpathy — Zero to Hero](https://www.youtube.com/playlist?list=PLAqhIrjkxbuWI23v9cThsA9GvCAUhRvKZ)
- 🎓 [Deep Learning Specialization — Andrew Ng](https://www.coursera.org/specializations/deep-learning)

---

## 💬 Phase 4 — NLP + Generative AI

**Duration:** 1–2 months

### Topics
- Transformers architecture (Attention, BERT, GPT)
- Embeddings & semantic search
- Fine-tuning vs Prompting
- RAG (Retrieval Augmented Generation)
- Vector databases

### Tools
- 🤗 Hugging Face Transformers
- 🦜 LangChain / LlamaIndex
- 🧠 OpenAI API
- 🗃️ ChromaDB / Pinecone

```python
from transformers import pipeline

classifier = pipeline('sentiment-analysis')
print(classifier('I love building AI systems!'))
# [{'label': 'POSITIVE', 'score': 0.9998}]
```

### Projects
- [ ] 🤖 Chatbot with Context Memory (LangChain)
- [ ] 📄 Document Q&A System (RAG)
- [ ] 📝 Resume Analyzer

### Resources
- 🌐 [HuggingFace NLP Course](https://huggingface.co/learn/nlp-course)
- 🌐 [DeepLearning.AI Short Courses](https://www.deeplearning.ai/short-courses/)
- 📚 *NLP with Transformers* — Tunstall et al.

---

## ⚙️ Phase 5 — AI Engineering *(Unfair Advantage)*

**Duration:** Ongoing

> This is where 7+ years of Java/Spring experience becomes a **superpower**.

### Topics
- Serving models with FastAPI + Docker
- Spring Boot ↔ AI service integration
- Spring AI (native Java LLM integration)
- LLM observability (latency, hallucination, drift)
- Event-driven AI pipelines with Kafka

### Spring Boot + AI Integration

```java
@RestController
public class AIController {

    @Autowired
    private ChatClient chatClient;

    @PostMapping("/chat")
    public String chat(@RequestBody String message) {
        return chatClient.prompt()
            .user(message)
            .call()
            .content();
    }
}
```

### Projects
- [ ] 🚀 FastAPI Model Server + Docker
- [ ] 🔗 RAG API with Spring Boot backend
- [ ] 💡 AI Feature PoC for Dell use case

### Resources
- 🌐 [Spring AI Docs](https://docs.spring.io/spring-ai/reference/)
- 🌐 [MLOps Zoomcamp — Free](https://github.com/DataTalksClub/mlops-zoomcamp)
- 📚 *Designing ML Systems* — Chip Huyen ⭐ Must Read

---

## 🧱 Tech Stack

| Layer | Technology |
|-------|-----------|
| ML & AI Work | Python, PyTorch, Scikit-learn |
| Production APIs | Java, Spring Boot, Spring AI |
| Model Serving | FastAPI, Uvicorn |
| Deployment | Docker, Kubernetes |
| Vector DB | ChromaDB (dev), Pinecone (prod) |
| LLM Providers | OpenAI, AWS Bedrock |
| Observability | LangSmith, Prometheus, Grafana |
| Cloud | AWS / Azure |

---

## 📚 Top Resources

### 🆓 Free
| Resource | Type | Best For |
|----------|------|----------|
| [fast.ai](https://course.fast.ai/) | Course | DL (Phase 3) |
| [Kaggle Learn](https://www.kaggle.com/learn) | Course | Python, ML (Phase 1-2) |
| [HuggingFace Learn](https://huggingface.co/learn) | Course | NLP, LLMs (Phase 4) |
| [DeepLearning.AI Short Courses](https://www.deeplearning.ai/short-courses/) | Course | GenAI, RAG (Phase 4) |
| [Karpathy — Zero to Hero](https://www.youtube.com/playlist?list=PLAqhIrjkxbuWI23v9cThsA9GvCAUhRvKZ) | YouTube | DL/LLMs (Phase 3-4) |
| [MLOps Zoomcamp](https://github.com/DataTalksClub/mlops-zoomcamp) | Course | Deployment (Phase 5) |

### 💰 Paid (Udemy — Wait for ₹499 Sales)
| Course | Best For |
|--------|----------|
| ML A-Z — Kirill Eremenko | Phase 2 |
| PyTorch for DL — Daniel Bourke | Phase 3 |
| LangChain Bootcamp — Jose Portilla | Phase 4 |
| Docker & K8s — Maximilian Schwarzmüller | Phase 5 |

### 📖 Books
| Book | Phase |
|------|-------|
| *Hands-On ML* — Aurélien Géron | 2–3 |
| *NLP with Transformers* — Tunstall | 4 |
| *Designing ML Systems* — Chip Huyen | 5 |
| *The Hundred-Page ML Book* — Burkov | 0 |

---

## ⚠️ Common Mistakes I'm Avoiding

- ❌ Jumping to LLMs without ML fundamentals
- ❌ Tutorial hell — watching without building
- ❌ Skipping math completely
- ❌ Trying to learn everything at once

---

## 📊 Progress Log

| Date | Milestone |
|------|-----------|
| — | Started the journey |

---

## 🤝 Connect

If you're on a similar journey, feel free to connect or open a discussion!

---

<div align="center">

**Built with 💪 by a Java backend dev who refused to be left behind by AI**

*7 years of Spring Boot → Now learning to make machines think*

</div>
