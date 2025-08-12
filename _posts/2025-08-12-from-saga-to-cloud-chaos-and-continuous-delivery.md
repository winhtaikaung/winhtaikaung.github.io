---
title:  From Docker to the Cloud: A Group Project Saga of Code, Chaos, and Continuous Delivery
description: About Docker to Cloud Deployment, Saga of Code , Chaos and Continuous Delivery. 
author: winhtaikaung 
date: 2018-09-01 11:33:00 +0800
categories: [Javascript]
tags: [DevOps,Docker,GroupProject,Chaos,Teamwork]
pin: true
math: true
mermaid: true
 
---
## From Docker to the Cloud: A Group Project Saga of Code, Chaos, and Continuous Delivery

Every so often in a developer's journey, a project comes along that’s more than just a list of requirements; it's a gauntlet. For my team, that was deploying a bookstore application. The mission seemed simple: take a Node.js app from a local machine to the cloud. But this five-phase odyssey would test our technical skills, our patience, and our ability to collaborate under pressure. It was a story of code, cloud, and a little bit of chaos.

### Chapter 1: The Blueprint and the First Hurdle

Our journey began with laying the foundation: Continuous Integration (CI). The goal was to create a safety net using GitHub Actions that would automatically run our linter and tests every time we pushed code. This practice is the bedrock of modern software development, preventing the classic "it works on my machine" problem.

Setting up the initial Node.js workflow was straightforward. But the first real challenge emerged when we needed to integrate a database for our tests. Our application required MySQL, so our CI pipeline needed it too. The solution was to run MySQL as a *service container* within our GitHub Actions job. This meant our CI environment would spin up a temporary, isolated MySQL database just for the test run. It was a neat, self-contained solution, but it demanded precision—matching Node and MySQL versions, handling port mapping, and, most critically, managing credentials securely using GitHub Secrets. After a bit of tinkering, our `ci.yml` file was humming, and our pull requests glowed with the comforting green checkmark of a successful run.

However, a different kind of challenge was brewing. One of our team members had gone silent. Deadlines wait for no one, so we made the tough call to press on, documenting our progress and communication meticulously in Microsoft Teams. It was our first lesson in project management: adapt and keep moving.

### Chapter 2: Taming the Docker Whale

With our CI pipeline in place, it was time to containerize our application. This is where Docker enters the scene. Think of a Docker container as a standardized, shippable box that holds everything an application needs to run: code, runtime, system tools, and libraries. This solves the problem of environment inconsistency. Whether on a developer's laptop or a production server, the app inside the container behaves identically.

**The Pros and Cons of Containers**

| Pros | Cons |
| :--- | :--- |
| **Lightweight & Fast:** Containers share the host OS kernel, making them smaller and quicker to start than traditional Virtual Machines (VMs). | **Shared Kernel Security:** Because the kernel is shared, a vulnerability in the host could potentially affect all containers. |
| **Portability:** "Build once, run anywhere." A container image works consistently across different environments. | **Complexity:** The ecosystem of tools for orchestration and networking can have a steep learning curve. |
| **Scalability:** Easy to replicate and scale services horizontally to handle more traffic. | **Not a VM:** Not suitable for running applications that require a completely different operating system from the host. |

My task was to write the `Dockerfile`. It felt like writing a recipe for our application. I started with a base Node.js image, set up the working directory, and carefully copied over our `package.json` files. Here, I used a crucial optimization technique: I copied and installed dependencies *before* copying the rest of the application source code. This way, Docker's build cache wouldn't need to reinstall all dependencies every single time we changed a line of code.

Here's a snippet of our `Dockerfile`:

```docker
# Use an official Node.js runtime as a parent image
FROM node:18-slim

# Create and change to the app directory
WORKDIR /usr/src/app

# Copy package.json and package-lock.json
# This leverages Docker's build cache
COPY package*.json ./

# Install production dependencies
RUN npm ci --omit=dev

# Bundle app source
COPY . .

# Inform Docker that the container is listening on port 8080
EXPOSE 8080

# Define the command to run the application
CMD [ "node", "server.js" ]
```

With the Dockerfile complete, I confidently added a step to our CI workflow to build the image. And… it failed. Over and over. After a frustrating hour, I found the culprit: a typo in my local test command that I had mentally transferred to the `CMD` instruction. It was a humbling reminder that automation is powerful, but it only does exactly what you tell it to do. One corrected line later, our CI pipeline was not just testing our code but successfully building a production-ready Docker image with every commit.

### Chapter 3: The Voyage into the Google Cloud

Our application was now neatly packaged in a container, ready for deployment. Our destination: the Google Cloud Platform (GCP). This marked our transition from a development and CI environment to a real, live production environment.

**Our GCP Toolkit:**

*   **Cloud SQL:** We needed a robust, managed database. Cloud SQL was the perfect fit. It's a fully managed relational database service that handles the tedious parts like patching, backups, and replication, letting us focus on our application. We chose the MySQL engine to match our development setup.
*   **Cloud Build:** This is GCP's fully managed CI/CD platform. We configured it to automatically trigger when we merged a pull request to our `main` branch. Its job was to take our source code, build the Docker image using our `Dockerfile`, and push it to GCP's Artifact Registry.
*   **Cloud Run:** This is a serverless platform designed to run containers. The beauty of Cloud Run is that it automatically scales based on traffic—even scaling down to zero, meaning you pay nothing if your service isn't being used. It was the perfect service to take our newly built container image from Cloud Build and run it as a live, public web service.

