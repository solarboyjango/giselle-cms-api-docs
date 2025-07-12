# Giselle-cms-api-docs

## Overview
This document provides an overview of the API endpoints available for managing Folder documents in the backend service. The API is designed based on the Retrieval-Augmented Generation (RAG) framework to support document retrieval and response generation.

## Database Auto-Initialization
The application automatically initializes the database on startup:
- **Collections**: Creates all necessary collections (`folders`, `files`, `vectorstore`, `etl_processors`, `etl_history`)
- **Indexes**: Creates all required indexes with unique names to avoid conflicts
- **System Data**: Initializes system knowledge base if it doesn't exist
- **Safety**: Uses safe index creation that skips existing indexes

**No manual database setup required** - the application handles everything automatically on first run and subsequent deployments.

## Authentication & Authorization
- **Authentication Method**: API Key
- **API Key Format**: `cms_12345`
- **Usage**: Clients must include the API Key in the request header.

**Example Request Header:**
```http
GET /secure-endpoint
X-API-Key: cms_12345
```

## Time Format
All timestamps in the API responses are in Unix timestamp format (seconds since epoch):
- Format: Integer number of seconds since January 1, 1970, 00:00:00 UTC
- Example: `1710403200` (represents 2024-03-14T10:30:00.000Z)
- All timestamps are in UTC timezone
- All timestamps are in seconds (not milliseconds)
- Frontend applications can easily convert these timestamps to local timezone for display

For API requests, the following date formats are supported:
1. Unix timestamp (recommended):
   - Example: `1710403200`
   - Represents seconds since epoch in UTC
   - Must be greater than 0 (1970-01-01 00:00:00)
   - Must be less than 253402300799 (9999-12-31 23:59:59)
   - Note: Frontend should convert milliseconds to seconds (divide by 1000) before sending

2. Simple date format: `YYYY-MM-DD`
   - Example: `2025-03-01`
   - When using this format, the time will be set to 00:00:00 UTC of the specified date
   - The system will automatically convert this to a Unix timestamp

3. ISO 8601 format: `YYYY-MM-DDThh:mm:ssZ`
   - Example: `2025-03-01T00:00:00Z`
   - Must include timezone indicator (Z for UTC)
   - The system will automatically convert this to a Unix timestamp

Example API requests with different date formats:
```bash
# Using Unix timestamp (in seconds)
curl 'https://api.example.com/files/folder/123?start_at_before=1710403200&end_at_after=1709251200' \
  -H 'X-API-Key: cms_12345'

# Using simple date format
curl 'https://api.example.com/files/folder/123?start_at_before=2025-06-01&end_at_after=2025-03-01' \
  -H 'X-API-Key: cms_12345'

# Using ISO 8601 format
curl 'https://api.example.com/files/folder/123?start_at_before=2025-06-01T00:00:00Z&end_at_after=2025-03-01T00:00:00Z' \
  -H 'X-API-Key: cms_12345'
```

## API Endpoints

### 1. Retrieve Folders
**Method:** `GET`  
**Endpoint:** `/folders`  
**Description:** Retrieves a list of available Folders. Supports sorting, searching, and pagination.

#### Request Parameters
| Parameter   | Type    | Required | Description |
|------------|---------|----------|-------------|
| `{field}`    | `string`  | No | Search by field value. Available fields: `name`, `prompt`, `status`, `type`, `folder_id`. Example: `name=知識庫6` or `status=active`. |
| `sort_field` | `string`  | No | Field to sort by (e.g., `name`, `created_at`). Default: `name`. |
| `sort_order` | `string`  | No | Sorting order (`ascend` or `descend`). Default: `ascend`. |
| `page`       | `integer` | No | The page number. Default: `1`. |
| `page_size`  | `integer` | No | Number of items per page. Default: `10`. |

#### Example Request
```bash
curl 'https://api.example.com/folders?name=知識庫6&page=1&page_size=10&sort_field=name&sort_order=descend' \
  -H 'X-API-Key: cms_12345'
```

