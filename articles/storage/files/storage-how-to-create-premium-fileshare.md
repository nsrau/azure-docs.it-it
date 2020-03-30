---
title: Creare una condivisione file di Azure premiumCreate a premium Azure file share
description: In questo articolo viene illustrato come creare una condivisione file premium di Azure.In this article, you learn how to create a premium Azure file share.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7680a28b165dc252159cf95311439508d3c867e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529108"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Come creare una condivisione file premium di AzureHow to create an premium Azure file share
Le condivisioni file Premium sono disponibili su supporti di archiviazione su disco a stato solido (SSD) e sono utili per carichi di lavoro che richiedono un uso intensivo di I/O, inclusi i database di hosting e l'elaborazione ad alte prestazioni (HPC). Le condivisioni file Premium sono ospitate in un tipo di account di archiviazione con uno scopo speciale, denominato account FileStorage.Premium file shares are hosted in a special purpose storage account kind, called a FileStorage account. Le condivisioni file Premium sono progettate per applicazioni ad alte prestazioni e su scala aziendale, fornendo una bassa latenza costante, operazioni di I/O al secondo elevate e condivisioni con velocità effettiva elevata.

Questo articolo illustra come creare questo nuovo tipo di account usando il portale di Azure, Azure PowerShell e l'interfaccia della riga di comando di Azure.This article shows you how to create this new account type using [Azure portal,](https://portal.azure.com/)Azure PowerShell, and Azure CLI.

## <a name="prerequisites"></a>Prerequisiti

