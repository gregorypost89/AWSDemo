# AWS Demonstration

This project will demonstrate how to set up basic AWS services using a serverless Chat Application.

> Since this project uses libraries such as Javascript, JQuery, etc., definitions and methodology will be explained into quote boxes such as this to break down.

## Creating a Chat app.

We will demonstrate how to use AWS by creating a simple chat application.

In folder v1 of this repository, lets create our css, fonts and js folder.  Our js folder will contain the following:

### bootstrap.js

### bootstrap.min.js

Bootstrap is a popular framework for developing clean, functional interfaces.  We will be utilizing both the compiled (bootstrap.js) and minified (bootstrap.min.js) javascript files.

### config.js

This javascript file contains custom parameters that are unique to our chat app, such as API endpoints and User Pool Names.  

> **API Endpoint:**
The point where the Application Program Interface(API), the code that allows communication between two programs, connects with the software.
An API will send a *request* from a web application and receive a *response*.  Therefore, an API endpoint is that *specific digital location where the requests are sent and where the resource lives.
**User Pool Name:**  
A Unique Name used to identify our AWS User Pool, which is a directory of users.

For now, our config.js will contain just the following snippet of code.  This defines a **ChatApp** variable that we can use within our program.

```javascript
var ChatApp = window.ChatApp || {};
```

### jquery-3.4.1.min.js

We will be using **JQuery**, which is a popular Javascript library used to simplify development.  This makes it so that we don't have to build our chat app from the ground up.  As seen from the file extension, we will be using JQuery version 3.4.1 and we will only need the *minified* version.

### site.js

site.js is essentially the backbone of our app and will be doing the majority of the work in this program.  Our v1 site.js looks like this:

```javascript
var ChatApp = window.ChatApp || {}

(function scopeWrapper($) {

}(jQuery));
```

>The function of this **Scope Wrapper** is to ensure that the  **\$** can be used within the scope of our function to mean **JQuery**, since the $ is what is used as shorthand for the JQuery library.

## HTML files:

###chat.html

This is the HTML structure of our chat page.

Right now, our body doesn't contain anything, but we will add content as we progress.

At the end of the file on lines 57-61, we have this:

```HTML
<script type="text/javascript">
    $(function () {
  
    });
</script>
```

The purpose of this function block is to execute our custom script. We take our static HTML and dynamically manipulate it using Javascript, which is a big part of what ensures that our app remains serverless.

## Bucket Policies for S3

When setting up our bucket policies, this assumes that we are not blocking new public bucket policies and we are not blocking public and cross-account access, as defined in the public access settings for this bucket.

### Some notes about S3

When we upload a serverless application into AWS, we upload a lot of static data, usually a combination of HTML, CSS and Javascript.  This static data gets put into an AWS service called **S3**, which stands for **Simple Storage Service.** 

S3 is a **key-blob store**, not a file system.  Data is fundamentally stored as "blobs", which is assigned to unique "keys".  These blobs are binary data, with some metadata that S3 maintains to keep track of it, such as unique identifiers and timestamps.

S3 is **eventually consistent**. This is fine as we are using it as a simple file store and not a live transactional database, where a stronger model like linearizability would be more appropriate.

S3 is **durable**.  When we put data into S3, it will stay there.  We should not have to worry about losing the data that we store in this system.

Again, there are no file names in this system.  You can only list a **prefix** in S3, not a directory name.

### S3 Policies

A **policy definition**  is created whenever we make our S3 data publicly available.  

We will need to restrict or permit the kind of data, and which specific data, is being accessed in S3.  Therefore, we will need to write specific policies to determine who or what gets access to this data.  

Our policies aren't just limited to what our users can access.  We can also make policy definitions for **other AWS services**.  One such service is **AWS Lambda** which lets us execute code on our app without requiring a server.  If this code results in writing to our S3 bucket, we need to grant Lambda explicit permission to do so.

### Policy Code:

Source: https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html

This is a .json formatted file that is also available as bucketPolicy.json in this repository. **(v1>Policies>bucketPolicy.json)**

Using AWS's auto-generate options, we are able to create a policy based on the parameters we provide.  

```JSON
{
    "Id": "Policy1578595111976",
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid": "Stmt1578595105247",
        "Action": [
          "s3:GetObject"
        ],
        "Effect": "Allow",
        "Resource": "arn:aws:s3:::S3-BUCKET-HERE/*",
        "Principal": "*"
      }
    ]
  }
  ```

> **Id:** An autogenerated ID.  This can be customized.
**Version:** The version of the policy itself.  This is the most current version of the policy as of the writing of this README.
**Statement:** Defines the policy itself.
**Sid**: An autogenerated ID for the statement.  LIke the **Id**, this can be customized.
**Effect:** Describes the type of access we want to grant.  The two choices are **Allow** and **Deny**.
**Principal:** - Describes *who* gets access to this bucket. Since we want *everybody* to access this bucket, we use **\*** to allow this to happen.
**Action:** The specific operations that we want this policy to affect.  Again, since we want everyone to *access* this bucket, we use **s3:getObject** as our action.
**Resource:** The actual name of the resource.
>> **arn** (Amazon Resource Name)
**:**
**aws** (Amazon Web Services)
**:** 
**s3** (Simple Storage Service)  
***:::***
**\<S3-BUCKET-HERE\>** (Name of the bucket, where your bucket name replaces the **S3-BUCKET-HERE** placeholder)
**/\*:** Used to say this will apply to **everything** in this bucket.

