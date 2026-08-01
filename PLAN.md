# Projekat: OVA-Breast-Clustering — plan i kontekst za AI asistenta

> Ovaj fajl se šalje AI modelu (Claude) da razume kompletan kontekst projekta,
> podatke, plan rada i Git workflow. Pročitaj ceo fajl pre davanja saveta o
> bilo kom delu.

---

## 1. Šta je projekat

Seminarski rad iz predmeta **Istraživanje podataka 2**, Matematički fakultet.
Zadatak: **klasterovanje** skupa podataka **OVA_Breast** (deo OVA/Ovarian
kolekcije mikroarray skupova genske ekspresije, dostupne na OpenML).

Radi se **samostalno** (jedan student, ne tim), rok je **4 dana**.

Repo: `nedeljko02/OvaBreastClustering` (GitHub, public).

---

## 2. Podaci — OVA_Breast

- Format: `.arff`
- **1544 instance** (uzorci tkiva)
- **10935 numeričkih atributa** genske ekspresije (mikroarray probesetovi,
  npr. `1007_s_at`, `AFFX-...`)
- **1 identifikator**: `ID_REF` (numerički, treba ga izbaciti kao feature)
- **1 ciljni atribut**: `Tissue`, binaran `{Breast, Other}`
  - Breast: 343 (22.2%)
  - Other: 1201 (77.8%)
  - **Jako neuravnoteženo** — koristi se SAMO za eksternu evaluaciju posle
    klasterovanja (ARI, NMI i sl.), NIKAD kao ulaz u algoritam klasterovanja
- **Problem: high-dimension, low-sample-size (HDLSS)** — broj atributa (10935)
  je ~7x veći od broja instanci (1544). Ovo je centralni metodološki izazov
  celog rada i mora biti eksplicitno adresiran u tekstu.
- Vrednosti ekspresije variraju u ogromnim rasponima (npr. od ~9 do >200000
  u istom redu) → obavezno skaliranje/normalizacija pre klasterovanja.
- Originalni fajl je ~108MB — **prevelik za GitHub** (limit 100MB/fajl).
  Ne verzionisati sirovi `.arff`; u Readme napisati izvor (OpenML link),
  a u repo staviti samo preprocesirane/redukovane verzije (mnogo manje).

---

## 3. Zahtevi iz uputstva predmeta (obavezno)

- Obavezno preprocesiranje podataka.
- Minimum **5 algoritama** klasterovanja.
- Analiza i poređenje rezultata različitih algoritama.
- Predaja sadrži:
  - tekstualni deo u **PDF**-u (tekst zadatka, opis podataka, opis obrade,
    opis i tumačenje rezultata)
  - podatke (originalna verzija + verzija posle preprocesiranja)
  - konstruisane modele (u odgovarajućem obliku za korišćeni alat — ovde:
    Python/scikit-learn, modeli kao `.pkl`)
  - sve što je potrebno za reprodukciju u lokalnom okruženju
- Ako se koristi tuđi kod sa interneta — obavezno navesti izvor.
- **Obavezni koraci**:
  - Vizuelno prikazati podatke u 2D ili 3D.
  - Napraviti modele sa **svim atributima** i sa **različitim redukovanim
    skupovima atributa**, pa ih uporediti (npr. pun skup 10935 atributa vs.
    PCA/varijansa-bazirana redukcija).
  - Kod OVA_Breast nema tekstualne kolone (nema `tags` ili slično) — ova
    stavka iz uputstva se ovde ne primenjuje direktno, ali treba to
    eksplicitno napisati u tekstu rada (da je razmotreno i da se ne
    primenjuje).
  - Nema smisla više ciljnih atributa za klasifikaciju ovde (klasterovanje
    je zadatak, `Tissue` je jedini binarni atribut i koristi se samo za
    eksternu evaluaciju).

---

## 4. Metodološki plan (šta se konkretno radi)

### Preprocesiranje
1. Učitavanje `.arff` (scipy.io.arff ili liac-arff)
2. Uklanjanje `ID_REF` (identifikator, nije feature)
3. Provera nedostajućih vrednosti
4. Odvajanje `Tissue` labele (čuva se odvojeno, samo za evaluaciju)
5. Standardizacija (`StandardScaler`) — obavezno zbog raspona vrednosti
6. Redukcija dimenzionalnosti — najmanje dve varijante:
   - **Pun skup** (svih ~10935 atributa, standardizovanih)
   - **PCA** (npr. 50 komponenti)
   - opciono: feature selection po varijansi (top-K najvarijabilnijih gena),
     što je uobičajen pristup za gene expression podatke

