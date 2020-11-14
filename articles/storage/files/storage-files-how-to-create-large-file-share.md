---
title: Abilitare e creare condivisioni file di grandi dimensioni-File di Azure
description: Questo articolo illustra come abilitare e creare condivisioni di file di grandi dimensioni.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/29/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 5acb8e347046780c84f0aa324b997abb5e2aa840
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629224"
---
# <a name="enable-and-create-large-file-shares"></a>Abilitare e creare condivisioni file di grandi dimensioni

Quando si abilitano condivisioni file di grandi dimensioni nell'account di archiviazione, le condivisioni file possono essere scalate fino a 100 TiB, aumentando al tempo stesso i limiti di IOPS e velocità effettiva per le condivisioni standard. È anche possibile abilitare questa scalabilità per gli account di archiviazione esistenti per le condivisioni file esistenti. Per informazioni dettagliate, vedere destinazioni per la [condivisione file e la scalabilità di file](storage-files-scale-targets.md#azure-files-scale-targets) . 

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Se si intende usare l'interfaccia della riga di comando di Azure, [installare l'ultima versione](/cli/azure/install-azure-cli?view=azure-cli-latest).
- Se si intende usare il modulo Azure PowerShell, [installare la versione più recente](/powershell/azure/install-az-ps?view=azps-4.6.0).

## <a name="restrictions"></a>Restrizioni

Per il momento, è possibile usare l'archiviazione con ridondanza locale (con ridondanza locale) o l'archiviazione con ridondanza della zona (ZRS) in account abilitati per la condivisione file di grandi dimensioni. Non è possibile usare archiviazione con ridondanza geografica (GZRS), archiviazione con ridondanza geografica (GRS), archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) o archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS).

L'abilitazione di condivisioni file di grandi dimensioni in un account è un processo irreversibile. Dopo averla abilitata, non sarà possibile convertire l'account in GZRS, GRS, RA-GRS o RA-GZRS.

