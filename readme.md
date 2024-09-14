# File Management System

## Overview

The File Management System is a RESTful API built with Gin and Go. It includes functionalities for user authentication, file upload and management, retrieval, sharing, searching, and caching. The system uses JWT for authentication and Redis for caching file metadata.


## Setup Instructions

### Prerequisites

- Go (1.18 or later)
- MySQL
- Redis
- S3 Bucket


## API Reference

### 1.Register

```http
  POST /register
```
#### Request Body
```json
   {
      "email": "user@example.com",
      "password": "password123"
   }

```
#### Response Body
```json
   {
      "message": "User registered successfully"
   }
```
### 2.Login

```http
  POST /login
```
#### Request Body
```json
   {
      "email": "user@example.com",
      "password": "password123"
   }

```
#### Response Body
```json
   {
      "token": "jwt_token_here"
   }

```
### 3.Upload files

```http
   POST /protected/upload
```
| Header          | Type     | Description                |
| :--------       | :------- | :------------------------- |
| `Authorization` | `string` | **Bearer** jwt token       |

#### Request Body
```json
      Multipart form-data with a file field.

```
#### Response Body
```json
   {
      "file_id": 1,
      "public_url": "https://yourbucket.s3.amazonaws.com/file.jpg"
   }

```
### 4.Share files

```http
   GET /protected/files
```
| Header          | Type     | Description                |
| :--------       | :------- | :------------------------- |
| `Authorization` | `string` | **Bearer** jwt token       |


#### Response Body
```json
   
   {
      "files": [
         {
            "file_id": 1,
            "file_name": "file.jpg",
            "file_size": 123456,
            "upload_date": "2024-09-14T12:00:00Z",
            "s3_url": "https://yourbucket.s3.amazonaws.com/file.jpg"
         }
      ]
   }

```
```http
   GET /protected/share/
```
| Header          | Type     | Description                |
| :--------       | :------- | :------------------------- |
| `Authorization` | `string` | **Bearer** jwt token       |


#### Response Body
```json
   {
      "file_id": 1,
      "public_url": "https://yourbucket.s3.amazonaws.com/file.jpg"
   }

```
### 5.Search files

```http
   POST /protected/upload
```
| Header          | Type     | Description                |
| :--------       | :------- | :------------------------- |
| `Authorization` | `string` | **Bearer** jwt token       |

#### Request Body
| Parameters      | Type     | Description                |
| :--------       | :------- | :------------------------- |
| `name`          | `string` | name to search             |
| `date`          | `string` | date to search             |

#### Response Body
```json
      {
         "files": [
            {
               "file_id": 1,
               "file_name": "file.jpg",
               "file_size": 123456,
               "upload_date": "2024-09-14T12:00:00Z",
               "s3_url": "https://yourbucket.s3.amazonaws.com/file.jpg"
            }
         ]
      }


```
### 6.Profile

```http
   GET /protected/profile
```
| Header          | Type     | Description                |
| :--------       | :------- | :------------------------- |
| `Authorization` | `string` | **Bearer** jwt token       |

#### Response Body
```json
      {
         "user_id": 1,
         "message": "Welcome to your profile"
      }
```

## Caching
- File metadata is cached using Redis with a TTL of 5 minutes.
- Cache is invalidated and refreshed automatically upon metadata updates.

## Database Schema

### Users Table

```sql
    CREATE TABLE users (
        id SERIAL PRIMARY KEY,
        email VARCHAR(255) UNIQUE NOT NULL,
        password_hash TEXT NOT NULL
    );

```
### Files Table

```sql
    CREATE TABLE files (
        id SERIAL PRIMARY KEY,
        user_id INT REFERENCES users(id),
        file_name VARCHAR(255) NOT NULL,
        file_size BIGINT NOT NULL,
        upload_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
        s3_url TEXT NOT NULL
    );
```
### Database Indexing

```sql
    ALTER TABLE files
    ADD INDEX idx_file_name (file_name),
    ADD INDEX idx_upload_date (upload_date),
    ADD INDEX idx_file_type (file_type);

```
