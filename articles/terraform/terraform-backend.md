---
title: 'Esercitazione: Archiviare lo stato di Terraform in Archiviazione di Azure'
description: Introduzione all'archiviazione dello stato Terraform in Archiviazione di Azure.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 374936c39221d79d59fc8a54dc2bc4a49800240d
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078574"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Esercitazione: Archiviare lo stato Terraform in Archiviazione di Azure

Lo stato Terraform viene usato per risolvere le differenze tra le risorse distribuite con configurazioni Terraform. Tramite lo stato, Terraform riconosce quali risorse di Azure aggiungere, aggiornare o eliminare. Per impostazione predefinita, lo stato di Terraform è archiviato in locale quando si esegue il comando `terraform apply`. Questa configurazione non è ideale per i motivi seguenti:

- Lo stato locale non funziona correttamente in un ambiente di team o collaborazione.
- Lo stato di Terraform può includere informazioni riservate.
- L'archiviazione dello stato in locale aumenta le probabilità di eliminazioni accidentali.

Terraform supporta la persistenza dello stato nell'archiviazione remota. Uno di questi back-end supportati è Archiviazione di Azure. Questo documento illustra come configurare e usare Archiviazione di Azure a questo scopo.

## <a name="configure-storage-account"></a>Configurare l'account di archiviazione

Prima di usare Archiviazione di Azure come back-end, è necessario creare un account di archiviazione. L'account di archiviazione può essere creato con il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o Terraform stesso. Usare l'esempio seguente per configurare l'account di archiviazione con l'interfaccia della riga di comando di Azure.

```azurecli
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

## <a name="configure-state-back-end"></a>Configurare il back-end dello stato

Il back-end dello stato di Terraform viene configurato quando si esegue il comando `terraform init`. Per configurare il back-end dello stato, sono necessari i dati seguenti:

- **storage_account_name**: il nome dell'account di archiviazione di Azure.
- **container_name**: il nome del contenitore BLOB.
- **chiave**: il nome del file di archivio dello stato da creare.
- **access_key**: la chiave di accesso all'archiviazione.

Ognuno di questi valori può essere specificato nel file di configurazione di Terraform o nella riga di comando. È consigliabile usare una variabile di ambiente per il valore `access_key`. L'uso di una variabile di ambiente impedisce che la chiave venga scritta su disco.

Creare una variabile di ambiente denominata `ARM_ACCESS_KEY` con il valore della chiave di accesso di Archiviazione di Azure.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Per proteggere ulteriormente la chiave di accesso dell'account di Archiviazione di Azure, salvarla in Azure Key Vault. La variabile di ambiente può quindi essere impostata usando un comando simile al seguente. Per altre informazioni su Azure Key Vault, vedere la [documentazione di Azure Key Vault](../key-vault/quick-create-cli.md).

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Per configurare Terraform per l'uso del back-end, è necessario eseguire i passaggi seguenti:
- Includere un blocco di configurazione di `backend` con un tipo di `azurerm`.
- Aggiungere un valore `storage_account_name` al blocco di configurazione.
- Aggiungere un valore `container_name` al blocco di configurazione.
- Aggiungere un valore `key` al blocco di configurazione.

L'esempio seguente configura un back-end di Terraform e crea un gruppo di risorse di Azure.

```hcl
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

Per inizializzare la configurazione, procedere come segue:

1. Eseguire il comando `terraform init`.
1. Eseguire il comando `terraform apply`.

A questo punto il file dello stato si trova nel BLOB di Archiviazione di Azure.

## <a name="state-locking"></a>Blocco dello stato

I BLOB di Archiviazione di Azure vengono bloccati automaticamente prima di qualsiasi operazione di scrittura dello stato. Questo modello impedisce l'esecuzione simultanea di più operazioni sullo stato, che possono causare danni. 

Per altre informazioni, vedere la sezione relativa al [blocco dello stato](https://www.terraform.io/docs/state/locking.html) nella documentazione di Terraform.

Il blocco può essere visto quando si esamina il BLOB tramite il portale di Azure o altri strumenti di gestione di Azure.

![BLOB di Azure con blocco](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Crittografia di dati inattivi

I dati archiviati in un BLOB di Azure vengono crittografati prima di diventare persistenti. Se necessario, Terraform recupera lo stato dal back-end e lo archivia nella memoria locale. Con questo modello, lo stato non viene mai scritto nel disco locale.

Per altre informazioni sulla crittografia di Archiviazione di Azure, vedere [Crittografia del servizio Archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Vedere altre informazioni sull'uso di Terraform in Azure](/azure/terraform)