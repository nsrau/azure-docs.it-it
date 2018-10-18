---
title: Creare un account di archiviazione della versione di anteprima di Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni su come creare rapidamente un nuovo account di archiviazione con accesso alla versione di anteprima di Data Lake Storage Gen2 usando il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure
services: storage
author: jamesbak
ms.component: data-lake-storage-gen2
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 06/27/2018
ms.author: jamesbak
ms.openlocfilehash: 4c3354786d7320caee0dfe2f0eca44909d77f2fe
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386852"
---
# <a name="quickstart-create-an-azure-data-lake-storage-gen2-preview-storage-account"></a>Guida introduttiva: Creare un account di archiviazione della versione di anteprima di Azure Data Lake Storage Gen2

Gli account della versione di anteprima di Azure Data Lake Storage Gen2 [supportano un servizio spazio dei nomi gerarchico](introduction.md) che fornisce un file system nativo basato su directory da usare con Hadoop Distributed File System (HDFS). L'accesso ai dati di Data Lake Storage Gen2 da HDFS è disponibile tramite il [driver ABFS](abfs-driver.md).

Per abilitare le funzionalità di Data Lake Storage Gen2 nell'account di archiviazione, [compilare il sondaggio di anteprima per richiedere l'accesso](https://aka.ms/adlsgen2signup). Una volta ottenuta l'approvazione, sarà possibile creare un nuovo account di Data Lake Storage Gen2. Questa guida introduttiva illustra come creare un account tramite il [portale di Azure](https://portal.azure.com/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

> [!NOTE]
> L'interfaccia utente di creazione dell'account nel portale di Azure viene aggiornata dopo che è stata ricevuta l'approvazione per la creazione di un account di Data Lake Storage Gen2. Analogamente, gli argomenti di PowerShell e dell'interfaccia della riga di comando relativi a Data Lake Storage Gen2 funzionano solo dopo avere ottenuto l'approvazione per l'anteprima.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

|           | Prerequisito |
|-----------|--------------|
|Portale     | Nessuno         |
|PowerShell | Per questa guida introduttiva è richiesto il modulo di Azure PowerShell versione **5.0.4-preview** o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione corrente. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps). |
|CLI        | È possibile accedere ad Azure ed eseguire i comandi dell'interfaccia della riga di comando di Azure in uno dei due modi seguenti: <ul><li>È possibile eseguire i comandi dell'interfaccia della riga di comando nel portale di Azure, in Azure Cloud Shell </li><li>È possibile installare l'interfaccia della riga di comando ed eseguire i relativi comandi in locale</li></ul>|

Quando si usa la riga di comando, è possibile scegliere se eseguire Azure Cloud Shell o installare l'interfaccia della riga di comando in locale.

### <a name="use-azure-cloud-shell"></a>Usare Azure Cloud Shell

Azure Cloud Shell è una shell Bash gratuita che è possibile eseguire direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Fare clic sul pulsante **Cloud Shell** nel menu in alto a destra nel portale di Azure:

