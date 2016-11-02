Description
===========

lambda-transferer is a lambda function to collect files via FTP into an AWS S3 bucket

Origin of the project
=====================

The vast majority of IT projects I have been involved with starts by collecting a set of data from a customer, on a regular basis or as a one off.
Each project implements its own solution to collect the data. I want to provide a set of standard tools to achieve this in a cheap and effective manner.

Version
=======
0.0.1 - unstable - currently being developed

Testing
=======
I strongly recommend to use node-lamba for local development

```
npm install -g node-lambda
node-lambda setup
node-lambda run
```

API
===

Event
------
* path - _string_ - the remote path of files to be collected
* mask - _string_ - Regular expression - only filenames matching the expression will be collected
* source - _object_ - with the following properties:
    * type - _string_ - only ftp supported for now
    * host - _string_ - The hostname or IP address of the FTP server. **Default:** 'localhost'
    * port - _integer_ - The port of the FTP server. **Default:** 21
    * secure - _mixed_ - Set to true for both control and data connection encryption, 'control' for control connection encryption only, or 'implicit' for implicitly encrypted control connection (this mode is deprecated in modern times, but usually uses port 990) **Default:** false
    * secureOptions - _object_ - Additional options to be passed to `tls.connect()`. **Default:** (none)
    * user - _string_ - Username for authentication. **Default:** 'anonymous'
    * password - _string_ - Password for authentication. **Default:** 'anonymous@'
    * connTimeout - _integer_ - How long (in milliseconds) to wait for the control connection to be established. **Default:** 10000
    * pasvTimeout - _integer_ - How long (in milliseconds) to wait for a PASV data connection to be established. **Default:** 10000
    * keepalive - _integer_ - How often (in milliseconds) to send a 'dummy' (NOOP) command to keep the connection alive. **Default:** 10000
* dest - _object_ - with the following properties:
  * type - _string_ - only s3 supported for now
  * bucketName - _string_ - S3 bucket to which the files will be downloaded to



  Sample
  ------
  ```
  {
    "path": "/tmp/test",
    "mask": ".*\\.txt",
    "source": {
          "type":  "ftp",
          "host":  "ftp.example.com",
          "user":    "username",
          "password":  "password"
        },
    "dest": {
          "type":  "s3",
          "bucketName":  "my-s3-bucket"
        }

  }
  ```

Output
------
* files - _array_ - array of objects with the following properties
  * fileName - _string_
  * size - _number_ - In Bytes
  * status - _string_ - with one of the following
    * s3 - The file has been succesfully downloaded to S3
    * ftp_error - An error occured when downloading the file
    * s3_error - The file could be downloaded but an error occured while uploading it to s3
  * error - _string_ - error short description
  * bucketName - _string_ - S3 bucket to where the file is stored
