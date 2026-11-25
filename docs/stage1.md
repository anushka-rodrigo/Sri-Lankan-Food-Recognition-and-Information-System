# Project Notes: Sri Lankan Food Recognition and Information System

Last updated: 25 Nov 2026 (after the second image-cleaning phase)

A plain-language record of what we planned, what we built, what we found, and what is next.

---

## 1. What are we building?

A system where a user takes or uploads a photo of a Sri Lankan food. A deep learning model says which food it is, and the app then shows its **ingredients, a recipe and approximate nutrition**.

```
photo → resize/normalise → model → class probabilities → top prediction → look up food info → show result
```

The model only predicts the food's name. The recipe and nutrition come from a separate lookup table (one row per food).

This is the mini project for **CCS 3572 | CSE 3582 – Deep Learning**, Faculty of Computing, USJ. Group of 4.

---

## 2. What the assignment asks for

- **Report:** 5-7 pages with a title page (names and IDs), an abstract (max 200 words), problem statement, dataset and preprocessing, model and training details, results and discussion, and conclusion and future work.
- **Code:** a Jupyter notebook or Python script uploaded to the LMS, with file names starting `<studentID>_`.
- **Presentation:** 8-10 slides, 8 minutes plus 2 minutes of questions.
- **Marking (100):** code runs without errors 20, correct preprocessing 15, model architecture 15, training and evaluation 20, visualisation and metrics 10, report and presentation 20.
- The sample ideas include image classification with CNNs. Expected outputs are a trained model, a confusion matrix, and accuracy/loss graphs.

---

## 3. Our setup

- Training on a personal machine (Windows, 16 GB RAM, RTX 2050, i5), not Colab.
- Conda environment `food-cnn`, working in VS Code notebooks.
- **Framework: PyTorch.** TensorFlow dropped native Windows GPU support after version 2.10, while PyTorch uses the GPU on Windows directly.
- Our lecturer confirmed that **pretrained models are allowed**.

---

## 4. The 9 classes

| Class name (in code) | Notes |
|---|---|
| dosa | thosai |
| hoppers | |
| string_hoppers | |
| idli | |
| kottu | |
| milk_rice | kiribath |
| paratha | decide if this means Indian paratha or Sri Lankan parotta |
| pittu | some images are Kerala-style puttu |
| rice | plain rice and rice-and-curry together in one class |

**Vada was dropped** from the project.

---

## 5. The story of our image data

### Step 1: collecting
Images came from public datasets plus a few manual downloads. About **2,665 raw images** were scanned in the first full cleaning run:

| Class | Raw images |
|---|---|
| dosa | 337 |
| hoppers | 281 |
| idli | 347 |
| kottu | 169 |
| milk rice | 258 |
| paratha | 123 |
| pittu | 359 |
| rice | 633 |
| string hoppers | 158 |

### Step 2: the big surprise
After the first duplicate check, we learned that many datasets (especially Roboflow exports) contain several augmented copies of one photo: rotated, flipped, brightened, cropped, tinted. For example, every dosa group had exactly 10 versions of one photo. So the real number of **distinct photos** was much smaller than the file counts.

- 2,003 images were in duplicate groups, and **1,497 duplicates were removed**.
- Every flagged image was a Roboflow file. Dosa had 32 groups of 10, idli had 33 groups of 10, and kottu had 28 groups of 6. The two kottu downloads overlapped almost completely.
- No false merges between different classes remained after we tightened the matcher.

### Step 3: the first cleaned set (1,168 images)

| Class | Unique photos |
|---|---|
| dosa | 49 |
| hoppers | 156 |
| idli | 50 |
| kottu | 31 |
| milk rice | 143 |
| paratha | 114 |
| pittu | 249 |
| rice | 293 |
| string hoppers | 83 |

Dosa, idli and kottu were far too small. A looser "audit" pass over this cleaned set found only 36 groups (78 images) of possible leftover copies, mostly pairs, at most about 3.6% of the data. They are kept and share a group ID, so the train/test split will keep them together.

