---
title: Usare File di Azure con il servizio contenitore di Azure
description: Usare Dischi di Azure con il servizio contenitore di Azure
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a5126bc4c5e7c9cd9832f33fc908e6c8b9e02b91
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="persistent-volumes-with-azure-files"></a>Volumi permanenti con i file di Azure

Un volume permanente rappresenta una parte di risorsa di archiviazione di cui è stato eseguito il provisioning per l'uso in un cluster Kubernetes. Un volume permanente può essere usato da uno o più pod e se ne può eseguire il provisioning in modo dinamico o in modo statico. Questo documento illustra in dettaglio il provisioning dinamico di una condivisione di file di Azure come volume Kubernetes permanente in un cluster servizio contenitore di Azure. 

Per altre informazioni sui volumi Kubernetes permanenti, vedere [Kubernetes persistent volumes][kubernetes-volumes] (Volumi Kubernetes permanenti).

## <a name="create-storage-account"></a>Crea account di archiviazione

Quando si esegue il provisioning dinamico di una condivisione di file di Azure come volume Kubernetes, è possibile usare qualsiasi account di archiviazione se è incluso nello stesso gruppo di risorse del cluster AKS. Se necessario, creare un account di archiviazione nello stesso gruppo di risorse del cluster servizio contenitore di Azure. 

Per identificare il gruppo di risorse appropriato, usare il comando [az group list][az-group-list].

```azurecli-interactive
az group list --output table
```

Il gruppo di risorse da cercare ha un nome simile a `MC_clustername_clustername_locaton`, dove clustername è il nome del cluster del servizio contenitore di Azure e location è l'area di Azure in cui è stato distribuito il cluster.

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Usare il comando [az storage account create][az-storage-account-create] per creare l'account di archiviazione. 

Usando questo esempio, aggiornare `--resource-group` con il nome del gruppo di risorse e `--name` con un nome desiderato.

```azurecli-interactive
az storage account create --resource-group MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Creazione della classe di archiviazione

Una classe di archiviazione permette di definire come creare una condivisione file di Azure. Nella classe è possibile indicare un account di archiviazione specifico. Se non si specifica alcun account di archiviazione, è necessario specificare un valore per `skuName` e `location`. Vengono quindi valutati tutti gli account di archiviazione nel gruppo di risorse associato per trovare una corrispondenza.

Per altre informazioni sulle classi di archiviazione Kubernetes per file di Azure, vedere [Kubernetes Storage Classes][kubernetes-storage-classes] (Classi di archiviazione Kubernetes).

Creare un file denominato `azure-file-sc.yaml` e copiarlo nel manifesto seguente. Aggiornare `storageAccount` con il nome dell'account di archiviazione di destinazione.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: mystorageaccount
```

Creare la classe di archiviazione con il comando [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Creare un'attestazione di volume permanente

Un'attestazione di volume permanente usa l'oggetto classe di archiviazione per effettuare il provisioning dinamico di una condivisione file di Azure. 

Il manifesto seguente può essere usato per creare un'attestazione di volume permanente di dimensioni di `5GB` con accesso `ReadWriteOnce`.

Creare un file denominato `azure-file-pvc.yaml` e copiarlo nel manifesto seguente. Assicurarsi che `storageClassName` corrisponda alla classe di archiviazione creata nell'ultimo passaggio.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Creare l'attestazione di volume permanente con il comando [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-file-pvc.yaml
```

Al termine, verrà creata la condivisione file. Viene creato anche un segreto Kubernetes che contiene le credenziali e le informazioni di connessione.

## <a name="using-the-persistent-volume"></a>Uso del volume permanente

Il manifesto seguente crea un pod che usa l'attestazione del volume permanente `azurefile` per montare la condivisione file di Azure nel percorso `/mnt/azure`.

Creare un file denominato `azure-pvc-files.yaml` e copiarlo nel manifesto seguente. Assicurarsi che `claimName` corrisponda all'attestazione di volume permanente creata nell'ultimo passaggio.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

Creare il pod con il comando [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-pvc-files.yaml
```

A questo punto è disponibile un pod in esecuzione con il disco di Azure montato nella directory `/mnt/azure`. È possibile vedere il montaggio del volume quando si controlla il pod tramite `kubectl describe pod mypod`.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui volumi Kubernetes che usano File di Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes per File di Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
