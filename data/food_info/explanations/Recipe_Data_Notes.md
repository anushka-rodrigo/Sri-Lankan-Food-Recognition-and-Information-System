# Curated recipe metadata

Class aliases: map `milk_rice` and `milk rice` to canonical `kiribath`; `thosai` to `dosa`; `string hoppers` to `string_hoppers`. Never silently map masala dosa, stuffed paratha, medu vada or vada pav to these selected variants. The paratha row describes **whole-wheat Indian plain paratha**, which differs from the refined-flour Sri Lankan parotta/godamba-roti type; collect a separate variant before claiming Sri Lankan parotta nutrition.

The kottu publisher's 450 kcal per quarter-recipe may undercount the listed pound of roti plus three cups of chicken. Its calculator inputs require an independent audit; preserve it only as a source observation, with this caveat displayed. It is not a validated estimate for the listed ingredient batch.

`sri_lankan_food_master.json` and `.csv` contain one explicitly named variant for each target image class. This is metadata, not an image training dataset. The JSON preserves a quarantined raw nutrition observation for vada that the flat CSV intentionally does not expose as usable nutrition.

Ingredient quantities apply to the **whole recipe**, not one serving. Household units, ranges and alternatives are preserved; no unsupported gram conversions were introduced. Ingredient names and quantities are factual metadata; directions and source images are not redistributed. Recipe links were inspected on 2026-11-10 through publisher pages or indexed publisher content. BigOven pittu is lower-confidence community metadata and needs yield verification.

Five records contain the publisher's approximate recipe-card nutrition: rice, paratha, chicken kottu, plain dosa and idli recipe 1. These are **not laboratory measurements**, independently calculated results, or universal nutrition values for the broad food class. Kottu calculator treatment of optional cheese is unspecified. RecipeTin rice uses the card's 205 kcal, although article prose says 206. Unknown nutrients stay null, including idli fat.

Hoppers, string hoppers and kiribath pages did not report nutrition. Pittu nutrition was not verified. Vada's displayed 378 kcal and 17 g protein have an unclear serving basis relative to a ten-vada yield, so the raw observation is quarantined. Do not show that observation as nutrition for one vada.

All cooked serving weights and per-100-g fields are null because an edible cooked gram yield was not verified. Water, evaporation, drained soaking water and absorbed frying oil make summing ingredient weights unsuitable as cooked yield. A separate modelled scenario supplement may supply estimates with explicit ingredient profiles and yield assumptions; it must remain distinguishable from these source-reported records.


