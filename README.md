# CI/CD Pipeline for Stan's Robot Shop

This repository contains the source code from [Stan's Robot Shop](https://github.com/instana/robot-shop), a sample microservice application. The application is designed to demonstrate various technologies and practices in building and deploying microservices, including containerization, orchestration, and monitoring, based on the original [robot-shop](https://github.com/instana/robot-shop), you can get more information from the [Instana blog post](https://www.instana.com/blog/stans-robot-shop-sample-microservice-application/).

The repository is used to build and deploy the application using a CI/CD pipeline. The pipeline automates the process of building, testing, and deploying the application to various environments, ensuring that changes can be delivered quickly and reliably.

Stan's Robot Shop is a sample microservice application you can use as a sandbox to test and learn containerised application orchestration and monitoring techniques. It is not intended to be a comprehensive reference example of how to write a microservices application, although you will better understand some of those concepts by playing with Stan's Robot Shop. To be clear, the error handling is patchy and there is not any security built into the application.

This sample microservice application has been built using these technologies:
- NodeJS ([Express](http://expressjs.com/))
- Java ([Spring Boot](https://spring.io/))
- Python ([Flask](http://flask.pocoo.org))
- Golang
- PHP (Apache)
- MongoDB
- Redis
- MySQL ([Maxmind](http://www.maxmind.com) data)
- RabbitMQ
- Nginx
- AngularJS (1.x)

The tools integrated into the CI/CD pipeline include:
- [Docker](https://www.docker.com/)
- [Docker Compose](https://docs.docker.com/compose/)
- [Jenkins](https://www.jenkins.io/)
- [Gitlab](https://about.gitlab.com/)
- [sonarQube](https://www.sonarqube.org/)
- [trivy](https://aquasecurity.github.io/trivy/)


## Workflow
The CI/CD pipeline is designed to automate the build, test, and deployment processes for Stan's Robot Shop. The workflow includes the following steps:
![workflow](/img/workflow.png)

# Steps
## Create Jenkins Server
Create and setup a Jenkins server to manage the CI/CD pipeline. Install the necessary plugins for Docker, Git, and any other tools required for the pipeline.

Create a api token in Jenkins for authentication. This token will be used to trigger the pipeline from Gitlab.
 - Go to `Manage Jenkins` > `Manage Users` > `Security` > `API Token` and create a new token.
![jenkinsAPIToken](/img/jenkinsAPIToken.png)

## Create and configure Gitlab Server
Create and setup a Gitlab server to host the source code repository for Stan's Robot Shop.
![createdGitlab](/img/createdGitlab.png)
Create a new project in Gitlab and push the source code to the repository. Configure the Gitlab CI/CD pipeline to trigger builds and tests on code changes.
![repo](/img/repo.png)
Go to the repository settings and create a webhook in Gitlab to trigger the Jenkins pipeline on code changes. In the URL field, enter the value with syntax `http://<username>:<token>@<IPofJenkins>:8080/job/<jobName>`.


The token is the API token created in Jenkins, and the job name is the name of the Jenkins job that will be triggered.
![webhook](/img/createWebhookFromGitlab.png)

Also, create a personal access token in Gitlab to allow Jenkins to access the repository. Go to `Settings` > `Access Tokens` and create a new token with the required scopes.
![createToken](/img/createToken.png)

Enable outbound requests from Gitlab to Jenkins. This is necessary for the webhook to work correctly. Go to `Settings` > `General` and enable the `Outbound requests` option.
![allowOutboundRequests](/img/allowOutboundRequest.png)


## Create and configure SonarQube Server
Create and setup a SonarQube server to analyze the code quality of Stan's Robot Shop.
![createdSonarQube](/img/CreatedSonarqube.png)
Create new token in SonarQube for authentication. This token will be used to trigger the SonarQube analysis from Jenkins.
 - Go to `My Account` > `Security` > `Generate Tokens` and create a new token.
![sonarToken](/img/sonarToken.png)

Create a webhook in SonarQube to run in the Jenkins pipeline on code changes. In the URL field, enter the URL of the Jenkins server.

![CreateWebhook](/img/createWebhook.png)

## Configure Jenkins Server
Go to `Manage Jenkins` > `Configure System` and configure the following:
- **Gitlab**: Add the Gitlab server URL and the API token created in the previous step.
![addGitlab](/img/addGitLab.png)
- **SonarQube**: Add the SonarQube server URL and the token created in the previous step.
![sonarServer](/img/sonarServer.png)

Go to `Manage Jenkins` > `Credentials` and add the following credentials:
![crednetials](/img/credentials.png)
- **gitlab**: Add the Gitlab personal access token created in the previous step.
- **sonar**: Add the SonarQube token created in the previous step.
- **docker**: Add the Docker Hub credentials to push the images to the Docker Hub repository.
- **gitlab-user**: Add the Gitlab username to access the repository.
- **NVD-API-KEY**: Add the NVD API key to access the NVD database for vulnerability scanning.


Connect the Jenkins agent to the Jenkins server. This can be done by installing the Jenkins agent on the machine where the pipeline will run and connecting it to the Jenkins server.
![connectAgent](/img/connectAgent.png)
![createdAgent](/img/createdAgent.png)



## Create Jenkins Pipeline
Create a new Jenkins pipeline job to build, test, and deploy Stan's Robot Shop.
Go to `New Item` > `Pipeline` and enter a name for the job. Configure the pipeline to be triggered by the Gitlab webhook created in the previous step.
![configPipeline](/img/configPipelineToBeTrigger.png)
In the pipeline section, select `Pipeline script from SCM` and choose `Git`. Enter the Gitlab repository URL and the branch to build.

![configurePipeline](/img/configPipeline.png)

Go back to repository settings in Gitlab > `webhooks` and test the webhook to ensure it is working correctly. The webhook should trigger the Jenkins pipeline and start the build process.
![testTrigger](/img/testTrigger.png)

Save the pipeline configuration and run the pipeline to test the setup. The pipeline should trigger on code changes and run the build, test, and deployment steps.
![donePipeline](/img/donePipeline.png)

See the analysis results in SonarQube.
![sonarqubeResults](/img/sonarqubeResult.png)

The trivy scan results can be seen in the Jenkins console output.
![trivyScan](/img/trivyScan.png)

The images are pushed to the Docker Hub repository.
![imagesPushed](/img/imagesPushed.png)

We can access to the application using the IP address of the Jenkins agent and the port 8080.
![deploySuccessfully](/img/deploySuccessfully.png)



