# Quartex Data Cleaning and Metadata Post-Processing Documentation

This document outlines the current data cleaning pipeline used to prepare MODS-mapped metadata (`step4-output_4.csv`) for ingestion into AM Quartex. It includes:

- Script logic overview
- Compliance mapping against Quartex import specifications
- Gaps or improvements still needed

------

## **Cleaning Workflow Summary (Script: `xml2csv-postprocessing-clean.py`)**

### Step 1: Load and Initialize

- Input: `step4-output_4.csv`
- Output: `step5-cleaned.csv`
- Uses `pandas` and `numpy` to perform column-level cleaning.

------

### Step 2: Text Cleaning & Normalization

- Clean all string fields using `clean_text`:
  - Remove non-ASCII characters (replaces corrupted encodings or special chars).
  - Strip whitespace.
- Normalize all column names:
  - Lowercase
  - Replace spaces and invalid characters with underscores

✅ **Meets Quartex requirements for UTF-8 encoding and formatting compatibility**

------

### Step 3: Remove Duplicate PIDs

- Drops duplicate rows based on the `pid` column
- Ensures one record per digital object

✅ **Supports uniqueness needed for asset-level metadata**

------

### Step 4: Auto-Detect and Convert Field Types

Automatically detects and converts:

- Numeric fields (e.g. counts, year values) ➝ `int`
- Date fields (based on `%Y-%m-%d` format match ratio) ➝ `datetime`
- Float columns with whole values ➝ `int`

✅ **Supports valid Quartex date format (YYYY-MM-DD and variations)**

------

### Step 5: Handle Nulls

- For numeric and text columns:
  - Replace empty strings (`""`) with `np.nan`
- If >70% of values are missing:
  - Optionally drop or impute

✅ **Prevents blank required fields from being overlooked**
🟨 But Quartex *requires* populating certain fields (e.g. Title, Rights), which must be explicitly validated

------

### Step 6: Convert Low-Cardinality Object Columns to Categories

- Converts any object column with low unique count (<10%) to `category` type
- Optimizes memory and potentially enhances Quartex filtering performance

✅ **Supports Quartex recommendation to optimize filter fields**

------

## ✅ **How the Script Aligns with AM Quartex Cleaning Criteria**

| Quartex Criteria                                 | Script Handles? | Notes                                                      |
| ------------------------------------------------ | --------------- | ---------------------------------------------------------- |
| CSV Format & UTF-8 Encoding                      | ✅               | Cleaned text, normalized characters                        |
| One Record Level per File                        | ✅               | Your CSV is asset-level only                               |
| File Size <10MB                                  | ⚠️ Manual        | No automatic file splitting yet                            |
| Date Formats (`YYYY-MM-DD`, etc.)                | ✅               | Uses `pd.to_datetime()` and detects valid formats          |
| Required Fields Populated                        | ❌               | Cleans, but does not *enforce* presence of required fields |
| No Number-Only Titles                            | ❌               | Not enforced in logic                                      |
| Unique Titles                                    | ❌               | Not validated                                              |
| Delimiters for CV Fields                         | ❌               | No logic to validate semicolon/pipe consistency            |
| Filter Field Optimization (<500 values)          | ✅               | Converts low-uniques to `category`                         |
| Parent-Child Hierarchy via Filename              | ❌               | Not enforced in logic                                      |
| Valid Filename Fields for Asset Linking          | ❌               | Not validated                                              |
| Promote/Indexed Fields                           | ⚠️ Manual        | Not handled via script—configurable in UI                  |
| Field Grouping & Configuration                   | ⚠️ Manual        | Controlled externally                                      |
| Supported Metadata Standards (Dublin Core, etc.) | ⚠️ Manual        | Field mapping assumed done prior                           |



------

# Quartex Rules **Not Yet Implemented in Script**

These items should be manually reviewed or added to future versions:

### Not Enforced (but critical)

- **Ensure `title` includes at least one letter**

- **Ensure required fields (title, rights, etc.) are not empty**
- **Enforce no numeric-only titles (e.g. “1960” ➝ “1960 [Photo]”)**
- **Validate that filenames (asset/item/section) match rules (e.g., no extensions, unique)**

### Partially Applicable

- No logic exists to:
  - Check if semicolons or pipes are used consistently in controlled vocab fields
  - Validate unique title naming across rows
  - Flag invalid field types (e.g., controlled vocabulary not assigned)

------

## Next Steps

| Task                      | Suggestion                                                   |
| ------------------------- | ------------------------------------------------------------ |
| Required Field Check      | Add logic to raise warnings if title/rights are blank        |
| Title Format Enforcement  | Add regex check: title must contain at least one letter      |
| CV Delimiter Validation   | Check if all multi-value fields use consistent delimiters    |
| File Size Check           | Auto-split file if >10MB                                     |
| Date Range Format Check   | Ensure date ranges use en-dash, not em-dash                  |
| Metadata Standard Mapping | Ensure field names are mapped to DC/MARC if publishing to aggregator |

------

## ✅ Final Output

- A cleaned CSV ready for ingestion (`step5-cleaned.csv`)
- Conforms to most Quartex import criteria
- Modular cleaning logic that can be extended for future validations
