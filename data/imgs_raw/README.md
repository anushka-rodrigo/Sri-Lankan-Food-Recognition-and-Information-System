\# Raw Image Data



This folder holds the \*\*original, unmodified\*\* food images used to train the classifier. The images are \*\*not committed to GitHub\*\* because of their size (see `.gitignore`). Download them from: \*\*\[Google Drive / OneDrive link]\*\*, then extract so the folder layout matches below.



> Rule: nothing in `raw/` is ever edited, renamed or deleted. All cleaning happens on copies in `data/clean/`.



\## Structure



```

data/raw/

├─ Dosa/

├─ Hoppers/

├─ Idli/

├─ Kottu/

├─ Milk Rice/

├─ Parata/

├─ Pittu/

├─ Rice/

├─ String Hoppers/

└─ Vada/

```



One folder = one class label. Folder names map to canonical class names in code/metadata:



| Folder | Canonical class | Notes |

|---|---|---|

| Dosa | dosa | Thosai |

| Hoppers | hoppers | Appa |

| Idli | idli | |

| Kottu | kottu | Some images manually downloaded from web search |

| Milk Rice | kiribath | Called `milk\_rice` in the image data |

| Parata | paratha | \[parotta / whole-wheat: decide] |

| Pittu | pittu | |

| Rice | rice | Plain rice AND rice-and-curry images (one class) |

| String Hoppers | string\_hoppers | Indiappa |

| Vada | vada | \[variant: e.g. parippu vadai] |



\## Contents (raw, before cleaning)



| Class | Approx. images | Source(s) |

|---|---|---|

| Dosa | \[337] | \[dataset name + link] |

| Hoppers | \[163] | \[ ] |

| Idli | \[347] | \[ ] |

| Kottu | \[112] | \[dataset] + 29 manual web downloads |

| Milk Rice | \[136] | \[ ] |

| Parata | \[122] | \[ ] |

| Pittu | \[359] | \[ ] |

| Rice | \[633] | plain \~426, rice and curry \~207 |

| String Hoppers | \[52] | \[ ] |

| Vada | \[TBD] | \[ ] |



Counts are updated as of \[date]. The numbers will drop after cleaning.



\## Known issues (to be handled in cleaning)



\- Class imbalance (some classes far larger than others).

\- Near-duplicate images (rotations, angles, lighting variants of the same photo) in some classes. These must stay together in one train/val/test split.

\- Some images contain several dishes, watermarks or low quality.

\- Mixed sources, so image style and resolution vary.



\## Licence and attribution



Images come from the public datasets and web sources listed above and are used for \*\*educational purposes only\*\* (University of Sri Jayewardenepura, Faculty of Computing, Deep Learning mini project). Credit the original dataset authors. Do not redistribute.

