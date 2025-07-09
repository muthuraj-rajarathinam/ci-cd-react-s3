# 🚀 React CI/CD Pipeline with GitHub Actions and AWS S3

I built a **CI/CD pipeline** that automatically builds and deploys a **React application** to **AWS S3** using **GitHub Actions**.

Every code push to the `main` branch triggers a workflow that:

* Installs dependencies
* Builds the React project
* Syncs the build output to a public **S3 bucket** configured for **static website hosting**

The pipeline securely uses **GitHub Secrets** for AWS credentials and region, ensuring automation with proper access control.

---

## ⚙️ Workflow Summary

```txt
✅ Install dependencies
🛠️ Build React project
☁️ Deploy to AWS S3 (static website)
```

---

## 📦 Tech Stack

* ⚛️ **React** (via Create React App)
* 🔁 **GitHub Actions** (CI/CD automation)
* 🪣 **AWS S3** (static web hosting)
* 🔐 **AWS IAM** (secure access)
* 🧰 **AWS CLI** (inside GitHub Actions)

---

## ✅ Live Demo

🌐 Your app will be live at:

```txt
http://your-bucket-name.s3-website.ap-south-1.amazonaws.com
```

---

## 🛠️ Project Setup

### 1. Create React App

```bash
npx create-react-app my-app
cd my-app
git init
```

---

### 2. Initialize Git & Push to GitHub

Create a new GitHub repository
Example: `ci-cd-react-s3`

```bash
git remote add origin https://github.com/yourusername/your-repo.git
git branch -M main
```

---

### 3. GitHub Actions Workflow Setup

Create workflow folder and file:

```bash
mkdir .github/workflows
touch .github/workflows/deploy.yml
```

Add the following to `deploy.yml`:

```yaml
name: Deploy React to S3

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm install

      - name: Build project
        run: npm run build

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}

      - name: Deploy to S3
        run: aws s3 sync build/ s3://${{ secrets.AWS_S3_BUCKET }} --delete
```

---

## ☁️ AWS Setup

### 4. Create a New S3 Bucket

* Go to **AWS S3 Console**
* Click **Create bucket**
* Bucket name must be **globally unique**
* **Uncheck** all **Block Public Access** options
* Click **Create**

---

### 5. Enable Static Website Hosting

* Go to your bucket → **Properties**
* Scroll to **Static Website Hosting**
* Enable it with:

  * **Index document**: `index.html`
  * **Error document**: `index.html`

---

### 6. Set Bucket Policy

Go to **Permissions → Bucket Policy** and paste this:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadAccess",
      "Effect": "Allow",
      "Principal": "*",
      "Action": ["s3:GetObject"],
      "Resource": [
        "arn:aws:s3:::your-bucket-name",
        "arn:aws:s3:::your-bucket-name/*"
      ]
    }
  ]
}
```

🔁 Replace `your-bucket-name` with your actual bucket name.

---

### 7. Create IAM User for GitHub

* Go to **AWS IAM → Users → Create User**
* Name: `github-deployer`
* Access Type: **Programmatic Access**
* Attach Policy: `AmazonS3FullAccess`
* Save these:

  * ✅ **AWS Access Key ID**
  * ✅ **AWS Secret Access Key**

---

## 🔐 GitHub Secrets Setup

In your GitHub repo:

**Settings → Secrets and Variables → Actions → New Repository Secret**

| Secret Name             | Value               |
| ----------------------- | ------------------- |
| `AWS_ACCESS_KEY_ID`     | Your IAM Access Key |
| `AWS_SECRET_ACCESS_KEY` | Your IAM Secret Key |
| `AWS_REGION`            | e.g., `ap-south-1`  |
| `AWS_S3_BUCKET`         | Your Bucket Name    |

---

## 🚀 Trigger Test Deployment

```bash
git add .
git commit -m "Initial commit"
git push -u origin main
```

---

## 🔍 How It Works

1. Code is pushed to `main`
2. GitHub Actions builds the React app
3. AWS CLI uploads `build/` folder to S3
4. S3 serves it as a public static website

---

## 📌 Notes

* ✅ Bucket name **must be globally unique**
* ✅ Ensure **Block Public Access** is disabled
* 🔐 Always use **GitHub Secrets** for credentials
* 🌐 Use `https://` for production URLs

---

## 🙋‍♂️ Author

**Muthuraj Rajarathinam**
💼 Passionate about DevOps, CI/CD, and scalable cloud apps
[LinkedIn](https://www.linkedin.com/in/muthurajrajarathinam) | 
[GitHub](https://github.com/muthuraj-rajarathinam)
