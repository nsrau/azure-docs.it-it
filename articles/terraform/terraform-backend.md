---
title: Usare Archiviazione di Azure come back-end Terraform
description: Introduzione all'archiviazione dello stato Terraform in Archiviazione di Azure.
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/13/2018
ms.author: tarcher
ms.openlocfilehash: a88ad25e335026d5172c7997f62629d5ada46f6e
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693303"
---
# <a name="store-terraform-state-in-azure-storage"></a>Archiviare lo stato Terraform in Archiviazione di Azure

Lo stato Terraform viene usato per risolvere le differenze tra le risorse distribuite con configurazioni Terraform. Usando lo stato, Terraform sa quali risorse di Azure devono essere aggiunte, aggiornate o eliminate. Per impostazione predefinita, lo stato Terraform è archiviato in locale quando si esegue il comando *apply di Terraform*. Questa configurazione non è ideale per diversi motivi:

- Lo stato locale non funziona correttamente in un team o un ambiente di collaborazione
- Lo stato Terraform può includere informazioni riservate
- L'archiviazione dello stato in locale aumenta le probabilità di eliminazione accidentale

Terraform include il concetto di back-end di stato, ovvero l'archiviazione remota per lo stato Terraform. Quando si usa un back-end di stato, il file di stato viene archiviato in un archivio dati, ad esempio Archiviazione di Azure. Questo documento descrive come configurare e usare Archiviazione di Azure come back-end per lo stato Terraform.

## <a name="configure-storage-account"></a>Configurare l'account di archiviazione

Prima di usare Archiviazione di Azure come back-end, è necessario creare un account di archiviazione. L'account di archiviazione può essere creato con il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o Terraform stesso. Usare l'esempio seguente per configurare l'account di archiviazione con l'interfaccia della riga di comando di Azure.

```azurecli-interactive
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Annotare il nome dell'account di archiviazione, il nome del contenitore e la chiave di accesso alle risorse di archiviazione. Questi valori sono necessari quando si configura lo stato remoto.

## <a name="configure-state-backend"></a>Configurare il back-end di stato

Il back-end di stato Terraform è configurato quando si esegue il comando *init di Terraform*. Per configurare il back-end lo stato sono necessari i dati seguenti.

- storage_account_name: il nome dell'account di Archiviazione di Azure.
- container_name: il nome del contenitore BLOB.
- key: il nome del file di archiviazione dello stato da creare.
- access_key: la chiave di accesso alle risorse di archiviazione.

Ognuno di questi valori può essere specificato nel file di configurazione di Terraform o nella riga di comando, tuttavia, è consigliabile usare una variabile di ambiente per `access_key`. L'uso di una variabile di ambiente impedisce che la chiave venga scritta su disco.

Creare una variabile di ambiente denominata `ARM_ACCESS_KEY` con il valore della chiave di accesso di Archiviazione di Azure.

```console
export ARM_ACCESS_KEY=<storage access key>
```

Per proteggere ulteriormente la chiave di accesso dell'account di Archiviazione di Azure, salvarla in Azure Key Vault. La variabile di ambiente può quindi essere impostata usando un comando simile al seguente. Per altre informazioni su Azure Key Vault, vedere la [documentazione di Azure Key Vault][azure-key-vault].

```console
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Per configurare Terraform per l'uso del back-end, includere una configurazione *back-end* con un tipo *azurerm* all'interno della configurazione di Terraform. Aggiungere i valori *storage_account_name*, *container_name* e *key* al blocco di configurazione.

Nell'esempio seguente configura un back-end di Terraform e crea un gruppo di risorse di Azure. Sostituire i valori con i valori del proprio ambiente.

```json
terraform {
  backend "azurerm" {
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

A questo punto, inizializzare la configurazione con il comando *init di Terraform* ed eseguire la configurazione con il comando *apply di Terraform*. Al termine, è possibile trovare il file dello stato nel BLOB del servizio di archiviazione di Azure.

## <a name="state-locking"></a>Blocco dello stato

Quando si usa un BLOB del servizio di archiviazione di Azure per archiviare lo stato, il BLOB viene automaticamente bloccato prima di qualsiasi operazione che scrive lo stato. Questa configurazione impedisce l'esecuzione simultanea di più operazioni sullo stato, che possono causare danni. Per altre informazioni, vedere la sezione relativa al [blocco dello stato][terraform-state-lock] nella documentazione di Terraform.

Il blocco può essere visualizzato quando si esaminano i blob tramite il portale di Azure o altri strumenti di gestione di Azure.

![BLOB di Azure con blocco](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Crittografia di dati inattivi

Per impostazione predefinita, i dati archiviati in un BLOB di Azure vengono crittografati prima di diventare persistenti nell'infrastruttura di archiviazione. Quando Terraform richiede lo stato, questo viene recuperato dal back-end e archiviato in memoria nel sistema di sviluppo. In questa configurazione lo stato è protetto in Archiviazione di Azure e non viene scritto sul disco locale.

Per altre informazioni sulla crittografia di Archiviazione di Azure, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi][azure-storage-encryption].

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla configurazione del back-end Terraform sono disponibili nella [documentazione del back-end Terraform][terraform-backend].

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
