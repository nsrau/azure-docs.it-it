---
title: Creare un account di archiviazione file di Azure premium
description: In questo articolo descrive come creare un account di archiviazione file di Azure premium e una condivisione di file premium.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 72dec14dde47580313e57bb3b8d7315604929277
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844551"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Come creare una condivisione di file di Azure premium

Il tipo di account di archiviazione FileStorage (anteprima) rappresenta un nuovo livello per i file di Azure, permettendo di creare condivisioni file con caratteristiche di prestazioni premium. Queste condivisioni file sono progettate per prestazioni elevate e applicazioni su scala enterprise, che fornisce bassa latenza costante, numero elevato di IOPS e velocità effettiva elevata condivisioni.

Questo articolo illustra come creare questo nuovo tipo di account usando [portale di Azure](https://portal.azure.com/), Azure PowerShell e CLI di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per accedere ad Archiviazione di Azure è necessaria una sottoscrizione di Azure. Se non si ha già una sottoscrizione, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Creare una condivisione di file premium tramite il portale di Azure

### <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al [portale di Azure](https://portal.azure.com/).

### <a name="create-a-filestorage-preview-storage-account"></a>Creare un account di archiviazione FileStorage (anteprima)

A questo punto si è pronti per creare l'account di archiviazione.

Ogni account di archiviazione deve appartenere a un gruppo di risorse di Azure. Un gruppo di risorse è un contenitore logico per raggruppare i servizi di Azure. Quando si crea un account di archiviazione, è possibile creare un nuovo gruppo di risorse o usarne uno esistente. Questo articolo illustra come creare un nuovo gruppo di risorse.

1. Nel portale di Azure, selezionare **gli account di archiviazione** nel menu a sinistra.

    ![Pagina principale del portale Azure selezionare l'account di archiviazione](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Nella finestra **Account di archiviazione** visualizzata scegliere **Aggiungi**.
1. Selezionare la sottoscrizione in cui creare l'account di archiviazione.
1. Nel campo **Gruppo di risorse** selezionare **Crea nuovo**. Immettere un nome per il nuovo gruppo di risorse, come illustrato nell'immagine seguente.

1. Immettere quindi un nome per l'account di archiviazione. Il nome scelto deve essere univoco in Azure. Deve avere inoltre una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole.
1. Selezionare la località per l'account di archiviazione o usare la località predefinita.
1. Per la **Performance** selezionate **Premium**.
1. Selezionare **tipologia Account** e scegliere **FileStorage (anteprima)**.
1. Lasciare **replica** impostata sul valore predefinito **l'archiviazione con ridondanza locale (LRS)**.

    ![Come creare un account di archiviazione premium di file](media/storage-how-to-create-premium-fileshare/premium-files-storage-account.png)

1. Selezionare **Rivedi e crea** per esaminare le impostazioni dell'account di archiviazione e creare l'account.
1. Selezionare **Create**.

Dopo aver creata la risorsa dell'account di archiviazione, passare ad esso.

### <a name="create-a-premium-file-share"></a>Creare una condivisione file premium

1. Nel menu a sinistra per l'account di archiviazione, scorrere fino al **per il servizio File** sezione e quindi selezionare **i file (anteprima)**.
1. Selezionare **+ condivisione File** per creare una condivisione di file premium.
1. Immettere un nome e una quota desiderata per la condivisione file e quindi selezionare **Create**.

> [!NOTE]
> Le dimensioni di condivisione con provisioning viene specificato dalla quota di condivisione, condivisioni file vengono fatturate le dimensioni di provisioning, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/files/) per altri dettagli.

   ![Creare una condivisione file premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Pulire le risorse

Se si desidera pulire le risorse create in questo articolo, è sufficiente eliminare il gruppo di risorse. Il gruppo di risorse anche se si elimina l'account di archiviazione associato, nonché a eventuali altre risorse associate al gruppo di risorse.

## <a name="create-a-premium-file-share-using-powershell"></a>Creare una condivisione di file premium tramite PowerShell

### <a name="create-an-account-using-powershell"></a>Creare un account con PowerShell

Installare innanzitutto la versione più recente del modulo [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).

Quindi, aggiornare il modulo di PowerShell, accedere alla sottoscrizione di Azure, creare un gruppo di risorse e quindi creare un account di archiviazione.

### <a name="upgrade-your-powershell-module"></a>Aggiornare il modulo di Powershell

Per interagire con i file di premium con PowerShell, è necessario installare il modulo Az.Storage più recente.

Iniziare aprendo una sessione di PowerShell con autorizzazioni elevate.

Installare il modulo Az.Storage:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Accedere alla sottoscrizione di Azure

Usare il comando `Login-AzAccount` e seguire le istruzioni visualizzate per eseguire l'autenticazione.

```powershell
Login-AzAccount
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

### <a name="create-a-filestorage-preview-storage-account"></a>Creare un account di archiviazione filestorage (anteprima)

Per creare un account di archiviazione FileStorage (anteprima) da PowerShell, usare il [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) comando:

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Creare una condivisione file premium

Ora che avete un account FileStorage, è possibile creare una condivisione di file premium. Usare la [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) cmdlet per crearne uno.

> [!NOTE]
> Le dimensioni di condivisione con provisioning viene specificato dalla quota di condivisione, condivisioni file vengono fatturate le dimensioni di provisioning, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/files/) per altri dettagli.

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

## <a name="create-a-premium-file-share-using-azure-cli"></a>Creare una condivisione di file premium tramite la CLI di Azure

Per avviare Azure Cloud Shell, accedere al [portale di Azure](https://portal.azure.com).

Per accedere all'installazione locale dell'interfaccia della riga di comando, eseguire il comando di accesso:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-premium-files"></a>Aggiungere l'estensione dell'interfaccia della riga per i file di Azure premium

Per interagire con i file premium usando l'interfaccia della riga di comando, è possibile aggiungere un'estensione per la shell.

A tale scopo, usando Cloud Shell o una shell locale, immettere il comando seguente: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per creare un nuovo gruppo di risorse con l'interfaccia della riga di comando di Azure, usare il comando [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-preview-storage-account"></a>Creare un account di archiviazione FileStorage (anteprima)

Per creare un account di archiviazione FileStorage (anteprima) della riga di comando di Azure, usare il [az storage account creare](/cli/azure/storage/account) comando.

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Ottenere la chiave dell'account di archiviazione

Chiavi dell'account di archiviazione controllano l'accesso alle risorse in un account di archiviazione, in questo articolo, viene usata la chiave per creare una condivisione di file premium. Vengono create automaticamente quando si crea un account di archiviazione. È possibile ottenere le chiavi per l'account di archiviazione con il comando [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Creare una condivisione file premium

Ora che avete un account FileStorage, è possibile creare una condivisione di file premium. Usare la [creare la condivisione di archiviazione az](/cli/azure/storage/share) comandi per crearne uno.

> [!NOTE]
> Le dimensioni di condivisione con provisioning viene specificato dalla quota di condivisione, condivisioni file vengono fatturate le dimensioni di provisioning, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/files/) per altri dettagli.

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

In questo articolo è stato creato un account di archiviazione premium di file. Per altre informazioni sulle prestazioni di che questo account offre, continuare con la sezione di livello delle prestazioni della Guida alla pianificazione.

> [!div class="nextstepaction"]
> [I livelli di prestazioni di condivisione di file](storage-files-planning.md#file-share-performance-tiers)