#### Response Example
```json
{
  "code": 200,
  "msg": "Folders retrieved successfully.",
  "page": 1,
  "page_size": 10,
  "total_count": 25,
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
| `page`        | `integer` | Current page number. |
| `page_size`   | `integer` | Number of folders returned per page. |
| `total_count` | `integer` | Total number of folders matching the query. |
| `data`    | `object`  | Response data. |
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
- `400 Bad Request` - Invalid parameters (e.g., incorrect `sort_field` or `sort_order`)  
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
| `status` | `string` | No  | Status (`active` or `inactive`). Default: `inactive`. |
| `type`   | `string` | Yes | Must be `knowledge_base`. Cannot create `system` type. |

#### Response Example
```json
{
  "code": 200,
  "msg": "Folder created successfully.",
  "data": {
    "folder_id": "789"
  }
}
```

#### Response Parameters
| Parameter     | Type    | Description |
|---------------|---------|-------------|
| `code` | integer | HTTP status code. |
| `msg`     | string  | Confirmation message. |
| `data`    | object  | Response data. |
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
**Description:** Updates the details of a specific Folder, including name, prompt, or status. When updating the status to "active", the system will check if the maximum number of active knowledge bases has been reached.

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
- `409 Conflict` - Maximum number of active knowledge bases reached  
- `500 Internal Server Error` - Server-side error  

#### Error Response Examples

1. Invalid Parameters:
```json
{
  "code": 400,
  "msg": "Invalid status value. Must be either 'active' or 'inactive'",
  "data": null
}
```

2. Maximum Active Knowledge Bases Reached:
```json
{
  "code": 409,
  "msg": "Maximum number of active knowledge bases reached",
  "data": null
}
```

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
| `{field}`    | `string`  | No | Search by field value. Available fields: `name`, `uploader`, `status`. Example: `name=policy.docx` or `status=processing`. |
| `start_at_after` | `string` | No | Filter files with start_at after this date (ISO 8601 format). Example: `2024-01-01T00:00:00Z`. |
| `start_at_before` | `string` | No | Filter files with start_at before this date (ISO 8601 format). Example: `2024-12-31T23:59:59Z`. |
| `end_at_after` | `string` | No | Filter files with end_at after this date (ISO 8601 format). Example: `2024-01-01T00:00:00Z`. |
| `end_at_before` | `string` | No | Filter files with end_at before this date (ISO 8601 format). Example: `2024-12-31T23:59:59Z`. |
| `sort_field` | `string`  | No | Field to sort by (e.g., `name`, `created_at`). Default: `name`. |
| `sort_order` | `string`  | No | Sorting order (`ascend` or `descend`). Default: `ascend`. |
| `page`       | `integer` | No | The page number. Default: `1`. |
| `page_size`  | `integer` | No | Number of items per page. Default: `10`. |

#### Example Request
```bash
# Search by name and status
curl 'https://api.example.com/files/folder/123?name=policy.docx&status=processing&page=1&page_size=10' \
  -H 'X-API-Key: cms_12345'

# Search by time range
curl 'https://api.example.com/files/folder/123?start_at_after=2024-01-01T00:00:00Z&start_at_before=2024-12-31T23:59:59Z&page=1&page_size=10' \
  -H 'X-API-Key: cms_12345'
