# Running Calcom with Kubernetes on Minikube

## Important Notes

This Docker Image is managed by the Cal.com Community. Join the team [here](https://github.com/calcom/docker/discussions/32). Support for this image can be found via the repository, located at [https://github.com/calcom/docker](https://github.com/calcom/docker)

**Currently, this image is intended for local development/evaluation use only, as there are specific requirements for providing environmental variables at build-time in order to specify a non-localhost BASE_URL. (this is due to the nature of the static site compilation, which embeds the variable values). The ability to update these variables at runtime is in-progress and will be available in the future.**

For Production, for the time being, please checkout the repository and build/push your own image privately.

## Requirements
```bash
linux os (ubuntu)
```

## Starting Minikube

### 1. Start Minikube:
```bash
minikube start
```

## Setting up PostgreSQL 

### 1. Create your connection configuration and secrets:
```bash
# Insert PostgreSQL secret YAML here
kubectl apply -f postgres-secret.yaml
```
### 2. Then verify that the contents are stored correctly:
```bash
kubectl get secret postgres-secret
```
### 3. Create PersistentVolume and PersistentVolumeClaim:
```bash
kubectl apply -f postgres-pv.yaml # Insert PostgreSQL PersistentVolume YAML here
kubectl apply -f postgres-pvc.yaml # Insert PostgreSQL PersistentVolumeClaim YAML here
```
### 4. Now,check that the persistent volume is available:
```bash
kubectl get pv
```
### 5. Now,check that the persistent volume claim is bound:
```bash
kubectl get pvc
```

### 6. Create PostgreSQL deployment:
```bash
# Insert PostgreSQL deployment YAML here
kubectl apply -f postgres-deployment.yaml
```
### 7. Create PostgreSQL Service:
```bash
# Insert PostgreSQL Service YAML here
kubectl apply -f postgres-svc.yaml
```


## Deploying Calcom

### 1. Create your secrets:
```bash
# Insert Calcom secret YAML here
kubectl apply -f calcom-secret.yaml
```
### 2. Create your  configMap:
```bash
# Insert Calcom Config YAML here
kubectl apply -f calcom-config.yaml
```

### 3. Deploy Calcom:
```bash
# Insert Calcom deployment YAML here
kubectl apply -f calcom-deployment.yaml
```
### 4. Create Calcom Service:
```bash
# Insert Calcom Service YAML here
kubectl apply -f calcom-svc.yaml
```

## Deploying Studio

### 1. Deploy Studio:
```bash
# Insert Studio deployment YAML here
kubectl apply -f studio-deployment.yaml
```

### 2. Create Calcom Service:
```bash
# Insert Studio Service YAML here
kubectl apply -f studo-svc.yaml
```

# Setting Up Ingress in Kubernetes

### 1. Enable Ingress Addon

Ensure the Ingress addon is enabled in your Minikube cluster:

```bash
minikube addons enable ingress
```

### 2. Modifying Hosts File and Ingress in Linux

#### Edit Local Hosts File

1. **Open Terminal:**
   Launch your preferred terminal application.

2. **Get minikube ip:**
    ```bash
    minikube ip
    ```
3. **Open the Hosts File:**
   Use a command-line text editor like `nano` to open the `hosts` file:
   ```bash
   sudo nano /etc/hosts
   ```
4. **Edit the File:**
    Add a new line at the end of the file:
    ```bash
    <minikube-ip> your-hostname.com
    ```
    Replace "minikube-ip" with the IP address of your Minikube cluster and your-hostname.com with the desired hostname.

5. **Save the File:**
         Save the changes (in nano, it's Ctrl + O, then Enter to confirm, and Ctrl + X to exit).

6. **Modify your Ingress YAML (ingress.yaml) to use the defined hostname:**
    Replace your-hostname.com, your-service-path, your-service-name, and your-service-port with your actual service details.

## Configuration

### Important Run-time variables

These variables must also be provided at runtime

| Variable | Description | Required | Default |
| --- | --- | --- | --- |
| CALCOM_LICENSE_KEY | Enterprise License Key | optional |  |
| NEXT_PUBLIC_WEBAPP_URL | Base URL of the site.  NOTE: if this value differs from the value used at build-time, there will be a slight delay during container start (to update the statically built files). | optional | `http://localhost:3000` |
| NEXTAUTH_URL | Location of the auth server. By default, this is the Cal.com docker instance itself. | optional | `{NEXT_PUBLIC_WEBAPP_URL}/api/auth` |
| NEXTAUTH_SECRET | must match build variable | required | `secret` |
| CALENDSO_ENCRYPTION_KEY | must match build variable | required | `secret` |
| DATABASE_URL | database url with credentials | required | `postgresql://unicorn_user:magical_password@database:5432/calendso` |

### Build-time variables

If building the image yourself, these variables must be provided at the time of the docker build, and can be provided by updating the .env file. Currently, if you require changes to these variables, you must follow the instructions to build and publish your own image. 

Updating these variables is not required for evaluation, but is required for running in production. Instructions for generating variables can be found in the [cal.com instructions](https://github.com/calcom/cal.com) 

| Variable | Description | Required | Default |
| --- | --- | --- | --- |
| NEXT_PUBLIC_WEBAPP_URL | Base URL injected into static files | optional | `http://localhost:3000` |
| NEXT_PUBLIC_LICENSE_CONSENT | license consent - true/false |  |  |
| CALCOM_TELEMETRY_DISABLED | Allow cal.com to collect anonymous usage data (set to `1` to disable) | | |
| DATABASE_URL | database url with credentials | required | `postgresql://unicorn_user:magical_password@database:5432/calendso` |
| NEXTAUTH_SECRET | Cookie encryption key | required | `secret` |
| CALENDSO_ENCRYPTION_KEY | Authentication encryption key | required | `secret` |

