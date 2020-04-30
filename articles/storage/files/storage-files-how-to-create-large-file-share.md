---
title: Abilitare e creare condivisioni file di grandi dimensioni-File di Azure
description: Questo articolo illustra come abilitare e creare condivisioni di file di grandi dimensioni.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd7726d2bbf2830d18d78b5f0b0d7202b734124d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537679"
---
# <a name="enable-and-create-large-file-shares"></a>Abilitare e creare condivisioni file di grandi dimensioni

Quando si abilitano condivisioni file di grandi dimensioni nell'account di archiviazione, le condivisioni file possono essere scalate fino a 100 TiB. È possibile abilitare questa scalabilità per gli account di archiviazione esistenti per le condivisioni file esistenti.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Se si intende usare l'interfaccia della riga di comando di Azure, [installare la versione più recente](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Se si intende usare Azure PowerShell, [installare la versione più recente](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Restrizioni

Per il momento, è possibile usare l'archiviazione con ridondanza locale (con ridondanza locale) o l'archiviazione con ridondanza della zona (ZRS) in account abilitati per la condivisione file di grandi dimensioni. Non è possibile usare l'archiviazione con ridondanza geografica (GZRS), l'archiviazione con ridondanza geografica (GRS) o l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).
L'abilitazione di condivisioni file di grandi dimensioni in un account è un processo irreversibile. Dopo averla abilitata, non sarà possibile convertire l'account in GZRS, GRS o RA-GRS.

## <a name="create-a-new-storage-account"></a>Creare un nuovo account di archiviazione.

### <a name="portal"></a>Portale

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella portale di Azure selezionare **tutti i servizi**. 
1. Nell'elenco delle risorse immettere account di **archiviazione**. Mentre si digita, l'elenco viene filtrato in base all'input. Selezionare **account di archiviazione**.
1. Nella finestra **account di archiviazione** visualizzata selezionare **Aggiungi**.
1. Selezionare la sottoscrizione che verrà usata per creare l'account di archiviazione.
1. Nel campo **Gruppo di risorse** selezionare **Crea nuovo**. Immettere un nome per il nuovo gruppo di risorse.

    ![Screenshot che mostra come creare un gruppo di risorse nel portale](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Immettere quindi un nome per l'account di archiviazione. Il nome deve essere univoco in Azure. Anche il nome deve avere una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole.
1. Selezionare la località per l'account di archiviazione.
1. Impostare la replica sull'archiviazione con **ridondanza locale** o sull' **archiviazione con ridondanza della zona**.
1. Lasciare i valori predefiniti per questi campi:

   |Campo  |valore  |
   |---------|---------|
   |Modello di distribuzione     |Gestione risorse         |
   |Prestazioni     |Standard         |
   |Tipo di account     |Archiviazione v2 (utilizzo generico V2)         |
   |Livello di accesso     |Accesso frequente         |

1. Selezionare **Avanzate**, quindi selezionare il pulsante di opzione **abilitato** a destra di **condivisioni file di grandi dimensioni**.
1. Selezionare **Rivedi e crea** per esaminare le impostazioni dell'account di archiviazione e creare l'account.

    ![Screenshot con il pulsante di opzione "Enabled" in un nuovo account di archiviazione nel portale di Azure](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Selezionare **Crea**.

### <a name="cli"></a>CLI

Installare prima [di tutto la versione più recente dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) in modo da poter abilitare condivisioni file di grandi dimensioni.

Per creare un account di archiviazione con condivisioni file di grandi dimensioni abilitata, usare il comando seguente. Sostituire `<yourStorageAccountName>`, `<yourResourceGroup>`e `<yourDesiredRegion>` con le informazioni.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Installare prima [di tutto la versione più recente di PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) in modo da poter abilitare le condivisioni file di grandi dimensioni.

Per creare un account di archiviazione con condivisioni file di grandi dimensioni abilitata, usare il comando seguente. Sostituire `<yourStorageAccountName>`, `<yourResourceGroup>`e `<yourDesiredRegion>` con le informazioni.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>Abilita condivisioni di file di grandi dimensioni in un account esistente

È anche possibile abilitare condivisioni file di grandi dimensioni per gli account esistenti. Se si abilitano condivisioni file di grandi dimensioni, non sarà possibile eseguire la conversione in GZRS, GRS o RA-GRS. L'abilitazione di condivisioni file di grandi dimensioni è irreversibile in questo account di archiviazione.

### <a name="portal"></a>Portale

1. Aprire il [portale di Azure](https://portal.azure.com)e passare all'account di archiviazione in cui si vuole abilitare le condivisioni file di grandi dimensioni.
1. Aprire l'account di archiviazione e selezionare **configurazione**.
1. Selezionare **abilitato** in **condivisioni file di grandi dimensioni**e quindi fare clic su **Salva**.
1. Selezionare **Panoramica** e selezionare **Aggiorna**.

![Selezione del pulsante di opzione abilitato in un account di archiviazione esistente nel portale di Azure](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

A questo punto sono state abilitate condivisioni file di grandi dimensioni nell'account di archiviazione. Successivamente, è necessario aggiornare la quota della condivisione esistente per sfruttare la capacità e la scalabilità migliorate.

Se viene visualizzato il messaggio di errore "le condivisioni file di grandi dimensioni non sono ancora disponibili per l'account", è possibile che l'area in cui si sta eseguendo l'implementazione sia in corso. Contattare il supporto tecnico in caso di necessità urgenti di condivisioni file di grandi dimensioni.

### <a name="cli"></a>CLI

Per abilitare le condivisioni file di grandi dimensioni per l'account esistente, usare il comando seguente. Sostituire `<yourStorageAccountName>` e `<yourResourceGroup>` con le informazioni.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Per abilitare le condivisioni file di grandi dimensioni per l'account esistente, usare il comando seguente. Sostituire `<yourStorageAccountName>` e `<yourResourceGroup>` con le informazioni.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Creare una condivisione file di grandi dimensioni

Dopo aver abilitato le condivisioni file di grandi dimensioni nell'account di archiviazione, è possibile creare condivisioni file in tale account con quote più elevate. 

### <a name="portal"></a>Portale

La creazione di una condivisione file di grandi dimensioni è quasi identica alla creazione di una condivisione file standard. La differenza principale consiste nel fatto che è possibile impostare una quota fino a 100 TiB.

1. Dall'account di archiviazione selezionare **condivisioni file**.
1. Selezionare **+ condivisione file**.
1. Immettere un nome per la condivisione file. È anche possibile impostare le dimensioni della quota, fino a 100 TiB. Selezionare quindi **Crea**. 

![Interfaccia utente di portale di Azure che mostra le caselle nome e quota](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

Per creare una condivisione file di grandi dimensioni, utilizzare il comando seguente. Sostituire `<yourStorageAccountName>`, `<yourStorageAccountKey>`e `<yourFileShareName>` con le informazioni.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Per creare una condivisione file di grandi dimensioni, utilizzare il comando seguente. Sostituire `<YourStorageAccountName>`, `<YourStorageAccountKey>`e `<YourStorageAccountFileShareName>` con le informazioni.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Espandi condivisioni file esistenti

Dopo aver abilitato le condivisioni file di grandi dimensioni nell'account di archiviazione, è anche possibile espandere le condivisioni file esistenti in tale account con una quota superiore. 

### <a name="portal"></a>Portale

1. Dall'account di archiviazione selezionare **condivisioni file**.
1. Fare clic con il pulsante destro del mouse sulla condivisione file e quindi scegliere **quota**.
1. Immettere la nuova dimensione desiderata e quindi fare clic su **OK**.

![Interfaccia utente di portale di Azure con quota di condivisioni file esistenti](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

Per impostare la quota sulla dimensione massima, utilizzare il comando seguente. Sostituire `<yourStorageAccountName>`, `<yourStorageAccountKey>`e `<yourFileShareName>` con le informazioni.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Per impostare la quota sulla dimensione massima, utilizzare il comando seguente. Sostituire `<YourStorageAccountName>`, `<YourStorageAccountKey>`e `<YourStorageAccountFileShareName>` con le informazioni.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Passaggi successivi

* [Connettere e montare una condivisione file in Windows](storage-how-to-use-files-windows.md)
* [Connettere e montare una condivisione file in Linux](storage-how-to-use-files-linux.md)
* [Connettere e montare una condivisione file in macOS](storage-how-to-use-files-mac.md)
