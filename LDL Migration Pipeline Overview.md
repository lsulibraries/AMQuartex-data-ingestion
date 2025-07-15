## LDL to AM Quartex Ingestion Workflow

### Overview

This documentation outlines all required steps, structure, and metadata formatting to migrate LDL digital content (MODS, RDF, OBJ) to **AM Quartex**, covering:

- File download, transformation, and relationship assignments
- Mapping and cleaning
- Relationship columns and PID formatting
- Folder and file structure rules
- Ingestion batch process
- Quartex behavior on compound/simple assets
- Terminology and all edge cases (including collections themselves)

---

## STEP-BY-STEP MIGRATION WORKFLOW

### 1. Download All Assets and Metadata

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

### 2. Create Initial Ingestion CSV (XML2CSV Steps 1–3)

Use `mapping.csv` and run XML-to-CSV scripts to extract MODS metadata.
 Each row = 1 digital object.

- Extract fields like `title`, `creator`, `type`, etc.
- Include `PID` column.

⚠️ This step **does not include relationship columns** yet. It only maps and flattens MODS values into target fields.

---

### 3. Clean the Mapped CSV

Using Python cleaning script:

- Normalize column names
- Strip non-printables
- Convert types (dates, ints)
- Drop empty/duplicate rows
- Rename PID format from:
   `collection-name_object-number` → `collection-name:object-number`
- Normalize file names:
   `collectionname_123_OBJ.JPG` → `collectionname-objectnumber.EXT`

---

### 4. Prepare Ingestion CSV for AM Quartex

#### A. **Required Columns**

| Column Name       | Description                                                  |
| ----------------- | ------------------------------------------------------------ |
| `record_level`    | One of: `Asset`, `Item`, `Section`                           |
| `asset_file_name` | File name of the parent asset (no extension)                 |
| `item_file_name`  | File name of the child object (used for compound children)   |
| `collection_pid`  | The PID of the collection in `format: collectionname:collection` |
| `parent_pid`      | PID of the object’s parent (for items or nested compound structure) |
| `title`           | Title of the object (must include at least 1 letter, not just numbers) |
| Other fields      | Mapped MODS fields (e.g. description, type, date, etc.)      |

#### B. **Logic to Apply for Each Column**

Below are detailed assignment rules for each scenario:

#### ✅ `record_level`

| Scenario                                         | Value   |
| ------------------------------------------------ | ------- |
| Collection object                                | `Asset` |
| Object that is `isMemberOfCollection` only       | `Asset` |
| Object that is `isMemberOf` or `isConstituentOf` | `Item`  |
| Object that is both (collection + parent object) | `Item`  |
| Compound object                                  | `Asset` |
| Children of compound                             | `Item`  |

#### ✅ `asset_file_name`

| Applies to             | Value                         |
| ---------------------- | ----------------------------- |
| `record_level = Asset` | Normalized PID (no extension) |
| `record_level = Item`  | PID of the parent object      |

#### ✅ `item_file_name`

| Applies to             | Value                   |
| ---------------------- | ----------------------- |
| `record_level = Item`  | PID of the child object |
| `record_level = Asset` | Leave blank             |

#### ✅ `collection_pid`

Always set as:
 `collectionname:collection` (same for all objects in the collection)

#### ✅ `parent_pid`

| Scenario                                      | Value                    |
| --------------------------------------------- | ------------------------ |
| Collection row                                | Leave blank              |
| Member of collection only                     | Leave blank              |
| Child of another object (compound, newspaper) | Set to parent object PID |
| Both collection + parent object               | Set to parent object PID |

> 🟨 **Clarification**: Even if an object has a `collection_pid`, if it has a parent object too, `parent_pid` must be populated.

#### ✅ `title`

- Must contain at least 1 letter
- Add qualifiers to numeric-only titles (e.g., "1952 [Map]")

---

### 5. Special Scenarios

#### Compound Assets

- `compound` = Asset
- children (pages, photos, clips) = Items
- PDF cannot be part of a folder/subfolder (must be ingested alone)

#### Collections

- Each collection must have its own row as an `Asset` with its PID and title
- Use `collectionname:collection` in `collection_pid`
- No `asset_file_name` or `item_file_name` is required

#### Nested Relationships

- If an object is both member of collection and of another object →
   `record_level = Item`,
   `asset_file_name = parent object PID`,
   `item_file_name = object PID`,
   `collection_pid = collectionname:collection`,
   `parent_pid = parent object PID`

---

### 6. File Naming & Upload Rules ([URL](https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/03/25/preparing-files-for-upload#OF))

#### A. Filenames

- Must match `asset_file_name` or `item_file_name`
- Do not include extensions in CSV
- Extensions (e.g., `.jpg`, `.pdf`) are handled during upload
- Remove `OBJ`, `PDF`, etc. from filename

#### B. Compound Asset

- Asset folders must not contain any additional sub-folders. Sub-folders will not be uploaded correctly
- Each compound asset folder counts as one asset and appears in [Manage Assets](https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/04/08/an-introduction-to-key-areas-of-a-quartex-website) as a folder containing multiple images once imported.
- When using the Quartex Uploader, compound asset folders must be organised into a parent or 'containing' folder. Selecting the parent folder in Uploader will upload all the folders within it as compound assets in Quartex. 
- For further guidance on organising compound assets for import, including creating parent folders, see the [Quartex Uploader](https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/03/18/using-the-quartex-uploader) instructional video
- **Note**: PDFs cannot be nested within subfolders. That is, PDFs cannot be "child" items in a compound asset. Upon ingestion multi-page file PDFs will be split into individual images per page, which appear in Quartex as items within an asset, similar to other compound assets. 

#### C. Uploading Files