### Vizuelizacija
- PCA 2D i 3D projekcija, obojena po `Tissue` (samo za vizuelnu referencu)
- opciono t-SNE/UMAP 2D za nelinearnu strukturu

### Klasterovanje — minimum 5 algoritama, na obe (ili više) varijante atributa
Predlog (po uzoru na sličan rad, prilagođeno):
1. K-Means
2. Agglomerative Clustering (Ward linkage)
3. Agglomerative Clustering (Complete linkage)
4. DBSCAN
5. BIRCH
- (opciono 6.: Gaussian Mixture Model ili Mean Shift, ako ima vremena)

Broj klastera (K) određuje se Elbow + Silhouette metodom pre K-Means/
Agglomerative/BIRCH.

### Evaluacija
- Interne metrike: Silhouette, Davies-Bouldin, Calinski-Harabasz
- Eksterna evaluacija (samo za tumačenje): poređenje klastera sa `Tissue`
  labelom (ARI, NMI, contingency tabela)
- Tabelarno poređenje svih kombinacija (algoritam × skup atributa)
- Heatmap / bar-chart poređenje

### Analiza i diskusija
- Kritička interpretacija (npr. da li visok Silhouette znači i koristan
  rezultat, ili je posledica malog broja klastera / mnogo šuma)
- Ograničenja: HDLSS problem, neuravnoteženost klasa, linearna ograničenja
  PCA
- Praktične implikacije i predlozi za dalji rad

---

## 5. Plan po danima (4 dana, solo rad)

**Dan 1 — Setup i preprocesiranje**
- Git repo setup, struktura foldera (`data/`, `output/`, `visualizations/`)
- Environment (venv, requirements.txt)
- Učitavanje `.arff`, osnovna statistika (broj instanci/atributa, raspodela
  `Tissue`)
- Uklanjanje `ID_REF`, provera nedostajućih vrednosti
- Standardizacija
- PCA/varijansa-bazirana redukcija (kreiranje 2+ skupova atributa)
- Snimanje `data_original` referenca (link/metapodaci, ne ceo arff) i
  `data_preprocessed.csv`
- Commit-i: `init`, `data-load`, `preprocessing`

**Dan 2 — Klasterovanje**
- Elbow + Silhouette za određivanje K
- Primena 5+ algoritama na pun skup atributa
- Primena istih algoritama na redukovan skup (PCA)
- Snimanje rezultata (labele klastera po modelu) u `output/`
- Commit-i: `full-features`, `reduced-features`, `algo-runs`

**Dan 3 — Evaluacija i vizualizacija**
- Računanje Silhouette/DB/CH za sve kombinacije
- 2D/3D vizualizacije (PCA obojen po klasterima i po `Tissue`)
- Tabela poređenja svih kombinacija, heatmap
- Eksterna evaluacija (ARI/NMI vs. `Tissue`)
- Commit-i: `metrics`, `visualizations`, `comparison`

**Dan 4 — Pisanje rada i finalizacija**
- Pisanje PDF-a (Uvod, Podaci i priprema, Metod, Rezultati, Diskusija,
  Zaključak) — struktura po uzoru na referentni rad (Farm-Ads primer)
- Ažuriranje Readme (uputstvo za pokretanje, struktura foldera, izvor
  podataka)
- Finalna provera reproducibilnosti (da li neko sa čistim environment-om
  može da ponovi ceo postupak)
- Commit-i: `report-draft`, `report-final`, `release` + `git tag v1.0`

Ukupno očekivano: **10–15 commit-a** kroz 4 dana.

---

## 6. Git workflow (solo, bez grana po članu)

Pošto radi jedan student, nema potrebe za `feature/*` granama i PR-ovima kao
u timskom projektu — radi se direktno na `main`, ali sa **čestim, malim,
opisnim commit-ima** (jedan commit = jedna logička celina), ne jedan
ogroman commit na kraju.

### Struktura foldera
```
OvaBreastClustering/
├── data/                  (preprocesirani CSV, NE sirovi arff)
├── output/                (rezultati klasterovanja, modeli .pkl)
├── visualizations/        (grafici .png)
├── notebook.ipynb         (kompletan kod)
├── .gitignore
├── Readme.md
├── requirements.txt
└── zapisnik.pdf           (tekstualni deo rada, dodaje se Dan 4)
```

