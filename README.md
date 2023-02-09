# MLOps Demo: Getting Started Guide

This repo provides a high level overview of steps required to provision the MLOps Demo resources using the [Red Hat Demo Platform](https://demo.redhat.com) to host an OpenShift cluster.

## Deploying The Demo

The MLOps demo is provisioned in multiple steps, each step is run from a script contained in a git repo. 

The reason for separate git repos is that each one contains a core unit of work which can be used as a template for customizing specific requirements for your environment at a later date if required.

### Step One: Request a Cluster from RHPDS

Login to the [Red Hat Demo Platform](https://demo.redhat.com), then navigate to `Catalog` > `Workshops`. In the filter option enter the text: `OpenShift 4.11`, then select the `OpenShift 4.11 Workshop` from the card list below. Finally, click the `Order` button to begin provisioning the new cluster.

Enter required information for Activity and Purpose. Select a geo-region closest to you, such as `us-east-2`. Suggest leaving the other options at defaults unless you would like to specifically enable or disable them.

The cluster will be created usually within an hour or so, and you should receive emails with links to access the cluster along with login credentials.

### Step Two: Bootstrap the MLOps OpenShift Cluster GitOps Repo

Clone and follow the instructions found in the [openshift-cluster-bootstrap-gitops](https://github.com/rh-intelligent-application-practice/cluster-bootstrap-gitops) repo.

This bootstrap script will create the OpenShift-Gitops namespace, and several components will be configured to sync in the openshift-gitops instance. 

This may take several minutes to complete the sync/install, you can utilize the ArgoCD URL at the bottom of the script to monitor the progress of the sync.

### Step Three: Bootstrap the MLOps Demo Tenant GitOps Repo

Clone and follow the instructions found in the [mlops-demo-tenant-gitops](https://github.com/rh-intelligent-application-practice/mlops-demo-tenant-gitops) repository.

Additional ArgoCD Application objects will be created in OpenShift GitOps to be synced. You can follow the progress of the sync using the same URL as the previous step. This sync should complete in a few seconds.

### Step Four: Bootstrap the MLOps Demo Application GitOps Repo

Clone and follow the instructions found in the [mlops-demo-application-gitops](https://github.com/rh-intelligent-application-practice/mlops-demo-gitops) repository.

This script will create several ArgoCD Application objects in the tenant ArgoCD instance and not the cluster openshift-gitops instance. Utilize the ArgoCD URL at the bottom of the script to follow the progress of the sync.

Once the sync is complete the demo environment is ready to go.

## Running the Demo

Details on how to run the demo go here.

> **_NOTE:_**  Under construction, see https://app.smartsheet.com/sheets/R52PR9x25fGrjrXwMpCGGmqRRw64vCHgvwchR2p1?rowId=5098253884974980

## Repos

The MLOps demo consists of a number of git repositories:

### GitOps Repos

- Cluster Bootstrap: [openshift-cluster-bootstrap-gitops](https://github.com/rh-intelligent-application-practice/openshift-cluster-bootstrap-gitops) - bootstraps an OpenShift cluster with several operators and other components that are utilized for Machine Learning.

- Tenant GitOps: [mlops-demo-tenant-gitops](https://github.com/rh-intelligent-application-practice/mlops-demo-tenant-gitops) - project structure for a team of application developers that require several namespaces for deploying and managing an application.

- Application GitOps: [mlops-demo-application-gitops](https://github.com/rh-intelligent-application-practice/mlops-demo-application-gitops) - resources that are deployed and managed by the application team.

### Application Repos

- Iris Training Service: [mlops-demo-iris-training-service](https://github.com/rh-intelligent-application-practice/mlops-demo-iris-training-service) - this repo contains source code for training the Iris machine learning model.

- Iris Inference Service: [mlops-demo-iris-inference-service](https://github.com/rh-intelligent-application-practice/mlops-demo-iris-inference-service) - this repo contains source code for deploying the Iris machine learning model using a custom Seldon wrapper.

### Other Repos

- Helm Charts: [rh-intelligent-application-practice/helm-charts](https://github.com/rh-intelligent-application-practice/helm-charts) - this repo is designed to create a space to build, maintain, and version custom helm charts that can be reused by multiple projects. This repo will automatically build and publish charts using GitHub Pages.
