---
title: Creare in modo dinamico un volume di file per pod multipli nel servizio Azure Kubernetes
description: Informazioni su come creare in modo dinamico un volume persistente con i File di Azure per l'uso con pod multipli contemporaneamente nel servizio Azure Kubernetes
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/08/2019
ms.author: mlearned
ms.openlocfilehash: 580363973afd918351931edfb187a1a8d38d6985
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "67665979"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Creare dinamicamente e usare un volume persistente con File di Azure nel servizio Azure Kubernetes

Un volume permanente rappresenta una parte di risorsa di archiviazione di cui è stato eseguito il provisioning per l'uso con pod Kubernetes. Un volume permanente può essere usato da uno o più pod e se ne può eseguire il provisioning in modo dinamico o in modo statico. Se più POD necessitano di accesso simultaneo allo stesso volume di archiviazione, è possibile usare File di Azure per connettersi usando il [protocollo SMB (Server Message Block)][smb-overview]. Questo articolo illustra come creare in modo dinamico una condivisione di file di Azure per l'uso da più POD in un cluster del servizio Azure Kubernetes.

Per altre informazioni sui volumi Kubernetes, vedere [Opzioni di archiviazione per le applicazioni in AKS][concepts-storage].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS esistente. Se è necessario un cluster AKS, vedere la Guida introduttiva di AKS [usando l'interfaccia della][aks-quickstart-cli] riga di comando di Azure o [l'portale di Azure][aks-quickstart-portal].

È necessaria anche l'interfaccia della riga di comando di Azure versione 2.0.59 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia][install-azure-cli]della riga di comando di Azure.

## <a name="create-a-storage-class"></a>Creare una classe di archiviazione

Una classe di archiviazione permette di definire come creare una condivisione file di Azure. Viene creato automaticamente un account di archiviazione nel [gruppo di risorse nodo][node-resource-group] da usare con la classe di archiviazione per archiviare le condivisioni file di Azure. Scegliere la seguente ridondanza di [archiviazione di Azure][storage-skus] per *SKUName*:

* *Standard_LRS*: archiviazione con ridondanza locale standard
* *Standard_GRS*: archiviazione con ridondanza geografica standard
* *Standard_RAGRS*: archiviazione con ridondanza geografica e accesso in lettura standard

> [!NOTE]
> File di Azure supportano archiviazione Premium nei cluster AKS che eseguono Kubernetes 1,13 o versione successiva.

Per altre informazioni sulle classi di archiviazione Kubernetes per File di Azure, vedere [classi di archiviazione Kubernetes][kubernetes-storage-classes].

Creare un file denominato `azure-file-sc.yaml` e copiarlo nell'esempio di manifesto seguente. Per ulteriori informazioni su *mountOptions*, vedere la sezione [Opzioni di montaggio][mount-options] .

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

Creare la classe di archiviazione con il comando [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>Creare un ruolo e un'associazione del cluster

I cluster servizio Azure Kubernetes usano il controllo degli accessi in base al ruolo Kubernetes per limitare le azioni che possono essere eseguite. I *ruoli* definiscono le autorizzazioni da concedere e le *associazioni* le applicano agli utenti desiderati. Queste assegnazioni possono essere applicate a uno spazio dei nomi specifico o all'intero cluster. Per ulteriori informazioni, vedere [utilizzo dell'autorizzazione RBAC][kubernetes-rbac].

Per consentire alla piattaforma Azure di creare le risorse di archiviazione necessarie, creare un *clusterrole* e un *clusterrolebinding*. Creare un file denominato `azure-pvc-roles.yaml` e copiarlo nel codice YAML seguente:

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:azure-cloud-provider
rules:
- apiGroups: ['']
  resources: ['secrets']
  verbs:     ['get','create']
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:azure-cloud-provider
roleRef:
  kind: ClusterRole
  apiGroup: rbac.authorization.k8s.io
  name: system:azure-cloud-provider
subjects:
- kind: ServiceAccount
  name: persistent-volume-binder
  namespace: kube-system
```

Assegnare le autorizzazioni con il comando [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Creare un'attestazione di volume permanente

Un'attestazione di volume permanente usa l'oggetto classe di archiviazione per effettuare il provisioning dinamico di una condivisione file di Azure. Lo YAML seguente può essere usato per creare un'attestazione di volume permanente con una dimensione di *5 GB* con accesso *ReadWriteMany*. Per ulteriori informazioni sulle modalità di accesso, vedere la documentazione relativa al [volume permanente Kubernetes][access-modes] .

Creare ora un file denominato `azure-file-pvc.yaml` e copiarlo nel codice YAML seguente. Assicurarsi che *storageClassName* corrisponda alla classe di archiviazione creata nell'ultimo passaggio:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Creare l'attestazione del volume permanente con il comando [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-pvc.yaml
```

Al termine, verrà creata la condivisione file. Viene creato anche un segreto Kubernetes che comprende le credenziali e le informazioni di connessione. È possibile usare il comando [kubectl Get][kubectl-get] per visualizzare lo stato del PVC:

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Usare il volume permanente

Lo YAML seguente crea un pod che usa l'attestazione di volume permanente *azurefile* per montare la condivisione file di Azure nel percorso */mnt/azure*. Per i contenitori di Windows Server (attualmente in anteprima in AKS), specificare un *mountPath* usando la convenzione di percorso di Windows, ad esempio *' d:'* .

Creare un file denominato `azure-pvc-files.yaml` e copiarlo nel codice YAML seguente. Assicurarsi che *claimName* corrisponda all'attestazione di volume permanente creata nell'ultimo passaggio.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

Creare il pod con il comando [kubectl Apply][kubectl-apply] .

```console
kubectl apply -f azure-pvc-files.yaml
```

È ora disponibile un pod in esecuzione con la condivisione file di Azure montata nella directory */mnt/azure*. Questa configurazione può essere visualizzata durante il controllo del pod tramite `kubectl describe pod mypod`. L'esempio sintetico di output seguente illustra il volume montato nel contenitore:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Opzioni di montaggio

I valori predefiniti *fileMode* e *dirMode* sono diversi a seconda della versione di Kubernetes, come descritto nella tabella seguente.

| version | value |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v1.8.6 o successiva | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 o successiva | 0755 |

Se si usa un cluster della versione 1.8.5 o superiore e si crea in modo dinamico il volume permanente con una classe di archiviazione, è possibile specificare le opzioni di montaggio nell'oggetto classe di archiviazione. L'esempio seguente imposta *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

Se si usa un cluster di una versione da 1.8.0 a 1.8.4, è possibile specificare un contesto di protezione con il valore *runAsUser* impostato su *0*. Per altre informazioni sul contesto di sicurezza di Pod, vedere [configurare un contesto di sicurezza][kubernetes-security-context].

## <a name="next-steps"></a>Passaggi successivi

Per le procedure consigliate associate, vedere procedure consigliate [per l'archiviazione e i backup in AKS][operator-best-practices-storage].

Altre informazioni sui volumi Kubernetes che usano File di Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes per File di Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks