# My Self Study on Continuous Integration, Continuous Delivery and Continuous Deployment
### **Continuous Integration (CI)**  
Continuous Integration is a practice in software development where **developers frequently integrate (merge) their code changes into a shared repository**. Each integration is automatically tested through a set of **automated tests** to ensure that new changes don’t break the existing functionality of the application.  
- **Main Goal**: Catch issues early by testing code often.  
- **Tools Used**: Jenkins, Travis CI, CircleCI.

**Example**: Imagine multiple developers working on different features of the same app. Instead of waiting until everyone is done to check if their code works together, CI allows them to continuously merge changes and check for any errors immediately.

---

### **Continuous Delivery (CD)**  
Continuous Delivery takes CI a step further by ensuring that the **code is always in a deployable state**. In this process, after the automated tests have been passed in CI, the software is **prepared for release to production** but not automatically deployed. This means a release can happen at any time because the code is always ready.  
- **Main Goal**: Make sure the code is always ready for deployment with minimal manual intervention.  
- **Tools Used**: AWS CodePipeline, GitLab, Bamboo.

**Example**: If a company wants to release their app every Friday, continuous delivery ensures that the app is always in a state that can be released, minimizing last-minute scrambling.

---

### **Continuous Deployment (CD)**  
Continuous Deployment is the most advanced stage of the pipeline, where every change that passes the CI and automated tests is **automatically deployed to production** without any human intervention. This allows for faster delivery of features and bug fixes to users.  
- **Main Goal**: Automatically deploy every change that passes all tests directly to production.  
- **Tools Used**: Spinnaker, Jenkins, GitLab.

**Example**: When a developer pushes new code, the app is automatically updated for users if all the tests pass, meaning new features can be live within minutes.

---

### **Summary**:
- **CI** focuses on integrating and testing code often.
- **CD (Delivery)** ensures that the code is always ready for deployment.
- **CD (Deployment)** takes it a step further by automatically pushing code to production without manual approval.

These practices work together to ensure that software development is fast, reliable, and automated.