The initial setup of Cloud SQL was a breeze, but our joy was short-lived. When we deployed our application to Cloud Run, it couldn't connect to the database. We were hit with a wall of connection errors. This began a three-hour debugging odyssey deep into the heart of GCP's IAM (Identity and Access Management) and secret management. The documentation felt like a labyrinth. The issue? Our Cloud Run service didn't have the correct permissions to access the Cloud SQL instance, and we were struggling to pass the database password to it securely. The solution finally clicked: we had to use GCP's Secret Manager to store our database password and then grant the Cloud Run service's specific service account the roles of "Cloud SQL Client" and "Secret Manager Secret Accessor." It was a complex dance of permissions that taught us a valuable lesson: in the cloud, security and identity are everything.

![A conceptual screenshot showing the Cloud SQL instance details page in the GCP console, highlighting the instance ID, region, and public IP address.](https://storage.googleapis.com/gweb-cloudblog-publish/images/Optimize-MySQL-performance-with-Cloud-SQL.max-1600x1600.jpg)
*(Image: A conceptual view of a Cloud SQL instance configuration in the GCP console.)*

**The Pros and Cons of a Cloud Provider**

| Pros | Cons |
| :--- | :--- |
| **Scalability & Elasticity:** Resources can be scaled up or down on demand. | **Cost Management:** The pay-as-you-go model can become expensive if not carefully monitored. |
| **Managed Services:** Reduces the operational burden of managing infrastructure like databases and servers. | **Vendor Lock-in:** Migrating complex systems from one cloud provider to another can be difficult and costly. |
| **Global Reach:** Easily deploy applications in data centers around the world to be closer to users. | **Complexity:** The sheer number of services and configuration options can be overwhelming. |

### Chapter 4: The Joy of a Fully Automated Pipeline

With our infrastructure in place, we reached the project's zenith: seeing it all work in concert. The final phase was to add two new features—filtering books by genre and deleting books—using our fully automated workflow.

The process was beautiful in its simplicity:
1.  Create a feature branch in Git.
2.  Add the new API endpoint and its corresponding Jest tests.
3.  Push the branch and create a pull request.
4.  GitHub Actions would automatically run our linter and tests against the temporary MySQL database.
5.  After the team reviewed and approved the PR, we’d merge it into `main`.

And then, the magic. The merge would trigger Cloud Build, which built our new Docker image and pushed it to the registry. Cloud Run would then automatically detect the new image and deploy it, replacing the old version with zero downtime. We had achieved true Continuous Deployment. What once would have been a manual, error-prone deployment process was now a seamless, automated flow that took just a few minutes.

### Chapter 5: The Unforeseen Storm

Just as we were celebrating our victory, the storm hit. The team member who had been silent reappeared and, in a misguided attempt to contribute, submitted an old, incorrect version of the project directly to the dropbox. Panic set in. All our meticulous work was at risk of being overwritten by a version that didn't even have a functional pipeline.

This is where our diligence paid off. We had a clear, auditable trail of our entire process: every commit in Git, every pull request on GitHub, and every conversation logged in Microsoft Teams. We presented this evidence to our professor, who was able to quickly understand the situation and rectify the submission. It was a stark lesson in the non-technical side of software development: clear communication and rigorous version control aren't just best practices; they are your safety net when things go wrong.

### Conclusion: The Treasure Map of Lessons Learned

This project was so much more than deploying an application. It was a crash course in the entire DevOps lifecycle. We learned the power of containerization with Docker, the scalability and complexity of GCP, and the sheer elegance of a well-oiled CI/CD pipeline. But the most important lessons were about teamwork. We learned how to navigate challenges, the critical importance of documenting everything, and how to build a resilient workflow that could withstand not just technical bugs, but human error as well. We went in as students; we came out as a deployment team.

### References

1.  Google Cloud. (n.d.). *Cloud SQL overview*. Retrieved from Google Cloud Documentation.
2.  Google Cloud. (n.d.). *Overview of Cloud Build*. Retrieved from Google Cloud Documentation.
3.  Google Cloud. (n.d.). *What is Cloud Run?*. Retrieved from Google Cloud Documentation.
4.  Fitz, T. (2024). *Continuous Deployment: Benefits, Pros/cons, Tools And Tips*. Harness.
5.  Google Cloud. (n.d.). *Cloud Run*. Retrieved from Google Cloud.
6.  AWS. (n.d.). *Benefits of continuous delivery*. Retrieved from AWS Documentation.
7.  Hall, J. (2021, January 13). *4 Benefits of Continuous Deployment*. Retrieved from jonathanhall.io.
8.  Google Cloud. (n.d.). *Cloud SQL*. Retrieved from Google Cloud Marketplace.
9.  s-peers AG. (n.d.). *Cloud Run: Leverage the scalable container platform of the Google Cloud*. Retrieved from s-peers.com.
10. K21 Academy. (2023, June 13). *Introduction to Google Cloud Build: Serverless CI/CD Platform*.
11. Docker Inc. (n.d.). *Docker Documentation*. Retrieved from docs.docker.com.
12. DuploCloud. (2023, December 4). *Docker Advantages and Disadvantages*.
13. NordVPN. (2023, August 23). *Docker vs. virtual machines: What's the difference?*.
14. Backblaze. (2023, July 14). *Docker Containers vs. VMs: A Look at the Pros and Cons*.
15. CloudZero. (2024, August 26). *AWS Vs. Azure Vs. Google Cloud: Which Is Right For Your Organization?*.