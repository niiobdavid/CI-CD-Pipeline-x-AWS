<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build a CI/CD Pipeline with AWS

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-devops-codepipeline-updated)

**Author:** davidniiamui@gmail.com  
**Email:** niiobdavid@gmail.com

---

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codepipeline-updated_fbdetger)

---

## Introducing Today's Project!

In this project, I will demonstrate how to use CodePipeline to set up a CI/CD pipeline. 
CodePipeline will  AUTOMATE the flow from GitHub all the way to CodeDeploy.
By the end of this project, I'll be able to simply push a change to the code and see the updated web app live in production (without me having to go into CodeBuild and CodeDeploy)

### Key tools and concepts

Services I used were CodePipeline, CodeDeploy,
CodeBuild, CodeArtifact, GitHub, VS Code, EC2, S3, IAM and CloudFormation. 
Key concepts I learnt include the different stages in a CI/CD pipeline, handling rollbacks and webhooks.

### Project reflection

This project took just under 3 hours, including documentation and troubleshooting time. 
The most challenging part was receiving permission errors during the rollback (Amazon Q saved the day). 
It was most rewarding to see the live, deployed web app update without me having to build and deploy the project again.

---

## Starting a CI/CD Pipeline

AWS CodePipeline is an AWS DevOps tool that helps create a workflow that automatically moves code from GitHub (my source code repository) all the way to CodeDeploy (my deployment tool).
This helps make sure deployments are consistent and reliable with less risk of human errors.

CodePipeline offers different execution modes based on how multiple runs of the same pipeline are treated.
I chose Superseded which means my latest pipeline run will take priority and cancel the older ones.
Other options include Queued (to process every run in the order that they were started) and Parallel (process both runs at the same time)

A service role gets created automatically during setup, so that CodePipeline has access to the resources it needs to coordinate the different stages (access to CodeConnection to connect to GitHub, access to CodeBuild and S3 to store artifacts).

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codepipeline-updated_gdnhtm)

---

## CI/CD Stages

The three stages I've set up in my CI/CD pipeline are: 
Source (source code for the web project in GitHub), 
Build (building the web app using CodeBuild), 
Deploy (deploying changes to the web app using CodeDeploy).

CodePipeline organizes the three stages into a single diagram that showcases the flow from Source to Deploy.
In each stage, you can see more details on the pipeline execution that it belongs to and shortcuts to the connected service.

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codepipeline-updated_fbdetger)

---

## Source Stage

In the Source stage, which is about where the source code lives (GitHub),  the default branch tells CodePipeline exactly which version of the code I want to use in this workflow.
In production environments, you can have many different branches to represent many different versions of your code /web app.
By specifying a branch, you're making sure a specific version is being deployed, not a random branch.



The source stage is also where you enable webhook events, which are like notifications. 
Whenever you make a change to the source code, the webhook event will detect the change and alert CodePipeline to trigger a new run.

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codepipeline-updated_sergt)

---

## Build Stage

The Build stage sets up how I'll build my web app and make it ready for deployment.
I configured CodeBuild to be my build provider and to use the input artifact that was outputted by the source stage.
The input artifact is the compressed code that the source stage retrieved from GitHub and compressed into a Zip file.

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codepipeline-updated_j1k2l3m4)

---

## Deploy Stage

The Deploy stage is where I set up CodeDeploy to be my deployment provider. 
It takes the BuildArifact from CodeBuild and the application and deployment settings that I've defined 
in my deployment group.


![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codepipeline-updated_m4n5o6p7)

---

## Success!

Since my CI/CD pipeline gets triggered by code changes and webhook events, I tested the pipeline by updating the code.
I added a line to my web app's index.jsp file and pushed those changes too.

The moment I pushed the code change, Codeipeline responded immediately by triggering a new build and Deploy stage.
The commit message under each stage reflects the latest code change that they're using. 
The Source stage was the first stage to reflect my latest commit message and eventually, the Build and Deploy stage said the same thing too.

Once my pipeline executed successfully, I checked my live web app and can confirm that it updated without me having to manually rebuild the project in CodeBuild and redeploy the project in CodeDeploy.
The changes went live into production straightaway.

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codepipeline-updated_e1f2g3h4)

---

## Testing the Pipeline

In a project extension, I initiated a rollback on the Deploy stage.
Automatic rollback is important for situations where a deployed change passed tests but is actually failing or does not look the way it should.

During the rollback, the source and build stages are unaffected because both stages come before deployment
I could verify this by comparing the commit messages related to each stage.
The deploy stage commit message has reverted to the commit attached to my 2nd pipeline run.
The other 2 stages are still using my latest commit messages.

After rollback, the live web app reverted to its original state before I did any test for changes.
This means that CodeDeploy rolled back successfully and instantly too.

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codepipeline-updated_sdfgsdfgdf)

---

---