### Commit konvencije
Prefiksi: `feat:`, `fix:`, `docs:`, `data:`, `viz:`
Primeri:
```
feat: ucitavanje arff i osnovna statistika
data: standardizacija i PCA redukcija (50 komponenti)
feat: KMeans i Agglomerative na punom skupu atributa
feat: DBSCAN i BIRCH na PCA skupu
viz: 2D/3D PCA projekcije obojene po Tissue labeli
feat: racunanje Silhouette/DB/CH za sve kombinacije
viz: heatmap poredjenja algoritama
docs: azuriran Readme sa uputstvom za pokretanje
docs: dodat zapisnik.pdf (tekstualni deo rada)
chore: cistenje repoa pred predaju
```

### Dnevne komande (jednostavne, bez rebase/PR)
```bash
git add <fajlovi>
git commit -m "prefiks: kratak opis"
git push
```

Na kraju Dana 4:
```bash
git tag v1.0
git push origin v1.0
```

---

## 7. Šta tražimo od AI asistenta

- Drži se strukture i redosleda iz plana po danima (sekcija 5).
- Vodi računa o HDLSS prirodi podataka (10935 atributa, 1544 instance) —
  ne predlagati pristupe koji ignorišu prokletstvo dimenzionalnosti.
- `Tissue` labela se ne sme koristiti kao ulaz u klasterovanje, samo za
  post-hoc evaluaciju.
- Ne verzionisati sirovi `.arff` fajl (>100MB, GitHub limit).
- Kod treba da bude reproducibilan: fiksiran `random_state` za sve
  stohastičke algoritme.
- Tehnički termini mogu ostati na engleskom (clustering, features, PCA,
  silhouette, itd.), objašnjenja idu na srpskom, pošto se predaje profesoru
  na srpskom jeziku.
- Tekstualni deo rada treba da prati strukturu referentnog primera (Uvod →
  Podaci i priprema → Metod → Rezultati → Diskusija → Zaključak), ali
  sadržaj mora biti prilagođen OVA_Breast podacima, ne kopiran iz primera.

---

## 8. Finalna commit istorija (kako je stvarno izvedeno)

Ovo je konkretna lista od 13 commit-a koju treba napraviti kroz 4 dana (u granicama
predviđenih 10-15):

```
Dan 1:
1. init: struktura projekta i gitignore
2. feat: ucitavanje arff i osnovna statistika
3. data: standardizacija i PCA/TopVar redukcija dimenzionalnosti
4. viz: 2D/3D PCA projekcije obojene po Tissue labeli

Dan 2:
5. feat: elbow i silhouette analiza za odredjivanje broja klastera
6. feat: KMeans i Agglomerative na Full, PCA-50 i TopVar-200 skupovima
7. feat: DBSCAN i BIRCH runovi, cuvanje svih rezultata klasterovanja

Dan 3:
8. feat: agregatno poredjenje algoritama i heatmap po skupovima atributa
9. viz: poredjenje punog i redukovanih skupova atributa
10. feat: analiza i vizuelizacija najboljeg modela, eksterna evaluacija
11. docs: cuvanje finalnog modela i rezultata evaluacije

Dan 4:
12. docs: dodat zapisnik.pdf (tekstualni deo rada)
13. docs: finalno azuriran Readme, cistenje repoa pred predaju

Tag: git tag v1.0 && git push origin v1.0
```

## 9. Ključni numerički rezultati (za referencu, dobijeni iz koda)

- Najbolji model: **Agglomerative Clustering (Complete linkage)** na **TopVar-200**
  skupu, Silhouette = **0.478**, Davies-Bouldin = 0.837, 10 klastera, 0 šuma
- DBSCAN na PCA-50 (eps=67.87): Silhouette = 0.358, 3 klastera, 76 šum-tačaka (4.9%)
- Redukcija dimenzionalnosti dosledno poboljšava Silhouette u odnosu na Full skup
  (npr. K-Means K=10: Full 0.074 → PCA-50 0.163)
- ARI/NMI vs. Tissue nizak kod svih modela (max NMI = 0.211) — klasteri ne prate
  direktno biološku Breast/Other podelu
