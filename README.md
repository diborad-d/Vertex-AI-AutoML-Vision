# Vertex-AI-AutoML-Vision
Vertex AI AutoML Vision Guide

Welcome to the Vertex AI AutoML Vision Guide! This repository provides a step-by-step guide for setting up Google Cloud, Vertex AI, AutoML training, making predictions, and troubleshooting. It includes detailed setup instructions for both code-side (Node.js) and Google Cloud-side configurations.

## **📌 Table of Contents**
- [🚀 Prerequisites](#-prerequisites)
- [🛠️ Google Cloud Setup](#-google-cloud-setup)
- [💻 Code Setup](#-code-setup)
- [🔧 Vertex AI AutoML Training](#-vertex-ai-automl-training)
- [🤖 Making Predictions](#-making-predictions)
- [🛑 Troubleshooting](#-troubleshooting)
- [📚 Additional Resources](#-additional-resources)


🚀 Prerequisites
1️⃣ Google Cloud Account & Project
Sign up for Google Cloud at cloud.google.com.

Create a Google Cloud Project.

Enable Billing for your project.

2️⃣ Enable Required Google Cloud APIs
Run the following command to enable Vertex AI and Cloud Storage APIs:

 gcloud services enable aiplatform.googleapis.com storage.googleapis.com
3️⃣ Install Required Tools
Google Cloud SDK: Install from here

Node.js (for code-side integration): Install from nodejs.org

Postman (for manual API testing): Install from postman.com

🛠️ Google Cloud Setup
1️⃣ Create a Google Cloud Storage (GCS) Bucket
gsutil mb -l us-central1 gs://your-bucket-name/
✔ This bucket will store your dataset images for AutoML training.

2️⃣ Create a Vertex AI Dataset
gcloud ai datasets create --display-name=car-dataset --metadata-schema-uri=gs://google-cloud-aiplatform/schema/dataset/metadata/image_1.0.0.yaml --region=us-central1
✔ This dataset will be used for training AutoML models.

3️⃣ Upload Images to GCS
gsutil cp -r /local-image-folder gs://your-bucket-name/
✔ Uploads images for training.

4️⃣ Prepare the Training CSV File
Ensure your CSV is in the following format:

gs://your-bucket-name/images/car1.jpg,car
Upload it to GCS:

gsutil cp training-dataset.csv gs://your-bucket-name/
💻 Code Setup
1️⃣ Clone the Repository
git clone https://github.com/your-username/vertex-ai-vision.git
cd vertex-ai-vision
2️⃣ Install Dependencies
npm install google-auth-library axios fs csv-parser open
3️⃣ Set Up OAuth 2.0 Credentials
Go to Google Cloud Console → Credentials

Create OAuth 2.0 Client ID for Web Application

Download the JSON file and save it as oauth-client.json in your repo

🔧 Vertex AI AutoML Training
1️⃣ Train an AutoML Model
gcloud ai custom-jobs create --region=us-central1 --display-name=car-automl-training --dataset-id=YOUR_DATASET_ID
✔ This command starts training your AutoML Vision model.

2️⃣ Deploy the Model to an Endpoint
gcloud ai endpoints create --display-name=car-endpoint --region=us-central1
✔ Deploys the trained model to an endpoint for predictions.

🤖 Making Predictions
Option 1: Using Postman
Get an OAuth Token

gcloud auth print-access-token
Set Up Postman Request:

Method: POST

URL:

https://us-central1-aiplatform.googleapis.com/v1/projects/YOUR_PROJECT_ID/locations/us-central1/endpoints/YOUR_ENDPOINT_ID:predict
Headers:

Authorization: Bearer YOUR_ACCESS_TOKEN

Content-Type: application/json

Body (Example for GCS Image Prediction):

{
  "instances": [{ "image": { "gcsUri": "gs://your-bucket-name/test-image.jpg" } }]
}
Option 2: Using Node.js Script
const axios = require("axios");
const fs = require("fs");

const ENDPOINT_URL = "https://us-central1-aiplatform.googleapis.com/v1/projects/YOUR_PROJECT_ID/locations/us-central1/endpoints/YOUR_ENDPOINT_ID:predict";
const ACCESS_TOKEN = "YOUR_ACCESS_TOKEN";
const imageBase64 = fs.readFileSync("./test-image.jpg", "base64");

axios.post(ENDPOINT_URL, { instances: [{ image: { content: imageBase64 } }] }, {
    headers: { Authorization: `Bearer ${ACCESS_TOKEN}`, "Content-Type": "application/json" }
}).then(response => {
    console.log("✅ Prediction Response:", response.data);
}).catch(error => {
    console.error("❌ Prediction Error:", error.response ? error.response.data : error.message);
});
✔ Run the script:

node predict.js
🛑 Troubleshooting
Issue	Fix
403 Permission Denied	Ensure IAM roles roles/aiplatform.user and roles/iam.serviceAccountUser are assigned
redirect_uri_mismatch	Add http://localhost:5000 in Google Cloud Console → Credentials
localhost refused to connect	Try a different port (e.g., 5000 instead of 8080)
NXDOMAIN (DNS error)	Use the correct dedicated endpoint URL from gcloud ai endpoints describe YOUR_ENDPOINT_ID
📚 Additional Resources
Google Cloud Vertex AI Docs

Google Cloud Storage

OAuth 2.0 Setup

🚀 Contributing
We welcome contributions! Feel free to submit issues or pull requests.

⭐ Star this repository if you found it useful!




