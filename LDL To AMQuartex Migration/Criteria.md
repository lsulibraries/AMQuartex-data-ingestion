## PREPARING ASSETS FOR IMPORT

### 1. Overview Criteria:

#### **Single-file Assets**

- There is no need for single-file assets to be organized into individual folders per asset before upload.
- Images, audio and video files can be uploaded on their own into Quartex via [FTP](https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/03/17/ftp-upload), the [Quartex Uploader](https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/03/18/using-the-quartex-uploader) or using the [Upload Single File](https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/03/18/single-file-upload-and-replacement) button found under ‘Add’ in [Manage Assets](https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/04/08/an-introduction-to-key-areas-of-a-quartex-website). 
- **Please Note:** this final option is limited to files 50 MB or smaller. See the *[File Upload and Replacement](https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/03/18/single-file-upload-and-replacement)* article for further information.

#### Compound Assets

The component files of an asset which is composed of more than one digital item (image/ audio /video), such as a printed book, scrapbook or multi-part oral history, must be organized into an asset folder before upload via [FTP](https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/03/17/ftp-upload) or [Quartex Uploader](https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/03/18/using-the-quartex-uploader).

#### Please Note:

- Failing to organize the **component files of a compound asset within an asset folder**, or **selecting your asset folder instead of the parent upload folder** when browsing to your files on the Uploader, will result in **importing the component files as individual assets instead of items**. 
- Asset folders must not contain any additional sub-folders. Sub-folders will not be uploaded correctly:

![Screenshot_FTP_File_Structure_WRONG](C:\Users\mfatol1\Desktop\Screenshot_FTP_File_Structure_WRONG.png)

- Each compound asset folder counts as one asset and appears in [Manage Assets](https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/04/08/an-introduction-to-key-areas-of-a-quartex-website) as a folder containing multiple images once imported.
- When using the Quartex Uploader, compound asset folders must be organized into a parent or **'containing'** folder. Selecting the parent folder in Uploader will upload all the folders within it as compound assets in Quartex. This is useful if you have a collection folder containing your assets, as it allows you to upload your entire collection at once. 

![Screenshot_Uploader_File_Structure](C:\Users\mfatol1\Desktop\Screenshot_Uploader_File_Structure.png)

- Any number of folders can be uploaded via FTP at once. For example:

![Screenshot_FTP_File_Structure_RIGHT](C:\Users\mfatol1\Desktop\Screenshot_FTP_File_Structure_RIGHT.png)

- PDFs cannot be nested within subfolders. 
  - That is, PDFs cannot be "child" items in a compound asset. 
  - Upon ingestion multi-page file PDFs will be split into individual images per page, which appear in Quartex as items within an asset, similar to other compound assets. 
  - However, because of this splitting process, PDFs cannot be added as items to an existing asset or uploaded with siblings in an asset folder like other image files. For more on working with PDFs and other file formats, see [Supported File Types](https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/03/15/supported-file-types).
- For further guidance on organising compound assets for import, including creating parent folders, see the [Quartex Uploader](https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/03/18/using-the-quartex-uploader) instructional video.

### 2. Detailed Criteria:

- **File Name:**

