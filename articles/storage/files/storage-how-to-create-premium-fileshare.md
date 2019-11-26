---
title: Creare una condivisione file di Azure Premium
description: Questo articolo illustra come creare una condivisione file di Azure Premium.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4b3e51b5e8d0ee9b62a7e7bc39955396f327c7e7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209545"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Come creare una condivisione file di Azure Premium
Le condivisioni file Premium sono offerte da supporti di archiviazione SSD (Solid-State Disk) e sono utili per i carichi di lavoro con utilizzo intensivo di i/o, inclusi i database di hosting e HPC (High Performance Computing). Le condivisioni file Premium sono ospitate in un tipo di account di archiviazione per scopi specifici, denominato account filestorage. Le condivisioni file Premium sono progettate per applicazioni a prestazioni elevate e di livello aziendale, garantendo una bassa latenza, IOPS elevate e condivisioni a velocità effettiva elevata.

Questo articolo illustra come creare questo nuovo tipo di account usando [portale di Azure](https://portal.azure.com/), Azure PowerShell e l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>prerequisiti

Per accedere alle risorse di Azure, tra cui le condivisioni file di Azure Premium, è necessaria una sottoscrizione di Azure. Se non si ha già una sottoscrizione, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Creare una condivisione file Premium usando il portale di Azure

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com/).

### <a name="create-a-filestorage-storage-account"></a>Creare un account di archiviazione filestorage

A questo punto si è pronti per creare l'account di archiviazione.

Ogni account di archiviazione deve appartenere a un gruppo di risorse di Azure. Un gruppo di risorse è un contenitore logico per raggruppare i servizi di Azure. Quando si crea un account di archiviazione, è possibile creare un nuovo gruppo di risorse o usarne uno esistente. Questo articolo illustra come creare un nuovo gruppo di risorse.

1. Nella portale di Azure selezionare account di **archiviazione** nel menu a sinistra.

    ![portale di Azure pagina principale selezionare account di archiviazione](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Nella finestra **Account di archiviazione** visualizzata scegliere **Aggiungi**.
1. Selezionare la sottoscrizione in cui creare l'account di archiviazione.
1. Nel campo **Gruppo di risorse** selezionare **Crea nuovo**. Immettere un nome per il nuovo gruppo di risorse, come illustrato nell'immagine seguente.

1. Immettere quindi un nome per l'account di archiviazione. Il nome scelto deve essere univoco in Azure. Deve avere inoltre una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole.
1. Selezionare la località per l'account di archiviazione o usare la località predefinita.
1. Per **prestazioni** selezionare **Premium**.
1. Selezionare **tipo di account** e scegliere **filestorage**.
1. Lasciare la **replica** impostata sul valore predefinito di **archiviazione con ridondanza locale (con ridondanza locale)** .

    ![Come creare un account di archiviazione per una condivisione file Premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Selezionare **Rivedi e crea** per esaminare le impostazioni dell'account di archiviazione e creare l'account.
1. Selezionare **Create**.

Una volta creata la risorsa dell'account di archiviazione, passare a essa.

### <a name="create-a-premium-file-share"></a>Creare una condivisione file premium

1. Nel menu a sinistra per l'account di archiviazione scorrere fino alla sezione **servizio file** , quindi selezionare **file**.
1. Selezionare **condivisione file** per creare una condivisione file Premium.
1. Immettere un nome e una quota desiderata per la condivisione file, quindi selezionare **Crea**.

> [!NOTE]
> Le dimensioni della condivisione di cui è stato effettuato il provisioning sono specificate dalla quota di condivisione, le condivisioni file vengono fatturate in base alle dimensioni di cui è stato effettuato il provisioning [. per ulteriori](https://azure.microsoft.com/pricing/details/storage/files/) informazioni, vedere

   ![Creare una condivisione file premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse create in questo articolo, è possibile eliminare semplicemente il gruppo di risorse. L'eliminazione del gruppo di risorse Elimina anche l'account di archiviazione associato e tutte le altre risorse associate al gruppo di risorse.

## <a name="create-a-premium-file-share-using-powershell"></a>Creare una condivisione file Premium con PowerShell

### <a name="create-an-account-using-powershell"></a>Creare un account con PowerShell

Installare innanzitutto la versione più recente del modulo [PowerShellGet](/powershell/scripting/gallery/installing-psget).

Quindi, aggiornare il modulo di PowerShell, accedere alla sottoscrizione di Azure, creare un gruppo di risorse e quindi creare un account di archiviazione.

### <a name="upgrade-your-powershell-module"></a>Aggiornare il modulo di PowerShell

Per interagire con una condivisione file Premium da con PowerShell, è necessario installare un modulo AZ. Storage versione 1.4.0 o il modulo AZ. storage più recente.

Iniziare aprendo una sessione di PowerShell con autorizzazioni elevate.

Installare il modulo AZ. storage:

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

### <a name="create-a-filestorage-storage-account"></a>Creare un account di archiviazione filestorage

Per creare un account di archiviazione filestorage da PowerShell, usare il comando [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) :

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Creare una condivisione file premium

Ora che si dispone di un account filestorage, è possibile creare una condivisione file Premium. Usare il cmdlet [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) per crearne uno.

> [!NOTE]
> Le dimensioni della condivisione di cui è stato effettuato il provisioning sono specificate dalla quota di condivisione, le condivisioni file vengono fatturate in base alle dimensioni di cui è stato effettuato il provisioning [. per ulteriori](https://azure.microsoft.com/pricing/details/storage/files/) informazioni, vedere

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

## <a name="create-a-premium-file-share-using-azure-cli"></a>Creare una condivisione file Premium usando l'interfaccia della riga di comando di Azure

Per avviare Azure Cloud Shell, accedere al [portale di Azure](https://portal.azure.com).

Se si vuole accedere all'installazione locale dell'interfaccia della riga di comando, assicurarsi di avere la versione più recente, quindi eseguire il comando login:

```cli
az login
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per creare un nuovo gruppo di risorse con l'interfaccia della riga di comando di Azure, usare il comando [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Creare un account di archiviazione filestorage

Per creare un account di archiviazione filestorage dall'interfaccia della riga di comando di Azure, usare il comando [AZ storage account create](/cli/azure/storage/account) .

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Ottenere la chiave dell'account di archiviazione

Le chiavi dell'account di archiviazione controllano l'accesso alle risorse in un account di archiviazione. in questo articolo viene usata la chiave per creare una condivisione file Premium. Vengono create automaticamente quando si crea un account di archiviazione. È possibile ottenere le chiavi per l'account di archiviazione con il comando [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Creare una condivisione file premium

Ora che si dispone di un account filestorage, è possibile creare una condivisione file Premium. Usare il comando [AZ storage Share create](/cli/azure/storage/share) per crearne uno.

> [!NOTE]
> Le dimensioni della condivisione di cui è stato effettuato il provisioning sono specificate dalla quota di condivisione, le condivisioni file vengono fatturate in base alle dimensioni di cui è stato effettuato il provisioning [. per ulteriori](https://azure.microsoft.com/pricing/details/storage/files/) informazioni, vedere

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

In questo articolo è stata creata una condivisione file Premium. Per informazioni sulle prestazioni offerte da questo account, passare alla sezione livello di prestazioni della Guida alla pianificazione.

> [!div class="nextstepaction"]
> [Livelli di prestazioni della condivisione file](storage-files-planning.md#file-share-performance-tiers)