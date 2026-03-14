This script creates a **simple class to manage uploads and downloads to Amazon S3** using Python. It wraps the AWS S3 functionality into a reusable **`S3Manager` class** so you don’t have to repeat the same code every time you interact with S3.


---

# 1️⃣ Imports

```python
import os
import boto3
from botocore.exceptions import ClientError
import logging
```

### `os`

Used for working with file paths.

Example:

```python
os.path.basename("folder/loops.py")
```

Output:

```
loops.py
```

---

### `boto3`

The **AWS SDK for Python**.
It allows Python programs to interact with AWS services like:

* S3 (storage)
* EC2
* DynamoDB
* Lambda

---

### `ClientError`

An exception type raised when AWS operations fail.

Examples of failure:

* Wrong bucket name
* No permissions
* File does not exist
* Network issues

---

### `logging`

Used to log errors instead of printing them.

Example:

```python
logging.error(e)
```

---

# 2️⃣ Creating the Class

```python
class S3Manager:
```

This defines a **class**.

A class is a **blueprint for creating objects**.

Here the class will handle:

* uploading files to S3
* downloading files from S3

---

# 3️⃣ Constructor (`__init__`)

```python
def __init__(self):
    self.client = boto3.client("s3")
    self.bucket = "DEFAULT_BUCKET_NAME"
```

This function runs **automatically when the class is created**.

Example:

```python
manager = S3Manager()
```

When that runs:

### `self.client`

Creates an S3 client connection.

Equivalent to:

```python
boto3.client("s3")
```

---

### `self.bucket`

Sets a **default bucket name**.

```
DEFAULT_BUCKET_NAME
```

But this can be overridden later.

---

# 4️⃣ Upload Function

```python
def upload_file(self, filename, bucket=None, object_name=None) -> bool:
```

Parameters:

| Parameter     | Meaning                    |
| ------------- | -------------------------- |
| `filename`    | local file path            |
| `bucket`      | optional bucket name       |
| `object_name` | name of the file inside S3 |

`-> bool` means the function returns **True or False**.

---

## Changing Bucket Dynamically

```python
if bucket:
    self.bucket = bucket
```

If a bucket is passed, replace the default bucket.

Example:

```python
manager.upload_file("file.txt", "my-bucket")
```

Now:

```
self.bucket = "my-bucket"
```

---

## Default Object Name

```python
if object_name is None:
    object_name = os.path.basename(filename)
```

If no object name is given:

Use the **file name only**.

Example:

```
filename = "/home/user/loops.py"
```

Result:

```
object_name = loops.py
```

---

# 5️⃣ Uploading the File

```python
self.client.upload_file(filename, self.bucket, object_name)
```

This sends the file to S3.

Equivalent CLI command:

```
aws s3 cp loops.py s3://bucket-name/
```

Parameters:

| Parameter     | Meaning        |
| ------------- | -------------- |
| `filename`    | local file     |
| `self.bucket` | S3 bucket      |
| `object_name` | name inside S3 |

---

# 6️⃣ Success Message

```python
print("Upload successful")
```

---

# 7️⃣ Error Handling

```python
except ClientError as e:
    logging.error(e)
    return False
```

If something fails:

* Log the error
* Return `False`

---

# 8️⃣ Download Function

```python
def download_file(self, filename, bucket=None):
```

This downloads a file from S3.

Parameters:

| Parameter  | Meaning               |
| ---------- | --------------------- |
| `filename` | where to save locally |
| `bucket`   | optional bucket       |

---

## Change Bucket

```python
if bucket:
    self.bucket = bucket
```

Allows using a different bucket.

---

# 9️⃣ Get File Key

```python
key = os.path.basename(filename)
```

The **S3 key** is the file name.

Example:

```
filename = pdf_2014_1.pdf
key = pdf_2014_1.pdf
```

---

# 🔟 Download File

```python
self.client.download_file(self.bucket, key, filename)
```

This downloads from S3.

Equivalent CLI command:

```
aws s3 cp s3://bucket/file.pdf file.pdf
```

Parameters:

| Parameter | Meaning           |
| --------- | ----------------- |
| bucket    | S3 bucket         |
| key       | file in S3        |
| filename  | local destination |

---

# 11️⃣ Create the Object

```python
manager = S3Manager()
```

Creates an instance of the class.

Now you can call:

```
manager.upload_file()
manager.download_file()
```

---

# 12️⃣ Upload Example

```python
manager.upload_file("loops.py")
```

Uploads:

```
loops.py
```

to:

```
DEFAULT_BUCKET_NAME
```

---

# 13️⃣ Download Example

```python
manager.download_file("pdf_2014_1.pdf", "pipeline-enem-bucket")
```

Downloads:

```
pdf_2014_1.pdf
```

from bucket:

```
pipeline-enem-bucket
```

and saves it locally as:

```
pdf_2014_1.pdf
```

---

# 🔄 Full Flow

```
Start
  ↓
Create S3Manager()
  ↓
Create S3 Client
  ↓
Upload File
  ↓
Catch Errors
  ↓
Download File
```

---

# ⚠️ Important Requirements

Before running this script you must configure AWS credentials:

```
aws configure
```

Or set environment variables:

```
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_REGION
```

---

