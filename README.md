# CHAOS EXPERIMENT CHARTS FOR KUBERNETES

These charts provide the specifications for running chaos experiments on a Kubernetes 
cluster and are are consumed by the Litmus Chaos Operator. The charts are categorized
based on the nature of the experiments. For example, chart are catgeorized as : general-K8s
(k8schaos), storage-specific(openebschaos) and application-specific(mysql)charts.  

The charts install Kubernetes custom resources (CRs) with support for creation-time 
injection of chaos parameters (defaults present in Values.yaml). To know more about the
chaos tests themselves and the parameters supported, visit [litmuschaos/litmus](https://github.com/litmuschaos/litmus)

Follow the steps provided below to install the charts on your Kubernetes cluster

## HELM SETUP

- Get Helm

```>> curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh```

- Give permissions 

```>> chmod 700 get_helm.sh```

- Execute Helm installtion scripts

```>> ./get_helm.sh```

- Define the cluster-admin clusterrole (if not present) w/ permissions for all actions on all resources

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
  name: cluster-admin
rules:
- apiGroups:
  - '*'
  resources:
  - '*'
  verbs:
  - '*'
- nonResourceURLs:
  - '*'
  verbs:
  - '*'
```

- Create the clusterrole 

```
>> kubectl create -f clusterrole.yaml
```

- Create Tiller service account in kube-system namespace, bind to clusterrole w/ clusterrolebinding

```
>> kubectl create serviceaccount -n kube-system tiller

>> kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
```

- Initialize helm w/ service account Tiller

```
>> helm init --service-account tiller
```

- Verify that the Tiller pods are up & running

```
>> kubectl --namespace kube-system get pods | grep tiller
  tiller-deploy-2885612843-xrj5m   1/1       Running   0   2d
```


## INSTALL THE CHAOS HELM CHARTS

- Add the litmuschaos helm repo: 

```
>> helm repo add litmuschaos https://litmuschaos.github.io/chaos-charts
"litmuschaos" has been added to your repositories

>> helm repo update
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "litmuschaos" chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈```
```

- List the available charts 

```
>> helm search chaos-charts
NAME                    CHART VERSION   APP VERSION     DESCRIPTION                                       
chaos-charts/k8schaos   0.1.0           1.0             Helm chart for Litmus Kubernetes Chaos Experiments
```

- Install the desired chaos chart from litmuchaos repo

```
helm install litmuschaos/k8schaos
NAME:   kneeling-dingo
LAST DEPLOYED: Fri May  3 16:20:30 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1alpha1/ChaosExperiment
NAME            AGE
container-kill  1s
pod-delete      1s
```
