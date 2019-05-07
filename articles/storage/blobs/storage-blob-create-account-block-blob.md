---
title: Creare un account di archiviazione blob di blocco - archiviazione di Azure | Microsoft Docs
description: Viene illustrato come creare un account di archiviazione blob di blocchi di Azure con caratteristiche di prestazioni premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9d8fb8f5f470dc47088efb30b7f823a0b8c624c8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141009"
---
# <a name="create-a-block-blob-storage-account"></a>Creare un account di archiviazione BLOB in blocchi

Il tipo di account di archiviazione blob blocco consente di creare i BLOB in blocchi con caratteristiche di prestazioni premium. Questo tipo di account di archiviazione è ottimizzato per carichi di lavoro con frequenze di transazioni elevate o che richiedono tempi di accesso molto veloce. Questo articolo illustra come creare un account di archiviazione blob di blocco tramite il portale di Azure, il comando di Azure o Azure PowerShell.

Per altre informazioni sugli account di archiviazione blob di blocco, vedere [panoramica dell'account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="create-account-in-the-azure-portal"></a>Creare account nel portale di Azure

Per creare un account di archiviazione blob di blocco nel portale di Azure, seguire questa procedura:

1. Nel portale di Azure, selezionare **tutti i servizi** > il **archiviazione** categoria > **account di archiviazione**.

1. Sotto **gli account di archiviazione**, selezionare **Add**.

1. Nel **sottoscrizione** campo, selezionare la sottoscrizione in cui creare l'account di archiviazione.

1. Nel **gruppo di risorse** campo, selezionare un gruppo di risorse esistente o selezionare **Crea nuovo**e immettere un nome per il nuovo gruppo di risorse.

1. Nel **nome account di archiviazione** immettere un nome per l'account. Tenere presente le linee guida seguenti:

   - Il nome deve essere univoco in Azure.
   - Il nome deve essere compreso tra 3 e 24 caratteri.
   - Il nome può includere solo lettere minuscole e numeri.

1. Nel **posizione** campo, selezionare un percorso per l'account di archiviazione o usare il percorso predefinito.

1. Per il resto delle impostazioni, configurare quanto segue:

   |Campo     |Value  |
   |---------|---------|
   |**Prestazioni**    |  Selezionare **Premium**.   |
   |**Tipo di account**    | Selezionare **BlockBlobStorage**.      |
   |**Replica**    |  Lasciare l'impostazione predefinita **archiviazione con ridondanza locale (LRS)**.      |

   ![Mostra interfaccia utente per creare un account di archiviazione blob di blocco del portale](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Selezionare **esaminare e creare** per rivedere le impostazioni dell'account di archiviazione.

1. Selezionare **Create**.

## <a name="create-account-using-azure-powershell"></a>Creare account con Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Aprire una sessione di Windows PowerShell con privilegi elevata (Esegui come amministratore).

1. Eseguire il comando seguente per assicurarsi che la versione più recente del `Az` modulo PowerShell è installato.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Aprire una nuova console di PowerShell e accedere con l'account Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Se necessario, creare un nuovo gruppo di risorse. Sostituire i valori tra virgolette ed eseguire il comando seguente.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Creare l'account di archiviazione blob di blocco. Sostituire i valori tra virgolette ed eseguire il comando seguente.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="create-account-using-azure-cli"></a>Creare account tramite la CLI di Azure

Per creare un account blob di blocco tramite la CLI di Azure, è necessario installare innanzitutto v CLI di Azure. 2.0.46 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

1. Accedere alla sottoscrizione di Azure.

   ```azurecli
   az login
   ```

1. Se necessario, creare un nuovo gruppo di risorse. Sostituire i valori tra parentesi quadre (incluse le parentesi) ed eseguire il comando seguente.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Creare l'account di archiviazione blob di blocco. Sostituire i valori tra parentesi quadre (incluse le parentesi) ed eseguire il comando seguente.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
