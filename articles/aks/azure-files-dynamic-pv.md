---
title: Creare dinamicamente la condivisione di File di Azure
titleSuffix: Azure Kubernetes Service
description: Informazioni su come creare in modo dinamico un volume persistente con i File di Azure per l'uso con pod multipli contemporaneamente nel servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 5e9e686d8da420c650709d3bedc103d0043fa679
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287105"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Creare dinamicamente e usare un volume persistente con File di Azure nel servizio Azure Kubernetes

Un volume permanente rappresenta una parte di risorsa di archiviazione di cui è stato eseguito il provisioning per l'uso con pod Kubernetes. Un volume permanente può essere usato da uno o più pod e se ne può eseguire il provisioning in modo dinamico o in modo statico. Se più pod necessitano di accesso simultaneo allo stesso volume di archiviazione, è possibile usare File di Azure per connettersi usando il [protocollo Server Message Block (SMB)][smb-overview]. Questo articolo illustra come creare in modo dinamico una condivisione di file di Azure per l'uso da più POD in un cluster del servizio Azure Kubernetes.

Per altre informazioni sui volumi Kubernetes, vedere [Opzioni di archiviazione per le applicazioni nel servizio Azure Kubernetes][concepts-storage].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

È anche necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.59 o versione successiva. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="create-a-storage-class"></a>Creare una classe di archiviazione

Una classe di archiviazione permette di definire come creare una condivisione file di Azure. Nel [gruppo di risorse nodo][node-resource-group] viene creato automaticamente un account di archiviazione da usare insieme alla classe di archiviazione per contenere le condivisioni file di Azure. Scegliere la [ridondanza dell'archiviazione di Azure][storage-skus] seguente per *skuName*:

* *Standard_LRS*: archiviazione con ridondanza locale standard
* *Standard_GRS*: archiviazione con ridondanza geografica standard
* *Standard_ZRS*: archiviazione con ridondanza della zona standard (ZRS)
* *Standard_RAGRS*: archiviazione con ridondanza geografica e accesso in lettura standard
* *Premium_LRS*: archiviazione con ridondanza locale Premium (LRS)

> [!NOTE]
> File di Azure supporta l'archiviazione Premium nei cluster del servizio Azure Kubernetes che eseguono Kubernetes 1.13 o versione successiva. Le dimensioni minime della condivisione file Premium sono pari a 100 GB

Per altre informazioni sulle classi di archiviazione Kubernetes per File di Azure, vedere l'articolo sulle [classi di archiviazione di Kubernetes][kubernetes-storage-classes].

Creare un file denominato `azure-file-sc.yaml` e copiarlo nell'esempio di manifesto seguente. Per altre informazioni su *mountOptions*, vedere la sezione [Opzioni di montaggio][mount-options].

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
parameters:
  skuName: Standard_LRS
```

Creare la classe di archiviazione con il comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Creare un'attestazione di volume permanente

Un'attestazione di volume permanente usa l'oggetto classe di archiviazione per effettuare il provisioning dinamico di una condivisione file di Azure. È possibile usare il codice YAML seguente per creare un'attestazione di volume permanente di dimensioni pari a *5 GB* con accesso *ReadWriteMany*. Per altre informazioni sulle modalità di accesso, vedere la documentazione sui [volumi permanenti di Kubernetes][access-modes].

Creare ora un file denominato `azure-file-pvc.yaml` e copiarlo nel codice YAML seguente. Assicurarsi che *storageClassName* corrisponda alla classe di archiviazione creata nell'ultimo passaggio:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> Se si usa lo SKU *Premium_LRS* per la classe di archiviazione, il valore minimo per *storage* deve essere *100Gi*.

Creare l'attestazione di volume permanente con il comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f azure-file-pvc.yaml
```

Al termine, verrà creata la condivisione file. Viene creato anche un segreto Kubernetes che comprende le credenziali e le informazioni di connessione. È possibile usare il comando [kubectl get][kubectl-get] per visualizzare lo stato dell'attestazione di volume permanente:

```console
$ kubectl get pvc my-azurefile

NAME           STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
my-azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            my-azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Usare il volume permanente

Il seguente YAML crea un pod che usa il volume permanente Claim *My-azurefile* per montare la condivisione file di Azure nel percorso */mnt/Azure* . Per i contenitori Windows Server specificare un valore per *mountPath* usando la convenzione di percorso di Windows, ad esempio *'D:'* .

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
        claimName: my-azurefile
```

Creare il pod con il comando [kubectl apply][kubectl-apply].

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
    ClaimName:  my-azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Opzioni di montaggio

Il valore predefinito per *fileMode* e *dirMode* è *0777* per Kubernetes 1.13.0 e versioni successive. Se si crea dinamicamente il volume permanente con una classe di archiviazione, è possibile specificare le opzioni di montaggio nell'oggetto classe di archiviazione. L'esempio seguente imposta *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
parameters:
  skuName: Standard_LRS
```

## <a name="next-steps"></a>Passaggi successivi

Per le procedure consigliate associate, vedere [Procedure consigliate per archiviazione e backup nel servizio Azure Kubernetes][operator-best-practices-storage].

Per i parametri della classe di archiviazione, vedere [provisioning dinamico](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#dynamic-provision).

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
