# Sri Lankan Food Metadata Dataset

This package contains food data and explanations only. It covers rice, hoppers, string hoppers, pittu, kiribath, paratha, kottu, dosa, idli and vada, with one explicitly named reference variant per class.

## Files

| File in `data/` | Purpose |
|---|---|
| `sri_lankan_food_master.csv` | Main ten-food catalog: names, ingredients, whole-recipe quantities, recipe URLs, provenance and available publisher nutrition |
| `sri_lankan_food_master.json` | Structured version of the main catalog, also preserving quarantined source observations |
| `food_catalog.json` | Catalog with alternative illustrative nutrition scenarios embedded separately |
| `coverage.csv` | Data availability for each food |
| `nutrition_model_estimates.csv` | Five illustrative nutrition scenarios with explicit assumed yields and portions |
| `nutrition_model_inputs.json` | Ingredient nutrient profiles, source references, recipe assumptions and gram quantities used in those scenarios |

CSV and JSON representations are alternatives, not independent datasets to combine as additional rows. Start with the master CSV for spreadsheet use, or the food catalog JSON for application development. The two documents under `explanations/` explain recipe selection and nutrition methodology.

## Nutrition status

Five foods have approximate publisher-reported nutrition per recipe-defined serving. Five have separate hypothetical scenarios. These scenarios are not the linked recipes and must not be silently substituted for them. Source ingredient values in the scenarios still require direct primary-source verification. All validated cooked-weight per-100-g fields remain unavailable; illustrative per-100-g numbers are estimates based on assumed yields.

Missing values mean unknown, not zero. The idli fat value is unavailable. Vada publisher nutrition has an unclear serving basis and is quarantined. The kottu publisher calculator may undercount the listed ingredients. The plain wheat paratha reference differs from refined-flour Sri Lankan parotta, and the lentil-vada scenario uses a pulse proxy.

Ingredients and quantities describe reference recipes, not ingredients confirmed in a photographed meal. Side dishes and accompaniments are excluded unless specifically listed. Ingredient quantities refer to the whole recipe. A user-selected variant and cooked edible weight are needed for meaningful quantity estimates.

This is a research seed dataset, not a validated national food-composition database. Retain source attribution, confirm reuse terms, and verify nutrition before relying on it. Source recipe instructions and images are not included.
