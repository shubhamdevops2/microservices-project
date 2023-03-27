
# Explanation 

In this repository is a microservices software architecture: -

![microservices architecture diagram](microservices.png)

For the below two microservices, They have there own mongo DB setup so they work like there individual project. We are just getting and updating the data from them by using rest API's.

## Example

I have split an "application" into three parts: 

1. **Microservice #1** - User authentication
    * Register - User can register with an email and password 
    * Sign In - User can sign in with email and password
    * Log out - User can log out
2. **Microservice #2** - Game
    * Play game - Once authenticated, user can play a simple game
    * See game results history - Every time the user plays the game, a new record is entered into the database with their score
3. **Front-End User Interface**
    * Controls the interactive Javascript functionality of the application
    * Brings together the two microservices into a **single user experience**.

## To integrate all this projects in jenkins, we have created one common Jenkins groovy script which will run for all this three projects as we are just changing the parameters in there Jenkinsfile.

**Main Parent repository for jenkins which we will stored in shared library**

https://github.com/shubhamdevops2/jenkins-pipeline-shared-resources-micro.git

**Below are the child repositories which have there own Jenkinsfile**
1) User authentication - https://github.com/shubhamdevops2/user-authentication.git
2) Game - https://github.com/shubhamdevops2/game.git
3) Display - https://github.com/shubhamdevops2/display.git

**This child repositories Jenkinsfile will trigger one file with the dynamic parameters from the shared library that we have mentioned like below for dispplay repository for example**

@Library('shared-library-micro@main') _
singleWarBuild{
    ecrRepoName = 'display'
    targetFile = 'package.json'
    dockerFile = 'Dockerfile'
    
}

**This will trigger singleWareBuild file which have various CI stages, as its placed in one jenkins job after completing this job, it will trigger another jenkins job with below repository for CD stages for kubernetes**

https://github.com/shubhamdevops2/kube.git

**This will run the Jenkinsfile.deploy as we have mentioned it in jenkins job**

@Library('shared-library-micro@main') _
helmDeployment{

}

**The above function will trigger the Helm Charts for the deployment by below shared library**

https://github.com/shubhamdevops2/jenkins-pipeline-shared-resources-micro.git

**The shared library is same but this time for the deployement it will use the deployment file.**

**Now it will perform multiple stages and will update the existing project to the latest with zero downtime.**

# Please note that for the deployment you must have kubernetes with two worker nodes and helm install on the main system.