- Upload via **FTP** or **Quartex Uploader**

- Do **not** use nested folders beyond compound parent folder

- Files can all be uploaded into a flat folder:

  ```
  bashCopyEdit/upload/
    LSU-AG001.jpg
    LSU-AG002.pdf
    Compound001/
      Compound001_001.jpg
      Compound001_002.jpg
  ```

✅ **Do not nest child folders** under compound objects. Child items must be inside the compound folder directly.

---

### 7. Ingest CSV in Batches

#### Batch Criteria

- Max: **2000 objects per CSV**
- Split CSVs **by institution** (preferred)
- Combine all cleaned + relationship-mapped CSVs for institution
- Break into chunks (max 2000 rows each)

#### Python Script Notes:

- Merge all cleaned MODS+accounting per institution
- Add `record_level`, `parent_pid`, `collection_pid`, etc.
- Normalize PIDs and file names
- Output: chunked CSVs `institution_batch_1.csv`, `..._2.csv`

---

### 8. Notes on PID Normalization

#### ✅ PID format in final CSV and filenames:

- From: `collectionname_objectnumber`
- To: `collectionname:objectnumber`

Use this format:

- CSV `asset_file_name`, `item_file_name`, `parent_pid`, `collection_pid`
- Uploaded OBJ files: rename to match new PIDs (e.g. `lsu-ag:001.jpg`)

---

### 9. Quartex Relationship Identification

| Object Type     | record_level | asset_file_name | item_file_name | parent_pid | collection_pid            |
| --------------- | ------------ | --------------- | -------------- | ---------- | ------------------------- |
| Collection      | Asset        | [blank]         | [blank]        | [blank]    | collectionname:collection |
| Compound Asset  | Asset        | PID             | [blank]        | [blank]    | collectionname:collection |
| Compound Child  | Item         | Parent PID      | PID            | Parent PID | collectionname:collection |
| Simple Asset    | Asset        | PID             | [blank]        | [blank]    | collectionname:collection |
| Nested Compound | Item         | Parent PID      | PID            | Parent PID | collectionname:collection |

---

### 10. Quartex Behavior Clarifications

- **Quartex does not use folders** to determine relationships. Everything is inferred from the CSV columns.
- File location is irrelevant, only filenames matter (must match filenames in ingestion CSV).
- No institution identifier is used in the CSV. You manage institutional separation by using separate ingestion batches (or collections assigned per institution).
- `collection_pid` ensures grouping of assets to the correct collection.
- `parent_pid` ensures correct nesting of compound relationships.

---

## 11. Clarifications:

#### **1. What are the FTP and Quartex Uploader used for?**

Both tools are used to **upload your digital asset files (OBJs: PDF, JPG, JP2, audio, video, etc.)** into the AM Quartex platform **before** you ingest metadata via the ingestion CSV.

**Must use one of these to upload files before ingestion!**
 The ingestion CSV **references filenames** (not paths), and these files must already exist in Quartex for it to attach them during import.

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



#### Does Folder structure approach is used for for assigning relationships?

**No** — there is only one actual method to create metadata relationships, and that is through the **ingestion CSV**. This CSV defines the hierarchy and relationships between assets, items, and collections.



#### **So what is the role of the folder structure, then?**

The folder structure is used **only during the file upload process** to help Quartex understand which files belong together as part of a **compound asset**. It does not define metadata relationships. Instead, it ensures that all parts of a compound asset (like pages of a book or parts of a multi-part audio) are grouped together.

In other words, the folder structure helps Quartex recognize a set of files as one compound asset during upload, but the CSV is what actually assigns the relationships and metadata once the files are ingested.

This is **not a metadata assignment**, but a **structural packaging rule** for compound asset *files*:

- A **compound asset** must have its components **inside a single folder** when uploaded

- Quartex reads this as a **compound parent → item relationship**, **only if structured correctly**

- E.g. for a book or oral history:

  ```
  MyBook001/
      MyBook001_001.jpg
      MyBook001_002.jpg
  ```

❌ However, Do **not nest folders inside folders**

#### 3. Does Quartex **require nested folder structure** for upload?

**No.** Quartex **does not require nested folders** beyond one level for compound assets.

### 🔹 Required (for compound upload):

✅ One folder per compound object
 ✅ Folder name = PID of the asset
 ✅ Files inside (not nested further)

Example:

```
PrintedBook001/
    PrintedBook001_001.jpg
    PrintedBook001_002.jpg
```

### ❌ Not Allowed:

```
PrintedBook001/
    Chapter1/
        Page1.jpg
```

or

```
Collection/
    Book001/
        Chapter1/
```

### 🔹 For simple assets:

- Just place the `.jpg`, `.pdf`, etc. directly in the upload root
- No folder needed

### Summary Table

| Feature                    | FTP Upload                         | Quartex Uploader      |
| -------------------------- | ---------------------------------- | --------------------- |
| Upload type                | Command-line / GUI                 | Desktop GUI           |
| Protocol                   | FTP                                | HTTPS                 |
| Supports compound upload?  | ✅ Yes                              | ✅ Yes                 |
| Requires folder structure? | ✅ For compound assets              | ✅ For compound assets |
| Relationship assignment?   | ❌ No                               | ❌ No                  |
| File naming matters?       | ✅ Yes                              | ✅ Yes                 |
| Path matters?              | ❌ No (except for compound folders) | ❌ No                  |



---

## Links

**1. Relationships via CSV Metadata import: ** https://community.quartexcollections.com/blogs/rebecca-lynd1/2025/04/29/preparing-metadata-for-import



**2. Folder Structure & Asset Upload** https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/03/25/preparing-files-for-upload#organisation%E2%80%91of%E2%80%91files
