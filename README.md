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
npx create-react-app my-app         # Creates a new React project 
cd my-app                           # Enters the project directory
git init                            # Initializes a Git repository to start version control &  connect to GitHub later.
```

---

### 2. Initialize Git & Push to GitHub

Create a new GitHub repository
Example: `ci-cd-react-s3`

```bash
git remote add origin https://github.com/yourusername/your-repo.git   # Links your local repo to GitHub
git branch -M main                                                    # Renames default branch to 'main'
```

---

## ⚙️ GitHub Actions Workflow Setup

Create the folders and file:

```bash
mkdir .github/workflows                   # Creates the workflow directory
touch .github/workflows/deploy.yml       # Creates the deployment config file
```

Add this to `deploy.yml`:

```yaml
name: Deploy React to S3                  # Name of the GitHub Actions workflow

on:
  push:
    branches:
      - main                              # Triggers the workflow only on push to main branch

jobs:
  deploy:
    runs-on: ubuntu-latest                # Runs the job on GitHub's Ubuntu VM

    steps:
      - name: Checkout code
        uses: actions/checkout@v3         # Pulls your code from GitHub into the VM

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'              # Installs Node.js version 18 for build tools

      - name: Install dependencies
        run: npm install                  # Installs project dependencies from package.json

      - name: Build project
        run: npm run build                # Creates a production build of the React app

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}           # Uses your AWS Access Key securely
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}   # Uses your AWS Secret Key securely
          aws-region: ${{ secrets.AWS_REGION }}                         # Uses your AWS region securely

      - name: Deploy to S3
        run: aws s3 sync build/ s3://${{ secrets.AWS_S3_BUCKET }} --delete   # Uploads the build folder to S3 and deletes removed files
```

---

## ☁️ AWS Setup

### 4. Create a New S3 Bucket

* Go to **AWS S3 Console**
* Click **Create bucket**
* Bucket name must be **globally unique**
* **Uncheck** all **Block Public Access** options (to make it publicly readable)
* Click **Create**

---

### 5. Enable Static Website Hosting

* Go to your bucket → **Properties**
* Scroll to **Static Website Hosting**
* Enable it with:

  * **Index document**: `index.html`     # Default homepage
  * **Error document**: `index.html`     # Shows same page for errors (good for SPA)

---

### 6. Set Bucket Policy

Go to **Permissions → Bucket Policy** and paste this:

```json
{
  "Version": "2012-10-17",            // Policy language version
  "Statement": [
    {
      "Sid": "PublicReadAccess",      // Statement ID
      "Effect": "Allow",              // Allows the action
      "Principal": "*",               // Applies to everyone (public)
      "Action": ["s3:GetObject"],     // Grants read access to objects
      "Resource": [
        "arn:aws:s3:::your-bucket-name",       // Bucket itself
        "arn:aws:s3:::your-bucket-name/*"      // All files inside the bucket
      ]
    }
  ]
}
```

🔁 Replace `your-bucket-name` with your actual S3 bucket name.

---

### 7. Create IAM User for GitHub Access

* Go to **AWS IAM → Users → Create User**
* Name it: `github-deployer`
* Access Type: **Programmatic Access** (for API)
* Attach Policy: `AmazonS3FullAccess` (gives full access to S3)
* Save and note:

  * ✅ **AWS Access Key ID**
  * ✅ **AWS Secret Access Key**

---

## 🔐 GitHub Secrets Setup

Go to:
**GitHub Repo → Settings → Secrets and Variables → Actions → New Repository Secret**

| Secret Name             | Value               | Explanation                       |
| ----------------------- | ------------------- | --------------------------------- |
| `AWS_ACCESS_KEY_ID`     | Your IAM Access Key | For authenticating to AWS         |
| `AWS_SECRET_ACCESS_KEY` | Your IAM Secret Key | Used with the Access Key          |
| `AWS_REGION`            | e.g., `ap-south-1`  | AWS region (like Mumbai)          |
| `AWS_S3_BUCKET`         | Your S3 bucket name | Where your app files are deployed |

---

## 🚀 Trigger Test Deployment

```bash
git add .                            # Stages all changes for commit
git commit -m "Initial commit"       # Saves changes with a message
git push -u origin main              # Pushes code to GitHub, starts CI/CD pipeline
```

---

## 🔍 How It Works (End-to-End)

1. You push code to `main` on GitHub
2. GitHub Actions starts running:

   * Installs Node.js
   * Installs dependencies
   * Builds your app
   * Deploys it to S3 using AWS CLI
3. Your React app becomes **live as a static website** on S3

---

## 📌 Notes

* ✅ Bucket name **must be globally unique**
* ✅ Ensure **Block Public Access** is **disabled**
* 🔐 Always use **GitHub Secrets** for AWS credentials
* 🌐 Use `https://` version of your URL in production

---

## 🙋‍♂️ Author

**Muthuraj Rajarathinam**
💼 Passionate about DevOps, CI/CD, and scalable cloud apps
🔗 [LinkedIn](https://www.linkedin.com/in/muthurajrajarathinam)
🐙 [GitHub](https://github.com/muthuraj-rajarathinam)

