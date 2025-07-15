# 🧠 AI-Powered Sentiment Analyzer using AWS Lambda & Amazon Comprehend

This project is a serverless **Sentiment Analyzer Web App** powered by **Amazon Comprehend**, using **AWS Lambda** and **API Gateway**. It detects the emotional tone of user input (Positive, Negative, Neutral, or Mixed) and returns the result in real time.

---

## 🔍 What Is Amazon Comprehend?

> **Amazon Comprehend** is an AWS-managed **Natural Language Processing (NLP)** service that uses machine learning to extract insights from text — like sentiment, key phrases, entities, and more — without needing to train any ML models.

In this project, we use its **`DetectSentiment`** feature, which automatically analyzes user input and returns one of:
- `POSITIVE`
- `NEGATIVE`
- `NEUTRAL`
- `MIXED`

---

## 🧠 What This Project Does

✅ User types any sentence in a form  
✅ JS frontend sends it to an API (via API Gateway)  
✅ Lambda function receives the text  
✅ Lambda sends it to **Amazon Comprehend**  
✅ Comprehend returns the sentiment  
✅ Result is shown on the frontend instantly

---

## 🧱 Architecture

```
User (Browser)
    ↓
HTML + JavaScript
    ↓
API Gateway (HTTP API)
    ↓
AWS Lambda
    ↓
Amazon Comprehend (DetectSentiment)
    ↓
Sentiment JSON Response (POSITIVE, etc.)
```

---

## 🛠 Tech Stack

| Component     | Purpose                                |
|---------------|-----------------------------------------|
| HTML + JS     | Frontend input & result display         |
| Lambda        | Python backend to process text          |
| Amazon Comprehend | NLP sentiment analysis            |
| API Gateway   | Expose Lambda to frontend securely      |
| IAM           | Secure permissions for Comprehend API   |

---

## 🧪 Sentiment Examples

| Input                                     | Output     |
|-------------------------------------------|------------|
| "I love building on AWS!"                | POSITIVE   |
| "This is the worst app ever."            | NEGATIVE   |
| "The event is on Thursday."              | NEUTRAL    |
| "The service is good, but very slow."    | MIXED      |

---

## 🛠 Step-by-Step Implementation

---

### ✅ Step 1: Create Lambda Function

- Name: `AnalyzeSentimentFunction`
- Runtime: Python 3.12

Paste this code:

```python
import json
import boto3

comprehend = boto3.client("comprehend")

def lambda_handler(event, context):
    body = json.loads(event.get("body", "{}"))
    text = body.get("text", "")

    if not text:
        return {
            "statusCode": 400,
            "headers": {"Access-Control-Allow-Origin": "*"},
            "body": json.dumps({"message": "Text input is required"})
        }

    response = comprehend.detect_sentiment(Text=text, LanguageCode='en')
    sentiment = response['Sentiment']

    return {
        "statusCode": 200,
        "headers": {
            "Content-Type": "application/json",
            "Access-Control-Allow-Origin": "*",
            "Access-Control-Allow-Headers": "Content-Type",
            "Access-Control-Allow-Methods": "OPTIONS,POST"
        },
        "body": json.dumps({ "sentiment": sentiment })
    }
```

📸 
<img width="1918" height="980" alt="Screenshot 2025-07-14 172445" src="https://github.com/user-attachments/assets/478b6331-915b-48fc-a037-366ce6ea8df8" />
<img width="1919" height="979" alt="Screenshot 2025-07-14 172603" src="https://github.com/user-attachments/assets/09132c07-0a31-418f-a4ed-a888bbc041ce" />

---

### ✅ Step 2: Add IAM Permission to Lambda Role

Add this **inline policy** to the Lambda's execution role:

```json
{
  "Effect": "Allow",
  "Action": "comprehend:DetectSentiment",
  "Resource": "*"
}
```

📸 
<img width="1919" height="980" alt="Screenshot 2025-07-14 172659" src="https://github.com/user-attachments/assets/90a67720-4b5e-4e2c-963a-a83cc4d5a163" />
<img width="1919" height="980" alt="Screenshot 2025-07-14 172726" src="https://github.com/user-attachments/assets/006b48d7-49d0-4e5c-b5df-f6a9616b08db" />
<img width="1919" height="980" alt="Screenshot 2025-07-14 172802" src="https://github.com/user-attachments/assets/ba8abf0e-2478-41ec-a788-6f83c1649459" />
<img width="1919" height="979" alt="Screenshot 2025-07-14 172833" src="https://github.com/user-attachments/assets/a3aa31cc-f75b-4f61-bfcc-f5229ec3a432" />

---

### ✅ Step 3: Create HTTP API Gateway

- Method: `POST` & `OPTIONS`
- Route: `/analyze`
- Integration: Lambda (`AnalyzeSentimentFunction`)
- Enable **CORS**:
  - Allowed Methods: `POST`, `OPTIONS`
  - Allowed Headers: `Content-Type`
  - Allowed Origins: `*`

📸 
<img width="1919" height="977" alt="Screenshot 2025-07-14 173005" src="https://github.com/user-attachments/assets/0feacecd-2816-48fc-bf71-f348c6a36b6a" />
<img width="1919" height="979" alt="Screenshot 2025-07-14 173039" src="https://github.com/user-attachments/assets/479bd6b9-19f8-4893-8c5c-08d20062022c" />
<img width="1919" height="981" alt="Screenshot 2025-07-14 173057" src="https://github.com/user-attachments/assets/294a96b2-fde4-4ac3-a450-c2ea27328d90" />
<img width="1919" height="981" alt="Screenshot 2025-07-14 173529" src="https://github.com/user-attachments/assets/c1027b88-bd5e-46b2-9a60-549957713a36" />
<img width="1919" height="980" alt="Screenshot 2025-07-14 173555" src="https://github.com/user-attachments/assets/3b609dbd-7f30-44a3-b722-5105c07d9db1" />
<img width="1919" height="978" alt="Screenshot 2025-07-14 173609" src="https://github.com/user-attachments/assets/3bb9c6fd-0f2e-4bf6-893d-4243674404b4" />

---

### ✅ Step 4: Build the Frontend

#### 📄 `index.html`

📸
<img width="1917" height="1033" alt="Screenshot 2025-07-14 173909" src="https://github.com/user-attachments/assets/853c1bdd-bc13-4fe1-8a9c-882e02a53c68" />
<img width="1141" height="132" alt="Screenshot 2025-07-14 180726" src="https://github.com/user-attachments/assets/fec03eda-cb81-4a53-85f7-08c19a2f7864" />

---

## 🛠 Common Fixes

| Problem                            | Solution                                              |
|------------------------------------|--------------------------------------------------------|
| CORS Error                         | Ensure CORS is enabled in API Gateway (step 3)         |
| "AccessDenied" from Lambda         | Add `comprehend:DetectSentiment` permission            |
| `undefined` response               | Make sure frontend URL matches API Gateway endpoint    |
| 500 Internal Error                 | Check Lambda logs in CloudWatch                        |

---

## 🚀 Improvements

- Add **language auto-detection** using `detect_dominant_language`
- Show **confidence scores** from Comprehend
- Add **CI/CD with GitHub Actions** for Lambda
- Host frontend on **S3 static site**

---

## 🧑‍💻 Author

**Ujjwal Wadhai**  

🔗 [GitHub](https://github.com/Ujjwal-04)
🔗 [LinkedIn](www.linkedin.com/in/ujjwal-wadhai)

---
