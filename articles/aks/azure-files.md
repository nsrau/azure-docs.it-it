---
title: Usare File di Azure con il servizio contenitore di Azure
description: Usare Dischi di Azure con il servizio contenitore di Azure
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/17/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b6267dd2bc1b29229b2e8016e2429ed88b7bf676
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="using-azure-files-with-kubernetes"></a>Utilizzo di File di Azure con Kubernetes

Le applicazioni basate su contenitore hanno spesso necessità di accedere e salvare in modo permanente i dati in un volume di dati esterno. È possibile usare File di Azure come archivio dati esterno. Questo articolo descrive in dettaglio l'utilizzo di File di Azure come volume Kubernetes nel servizio contenitore di Azure.

Per altre informazioni sui volumi Kubernetes, vedere [Kubernetes volumes][kubernetes-volumes] (Volumi Kubernetes).

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure

Prima di usare una condivisione file di Azure come volume Kubernetes, è necessario creare un account di archiviazione di Azure e la condivisione file. Per completare queste attività, usare lo script seguente. Prendere nota o aggiornare i valori dei parametri perché alcuni sono necessari quando si crea il volume Kubernetes.

```azurecli-interactive
# Change these four parameters
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create the Resource Group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create the storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)
```

## <a name="create-kubernetes-secret"></a>Creare un segreto Kubernetes

Kubernetes necessita di credenziali per accedere alla condivisione file. Queste credenziali vengono archiviate in un [segreto Kubernetes][kubernetes-secret], cui viene fatto riferimento durante la creazione di un pod Kubernetes.

Quando si crea un segreto Kubernetes, i valori del segreto devono essere con codificata base64.

Codificare prima il nome dell'account di archiviazione. Se necessario, sostituire `$AKS_PERS_STORAGE_ACCOUNT_NAME` con il nome del proprio account di archiviazione di Azure.

```azurecli-interactive
echo -n $AKS_PERS_STORAGE_ACCOUNT_NAME | base64
```

Codificare quindi la chiave dell'account di archiviazione. Se necessario, sostituire `$STORAGE_KEY` con il nome della chiave dell'account di archiviazione di Azure.

```azurecli-interactive
echo -n $STORAGE_KEY | base64
```

Creare un file denominato `azure-secret.yaml` e copiarlo nel codice YAML seguente. Aggiornare i valori `azurestorageaccountname` e `azurestorageaccountkey` con i valori codificati con il sistema Base64 recuperati nel passaggio precedente.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: azure-secret
type: Opaque
data:
  azurestorageaccountname: <base64_encoded_storage_account_name>
  azurestorageaccountkey: <base64_encoded_storage_account_key>
```

Usare il comando [kubectl create][kubectl-create] per creare il segreto.

```azurecli-interactive
kubectl create -f azure-secret.yaml
```

## <a name="mount-file-share-as-volume"></a>Montare la condivisione file come volume

È possibile montare la condivisione file di Azure nel pod tramite la configurazione del volume nelle sue specifiche. Creare un nuovo file denominato `azure-files-pod.yaml` con il contenuto seguente. Aggiornare `aksshare` con il nome assegnato alla condivisione file di Azure.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: kubernetes/pause
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Usare kubectl per creare un pod.

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

A questo punto esiste un contenitore in esecuzione con la condivisione file di Azure montata nella directory `/mnt/azure`. È possibile vedere il montaggio del volume quando si controlla il pod tramite `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui volumi Kubernetes usando File di Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes per file di Azure][kubernetes-files]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
