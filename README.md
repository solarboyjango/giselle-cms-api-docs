# Giselle-cms-api-docs

## Overview
This document provides an overview of the API endpoints available for managing Folder documents in the backend service. The API is designed based on the Retrieval-Augmented Generation (RAG) framework to support document retrieval and response generation.

## Authentication & Authorization
- **Authentication Method**: API Key
- **API Key Format**: `cms_12345`
- **Usage**: Clients must include the API Key in the request header.

**Example Request Header:**
```http
GET /secure-endpoint
X-API-Key: cms_12345
```

## API Endpoints

### 1. Retrieve Folders
**Method:** `GET`  
**Endpoint:** `/folders`  
**Description:** Retrieves a list of available Folders. Supports sorting and searching.

#### Request Parameters
| Parameter   | Type    | Required | Description |
|------------|---------|----------|-------------|
| `query`      | `string`  | No | Search keyword to filter Folders by name or prompt. |
| `sort_by`    | `string`  | No | Field to sort by (e.g., `name`, `created_at`). Default: `name`. |
| `sort_order` | `string`  | No | Sorting order (`asc` for ascending, `desc` for descending). Default: `asc`. |

#### Response Example
```json
{
  "code": 200,
  "msg": "Folders retrieved successfully.",
  "data": {
    "folders": [
      {
        "folder_id": "123",
        "name": "HR Documents",
        "prompt": "Retrieve HR policies and guidelines",
        "created_at": "2024-02-20T12:34:56Z",
        "updated_at": "2024-02-25T10:00:00Z",
        "status": "active",
        "type": "knowledge_base"
      },
      {
        "folder_id": "456",
        "name": "Legal Policies",
        "prompt": "Access corporate legal compliance documents",
        "created_at": "2024-01-15T08:00:00Z",
        "updated_at": "2024-01-20T14:30:00Z",
        "status": "inactive",
        "type": "knowledge_base"
      }
    ]
  }
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `code` | `integer` | HTTP status code of the response. |
| `msg`    | `string`  | A confirmation message indicating success. |
| `folders`    | `array`   | A list of available Folders. |
| `folder_id`  | `string`  | The unique ID of the Folder. |
| `name`       | `string`  | The name of the Folder. |
| `prompt`     | `string`  | A short description or recommended usage for the Folder. |
| `created_at` | `string`  | The timestamp when the Folder was created (ISO 8601 format). |
| `updated_at` | `string`  | The timestamp of the last update to the Folder (ISO 8601 format). |
| `status`     | `string`  | The current status of the Folder (`active` for enabled, `inactive` for disabled). |
| `type`       | `string`  | Type of Folder (`knowledge_base`). |

#### Status Codes
- `200 OK` - Successful retrieval  
- `400 Bad Request` - Invalid parameters (e.g., incorrect `sort_by` field)  
- `403 Forbidden` - Missing or invalid API Key  
- `500 Internal Server Error` - Server-side error  

---
### 2. Create Folder
**Method:** `POST`
**Endpoint:** `/folders`
**Description:** Creates a new Folder of type `knowledge_base`. Cannot be used to create a `system` type.

#### Request Body
```json
{
  "name": "New Folder",
  "prompt": "Short description or usage hint",
  "status": "active",
  "type": "knowledge_base"
}
```

#### Request Body Parameters
| Parameter | Type    | Required | Description |
|----------|---------|----------|-------------|
| `name`   | `string` | Yes | The name of the new Folder. |
| `prompt` | `string` | No  | Description or usage hint. |
| `status` | `string` | No  | Status (`active` or `inactive`). Default: `active`. |
| `type`   | `string` | Yes | Must be `knowledge_base`. Cannot create `system` type. |

#### Response Example
```json
{
  "code": 200,
  "msg": "Folder created successfully.",
  "folder_id": "789"
}
```

#### Response Parameters
| Parameter     | Type    | Description |
|---------------|---------|-------------|
| `code` | integer | HTTP status code. |
| `msg`     | string  | Confirmation message. |
| `folder_id`   | string  | Unique ID of the created Folder. |

#### Status Codes
- `200 Created` - Folder created successfully  
- `400 Bad Request` - Missing or invalid parameters  
- `403 Forbidden` - Attempting to create a `system` type  
- `500 Internal Server Error` - Server-side error  
---



### 3. Update Folder Details
**Method:** `PUT`  
**Endpoint:** `/folders/{folder_id}`  
**Description:** Updates the details of a specific Folder, including name, prompt, or status.

#### Request Parameters
| Parameter   | Type    | Required | Description |
|------------|---------|----------|-------------|
| `folder_id`  | `string`  | Yes | The unique ID of the Folder to update. |

#### Request Body
```json
{
  "name": "Updated Folder Name",
  "prompt": "Updated prompt description",
  "status": "active"
}
```

#### Request Body Parameters
| Parameter | Type    | Required | Description |
|----------|---------|----------|-------------|
| `name`   | `string` | No | The new name of the Folder. |
| `prompt` | `string` | No | The new description or recommended usage of the Folder. |
| `status` | `string` | No | The status of the Folder (`active` for enabled, `inactive` for disabled). |

#### Response Example
```json
{
  "code": 200,
  "msg": "Folder details updated successfully."
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `code` | `integer` | HTTP status code of the response. |
| `msg`    | `string`  | A confirmation message indicating success. |

#### Status Codes
- `200 OK` - Successfully updated  
- `400 Bad Request` - Invalid parameters (e.g., incorrect `status` value)  
- `403 Forbidden` - Missing or invalid API Key  
- `404 Not Found` - Folder not found  
- `500 Internal Server Error` - Server-side error  



---

### 4. Delete Folder
**Method:** `DELETE`
**Endpoint:** `/folders/{folder_id}`
**Description:** Deletes a Folder of type `knowledge_base`. `system` type cannot be deleted.

#### Request Parameters
| Parameter   | Type   | Required | Description |
|-------------|--------|----------|-------------|
| `folder_id` | string | Yes      | The ID of the Folder to delete. |

#### Response Example
```json
{
  "code": 200,
  "msg": "Folder deleted successfully."
}
```

#### Status Codes
- `200 OK` - Folder deleted successfully  
- `400 Bad Request` - Attempting to delete a `system` type  
- `403 Forbidden` - Missing or invalid API Key  
- `404 Not Found` - Folder not found  
- `500 Internal Server Error` - Server-side error  

---


### 5. Retrieve Files in a Folder
**Method:** `GET`  
**Endpoint:** `/files/folder/{folder_id}`  
**Description:** Retrieves all files in a specific Folder. Supports pagination, sorting, and search.

#### Request Parameters
| Parameter   | Type    | Required | Description |
|------------|---------|----------|-------------|
| `folder_id`  | `string`  | Yes | The unique ID of the Folder. |
| `page`       | `integer` | No | The page number. Default: `1`. |
| `page_size`  | `integer` | No | Number of items per page. Default: `10`. |
| `query`      | `string`  | No | Search keyword to filter file names. |
| `sort_by`    | `string`  | No | Field to sort by (e.g., `name`, `created_at`). Default: `name`. |
| `sort_order` | `string`  | No | Sorting order (`asc` for ascending, `desc` for descending). Default: `asc`. |

#### Response Example
```json
{
  "total_count": 25,
  "page": 1,
  "page_size": 10,
  "files": [
    {
      "file_id": "A1B2",
      "name": "policy.docx",
      "url": "https://markdownlivepreview-1.com",
      "uploader": "user@example.com",
      "start_at": "2024-03-01T00:00:00Z",
      "end_at": "2024-06-01T00:00:00Z",
      "created_at": "2024-02-20T12:34:56Z",
      "updated_at": "2024-02-25T10:00:00Z"
    },
    {
      "file_id": "C3D4",
      "name": "guidelines.pdf",
      "url": "https://markdownlivepreview-2.com",
      "uploader": "admin@example.com",
      "start_at": "2024-01-01T00:00:00Z",
      "end_at": null,
      "created_at": "2024-01-15T08:00:00Z",
      "updated_at": "2024-01-20T14:30:00Z"
    }
  ]
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `total_count` | `integer` | Total number of files matching the query. |
| `page`        | `integer` | Current page number. |
| `page_size`   | `integer` | Number of files returned per page. |
| `files`       | `array`   | List of files in the specified Folder. |
| `file_id`     | `string`  | The unique ID of the file. |
| `name`    | `string`  | The name of the file. |
| `url`    | `string`  | The downloading url of the file. |
| `uploader`    | `string`  | The email or username of the user who uploaded the file. |
| `start_at`    | `string`  | The start date of the file's availability (ISO 8601). If `null`, the file is available immediately. |
| `end_at`      | `string`  | The end date of the file's availability (ISO 8601). If `null`, the file remains available indefinitely. |
| `created_at`  | `string`  | The timestamp when the file was created (ISO 8601 format). |
| `updated_at`  | `string`  | The timestamp of the last update to the file (ISO 8601 format). |

#### Status Codes
- `200 OK` - Successful retrieval  
- `400 Bad Request` - Invalid parameters (e.g., incorrect `folder_id`, invalid `page` value)  
- `403 Forbidden` - Missing or invalid API Key  
- `404 Not Found` - Folder not found  
- `500 Internal Server Error` - Server-side error  

---


### 6. Batch Check Before Upload

**Method:** `POST`  
**Endpoint:** `/files/batch_check`  
**Description:** Performs pre-upload validation for a list of files. Checks for filename duplicates and unsupported file types.  

#### Request Parameters

| Parameter   | Type     | Required | Description                   |
| ----------- | -------- | -------- | ----------------------------- |
| `folder_id` | `string` | Yes      | The unique ID of the Folder.  |
| `names`     | `array`  | Yes      | A list of filenames to check. |

#### Request Body

```json
{
  "folder_id": "123",
  "names": ["report.docx", "script.exe", "summary.pdf"]
}
```

#### Response Example

```json
{
  "duplicates": ["report.docx"],
  "invalid_extensions": ["script.exe"]
}
```

#### Response Parameters

| Parameter            | Type    | Description                                 |
| -------------------- | ------- | ------------------------------------------- |
| `duplicates`         | `array` | Filenames that already exist in the folder. |
| `invalid_extensions` | `array` | Filenames with unsupported extensions.      |

#### Allowed Extensions

* `.pdf`, `.ppt`, `.pptx`, `.doc`, `.docx`, `.txt`, `.csv`, `.xls`, `.xlsx`

#### Status Codes

* `200 OK` – Validation completed successfully
* `400 Bad Request` – Missing or invalid parameters
* `403 Forbidden` – Invalid API Key
* `404 Not Found` – Folder not found
* `500 Internal Server Error` – Server error during check


---
### 7. Batch Upload Files

**Method:** `POST`  
**Endpoint:** `/upload`  
**Content-Type:** `multipart/form-data`  
**Description:** Uploads multiple files to a specified Folder. All files in the batch must be uploaded by the same user and share the same availability period.  

#### Request Parameters (Form Data)

| Parameter   | Type     | Required | Description  |
| ----------- | -------- | -------- | -------------|
| `folder_id` | `string` | Yes      | The unique ID of the Folder. |
| `uploader`  | `string` | Yes      | The email or username of the user uploading the files. |
| `start_at`  | `string` | Yes      | The start date of the file's availability (`YYYY-MM-DD`). If null, the file is available immediately. |
| `end_at`    | `string` | Yes      | The end date of the file's availability (`YYYY-MM-DD`). If `null`, the file remains available indefinitely. |
| `files`     | `file[]` | Yes      | A list of files to be uploaded. Multiple files should be submitted using the same key name.   |

#### Example Request (cURL)

```bash
curl -X POST https://api.example.com/upload \
  -H "Authorization: Bearer <API_KEY>" \
  -F "folder_id=123" \
  -F "uploader=user@example.com" \
  -F "start_at=2024-03-01" \
  -F "end_at=2024-06-01" \
  -F "files=@report.docx" \
  -F "files=@summary.pdf"
```

#### Response Example

```json
{
  "uploaded": ["report.docx", "summary.pdf"],
  "msg": "Files uploaded successfully."
}
```

#### Response Parameters

| Parameter  | Type     | Description                                          |
| ---------- | -------- | ---------------------------------------------------- |
| `uploaded` | `array`  | A list of filenames that were successfully uploaded. |
| `msg`  | `string` | A confirmation message indicating success.           |

#### Status Codes

* `200 Created` - Files successfully uploaded
* `400 Bad Request` - Invalid parameters (e.g., missing `folder_id`, empty `files` list, or incorrect `start_at`/`end_at` format)
* `403 Forbidden` - Missing or invalid API Key
* `500 Internal Server Error` - Server-side error


---


### 8. Batch Overwrite Existing Files

**Method:** `PUT`  
**Endpoint:** `/files`  
**Content-Type:** `multipart/form-data`  
**Description:** Overwrites existing files in a specified Folder. Files can be updated by any user (not restricted to the original uploader). If `start_at` and `end_at` are not provided, the system will retain the previous availability settings of each file.

#### Request Parameters

| Parameter   | Type     | Required | Description  |
| ----------- | -------- | -------- | ------------ |
| `folder_id` | `string` | Yes | The unique ID of the Folder. |
| `uploader`  | `string` | Yes | The email or username of the user overwriting the files. |
| `files`     | `file[]` | Yes | A list of files to be overwritten. The `name` of each uploaded file must match an existing file in the folder. |
| `start_at`  | `string` | No  | (Optional) New start date of availability (`YYYY-MM-DD`). If `null` provided, the original setting will be kept. |
| `end_at`    | `string` | No  | (Optional) New end date of availability (`YYYY-MM-DD`). If `null` provided, the original setting will be kept. |

#### Example Request (cURL)

```bash
curl -X PUT https://api.example.com/files \
  -H "Authorization: Bearer <API_KEY>" \
  -F "folder_id=123" \
  -F "uploader=user@example.com" \
  -F "files=@report.docx"
```

#### Response Example

```json
{
  "overwritten": ["report.docx"],
  "msg": "Files overwritten successfully."
}
```

#### Response Parameters

| Parameter     | Type     | Description                                             |
| ------------- | -------- | ------------------------------------------------------- |
| `overwritten` | `array`  | A list of filenames that were successfully overwritten. |
| `msg`     | `string` | A confirmation message indicating success.              |

#### Status Codes

* `200 OK` - Files successfully overwritten
* `400 Bad Request` - Invalid parameters (e.g., missing `folder_id`, empty `files` list, or non-existent file)
* `403 Forbidden` - Missing or invalid API Key
* `404 Not Found` - Folder or file not found
* `500 Internal Server Error` - Server-side error


---

### 9. Batch Delete Files
**Method:** `DELETE`  
**Endpoint:** `/files`  
**Description:** Deletes multiple files from a Folder.

#### Request Parameters
| Parameter   | Type    | Required | Description |
|------------|---------|----------|-------------|
| `folder_id`  | `string`  | Yes | The unique ID of the Folder. |
| `file_ids`   | `array`   | Yes | A list of file IDs to be deleted. |

#### Request Body
```json
{
  "folder_id": "123",
  "file_ids": ["A1B2", "C3D4"]
}
```

#### Response Example
```json
{
  "deleted": ["A1B2", "C3D4"],
  "msg": "Files deleted successfully."
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `deleted`   | `array`  | A list of file IDs that were successfully deleted. |
| `msg`   | `string` | A confirmation message indicating success. |

#### Status Codes
- `200 OK` - Files successfully deleted  
- `400 Bad Request` - Invalid parameters (e.g., missing `folder_id`, empty `file_ids` list, or non-existent file)  
- `403 Forbidden` - Missing or invalid API Key  
- `404 Not Found` - Folder or file not found  
- `500 Internal Server Error` - Server-side error  




### 10. Modify File Lifecycle
**Method:** `PUT`  
**Endpoint:** `/files/{file_id}/lifecycle`  
**Description:** Modifies the start and end time of a file. If `start_at` is `null` or not provided, the file becomes active immediately. If `end_at` is `null` or not provided, the file remains active indefinitely.

#### Request Parameters
| Parameter   | Type    | Required | Description |
|------------|---------|----------|-------------|
| `file_id`    | `string`  | Yes | The unique ID of the file to update. |
| `start_at`   | `string`  | No | The start date of the file's availability (ISO 8601). If `null`, the file is available immediately. |
| `end_at`     | `string`  | No | The end date of the file's availability (ISO 8601). If `null`, the file remains available indefinitely. |

#### Request Body
```json
{
  "start_at": null,
  "end_at": "2024-06-01T00:00:00Z"
}
```

#### Response Example
```json
{
  "msg": "File lifecycle updated successfully."
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `msg`   | `string` | A confirmation message indicating success. |

#### Status Codes
- `200 OK` - Successfully updated  
- `400 Bad Request` - Invalid parameters (e.g., incorrect `file_id`, invalid `start_at`/`end_at` format)  
- `403 Forbidden` - Missing or invalid API Key  
- `404 Not Found` - File not found  
- `500 Internal Server Error` - Server-side error  

---

### 11. Retrieve System Prompt
**Method:** `GET`  
**Endpoint:** `/system/prompt`  
**Description:** Retrieves the system prompt configuration. This endpoint specifically targets the system type prompt which is unique in the system.

#### Response Example
```json
{
  "code": 200,
  "msg": "System prompt retrieved successfully.",
  "data": {
    "prompt": "System-wide configuration and settings",
    "created_at": "2024-01-01T00:00:00Z",
    "updated_at": "2024-02-25T10:00:00Z",
  }
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `code` | `integer` | HTTP status code of the response. |
| `msg`    | `string`  | A confirmation message indicating success. |
| `data`    | `object`  | The system prompt configuration. |
| `prompt`     | `string`  | The system prompt content. |
| `created_at` | `string`  | The timestamp when the system prompt was created (ISO 8601 format). |
| `updated_at` | `string`  | The timestamp of the last update (ISO 8601 format). |


#### Status Codes
- `200 OK` - Successful retrieval  
- `403 Forbidden` - Missing or invalid API Key  
- `500 Internal Server Error` - Server-side error  

---

### 12. Update System Prompt
**Method:** `PUT`  
**Endpoint:** `/system/prompt`  
**Description:** Updates the system prompt configuration. This endpoint can only modify the existing system prompt, as the system prompt is unique and cannot be created or deleted.

#### Request Body
```json
{
  "prompt": "Updated system-wide configuration and settings"
}
```

#### Request Body Parameters
| Parameter | Type    | Required | Description |
|----------|---------|----------|-------------|
| `prompt` | `string` | No | The new system prompt content. |

#### Response Example
```json
{
  "code": 200,
  "msg": "System prompt updated successfully."
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `code` | `integer` | HTTP status code of the response. |
| `msg`    | `string`  | A confirmation message indicating success. |

#### Status Codes
- `200 OK` - Successfully updated  
- `400 Bad Request` - Invalid parameters 
- `403 Forbidden` - Missing or invalid API Key  
- `500 Internal Server Error` - Server-side error  

---

## Error Handling
| Error Code | Description |
|------------|-------------|
| 400 Bad Request | The request contains invalid parameters or missing fields. |
| 403 Forbidden | Missing or invalid API Key. |
| 404 Not Found | The requested resource does not exist. |
| 500 Internal Server Error | Unexpected server-side error. |

## Use Cases
### Use Case 1: Retrieving a List of Available Knowledge Bases
A developer wants to retrieve all available knowledge bases in the system to allow users to select from them.

### Use Case 2: Uploading a Document to a Folder
A user wants to upload a new document to an existing Folder.




