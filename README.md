# Building a CI/CD Pipeline to Azure App Service

 

## Introduction

In this project, you will build a Github repository from scratch and create a scaffolding that will assist you in performing both Continuous Integration and Continuous Delivery. You'll use Github Actions along with a Makefile, requirements.txt and application code to perform an initial lint, test, and install cycle. Next, you'll integrate this project with Azure Pipelines to enable Continuous Delivery to Azure App Service.

## Project Plan

* Link to Trello's board for the project - (https://trello.com/b/gu4ptXY3/azure-udacity-project-2)
* Link to a spreadsheet - (https://github.com/TreAt1412/udacity2/blob/main/plan.xlsx)

## Pre requisites

* An Azure Account ( https://portal.azure.com/)
* A GitHub Account ( http://github.com/)
* An Azure DevOps Account (https://dev.azure.com/)

## First: CI Pipeline with GitHub Actions
 

### Architectural Diagram - CI - GitHub Actions

* Architectural Diagram 

 ![alt text](https://github.com/jfcb853/Udacity-DevOps-Azure-Project-2/blob/main/images/ci-diagram.png)
  
### Instructions

* Enter to Azure Portal with your Account and open a Azure Cloud shell ( use Bash)
 

* First of all set up SSH Keys in your azure cloud shell, add the `id_rsa.pub` key to your GitHub repo ( ssh keys)  and then clone the project there.

```sh
ssh-keygen -t rsa -b 2048 -C "dovietanh74@gmail.com"
 cat ~/.ssh/id_rsa.pub
```
![gen key with azure cloud shell](https://github.com/user-attachments/assets/96649109-b725-4f6c-9d8e-d4a40c5f33d6)

* then upload public key to Github account setting 

![upload key to github action](https://github.com/user-attachments/assets/9bd965eb-65dc-43cd-b473-5199b189c5f9)


* Clone project
   
* Project Scaffolding ( files):

| Plugin | README |
| ------ | ------ |
| Makefile | to create shortcuts to build, test, and deploy a project|
| requirements.txt| to list what packages a project needs |
| hello.py | a basic python app |
| test_hello.py | the test python file to above app|

 Create a virtual environment for your application.

```sh
python3 -m venv ~/.myrepo
source ~/.myrepo/bin/activate
```

* Run `make all` which will install, lint, and test code

```sh
make all
```
 ![result of make all](https://github.com/user-attachments/assets/1f8817d4-ba70-4b8c-8a7c-a0079b765c6f)


* Next set up Github Actions in your repo do this :

replace action file with new code

![update github action file](https://github.com/user-attachments/assets/70efec24-cda8-4368-995b-ffb4af19e3e3)

Go to GItHUb Actions and you will see the Workflow in action!!!

![run github action success](https://github.com/user-attachments/assets/08108411-8996-4820-9ce5-0c2da8801fff)


## Second : CI/CD Pipeline with AZURE DEVOPS

  

* Go to Azure Devops page  and sign in it, create a new Project inside your organization ( if you don't have an organization create one first).

* In your new Project in Azure DevOps, go to Project Settings and create a new `Pipeline --> Service Connection` as is explained on the YouTube video link  below ( Service Connection must be of Type Azure Resource Manager)

>  Name your Service Connection `AZServiceConnection`
>   Use a link of as this `https://dev.azure.com/<organization>/<project_name>/_apis/serviceendpoint/endpoints?api-version=5.0-preview.2`  to find your ServiceConnectionID   

![get service connection id](https://github.com/user-attachments/assets/f6a8c5a7-0fde-4306-9277-3b37840cbbeb)

* Create the webapp deploying the code from the local workspace to Azure app Service ( using Free Plan)  with this command:

```sh
az webapp up -n <name of webapp> --location eastus --sku F1 -g <azurerg>
```

![create web app success](https://github.com/user-attachments/assets/25a16459-ec3a-4e3d-b2d0-69b0adfcae36)

 Your service URL  will be something like this : `https://<name of webapp>.azurewebsites.net/`
 

* In  your new Project in Azure DevOps, go to Pipelines -->New Pipeline --> GitHub --> Select Your Repo --> select `an Existing YAML file` --> replace code with code in azure-pipelines.yaml file
update **azureServiceConnectionId** with service connection id we get before
update **webAppName** with web app name created before
![pipeline](https://github.com/user-attachments/assets/cb099db6-3eba-4645-a57e-bd4a031c56a3)

![pipeline ](https://github.com/user-attachments/assets/0cdca192-01ad-43f0-8cf2-470dee691d8e)
 
 

save and see result of pipeline deployment

![run pipeline success](https://github.com/user-attachments/assets/6e5d2572-87a1-4108-b417-d29693673293)


* Check that the webapp is running opening his URL, example:

```sh
https://flask-anhdv29-123.azurewebsites.net/
```
 ![web deploy success](https://github.com/user-attachments/assets/8b962bfa-c2ad-4557-8eb5-e214db554935)

* Update the file `make_predict_azure_app.sh` with the webapp service end point
 
* run below command to test our application

```sh
./make_predict_azure_app.sh
```

Answer:

```sh
Port: 443
{"prediction":[20.123123]}
```
![run predict file success](https://github.com/user-attachments/assets/00b116e7-08f6-41c1-9cf8-0754980cfd5b)

* Logging: Check on webapp log tail `az webapp log tail` , the answer like this:
 ![web log](https://github.com/user-attachments/assets/496ecff9-e54b-4263-84b0-ce9443269c78)


* Execute a Load Testing , running Locust .
 
```cmd
locust -f locustfile.py --host https://flask-anhdv29-123.azurewebsites.net/ --users 500 --spawn-rate 5 
```

>from there you can see how the load testing is performing and how you app established on a # of RPS
> This is good to know how good is your webapp and your plan to manage the requests. So you can decide to scale up the plan service of your webapp for example.
-> Locust could generate a stats report.
>
![locust result](https://github.com/user-attachments/assets/7cdba41e-a0ca-4d17-87b5-f72791f15fec)

![locust report](https://github.com/user-attachments/assets/72ded0e7-77c4-425d-b511-7dbffb46d7b6)

Remaining screenshots can be checked in the screenshot folder.

## Future Enhancements

* Adding more test cases.
* Creating a UI for making predictions.
* Using Github Actions instead of Azure pipelines.
* Run the app on Kubernetes cluster


## YouTube Demo
## Todo
Link to YouTube Demo Video -> (https://www.youtube.com/watch?v=qLqSj2UjvGQ)
 