## <a name="create-a-new-storage-account"></a>Creare un nuovo account di archiviazione.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel portale di Azure fare clic su **Tutti i servizi**. 
1. Nell'elenco delle risorse immettere account di **archiviazione**. Mentre si digita, l'elenco viene filtrato in base all'input. Selezionare **Account di archiviazione**.
1. Nella finestra **account di archiviazione** visualizzata selezionare **Aggiungi**.
1. Selezionare la sottoscrizione che verrà usata per creare l'account di archiviazione.
1. Nel campo **Gruppo di risorse** selezionare **Crea nuovo**. Immettere un nome per il nuovo gruppo di risorse.

    ![Screenshot che mostra come creare un gruppo di risorse nel portale](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Immettere quindi un nome per l'account di archiviazione. Il nome deve essere univoco in Azure. Anche il nome deve avere una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole.
1. Selezionare la località per l'account di archiviazione.
1. Impostare la replica sull'archiviazione con **ridondanza locale** o sull' **archiviazione con ridondanza della zona**.
1. Lasciare i valori predefiniti per questi campi:

   |Campo  |Valore  |
   |---------|---------|
   |Modello di distribuzione     |Gestione risorse         |
   |Prestazioni     |Standard         |
   |Tipologia account     |Archiviazione v2 (utilizzo generico V2)         |
   |Livello di accesso     |Accesso frequente         |

1. Selezionare **Avanzate** , quindi selezionare il pulsante di opzione **abilitato** a destra di **condivisioni file di grandi dimensioni**.
1. Selezionare **Rivedi e crea** per esaminare le impostazioni dell'account di archiviazione e creare l'account.

    ![Screenshot con il pulsante di opzione "Enabled" in un nuovo account di archiviazione nel portale di Azure](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Selezionare **Crea**.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Installare prima [di tutto la versione più recente dell'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) in modo da poter abilitare condivisioni file di grandi dimensioni.

Per creare un account di archiviazione con condivisioni file di grandi dimensioni abilitata, usare il comando seguente. Sostituire `<yourStorageAccountName>` , `<yourResourceGroup>` e `<yourDesiredRegion>` con le informazioni.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Installare prima [di tutto la versione più recente di PowerShell](/powershell/azure/install-az-ps?view=azps-3.0.0) in modo da poter abilitare le condivisioni file di grandi dimensioni.

Per creare un account di archiviazione con condivisioni file di grandi dimensioni abilitata, usare il comando seguente. Sostituire `<yourStorageAccountName>` , `<yourResourceGroup>` e `<yourDesiredRegion>` con le informazioni.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```
---

## <a name="enable-large-files-shares-on-an-existing-account"></a>Abilita condivisioni di file di grandi dimensioni in un account esistente

È anche possibile abilitare condivisioni file di grandi dimensioni per gli account esistenti. Se si abilitano condivisioni file di grandi dimensioni, non sarà possibile eseguire la conversione in GZRS, GRS, RA-GRS o RA-GZRS. L'abilitazione di condivisioni file di grandi dimensioni è irreversibile in questo account di archiviazione.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Aprire il [portale di Azure](https://portal.azure.com)e passare all'account di archiviazione in cui si vuole abilitare le condivisioni file di grandi dimensioni.
1. Aprire l'account di archiviazione e selezionare **configurazione**.
1. Selezionare **abilitato** in **condivisioni file di grandi dimensioni** e quindi fare clic su **Salva**.
1. Selezionare **Panoramica** e selezionare **Aggiorna**.

![Selezione del pulsante di opzione abilitato in un account di archiviazione esistente nel portale di Azure](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

A questo punto sono state abilitate condivisioni file di grandi dimensioni nell'account di archiviazione. Successivamente, è necessario [aggiornare la quota della condivisione esistente](#expand-existing-file-shares) per sfruttare la capacità e la scalabilità migliorate.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per abilitare le condivisioni file di grandi dimensioni per l'account esistente, usare il comando seguente. Sostituire `<yourStorageAccountName>` e `<yourResourceGroup>` con le informazioni.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

A questo punto sono state abilitate condivisioni file di grandi dimensioni nell'account di archiviazione. Successivamente, è necessario [aggiornare la quota della condivisione esistente](#expand-existing-file-shares) per sfruttare la capacità e la scalabilità migliorate.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per abilitare le condivisioni file di grandi dimensioni per l'account esistente, usare il comando seguente. Sostituire `<yourStorageAccountName>` e `<yourResourceGroup>` con le informazioni.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

A questo punto sono state abilitate condivisioni file di grandi dimensioni nell'account di archiviazione. Successivamente, è necessario [aggiornare la quota della condivisione esistente](#expand-existing-file-shares) per sfruttare la capacità e la scalabilità migliorate.

---

## <a name="create-a-large-file-share"></a>Creare una condivisione file di grandi dimensioni

Dopo aver abilitato le condivisioni file di grandi dimensioni nell'account di archiviazione, è possibile creare condivisioni file in tale account con quote più elevate. 

# <a name="portal"></a>[Portale](#tab/azure-portal)

La creazione di una condivisione file di grandi dimensioni è quasi identica alla creazione di una condivisione file standard. La differenza principale consiste nel fatto che è possibile impostare una quota fino a 100 TiB.

1. Dall'account di archiviazione selezionare **condivisioni file**.
1. Selezionare **+ condivisione file**.
1. Immettere un nome per la condivisione file. È anche possibile impostare le dimensioni della quota, fino a 100 TiB. Selezionare quindi **Crea**. 

![Interfaccia utente di portale di Azure che mostra le caselle nome e quota](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per creare una condivisione file di grandi dimensioni, utilizzare il comando seguente. Sostituire `<yourStorageAccountName>` , `<yourStorageAccountKey>` e `<yourFileShareName>` con le informazioni.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per creare una condivisione file di grandi dimensioni, utilizzare il comando seguente. Sostituire `<YourStorageAccountName>` , `<YourStorageAccountKey>` e `<YourStorageAccountFileShareName>` con le informazioni.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```
---

## <a name="expand-existing-file-shares"></a>Espandi condivisioni file esistenti

Dopo aver abilitato le condivisioni file di grandi dimensioni nell'account di archiviazione, è anche possibile espandere le condivisioni file esistenti in tale account con una quota superiore. 

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Dall'account di archiviazione selezionare **condivisioni file**.
1. Fare clic con il pulsante destro del mouse sulla condivisione file e quindi scegliere **quota**.
1. Immettere la nuova dimensione desiderata e quindi fare clic su **OK**.

![Interfaccia utente di portale di Azure con quota di condivisioni file esistenti](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per impostare la quota sulla dimensione massima, utilizzare il comando seguente. Sostituire `<yourStorageAccountName>` , `<yourStorageAccountKey>` e `<yourFileShareName>` con le informazioni.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per impostare la quota sulla dimensione massima, utilizzare il comando seguente. Sostituire `<YourStorageAccountName>` , `<YourStorageAccountKey>` e `<YourStorageAccountFileShareName>` con le informazioni.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```
---

## <a name="next-steps"></a>Passaggi successivi

* [Connettere e montare una condivisione file in Windows](storage-how-to-use-files-windows.md)
* [Connettere e montare una condivisione file in Linux](storage-how-to-use-files-linux.md)
* [Connettere e montare una condivisione file in macOS](storage-how-to-use-files-mac.md)