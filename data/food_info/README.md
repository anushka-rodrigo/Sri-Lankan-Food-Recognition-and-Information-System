# Sri Lankan food lookup

The app reads [food_lookup.csv](data/food_lookup.csv), with one row per class. Join on the exact case-sensitive `class_name` and show `display_name` to users. If a training or inference pipeline uses the cleaning notebook's folder labels, map them at the prediction boundary.

| Cleaned folder label | Lookup class_name |
| --- | --- |
| dosa | Dosa |
| hoppers | Hoppers |
| idli | Idli |
| kottu | Kottu |
| milk_rice | Milk Rice |
| paratha | Parata |
| pittu | Pittu |
| rice | Rice |
| string_hoppers | String Hoppers |

Nutrition is per 100 g of the cooked reference dish. Ingredient quantities describe the recipe batch. See the [nutrition methodology](explanations/Nutrition_Methodology.md), [recipe notes](explanations/Recipe_Data_Notes.md), and [source credits](explanations/sources_credits.csv).
