# Giselle-cms-api-docs

## Overview
This document provides an overview of the API endpoints available for managing knowledge base documents in the backend service. The API is designed based on the Retrieval-Augmented Generation (RAG) framework to support document retrieval and response generation.

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

### 1. Retrieve Knowledge Bases
**Method:** `GET`  
**Endpoint:** `/sources`  
**Description:** Retrieves a list of available knowledge bases. Supports sorting and searching.

#### Request Parameters
| Parameter   | Type    | Required | Description |
|------------|---------|----------|-------------|
| `query`      | `string`  | No | Search keyword to filter knowledge bases by name or category. |
| `sort_by`    | `string`  | No | Field to sort by (e.g., `name`, `created_at`). Default: `name`. |
| `sort_order` | `string`  | No | Sorting order (`asc` for ascending, `desc` for descending). Default: `asc`. |

#### Response Example
```json
{
  "status_code": 200,
  "message": "Knowledge bases retrieved successfully.",
  "data": {
    "sources": [
      {
        "source_id": "123",
        "name": "HR Documents",
        "category": "Human Resources",
        "prompt": "Retrieve HR policies and guidelines",
        "created_at": "2024-02-20T12:34:56Z",
        "updated_at": "2024-02-25T10:00:00Z",
        "status": "active"
      },
      {
        "source_id": "456",
        "name": "Legal Policies",
        "category": "Legal",
        "prompt": "Access corporate legal compliance documents",
        "created_at": "2024-01-15T08:00:00Z",
        "updated_at": "2024-01-20T14:30:00Z",
        "status": "inactive"
      }
    ]
  }
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `status_code` | `integer` | HTTP status code of the response. |
| `message`    | `string`  | A confirmation message indicating success. |
| `data`       | `object`  | The response data containing the list of knowledge bases. |
| `sources`    | `array`   | A list of available knowledge bases. |
| `source_id`  | `string`  | The unique ID of the knowledge base. |
| `name`       | `string`  | The name of the knowledge base. |
| `category`   | `string`  | The category associated with the knowledge base. |
| `prompt`     | `string`  | A short description or recommended usage for the knowledge base. |
| `created_at` | `string`  | The timestamp when the knowledge base was created (ISO 8601 format). |
| `updated_at` | `string`  | The timestamp of the last update to the knowledge base (ISO 8601 format). |
| `status`     | `string`  | The current status of the knowledge base (`active` for enabled, `inactive` for disabled). |

#### Status Codes
- `200 OK` - Successful retrieval  
- `400 Bad Request` - Invalid parameters (e.g., incorrect `sort_by` field)  
- `403 Forbidden` - Missing or invalid API Key  
- `500 Internal Server Error` - Server-side error  

---
### 2. Update Knowledge Base Details
**Method:** `PUT`  
**Endpoint:** `/sources/{source_id}`  
**Description:** Updates the details of a specific knowledge base, including name, prompt, or status.

#### Request Parameters
| Parameter   | Type    | Required | Description |
|------------|---------|----------|-------------|
| `source_id`  | `string`  | Yes | The unique ID of the knowledge base to update. |

#### Request Body
```json
{
  "name": "Updated Knowledge Base Name",
  "prompt": "Updated prompt description",
  "status": "active"
}
```

#### Request Body Parameters
| Parameter | Type    | Required | Description |
|----------|---------|----------|-------------|
| `name`   | `string` | No | The new name of the knowledge base. |
| `prompt` | `string` | No | The new description or recommended usage of the knowledge base. |
| `status` | `string` | No | The status of the knowledge base (`active` for enabled, `inactive` for disabled). |

#### Response Example
```json
{
  "status_code": 200,
  "message": "Knowledge base details updated successfully."
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `status_code` | `integer` | HTTP status code of the response. |
| `message`    | `string`  | A confirmation message indicating success. |

#### Status Codes
- `200 OK` - Successfully updated  
- `400 Bad Request` - Invalid parameters (e.g., incorrect `status` value)  
- `403 Forbidden` - Missing or invalid API Key  
- `404 Not Found` - Knowledge base not found  
- `500 Internal Server Error` - Server-side error  

---




### 2. Update Knowledge Base Name
**Method:** `PUT`  
**Endpoint:** `/sources/{source_id}`  
**Description:** Updates the name of a specific knowledge base.

#### Request Parameters
| Parameter   | Type   | Required | Description |
|------------|--------|----------|-------------|
| `source_id` | `string` | Yes | The unique ID of the knowledge base to update. |

#### Request Body
```json
{
  "name": "New_Knowledge_Base_Name"
}
```
| Parameter | Type   | Required | Description |
|----------|--------|----------|-------------|
| `name`   | `string` | Yes | The new name of the knowledge base. |

#### Response Example
```json
{
  "message": "Knowledge base name updated successfully."
}
```

#### Status Codes
- `200 OK` - Successfully updated  
- `400 Bad Request` - Invalid parameters (e.g., missing `name` or invalid `source_id`)  
- `403 Forbidden` - Missing or invalid API Key  
- `404 Not Found` - Knowledge base not found  
- `500 Internal Server Error` - Server-side error  

---

### 3. Retrieve Files in a Knowledge Base
**Method:** `GET`  
**Endpoint:** `/files/source/{source_id}`  
**Description:** Retrieves all files in a specific knowledge base. Supports pagination, sorting, and search.

#### Request Parameters
| Parameter   | Type    | Required | Description |
|------------|---------|----------|-------------|
| `source_id`  | `string`  | Yes | The unique ID of the knowledge base. |
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
      "uploader": "user@example.com",
      "start_at": "2024-03-01T00:00:00Z",
      "end_at": "2024-06-01T00:00:00Z",
      "created_at": "2024-02-20T12:34:56Z",
      "updated_at": "2024-02-25T10:00:00Z"
    },
    {
      "file_id": "C3D4",
      "name": "guidelines.pdf",
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
| `files`       | `array`   | List of files in the specified knowledge base. |
| `file_id`     | `string`  | The unique ID of the file. |
| `name`    | `string`  | The name of the file. |
| `uploader`    | `string`  | The email or username of the user who uploaded the file. |
| `start_at`    | `string`  | The start date of the file's availability (ISO 8601). If `null`, the file is available immediately. |
| `end_at`      | `string`  | The end date of the file's availability (ISO 8601). If `null`, the file remains available indefinitely. |
| `created_at`  | `string`  | The timestamp when the file was created (ISO 8601 format). |
| `updated_at`  | `string`  | The timestamp of the last update to the file (ISO 8601 format). |

#### Status Codes
- `200 OK` - Successful retrieval  
- `400 Bad Request` - Invalid parameters (e.g., incorrect `source_id`, invalid `page` value)  
- `403 Forbidden` - Missing or invalid API Key  
- `404 Not Found` - Knowledge base not found  
- `500 Internal Server Error` - Server-side error  

---


### 4. Batch Check for Duplicate Filenames
**Method:** `POST`  
**Endpoint:** `/files/check_duplicates`  
**Description:** Checks if filenames already exist in a specified knowledge base.

#### Request Parameters
| Parameter   | Type    | Required | Description |
|------------|---------|----------|-------------|
| `source_id`  | `string`  | Yes | The unique ID of the knowledge base. |
| `names`  | `array`   | Yes | A list of filenames to check for duplicates. |

#### Request Body
```json
{
  "source_id": "123",
  "names": ["report.docx", "summary.pdf"]
}
```

#### Response Example
```json
{
  "duplicates": ["report.docx"]
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `duplicates` | `array`  | A list of filenames that already exist in the specified knowledge base. |

#### Status Codes
- `200 OK` - Successful check  
- `400 Bad Request` - Invalid parameters (e.g., missing `source_id`, empty `names` list)  
- `403 Forbidden` - Missing or invalid API Key  
- `404 Not Found` - Knowledge base not found  
- `500 Internal Server Error` - Server-side error  

---



### 5. Batch Upload Files
**Method:** `POST`  
**Endpoint:** `/upload`  
**Description:** Uploads multiple files to a specified knowledge base. All files in the batch must be uploaded by the same user and share the same availability period.

#### Request Parameters
| Parameter   | Type    | Required | Description |
|------------|---------|----------|-------------|
| `source_id`  | `string`  | Yes | The unique ID of the knowledge base. |
| `uploader`   | `string`  | Yes | The email or username of the user uploading the files. |
| `start_at`   | `string`  | Yes | The start date of the file's availability (ISO 8601). If `null`, the file is available immediately. |
| `end_at`     | `string`  | Yes | The end date of the file's availability (ISO 8601). If `null`, the file remains available indefinitely. |
| `files`      | `array`   | Yes | A list of files to be uploaded. |
| `name`   | `string`  | Yes | The name of the file being uploaded. |
| `content`    | `string`  | Yes | The base64-encoded file content. |

#### Request Body
```json
{
  "source_id": "123",
  "uploader": "user@example.com",
  "start_at": "2024-03-01T00:00:00Z",
  "end_at": "2024-06-01T00:00:00Z",
  "files": [
    {"name": "report.docx", "content": "base64_encoded_data"},
    {"name": "summary.pdf", "content": "base64_encoded_data"}
  ]
}
```

#### Response Example
```json
{
  "uploaded": ["report.docx", "summary.pdf"],
  "message": "Files uploaded successfully."
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `uploaded`  | `array`  | A list of filenames that were successfully uploaded. |
| `message`   | `string` | A confirmation message indicating success. |

#### Status Codes
- `201 Created` - Files successfully uploaded  
- `400 Bad Request` - Invalid parameters (e.g., missing `source_id`, empty `files` list, or incorrect `start_at`/`end_at` format)  
- `403 Forbidden` - Missing or invalid API Key  
- `500 Internal Server Error` - Server-side error  

---


### 6. Batch Overwrite Existing Files
**Method:** `PUT`  
**Endpoint:** `/files`  
**Description:** Overwrites existing files in a specified knowledge base. All files in the batch must be updated by the same user. The availability period (`start_at`, `end_at`) of the original file remains unchanged.

#### Request Parameters
| Parameter   | Type    | Required | Description |
|------------|---------|----------|-------------|
| `source_id`  | `string`  | Yes | The unique ID of the knowledge base. |
| `uploader`   | `string`  | Yes | The email or username of the user overwriting the files. |
| `files`      | `array`   | Yes | A list of files to be overwritten. |
| `name`   | `string`  | Yes | The name of the file being overwritten. |
| `content`    | `string`  | Yes | The base64-encoded updated file content. |

#### Request Body
```json
{
  "source_id": "123",
  "uploader": "user@example.com",
  "files": [
    {"name": "report.docx", "content": "updated_base64_encoded_data"}
  ]
}
```

#### Response Example
```json
{
  "overwritten": ["report.docx"],
  "message": "Files overwritten successfully."
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `overwritten` | `array`  | A list of filenames that were successfully overwritten. |
| `message`    | `string` | A confirmation message indicating success. |

#### Status Codes
- `200 OK` - Files successfully overwritten  
- `400 Bad Request` - Invalid parameters (e.g., missing `source_id`, empty `files` list, or non-existent file)  
- `403 Forbidden` - Missing or invalid API Key  
- `404 Not Found` - Knowledge base or file not found  
- `500 Internal Server Error` - Server-side error  


---

### 7. Batch Delete Files
**Method:** `DELETE`  
**Endpoint:** `/files`  
**Description:** Deletes multiple files from a knowledge base.

#### Request Parameters
| Parameter   | Type    | Required | Description |
|------------|---------|----------|-------------|
| `source_id`  | `string`  | Yes | The unique ID of the knowledge base. |
| `file_ids`   | `array`   | Yes | A list of file IDs to be deleted. |

#### Request Body
```json
{
  "source_id": "123",
  "file_ids": ["A1B2", "C3D4"]
}
```

#### Response Example
```json
{
  "deleted": ["A1B2", "C3D4"],
  "message": "Files deleted successfully."
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `deleted`   | `array`  | A list of file IDs that were successfully deleted. |
| `message`   | `string` | A confirmation message indicating success. |

#### Status Codes
- `200 OK` - Files successfully deleted  
- `400 Bad Request` - Invalid parameters (e.g., missing `source_id`, empty `file_ids` list, or non-existent file)  
- `403 Forbidden` - Missing or invalid API Key  
- `404 Not Found` - Knowledge base or file not found  
- `500 Internal Server Error` - Server-side error  




### 8. Modify File Lifecycle
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
  "message": "File lifecycle updated successfully."
}
```

#### Response Parameters
| Parameter   | Type    | Description |
|------------|---------|-------------|
| `message`   | `string` | A confirmation message indicating success. |

#### Status Codes
- `200 OK` - Successfully updated  
- `400 Bad Request` - Invalid parameters (e.g., incorrect `file_id`, invalid `start_at`/`end_at` format)  
- `403 Forbidden` - Missing or invalid API Key  
- `404 Not Found` - File not found  
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

### Use Case 2: Uploading a Document to a Knowledge Base
A user wants to upload a new document to an existing knowledge base.

