---
title: Creare un account di archiviazione BLOB in blocchi - Archiviazione di AzureCreate a block blob storage account - Azure Storage Documenti Microsoft
description: Viene illustrato come creare un account Azure BlockBlobStorage con caratteristiche di prestazioni premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6303644ada5c6f093611dba94daf8006f8cc5819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536905"
---
# <a name="create-a-blockblobstorage-account"></a>Creare un account BlockBlobStorageCreate a BlockBlobStorage account

Il tipo di account BlockBlobStorage consente di creare BLOB di blocchi con caratteristiche di prestazioni premium. Questo tipo di account di archiviazione è ottimizzato per carichi di lavoro con tassi di transazioni elevati o che richiedono tempi di accesso molto rapidi. Questo articolo illustra come creare un account BlockBlobStorage usando il portale di Azure, l'interfaccia della riga di comando di Azure o Azure PowerShell.This article shows how to create a BlockBlobStorage account by using the Azure portal, the Azure CLI, or Azure PowerShell.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Per altre informazioni sugli account BlockBlobStorage, vedere Panoramica degli account di archiviazione di Azure.For more information about BlockBlobStorage accounts, see [Azure storage account overview.](https://docs.microsoft.com/azure/storage/common/storage-account-overview)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

# <a name="portal"></a>[Portale](#tab/azure-portal)

No.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Questo articolo delle procedure richiede il modulo di Azure PowerShell Az versione 1.2.0 o successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione corrente. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

È possibile accedere ad Azure ed eseguire i comandi dell'interfaccia della riga di comando di Azure in uno dei due modi seguenti:

- È possibile eseguire comandi dell'interfaccia della riga di comando dal portale di Azure in Azure Cloud Shell.You can run CLI commands from within the Azure portal, in Azure Cloud Shell.
- È possibile installare l'interfaccia della riga di comando ed eseguire i comandi dell'interfaccia della riga di comando in locale.

### <a name="use-azure-cloud-shell"></a>Usare Azure Cloud Shell

Azure Cloud Shell è una shell Bash gratuita che è possibile eseguire direttamente nel portale di Azure. L'interfaccia della riga di comando di Azure è preinstallata e configurata per l'uso con l'account. Fare clic sul pulsante **Cloud Shell** nel menu nella sezione in alto a destra del portale di Azure:

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Il pulsante avvia una shell interattiva che è possibile utilizzare per eseguire la procedura descritta in questo articolo how-to:

[![Screenshot della finestra di Cloud Shell nel portale](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installare l'interfaccia della riga di comando in locale

È anche possibile installare e usare l'interfaccia della riga di comando di Azure in locale. Questo articolo delle procedure richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.46 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Accedere ad Azure

# <a name="portal"></a>[Portale](#tab/azure-portal)

Accedere al [portale](https://portal.azure.com)di Azure .

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate per eseguire l'autenticazione.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Per avviare Azure Cloud Shell, accedere al portale di [Azure.](https://portal.azure.com)

Per accedere all'installazione locale dell'interfaccia della riga di comando, eseguire il comando [az login:](/cli/azure/reference-index#az-login)

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Creare un account BlockBlobStorageCreate a BlockBlobStorage account

## <a name="portal"></a>[Portale](#tab/azure-portal)
Per creare un account BlockBlobStorage nel portale di Azure, eseguire la procedura seguente:To create a BlockBlobStorage account in the Azure portal, follow these steps:

1. Nel portale di Azure selezionare **Tutti i servizi** > la categoria **Archiviazione** > account **di archiviazione**.

1. In **Account di archiviazione**selezionare **Aggiungi**.

1. Nel campo **Sottoscrizione** selezionare la sottoscrizione in cui creare l'account di archiviazione.

1. Nel campo **Gruppo di** risorse selezionare un gruppo di risorse esistente o **selezionare Crea nuovo**e immettere un nome per il nuovo gruppo di risorse.

1. Nel campo **Nome account di archiviazione** immettere un nome per l'account. Tenere presenti le linee guida seguenti:Note the following guidelines:

   - Il nome deve essere univoco in Azure.
   - Il nome deve avere una lunghezza compresa tra tre e 24 caratteri.
   - Il nome può includere solo numeri e lettere minuscole.

1. Nel campo **Percorso** selezionare un percorso per l'account di archiviazione oppure usare il percorso predefinito.

1. Per il resto delle impostazioni, configurare quanto segue:

   |Campo     |valore  |
   |---------|---------|
   |**Prestazioni**    |  Selezionare **Premium**.   |
   |**Tipo di account**    | Selezionare **BlockBlobStorage**.      |
   |**Replica**    |  Lasciare l'impostazione predefinita **LRS (Locally-Redundant Storage).**      |

   ![Mostra l'interfaccia utente del portale per creare un account di archiviazione BLOB in blocchiShows portal UI to create a block blob storage account](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Selezionare **Revisione e creazione** per rivedere le impostazioni dell'account di archiviazione.

1. Selezionare **Crea**.

## <a name="azure-powershell"></a>[Powershell di AzureAzure Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Aprire una sessione di Windows PowerShell con privilegi elevati (Esegui come amministratore).

1. Eseguire il comando seguente per assicurarsi `Az` che sia installata la versione più recente del modulo PowerShell.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Aprire una nuova console di PowerShell e accedere con l'account Azure.Open a new PowerShell console and sign in with your Azure account.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Se necessario, creare un nuovo gruppo di risorse. Sostituire i valori tra virgolette ed eseguire il comando seguente.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Creare l'account BlockBlobStorage.Create the BlockBlobStorage account. Sostituire i valori tra virgolette ed eseguire il comando seguente.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Per creare un account BLOB di blocchi usando l'interfaccia della riga di comando di Azure, è innanzitutto necessario installare l'interfaccia della riga di comando di Azure.To create a block blob account by using the Azure CLI, you must first install Azure CLI v. 2.0.46 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

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

1. Creare l'account BlockBlobStorage.Create the BlockBlobStorage account. Sostituire i valori tra parentesi quadre (incluse le parentesi) ed eseguire il comando seguente.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

---

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
