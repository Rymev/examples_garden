# Example: Deployment with openshift
Purpose of this file : Deploy a simple docker image on an OpenShift application

## Prerequisite
*Objectives: connect to the CLI, the good project, create a service account, add credentials
 for a specific repository*
### Work with OpenShift CLI
You must install OpenShift: https://www.okd.io/download.html
You must install OpenShift: https://www.okd.io/download.html
Then you can log in:
```
# Human use
oc login server:port

# Automated use
oc login server:port --token='MY_5UP3R_S3CRET_T0K3N'

# Select the good project
oc project My_super_project
```

### Create an user
If you want to automatise deployment, or give access to someone else, you may create a "service account":
```
oc create serviceaccount gitlab_dedicated_account
```
Then you must add rights
```
oc policy add-role-to-user edit -z gitlab_dedicated_account
```
-z to specify it's a service account, 'edit' is the right.

### Optional: add a private registry
If you want to work with a private registry, you need to add a dedicated secret
```
oc create secret docker-registry <secret_name> \
--docker-password=1234 \
--docker-server=registry.somewhere.com \
--docker-username=Remy  \
--docker-email=myadress@wanadoo.com
```