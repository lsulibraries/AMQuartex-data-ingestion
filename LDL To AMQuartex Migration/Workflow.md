# LDL to AM Quartex Ingestion Workflow

### 1. DOWNLOAD ALL ASSETS AND METADATA

For each **institution → collection**, download and organize:

- `MODS` XML files
- `RDF` files
- `OBJ` datastreams (e.g. `.pdf`, `.jp2`, `.jpg`)

```
Institution/
   Collection/
       Data/
          MODS/
          RDF/
          OBJS/
       Collection_PID.csv
```

---

### 2. CREATE INITIAL INGESTION CSV (XML2CSV Steps 1–3) 

Use `mapping.csv` and run XML-to-CSV scripts to extract MODS metadata.
 Each row = 1 digital object.

- Extract fields like `title`, `creator`, `type`, etc.
- Include `PID` column.

⚠️ This step **does not include relationship columns** yet. It only maps and flattens MODS values into target fields.

---

### 3. CLEAN THE MAPPED CSV:

Using Python cleaning script:

- Normalize column names
- Strip non-printables
- Convert types (dates, ints)
- Drop empty/duplicate rows
- Rename PID format from:
  `collection-name_object-number` → `collection-name:object-number`
---

## 4. PREPARE FILES FOR UPLOAD:

Perform Tasks Bellow to prepare folders containing digital assets to upload to AMQuartex ([Link to article](https://community.quartexcollections.com/blogs/rebecca-lynd1/2025/04/29/preparing-metadata-for-import).)

1. **Update item names:**

   - Must renamed and match `item_file_name` (`PID`s)
     - Remove `OBJ`, `PDF`, etc. from filename.
   - Asset **folder** and **filenames** must be **unique**
   - File name size Under **128** for all files except PDF and **120** for PDFs (AMQuartex recommends keeping filenames under **100** characters to be safe)
   - Avoid using **double quotation** (single quotation marks are fine)
   - **Example:** 
     - File Name:  `dcc-publications_332_OBJ.jpg` ➡ `dcc-publications_332.jpg`
     - PID Name:  `dcc-publications:332`➡  `dcc-publications_332` 

2. **Update Asset Folder names:**

   - 1st Level Folder would be `collection_pid` Or **Collection Name**
   - 2nd Level Folder(s) would be **compound** `PID`s 

3. **Note**

   - No more than 1 nested Asset folder is allowed in each collection directory.
   - All items associated to a compound or any nested compounds within that compound should be placed in 2nd level Compound folder(s)
   - Items (Files, e.g `PDF`, `jpg`, etc)can all be uploaded into a flat folder:

4. Overall Folder Structure format:

   ```
   /Collection/
     item001.jpg
     item002.pdf
     Compound/
       item003.jpg
       item004.jpg
   ```

   

---

## 5. UPLOAD TO AMQartex:

FTP and Quartex Uploader to **upload digital asset files (OBJs: PDF, JPG, JP2, audio, video, etc.)** into the AM Quartex platform **before** ingesting metadata via the ingestion CSV.

The ingestion CSV **references filenames** (not paths), and these files must already exist in Quartex for it to attach them during import:

1. **FTP Upload**
   - Uses an **FTP client** (like FileZilla)

   - Good for **bulk uploading large batches** of files

   - Ideal for institutions with **IT access and automation needs**

   - Uploads files to the Quartex cloud server

   - Less user-friendly; mostly file-drop based

2. **Quartex Uploader (Desktop App)**

   - A **desktop GUI application** provided by Quartex

   - Uses **HTTPS** instead of FTP

   - Easier for non-technical users
     - Allows **folder-based upload** of:
       - Simple assets (files like `.jpg`, `.pdf`)
       - Compound assets (folders containing page/image files)

   - Gives control over whether to overwrite existing assets

#### **Folder Structure in Upload:**

- **For compound upload:**

  - One folder per compound object

  - Folder name = PID of the asset

  - Files inside (not nested further)

- **For simple assets:**

  - Just place the `.jpg`, `.pdf`, etc. directly in the upload root
  - No folder needed

---

## 6. PREPARE METADATA CSV:

### 1️⃣ Normalize PIDs:

- Change the PID: 
  - `collectionName_objectNumber` ➡ `collectionName:objectNumber`
  - `dcc-publications:332`➡  `dcc-publications_332` 


### 2️⃣ Add Content Relationship Fields to Mapped CSV:

Update Mapped metadata csv and add required columns for `asset`/`item` relationship:

#### **Required Columns**

| Column Name       | Description                                                  |
| ----------------- | ------------------------------------------------------------ |
| `record_level`    | One of: `Asset`, `Item`, `Section`  \|  **⛔ DON'T KNOW IF WE HAVE SECTION LEVEL ITEMS** |
| `asset_file_name` | File name of the parent asset (no extension)                 |
| `item_file_name`  | File name of the child object (used for compound children)   |
| `collection_pid`  | The PID of the collection in `format: collectionname:collection` |
| `parent_pid`      | PID of the object’s parent (for items or nested compound structure) |
| `title`           | Title of the object (must include at least 1 letter, not just numbers) |
| Other fields      | Mapped MODS fields (e.g. description, type, date, etc.)      |

#### Asset/Item Objects explained:

1. `record_level`:

   - **Asset Compound objects:**

     - Object that is **member of collection** (Relationship label = `isMemberOfCollection`) And **Compound Data Type** (`BookCmodel`, `collectionCModel`)

     - Object that is **Member of another object**  (Relationship label = `isMemberOf` or `isConstituentOf`) And **Compound Data Type** (`BookCmodel`, `collectionCModel`)

   - **Item, Child Objects (for instance, an illustration or diagram within a book):**

     - Object that is **member of collection**  (Relationship label = `isMemberOfCollection`) **And** **Objects** **Data type**.

     - Object that is **Member of another object**  (Relationship label = `isMemberOf` or `isConstituentOf`)  **And** **Objects** **Data type.**

2. `asset_file_name`
   - `record_level = Asset` ➡ `parent_PID` (Normalized)
   - `record_level = Item` ➡ `parent_PID` (Normalized)
3. `item_file_name`
   - `record_level = Asset` ➡ Blank
   - `record_level = Item` ➡ `PID` (Normalized)
4. `parent_pid`
   - Either if `record_level` is **`Item`** or **`Asset`** ➡ `parent_PID` (Normalized)
   - For **Collection Object** (e.g `lsu-Ag:collection`), either:
     -  `parent_PID` (Which is root)
5. `collection_pid`  ➡ `collection_name:collection`

### 3️⃣ Distribute Ingestion CSVs in Batches:

#### Batch Criteria

- Max: **2000 objects per CSV**

- Break into chunks (max 2000 rows each)

  - Collection by collection ingestion:
    - Break each mapped, cleaned, updated Collection csv into chunks

  - Institution by institution ingestion:
    - Combine all cleaned + relationship-mapped CSVs for each institution
    - Break into chunks (max 2000 rows each)

- Output: chunked CSVs `institution_batch_1.csv`, `collection_batch_1.csv`

### Links:

- [Preparing metadata for import](https://community.quartexcollections.com/blogs/rebecca-lynd1/2025/04/29/preparing-metadata-for-import).
- [Asset, Item and Section Fields](https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/03/16/document-image-and-section-fields).

---

## 7. UPLOAD CSVs TO AMQuartex

Upload Chucked Metadata CSV to AMQuartex using same methods for uploading files.
