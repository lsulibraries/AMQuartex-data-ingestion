# Quartex Metadata Preparation and Data Cleaning Checklist

This checklist combines mandatory metadata formatting requirements, data cleaning expectations, and recommended best practices drawn from Quartex’s [official Knowledge Base article](https://community.quartexcollections.com/browse/kb-toc) (April 29, 2025).

### **1. File Format and Encoding**

- Must be in CSV or TSV
- Recommended: use UTF-8 encoding, especially for multilingual or special characters

### **2. Single Record Level per File**

- Each metadata file must contain only one record level:
  - Asset
  - Item
  - Section
- Do not mix multiple record levels in a single CSV
- For compound objects, split into separate files by level

### **3. File Size Limits**

- Recommended maximum size: 10MB per file
- Larger files should be split into multiple files to ensure smooth processing

### **4. Filename Columns for Linking to Digital Content**

To associate metadata with actual files:

- Asset-level: include asset filename (without extension)
- Item-level: include both asset filename and item filename
- Section-level: include asset filename, start item filename, and end item filename
- File extensions such as .jpg or .pdf should not be included in any of these filename fields

### **5. Date Format Standards**

- All date fields must conform to Quartex-supported formats
- Multiple valid formats may be used in the same file
- Incorrectly formatted dates will be rejected during import
- Supported formats include:
  - DD/MM/YYYY (15/10/2012)
  - YYYY/MM/DD (2012/10/15)
  - Month DD YYYY (October 15 2012)
  - and several other accepted date variations and ranges
- Avoid MM/DD/YYYY format
- Use en-dash (–) for ranges (not em-dash)

### **6. Required Fields Must Be Populated**

- All fields marked as Required in the CSM must have values
- Title is the only default required Standard Field
- Required fields cannot be empty or the asset will be marked "Incomplete" and won't be promoted
- Item-level fields cannot be set as required

### **7. Asset Titles**

- Titles must contain at least one letter
- Numeric-only titles such as "1960" are not allowed
- Example of valid format: "1960 [Photo]"
- Recommended: make titles unique for clarity, accessibility, and search results

### **8. Controlled Vocabulary Fields**

- Use consistent delimiters to separate values within each field
  - Allowed delimiters: semicolon or pipe
- Each field must use the same delimiter internally
- Recommended: if the field is intended for front-end filtering, limit to 500 values
- For vocabularies with more than 500 terms, use Search Categories instead of filters
- Must assign vocabularies in "Advanced Options" before deletion

### **9. Parent-Child or Compound Object Handling**

- Hierarchies are inferred from filenames, not via a parent_id field
- Proper folder and filename organization is required for ingest
- PDFs should not be nested in subfolders (treated as single assets)

### **10. Additional Criteria for Section and Item Imports**

Section-Level Imports:

- Must include columns for:
  - start item filename
  - end item filename

Item-Level Imports:

- Must include:
  - item filename

### **11. Field Configuration and Management Requirements**

- Configure fields by level: Asset, Item, or Section
- Fields grouped into "Standard Fields" or custom field groups
- Only asset-level field groups can be assigned to collections
- Field types: Free Text, Date, Controlled Vocabulary, Related Assets
- Promoted fields are indexed and discoverable; non-promoted fields remain hidden
- Character limits and help text can be set per field
- Fields and field groups can be reordered but not moved across groups

### **12. Supported Metadata Standards**

- Use Field Clusters for:
  - Dublin Core (recommended for aggregation and OAI-PMH)
  - MARC21 (for published assets shared with library catalogs)
  - ISAD(G) (for archival collections)
- Each cluster includes predefined fields with types and requirements
- Only one field can be mapped to each DC field
- All fields default to promoted and can be toggled

### **13. Recommended Metadata Practices (Optional but Advised)**

Unique Titles for All Assets:

- Improves accessibility and site usability

OAI-PMH Aggregator Readiness:

- Consider mapping to Dublin Core fields for harvest compatibility

Filter Field Optimization:

- Limit sidebar filter fields (controlled vocabulary fields) to fewer than 500 terms for better performance and accessibility
- Use Search Categories to present larger vocabularies

## AM Quartex Cleaning Summary

| Criteria Category                | Required   | Notes                                   |      |
| -------------------------------- | ---------- | --------------------------------------- | ---- |
| CSV/TSV with UTF-8               | ✅ Yes      | Must format correctly for encoding      |      |
| One Record Level per File        | ✅ Yes      | Asset, Item, or Section only            |      |
| File Size <10MB                  | ✅ Yes      | Larger files should be split            |      |
| Filename Columns (No extensions) | ✅ Yes      | Needed per level                        |      |
| Valid Date Formats               | ✅ Yes      | Use supported formats only              |      |
| Required Fields Populated        | ✅ Yes      | Title, rights, etc.                     |      |
| Title Includes Letters           | ✅ Yes      | No number-only titles                   |      |
| Unique Titles                    | 🔶 Advised  | For accessibility & usability           |      |
| CV Delimiter Consistency         | ✅ Yes      | `;` or `                                | `    |
| Filter Field Term Limits (<500)  | 🔶 Advised  | Avoid filter overload                   |      |
| Dublin Core Mapping for OAI-PMH  | 🔶 Advised  | For aggregator compatibility            |      |
| Item/Section Import Filenames    | ✅ Yes      | Must define start/end or item filenames |      |
| Parent-Child Inference           | ⚠️ Inferred | Via filenames, not `parent_id`          |      |
| Field Configuration & Grouping   | ✅ Yes      | Required before metadata upload         |      |
| Promoted Field Toggle            | ✅ Yes      | For search and front-end visibility     |      |
| Field Types Set (Text, CV, etc.) | ✅ Yes      | Defined per field at config time        |      |
| Controlled Vocabulary Setup      | ✅ Yes      | Advanced tab config needed              |      |
| Asset Titles Unique              | 🔶 Advised  | Helps with URL and search readability   |      |