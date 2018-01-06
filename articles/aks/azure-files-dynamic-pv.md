---
title: Usare File di Azure con il servizio contenitore di Azure
description: Usare Dischi di Azure con il servizio contenitore di Azure
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/04/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: d468944883cca80946001724c38dd5ec9ba0d94f
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="persistent-volumes-with-azure-files---dynamic-provisioning"></a>Provisioning permanente per i volumi con file di Azure - dinamici

Un volume permanente rappresenta una parte di spazio di archiviazione è stato eseguito il provisioning per l'utilizzo in un cluster Kubernetes. Un volume persistente può essere utilizzato da uno o più unità e può essere staticamente o dinamicamente il provisioning. Questo documento illustra in dettaglio il provisioning dinamico di una condivisione di file di Azure come volume Kubernetes persistente in un cluster AKS. 

Per ulteriori informazioni sui volumi permanenti Kubernetes, vedere [volumi permanenti Kubernetes][kubernetes-volumes].

## <a name="prerequisites"></a>Prerequisiti

Quando il provisioning dinamico di una condivisione di file di Azure come volume Kubernetes, qualsiasi account di archiviazione è utilizzabile come è inclusa nello stesso gruppo di risorse del cluster AKS. Se necessario, creare un account di archiviazione nello stesso gruppo di risorse del cluster AKS. 

Per identificare il gruppo di risorse appropriato, utilizzare il [elenco gruppi az] [ az-group-list] comando.

```azurecli-interactive
az group list --output table
```

L'output di esempio seguente mostra i gruppi di risorse, che sia associata a un cluster AKS. Il gruppo di risorse con un nome come *MC_myAKSCluster_myAKSCluster_eastus* contiene le risorse del cluster AKS e in cui è necessario creare l'account di archiviazione. 

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Dopo aver individuato il gruppo di risorse, creare l'account di archiviazione con il [creare account di archiviazione az] [ az-storage-account-create] comando.

```azurecli-interactive
az storage account create --resource-group  MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Creare una classe di archiviazione

Una classe di archiviazione viene utilizzata per definire la configurazione di un volume permanente creato dinamicamente. Gli elementi, ad esempio il nome di account di archiviazione di Azure, SKU e area sono definiti nell'oggetto classe di archiviazione. Per ulteriori informazioni sulle classi di archiviazione Kubernetes, vedere [classi di archiviazione Kubernetes][kubernetes-storage-classes].

Nell'esempio seguente specifica che tipo di qualsiasi account di archiviazione di SKU `Standard_LRS` nel `eastus` area utilizzabile per la richiesta di archiviazione. 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  skuName: Standard_LRS
```

Per usare un account di archiviazione specifico, il `storageAccount` parametro può essere utilizzato.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: azure_storage_account_name
```

## <a name="create-persistent-volume-claim"></a>Creare un volume permanente attestazione

Un'attestazione di volume permanente utilizza l'oggetto di classe di archiviazione per il provisioning dinamico di una parte di spazio di archiviazione. Quando si utilizza un file di Azure, viene creata una condivisione di file di Azure nell'account di archiviazione selezionato o specificato nell'oggetto classe di archiviazione.

>  [!NOTE]
>   Verificare che un account di archiviazione appropriato sia stata già creato nello stesso gruppo di risorse del cluster AKS. L'attestazione volume permanente avrà esito negativo per effettuare il provisioning di condividere il file di Azure, se un account di archiviazione non è disponibile. 

Il manifesto seguente consente di creare un'attestazione di volume permanente `5GB` dimensioni con `ReadWriteOnce` accesso. Per ulteriori informazioni sulle modalità di accesso PVC, vedere [modalità di accesso][access-modes].

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

## <a name="using-the-persistent-volume"></a>Utilizzando il volume permanente

Una volta l'attestazione permanente volume è stato creato e lo spazio di archiviazione eseguito correttamente il provisioning, è possibile creare un pod con accesso al volume. Il manifesto seguente crea un pod che utilizza l'attestazione volume permanente `azurefile` montare alla condivisione file di Azure di `/var/www/html` percorso. 

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
      - mountPath: "/var/www/html"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

## <a name="mount-options"></a>Opzioni di montaggio

Valori predefiniti di fileMode e dirMode diversi tra versioni Kubernetes come descritto nella tabella seguente. 

| version | value |
| ---- | ---- |
| V1.6.x, v1.7.x | 0777 |
| V1.8.0 v1.8.5 | 0700 |
| V1.8.6 o versione successiva | 0755 |
| V1.9.0 | 0700 |
| V1.9.1 o versione successiva | 0755 |

Se si utilizza un cluster della versione 1.8.5 o montaggio maggiore, è possibile specificare opzioni per l'oggetto di classe di archiviazione. L'esempio seguente imposta `0777`. 

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

Se si utilizza un cluster della versione 1.8.0 - 1.8.4, è possibile specificare un contesto di sicurezza con il `runAsUser` valore impostato su `0`. Per ulteriori informazioni sul contesto di sicurezza Pod, vedere [configura un contesto di sicurezza][kubernetes-security-context].

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui volumi di permanente Kubernetes utilizzando file di Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes per file di Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create