```

#### Notes
- `folder_id` is not searchable as it is already specified in the URL path
- `file_id` is not searchable as it is a system-generated unique identifier. To retrieve a specific file, use the dedicated endpoint `/files/{file_id}`
- Time range search can be used to find files that are active during a specific period
- All timestamps should be in ISO 8601 format with UTC timezone

#### Response Example
```json
{
  "code": 200,
  "msg": "Files retrieved successfully.",
  "page": 1,
  "page_size": 10,
  "total_count": 25,
  "data": {
    "files": [
      {
        "file_id": "A1B2",
        "name": "policy.docx",
        "url": "https://markdownlivepreview-1.com",
        "uploader": "user@example.com",
        "start_at": 1710403200,
        "end_at": 1717200000,
        "created_at": 1708425296,
        "updated_at": 1708848000,
        "status": "success"
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
| `page`        | `integer` | Current page number. |
| `page_size`   | `integer` | Number of files returned per page. |
| `total_count` | `integer` | Total number of files matching the query. |
| `data`    | `object`  | Response data. |
| `files`       | `array`   | List of files in the specified Folder. |
| `file_id`     | `string`  | Unique identifier of the file. |
| `name`        | `string`  | Name of the file. |
| `url`         | `string`  | Temporary access URL for the file. |
| `uploader`    | `string`  | Email or username of the user who uploaded the file. |
| `start_at`    | `string`  | Start date of file availability (ISO 8601 format). |
| `end_at`      | `string`  | End date of file availability (ISO 8601 format). |
| `created_at`  | `string`  | Timestamp when the file was created (ISO 8601 format). |
| `updated_at`  | `string`  | Timestamp of the last update (ISO 8601 format). |
| `status`      | `string`  | Current status of the file (`processing`, `success` or `failed`). |

#### Status Codes
- `200 OK` - Successful retrieval  
- `400 Bad Request` - Invalid parameters  
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
  "code": 200,
  "msg": "Files checked successfully.",
  "data": {
    "duplicates": ["report.docx"],
    "invalid_extensions": ["script.exe"]
  }
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `code` | `integer` | HTTP status code of the response. |
| `msg`    | `string`  | A confirmation message indicating success. |
| `data`    | `object`  | Response data. |
| `duplicates`         | `array` | Filenames that already exist in the folder. |
| `invalid_extensions` | `array` | Filenames with unsupported extensions. |

#### Status Codes
- `200 OK` - Validation completed successfully
- `400 Bad Request` - Missing or invalid parameters
- `403 Forbidden` - Invalid API Key
- `404 Not Found` - Folder not found
- `500 Internal Server Error` - Server error during check

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
| `start_at`  | `string` | Yes      | The start date of the file's availability. Supports both YYYY-MM-DD and ISO 8601 formats. If null, the file is available immediately. |
| `end_at`    | `string` | Yes      | The end date of the file's availability. Supports both YYYY-MM-DD and ISO 8601 formats. If `null`, the file remains available indefinitely. |
| `status`    | `string` | No       | The initial status of the files (`processing`, `success`, or `failed`). Default: `processing`. |
| `files`     | `file[]` | Yes      | A list of files to be uploaded. Multiple files should be submitted using the same key name.   |

#### Example Request
```bash
# Using simple date format
curl -X POST 'https://api.example.com/upload' \
  -H 'X-API-Key: cms_12345' \
  -F 'folder_id=123' \
  -F 'uploader=user@example.com' \
  -F 'start_at=2025-03-01' \
  -F 'end_at=2025-06-01' \
  -F 'files=@file1.pdf' \
  -F 'files=@file2.pdf'

# Using ISO 8601 format
curl -X POST 'https://api.example.com/upload' \
  -H 'X-API-Key: cms_12345' \
  -F 'folder_id=123' \
  -F 'uploader=user@example.com' \
  -F 'start_at=2025-03-01T00:00:00Z' \
  -F 'end_at=2025-06-01T00:00:00Z' \
  -F 'files=@file1.pdf' \
  -F 'files=@file2.pdf'
```

#### Response Example
```json
{
  "code": 200,
  "msg": "Files uploaded successfully.",
  "data": {
    "uploaded": [
      {
        "name": "report.docx",
        "file_id": "123456"
      },
      {
        "name": "summary.pdf",
        "file_id": "1234567"
      }
    ],
    "errors": null
  }
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `code` | `integer` | HTTP status code of the response. |
| `msg`    | `string`  | A confirmation message indicating success. |
| `data`    | `object`  | Response data. |
| `uploaded` | `array`  | A list of successfully uploaded files, each containing `name` and `file_id`. |
| `errors`   | `array`  | A list of error messages if any uploads failed. |

#### Status Codes
- `200 Created` - Files successfully uploaded
- `207 Multi-Status` - Some files uploaded successfully, some failed
- `400 Bad Request` - Invalid parameters
- `403 Forbidden` - Missing or invalid API Key
- `500 Internal Server Error` - Server-side error

---

### 8. Batch Overwrite Existing Files (Deprecation Notice)
**Method:** `PUT`  
**Endpoint:** `/files`  
**Content-Type:** `multipart/form-data`  
**Description:** Overwrites existing files in a specified Folder. Files can be updated by any user (not restricted to the original uploader). If `start_at` and `end_at` are not provided, the system will retain the previous availability settings of each file.

> **Deprecation Notice:** This endpoint is deprecated and will be removed in a future version. Please use the "Batch Upload Files" endpoint (`POST /upload`) instead, which now supports automatic overwriting of existing files while preserving their original creation timestamps.

#### Request Parameters
| Parameter   | Type     | Required | Description  |
| ----------- | -------- | -------- | ------------ |
| `folder_id` | `string` | Yes | The unique ID of the Folder. |
| `uploader`  | `string` | Yes | The email or username of the user overwriting the files. |
| `files`     | `file[]` | Yes | A list of files to be overwritten. The `name` of each uploaded file must match an existing file in the folder. |
| `start_at`  | `string` | No  | (Optional) New start date of availability. Supports both YYYY-MM-DD and ISO 8601 formats. If `null` provided, the original setting will be kept. |
| `end_at`    | `string` | No  | (Optional) New end date of availability. Supports both YYYY-MM-DD and ISO 8601 formats. If `null` provided, the original setting will be kept. |
| `status`    | `string` | No  | (Optional) New status of the files (`processing`, `success`, or `failed`). Default: `processing`. |

#### Example Request
```bash
# Using simple date format
curl -X PUT 'https://api.example.com/files' \
  -H 'X-API-Key: cms_12345' \
  -F 'folder_id=123' \
  -F 'uploader=user@example.com' \
  -F 'start_at=2025-03-01' \
  -F 'end_at=2025-06-01' \
  -F 'files=@file1.pdf' \
  -F 'files=@file2.pdf'

# Using ISO 8601 format
curl -X PUT 'https://api.example.com/files' \
  -H 'X-API-Key: cms_12345' \
  -F 'folder_id=123' \
  -F 'uploader=user@example.com' \
  -F 'start_at=2025-03-01T00:00:00Z' \
  -F 'end_at=2025-06-01T00:00:00Z' \
  -F 'files=@file1.pdf' \
  -F 'files=@file2.pdf'
```

#### Response Example
```json
{
  "code": 200,
  "msg": "Files overwritten successfully.",
  "data": {
    "overwritten": ["report.docx"]
  }
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `code` | `integer` | HTTP status code of the response. |
| `msg`    | `string`  | A confirmation message indicating success. |
| `data`    | `object`  | Response data. |
| `overwritten` | `array`  | A list of filenames that were successfully overwritten. |

#### Status Codes
- `200 OK` - Files successfully overwritten
- `400 Bad Request` - Invalid parameters
- `403 Forbidden` - Missing or invalid API Key
- `404 Not Found` - Folder or file not found
- `500 Internal Server Error` - Server-side error

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
  "code": 200,
  "msg": "Successfully deleted 2 files.",
  "data": {
    "deleted": ["A1B2", "C3D4"]
  }
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `code` | `integer` | HTTP status code of the response. |
| `msg`    | `string`  | A confirmation message indicating success. |
| `data`    | `object`  | Response data. |
| `deleted`   | `array`  | A list of file IDs that were successfully deleted. |

#### Status Codes
- `200 OK` - Files successfully deleted  
- `400 Bad Request` - Invalid parameters  
- `403 Forbidden` - Missing or invalid API Key  
- `404 Not Found` - Folder or file not found  
- `500 Internal Server Error` - Server-side error  

---

### 10. Modify File Lifecycle
**Method:** `PUT`  
**Endpoint:** `/files/lifecycle`  
**Description:** Modifies the start and end time of multiple files in a folder. If `start_at` is `null` or not provided, the files become active immediately. If `end_at` is `null` or not provided, the files remain active indefinitely.

#### Request Parameters
| Parameter   | Type    | Required | Description |
|------------|---------|----------|-------------|
| `folder_id`  | `string`  | Yes | The unique ID of the folder containing the files. |
| `file_ids`   | `array`   | Yes | A list of file IDs to update. |
| `start_at`   | `string`  | No | The start date of the files' availability. Supports both YYYY-MM-DD and ISO 8601 formats. If `null`, the files are available immediately. |
| `end_at`     | `string`  | No | The end date of the files' availability. Supports both YYYY-MM-DD and ISO 8601 formats. If `null`, the files remain available indefinitely. |

#### Request Body
```json
{
  "folder_id": "123",
  "file_ids": ["A1B2", "C3D4"],
  "start_at": 1710403200,
  "end_at": 1717200000
}
```

#### Example Request
```bash
# Using Unix timestamp
curl -X PUT 'https://api.example.com/files/lifecycle' \
  -H 'X-API-Key: cms_12345' \
  -H 'Content-Type: application/json' \
  -d '{
    "folder_id": "123",
    "file_ids": ["A1B2", "C3D4"],
    "start_at": 1710403200,
    "end_at": 1717200000
  }'

# Using simple date format
curl -X PUT 'https://api.example.com/files/lifecycle' \
  -H 'X-API-Key: cms_12345' \
  -H 'Content-Type: application/json' \
  -d '{
    "folder_id": "123",
    "file_ids": ["A1B2", "C3D4"],
    "start_at": "2025-03-01",
    "end_at": "2025-06-01"
  }'
```

#### Response Example
```json
{
  "code": 200,
  "msg": "Files lifecycle updated successfully.",
  "data": {
    "updated": ["A1B2", "C3D4"]
  }
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `code` | `integer` | HTTP status code of the response. |
| `msg`    | `string`  | A confirmation message indicating success. |
| `data`    | `object`  | Response data. |
| `updated`  | `array`   | A list of file IDs that were successfully updated. |

#### Status Codes
- `200 OK` - Successfully updated  
- `400 Bad Request` - Invalid parameters  
- `403 Forbidden` - Missing or invalid API Key  
- `404 Not Found` - Folder not found or no files were updated  
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
    "created_at": 1704067200,
    "updated_at": 1708848000
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

### 13. Process Single File ETL
**Method:** `POST`  
**Endpoint:** `/etl/files/{folder_id}/{file_id}`  
**Description:** Processes a single file for ETL (Extract, Transform, Load) operations. This endpoint allows direct processing of a specific file without going through the automatic ETL queue.

#### Request Parameters
| Parameter   | Type    | Required | Description |
|------------|---------|----------|-------------|
| `folder_id`  | `string`  | Yes | The unique ID of the Folder containing the file. |
| `file_id`    | `string`  | Yes | The unique ID of the file to process. |

#### Example Request
```bash
curl -X POST 'https://api.example.com/etl/files/123/456' \
  -H 'X-API-Key: cms_12345'
```

#### Response Example
```json
{
  "code": 200,
  "msg": "File processing started successfully",
  "data": {
    "file_id": "456",
    "status": "success",
    "vector_count": 150,
    "is_available": true
  }
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `code` | `integer` | HTTP status code of the response. |
| `msg`    | `string`  | A confirmation message indicating success. |
| `data`    | `object`  | Response data. |
| `file_id`     | `string`  | The ID of the processed file. |
| `status`      | `string`  | Processing status (`success` or `failed`). |
| `vector_count` | `integer` | Number of vectors generated from the file. |
| `is_available` | `boolean` | Whether the file is currently available based on its lifecycle settings. |

#### Status Codes
- `200 OK` - File processing started successfully
- `400 Bad Request` - Invalid parameters or file already being processed
- `403 Forbidden` - Missing or invalid API Key
- `404 Not Found` - File not found in the specified folder
- `500 Internal Server Error` - Server-side error

#### Error Response Examples

1. File Already Being Processed:
```json
{
  "code": 200,
  "msg": "File is already being processed",
  "data": {
    "file_id": "456",
    "status": "processed"
  }
}
```

2. File Not Found:
```json
{
  "code": 404,
  "msg": "File 456 not found in folder 123",
  "data": null
}
```

---

### 14. ETL Ping (Auto Processing)
**Method:** `POST`  
**Endpoint:** `/etl/ping`  
**Description:** Triggers automatic ETL processing by starting an idle processor to handle pending files. This endpoint is designed to be called periodically to maintain continuous ETL processing.

#### Example Request
```bash
curl -X POST 'https://api.example.com/etl/ping' \
  -H 'X-API-Key: cms_12345'
```

#### Response Example
```json
{
  "code": 200,
  "msg": "ETL ping completed. Started 1 ETL processors",
  "data": {
    "running_count": 1,
    "processors_id": 1
  }
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `code` | `integer` | HTTP status code of the response. |
| `msg`    | `string`  | A confirmation message indicating success. |
| `data`    | `object`  | Response data. |
| `running_count` | `integer` | Number of processors currently running ETL operations. |
| `processors_id` | `integer` | ID of the processor that was started (null if no idle processor was available). |

#### Status Codes
- `200 OK` - ETL ping completed successfully
- `403 Forbidden` - Missing or invalid API Key
- `500 Internal Server Error` - Server-side error

#### Response Examples

1. When an idle processor is available:
```json
{
  "code": 200,
  "msg": "ETL ping completed. Started 1 ETL processors",
  "data": {
    "running_count": 1,
    "processors_id": 1
  }
}
```

2. When no idle processor is available:
```json
{
  "code": 200,
  "msg": "ETL ping completed. Started 0 ETL processors",
  "data": {
    "running_count": 2,
    "processors_id": null
  }
}
```

#### Notes
- This endpoint is designed to be called periodically (e.g., every minute) to maintain continuous ETL processing
- Only one processor is started per ping to avoid overwhelming the system
- The processor will automatically handle multiple files until no more pending files are available
- Processors work independently and can handle files concurrently

---

### 15. Retrieve Chat Logs by Conversation
**Method:** `GET`  
**Endpoint:** `/chatlog/conversation/{conversation_id}/channel/{channel_id}/user/{user_id}`  
**Description:** Retrieves chat logs for a specific conversation, extracting metadata and formatting the response for easy consumption.

#### Request Parameters
| Parameter   | Type    | Required | Description |
|------------|---------|----------|-------------|
| `conversation_id` | `string` | Yes | The unique conversation ID to retrieve logs for. |
| `channel_id` | `string` | Yes | The channel identifier (e.g., "webchat", "directline"). |
| `user_id` | `string` | Yes | The unique user identifier. |

#### Example Request
```bash
curl -X GET "https://api.example.com/chatlog/conversation/1752304746/channel/webchat/user/febf6976-d245-4490-a38a-7fd9e905e3df" \
  -H "X-API-Key: cms_12345" \
  -H "Content-Type: application/json"
```

#### Response Example
```json
{
  "code": 200,
  "msg": "Chat logs retrieved successfully",
  "data": [
    {
      "conversation_id": "1752304746",
      "channel_id": "webchat",
      "created_at": 1752275951,
      "meta": [
        {
          "title": "G492.txt"
        },
        {
          "title": "114期導入文創打開市場敲門磚.pdf"
        },
        {
          "title": "132期運用ChatGPT生成文章的步驟.pdf"
        }
      ]
    }
  ]
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `code` | `integer` | HTTP status code of the response. |
| `msg` | `string` | A confirmation message indicating success. |
| `data` | `array` | Array of chat log summaries. |
| `conversation_id` | `string` | The conversation identifier. |
| `channel_id` | `string` | The channel identifier. |
| `created_at` | `integer` | Unix timestamp when the chat log was created. |
| `meta` | `array` | Array of metadata objects containing document titles. |
| `title` | `string` | The title of the referenced document. |

#### Status Codes
- `200 OK` - Chat logs retrieved successfully
- `403 Forbidden` - Missing or invalid API Key
- `500 Internal Server Error` - Server-side error

#### Notes
- Only chat logs with valid metadata are returned
- The `meta` field contains extracted document titles from the original chat log metadata
- Timestamps are returned in Unix timestamp format (seconds since epoch)
- Documents without metadata are automatically filtered out
- The API supports multiple metadata objects per chat log entry

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
