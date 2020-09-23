# Generator for Kubernetes resources using Kustomize and Skaffold

This generator creates the resources to deploy a Spring Boot application and a MySQL Database.

You will need to install the following command line tools

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
* [kustomize](https://kubernetes-sigs.github.io/kustomize/installation/)
* [skaffold](https://skaffold.dev/docs/install/)

If you want to use Helm to deploy services, install it's CLI

* [helm v3 cli](https://github.com/helm/helm/releases/tag/v3.3.1)

If you used the starter service, then the generator has already been executed and you can immediately deploy the application's required services and then build and deploy the application.

## Deploy required services

If your application needs services such as a Database or Message Queue, this section shows you how to deploy these services to Kubernetes.

For MySQL, you have two deployment options, pick one.

### Option 1:  MySQL using simple deployment/service YAML

**Step 1:** Create a secret:

```
kubectl create secret generic mysql \
  --from-literal=mysql-root-password=$(echo $RANDOM) \
  --from-literal=mysql-password=$(echo $RANDOM)
```

**Step 2:** Deploy the service

```
skaffold run -p local-services
```

#### Deleting

```bash
skaffold delete -p local-services
```

### Option 2: MySQL using Helm

You should have Helm v3 installed.

**Step 1:** Add the Bitnami Helm chart repository to your installation:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

**Step 2:** Create the service release:

```bash
skaffold run -p local-services-helm
```

#### Deleting

To delete the service release:

```bash
skaffold delete -p local-services-helm
```

### Building and deploying the application

This generator relies on the [Jib Maven Plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin)

If you are using Minikube you should configure your terminal to use the same Docker environment:

```bash
eval $(minikube docker-env)
```

**Step 1:** Build the project, create the container image, and deploy to Kubernetes

```bash
skaffold run -p local
```


Use `kubectl get all` to verify that the resources created.

Accessing the application's endpoint varies based on the type of Kubernetes cluster you are using.  For example, if minikube is being used, look for the endpoint to access using the command `minikube service list`

As you make changes to the application, execute the `skaffold run -p local` command to rebuild the container and update the application running in the Kubernetes cluster.

### Deleting

```bash
skaffold delete -p local
```

## Generator Commands

The Tanzu Starter Service can run these generator commands on the server when creating a new project.  Alternatively, you can run the generator command using the Tanzu Starter Service CLI named `tss`.

**TODO: LINK TO DOWNLOAD CLIENT**

`k8s new` creates

* A `skaffold.yaml` file in the root directory
* A kustomize directory structure with an overlay named `local`.  The kustomize file is located in `kubernetes/overlays/local/kustomization.yaml`
* Adds the [Jib Maven Plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) to the Maven `pom.xml` under the profile `local`
* Adds an Spring Actuator dependency to the Maven `pom.xml` if it is not already present

`k8s update` creates
* A kustomize directory structure with overlays named `local-services` and `local-services-helm` for MySql if the project has a dependency on the `mysql-connector-java` library.

## Running the application and services

Once the generator commands have be executed you can run the application and services.