1. - Must match `item_file_name` (`PID`s)
     - Must be name only after the Object PID
     - Remove `OBJ`, `PDF`, etc. from filename

   - Asset folder and filenames must be **unique**. There cannot be more than one asset with the same name uploaded to your Quartex account.

     - File name size:
       - Filenames have a maximum length of 128 characters
       - except for PDFs which have a maximum length of 120 characters  (to allow for page numbers generated for the children generated upon splitting)
       - **Ideally**, AMQuartex recommends keeping filenames under 100 characters to be safe.

     - Notes:
       - Avoid using **double quotation** marks (single quotation marks are fine). Double quotation marks render the individual asset viewer unable to load the asset or metadata in the DAMS.
       - The filename that displays in Quartex is everything before the period (e.g. - `file1234` in `file1234.jpg)`.
         - Filenames do not include the file extension and should not. Avoid filenames like `file1234.jpg.jpg`.
       - If planning to generate MARC (Machine-Readable Cataloguing) records for your collections, avoid using brackets and commas as these are not compatible with [MARC](https://www.loc.gov/marc/umb/).
       - Whilst Asset titles can include numerical detail, they must always include at least one letter. 

   - Must contain extensions (Do not include extensions in **CSV**)

2. **Alphanumeric Sorting structure in AMQuartex:**

   - **Alphanumeric Sorting:** Quartex uses alphanumeric sorting for all uploaded assets. This means:

     - The number ‘10’ will always appear after ‘1’, and before ‘2’. 

     - Similarly, capital letters will always appear before lowercase letters in a list – A-Z then a-z.

   - **To enable files and folders to sort in a logical order**, consider using leading `0s` in both **folder** and **filenames**.

   - The following example will display assets in a logical order:

     ```
     PrintedBook001
     PrintedBook002
     PrintedBook003 etc.
     ```

   - The following example will display items within an asset in a logical order:

     ```
     PrintedBook001_001
     PrintedBook001_002
     PrintedBook001_003 etc.
     ```

3. **Folder Structure:**

   - 1st Level Folder would be `collection_pid` Or **Collection Name**

   - 2nd Level Folder(s) would be **compound** `PID`s 
     - Within Each compound all Files that are directly or indirectly associated to the compound will be copied. 
       - Indirectly means if an object resides within 1 or more than one compound PIDs (`compoundNewsPaper` or `NewspaperIssue`) should be copied under the 1st compound

   - **Note**, No more than 1 nested compound folder is allowed!

   - Files can all be uploaded into a flat folder:

4. **Uploading Simple and Complex Assets Together**

   Using the structures provided above, you can upload a combination of asset complexity types together. Create your folder that all assets will sit in. Within this folder, you can put your simple asset files and your subfolders for compound assets. Don't forget, PDFs do not need to be nestled into subfolders. Below is an example of a folder ready for upload with different types of asset within it:

![Asset upload files](C:\Users\mfatol1\Desktop\Asset upload files.png)

---

# PREPARE INGESTION CSV FOR AMQartex:

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

- Three pages exist for the configuration and management of different levels of metadata fields:

  - **Asset Fields** – Metadata fields to describe the **entire [record](https://community.quartexcollections.com/glossary/record).**
    - A **record** is an **entry** in My Assets. In its most basic form it is metadata containing:
      - **At least a title**. 
      - **Multiple files**, detailed **metadata** and **transcripts**.  

  - **Item Fields** – Metadata fields to describe files **within** [compound assets](https://community.quartexcollections.com/glossary/compound-asset) **(for instance, an illustration or diagram within a book)**.
    - **A compound asset** contains multiple files. These can be created from a folder of files, or from a multi-page PDF. 
    - For example, a printed book represented by a folder of digital images for each page is a compound asset. 
    - So is a printed book in the form of one large PDF; PDFs get split into individual images when they are ingested into Quartex.

  - **Section Fields** – Metadata fields to describe a group of items within an asset (for instance, chapters in a book).
    - ⛔ **DON'T KNOW IF WE HAVE SECTION LEVEL ITEMS!!!!**

- **Please Note:** 

  - If you want to present Item or Section metadata, these will need to be enabled for display on the Assets List under **Visible Document Metadata** in the [Asset Details Configuration Options](https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/04/08/document-configuration-options) section of that page. 
  - The order and display of all metadata fields is configured further down on the Assets List ([read about it here](https://community.quartexcollections.com/blogs/rebecca-lynd1/2021/04/08/configuring-asset-item-and-section-metadata-displa)).

- **Grouping Fields**

  - Each metadata field is contained within a `field group` and each field group can be **configured** to appear across all, or only some of your assets.

- **Standard Fields**
  Metadata fields added to the 'Standard Fields' group are available in every record regardless of collection. For example ‘Title’, ‘Date’, ‘Collection Summary’ or ‘Description’.

- **Additional Field Groups**

  Additional field groups can be added to group fields which may only be relevant to certain collections or material types; such as objects or media recordings. Splitting fields into different field groups allows users to establish collection-specific requirements and labelling. 

| Scenario                                         | Value   |
| ------------------------------------------------ | ------- |
| Collection object                                | `Asset` |
| Compound object ()                               | `Asset` |
| Children of compound                             | `Item`  |
| Object that is both (collection + parent object) | `Item`  |

#### ✅ `asset_file_name`

| Applies to             | Value                         |
| ---------------------- | ----------------------------- |
| `record_level = Asset` | Normalized PID (no extension) |
| `record_level = Item`  | PID of the parent object      |

#### ✅ `item_file_name`

| Applies to               | Value                                                |
| ------------------------ | ---------------------------------------------------- |
| `record_level = Item`    | PID of the child object                              |
| `record_level = Asset`   | Leave blank                                          |
| `record_level = Section` | **first** and **last** files (`PID`) in each section |

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

### 9. Quartex Relationship Identification

| Object Type     | record_level | asset_file_name | item_file_name | parent_pid | collection_pid            |
| --------------- | ------------ | --------------- | -------------- | ---------- | ------------------------- |
| Collection      | Asset        | [blank]         | [blank]        | [blank]    | collectionname:collection |
| Compound Asset  | Asset        | parent_PID      | [blank]        | parent_PID | collectionname:collection |
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

Both tools are used to **upload digital asset files (OBJs: PDF, JPG, JP2, audio, video, etc.)** into the AM Quartex platform **before** ingesting metadata via the ingestion CSV.

Must use one of these to upload files before ingestion, as the ingestion CSV **references filenames** (not paths), and these files must already exist in Quartex for it to attach them during import.

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



#### 2. Does Folder structure approach is used for for assigning relationships?

**No** — there is only one actual method to create metadata relationships, and that is through the **ingestion CSV**. This CSV defines the hierarchy and relationships between assets, items, and collections.



#### **3. What is the role of the folder structure?**

The folder structure is used **only during the file upload process** to help Quartex understand which files belong together as part of a **compound asset**. 

It does not define metadata relationships. Instead, it ensures that all parts of a compound asset (like pages of a book or parts of a multi-part audio) are grouped together.

In other words, the folder structure helps Quartex recognize a set of files as one compound asset during upload, but the CSV is what actually assigns the relationships and metadata once the files are ingested.

This is **not a metadata assignment**, but a **structural packaging rule** for compound asset *files*:

- A **compound asset** must have its components **inside a single folder** when uploaded

- Quartex reads this as a **compound parent → item relationship**, **only if structured correctly**

- ❌ Do **not nest folders inside folders**

- **Folder Structure in Upload:**

  - **For compound upload:**

    - One folder per compound object

    - Folder name = PID of the asset

    - Files inside (not nested further)

  - **For simple assets:**

    - Just place the `.jpg`, `.pdf`, etc. directly in the upload root
    - No folder needed

- **✅ Allowed Example:**

  ```
  /collection/
    item001.jpg
    MyBook001/
      MyBook001_001.jpg
      MyBook001_002.jpg
  ```

- **❌ Not Allowed Example:**

  ```
  /collection/
    item001.jpg 
    PrintedBook001/
      Chapter1/
          Page1.jpg
          Page2.jpg
  ```

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

