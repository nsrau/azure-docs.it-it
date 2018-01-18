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
ms.openlocfilehash: 4873b98c8ba4f1e574be20baebef3b6860341529
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="persistent-volumes-with-azure-files---dynamic-provisioning"></a>Volumi permanenti con i file di Azure - provisioning dinamico

Un volume permanente rappresenta una parte di risorsa di archiviazione di cui è stato eseguito il provisioning per l'uso in un cluster Kubernetes. Un volume permanente può essere usato da uno o più pod e se ne può eseguire il provisioning in modo dinamico o in modo statico. Questo documento illustra in dettaglio il provisioning dinamico di una condivisione di file di Azure come volume Kubernetes permanente in un cluster servizio contenitore di Azure. 

Per altre informazioni sui volumi Kubernetes permanenti, vedere [Kubernetes persistent volumes][kubernetes-volumes] (Volumi Kubernetes permanenti).

## <a name="prerequisites"></a>Prerequisiti

Quando si esegue il provisioning dinamico di una condivisione di file di Azure come volume Kubernetes, è possibile usare qualsiasi account di archiviazione se è incluso nello stesso gruppo di risorse del cluster AKS. Se necessario, creare un account di archiviazione nello stesso gruppo di risorse del cluster servizio contenitore di Azure. 

Per identificare il gruppo di risorse appropriato, usare il comando [az group list][az-group-list].

```azurecli-interactive
az group list --output table
```

L'output di esempio seguente mostra i gruppi di risorse, associati entrambi a un cluster servizio contenitore di Azure. Il gruppo di risorse con un nome simile a *MC_myAKSCluster_myAKSCluster_eastus* contiene le risorse del cluster servizio contenitore di Azure e si trova nella posizione in cui è necessario creare l'account di archiviazione. 

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Dopo aver individuato il gruppo di risorse, creare l'account di archiviazione con il comando [az storage account create][az-storage-account-create].

```azurecli-interactive
az storage account create --resource-group  MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Creazione della classe di archiviazione

Una classe di archiviazione viene usata per definire la configurazione di un volume permanente creato dinamicamente. Gli elementi come il nome di account di archiviazione di Azure, SKU, e l'area sono definiti nell'oggetto classe di archiviazione. Per altre informazioni sulle classi di archiviazione Kubernetes, vedere [Kubernetes Storage Classes][kubernetes-storage-classes] (Classi di archiviazione Kubernetes).

L'esempio seguente specifica che in una richiesta di risorsa di archiviazione è possibile usare qualsiasi account di archiviazione di tipo `Standard_LRS` di SKU nell'area `eastus`. 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  skuName: Standard_LRS
```

Per usare un account di archiviazione specifico è possibile usare il parametro `storageAccount`.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: azure_storage_account_name
```

## <a name="create-persistent-volume-claim"></a>Creare un'attestazione di volume permanente

Un'attestazione di volume permanente usa l'oggetto classe di archiviazione per il provisioning dinamico di una parte di spazio di archiviazione. Quando si usa File di Azure, viene creata una condivisione di file di Azure nell'account di archiviazione selezionato o specificato nell'oggetto classe di archiviazione.

>  [!NOTE]
>   Verificare che sia stato già creato un account di archiviazione appropriato nello stesso gruppo di risorse delle risorse del cluster servizio contenitore di Azure. Questo gruppo di risorse ha un nome simile a *MC_myAKSCluster_myAKSCluster_eastus*. L'attestazione di volume permanente non riuscirà a effettuare il provisioning della condivisione di file di Azure se non è disponibile un account di archiviazione. 

Il manifesto seguente può essere usato per creare un'attestazione di volume permanente di dimensioni di `5GB` con accesso `ReadWriteOnce`. Per altre informazioni sulle modalità di accesso PVC, vedere [Access Modes][access-modes] (Modalità di accesso).

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

## <a name="using-the-persistent-volume"></a>Uso del volume permanente

Una volta creata l'attestazione del volume permanente ed eseguito correttamente il provisioning delle risorse di archiviazione, è possibile creare un pod con accesso al volume. Il manifesto seguente crea un pod che usa l'attestazione del volume permanente `azurefile` per montare la condivisione file di Azure nel percorso `/var/www/html`. 

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

I valori predefiniti fileMode e dirMode differiscono tra le versioni di Kubernetes come descritto nella tabella seguente. 

| version | valore |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v1.8.6 o successiva | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 o successiva | 0755 |

Se si usa un cluster della versione 1.8.5 o superiore, è possibile specificare opzioni di montaggio per l'oggetto classe di archiviazione. L'esempio imposta `0777`. 

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

Se si usa un cluster della versione 1.8.0 - 1.8.4, è possibile specificare un contesto di protezione con il valore `runAsUser` impostato su `0`. Per altre informazioni sul contesto di sicurezza Pod, vedere [Configure a Security Context][kubernetes-security-context] (Configurazione di un contesto di protezione).

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
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create