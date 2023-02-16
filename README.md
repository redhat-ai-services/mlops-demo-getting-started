# mlops-demo-getting-started

This repo is intended to serve as a high level overview of the MLOps Demo resources.

## Deploying The Demo

### Step One: Request a Cluster from RHPDS

Login to the [Red Hat Demo Platform](https://demo.redhat.com), then navigate to `Catalog` > `Workshops`. In the filter option enter the text: `OpenShift 4.11`, then select the `OpenShift 4.11 Workshop` from the card list below. Finally, click the `Order` button to begin provisioning the new cluster.

Enter required information for Activity and Purpose. Select a geo-region closest to you, such as `us-east-2`. Suggest leaving the other options at defaults unless you would like to specifically enable or disable them.

The cluster will be created usually within an hour or so, and you should receive emails with links to access the cluster along with login credentials.

### Step Two: Bootstrap openshift-cluster-bootstrap-gitops

Begin by cloning the [openshift-cluster-bootstrap-gitops](https://github.com/rh-intelligent-application-practice/cluster-bootstrap-gitops) repo to your local machine.

You must have `oc` and `kustomize` installed on your local machine in order to run the bootstrap script.

If this is the first time you have run this script, you may need to add a Sealed Secret master key.  If any of your gitops repos depend on Sealed Secrets you will need to place a copy of the key in `bootstrap/base/sealed-secrets-secret.yaml`.  The project does not yet deploy any Sealed Secrets so you can skip this step and a master key will be created for you.

Login to the cluster from the command line with a cluster admin user.  To obtain a login string you can utilize the `Copy login command` option from the OpenShift Web Console.

Run the `bootstrap.sh` script from the command line as you see below:

```sh
./bootstrap.sh
```

When prompted to select a bootstrap folder, choose the following:

```sh
1) bootstrap/overlays/rhpds-4.11/
```

OpenShift-Gitops will be installed to the cluster, and several components will be configured to sync in the openshift-gitops instance.  Utilize the URL at the bottom of the script to follow the progress of the sync.  Login using the cluster admin username/password via OpenShift OAuth.  This may take several minutes to complete the sync/install.

### Step Three: Bootstrap mlops-demo-tenant-gitops

Clone the [mlops-demo-tenant-gitops](https://github.com/rh-intelligent-application-practice/mlops-demo-tenant-gitops) repo to your local machine.

Like with the openshift-cluster-bootstrap-gitops process, you will need `oc` installed and you will need to be logged into the cluster on the command line.

Run the `bootstrap.sh` script from the command line as you see below:

```sh
./bootstrap.sh
```

Additional ArgoCD Application objects will be created in OpenShift GitOps to be synced.  You can follow the progress of the sync using the same URL.  This sync should complete in a few seconds.

### Step Four: Bootstrap mlops-demo-application-gitops

Clone the [mlops-demo-application-gitops](https://github.com/rh-intelligent-application-practice/mlops-demo-gitops) repo to your local machine.

Like with the repos, you will need `oc` installed and you will need to be logged into the cluster on the command line.

Run the `bootstrap.sh` script from the command line as you see below:

```sh
./bootstrap.sh
```

This script will create several ArgoCD Application objects in the tenant ArgoCD instance and not the cluster openshift-gitops instance.  Utilize the URL at the bottom of the script to follow the progress of the sync.

Once the sync is complete the demo environment is ready to go.

## Running the Demo
### Deploy the inference service
At this point our model is trained and ready to be deployed.
You can access the model training notebook by cloning the following project:

 [rh-intelligent-application-practice/mlops-demo-iris-inference-service](https://github.com/rh-intelligent-application-practice/mlops-demo-iris-inference-service/tree/main/models)

In the following steps, we will: 

- create an image with the trained model:
[iris-model.pkl](https://github.com/rh-intelligent-application-practice/mlops-demo-iris-training-service)

- Deploy the image to Dev, Test, and Production environments

Create an image from the model by running the Openshift pipeline generated for you:

1. Login to the openshift console https://console-openshift-console.apps.cluster-**guid**.**guid**.**sandbox**.opentlc.com
  
2. From the projects, select `mlops-demo-pipelines`

  ![Select Project](images/select-pipelines-project.png "Select Project")
  
3. From the left menu, select the pipelines option.

  ![Select Pipelines](images/select_pipelines.png "Select Pipelines")
  
4. Select the `iris-inference-service` pipeline

5. From the options in the top right corner, select the Start option.

  ![Start Pipeline](images/start_pipeline.png "Start pipeline")
  
6. In the *start pipeline* modal, you will leave most of the parameters as __default__, except for the **Workspaces**

  6.1. Set the `sourcecode-workspace` and `gitops-workspace` as `VolumeClaimTemplate`
  
  ![Start Pipeline Modal](images/start-pipeline-vct.png "Start Pipeline modal")
  
  6.2. In the `Show VolumeClaimTemplate options` for `sourcecode-workspace`, set the size to 5 GB
  
  ![Workspace configuration](images/start-pipeline-vct-size.png "Workspace configuration")
  
7. Click the Start button, and monitor the pipeline to its completion

![Completed Pipeline](images/completed_pipeline.png "Pipeline completed: success")

8. Login to ArgoCD, and validate that all applications are synchronized.

![Sync application](images/synchronized-apps.png "Synchronized applications")

### Grafana dashboard

1. Navigate to the grafana route: https://grafana-route-mlops-demo-dev.apps.cluster-**guid**.**guid**.**sandbox**.opentlc.com

2. use the following credentials to login as Grafana administrator: `grafana_admin:r3dh4t1!`

3. From the left panel, select `Dashboards > manage`

![Grafana Dashboards Manage](images/grafana-dashboards-manage.png "Grafana Dashboards Manage")

4. In the dashboards folders, locate and select the `mlops-demo-dev > Prediction Analytics` dashboard

![Prediction Analytics Dashboard](images/mlops-dev-dashboard-folder.png "Predition Analytics Dashboard")

5. Observe the available dashboards.

![Seldon Core Dashboard](images/seldon-core-dashboard.png "Seldon core dashboard")

### Use the prediction service

1. Open the following Jupyter notebook with your favorite IDE: [mlops-demo-iris-inference-service/notebooks/seldon-request.ipynb](https://github.com/rh-intelligent-application-practice/mlops-demo-iris-inference-service/blob/main/notebooks/seldon-request.ipynb)

```
template for URL:
https://iris-inference-service-mlops-demo-dev.apps.cluster-GUID.GUID.SANDBOX.opentlc.com
```

2. Modify the 4th cell to provide your deployed cluster URL

![Set Service URL](images/cell-url.png "Set Service URL")

3. Run all the cells to send different requests to the inference service.

### Kafka data producer

Explore the kafka data producer:

1. Login to the Openshift web console.
2. Using the topology diagram for the `mlops-demo-dev` namespace, locate the `iris-message-generator` component

![Message generator topology](images/message-generator.png "Message Generator Topology")


3. Observe the `iris-message-generator` pod logs, and observe how every 10 seconds, a new record is generated.

![Message log](images/message-log.png "Message log")


## Repos

### GitOps Repos

#### openshift-cluster-bootstrap-gitops

[rh-intelligent-application-practice/openshift-cluster-bootstrap-gitops](https://github.com/rh-intelligent-application-practice/openshift-cluster-bootstrap-gitops)

This repo contains resources used to configure cluster level resources, primarily operators.  This repo is intended to be reusable for different demos and contain a core set of OpenShift features that would commonly be used for a Data Science environment.

This repo bootstraps several key resources such as OpenShift-Gitops and Sealed Secrets.  Once the initial components are deployed several ArgoCD Application objects are created which are used to install and manage the install of several operators on the cluster.

One important feature of this repo is that it depends on a Sealed Secret master key which cannot be checked into git.  If you already have a master key on your local machine it will automatically utilize that that key when deploying Sealed Secrets.  If a key is not present, the bootstrap script will prompt you before deploying Sealed Secrets and saving a master key to your local machine for future reuse.  If any of your gitops repos deploy a Sealed Secret object you must have the correct master key to unseal those objects, which means you may need to get the key from the user that initially sealed the secret.

#### mlops-demo-tenant-gitops

[rh-intelligent-application-practice/mlops-demo-tenant-gitops](https://github.com/rh-intelligent-application-practice/mlops-demo-tenant-gitops)

This repo is designed to be used alongside the openshift-cluster-bootstrap-gitops repo.  Like the openshift-cluster-bootstrap-gitops repo this repo is intended to deploy cluster level configurations but instead focuses on components that are specific to the MLOps Demo that are generally in the scope of a Cluster Admin.

The primary goal of this repo is to create the tenant environment.  A tenant is generally a team of application developers that require several namespaces for deploying and managing an application.

This repo creates an opinionated way for deploying and managing resources for a multi-tiered deployment of application components.

#### mlops-demo-application-gitops

[rh-intelligent-application-practice/mlops-demo-application-gitops](https://github.com/rh-intelligent-application-practice/mlops-demo-application-gitops)

This repo contains resources that are deployed an managed by the application team in a gitops environment.  This repo is intended to deploy resources to the namespaces created by the tenant-gitops repo utilizing the argocd instance created by that repo.

### Application Repos

#### mlops-demo-iris-training-service

[rh-intelligent-application-practice/mlops-demo-iris-training-service](https://github.com/rh-intelligent-application-practice/mlops-demo-iris-training-service)

This repo contains source code for training the Iris machine learning model.

#### mlops-demo-iris-inference-service

[rh-intelligent-application-practice/mlops-demo-iris-inference-service](https://github.com/rh-intelligent-application-practice/mlops-demo-iris-inference-service)

This repo contains source code for deploying the Iris machine learning model using a custom Seldon wrapper.

### Other Repos

#### helm-charts

[rh-intelligent-application-practice/helm-charts](https://github.com/rh-intelligent-application-practice/helm-charts)

The helm-charts repo is designed to create a space to build, maintain, and version custom helm charts that can be reused by multiple projects.  This repo will automatically build and publish charts using GitHub Pages.
