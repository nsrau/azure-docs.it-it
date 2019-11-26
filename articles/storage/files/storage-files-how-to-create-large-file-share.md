---
title: Abilitare e creare condivisioni file di grandi dimensioni-File di Azure
description: Questo articolo illustra come abilitare e creare condivisioni di file di grandi dimensioni.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 34b8af56a8f2f108b96ca07fa73f90bb9eb5bf13
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422734"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>Come abilitare e creare condivisioni file di grandi dimensioni

In origine, le condivisioni file standard potevano essere scalate fino a 5 TiB, ora con condivisioni file di grandi dimensioni, che possono essere scalate fino a 100 TiB. Per impostazione predefinita, le condivisioni file Premium vengono ridimensionate fino a 100 TiB. 

Per scalare fino a 100 TiB usando condivisioni file standard, è necessario abilitare l'account di archiviazione per l'uso di condivisioni file di grandi dimensioni. È possibile abilitare un account esistente o creare un nuovo account per usare condivisioni file di grandi dimensioni.

## <a name="prerequisites"></a>prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Se si intende usare l'interfaccia della riga di comando di Azure, assicurarsi di [installare la versione più recente](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Se si intende usare Azure PowerShell, assicurarsi di [installare la versione più recente](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Restrizioni

Gli account abilitati per condivisioni file di grandi dimensioni supportano con ridondanza locale o ZRS. Per il momento, gli account abilitati per le condivisioni file di grandi dimensioni non supportano GZRS, GRS o RA-GRS. L'abilitazione di condivisioni file di grandi dimensioni in un account è un processo irreversibile, una volta abilitato, l'account non può essere convertito in GZRS, GRS o RA-GRS.

## <a name="create-a-new-storage-account"></a>Creare un nuovo account di archiviazione

### <a name="portal"></a>di Microsoft Azure

