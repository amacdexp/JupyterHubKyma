# JupyterHubKyma
JupyterHub running on SAP Cloud Platform Kyma Runtime (Kubernetes) with XSUAA OAUTH2 authentication

Run a shared jupyter hub environment that enables XSUAA autenticated users to access.

## Pre Reqs:
* Provision a Kyma environment on SAP Cloud Platform
* Create a namespace e.g. "mlteam"
* Download your Kyma/K8S access kubeconfig.yaml and set KUBECONFIG environment variable
> ## windows cmd
>> Set KUBECONFIG=C:\Users\<user>\Downloads\kubeconfig.yml 
> ## windows powershell
>> $env:KUBECONFIG="C:\Users\<user>\Downloads\kubeconfig.yml" 
> ## linux
>> export KUBECONFIG=/<path>/kubeconfig.yml 

* Check the cluster in your new namespace
> kubectl cluster-info  -n mlteam


## STEP 1 - Create Persitant volume claim 
<strong> To be used for storage of Jupyter Notebooks and associated files </strong>
kubectl replace --force -n mlteam -f deployment_pvc.yaml


## STEP 2 - Create XSUAA service  
<strong> NOTE: Ensure xsapp does not already exist, deleted previous or rename if required </strong>
kubectl replace --force -n mlteam -f deployment_xsuaa.yaml
