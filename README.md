# GlueScore: An Interpretable Glue-Likeness Scoring Pipeline

A six-step, reproducible pipeline for building and validating GlueScore, an interpretable,
descriptor-based score that ranks candidate molecules by resemblance to known molecular glues.
Each step is a separate Colab notebook; every step (after Step 1) loads the previous step's
saved output from Google Drive and saves its own output for the next step to use.

## Step 1: Dataset Construction

Starting from `MolGlueDB_full.csv`, a curated set of known molecular glues and a comparable
drug-like background set were assembled, with class labels assigned (glue = 1, background = 0)
for downstream analysis.

## Step 2: Data Cleaning and Standardization

Both datasets were cleaned and standardized using a consistent procedure, structure
normalization, duplicate removal, and exclusion of invalid or non-comparable molecules, so
that only usable, comparable structures carried forward.

## Step 3: Feature Generation

Standard 2D physicochemical descriptors capturing size, polarity, hydrogen bonding,
flexibility, and ring characteristics were computed for every molecule, giving both classes an
identical feature set to be compared on.

## Step 4: Chemical Space Characterization and Descriptor Selection

The two classes were compared descriptively and statistically to identify which descriptors
separated glues from background, and the descriptor set was progressively narrowed through
significance filtering and correlation screening down to 7 final, non-redundant descriptors.

## Step 5: Development of the GlueScore Formula

Using only a training split (80%) of the final dataset, each descriptor was standardized, its
separation between glues and background quantified, and combined into a weighted formula:

```
GlueScore(x) = sum over j of [ Weight_j * Sign_j * z_j(x) ]
```

The fitted scaler, weight table, and both splits were saved for use in Step 6. Training-set AUC
(a fit check, not a validation result): **0.862**.

## Step 6: Validation and Supporting Analysis

The fixed formula from Step 5 was applied to the held-out test set, molecules never used to
build it, giving an honest test of generalization. Results were also examined by IMiD vs.
non-IMiD subgroup, and benchmarked against supporting logistic regression and random forest
models trained on the same data.

| Metric | Value |
|---|---|
| Test-set AUC (GlueScore) | 0.866 |
| Logistic Regression AUC | 0.888 |
| Random Forest AUC | 0.971 |
| Glues captured in top 25% of ranking | 49.5% |
| Glues captured in top 75% of ranking | 99.1% |

The close match between training and test AUC indicates no meaningful overfitting.
