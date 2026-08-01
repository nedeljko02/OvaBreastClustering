# OVA_Breast Clustering 🧬

Seminarski rad iz predmeta **Istraživanje podataka 2** — klasterovanje skupa podataka
OVA_Breast (mikroarray podaci genske ekspresije, deo OVA/Ovarian kolekcije, OpenML ID 1128).

## 🚀 Pokretanje

### 1. Setup okruženja

```bash
# Kreiraj virtuelno okruženje
python3 -m venv .venv

# Aktiviraj ga
source .venv/bin/activate  # Linux/Mac
.venv\Scripts\activate     # Windows

# Instaliraj zavisnosti
pip install -r requirements.txt
```

### 3. Pokreni Jupyter Notebook

```bash
jupyter notebook notebooks/OVA_Breast_Clustering.ipynb
```

### 4. Pokretanje ćelija

- **Pokreni sve odjednom**: `Cell` → `Run All`
- **Ili pokreni redom**: `Shift + Enter` za svaku ćeliju

Notebook je organizovan po radnim danima (Dan 1–3), sa markdown ćelijama koje
označavaju tačke u kojima je u stvarnom radu napravljen git commit (`🔵 GIT COMMIT`).

## 📂 Gde se čuvaju rezultati

Nakon izvršavanja, popuniće se:

### `data/` folder
- `data_preprocessed_pca50.csv` — PCA redukcija (50 komponenti)
- `data_preprocessed_topvar200.csv` — top 200 najvarijabilnijih gena
- `data_preprocessed_full.pkl` — pun standardizovani skup (lokalno, nije u git-u — prevelik)

### `output/` folder
- `clustering_results.csv` — svi rezultati (18 kombinacija algoritam × skup atributa)
- `top15_results.csv` — top 15 modela po Silhouette koeficijentu
- `all_cluster_labels.pkl` — labele klastera za sve kombinacije
- `best_model.pkl` — najbolji model
- `scaler.pkl`, `pca_50.pkl`, `top_var_genes.pkl` — pomoćni objekti za preprocesiranje

### `visualizations/` folder — grafici (PNG)
- `class_distribution.png` — raspodela Tissue klasa
- `pca_explained_variance.png` — kumulativna objašnjena varijansa
- `pca_2d_3d.png` — 2D/3D PCA vizuelizacija
- `elbow_silhouette.png` — Elbow i Silhouette analiza za izbor K
- `algorithm_comparison.png` — agregatno poređenje algoritama
- `heatmap_silhouette.png` — heatmap Silhouette po algoritmu/skupu
- `full_vs_reduced.png` — poređenje punog i redukovanih skupova atributa
- `best_model_visualization.png` — vizuelizacija najboljeg modela

## 📊 Šta notebook radi

1. Učitava `.arff` podatke i prikazuje osnovnu statistiku
2. Preprocesira podatke (uklanjanje ID_REF, standardizacija)
3. Kreira 3 skupa atributa: Full (10935 atr.), PCA-50, TopVar-200
4. Vizuelizuje podatke u 2D i 3D (PCA projekcija)
5. Određuje optimalan broj klastera (Elbow + Silhouette)
6. Primenjuje 6 konfiguracija algoritama (K-Means ×2, Agglomerative ×2, DBSCAN, BIRCH)
   na sva 3 skupa atributa (18 kombinacija ukupno)
7. Evaluira rezultate (Silhouette, Davies-Bouldin, Calinski-Harabasz, ARI, NMI)
8. Poredi pun skup naspram redukovanih skupova atributa
9. Analizira i vizuelizuje najbolji model

## 📄 Tekstualni deo rada

Kompletan tekstualni deo rada nalazi se u `zapisnik.pdf` (izvorni LaTeX kod u
`zapisnik.tex`), i sadrži: tekst zadatka, opis podataka, opis obrade, opis i tumačenje
rezultata.

---

**Autor**: Luka Nedeljković 147/2021
