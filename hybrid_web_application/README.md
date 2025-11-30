# **Hybrid Web Application with EC2, CloudFront, and S3**

### **A Practical Networking + Cloud Architecture Workflow**

This project demonstrates how to build a hybrid web application in AWS where **EC2 serves the HTML shell** and **CloudFront + S3** deliver all static assets. The design follows a modern, scalable pattern used in enterprise environments to improve performance, security, and content delivery efficiency.

The repository includes:

* Architecture overview
* Build steps
* Final rendered webpage
* Verification evidence
* Cleanup steps

The final page renders:
✔ Blue background (CloudFront-delivered CSS)
✔ “Hello from Jahson!” message (EC2-served HTML)
✔ SUCCESS logo (CloudFront-delivered image)

---

## **📌 Architecture Summary**

**Services Used**

* **Amazon EC2** – Apache web server hosting index.html
* **Amazon S3 (private)** – Static assets (style.css, success-logo.png)
* **Amazon CloudFront** – Global CDN distributing static content
* **IAM & Security Groups** – Access control and least-privilege enforcement

**Traffic Flow**

```
Client → EC2 (HTML only)
Client → CloudFront → S3 (CSS + images)
```

This ensures faster load times, better scalability, and stronger security boundaries.

---

## **🧱 Build Steps**

### **1. Create Private S3 Bucket**

* Block All Public Access = ON
* Upload:

  * style.css
  * success-logo.png
* No public bucket policies or object ACLs

### **2. Create CloudFront Distribution**

* Origin: S3 bucket
* Status: Enabled
* Use the CloudFront domain for all static asset references in HTML

### **3. Launch EC2 Instance**

* Amazon Linux 2023, t2.micro
* Install Apache:

  ```
  sudo dnf install httpd -y
  sudo systemctl enable --now httpd
  ```
* Security Group: HTTP (80) allowed, SSH restricted to trusted IPs

### **4. Upload HTML File**

Place index.html in `/var/www/html/` with CloudFront references:

```html
<link rel="stylesheet" href="https://<cloudfront-domain>/style.css">
<img src="https://<cloudfront-domain>/success.png">
```

### **Troubleshooting Note**

I initially had my name formatted incorrectly.
I re-opened the file using nano, updated the text, saved it, and refreshed the page to confirm the change.

---

## **🧪 Verification & Testing**

### **What Was Confirmed**

* S3 bucket is fully private
* CloudFront successfully retrieves static assets
* EC2 serves only HTML (no static files)
* HTML, CSS, and images render correctly
* Developer tools show CloudFront headers for all asset requests
* End-to-end page load works as designed

---

## **🔐 Security Highlights**

* S3 bucket: non-public, no ACLs, no public-read access
* EC2 SG minimizes inbound exposure
* Static assets delivered through CloudFront only
* No direct S3 URLs exposed
* IAM used with least-privilege permissions

---

## **🧹 Cleanup**

All AWS resources were deleted after testing to prevent ongoing charges:

* EC2 instance terminated
* S3 bucket deleted
* CloudFront distribution disabled/removed
* Security groups and roles reviewed and cleaned up

---

## **💡 Reflection**

This project reinforced key principles of cloud networking and architecture:

* Separating compute (EC2) from static content (S3+CloudFront) increases performance
* Private buckets combined with CloudFront improve security posture
* Global edge delivery reduces latency
* Troubleshooting required carefully validating file paths and HTML references

Future improvements may include adding HTTPS on EC2 via ALB, using CloudFront Origin Access Control (OAC), or enabling CI/CD to automate asset deployments.

---

## **📁 Repository Structure**

```
/
├── index.html
├── screenshots/
├── README.md
```

---

## **🏁 Final Result**

A secure, performant, hybrid AWS web application that demonstrates:

* Content delivery architecture
* Private-to-public service integration
* Traffic flow reasoning
* Real-world troubleshooting
* Cloud networking fundamentals
