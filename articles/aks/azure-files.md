---
title: Usare File di Azure con il servizio contenitore di Azure | Microsoft Docs
description: Usare Dischi di Azure con il servizio contenitore di Azure
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 11457e6556e6400d8f58f71c71ab1e790bcef8f1
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2017
---
# <a name="using-azure-files-with-kubernetes"></a>Utilizzo di File di Azure con Kubernetes

Le applicazioni basate su contenitore hanno spesso necessità di accedere e salvare in modo permanente i dati in un volume di dati esterno. È possibile usare File di Azure come archivio dati esterno. Questo articolo descrive in dettaglio l'utilizzo di File di Azure come volume Kubernetes nel servizio contenitore di Azure.

Per altre informazioni sui volumi Kubernetes, vedere [Kubernetes volumes][kubernetes-volumes] (Volumi Kubernetes).

## <a name="creating-a-file-share"></a>Creazione di una condivisione file

Con il servizio contenitore di Azure è possibile usare una condivisione file di Azure esistente. Se è necessario crearne una, usare il set di comandi seguente.

Creare un gruppo di risorse per la condivisione file di Azure con il comando [az group create][az-group-create]. Il gruppo di risorse dell'account di archiviazione e il cluster Kubernetes devono trovarsi nella stessa area.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Usare il comando [az storage account create][az-storage-create] per creare un account di Archiviazione di Microsoft Azure. Il nome dell'account di archiviazione deve essere univoco. Aggiornare il valore dell'argomento `--name` con un valore univoco.

```azurecli-interactive
az storage account create --name mystorageaccount --resource-group myResourceGroup --sku Standard_LRS
```

Usare il comando [az storage account keys list][az-storage-key-list] per restituire la chiave di archiviazione. Aggiornare il valore dell'argomento `--account-name` con il nome dell'account di archiviazione univoco.

Prendere nota di uno dei valori chiave, che verrà usato nei passaggi successivi.

```azurecli-interactive
az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup --output table
```

Usare il comando [az storage share create][az-storage-share-create] per creare la condivisione file di Azure. Aggiornare il valore `--account-key` con il valore ottenuto nel passaggio precedente.

```azurecli-interactive
az storage share create --name myfileshare --account-name mystorageaccount --account-key <key>
```

## <a name="create-kubernetes-secret"></a>Creare un segreto Kubernetes

Kubernetes necessita di credenziali per accedere alla condivisione file. Anziché archiviare nome e chiave dell'account di Archiviazione di Azure con ogni pod, i valori vengono archiviati una sola volta in un [segreto Kubernetes][kubernetes-secret] e referenziati da ogni volume di File di Azure. 

I valori in un manifesto del secreto Kubernetes devono avere la codifica Base64. Usare i comandi seguenti per restituire i valori codificati.

Codificare prima il nome dell'account di archiviazione. Sostituire `storage-account` con il nome del proprio account di archiviazione di Azure.

```azurecli-interactive
echo -n <storage-account> | base64
```

È ora necessaria la chiave di accesso dell'account di archiviazione. Eseguire il comando seguente per restituire la chiave codificata. Sostituire `storage-key` con la chiave ottenuta in un passaggio precedente

```azurecli-interactive
echo -n <storage-key> | base64
```

Creare un file denominato `azure-secret.yml` e copiarlo nel codice YAML seguente. Aggiornare i valori `azurestorageaccountname` e `azurestorageaccountkey` con i valori codificati con il sistema Base64 recuperati nel passaggio precedente.

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

Usare il comando [kubectl apply][kubectl-apply] per creare il segreto.

```azurecli-interactive
kubectl apply -f azure-secret.yml
```

## <a name="mount-file-share-as-volume"></a>Montare la condivisione file come volume

È possibile montare la condivisione file di Azure nel pod tramite la configurazione del volume nelle sue specifiche. Creare un nuovo file denominato `azure-files-pod.yml` con il contenuto seguente. Aggiornare `share-name` con il nome assegnato alla condivisione file di Azure.

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
      shareName: <share-name>
      readOnly: false
```

Usare kubectl per creare un pod.

```azurecli-interactive
kubectl apply -f azure-files-pod.yml
```

A questo punto esiste un contenitore in esecuzione con la condivisione file di Azure montata nella directory `/mnt/azure`. È possibile vedere il montaggio del volume quando si controlla il pod tramite `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui volumi Kubernetes usando File di Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes per File di Azure](https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md)

<!-- LINKS -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[kubectl-apply]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#apply
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[az-group-create]: /cli/azure/group#az_group_create