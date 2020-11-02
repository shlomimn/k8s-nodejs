# k8s-nodejs

# Task
Write all necessary Kubernetes files to deploy a simple NodeJS process to the cluster.

# General
All .yaml files create the required deployment.

Using *kubectl kustomization . > allInOne.yaml*
I have concatinated all .yaml files in the correct applying order.
Run CMD to apply all files in a single command:
* kubectl apply -f allInOne.yaml

# Tolerations 
- No need to use a real repo.
- No need to build the docker image 
  Thus, Docker image “nodejs-api” will not be pulled.

# Requirements & Details
1. The pod should belong to the “api-servers” namespace.
   * Relevant file: namespace.yaml
   * All .yaml files include *namespace: api-server* in metadata.
   
2. Reference for secret file:
“docker-registry-secret” (assume that secret is already exists).
   * I have create docker-registry-secret.txt file locally -> includes a single password inside.
   
```   
[root@shlomime k8s]# cat docker-registry-secret 
#!@VeryStr0ngSecret1!#
```


   * I encrepted in base64 and transfered the password in file above into *secret.yaml*
   *kubectl create secret generic db-secrets --from-file=./docker-registry-secret -o yaml --namespace=api-servers*

```
[root@shlomime k8s]# kubectl get secrets 
NAME                   TYPE                                  DATA   AGE
db-secrets             Opaque                                1      12s
```


   * In deployment.yaml,
     I configured an env section which sets the above password as a env parameter,
     The following is shown by using an nginx image instead of nodejs-api using the same files
     
```
[root@shlomime k8s]# kubectl get pod
NAME                         READY   STATUS    RESTARTS   AGE
my-deploy-848d7d4865-4rmjn   1/1     Running   0          51s
my-deploy-848d7d4865-nsn9h   1/1     Running   0          51s
[root@shlomime k8s]# 
[root@shlomime k8s]# 
[root@shlomime k8s]# kubectl exec -it my-deploy-848d7d4865-4rmjn bash
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
root@my-deploy-848d7d4865-4rmjn:/# echo $SECRET_PASSWORD
#!@VeryStr0ngSecret1!#
root@my-deploy-848d7d4865-4rmjn:/# 
```





