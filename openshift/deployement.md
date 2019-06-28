# Example: Deployment with openshift
Purpose of this file : Deploy a simple docker image on an OpenShift application

##  SETUP
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
Sometime you need to add 2 secrets, one for the registry and one for the repository (case in gitlab-ci forge)

Then just link your secret(s) to an account:
```
oc secrets link default secret1 secret2 --for=pull
```

**You should now be able to do your deployment**

## DEPLOY AN APPLICATION
###Optional: add an image in OpenShift repository
It can be done via ImageStream, but in a more easy way:
```
oc tag --source=docker gitlab_registry/myproject/image:latest image:latest
```
note : I simulate an image from a gitlab registry. The tag latest force the automatic update with ImagePullPolicy in 
deployment YML.

### Template
An easy way to automate deployment and assure that there is no digression is to use a template.
Here an easy example. I want to deploy a smashing dashboard from a docker image:
```yaml
apiVersion: v1
kind: Template
labels:
  template: smashing-template
metadata:
  name: smashing-template

parameters:

objects:
- apiVersion: v1
  kind: Service
  metadata:
    labels:
      app: smashing
    name: smashing
  spec:
    ports:
    - name: 3030-tcp
      port: 3030
      protocol: TCP
      targetPort: 3030
    selector:
      app: smashing
    sessionAffinity: None
    type: ClusterIP


- apiVersion: route.openshift.io/v1
  kind: Route
  metadata:
    labels:
      app: smashing
    name: smashing-route
  spec:
    host: MySuperDashboard.com
    port:
      targetPort: 3030-tcp
    to:
      kind: Service
      name: smashing
      weight: 100
    wildcardPolicy: None

- apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: smashing-deployment
    labels:
      app: smashing
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: smashing
    template:
      metadata:
        labels:
          app: smashing
      spec:
        containers:
          - name: smashing
            image: interner.registry:5000/MyProject/smashing:latest
            ports:
            - name: smashing
              protocol: TCP
              containerPort: 3030
```
Quickly: In this example, we deploy a new service : "smashing". We associate a 'route' to it (an url). Then we define
our deployment (which image to use, replicas etc...)

###Execute a template
```
oc process -f oc_template.yml  | oc apply -f -
```
We transform the template in readable objetcs and send them to OpenShift.