Per accedere alle risorse di Azure, incluse le condivisioni file premium di Azure, è necessaria una sottoscrizione di Azure.To access Azure resources including premium Azure file shares, you'll need an Azure subscription. Se non si ha già una sottoscrizione, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Creare una condivisione file premium tramite il portale di AzureCreate a premium file share using the Azure portal

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale](https://portal.azure.com/)di Azure .

### <a name="create-a-filestorage-storage-account"></a>Creare un account di archiviazione di archiviazione fileCreate a filestorage storage account

A questo punto è possibile creare l'account di archiviazione.

Ogni account di archiviazione deve appartenere a un gruppo di risorse di Azure. Un gruppo di risorse è un contenitore logico per raggruppare i servizi di Azure. Quando si crea un account di archiviazione, è possibile creare un nuovo gruppo di risorse o usarne uno esistente. Questo articolo illustra come creare un nuovo gruppo di risorse.

1. Nel portale di Azure selezionare **Account di archiviazione** nel menu a sinistra.

    ![Pagina principale del portale di Azure selezionare l'account di archiviazioneAzure portal main page select storage account](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Nella finestra **Account di archiviazione** visualizzata scegliere **Aggiungi**.
1. Selezionare la sottoscrizione in cui creare l'account di archiviazione.
1. Nel campo **Gruppo di risorse** selezionare **Crea nuovo**. Immettere un nome per il nuovo gruppo di risorse, come illustrato nell'immagine seguente.

1. Immettere quindi un nome per l'account di archiviazione. Il nome scelto deve essere univoco in Azure. Deve avere inoltre una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole.
1. Selezionare la località per l'account di archiviazione o usare la località predefinita.
1. Per **Prestazioni** selezionare **Premium**.
1. Selezionare **Tipo di account** e scegliere **FileStorage**.
1. Lasciare **la replica** impostata sul valore predefinito **LRS (Locally-Redundant Storage)**.

    ![Come creare un account di archiviazione per una condivisione file premiumHow to create a storage account for a premium file share](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Selezionare **Rivedi e crea** per esaminare le impostazioni dell'account di archiviazione e creare l'account.
1. Selezionare **Crea**.

Dopo aver creato la risorsa dell'account di archiviazione, passare a essa.

### <a name="create-a-premium-file-share"></a>Creare una condivisione file premium

1. Nel menu a sinistra dell'account di archiviazione scorrere fino alla sezione **Servizio file** e quindi selezionare **File**.
1. Selezionare **Condivisione file** per creare una condivisione file premium.
1. Immettere un nome e una quota desiderata per la condivisione file, quindi selezionare **Crea**.

> [!NOTE]
> Le dimensioni delle condivisioni di cui è stato eseguito il provisioning sono specificate dalla quota di condivisione, le condivisioni file vengono fatturate in base alle dimensioni di cui è stato eseguito il provisioning, consulta la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/files/) per ulteriori dettagli.

   ![Creare una condivisione file premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Pulire le risorse

Se si desidera pulire le risorse create in questo articolo, è sufficiente eliminare il gruppo di risorse. L'eliminazione del gruppo di risorse comporta anche l'eliminazione dell'account di archiviazione associato e di tutte le altre risorse associate al gruppo di risorse.

## <a name="create-a-premium-file-share-using-powershell"></a>Creare una condivisione file premium tramite PowerShellCreate a premium file share using PowerShell

### <a name="create-an-account-using-powershell"></a>Creare un account con PowerShell

Installare innanzitutto la versione più recente del modulo [PowerShellGet](/powershell/scripting/gallery/installing-psget).

Aggiornare quindi il modulo di PowerShell, accedere alla sottoscrizione di Azure, creare un gruppo di risorse e quindi creare un account di archiviazione.

### <a name="upgrade-your-powershell-module"></a>Aggiornare il modulo di PowerShellUpgrade your PowerShell module

Per interagire con una condivisione file premium da PowerShell, è necessario installare un modulo Az.Storage versione 1.4.0 o il modulo Az.Storage più recente.

Iniziare aprendo una sessione di PowerShell con autorizzazioni elevate.

Installare il modulo Az.Storage:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Accedere alla sottoscrizione di Azure

Usare il comando `Connect-AzAccount` e seguire le istruzioni visualizzate per eseguire l'autenticazione.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per creare un nuovo gruppo di risorse con PowerShell, usare il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>Creare un account di archiviazione FileStorageCreate a FileStorage storage account

Per creare un account di archiviazione FileStorage da PowerShell, usare il comando [New-AzStorageAccount:To](/powershell/module/az.storage/New-azStorageAccount) create a FileStorage storage account from PowerShell, use the New-AzStorageAccount command:

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Creare una condivisione file premium

Ora che si dispone di un account FileStorage, è possibile creare una condivisione file premium. Utilizzare il cmdlet [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) per crearne uno.

> [!NOTE]
> Le dimensioni delle condivisioni di cui è stato eseguito il provisioning sono specificate dalla quota di condivisione, le condivisioni file vengono fatturate in base alle dimensioni di cui è stato eseguito il provisioning, consulta la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/files/) per ulteriori dettagli.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere il gruppo di risorse e tutte le risorse correlate, incluso il nuovo account di archiviazione, usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Creare una condivisione file premium usando l'interfaccia della riga di comando di AzureCreate a premium file share using Azure CLI

Per avviare Azure Cloud Shell, accedere al [portale di Azure](https://portal.azure.com).

Se si desidera accedere all'installazione locale dell'interfaccia della riga di comando, assicurarsi innanzitutto di disporre della versione più recente, quindi eseguire il comando di accesso:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per creare un nuovo gruppo di risorse con l'interfaccia della riga di comando di Azure, usare il comando [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Creare un account di archiviazione FileStorageCreate a FileStorage storage account

Per creare un account di archiviazione FileStorage dall'interfaccia della riga di comando di Azure, usare il comando [az storage account create.](/cli/azure/storage/account)

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Ottenere la chiave dell'account di archiviazione

Le chiavi dell'account di archiviazione controllano l'accesso alle risorse in un account di archiviazione, in questo articolo viene usata la chiave per creare una condivisione file premium. Vengono create automaticamente quando si crea un account di archiviazione. È possibile ottenere le chiavi per l'account di archiviazione con il comando [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Creare una condivisione file premium

Ora che si dispone di un account di archiviazione file, è possibile creare una condivisione file premium. Usare il comando [az storage share create](/cli/azure/storage/share) per crearne uno.

> [!NOTE]
> Le dimensioni delle condivisioni di cui è stato eseguito il provisioning sono specificate dalla quota di condivisione, le condivisioni file vengono fatturate in base alle dimensioni di cui è stato eseguito il provisioning, consulta la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/files/) per ulteriori dettagli.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere il gruppo di risorse e tutte le risorse correlate, incluso il nuovo account di archiviazione, usare il comando [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una condivisione file premium. Per informazioni sulle prestazioni offerte da questo account, passare alla sezione relativa al livello di prestazioni della guida alla pianificazione.

> [!div class="nextstepaction"]
> [Livelli di condivisione file](storage-files-planning.md#storage-tiers)