### Step 4: collecting more for the weak classes
We found a new dosa/idli dataset and about 40 new kottu images, then ran a second cleaning phase on just those three classes. **Current counts (total 1,371):**

| Class | Unique photos now |
|---|---|
| dosa | 181 |
| hoppers | 156 |
| idli | 86 |
| kottu | 66 |
| milk rice | 143 |
| paratha | 114 |
| pittu | 249 |
| rice | 293 |
| string hoppers | 83 |

The rule of thumb we used: ~60 is the minimum for kottu and ~80 for dosa/idli, and about 100+ per class is comfortable. We now meet the minimums. Kottu (66) vs rice (293) is about 4.4 to 1, which we'll handle with class weights and caps.

### Things we noticed about the data
- **Rice:** about 127 of 293 images come from three phone-photo sessions of the same red bowl and plate setup (dates 2024-09-02, 09-03 and 09-10). There are also ~38 images from a Malay/Indonesian "nasi" dataset. Risk: the model learns "red bowl = rice" instead of what rice looks like.
- **Pittu:** includes Kerala-style puttu.
- **Dosa/idli:** the new datasets contain mixed platters (dosa, idli and vada in one photo). These have ambiguous labels.
- **Cropped or tinted variants** from Roboflow are the same photo, so only one is kept.

---

## 6. How the duplicate check works (simple version)

The goal is to find images that are the *same photo* even if resized, rotated, flipped, brightened or cropped, without checking thousands by hand.

1. **Fingerprints.** Each image is shrunk and turned into two short "perceptual hash" fingerprints (they capture the rough layout of light and dark, not exact pixels). Brightness is evened out first, so brighter or darker copies still match.
2. **Orientation.** Each image is fingerprinted in all 8 rotations and flips, so a rotated copy still matches.
3. **Strict rule.** Two images are "the same photo" only if **both** fingerprints are close **and** their colour balance is similar (this stopped white rice and white milk rice from matching, and a milk rice mound on a green leaf from matching rice on a white plate).
4. **Roboflow names.** Files like `idli18_jpg.rf.xxxx` come from the same source photo, so files with the same name before `.rf.` in the same folder are grouped too.
5. **Groups.** All matches are merged into groups. The biggest image in each group is the default "keeper"; the rest are dropped.
6. **Conflicts.** A group that mixes two classes is a "conflict". By default all its images are dropped (mixed platters are ambiguous anyway), and you can rescue good ones by hand.
7. **Review sheets.** The notebook makes contact sheets (a row of thumbnails per group) so reviewing is quick.
8. **Cleaned copies.** Survivors are copied to `imgs_cleaned/` with clean names (`dosa_0001.jpg`), converted to RGB JPEG, with phone rotation applied. `imgs_raw/` is never modified.

Settings used: hash distance ≤ 6, colour difference ≤ 0.08, minimum image side 100 px. The audit pass uses looser values (10 and 0.2) on purpose, so it over-reports and we can look at what it finds.

---

## 7. Folders, notebooks and logs

```
Sri-Lankan-Food-Recognition-and-Information-System/
├─ data/
│  ├─ imgs_raw/        original images, never edited (not in GitHub)
│  ├─ imgs_cleaned/    cleaned images (not in GitHub)
│  ├─ imgs_split/      train / val / test (later; not in GitHub)
│  └─ food_info/       recipes, ingredients, nutrition (in GitHub)
├─ logs/
│  ├─ dedup/           master logs (manifest, removed log, duplicate groups)
│  ├─ dedup_r1/        archive of the first run
│  └─ dedup_r2/        outputs of the second run (dosa, idli, kottu only)
├─ notebooks/
│  ├─ cleaning_duplicates.ipynb      first full run (all classes)
│  ├─ cleaning_duplicates_v2.ipynb   second run (dosa, idli, kottu only)
│  └─ audit_duplicates.ipynb         looser check on the cleaned set
└─ README.md
```

