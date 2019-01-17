---
title: Kubernetes in Azure - Ridimensionamento automatico del cluster
description: Informazioni su come usare il ridimensionamento automatico del cluster con il servizio Azure Kubernetes per ridimensionare automaticamente il cluster e soddisfare la richiesta.
services: container-service
author: sakthivetrivel
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/19/18
ms.author: sakthivetrivel
ms.custom: mvc
ms.openlocfilehash: 178dd53046dc9e9b332272914b3aafbb80bc5f4c
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214981"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Ridimensionamento automatico del cluster nel servizio Azure Kubernetes - Anteprima

Il servizio Azure Kubernetes fornisce una soluzione flessibile per la distribuzione di un cluster Kubernetes gestito in Azure. Con la crescita delle richieste di risorsa, il ridimensionamento automatico del cluster consente l'aumento delle dimensioni del cluster per soddisfare tale richiesta, in base ai vincoli impostati. Il ridimensionamento automatico del cluster (CA) esegue tale operazione, ridimensionando i nodi agente in base ai pod in sospeso. Analizza il cluster periodicamente per cercare in pod in sospeso o nodi vuoti e ne aumenta la dimensione, se possibile. Per impostazione predefinita, CA analizza i pod in sospeso ogni 10 secondi e rimuove un nodo, se non risulta necessario per più di 10 minuti. Se usato con il [ridimensionamento automatico orizzontale dei pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) (HPA), l'attributo di protezione host aggiornerà le repliche di pod e le risorse in base alla richiesta. Se non vi sono nodi sufficienti o nodi non necessari applicando la scalabilità del pod, la CA risponderà e pianificherà i pod nel nuovo set di nodi.

Questo articolo descrive come distribuire il ridimensionamento automatico del cluster nei nodi agente. Tuttavia, poiché il ridimensionamento automatico del cluster viene distribuito nello spazio dei nomi kube-system, non ridurrà le prestazioni del nodo che esegue questo pod.

> [!IMPORTANT]
> L'integrazione di ridimensionamento automatico del cluster di Azure AD per il servizio Azure Kubernetes è attualmente in **anteprima**. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.
>

## <a name="prerequisites-and-considerations"></a>Prerequisiti e considerazioni

Questo documento presuppone che si abbia già un cluster servizio Azure Kubernetes di RBAC abilitato. Se è necessario un cluster del servizio Azure Kubernetes, vedere la [guida introduttiva al servizio Azure Kubernetes][aks-quick-start].

 Per sfruttare il ridimensionamento automatico del cluster, il cluster deve usare Kubernetes v1.10.X o versione successiva e deve essere abilitato per RBAC. Per aggiornare il cluster, vedere l'articolo sull'[aggiornamento di un cluster servizio Azure Kubernetes][aks-upgrade].

Definire le richieste di risorse per i pod. La funzione di ridimensionamento automatico del cluster esamina le richieste di risorse create dai pod, non le risorse effettivamente in uso, come invece fa la funzione di ridimensionamento automatico orizzontale dei pod. All'interno della sezione `spec: containers` della definizione di distribuzione, definire i requisiti di CPU e memoria. Il frammento di codice di esempio seguente richiede 0,5 vCPU e 64 Mb di memoria nel nodo:

  ```yaml
  resources:
    requests:
      cpu: 500m
      memory: 64Mb
  ```

Se si usa la funzione di ridimensionamento automatico del cluster, evitare di ridimensionare manualmente il numero di nodi. La funzione di ridimensionamento automatico del cluster potrebbe non essere in grado di determinare la quantità corretta di risorse di calcolo necessaria ed entrare in conflitto con il numero di nodi definito manualmente dall'utente.

## <a name="gather-information"></a>Raccogliere informazioni

Per generare le autorizzazioni per il ridimensionamento automatico del cluster per l'esecuzione nel cluster, eseguire questo script bash:

```sh
#! /bin/bash
ID=`az account show --query id -o json`
SUBSCRIPTION_ID=`echo $ID | tr -d '"' `

TENANT=`az account show --query tenantId -o json`
TENANT_ID=`echo $TENANT | tr -d '"' | base64`

read -p "What's your cluster name? " cluster_name
read -p "Resource group name? " resource_group

CLUSTER_NAME=`echo $cluster_name | base64`
RESOURCE_GROUP=`echo $resource_group | base64`

PERMISSIONS=`az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$SUBSCRIPTION_ID" -o json`
CLIENT_ID=`echo $PERMISSIONS | sed -e 's/^.*"appId"[ ]*:[ ]*"//' -e 's/".*//' | base64`
CLIENT_SECRET=`echo $PERMISSIONS | sed -e 's/^.*"password"[ ]*:[ ]*"//' -e 's/".*//' | base64`

