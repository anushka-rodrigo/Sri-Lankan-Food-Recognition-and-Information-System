\# Sri Lankan Food Recognition and Information System



A deep learning mini project (CCS 3572 | CSE 3582 – Deep Learning, Faculty of Computing, USJ). A user uploads or takes a photo of a Sri Lankan food, a CNN identifies it, and the system shows the food's \*\*ingredients, a recipe and approximate nutrition information\*\*.



\## Classes (10)



Dosa · Hoppers · String Hoppers · Idli · Kottu · Milk Rice (Kiribath) · Rice (plain and with curry) · Pittu · Parata · Vada



\## How it works



```

photo → resize/normalize → CNN → class probabilities → top prediction → look up food info → show result

```



\- \*\*Model:\*\* CNN built and trained by us (TensorFlow/Keras or PyTorch: \[choose]), compared with a pretrained baseline (\[e.g. MobileNetV2]).

\- \*\*Food info:\*\* ingredients, recipe and nutrition come from a curated metadata table (`data/nutrition/`). The CNN only predicts the class. The information is looked up by class name.



\## Project structure



```

Sri-Lankan-Food-Recognition-and-Information-System/

├─ data/

│  ├─ raw/         original images (not in GitHub, see data/raw/README.md)

│  ├─ clean/       cleaned images (not in GitHub)

│  ├─ split/       train / val / test (not in GitHub)

│  └─ nutrition/   food metadata: ingredients, recipes, nutrition (in GitHub)

├─ notebooks/      cleaning, training, evaluation

├─ models/         saved model weights (if small)

├─ reports/        report, figures, slides

└─ README.md

```



\## Getting the data



1\. Download the image dataset from \*\*\[link]\*\* and extract it into `data/raw/`.

2\. The nutrition metadata is already in `data/nutrition/`.



\## Pipeline



1\. Collect and clean images (duplicates, wrong labels, low quality)

2\. Split into train / validation / test (no leakage)

3\. Preprocess: resize, normalize, augment (training only)

4\. Train a baseline CNN, then tune and compare with a pretrained model

5\. Evaluate: accuracy, per-class metrics, confusion matrix

6\. Connect predictions to food information



\## Limitations



\- One dominant dish per photo is assumed.

\- Nutrition values are approximate: some are recipe-card values, others modelled estimates. They are not laboratory values.

\- Metadata describes one reference variant per food, not every regional variation.



\## Team



\[Name – ID], \[Name – ID], \[Name – ID], \[Name – ID]



\## Credits



Image datasets: \[list]. Recipe and nutrition metadata sources are listed in `data/nutrition/README.md`.

