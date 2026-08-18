# 🎬 FlixShield: Netflix Recommendation System & Content Analysis

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Complete-brightgreen.svg)]()

An end-to-end data science project that analyzes Netflix's content library (movies & TV shows up to 2021) and deploys a **content-based recommendation system** with an interactive web dashboard. Built to enhance user engagement and reduce subscriber churn through personalized content discovery.

---

## 🚀 Live Demo

Run locally in seconds:

```bash
pip install -r requirements.txt
python app.py
# Open http://localhost:424
```

---

## 📌 Project Highlights

| Feature | Description |
|---------|-------------|
| **Content-Based Recommender** | Cosine similarity on TF-IDF vectors (metadata: genres, cast, director, description, country, rating) |
| **Interactive Dashboard** | Netflix-styled Dash/Flask web app with real-time recommendations |
| **Unsupervised Clustering** | K-Means (k=4) & Agglomerative Hierarchical (k=2) with PCA dimensionality reduction |
| **Comprehensive EDA** | 10+ visualizations: genre trends, country distribution, rating analysis, word clouds |
| **Production-Ready** | Precomputed models (pickle), Gunicorn deployment support, modular architecture |

---

## 🏗️ Architecture Overview

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────────┐
│  Raw Netflix    │────▶│  Preprocessing   │────▶│  Feature Engineering │
│  Dataset (CSV)  │     │  (nulls, nested, │     │  (TF-IDF + PCA)      │
└─────────────────┘     │   rating bins)   │     └──────────┬──────────┘
                        └──────────────────┘                │
                                                           ▼
                        ┌──────────────────┐     ┌─────────────────────┐
                        │  Clustering      │◀────│  Dimensionality     │
                        │  (K-Means,       │     │  Reduction (PCA)    │
                        │   Hierarchical)  │     └─────────────────────┘
                        └────────┬─────────┘
                                 │
                                 ▼
                        ┌──────────────────┐     ┌─────────────────────┐
                        │  Cosine Similarity│────▶│  Dash Web App       │
                        │  Matrix (N×N)    │     │  (Real-time Recs)   │
                        └──────────────────┘     └─────────────────────┘
```

---

## 📊 Dataset

- **Source**: Netflix Movies and TV Shows dataset (up to 2021)
- **Size**: ~8,800 titles | 2.6 MB (`df_content.csv`)
- **Features**: `title`, `type`, `director`, `cast`, `country`, `date_added`, `release_year`, `rating`, `duration`, `listed_in` (genres), `description`
- **Preprocessing**: Missing value imputation, nested column splitting, rating binning (Adult/Teen/Family/Not Rated)

---

## 🔬 Methodology

### 1. Data Preprocessing
- **Null Handling**: Filled `director`, `cast`, `country` with "Unknown"; `date_added` with mode; `rating` categorized as "Not Rated"
- **Nested Columns**: Split comma-separated values in `director`, `cast` (top 3), `listed_in`, `country`
- **Rating Binning**: 
  - **Adult**: TV-MA, R, NC-17, NR, UR
  - **Teen**: TV-14, PG-13
  - **Family**: TV-PG, PG, G, TV-G, TV-Y, TV-Y7, TV-Y7-FV
  - **Not Rated**: NR, UR, Not Rated

### 2. Feature Engineering & Vectorization
- **Text "Soup"**: Combined director + top 3 cast + country + genres + rating category + description
- **NLP Pipeline**: Lowercasing → Stopword removal (NLTK) → Lemmatization (spaCy) → Special character removal
- **TF-IDF**: `max_features=5000`, `ngram_range=(1,2)`, `stop_words='english'`

### 3. Dimensionality Reduction
- **PCA**: Retained ~95% variance for faster clustering and noise reduction

### 4. Clustering
| Algorithm | Optimal Clusters | Key Insight |
|-----------|------------------|-------------|
| **K-Means** | 4 (Elbow + Silhouette) | Drama/International, Action/Thriller, Comedy/Family, Documentaries |
| **Agglomerative** | 2 (Dendrogram) | Movie-heavy vs TV Show-heavy content |

### 5. Recommendation System
- **Method**: Cosine similarity on TF-IDF vectors
- **Input**: Any title from dataset
- **Output**: Top 10 similar titles with similarity scores (0–1)
- **Latency**: Sub-millisecond (precomputed matrix)

---

## 🛠️ Tech Stack

| Category | Tools |
|----------|-------|
| **Core** | Python 3.8+, Pandas, NumPy |
| **ML/NLP** | Scikit-learn (TF-IDF, PCA, K-Means, Agglomerative, cosine_similarity), NLTK, spaCy |
| **Visualization** | Matplotlib, Seaborn, Plotly, WordCloud, Yellowbrick |
| **Web Framework** | Flask, Dash, Dash Table |
| **Deployment** | Gunicorn |
| **Development** | Jupyter Notebook, Jupyter Dash |

---

## 📁 Repository Structure

```
FlixShield-Netflix-Recommendation-System-and-Content-Analysis/
│
├── app.py                                    # Dash/Flask web application
├── app_style.css                             # Custom Netflix-themed styling
├── requirements.txt                          # Python dependencies
├── README.md                                 # This file
├── PROJECT_DOCUMENTATION.md                  # Comprehensive technical docs
│
├── df_content.csv                            # Preprocessed dataset (2.6 MB)
├── tfidf_vectorizer.pkl                      # Trained TF-IDF vectorizer (1.4 MB)
├── cosine_similarity.pkl                     # Precomputed similarity matrix
│
└── netflix-recommendation-system-and-content-analysis.ipynb  # Full analysis notebook
```

---

## 🚀 Quick Start

### Prerequisites
- Python 3.8+
- pip

### Installation
```bash
# Clone the repository
git clone https://github.com/Davender/FlixShield-Netflix-Recommendation-System-and-Content-Analysis.git
cd FlixShield-Netflix-Recommendation-System-and-Content-Analysis