SUBSCRIPTION_ID=`echo $ID | tr -d '"' | base64 `

NODE_RESOURCE_GROUP=`az aks show --name $cluster_name  --resource-group $resource_group -o tsv --query 'nodeResourceGroup' | base64`

echo "---
apiVersion: v1
kind: Secret
metadata:
    name: cluster-autoscaler-azure
    namespace: kube-system
data:
    ClientID: $CLIENT_ID
    ClientSecret: $CLIENT_SECRET
    ResourceGroup: $RESOURCE_GROUP
    SubscriptionID: $SUBSCRIPTION_ID
    TenantID: $TENANT_ID
    VMType: QUtTCg==
    ClusterName: $CLUSTER_NAME
    NodeResourceGroup: $NODE_RESOURCE_GROUP
---"
```

Una volta completati i passaggi nello script, lo script restituirà i dettagli sotto forma di segreto, in questo modo:

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>$
  ResourceGroup: <base64-encoded-resource-group>  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

Ottenere quindi il nome del pool di nodi eseguendo il comando seguente. 

```console
$ kubectl get nodes --show-labels
```

Output:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

Quindi, estrarre il valore dell'etichetta **agentpool**. Il nome predefinito per il pool di nodi di un cluster è "nodepool1".

Usando ora il segreto e il pool di nodi, è possibile creare un grafico di distribuzione.

## <a name="create-a-deployment-chart"></a>Creare un grafico di distribuzione

Creare un file denominato `aks-cluster-autoscaler.yaml` e copiarvi il codice YAML seguente.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["events","endpoints"]
  verbs: ["create", "patch"]
- apiGroups: [""]
  resources: ["pods/eviction"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["pods/status"]
  verbs: ["update"]
- apiGroups: [""]
  resources: ["endpoints"]
  resourceNames: ["cluster-autoscaler"]
  verbs: ["get","update"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["watch","list","get","update"]
- apiGroups: [""]
  resources: ["pods","services","replicationcontrollers","persistentvolumeclaims","persistentvolumes"]
  verbs: ["watch","list","get"]
- apiGroups: ["extensions"]
  resources: ["replicasets","daemonsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["policy"]
  resources: ["poddisruptionbudgets"]
  verbs: ["watch","list"]
- apiGroups: ["apps"]
  resources: ["statefulsets", "replicasets"]
  verbs: ["watch","list","get"]
- apiGroups: ["storage.k8s.io"]
  resources: ["storageclasses"]
  verbs: ["get", "list", "watch"]

---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["configmaps"]
  resourceNames: ["cluster-autoscaler-status"]
  verbs: ["delete","get","update"]

---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cluster-autoscaler
  template:
    metadata:
      labels:
        app: cluster-autoscaler
    spec:
      serviceAccountName: cluster-autoscaler
      containers:
      - image: gcr.io/google-containers/cluster-autoscaler:v1.2.2
        imagePullPolicy: Always
        name: cluster-autoscaler
        resources:
          limits:
            cpu: 100m
            memory: 300Mi
          requests:
            cpu: 100m
            memory: 300Mi
        command:
        - ./cluster-autoscaler
        - --v=3
        - --logtostderr=true
        - --cloud-provider=azure
        - --skip-nodes-with-local-storage=false
        - --nodes=1:10:nodepool1
        env:
        - name: ARM_SUBSCRIPTION_ID
          valueFrom:
            secretKeyRef:
              key: SubscriptionID
              name: cluster-autoscaler-azure
        - name: ARM_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: ResourceGroup
              name: cluster-autoscaler-azure
        - name: ARM_TENANT_ID
          valueFrom:
            secretKeyRef:
              key: TenantID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_ID
          valueFrom:
            secretKeyRef:
              key: ClientID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_SECRET
          valueFrom:
            secretKeyRef:
              key: ClientSecret
              name: cluster-autoscaler-azure
        - name: ARM_VM_TYPE
          valueFrom:
            secretKeyRef:
              key: VMType
              name: cluster-autoscaler-azure
        - name: AZURE_CLUSTER_NAME
          valueFrom:
            secretKeyRef:
              key: ClusterName
              name: cluster-autoscaler-azure
        - name: AZURE_NODE_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: NodeResourceGroup
              name: cluster-autoscaler-azure
      restartPolicy: Always
```

Copiare e incollare il segreto creato nel passaggio precedente e inserirlo all'inizio del file.

Successivamente, per impostare l'intervallo di nodi, compilare l'argomento per `--nodes` sotto `command` nel modulo MIN:MAX:NODE_POOL_NAME. Ad esempio: `--nodes=3:10:nodepool1` imposta il numero minimo di nodi a 3, il numero massimo di nodi a 10 e il nome del pool di nodi a nodepool1.

