# Checklist

Here’s a **simple 10-rule summary** you can keep in mind when naming things in Python.

1. **Use `snake_case` for files, variables, and functions**
   Example: `load_data.py`, `publication_year`, `filter_papers()`.

2. **Use `PascalCase` for classes**
   Example: `ClimateHealthClassifier`, `NetworkAnalyzer`.

3. **Use `UPPER_CASE` for constants**
   Example: `CITY_KEYWORDS`, `YEAR_MIN`, `REL_THRESHOLD`.

4. **Name functions with verbs (actions)**
   Example: `load_data()`, `filter_papers()`, `compute_metrics()`.

5. **Name variables with nouns (things/data)**
   Example: `papers_df`, `author_network`, `publication_year`.

6. **Use descriptive names instead of abbreviations**
   Prefer `mortality_impacts_df` over `mort_imp_df`.

7. **Keep names consistent across the project**
   Example: always use `_df` for DataFrames, `_path` for file paths.

8. **Use prefixes for booleans**
   Example: `is_valid`, `has_location`, `contains_keyword`.

9. **Avoid unnecessary words**
   Prefer `climate_health_df` over `climate_health_dataframe_object`.

10. **Make names readable like natural language**
    Code should read clearly:
    `filtered_df = filter_climate_health_papers(papers_df)`.

**One mental test:**
If someone reads your code aloud and it sounds like a sentence, your naming is probably good.

## Files/modules

- Use snake_case
- lowercase only
- underscores between words
- describe the module’s responsibility








```
data/
models/
analysis/
utils/

data_loader.py
text_preprocessing.py
ad_analysis_pipeline.py
network_analysis_utils.py

load_wmp_data.py
filter_climate_health.py
urban_keyword_filter.py
```
- Avoid

```
DataLoader.py
myScript.py
script1.py
analysisFINAL.py
```

## Functions

- Try to use verbs because they do something.

```
load_*
filter_*
compute_*
extract_*
build_*
create_*
validate_*
apply_*
```

```
load_dataset()
filter_climate_health_papers()
merge_mortality_impacts()
build_keyword_pattern()
apply_city_keyword_filter()
calculate_network_density()
```

## Variables
