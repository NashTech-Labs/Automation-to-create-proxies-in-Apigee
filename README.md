# Proxy in Apigee

In Apigee, proxies are a fundamental component of the platform and play a crucial role in API management. Proxies act as intermediaries between client applications and backend services. They receive incoming API requests, apply various policies and transformations, and route the requests to the appropriate backend services.

## Creating proxies in Apigee using Jenkins

This repository contains Jenkinsfile which has script for creating proxies in Apigee.

For using this template we have to update our Apigee organisation name in jenkinsfile for parameter:

`def orgList = ['APIGEE_ORGANISATION_NAME']`

Mention the PROXY_BUNDLE_NAME folder name containing proxy bundle name inside proxies folder.

Create a Jenkins pipeline using this repository and build that pipeline by passing parameters to it.


![jenkins_pipeline](https://i.postimg.cc/ncnKKD4j/Screenshot-from-2023-06-18-14-47-43.png)


Build the pipeline and it will create proxies in Apigee.

To verify this move to:

Apigee > Develop > API Proxies
