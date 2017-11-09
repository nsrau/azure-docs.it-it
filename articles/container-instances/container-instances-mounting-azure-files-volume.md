---
title: Montare un volume di File di Azure in Istanze di contenitore di Azure
description: Informazioni su come montare un volume di File di Azure per rendere persistente lo stato con Istanze di contenitore di Azure
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 41c3a449b39d6ef77e1dd0cf10699f8debcad475
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2017
---
# <a name="mounting-an-azure-file-share-with-azure-container-instances"></a>Montare una condivisione file di Azure con Istanze di contenitore di Azure

Per impostazione predefinita, Istanze di contenitore di Azure è senza stato. Se il contenitore si blocca o si arresta, lo stato viene perso. Per rendere persistente lo stato oltre la durata del contenitore, è necessario montare un volume da un archivio esterno. Questo articolo illustra come montare una condivisione file di Azure per l'uso con Istanze di contenitore di Azure.

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure

Prima di usare una condivisione file di Azure con Istanze di contenitore di Azure è necessario creare la condivisione. Eseguire questo script per creare un account di archiviazione per ospitare la condivisione file e la condivisione in sé. Il nome dell'account di archiviazione deve essere globalmente univoco, quindi lo script aggiunge un valore casuale alla stringa di base.

```azurecli-interactive
# Change these four parameters
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>Acquisire i dettagli di accesso dell'account di archiviazione

Per montare una condivisione file di Azure come volume in Istanze di contenitore di Azure sono necessari tre valori: il nome dell'account di archiviazione, il nome della condivisione e la chiave di accesso alle risorse di archiviazione.

Se si usa lo script precedente, il nome dell'account di archiviazione viene creato con un valore casuale alla fine. Per eseguire una query sulla stringa finale (inclusa la parte casuale), usare i comandi seguenti:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

Il nome della condivisione è già noto (*acishare* nello script precedente), quindi resta da trovare solo la chiave dell'account di archiviazione, che può essere recuperata tramite il comando seguente:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="store-storage-account-access-details-with-azure-key-vault"></a>Archiviare i dettagli di accesso dell'account di archiviazione con Azure Key Vault

Le chiavi dell'account di archiviazione proteggono l'accesso ai dati, quindi è consigliabile archiviarle in Azure Key Vault.

Creare un insieme di credenziali delle chiavi con l'interfaccia della riga di comando di Azure:

```azurecli-interactive
KEYVAULT_NAME=aci-keyvault
az keyvault create -n $KEYVAULT_NAME --enabled-for-template-deployment -g $ACI_PERS_RESOURCE_GROUP
```

L'opzione `enabled-for-template-deployment` consente ad Azure Resource Manager di eseguire il pull dei segreti dall'insieme di credenziali delle chiavi al momento della distribuzione.

Archiviare la chiave dell'account di archiviazione come nuovo segreto nell'insieme di credenziali delle chiavi:

```azurecli-interactive
KEYVAULT_SECRET_NAME=azurefilesstoragekey
az keyvault secret set --vault-name $KEYVAULT_NAME --name $KEYVAULT_SECRET_NAME --value $STORAGE_KEY
```

## <a name="mount-the-volume"></a>Montare il volume

Il montaggio di una condivisione file di Azure come volume in un contenitore prevede due fasi. Si forniscono prima i dettagli della condivisione nell'ambito della definizione del gruppo di contenitori, quindi si specifica come montare il volume in uno o più contenitori del gruppo.

Per definire i volumi da rendere disponibili per il montaggio, aggiungere una matrice `volumes` alla definizione del gruppo di contenitori nel modello di Azure Resource Manager, quindi farvi riferimento nella definizione dei singoli contenitori.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "type": "string"
    },
    "storageaccountkey": {
      "type": "securestring"
    }
  },
  "resources":[{
    "name": "hellofiles",
    "type": "Microsoft.ContainerInstance/containerGroups",
    "apiVersion": "2017-08-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
      "containers": [{
        "name": "hellofiles",
        "properties": {
          "image": "seanmckenna/aci-hellofiles",
          "resources": {
            "requests": {
              "cpu": 1,
              "memoryInGb": 1.5
            }
          },
          "ports": [{
            "port": 80
          }],
          "volumeMounts": [{
            "name": "myvolume",
            "mountPath": "/aci/logs/"
          }]
        }
      }],
      "osType": "Linux",
      "ipAddress": {
        "type": "Public",
        "ports": [{
          "protocol": "tcp",
          "port": "80"
        }]
      },
      "volumes": [{
        "name": "myvolume",
        "azureFile": {
          "shareName": "acishare",
          "storageAccountName": "[parameters('storageaccountname')]",
          "storageAccountKey": "[parameters('storageaccountkey')]"
        }
      }]
    }
  }]
}
```

Il modello include il nome dell'account di archiviazione e la chiave come parametri che possono essere forniti in un file di parametri separati. Per popolare il file dei parametri sono necessari tre valori: nome dell'account di archiviazione, ID risorsa di Azure Key Vault e nome del segreto dell'insieme di credenziali delle chiavi usato per archiviare la chiave di archiviazione. Se è stata seguita la procedura precedente, è possibile ottenere i valori con questo script:

```azurecli-interactive
echo $STORAGE_ACCOUNT
echo $KEYVAULT_SECRET_NAME
az keyvault show --name $KEYVAULT_NAME --query [id] -o tsv
```

Inserire i valori nel file dei parametri:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "value": "<my_storage_account_name>"
    },
   "storageaccountkey": {
      "reference": {
        "keyVault": {
          "id": "<my_keyvault_id>"
        },
        "secretName": "<my_storage_account_key_secret_name>"
      }
    }
  }
}
```

## <a name="deploy-the-container-and-manage-files"></a>Distribuire il contenitore e gestire i file

Con il modello definito, è possibile creare il contenitore e montarne il volume usando l'interfaccia della riga di comando di Azure. Supponendo che il file del modello sia denominato *azuredeploy.json* e che il file dei parametri sia denominato *azuredeploy.parameters.json*, la riga di comando sarà:

```azurecli-interactive
az group deployment create --name hellofilesdeployment --template-file azuredeploy.json --parameters @azuredeploy.parameters.json --resource-group myResourceGroup
```

Dopo l'avvio del contenitore, è possibile usare la semplice app Web distribuita tramite l'immagine **seanmckenna/aci-hellofiles** per gestire i file nella condivisione file di Azure nel percorso di montaggio specificato. Ottenere l'indirizzo IP dell'app Web con il comando seguente:

```azurecli-interactive
az container show --resource-group myResourceGroup --name hellofiles -o table
```

È possibile usare uno strumento come [Microsoft Azure Storage Explorer](http://storageexplorer.com) per recuperare e ispezionare il file scritto nella condivisione file.

>[!NOTE]
> Per altre informazioni sull'uso dei modelli di Azure Resource Manager e dei file dei parametri e sulla distribuzione con l'interfaccia della riga di comando di Azure, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

## <a name="next-steps"></a>Passaggi successivi

- Distribuire il primo contenitore usando la [guida introduttiva](container-instances-quickstart.md) Istanze di contenitore di Azure
- Informazioni sulla [relazione tra Istanze di contenitore di Azure e gli agenti di orchestrazione di contenitori](container-instances-orchestrator-relationship.md)
