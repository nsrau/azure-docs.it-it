---
title: Creare un account di archiviazione BLOB in blocchi-archiviazione di Azure | Microsoft Docs
description: Mostra come creare un account Azure BlockBlobStorage con le caratteristiche di prestazioni Premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 98a9295363461864d3abbb11bbc22b8bd8d6fdfa
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933190"
---
# <a name="create-a-blockblobstorage-account"></a>Creare un account BlockBlobStorage

Il tipo di account BlockBlobStorage consente di creare BLOB in blocchi con caratteristiche di prestazioni Premium. Questo tipo di account di archiviazione è ottimizzato per carichi di lavoro con percentuali di transazioni elevate o che richiedono tempi di accesso molto rapidi. Questo articolo illustra come creare un account BlockBlobStorage usando il portale di Azure, l'interfaccia della riga di comando di Azure o Azure PowerShell.

Per altre informazioni sugli account BlockBlobStorage, vedere [Panoramica dell'account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

# <a name="portaltabazure-portal"></a>[di Microsoft Azure](#tab/azure-portal)

None.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Questo articolo sulle procedure richiede il modulo Azure PowerShell AZ versione 1.2.0 o versioni successive. Eseguire `Get-Module -ListAvailable Az` per trovare la versione corrente. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile accedere ad Azure ed eseguire i comandi dell'interfaccia della riga di comando di Azure in uno dei due modi seguenti:

- È possibile eseguire i comandi dell'interfaccia della riga di comando dall'interno del portale di Azure, Azure Cloud Shell.
- È possibile installare l'interfaccia della riga di comando ed eseguire i comandi CLI localmente.

### <a name="use-azure-cloud-shell"></a>Usare Azure Cloud Shell

Azure Cloud Shell è una shell Bash gratuita che è possibile eseguire direttamente nel portale di Azure. L'interfaccia della riga di comando di Azure è preinstallata e configurata per l'uso con l'account. Fare clic sul pulsante **cloud Shell** nel menu nella sezione in alto a destra del portale di Azure:

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Il pulsante avvia una shell interattiva che è possibile usare per eseguire la procedura descritta in questo articolo:

[![Screenshot che mostra la finestra di Cloud Shell nel portale](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installare l'interfaccia della riga di comando in locale

È anche possibile installare e usare l'interfaccia della riga di comando di Azure in locale. Questo articolo illustra le procedure necessarie per eseguire l'interfaccia della riga di comando di Azure versione 2.0.46 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Accedere a Azure

# <a name="portaltabazure-portal"></a>[di Microsoft Azure](#tab/azure-portal)

Accedere al [portale di Azure](https://portal.azure.com).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate per eseguire l'autenticazione.

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

Per avviare Azure Cloud Shell, accedere al [portale di Azure](https://portal.azure.com).

Per accedere all'installazione locale dell'interfaccia della riga di comando, eseguire il comando [AZ login](/cli/azure/reference-index#az-login) :

```cli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Creare un account BlockBlobStorage

## <a name="portaltabazure-portal"></a>[di Microsoft Azure](#tab/azure-portal)
Per creare un account BlockBlobStorage nel portale di Azure, seguire questa procedura:

1. Nella portale di Azure selezionare tutti i **servizi** > la categoria **archiviazione** > **account di archiviazione**.

1. In **account di archiviazione**selezionare **Aggiungi**.

1. Nel campo **sottoscrizione** selezionare la sottoscrizione in cui creare l'account di archiviazione.

1. Nel campo **gruppo di risorse** selezionare un gruppo di risorse esistente oppure selezionare **Crea nuovo**e immettere un nome per il nuovo gruppo di risorse.

1. Nel campo **nome account di archiviazione** immettere un nome per l'account. Tenere presenti le linee guida seguenti:

   - Il nome deve essere univoco in Azure.
   - Il nome deve avere una lunghezza compresa tra 3 e 24 caratteri.
   - Il nome può includere solo numeri e lettere minuscole.

1. Nel campo **percorso** selezionare un percorso per l'account di archiviazione oppure usare il percorso predefinito.

1. Per le altre impostazioni, configurare quanto segue:

   |Campo     |Value  |
   |---------|---------|
   |**Prestazioni**    |  Selezionare **Premium**.   |
   |**Tipo di account**    | Selezionare **BlockBlobStorage**.      |
   |**Replica**    |  Lasciare l'impostazione predefinita di **archiviazione con ridondanza locale (con ridondanza locale)** .      |

   ![Mostra l'interfaccia utente del portale per creare un account di archiviazione BLOB in blocchi](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Selezionare **Verifica + crea** per verificare le impostazioni dell'account di archiviazione.

1. Selezionare **Create** (Crea).

## <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Aprire una sessione di Windows PowerShell con privilegi elevati (Esegui come amministratore).

1. Eseguire il comando seguente per verificare che sia installata la versione più recente del modulo `Az` PowerShell.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Aprire una nuova console di PowerShell e accedere con l'account Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Se necessario, creare un nuovo gruppo di risorse. Sostituire i valori in Quotations ed eseguire il comando seguente.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Creare l'account BlockBlobStorage. Sostituire i valori in Quotations ed eseguire il comando seguente.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

Per creare un account BLOB in blocchi usando l'interfaccia della riga di comando di Azure, è necessario installare l'interfaccia della riga di comando di Azure v. 2.0.46 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

1. Accedere alla sottoscrizione di Azure.

   ```azurecli
   az login
   ```

1. Se necessario, creare un nuovo gruppo di risorse. Sostituire i valori tra parentesi quadre (incluse le parentesi quadre) ed eseguire il comando seguente.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Creare l'account BlockBlobStorage. Sostituire i valori tra parentesi quadre (incluse le parentesi quadre) ed eseguire il comando seguente.

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
