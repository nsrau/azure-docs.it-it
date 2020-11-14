---
title: Creare una condivisione file di Azure Premium
description: Informazioni su come creare una condivisione file Premium di Azure usando il portale di Azure, il modulo Azure PowerShell o l'interfaccia della riga di comando di Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/26/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 1ec8e4a945f8b8277d05c11bf3673d2e4ab15f9a
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94626793"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Come creare una condivisione file Premium di Azure

Le condivisioni file Premium sono offerte da supporti di archiviazione SSD (Solid-State Disk) e sono utili per i carichi di lavoro con utilizzo intensivo di i/o, inclusi i database di hosting e HPC (High Performance Computing). Le condivisioni file Premium sono ospitate in un tipo di account di archiviazione per scopi specifici, denominato account filestorage. Le condivisioni file Premium sono progettate per applicazioni a prestazioni elevate e di livello aziendale, garantendo una bassa latenza, IOPS elevate e condivisioni a velocità effettiva elevata.

Questo articolo illustra come creare questo nuovo tipo di account usando il [portale di Azure](https://portal.azure.com/), il modulo Azure PowerShell e l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Se si intende usare l'interfaccia della riga di comando di Azure, [installare l'ultima versione](/cli/azure/install-azure-cli?view=azure-cli-latest).
- Se si intende usare il modulo Azure PowerShell, [installare la versione più recente](/powershell/azure/install-az-ps?view=azps-4.6.0).

## <a name="create-a-filestorage-storage-account"></a>Creare un account di archiviazione filestorage

Ogni account di archiviazione deve appartenere a un gruppo di risorse di Azure. Un gruppo di risorse è un contenitore logico per raggruppare i servizi di Azure. Quando si crea un account di archiviazione, è possibile creare un nuovo gruppo di risorse o usarne uno esistente. Per le condivisioni file Premium è necessario un account filestorage.

# <a name="portal"></a>[Portale](#tab/azure-portal)

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com/).

A questo punto si è pronti per creare l'account di archiviazione.

1. Nella portale di Azure selezionare account di **archiviazione** nel menu a sinistra.

    ![portale di Azure pagina principale selezionare account di archiviazione](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Nella finestra **Account di archiviazione** visualizzata scegliere **Aggiungi**.
1. Selezionare la sottoscrizione in cui creare l'account di archiviazione.
1. Nel campo **Gruppo di risorse** selezionare **Crea nuovo**. Immettere un nome per il nuovo gruppo di risorse, come illustrato nell'immagine seguente.

1. Immettere quindi un nome per l'account di archiviazione. Il nome scelto deve essere univoco in Azure. Deve avere inoltre una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole.
1. Selezionare la località per l'account di archiviazione o usare la località predefinita.
1. Per **prestazioni** selezionare **Premium**.

    È necessario selezionare **Premium** per l' **archiviazione filestorage** come opzione disponibile nell'elenco a discesa **tipo di account** .

1. Selezionare **tipo di account** e scegliere **filestorage**.
1. Lasciare la **replica** impostata sul valore predefinito di **archiviazione con ridondanza locale (con ridondanza locale)**.

    ![Come creare un account di archiviazione per una condivisione file Premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Selezionare **Rivedi e crea** per esaminare le impostazioni dell'account di archiviazione e creare l'account.
1. Selezionare **Crea**.

Una volta creata la risorsa dell'account di archiviazione, passare a essa.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="sign-in-to-azure"></a>Accedere ad Azure

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

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per avviare Azure Cloud Shell, accedere al [portale di Azure](https://portal.azure.com).

Se si vuole accedere all'installazione locale dell'interfaccia della riga di comando, assicurarsi di avere la versione più recente, quindi accedere:

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
---

## <a name="create-a-premium-file-share"></a>Creare una condivisione file premium

Ora che è stato creato un account filestorage, è possibile creare una condivisione file Premium all'interno dell'account di archiviazione.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel menu a sinistra per l'account di archiviazione scorrere fino alla sezione **servizio file** , quindi selezionare **file**.
1. Selezionare **condivisione file** per creare una condivisione file Premium.
1. Immettere un nome e una quota desiderata per la condivisione file, quindi selezionare **Crea**.

> [!NOTE]
> Le dimensioni della condivisione di cui è stato effettuato il provisioning sono specificate dalla quota di condivisione, le condivisioni file vengono fatturate in base alle dimensioni con provisioning Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/files/).

   ![Creare una condivisione file premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per creare una condivisione file Premium con il modulo Azure PowerShell, usare il cmdlet [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) .

> [!NOTE]
> Le dimensioni della condivisione di cui è stato effettuato il provisioning sono specificate dalla quota di condivisione, le condivisioni file vengono fatturate in base alle dimensioni con provisioning Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/files/).

```powershell
New-AzStorageShare `
   -Name myshare `
   -EnabledProtocol SMB `
   -Context $storageAcct.Context
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per creare una condivisione file Premium con l'interfaccia della riga di comando di Azure, usare il comando [AZ storage Share create](/cli/azure/storage/share) .

> [!NOTE]
> Le dimensioni della condivisione di cui è stato effettuato il provisioning sono specificate dalla quota di condivisione, le condivisioni file vengono fatturate in base alle dimensioni con provisioning Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --enabled-protocol SMB \
    --name "myshare" 
```
---

## <a name="clean-up-resources"></a>Pulire le risorse

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per pulire le risorse create in questo articolo, eliminare il gruppo di risorse. L'eliminazione del gruppo di risorse Elimina anche l'account di archiviazione associato e tutte le altre risorse associate al gruppo di risorse.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per pulire le risorse create in questo articolo, eliminare il gruppo di risorse. L'eliminazione del gruppo di risorse Elimina anche l'account di archiviazione associato e tutte le altre risorse associate al gruppo di risorse.

Per rimuovere il gruppo di risorse e tutte le risorse correlate, incluso il nuovo account di archiviazione, usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per pulire le risorse create in questo articolo, eliminare il gruppo di risorse. L'eliminazione del gruppo di risorse Elimina anche l'account di archiviazione associato e tutte le altre risorse associate al gruppo di risorse.

Per rimuovere il gruppo di risorse e tutte le risorse correlate, incluso il nuovo account di archiviazione, usare il comando [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```
---

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una condivisione file Premium. Per informazioni sulle prestazioni offerte da questo account, passare alla sezione livello di prestazioni della Guida alla pianificazione.

> [!div class="nextstepaction"]
> [Livelli di condivisione file](storage-files-planning.md#storage-tiers)