- **manifest.csv:** one row per cleaned image, with class, source, group ID and original path. The split will be built from this.
- **removed_log.csv:** every image removed and why.
- **dup_groups.csv:** the duplicate groups and the keep/drop decisions.
- Images are **not committed to GitHub** (too big). They're shared via a Drive link written in the READMEs. `data/imgs_raw/` and `data/imgs_cleaned/` each have a README describing contents, sources and known issues.
- `.gitignore` ignores the images and keeps the small CSVs in `logs/dedup/` and the READMEs. Group IDs from the second run are text like `v2_17`, so they never clash with the first run's numbers.

---

## 8. The nutrition / food-info dataset

A teammate is building it (the leader isn't touching it until he finishes). It is **not images**, only a small lookup table with one row per food.

**Issues found, and what to fix** (most important first):
1. Paratha is whole-wheat Indian paratha but our images may be parotta, so the food variants must match the images.
2. Only 5 foods had reported nutrition values (rice, paratha, kottu, dosa, idli). The other 5 (hoppers, string hoppers, pittu, milk rice and vada) were modelled estimates, so label them "estimated".
3. Rice-and-curry has no nutrition, since only plain rice is covered.
4. There are ingredients but no cooking steps. Write short steps in our own words.
5. Class names differ (`kiribath` vs `milk_rice` and so on), so we need one mapping table.
6. Serving sizes are inconsistent (1 cup, 1 dosa, quarter of a recipe), and all per-100g values are missing. Pick one basis.
7. Kottu calories look too low, and idli fat is blank (unknown must be "N/A", never 0).
8. Values were copied from third-party pages and not verified against a food database. Some recipes aren't Sri Lankan style.
9. Reuse terms are unchecked, so keep a sources list for the report.

**Target output:** `data/food_info/food_lookup.csv` with 9 rows (vada removed) and columns like `class_name, display_name, ingredients, steps, serving_description, serving_g, kcal, protein_g, carbs_g, fat_g, fiber_g, source, status` where status is `reported`, `estimated` or `unknown`. The app reads only this file.

---

## 9. Decisions made so far

- 9 classes, vada dropped.
- Canonical lowercase class names (table in section 4).
- Pretrained models allowed. **Main model = fine-tuned pretrained network; from-scratch CNN = baseline for comparison.**
- Framework: PyTorch.
- Evaluate with **grouped, stratified cross-validation**, since small classes make a single test set too noisy. Report macro-F1 and per-class recall, not just accuracy.
- Keep duplicate groups and photo sessions together in the split, so nothing leaks between train and test.
- Cap rice and pittu sessions (about 15 images per session) so one photo shoot can't dominate.
- Handle imbalance with a class-weighted loss or balanced sampling, plus stronger augmentation on small classes.
- App shows the top-3 guesses with confidence and says "not sure" when confidence is low.
- Don't treat nutrition data as verified, and say so in the report.

---

## 10. Modelling plan (the "experiment ladder")

1. **Frozen pretrained model + simple classifier.** Run every image through a pretrained network once, save the "embeddings", and train a simple classifier on them. Fast, hard to overfit, and a strong first baseline.
2. **Partial fine-tuning:** unfreeze the last block or two at a low learning rate.
3. **Full fine-tuning** with a lower learning rate for early layers.
4. **From-scratch CNN** as a baseline (expected to be the weakest, and that gap is itself a finding).

Candidate backbones (pick 2-3): MobileNetV3 or EfficientNet-B0 (small, fast), ResNet-50, and a CLIP-style model used for embeddings.

Extras we planned:
- **Label-noise finder:** use cross-validated predictions to list images whose predicted class disagrees with their folder, so you only check those by hand.
- **Learning curve:** train on 25, 50, 75 and 100% of the data and plot accuracy per class, to show where more data would help.
- **Grad-CAM heatmaps:** check that the model looks at the food, not the plate or cloth.
- **Test-time augmentation** and model averaging for cheap gains.

---

## 11. What "normalisation" means here

We don't rewrite the image files. At load time the code resizes each image to a fixed size (224×224 for most pretrained models), scales pixels to 0-1, and then applies the pretrained model's own mean and standard deviation. Augmentation (crops, flips, small rotations, mild colour changes) is applied to **training images only**.

---

## 12. To-do list (in order)

- [ ] Run the audit notebook on the new cleaned set and handle leftover groups
- [ ] Skim the new dosa, idli and kottu images for mixed platters, non-food and weird artifacts (a thumbnail-grid helper will make this quick)
- [ ] Update counts in both READMEs; back up `imgs_cleaned` to Drive; push to GitHub
- [ ] Agree a **data freeze date** with the group
- [ ] Build the master table (file, class, source, group, session), merging audit groups and capping sessions
- [ ] Design and build the split (last, because the cleaned filenames are renumbered on every rerun)
- [ ] Build the input pipeline (resize, normalise, augment)
- [ ] First experiment: frozen embeddings, learning curve, label-noise list
- [ ] Fine-tune models; train the from-scratch baseline
- [ ] Evaluation: confusion matrix, per-class metrics, misclassified examples, Grad-CAM
- [ ] Nutrition teammate finishes `food_lookup.csv`; link it to predictions
- [ ] Optional small demo app
- [ ] Report, slides, rehearsal; name files `<studentID>_...`; upload to the LMS

---

## 13. Problems we hit (and what we learned)

- **File counts lie.** One photo can appear 10 times in a dataset. Always count unique photos.
- **The first matcher merged different foods** (rice vs milk rice vs hopper). A white blob on a plate looks the same to a coarse fingerprint. Fixes: require two fingerprints to agree, add a colour check, and lower the threshold.
- **Idli is the hardest for the matcher.** Round white idlis on dark plates look alike even in different photos. Mixed platters also appear under both dosa and idli.
- **A missed duplicate is cheap, a false merge is expensive.** For small classes, lean strict, and let the looser audit catch leftovers.
- **Notebook mix-ups:** cells pasted into the wrong slot, and an old Cell 5 that would have wiped every class's cleaned folder. Always check the first line of each cell before running.
- **A restarted run needs fresh scans.** Cell 5 uses paths recorded in Cell 2, so if folders are renamed or moved afterward, rerun Cells 1-4 first.
- **Don't move the master logs.** Moving `logs/dedup` made the merge step fail. Copy backups instead, and keep the archive in `dedup_r1`.
- **The Windows terminal needs the right kernel.** Select `Python (food-cnn)`, and run notebooks from anywhere, since the code finds the project root itself.

---

## 14. Risks and limitations to mention in the report

- Small classes (kottu 66, idli 86), so results for them are noisy.
- Possible leftover near-duplicates, handled by shared group IDs in the split.
- Photo-session and source bias (rice), and datasets with their own style.
- Some variants differ from Sri Lankan versions (pittu, paratha).
- Single dish per photo is assumed. Mixed platters will confuse the model.
- The nutrition data is approximate, partly estimated, and not lab-verified.
- Images come from public datasets and web sources for educational use only. Credit every source.

---

## 15. Glossary

- **Augmentation:** randomly changing an image (flip, rotate, crop, brightness) to create variety.
- **Near-duplicate:** the same photo with small changes.
- **Perceptual hash:** a short fingerprint of how an image looks, so similar-looking images get similar fingerprints.
- **Roboflow:** a platform whose dataset exports often include augmented copies of each photo.
- **Transfer learning:** starting from a model trained on millions of images and adapting it to our foods.
- **Fine-tuning:** continuing to train a pretrained model on our data.
- **Embedding:** a list of numbers a pretrained model produces to describe an image.
- **Cross-validation:** splitting the data into several folds and training and testing multiple times, then averaging.
- **Macro-F1:** an average score across all classes that treats small classes equally.
- **Leakage:** when copies of the same photo end up in both train and test, giving fake accuracy.
- **Shortcut learning:** a model learning the background instead of the food.
- **Grad-CAM:** a heatmap showing which part of an image the model used.
- **Curated / compiled dataset:** data selected, cleaned and combined from several sources, with sources documented.
- **Normalisation:** rescaling pixel values the way the model expects (done at load time, not in the files).