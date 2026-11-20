# Cleaned Image Data

This folder holds the **cleaned** food images used to train and evaluate the classifier. It is produced from `data/imgs_raw/` by `notebooks/cleaning_duplicates.ipynb` (Cells 1-5). The images are **not committed to GitHub** because of their size (see `.gitignore`). Regenerate them from the raw data (see `data/imgs_raw/README.md`) or download them from: **[Google Drive / OneDrive link]**.

> Do not edit or add images here by hand. Cell 5 rebuilds the class folders from scratch on every run, so manual changes will be lost. To add or remove images, change `imgs_raw/` and rerun the notebook.

## Structure

```
data/imgs_cleaned/
├─ dosa/
├─ hoppers/
├─ idli/
├─ kottu/
├─ milk_rice/
├─ paratha/
├─ pittu/
├─ rice/
├─ string_hoppers/
└─ README.md
```

One folder = one class label, using lowercase canonical names (matching `data/food_info/`). Files are renamed `<class>_<number>.jpg` (e.g. `dosa_0001.jpg`). **The numbering changes whenever the notebook is rerun**, so never hard-code filenames. Use `logs/dedup/manifest.csv` to trace any file back to its original.

## Contents (as of [date])

| Class | Images | Sources |
|---|---|---|
| dosa | 49 | original dataset |
| hoppers | 156 | existing dataset 81, new dataset 75 |
| idli | 50 | original dataset |
| kottu | 31 | existing dataset 28, new dataset 3 |
| milk_rice | 143 | existing dataset 72, new dataset 71 |
| paratha | 114 | existing dataset 113, new dataset 1 |
| pittu | 249 | original dataset |
| rice | 293 | original dataset (plain rice and rice with curry in one class) |
| string_hoppers | 83 | existing dataset 20, new dataset 63 |
| **Total** | **1,168** | |

Vada was removed from the project, so there are 9 classes.

## What was done

1. Every image was scanned for corruption and tiny size (under 100 px): none were found.
2. Exact and near-duplicates (copies that differ in resize, crop, rotation, flip or brightness, including Roboflow-augmented versions) were grouped across all sources. One image was kept per group, and **1,497 duplicates were removed** (`logs/dedup/removed_log.csv`).
3. Images were converted to RGB JPEG (quality 95), with phone rotation tags applied so orientation is upright.
4. A looser audit pass over this folder found 36 groups (78 images) of possible leftover copies, which are mostly pairs. They are kept, and share a group ID so the train/val/test split keeps them together.

## Related files

| File | Purpose |
|---|---|
| `logs/dedup/manifest.csv` | each cleaned file with its class, source, group ID and original path |
| `logs/dedup/removed_log.csv` | every removed image and the reason |
| `logs/dedup/dup_groups.csv` | duplicate groups and the keep/drop decisions |

## Known limitations

- **The counts are unique photos, but diversity is uneven.** Roughly 40% of `rice` comes from three phone-photo sessions of the same bowl and plate setup. Sessions will be capped at split time.
- **Small classes:** kottu (31), dosa (49) and idli (50) have few unique photos. More images are being collected.
- Near-duplicate photos of the same dish (burst shots, different crops) can remain. Group IDs are used so they never end up on both sides of the train/test split.
- Some classes use a specific variant (for example, `pittu` includes Kerala-style puttu, and `paratha` may differ from Sri Lankan parotta). Document this in the report.
- Images come from public datasets and web sources used for educational purposes only. See `data/imgs_raw/README.md` for attribution.