[![Cloud Shell](./media/quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Il pulsante avvia una shell interattiva che è possibile usare per eseguire i passaggi di questa guida introduttiva:

[![Screenshot che mostra la finestra di Cloud Shell nel portale](./media/quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installare l'interfaccia della riga di comando in locale

È anche possibile installare e usare l'interfaccia della riga di comando di Azure in locale. Questa guida introduttiva richiede l'interfaccia della riga di comando di Azure 2.0.38 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="overview-of-creating-an-azure-data-lake-storage-gen2-account"></a>Panoramica della creazione di un account di Azure Data Lake Storage Gen2

Prima di creare un account, è necessario un gruppo di risorse che funge da contenitore logico per gli account di archiviazione o altre risorse di Azure create. Per pulire le risorse create in questa guida introduttiva, è sufficiente eliminare il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminati anche l'account di archiviazione associato e tutte le altre risorse correlate al gruppo di risorse. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> È necessario creare nuovi account di archiviazione con tipo **Archiviazione V2 (utilizzo generico v2)** per sfruttare le funzionalità di Data Lake Storage Gen2.  

Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).

Quando si assegna un nome all'account di archiviazione, tenere presenti queste regole:

- I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.
- Nome dell'account di archiviazione deve essere univoco all'interno di Azure. Due account di archiviazione non possono avere lo stesso nome.

## <a name="create-an-account-using-the-azure-portal"></a>Creare un account usando il portale di Azure

Accedere al [Portale di Azure](https://portal.azure.com).

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per creare un gruppo di risorse nel portale di Azure, seguire questa procedura:

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e scegliere **Gruppi di risorse**.
2. Fare clic sul pulsante **Aggiungi** per aggiungere un nuovo gruppo di risorse.
3. Immettere un nome per il nuovo gruppo di risorse.
4. Selezionare la sottoscrizione in cui creare il nuovo gruppo di risorse.
5. Scegliere la posizione per il gruppo di risorse.
6. Fare clic sul pulsante **Create** (Crea).  

![Screenshot che mostra la creazione del gruppo di risorse nel portale di Azure](./media/quickstart-create-account/create-resource-group.png)

### <a name="create-a-general-purpose-v2-storage-account"></a>Creare un account di archiviazione per utilizzo generico v2

Per creare un account di archiviazione per utilizzo generico v2 nel portale di Azure, eseguire questa procedura:

> [!NOTE]
> Lo spazio dei nomi gerarchico è abilitato solo in Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centro-occidentali, Europa settentrionale, Europa occidentale, Asia sud-orientale e Australia orientale. Assicurarsi di specificare una di queste posizioni quando si crea l'account di archiviazione.

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e scegliere **Tutti i servizi**. Scorrere quindi verso il basso fino ad **Archiviazione** e scegliere **Account di archiviazione**. Nella finestra **Account di archiviazione** visualizzata scegliere **Aggiungi**.
2. Immettere un nome per l'account di archiviazione.
3. Lasciare l'opzione **Modello di distribuzione** impostata sul valore predefinito.
4. Impostare il campo **Tipologia account** su **StorageV2 (utilizzo generico v2)**.
5. Impostare **Posizione** su **Stati Uniti occidentali 2**
6. Lasciare il campo **Replica** impostato su **Archiviazione con ridondanza locale**.
7. Lasciare questi campi impostati sui valori predefiniti: **Replica**, **Prestazioni**, **Livello di accesso**.
8. Scegliere la sottoscrizione in cui creare l'account di archiviazione.
9. Nella sezione **Gruppo di risorse** selezionare **Usa esistente** e quindi scegliere il gruppo di risorse creato nella sezione precedente.
10. Mantenere il valore predefinito per **Reti virtuali**
11. Nella sezione **Data Lake Storage Gen2 (preview)** (Data Lake Storage Gen2 - anteprima) impostare **Hierarchical namespace** (Spazio dei nomi gerarchico) su **Abilitato**.
12. Fare clic su **Crea** per creare l'account di archiviazione.

![Screenshot che mostra la creazione dell'account di archiviazione nel portale di Azure](./media/quickstart-create-account/azure-data-lake-storage-account-create.png)

L'account di archiviazione viene ora creato tramite il portale.

### <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere un gruppo di risorse usando il portale di Azure:

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e scegliere **Gruppi di risorse** per visualizzare l'elenco dei gruppi di risorse.
2. Individuare il gruppo di risorse da eliminare e fare clic con il pulsante destro del mouse su **Altro** (**...**) a destra dell'elenco.
3. Scegliere **Elimina gruppo di risorse** e confermare.

## <a name="create-an-account-using-powershell"></a>Creare un account con PowerShell

Accedere alla sottoscrizione di Azure con il comando `Login-AzureRmAccount` e seguire le istruzioni visualizzate per eseguire l'autenticazione.

```powershell
Login-AzureRmAccount
```

### <a name="upgrade-your-powershell-module"></a>Aggiornare il modulo di Powershell

Per interagire con Data Lake Storage Gen2 tramite PowerShell, è necessario eseguire l'aggiornamento del modulo alla versione di anteprima.

A tale scopo, aprire un'istanza di PowerShell con privilegi elevati e immettere il comando seguente: `Install-Module AzureRM.Storage –Repository PSGallery -RequiredVersion 5.0.4-preview –AllowPrerelease –AllowClobber –Force `

Quindi riavviare la shell.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per creare un nuovo gruppo di risorse con PowerShell, usare il comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup): 

> [!NOTE]
> Lo spazio dei nomi gerarchico è abilitato solo in Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centro-occidentali, Europa settentrionale, Europa occidentale, Asia sud-orientale e Australia orientale. Assicurarsi di specificare una di queste posizioni quando si crea l'account di archiviazione.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>Creare un account di archiviazione per utilizzo generico v2

Per creare un account di archiviazione per utilizzo generico v2 da PowerShell con l'archiviazione con ridondanza locale, usare il comando [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount):

```powershell
Get-AzureRmLocation | select Location 
$location = "westus2"

New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere il gruppo di risorse e tutte le risorse correlate, incluso il nuovo account di archiviazione, usare il comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup): 

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Creare un account con l'interfaccia della riga di comando di Azure

Per avviare Azure Cloud Shell, accedere al [portale di Azure](https://portal.azure.com).

Per accedere all'installazione locale dell'interfaccia della riga di comando, eseguire il comando di accesso:

```cli
az login
```

### <a name="upgrade-your-cli-module"></a>Aggiornare il modulo dell'interfaccia della riga di comando

Per interagire con Data Lake Storage Gen2 tramite l'interfaccia della riga di comando, è necessario aggiungere l'estensione alla shell.

A tale scopo, usando Cloud Shell o una shell locale, immettere il comando seguente: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per creare un nuovo gruppo di risorse con l'interfaccia della riga di comando di Azure, usare il comando [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create \
    --name storage-quickstart-resource-group \
    --location westus2
```

> [!NOTE]
> Lo spazio dei nomi gerarchico è abilitato solo in Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centro-occidentali, Europa settentrionale, Europa occidentale, Asia sud-orientale e Australia orientale. Assicurarsi di specificare una di queste posizioni quando si crea l'account di archiviazione.

### <a name="create-a-general-purpose-v2-storage-account"></a>Creare un account di archiviazione per utilizzo generico v2

Per creare un account di archiviazione per utilizzo generico v2 dall'interfaccia della riga di comando di Azure con l'archiviazione con ridondanza locale, usare il comando [az storage account create](/cli/azure/storage/account#az_storage_account_create).

```azurecli-interactive
az storage account create \
    --name storagequickstart \
    --resource-group storage-quickstart-resource-group \
    --location westus2 \
    --sku Standard_LRS \
    --kind StorageV2 \
    --hierarchical-namespace true
```

### <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere il gruppo di risorse e tutte le risorse correlate, incluso il nuovo account di archiviazione, usare il comando [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un account di archiviazione di Data Lake Storage Gen2. Per informazioni su come caricare e scaricare BLOB nell'account di archiviazione, passare alla guida introduttiva su Archiviazione BLOB.

* [Spostamento dei dati da e verso l'archivio BLOB di Azure tramite AzCopy](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azcopy)