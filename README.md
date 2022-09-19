# mlops-demo-getting-started

This repo is intended to serve as a high level overview of the MLOps Demo resources.

## Deploying The Demo

### Step One: Request a Cluster from RHPDS

From rhpds.redhat.com navigate to `Services` > `Catalogs` and locate the option for `OpenShift 4.10 Workshop` under `Openshift Workshop`.  Choose the option to `Order`.

Keep all of the default options, check the warning box, and select an appropriate `Purpose`.

The cluster will be created usually within 10-15 minutes and you should recieve emails with links to access the cluster along with credentials.

### Step Two: Bootstrap cluster-bootstrap-gitops

Begin by cloning the [cluster-bootstrap-gitops](https://github.com/rh-intelligent-application-practice/cluster-bootstrap-gitops) repo to your local machine.

You must have `oc` and `kustomize` installed on your local machine in order to run the bootstrap script.

If this is the first time you have run this script, you may need to add a Sealed Secret master key.  If any of your gitops repos depend on Sealed Secrets you will need to place a copy of the key in `bootstrap/base/sealed-secrets-secret.yaml`.  The project does not yet deploy any Sealed Secrets so you can skip this step and a master key will be created for you.

Login to the cluster from the command line with a cluster admin user.  To obtain a login string you can utilize the `Copy login command` option from the OpenShift Web Console.

Run the `bootstrap.sh` script from the command line as you see below:

```sh
./bootstrap.sh
```

When prompted to select a bootstrap folder, choose the following:

```sh
1) bootstrap/overlays/rhpds-4.10/
```

OpenShift-Gitops will be installed to the cluster, and several components will be configured to sync in the openshift-gitops instance.  Utilize the URL at the bottom of the script to follow the progress of the sync.  Login using the cluster admin username/password via OpenShift OAuth.  This may take several minutes to complete the sync/install.

### Step Three: Bootstrap mlops-demo-tenant-gitops

Clone the [mlops-demo-tenant-gitops](https://github.com/rh-intelligent-application-practice/mlops-demo-tenant-gitops) repo to your local machine.

Like with the cluster-bootstrap-gitops process, you will need `oc` installed and you will need to be logged into the cluster on the command line.

Run the `bootstrap.sh` script from the command line as you see below:

```sh
./bootstrap.sh
```

Additional ArgoCD Application objects will be created in OpenShift GitOps to be synced.  You can follow the progress of the sync using the same URL.  This sync should complete in a few seconds.

### Step Four: Bootstrap mlops-demo-tenant-gitops

Clone the [mlops-demo-gitops](https://github.com/rh-intelligent-application-practice/mlops-demo-gitops) repo to your local machine.

Like with the repos, you will need `oc` installed and you will need to be logged into the cluster on the command line.

Run the `bootstrap.sh` script from the command line as you see below:

```sh
./bootstrap.sh
```

This script will create several ArgoCD Application objects in the tenant ArgoCD instance and not the cluster openshift-gitops instance.  Utilize the URL at the bottom of the script to follow the progress of the sync.

Once the sync is complete the demo enivoronment is ready to go.

## Running the Demo

Details on how to run the demo go here.

## Repos

### GitOps Repos

#### cluster-bootstrap-gitops

This repo contains resources used to configure cluster level resources, primarily operators.  This repo is intended to be reusable for different demos and contain a core set of OpenShift features that would commonly be used for a Data Science environment.

This repo bootstraps several key resources such as OpenShift-Gitops and Sealed Secrets.  Once the initial components are deployed several ArgoCD Application objects are created which are used to install and manage the install of several operators on the cluster.

One important feature of this repo is that it depends on a Sealed Secret master key which cannot be checked into git.  If you already have a master key on your local machine it will automatically utilize that that key when deploying Sealed Secrets.  If a key is not present, the bootstrap script will prompt you before deploying Sealed Secrets and saving a master key to your local machine for future reuse.  If any of your gitops repos deploy a Sealed Secret object you must have the correct master key to unseal those objects, which means you may need to get the key from the user that initially sealed the secret.

#### mlops-demo-tenant-gitops

This repo is designed to be used alongside the cluster-bootstrap-gitops repo.  Like the cluster-bootstrap-gitops repo this repo is intended to deploy cluster level configurations but instead focuses on components that are specific to the MLOps Demo that are generally in the scope of a Cluster Admin.

The primary goal of this repo is to create the tenant environment.  A tenant is generally a team of application developers that require several namespaces for deploying and managing an application.

This repo creates an oppinionated way for deploying and managing resources for a multi-tiered deployment of application components.

#### mlops-demo-gitops

This repo contains resources that are deployed an managed by the application team in a gitops environment.  This repo is intended to deploy resources to the namespaces created by the tenant-gitops repo utilizing the argocd instance created by that repo.

### Application Repos

Currently there are no application repos

### Other Repos

### helm-charts

The helm-charts repo is designed to create a space to build, maintain, and version custom helm charts that can be reused by multiple projects.  This repo will automatically build and publish charts using GitHub Pages.
