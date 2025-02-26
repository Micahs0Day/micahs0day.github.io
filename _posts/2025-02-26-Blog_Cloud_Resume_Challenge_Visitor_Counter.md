---
layout: post
title: Cloud Resume Challenge - Visitor Counter
published: true
categories: blog
comments: true
---
![]({{site.baseurl}}/images/crc_visitor_counter.png)

# Cloud Resume Challenge - JavaScript / Database / API / Lambda

Date: February 26, 2025
Status: Done

### Overview:

1. **DynamoDB**:
    - Create a table with a primary key/value (e.g., `VCID`) and an attribute to store the visitor count.
2. **Lambda function (Python)**:
    - Create lambda function with default execution role
    - Edit default role to enable access to DynamoDB
    - Write Python to:
        - Initialize DynamoDB connection using `boto3`.
        - Fetch the visitor count from DynamoDB.
        - Increment the count.
        - Save the updated count back to DynamoDB.
        - Return the updated count.
3. Add a public endpoint to the lambda function using API Gateway
4. **Frontend (JavaScript)**:
    - Call the API Gateway using `fetch()`.
    - Display the returned visitor count.

---

### **1. DynamoDB Table Setup ✅**

[Choosing the Right DynamoDB Partition Key | Amazon Web Services](https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key/)

[Core components of Amazon DynamoDB - Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html)

1. Go to the AWS DynamoDB console and create a new table (`VisitorCountTable`) with `visitor_count_id` as the partition key.
2. Set the initial count manually to 1.
3. Create another attribute named `visitor_count` and set it to 0

![]({{site.baseurl}}/images/crc_visitor_counter_dynamodb.png)

---

### 2. Create Lambda Function and update default Lambda Role ✅

**Permission Policy:**

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "UpdateDynamoDB",
            "Effect": "Allow",
            "Action": [
                "dynamodb:UpdateItem",
                "dynamodb:Scan"
            ],
            "Resource": "arn:aws:dynamodb:us-east-1:123456789:table/VisitorCountTable"
        }
    ]
}
```

### 3. **Lambda Function Code (Python) ✅**

[Define Lambda function handler in Python - AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/python-handler.html)

[scan - Boto3 1.35.29 documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/dynamodb/client/scan.html)

[update_item - Boto3 1.35.26 documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/dynamodb/client/update_item.html)

[message: "Internal server error" when try to access aws gateway api](https://stackoverflow.com/questions/47672377/message-internal-server-error-when-try-to-access-aws-gateway-api)

```python
import json
import boto3

client = boto3.client("dynamodb")

def lambda_handler(event, context):
    return Get_Visitor_Count()

def Get_Visitor_Count():
    ##
    response = client.scan(TableName="VisitorCountTable")

    ##
    if "Items" in response:
        # Save the response (JSON)
        count = response["Items"][0]["visitor_count"]["N"]
        count = int(count)
    else:
        count = 0
    count += 1

    request = client.update_item(
        ExpressionAttributeNames={
            "#VC": "visitor_count",
        },
        ExpressionAttributeValues={
            ":count": {
                # N is attribute of type number
                "N": str(count),
            },
        },
        Key={
            "visitor_count_id": {
                "N": "1",
            },
        },
        ReturnValues="ALL_NEW",
        TableName="VisitorCountTable",
        # An expression that defines one or more attributes to be updated, the action to be performed on them, and new values for them.
        # SET - Adds one or more attributes and values to an item. If any of these attributes already exist, they are replaced by the new values.
        UpdateExpression="SET #VC = :count",
    )

    return {
        'statusCode': 200,
        'headers': {'Content-Type': 'application/json'},
        'body': json.dumps({'visitorcount': count})
    }

```

---

### **4. Connect API Gateway & Lambda ✅**

[Invoking a Lambda function using an Amazon API Gateway endpoint - AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/services-apigateway.html)

### To add a public endpoint to your Lambda function

1. Open the [Functions page](https://console.aws.amazon.com/lambda/home#/functions) of the Lambda console.
2. Choose a function.
3. Under **Function overview**, choose **Add trigger**.
4. Select **API Gateway**.
5. Choose **Create an API**
    1. **New API:** For **API type**, choose **HTTP API**. For more information, see [Choosing an API type](https://docs.aws.amazon.com/lambda/latest/dg/services-apigateway.html#services-apigateway-apitypes).
6. For **Security**, choose **Open**.
7. Enable CORS
8. Choose **Add**.
9. Navigate to API Gateway > APIs > *API you just created* > Routes 
10. Edit Route to GET

---

### 5. **Frontend (JavaScript) ✅**

[JSFiddle - Code Playground](https://jsfiddle.net/)

[JavaScript Fetch API use cases for fetching data, JSON, XML, etc. | Learn JavaScript](https://learnjavascript.online/topics/fetch.html)

[Document: getElementById() method - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementById)

[Node: textContent property - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent#differences_from_innertext)

Example HTML

```html
<html lang="en">
  <body>
		Welcome, Visitor #<div id="VisitorCounter"></div>
  </body>
</html>

```

Example JavaScript

```jsx
<script>
fetch('https://aegjbsexha.execute-api.us-east-1.amazonaws.com/default/VisitorCounter')
    .then(response => response.json())
  	.then(data => {
  	document.getElementById("VisitorCounter").textContent = data["visitorcount"];
  })
</script>
```

Example CSS

```json
.visitor-count {
  position: absolute;  /* Keeps the element fixed while scrolling */
  top: 50px;        /* Places the element 50px from the top */
  left: 0;          /* Aligns the element to the left */
  width: 100%;      /* Optional: makes the element span the full width of the page */
  text-align: center; /* Optional: centers the text horizontally */
  display: inline-block; /* Keeps this element as a block but inline with other elements */
  z-index: 1000;    /* Ensures the element stays on top of other content */
}

#VisitorCounter {
  display: inline; /* Ensure the VisitorCounter div stays inline with the Welcome text */
}
```

---

### 6. Update href links with S3 URLs ✅

```css
.social.twitter:before {
  background: url("https://m0d-website.s3.amazonaws.com/images/twitter.png") center no-repeat;
}
.social.github:before {
  background: url("https://m0d-website.s3.amazonaws.com/images/github.png") center no-repeat;
}
.social.linked-in:before {
  background: url("https://m0d-website.s3.amazonaws.com/images/linkedin.png") center no-repeat;
}
```
