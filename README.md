# JupyterHubKyma
JupyterHub running on SAP Cloud Platform Kyma Runtime (Kubernetes) with XSUAA OAUTH2 authentication

Exanmple of runing a shared JupyteHub environment that enables XSUAA autenticated users to access.
Also included is the hana-ml python library to simplify connection to SAP Hana with pandas dataframes, as well as providing the ability to push down ML logic to HANA reducing data being copied. 

## Pre Reqs:
* Provision a Kyma environment on SAP Cloud Platform
* Create a namespace e.g. "mlteam"
* Download your Kyma/K8S access kubeconfig.yml and set KUBECONFIG environment variable to point to kubeconfig.yml
> ### Windows cmd
>> Set KUBECONFIG=C:\Users\<user>\Downloads\kubeconfig.yml 
> ### Windows Powershell
>> $env:KUBECONFIG="C:\Users\<user>\Downloads\kubeconfig.yml" 
> ### Linux
>> export KUBECONFIG=/<path>/kubeconfig.yml 

* Check the cluster in your new namespace
> kubectl cluster-info  -n mlteam


## STEP 1 - Create Persitant volume claim 
<strong> To be used for storage of Jupyter Notebooks and associated files </strong>  
kubectl replace --force -n mlteam -f deployment_pvc.yaml


## STEP 2 - Create XSUAA service  
<strong> NOTE: Ensure xsapp does not already exist, deleted previous or rename if required </strong>  

Deploy xsuaa service which creates role in SCP CF 
> kubectl replace --force -n mlteam -f deployment_xsuaa.yaml

Manually generate the XSUAA credentials in Kyma  UI 
> Select namespace created (e.g. mlteam)
> Navigate to 'Service Management -> Instances -> xsuaa-jupyterhub-mlteam'  
> Select 'Credentials' Tab  
> Click 'Create Credentials' Button  
> Click on the generated credentials   
> Click 'Decode' button  


## STEP 3 -Deploy JupypterHub and Service access point

Manually update <strong> deployment_jupyterhub.yaml </strong> with
* clientid / secret from Step 2  
* urls to domain specific XSUAA service  
* cluster info  

Deploy Jupyterhub
> kubectl replace --force -n mlteam -f deployment_jupyterhub.yaml

NOTE:  Deployment may take ~10 minutes as includes a jupyter build step. If you have docker repository you can prepare an docker file / images containing the more time consuming steps. You would then refer to this image in the deployment yaml.  

NOTE2: Occassionally you may see in the Pod logs that the the Jupyter build failed due to low memory.  Re-running usually resolves, but if problem persists increase memory of pod and/or create an image with the JupyterHub pre-built  

## STEP 4 (optional) Add a shared folder in Jupyter terminal window
> ln -s  /home/mlteam $HOME/mlteam


## Commonly used Kubcetl commands while deploying and checking
> kubectl -n mlteam get services  
> kubectl get APIRule -n mlteam jupyterhub -o yaml  
> kubectl get secret -n mlteam <manually generated secret> -o yaml  
> kubectl get ServiceInstance -n mlteam  
  
> kubectl get pods -n mlteam  
> kubectl exec --stdin -n mlteam --tty jupyterhub-<POD ID> -- /bin/bash  




