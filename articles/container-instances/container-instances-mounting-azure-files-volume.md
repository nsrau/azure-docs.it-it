---
title: Esecuzione del montaggio di un volume di File di Azure in Istanze di contenitore di Azure
description: Informazioni su come montare un volume di File di Azure per rendere persistente lo stato con Istanze di contenitore di Azure
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 06a6e91725e751fbea97d9a3b60f48fa50121fc4
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="mount-an-azure-file-share-with-azure-container-instances"></a>Esecuzione del montaggio di una condivisione file di Azure con Istanze di contenitore di Azure

Per impostazione predefinita, Istanze di contenitore di Azure è senza stato. Se il contenitore si blocca o si arresta, lo stato viene perso. Per rendere persistente lo stato oltre la durata del contenitore, è necessario montare un volume da un archivio esterno. Questo articolo illustra come montare una condivisione file di Azure per l'uso con Istanze di contenitore di Azure.

> [!NOTE]
> L'installazione di una condivisione di file di Azure è attualmente limitato ai contenitori di Linux. Mentre ci stiamo lavorando per portare tutte le funzionalità ai contenitori di Windows, è possibile trovare le differenze di piattaforma corrente in [quote e la disponibilità di area per le istanze di Azure contenitore](container-instances-quotas.md).

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure

Prima di usare una condivisione file di Azure con Istanze di contenitore di Azure è necessario creare la condivisione. Eseguire questo script per creare un account di archiviazione per ospitare la condivisione file e la condivisione in sé. Il nome dell'account di archiviazione deve essere globalmente univoco, quindi lo script aggiunge un valore casuale alla stringa di base.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Export the connection string as an environment variable. The following 'az storage share create' command
# references this environment variable when creating the Azure file share.
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $ACI_PERS_RESOURCE_GROUP --name $ACI_PERS_STORAGE_ACCOUNT_NAME --output tsv`

# Create the file share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="get-storage-credentials"></a>Ottenere le credenziali di archiviazione

Per montare una condivisione file di Azure come volume in Istanze di contenitore di Azure sono necessari tre valori: il nome dell'account di archiviazione, il nome della condivisione e la chiave di accesso alle risorse di archiviazione.

Se si usa lo script precedente, il nome dell'account di archiviazione viene creato con un valore casuale alla fine. Per eseguire una query sulla stringa finale (inclusa la parte casuale), usare i comandi seguenti:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" --output tsv)
echo $STORAGE_ACCOUNT
```

Il nome della condivisione è già noto (definito come *acishare* nello script precedente), quindi resta da trovare solo la chiave dell'account di archiviazione, che può essere recuperata tramite il comando seguente:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>Distribuire contenitore e montare volumi

Per montare un volume in un contenitore di una condivisione di file di Azure, specificare la condivisione e il volume punto di montaggio quando si crea il contenitore con [contenitore az creare][az-container-create]. Se è stata eseguita la procedura precedente, è possibile montare la condivisione creata in precedenza tramite il comando seguente per creare un contenitore:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image seanmckenna/aci-hellofiles \
    --ip-address Public \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

## <a name="manage-files-in-mounted-volume"></a>Gestire i file nel volume montato

Dopo il contenitore viene avviata, è possibile usare l'app web semplici distribuito tramite il [aci/seanmckenna-hellofiles] [ aci-hellofiles] immagine per gestire i file nella condivisione di file di Azure in corrispondenza del percorso di montaggio specificato. Ottenere l'indirizzo IP per l'app web con il [Mostra contenitore az] [ az-container-show] comando:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --output table
```

È possibile utilizzare il [portale di Azure] [ portal] o uno strumento, ad esempio il [Microsoft Azure Storage Explorer] [ storage-explorer] per recuperare ed esaminare il file scritti la condivisione di file.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla [relazione tra Istanze di contenitore di Azure e gli agenti di orchestrazione di contenitori](container-instances-orchestrator-relationship.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/r/seanmckenna/aci-hellofiles/
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show