# Install dependencies
pip install -r requirements.txt
```

### Run the Dashboard
```bash
# Development
python app.py
# Visit http://localhost:424

# Production (Gunicorn)
gunicorn -w 4 -b 0.0.0.0:424 app:app.server
```

### Run the Analysis Notebook
```bash
jupyter notebook netflix-recommendation-system-and-content-analysis.ipynb
```

---

## 📸 Dashboard Preview

The web app features:
- **Netflix-branded UI** (red/black theme, custom fonts)
- **Searchable dropdown** with all 8,800+ titles
- **Real-time recommendations** in a styled data table
- **Error handling** for edge cases

---

## 📈 Key Results

| Metric | Result |
|--------|--------|
| **Content Split** | ~70% Movies, ~30% TV Shows |
| **Top Genres** | Drama, Comedy, Action, Documentary |
| **Top Countries** | United States, India, United Kingdom |
| **K-Means Silhouette** | ~0.35 (well-separated clusters) |
| **Recommendation Latency** | < 1ms (precomputed) |

---

## 🔮 Future Enhancements

- [ ] **Hybrid Recommender**: Add collaborative filtering with user interaction data
- [ ] **Deep Learning**: BERT/transformer embeddings for descriptions
- [ ] **Explainability**: Show *why* (shared genres, actors, directors)
- [ ] **Real-time Pipeline**: Automated ingestion for new content
- [ ] **A/B Testing Framework**: Measure CTR and engagement
- [ ] **Mobile App**: React Native / Flutter frontend
- [ ] **Multi-language Support**: International content recommendations

---

## 📄 Documentation

- **README.md** — This file (project overview & quick start)
- **PROJECT_DOCUMENTATION.md** — Complete technical documentation with code snippets, methodology details, and deployment guides

---

## 👤 Author

**Davender**  
[GitHub Profile](https://github.com/Davender) • [LinkedIn](https://linkedin.com/in/davender) *(add your link)*

---

## 🤝 Contributing

Contributions welcome! Please:
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📜 License

Distributed under the MIT License. See `LICENSE` for more information.

---

## 🙏 Acknowledgments

- Netflix dataset from [Kaggle](https://www.kaggle.com/datasets/shivamb/netflix-shows)
- Inspired by Netflix's own recommendation research
- Built with ❤️ using open-source tools

---

> **FlixShield** — *Because everyone deserves their perfect next watch.*