Quindi, compilare il campo immagine sotto **contenitori** con la versione della scalabilità automatica del cluster che si desidera usare. servizio Azure Kubernetes richiede v1.2.2 o versione successiva. In questo esempio usa v1.2.2.

## <a name="deployment"></a>Distribuzione

Distribuire il ridimensionamento automatico del cluster eseguendo

```console
kubectl create -f aks-cluster-autoscaler.yaml
```

Per verificare se il ridimensionamento automatico del cluster è in esecuzione, usare il comando seguente e controllare l'elenco dei pod. Dovrebbe essere presente un pod con prefisso "cluster-autoscaler" in esecuzione. Se è presente, il ridimensionamento automatico del cluster è stata distribuito.

```console
kubectl -n kube-system get pods
```

Per visualizzare lo stato del ridimensionamento automatico del cluster, eseguire

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

## <a name="interpreting-the-cluster-autoscaler-status"></a>Interpretazione dello stato di ridimensionamento automatico del cluster

```console
$ kubectl -n kube-system describe configmap cluster-autoscaler-status
Name:         cluster-autoscaler-status
Namespace:    kube-system
Labels:       <none>
Annotations:  cluster-autoscaler.kubernetes.io/last-updated=2018-07-25 22:59:22.661669494 +0000 UTC

Data
====
status:
----
Cluster-autoscaler status at 2018-07-25 22:59:22.661669494 +0000 UTC:
Cluster-wide:
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 registered=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC

NodeGroups:
  Name:        nodepool1
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0 cloudProviderTarget=1 (minSize=1, maxSize=5))
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 cloudProviderTarget=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC


Events:  <none>
```

Lo stato di ridimensionamento automatico del cluster può essere visualizzato in due livelli diversi: a livello di cluster e all'interno di ogni gruppo di nodi. Poiché servizio Azure Kubernetes attualmente supporta solo un pool di nodi, queste metriche sono identiche.

* Stato indica lo stato complessivo dei nodi. Se il ridimensionamento automatico del cluster cerca di creare o rimuovere nodi nel cluster, lo stato passa a "Unhealthy". Viene anche presentata un'analisi dello stato dei diversi nodi:
    * "Ready" significa che un nodo è pronto per includere pod pianificati.
    * "Unready" significa che un nodo è stato interrotto dopo l'avvio.
    * "NotStarted" significa che un nodo non è ancora avviato completamente.
    * "LongNotStarted" significa che un nodo non è stato in grado di avviarsi entro un limite di tempo ragionevole.
    * "Registered" significa che un nodo è registrato nel gruppo.
    * "Unregistered" significa che un nodo è presente nel lato provider del cluster, ma non è stato registrato in Kubernetes.
  
* "ScaleUp" permette di controllare quando il cluster determina che è necessario un aumento delle prestazioni nel cluster.
    * Una transizione avviene quando il numero di nodi nel cluster cambia o quando cambia lo stato di un nodo.
    * Il numero di nodi pronti è il numero di nodi disponibili e pronti nel cluster. 
    * "cloudProviderTarget" è il numero di nodi che il ridimensionamento automatico del cluster ha determinato come necessario perché il cluster possa gestire il proprio carico di lavoro.

* "ScaleDown" permette di controllare se vi sono candidati per la riduzione delle prestazioni. 
    * Un candidato per la riduzione delle prestazioni è un nodo che il ridimensionamento automatico del cluster ha determinato come rimovibile senza influire sulla capacità del cluster di gestire il proprio carico di lavoro. 
    * I tempi forniti indicano l'ultima volta che il cluster è stato controllato per individuare eventuali candidati per la riduzione delle prestazioni e la data e l'ora dell'ultima transazione.

Alla voce Events, infine, è possibile visualizzare qualsiasi evento di aumento o riduzione delle prestazioni, riuscito o non riuscito, insieme ai rispettivi momenti, eseguito dal ridimensionamento automatico del cluster.

## <a name="next-steps"></a>Passaggi successivi

Per utilizzare il ridimensionamento automatico del cluster con il ridimensionamento automatico orizzontale dei pod, consultare [scalabilità dell'applicazione di Kubernetes e infrastruttura][aks-tutorial-scale].

Esercitazioni sul servizio Azure Container che ne illustrano la distribuzione e la gestione.

> [!div class="nextstepaction"]
> [Esercitazione sul servizio contenitore di Azure][aks-tutorial-prepare-app]

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md

<!-- LINKS - external -->
[cluster-autoscale]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md
