## 📁 **Quartex Migration, Asset Preparation & Folder Structure Guidelines**

*To be included in the Migration Documentation*

This section outlines how digital assets (images, PDFs, audio, video) must be structured and named for successful ingestion into AM Quartex. These rules do not relate to metadata cleaning but are essential for file upload integrity during migration.

------

### **1. Asset Types and Folder Requirements**

- **Single-file Assets** (e.g., one image, audio, or video):
  - Do **not** require individual folders.
  - Can be uploaded directly via FTP or Quartex Uploader.
  - File size must be under **50MB** if using web uploader.
- **Compound Assets** (e.g., books, scrapbooks, multi-part oral histories):
  - Must be placed inside an individual **asset folder**.
  - Asset folders must contain **only files** — no subfolders.
  - Each compound folder = one asset in Quartex.

------

### **2. Upload Folder Structure**

- Use a **parent folder** to group multiple compound asset folders.
- Upload the **parent folder**, not the individual asset folders inside it.
- This enables batch upload of multiple compound assets.

✅ Example:

```
swiftCopyEdit/CollectionParentFolder/
  ├── Book001/
  ├── Book002/
  ├── AudioSet01/
```

------

### **3. File Naming Conventions**

- **Unique Filenames** are required across your Quartex account.
  - Duplicate filenames will overwrite or conflict with existing assets.
- **Do not include file extensions** in metadata filename columns (e.g., no `.jpg`)
- **Max filename length**:
  - Images/audio/video: 128 characters
  - PDFs: 120 characters
- **Avoid** problematic characters:
  - ❌ Double quotation marks
  - ❌ Brackets and commas (especially if generating MARC records)

------

### **4. Sorting and Sequence Rules**

- Quartex uses **alphanumeric sorting**:
  - "10" appears after "1" and before "2"
  - Capital letters sort before lowercase
- To enforce logical order, use **leading zeros**:
  - `Page001`, `Page002`, ..., `Page010`

------

### **5. EXIF Rotation Consideration**

- Quartex **ignores EXIF rotation flags**
- To prevent images from displaying rotated:
  - Re-save images with the correct rotation applied to the actual pixel data (e.g., using Photoshop or MS Paint)



### 📂 **Quartex Collection Management Overview**

*To be added to Migration Documentation*

Each collection in Quartex supports a suite of asset and metadata management functions:

------

#### **Displayed Information per Collection**

- **Collection Name**
- **Last Updated**: Date/time of last modification
- **View Assets**: Browse assigned assets
- **Assign Assets**: Add assets to this collection
- **Export Metadata**: Export all metadata in CSV format
- **Edit Info**: Modify collection name or details
- **Delete Collection**: Remove the collection

------

#### **View Assets**

- Collections display assets assigned to them.
- Each asset must be part of **at least one collection** to be promoted and published.
- Assets can belong to **multiple collections**.
- Collections can be published to **multiple websites**.
- Assets in "Manage Assets" can be filtered by collection.

------

#### **Assign Assets**

- Use the **Assign Assets** button to select assets from Manage Assets and associate them with the collection.

------

#### **Export Metadata**

- Use **Export Metadata** to generate a CSV containing:
  - All asset and item-level metadata
  - Unique identifiers for each asset/item
  - Blank fields for any empty metadata entries
- CSV download is initiated via the **Processes menu**.
- For details, refer to the **Export Metadata** section in Quartex documentation.