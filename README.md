# giselle-cms-api-docs


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
**Description:** Retrieves a list of available knowledge bases.  
**Request Parameters:** None  
**Response Example:**
```json
{
  "sources": [
    {"source_id": "123", "name": "HR Documents"},
    {"source_id": "456", "name": "Legal Policies"}
  ]
}
```
**Status Codes:**  
- `200 OK` - Successful retrieval
- `403 Forbidden` - Missing or invalid API Key
- `500 Internal Server Error` - Server-side error

---

### 2. Retrieve Files in a Knowledge Base
**Method:** `GET`  
**Endpoint:** `/files/source/{source_id}`  
**Description:** Retrieves all files in a specific knowledge base. Supports pagination, sorting, and search.

**Request Parameters:**
- `source_id` *(string, required)* - The knowledge base ID.
- `page` *(integer, optional)* - The page number.
- `page_size` *(integer, optional)* - Number of items per page.
- `query` *(string, optional)* - Search keyword.
- `sort_by` *(string, optional)* - Field to sort by (e.g., `filename`, `created_at`).
- `sort_order` *(string, optional)* - Sorting order (`asc` for ascending, `desc` for descending). Default: `asc`.

**Example Request:**
```
GET /files/source/123?page=1&page_size=10&query=policy&sort_by=created_at&sort_order=desc
X-API-Key: cms_12345
```

**Response Example:**
```json
{
  "total_count": 25,
  "page": 1,
  "page_size": 10,
  "files": [
    {"file_id": "A1B2", "filename": "policy.docx", "created_at": "2024-02-20T12:34:56Z"},
    {"file_id": "C3D4", "filename": "guidelines.pdf", "created_at": "2024-01-15T08:00:00Z"}
  ]
}
```
**Status Codes:**  
- `200 OK` - Successful retrieval
- `403 Forbidden` - Missing or invalid API Key
- `500 Internal Server Error` - Server-side error

---

### 3. Check for Duplicate Filenames
**Method:** `POST`  
**Endpoint:** `/files/check_duplicates`  
**Description:** Checks if filenames already exist in a specified knowledge base.

**Request Headers:**
- `X-API-Key: cms_12345`

**Request Body:**
```json
{
  "source_id": "123",
  "filenames": ["report.docx", "summary.pdf"]
}
```

**Response Example:**
```json
{
  "duplicates": ["report.docx"]
}
```
**Status Codes:**  
- `200 OK` - Successful check
- `403 Forbidden` - Missing or invalid API Key
- `400 Bad Request` - Missing or invalid parameters
- `500 Internal Server Error` - Server-side error

---

### 4. Upload Files
**Method:** `POST`  
**Endpoint:** `/upload`  
**Description:** Uploads multiple files to a specified knowledge base.

**Request Headers:**
- `X-API-Key: cms_12345`

**Request Body:**
```json
{
  "source_id": "123",
  "files": [
    {"filename": "report.docx", "content": "base64_encoded_data"},
    {"filename": "summary.pdf", "content": "base64_encoded_data"}
  ]
}
```

**Response Example:**
```json
{
  "uploaded": ["report.docx", "summary.pdf"]
}
```
**Status Codes:**  
- `201 Created` - Files successfully uploaded
- `403 Forbidden` - Missing or invalid API Key
- `400 Bad Request` - Invalid request format
- `500 Internal Server Error` - Server-side error

---

### 5. Overwrite Existing Files
**Method:** `PUT`  
**Endpoint:** `/files`  
**Description:** Overwrites existing files in a specified knowledge base.

**Request Headers:**
- `X-API-Key: cms_12345`

**Request Body:**
```json
{
  "source_id": "123",
  "files": [
    {"filename": "report.docx", "content": "updated_base64_encoded_data"}
  ]
}
```

**Response Example:**
```json
{
  "message": "Batch overwrite successful",
  "success": ["report.docx"]
}
```
**Status Codes:**  
- `200 OK` - Files successfully overwritten
- `403 Forbidden` - Missing or invalid API Key
- `400 Bad Request` - Invalid request format
- `500 Internal Server Error` - Server-side error

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

**Steps:**
1. Send a `GET /sources` request with `X-API-Key: cms_12345`.
2. Receive a list of available knowledge bases.

### Use Case 2: Uploading a Document to a Knowledge Base
A user wants to upload a new document to an existing knowledge base.

**Steps:**
1. Send a `POST /upload` request with `source_id` and file data.
2. The system validates the file and uploads it.
3. Receive a response confirming successful upload.

---

## Next Steps
- Implement API Key authentication in FastAPI middleware.
- Define monitoring and logging for API access.
- Expand API functionality as needed.

