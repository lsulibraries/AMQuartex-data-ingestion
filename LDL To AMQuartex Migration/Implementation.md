```py
def download_mods_rdf_objs():
    # Download MODS, RDF, OBJ datastreams for all objects in LDL
    # Save under /institution/collection/Data/{MODS,RDF,OBJS}
def run_xml2csv_mapping_pipeline():
    # Run pipeline steps 1-3 using mapping CSV (Elisa) to extract mapped metadata
    # Outputs a cleaned mapped CSV for each collection with metadata per PID
def clean_mapped_csv():
    # Clean CSVs:
    # - Trim whitespace
    # - Normalize casing
    # - Fix invalid characters
    # - Convert/validate dates
    # - Standardize fields for ingestion
def generate_relationship_pid_csv():
    # From accounting CSVs:
    # - Extract PID, parent PID, collection PID, titles, relationship flags
    # - Normalize PIDs (`:` -> `-`)
    # - Create clean relationship CSV
def merge_metadata_with_relationships():
    # Merge cleaned XML2CSV metadata CSV with the relationship CSV
    # Inject relationship columns into the metadata for each PID
def normalize_pids_and_filenames():
    # For ingestion CSV:
    # - Normalize all PIDs to replace `:` with `-`
    # For OBJ files:
    # - Rename OBJ files to match normalized PID (and remove extraneous elements from names)
def upload_objs_to_quartex():
    # Upload renamed OBJ files to Quartex uploader or FTP
    # No folder structure needed; ensure all files are present for CSV references
def upload_objs_to_quartex():
    # Upload renamed OBJ files to Quartex uploader or FTP
    # No folder structure needed; ensure all files are present for CSV references
def batch_ingestion_csvs_by_institution():
    # For each institution:
    # - Combine all ingestion-ready CSVs across collections
    # - Split into chunks of 2,000 rows
    # - Save as ingest_batch_001.csv, ingest_batch_002.csv, etc. under the institution folder
def validate_ingestion_csv_and_files():
    # Check:
    # - All PIDs in CSV match uploaded files
    # - All required fields populated
    # - Correct PID/filename alignment
def verify_quartex_ingestion():
    # Confirm objects, relationships, and metadata appear correctly on Quartex
    # Promote and publish to live site as needed
```
