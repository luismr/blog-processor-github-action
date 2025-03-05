### **Non-Functional Specification: `article.yaml` Structure**

#### **Purpose**
The `article.yaml` file serves as the metadata descriptor for each blog post, ensuring structured and consistent information storage. It is used by the GitHub Action to process, organize, and generate blog posts in a structured manner.

---

### **1. File Format**
- The `article.yaml` file follows the **YAML 1.2** format.
- It is a **UTF-8 encoded text file**.
- Key-value pairs are **case-sensitive**.
- Indentation uses **two spaces** (no tabs allowed).

---

### **2. File Location**
- The `article.yaml` file must be located inside the blog post’s folder:
  ```
  /articles/<post_title_unix_folder_formatted>/article.yaml
  ```

  **Example:**
  ```
  /articles/my-first-blog-post/article.yaml
  ```

---

### **3. YAML Structure**
The file consists of key-value pairs representing metadata:

```yaml
title: "My First Blog Post"
description: "An introduction to my new blog."
author: "GitHub User"
created_at: "2025-03-05T12:00:00Z"
updated_at: "2025-03-06T14:30:00Z"
tags:
  - "technology"
  - "github"
  - "automation"
status: "published"
```

---

### **4. Field Definitions**

| Field        | Type          | Required | Description |
|-------------|--------------|----------|-------------|
| `title`     | `string`      | ✅ Yes    | The title of the blog post. It must be unique and should not exceed 100 characters. |
| `description` | `string`    | ✅ Yes    | A short description of the blog post (max 255 characters). Used for SEO and summaries. |
| `author`    | `string`      | ✅ Yes    | The name of the author. It is automatically assigned from the GitHub commit author if not provided. |
| `created_at` | `ISO 8601 datetime` | ✅ Yes | The date and time when the article was first created (e.g., `"2025-03-05T12:00:00Z"`). |
| `updated_at` | `ISO 8601 datetime` | ✅ Yes | The date and time of the last update. It is updated automatically when the article changes. |
| `tags`      | `list<string>` | ❌ No    | A list of keywords or tags related to the article. Helps in categorization. |
| `status`    | `enum: ["draft", "published"]` | ✅ Yes | The publishing state of the article. If `"draft"`, the article is not published on GitHub Pages. |

---

### **5. Constraints and Validations**
- **Title Uniqueness:**  
  - The `title` should be unique within all blog posts to prevent conflicts.
  - Duplicate titles trigger an **error** during processing.

- **Date Format:**  
  - The `created_at` and `updated_at` fields must follow the **ISO 8601 UTC format** (`YYYY-MM-DDTHH:MM:SSZ`).
  - Example: `"2025-03-05T12:00:00Z"`

- **Tag List:**  
  - The `tags` field is optional but must be an **array** if present.
  - Each tag must be a **lowercase string** with no spaces (use dashes if needed).
  - Example:
    ```yaml
    tags:
      - "github"
      - "automation"
      - "blogging"
    ```

- **Status Handling:**  
  - The `status` field can only have **two values**: `"draft"` or `"published"`.
  - Draft articles will be ignored in the final blog deployment.

---

### **6. Error Handling**
- **Missing Required Fields:**  
  - If a required field (`title`, `description`, `author`, `created_at`, `updated_at`, or `status`) is missing, the GitHub Action will log an **error** and stop processing the article.

- **Invalid YAML Format:**  
  - If the file has incorrect YAML syntax (e.g., indentation errors), the system will log an **error** and **skip** the article.

- **Invalid Date Format:**  
  - If `created_at` or `updated_at` is not in ISO 8601 format, the action logs an **error** and stops processing.

- **Unsupported Characters in Title:**  
  - Titles must be **plain text** (no special characters, emojis, or non-ASCII characters).
  - Allowed characters: **A-Z, a-z, 0-9, spaces, hyphens (-), underscores (_)**
  - Example of an invalid title:
    ```yaml
    title: "My Blog Post 🚀"
    ```
  - Error: `"Invalid character in title"`

---

### **7. Example Valid and Invalid `article.yaml` Files**

✅ **Valid Example**
```yaml
title: "A Guide to GitHub Actions"
description: "Learn how to automate tasks using GitHub Actions."
author: "John Doe"
created_at: "2025-02-28T10:15:00Z"
updated_at: "2025-03-01T12:30:00Z"
tags:
  - "github"
  - "automation"
  - "devops"
status: "published"
```

❌ **Invalid Example (Incorrect YAML Syntax)**
```yaml
title: "My First Post"
description "Missing colon after key"
author: "Jane Doe"
created_at: "March 5, 2025"  # Invalid date format
updated_at: "2025/03/06"  # Invalid date separator
tags: "not a list"  # Invalid, should be a list
status: "unknown"  # Invalid, should be "draft" or "published"
```
Errors:
- Missing colon in `description`
- `created_at` and `updated_at` are in the wrong format
- `tags` should be a list (`-` notation)
- `status` contains an invalid value

---

### **8. Automation Considerations**
- **Default Author Handling:**  
  - If the `author` field is missing, it is automatically populated using the **GitHub commit author**.

- **Automatic Date Updates:**  
  - If `updated_at` is missing, it will be set to the **current timestamp** when the GitHub Action runs.

- **Slug Generation:**  
  - The **title** is converted into a URL-friendly format (`my-first-blog-post`) for **folder names and URLs**.

---

### **9. Future Enhancements**
- Support for additional metadata fields, such as:
  - `categories` for broader topic classification.
  - `reading_time` (estimated time to read the article).
- Automated generation of **author profiles** based on commit history.

---

### **Conclusion**
The `article.yaml` specification ensures structured metadata handling, prevents inconsistencies, and automates blog post processing in a **GitHub-based Markdown-to-HTML workflow**.

Would you like any refinements, such as adding support for **multi-author posts or custom metadata fields**? 🚀