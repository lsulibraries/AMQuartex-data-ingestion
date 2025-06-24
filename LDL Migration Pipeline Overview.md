# Migration Pipeline Overview

This process is designed to ensure that all metadata, digital files, and structural hierarchies are properly extracted, transformed, and cleaned prior to ingestion into Quartex.

### Step-by-Step Workflow:

------

### **1. MODS / RDF / Object File Download**

**Goal**: Retrieve all necessary Fedora datastreams for ingestion into Quartex.

- Tools: Python scripts using SPARQL and cURL
- Datastreams downloaded:
  - `MODS` XML files (for metadata mapping)
  - `RDF` (for future relationships if needed)
  - `OBJ`, `PDF`, etc. (primary content)
- All downloaded content is organized by institution and collection.

------

### **2. Metadata Mapping via `xml2csv` (Step 1–3)**

**Goal**: Convert MODS XML to structured CSVs using librarian-provided mapping.

- Use custom Python scripts to:
  - Parse MODS XML paths (`XPath`)
  - Map XPath values and attributes to target field names
  - Output an initial merged ingestion-ready CSV (`step4-output_4.csv`)
- Validation:
  - Invalid XMLs, empty MODS, or malformed mappings are logged.

------
### 2.2 File names for relationship between files and Contens
-----
### **3. Metadata Cleaning & Normalization (`step5-cleaned.csv`)**

**Goal**: Clean and normalize metadata to align with Quartex data preparation checklist.

**Cleaning is performed using `xml2csv-postprocessing-clean.py` which implements the following:**

#### **a. Duplicate Removal**

- Drops duplicate rows based on `pid` (primary identifier)

#### **b. Column Name Normalization**

- Converts column headers to:
  - Lowercase
  - Snake_case
  - Removes illegal symbols (e.g., `$`, `&`, `@`, etc.)

#### **c. Value Cleaning**

- Applies `clean_text()`:
  - Removes non-ASCII characters
  - Trims leading/trailing whitespace

#### **d. Data Type Detection & Conversion**

- Detects and converts:
  - Dates → `datetime64`
  - Numbers → `int` or `float`
  - Integer-like floats → `int`
  - Controlled vocabularies with few unique values → `category`

#### **e. Missing Value Handling**

- Converts empty strings to `NaN`
- Tracks string-based nulls in `fix_nulls` list
- Flags columns with >70% nulls for further review (drop or fill)

------

### **4. Ingestion to AM Quartex**

**Goal**: Upload metadata and digital assets collection-by-collection or in 2000-object batches.

- Follows Quartex ingestion guidelines from their admin documentation
- Requires:
  - Proper folder hierarchy for assets (single vs. compound)
  - Valid CSV filenames (no extensions in filename columns)
  - Matching asset filenames with actual files
- Ingestion is monitored via the Quartex Uploader or FTP and status is tracked in the Quartex dashboard.

------

## 📋 AM Quartex Compliance Summary

| Requirement                                    | Status                    | Notes                                            |
| ---------------------------------------------- | ------------------------- | ------------------------------------------------ |
| UTF-8 CSV Upload                               | ✅ Implemented             | Files are saved in standard encoding             |
| One record level per file                      | ✅ Implemented             | Output CSVs are grouped per batch                |
| File size limits (<10MB)                       | ✅ Monitored manually      | Large CSVs are split                             |
| Filename columns (without extension)           | ⚠️ Manual enforcement      | Script doesn't enforce this yet                  |
| Required fields (title, rights, etc.)          | ⚠️ Needs validation        | Script flags nulls but does not enforce presence |
| Valid date formats                             | ✅ Implemented             | Auto-converted with fallback coercion            |
| Controlled vocabulary consistency              | ✅ Implemented             | Columns with few unique values set as `category` |
| Numeric-only title prevention                  | ⚠️ Not enforced yet        | Should validate presence of letters in title     |
| Alphanumeric sorting of files                  | ⚠️ Manual in folder setup  | Filenames not yet auto-padded                    |
| No subfolders in asset folders                 | ⚠️ Manual compliance       | Not checked in code                              |
| EXIF rotation stripping                        | ❌ Not implemented         | Requires image reprocessing tool                 |
| Required fields enforcement before ingest      | ⚠️ Partial                 | Only flagged, not enforced                       |
| Field-level promotion/indexing flags           | ❌ Not handled in cleaning | Must be done in Quartex admin                    |
| Mapping to DC/OAI fields for aggregator export | ❌ Not yet implemented     | Requires explicit mapping strategy               |



------

## Recommendations (To Implement)

To further align with AM Quartex standards:

1. **Add validation for title field**:
   - Ensure titles are not numeric-only.
2. **Ensure all required fields are not null**:
   - Validate and enforce `title`, `rights`, `reuse policy`, etc.
3. **Add support for filename sanitization**:
   - Enforce length limit, illegal character removal, and no extensions.
4. **Preview or validate asset folder structures**:
   - Check if asset folders match content in the `filename` columns.
5. **Check EXIF metadata if ingesting image files**:
   - Re-save images without EXIF rotation flags.

------

## Folder Naming & Migration Preparation

As noted in `Quartex Content Migration Checklist.md`:

- Single-file assets → directly in the parent folder
- Compound objects → one folder per object (no nested folders)
- Parent folder used for bulk uploads
- File ordering: use `Page001`, `Page002`, etc. for sequence
- Refrain from using characters like `"` or brackets in filenames

------

## ✅ Summary

Your current script does a solid job of cleaning and preparing mapped metadata for ingestion. It:

- Detects and fixes encoding
- Normalizes formats
- Cleans values and structures
- Converts data types for better filtering and indexing
- Flags nulls and prepares for batch ingestion