Accedere al [portale di Azure](https://portal.azure.com).

1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Account di archiviazione**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Account di archiviazione**.
1. Nella finestra **Account di archiviazione** visualizzata scegliere **Aggiungi**.
1. Selezionare la sottoscrizione in cui creare l'account di archiviazione.
1. Nel campo **Gruppo di risorse** selezionare **Crea nuovo**. Immettere un nome per il nuovo gruppo di risorse, come illustrato nell'immagine seguente.

    ![Screenshot che mostra come creare un gruppo di risorse nel portale](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Immettere quindi un nome per l'account di archiviazione. Il nome scelto deve essere univoco in Azure. Deve avere inoltre una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole.
1. Selezionare una località per l'account di archiviazione, assicurarsi che sia [una delle aree supportate per LFS](storage-files-planning.md#regional-availability).
1. Impostare la replica su archiviazione con **ridondanza locale** o **archiviazione con ridondanza della zona**.
1. Mantenere i valori predefiniti per questi campi:

   |Campo  |Valore  |
   |---------|---------|
   |Modello di distribuzione     |Gestione risorse         |
   |Prestazioni     |Standard         |
   |Tipo di account     |Archiviazione v2 (utilizzo generico V2)         |
   |Livello di accesso     |Accesso frequente         |

1. Selezionare **Avanzate** e selezionare **abilitata** per le **condivisioni file di grandi dimensioni**.
1. Selezionare **Rivedi e crea** per esaminare le impostazioni dell'account di archiviazione e creare l'account.

    ![Large-file-Shares-Advanced-Enable. png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Selezionare **Create**.


### <a name="cli"></a>CLI

Prima di tutto, assicurarsi di [installare la versione più recente](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), in modo da poter abilitare questa funzionalità.

Per creare un account di archiviazione con condivisioni file di grandi dimensioni abilitate, sostituire `<yourStorageAccountName>`, `<yourResourceGroup>`e `<yourDesiredRegion>` con i valori, quindi usare il comando seguente:

```azurecli-interactive
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
az storage account create –name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> –sku Standard_LRS --kind StorageV2 –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Prima di tutto, assicurarsi di [installare la versione più recente](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0), in modo da poter abilitare questa funzionalità.

Per creare un account di archiviazione con condivisioni file di grandi dimensioni abilitate, sostituire `<yourStorageAccountName>`, `<yourResourceGroup>`e `<yourDesiredRegion>` con i valori, quindi usare il comando seguente:

```PowerShell
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-on-existing-account"></a>Abilita sull'account esistente

### <a name="portal"></a>di Microsoft Azure

È anche possibile abilitare condivisioni di file di grandi dimensioni per gli account esistenti. In tal caso, l'account non sarà più in grado di eseguire la conversione in GZRS, GRS o RA-GRS. Questa scelta è irreversibile in questo account.

1. Aprire il [portale di Azure](https://portal.azure.com) e passare all'account di archiviazione in cui si vuole abilitare le condivisioni file di grandi dimensioni.
1. Aprire l'account di archiviazione e selezionare **configurazione**.
1. Selezionare **abilitato** in **condivisioni file di grandi dimensioni**e quindi selezionare Salva.
1. Selezionare **Panoramica** e selezionare **Aggiorna**.

![Enable-large-file-Shares-on-existing. png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

A questo punto sono state abilitate condivisioni file di grandi dimensioni nell'account di archiviazione.

Se viene visualizzato l'errore seguente: "le condivisioni file di grandi dimensioni non sono ancora disponibili per l'account." È possibile attendere del tempo, perché l'area è probabilmente in fase di completamento della distribuzione o, se si hanno esigenze urgenti, rivolgersi al supporto tecnico.

### <a name="cli"></a>CLI

È possibile abilitare condivisioni file di grandi dimensioni per gli account esistenti. In tal caso, l'account non sarà più in grado di eseguire la conversione in GZRS, GRS o RA-GRS. Questa scelta è irreversibile in questo account.

Sostituire `<yourStorageAccountName>` e `<yourResourceGroup>` nel comando seguente, quindi usarlo per abilitare le condivisioni file di grandi dimensioni nell'account esistente:

```azurecli-interactive
az storage account update –name <yourStorageAccountName> -g <yourResourceGroup> –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

È possibile abilitare condivisioni file di grandi dimensioni per gli account esistenti. In tal caso, l'account non sarà più in grado di eseguire la conversione in GZRS, GRS o RA-GRS. Questa scelta è irreversibile in questo account.

Sostituire `<yourStorageAccountName>` e `<yourResourceGroup>` nel comando seguente, quindi usarlo per abilitare le condivisioni file di grandi dimensioni nell'account esistente:

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Creare una condivisione file di grandi dimensioni

### <a name="portal"></a>di Microsoft Azure

La creazione di una condivisione file di grandi dimensioni è quasi identica alla creazione di una condivisione file standard. La differenza principale consiste nel fatto che è possibile impostare una quota fino a 100 TiB.

1. Dall'account di archiviazione selezionare **condivisioni file**.
1. Selezionare **+ Condivisione file**.
1. Immettere un nome per la condivisione file e, facoltativamente, le dimensioni della quota, fino a 100 TiB, quindi selezionare **Crea**. 

![Large-file-Shares-create-share. png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

Una volta abilitate le condivisioni file di grandi dimensioni nell'account di archiviazione, è possibile creare condivisioni file in tale account con quote più elevate. Sostituire `<yourStorageAccountName>`, `<yourStorageAccountKey>`e `<yourFileShareName>` nel comando seguente con i valori, quindi è possibile usarlo per creare una condivisione file di grandi dimensioni:

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Una volta abilitate le condivisioni file di grandi dimensioni nell'account di archiviazione, è possibile creare condivisioni file in tale account con quote più elevate. Sostituire `<YourStorageAccountName>`, `<YourStorageAccountKey>`e `<YourStorageAccountFileShareName>` nel comando seguente con i valori, quindi è possibile usarlo per creare una condivisione file di grandi dimensioni:

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Espandi condivisioni file esistenti

### <a name="portal"></a>di Microsoft Azure

Una volta abilitate le condivisioni file di grandi dimensioni nell'account di archiviazione, è possibile espandere le condivisioni esistenti con la quota più elevata.

1. Dall'account di archiviazione selezionare **condivisioni file**.
1. Fare clic con il pulsante destro del mouse sulla condivisione file e scegliere **quota**.
1. Immettere le nuove dimensioni desiderate, quindi fare clic su **OK**.

![Update-large-file-share-quota. png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

Una volta abilitate le condivisioni file di grandi dimensioni nell'account di archiviazione, è possibile espandere le condivisioni file esistenti nell'account per le quote più elevate. Sostituire `<yourStorageAccountName>`, `<yourStorageAccountKey>`e `<yourFileShareName>` nel comando seguente con i valori, quindi è possibile usarlo per impostare la quota sulla dimensione massima:

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Una volta abilitate le condivisioni file di grandi dimensioni nell'account di archiviazione, è possibile espandere le condivisioni file esistenti nell'account per le quote più elevate. Sostituire `<YourStorageAccountName>`, `<YourStorageAccountKey>`e `<YourStorageAccountFileShareName>` nel comando seguente con i valori, quindi è possibile usarlo per impostare la quota sulla dimensione massima:

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi e montare una condivisione file: Windows](storage-how-to-use-files-windows.md)
* [Connettersi e montare una condivisione file: Linux](../storage-how-to-use-files-linux.md)
* [Connettersi e montare una condivisione file: macOS](storage-how-to-use-files-mac.md)