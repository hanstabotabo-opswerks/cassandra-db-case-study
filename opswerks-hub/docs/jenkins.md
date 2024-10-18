# Install Jenkins with Helm v3

A typical Jenkins deployment consists of a controller node and, optionally, one or more agents. To simplify the deployment of Jenkins, we’ll use Helm to deploy Jenkins. Helm is a package manager for Kubernetes and its package format is called a chart. Many community-developed charts are available on GitHub.

Helm Charts provide “push button” deployment and deletion of apps, making adoption and development of Kubernetes apps easier for those with little container or microservices experience.

## Prerequisites

### Helm Command Line Interface
If you don’t have the Helm command line interface installed and configured locally, follow the sections below to install and configure Helm.

### Install Helm
To install Helm CLI, follow the instructions from the [Installing Helm page](https://helm.sh/docs/intro/install/).

### Configure Helm
Once Helm is installed and set up properly, add the Jenkins repo as follows:

```bash
helm repo add jenkinsci https://charts.jenkins.io
helm repo update
```

You can list the Helm charts in the Jenkins repo using this command:

```bash
helm search repo jenkinsci
```


## Create a Service Account
Create a service account called `jenkins` by pasting the content from [this URL](https://raw.githubusercontent.com/installing-jenkins-on-kubernetes/jenkins-sa.yaml) into a file named `jenkins-sa.yaml` and applying it with:

```bash
kubectl apply -f jenkins-sa.yaml
```

## Install Jenkins
We will deploy Jenkins using the Jenkins Kubernetes plugin.

Paste the content from [this URL](https://raw.githubusercontent.com/jenkinsci/helm-charts/main/charts/jenkins/values.yaml) into a file called `jenkins-values.yaml`. Modify it to set:

- `nodePort: 32001
- Set the service account under `serviceAccount`:

```yaml
serviceAccount:
  create: false
  name: jenkins
  annotations: {}
```

Install Jenkins using Helm with the following command:

```bash
helm install jenkins -n jenkins -f jenkins-values.yaml jenkinsci/jenkins
```

## Retrieve Jenkins Admin Password
You can retrieve the admin password using the following command:

```bash
jsonpath="{.data.jenkins-admin-password}"
secret=$(kubectl get secret -n jenkins jenkins -o jsonpath=$jsonpath)
echo $(echo $secret | base64 --decode)
```

## Access Jenkins
After installation, check the status of your Jenkins pod:

```bash
kubectl get pods -n jenkins
```

To access the Jenkins UI, use port forwarding:

```bash
kubectl -n jenkins port-forward <pod_name> 8080:8080
```

Then, visit `http://127.0.0.1:8080/` and log in with `admin` as the username and the password retrieved earlier.nothing yet
