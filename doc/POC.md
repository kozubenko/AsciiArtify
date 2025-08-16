No problem, here is a prettified and copy-pastable version of the Argo CD installation guide.

-----

# 🚀 Getting Started with Argo CD

This guide provides the steps to install and configure **Argo CD** for both a standard Kubernetes cluster and a local development environment using **Kind**.

-----

## ✅ Requirements

  * Installed `kubectl` command-line tool.
  * A valid `kubeconfig` file (default: `~/.kube/config`).
  * For local setup: `kind` installed and a cluster created.
  * CoreDNS enabled.

-----

## 🌐 Standard Installation

### 1\. Install Argo CD

Create the namespace and apply the installation manifests:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 2\. Install Argo CD CLI

Download the latest version from the [releases page](https://github.com/argoproj/argo-cd/releases) or install via Homebrew:

```bash
brew install argocd
```

💡 **Tip:** Use `argocd login --core` to configure CLI access without exposing the API server or retrieving the password manually.

### 3\. Create an Application

#### 🖥️ CLI

Create an example application from a Git repository:

```bash
kubectl config set-context --current --namespace=argocd
argocd app create guestbook \
  --repo https://github.com/argoproj/argocd-example-apps.git \
  --path guestbook \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default
argocd app sync guestbook
```

#### 🌍 UI

Open your browser and navigate to the Argo CD UI.

1.  Click **`+ New App`**.
2.  Fill in the details:
      * **Application Name:** `guestbook`
      * **Project:** `default`
      * **Sync Policy:** `Manual`
3.  Set the **Source Repository**:
      * **Repository URL:** `https://github.com/argoproj/argocd-example-apps.git`
      * **Revision:** `HEAD`
      * **Path:** `helm-guestbook`
4.  Set the **Destination**:
      * **Cluster URL:** `https://kubernetes.default.svc`
      * **Namespace:** `default`
5.  Click **`Create`** to deploy the application.

-----

## 🖥️ Local Installation with Kind

This section describes how to set up a local development environment.

### 1\. Install Kind & Create a Cluster

Follow the instructions [here](https://www.google.com/search?q=https://kind.sigs.k8s.io/docs/user/quick-start/%23installation) to install Kind. Then, create a local cluster:

```bash
kind create cluster --name argocd-cluster
kubectl cluster-info --context kind-argocd-cluster
```

### 2\. Install Argo CD

Apply the manifests to the Kind cluster:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 3\. Access the UI

Expose the Argo CD API server locally via port-forwarding:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Now open `http://localhost:8080` in your browser.

### 4\. Login to Argo CD

Retrieve the default admin password:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath='{.data.password}' | base64 -d
```

  * **Username:** `admin`
  * **Password:** *retrieved value*

You can now log in to the UI or CLI and start creating and syncing applications.

From [step 7](https://argo-cd.readthedocs.io/en/stable/getting_started/#7-sync-deploy-the-application) you can create and think test app. 

As a result you will have something like this

![image](../Screenshot%202025-08-16%20at%2023.03.37.png)