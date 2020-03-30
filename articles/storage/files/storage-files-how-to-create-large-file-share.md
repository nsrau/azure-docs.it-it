---
title: Abilitare e creare condivisioni file di grandi dimensioni - File di AzureEnable and create large file shares - Azure Files
description: In questo articolo viene illustrato come abilitare e creare condivisioni file di grandi dimensioni.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c683e9847864de4e3409fb6dbd533497a5ae3cea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061008"
---
# <a name="enable-and-create-large-file-shares"></a>Abilitare e creare condivisioni file di grandi dimensioniEnable and create large file shares

Quando si abilitano condivisioni file di grandi dimensioni nell'account di archiviazione, le condivisioni file possono essere scalabili fino a 100 TiB. È possibile abilitare questa scalabilità negli account di archiviazione esistenti per le condivisioni file esistenti.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Se si intende usare l'interfaccia della riga di comando di Azure, [installare la versione più recente.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Se si intende usare Azure PowerShell, [installare la versione più recente.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="restrictions"></a>Restrizioni

Per il momento, è possibile utilizzare solo l'archiviazione con ridondanza locale (LRS) o l'archiviazione con ridondanza di zona (RS) solo in account abilitati per la condivisione file di grandi dimensioni. Non è possibile usare l'archiviazione con ridondanza di zona geografica, l'archiviazione con ridondanza geografica (GRS) o l'archiviazione con ridondanza geografica di accesso in lettura (RA-GRS).
L'abilitazione di condivisioni file di grandi dimensioni in un account è un processo irreversibile. Dopo averlo abilitato, non potrai convertire il tuo account in GRS, GRS o RA-GRS.

## <a name="create-a-new-storage-account"></a>Creare un nuovo account di archiviazione.

### <a name="portal"></a>Portale

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Nel portale di Azure fare clic su **Tutti i servizi**. 
1. Nell'elenco delle risorse immettere **Account di archiviazione.** Mentre si digita, l'elenco viene filtrato in base all'input. Selezionare **Account di archiviazione**.
1. Nella finestra **Account di archiviazione** visualizzata selezionare **Aggiungi**.
1. Selezionare la sottoscrizione da usare per creare l'account di archiviazione.
1. Nel campo **Gruppo di risorse** selezionare **Crea nuovo**. Immettere un nome per il nuovo gruppo di risorse.

    ![Screenshot che mostra come creare un gruppo di risorse nel portale](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Immettere quindi un nome per l'account di archiviazione. Il nome deve essere univoco in Azure. Il nome deve inoltre avere una lunghezza da 3 a 24 caratteri e può avere solo numeri e lettere minuscole.
1. Selezionare un percorso per l'account di archiviazione e assicurarsi che sia [una delle repliche supportate per le condivisioni file](storage-files-planning.md#regional-availability)di grandi dimensioni.
1. Impostare la replica su **Archiviazione con ridondanza locale** o Archiviazione con **ridondanza di zona**.
1. Lasciare i valori predefiniti di questi campi:

   |Campo  |valore  |
   |---------|---------|
   |Modello di distribuzione     |Gestione risorse         |
   |Prestazioni     |Standard         |
   |Tipo di account     |Archiviazione v2 (utilizzo generico V2)         |
   |Livello di accesso     |Accesso frequente         |

1. Selezionare **Avanzate**, quindi il pulsante di opzione **Abilitato** a destra di **Condivisioni file**di grandi dimensioni .
1. Selezionare **Rivedi e crea** per esaminare le impostazioni dell'account di archiviazione e creare l'account.

    ![Screenshot con il pulsante di opzione "abilitato" in un nuovo account di archiviazione nel portale di Azure](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Selezionare **Crea**.

### <a name="cli"></a>CLI

Installare innanzitutto [la versione più recente dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) in modo da poter abilitare condivisioni file di grandi dimensioni.

Per creare un account di archiviazione con condivisioni file di grandi dimensioni abilitate, usare il comando seguente. Sostituire `<yourStorageAccountName>` `<yourResourceGroup>`, `<yourDesiredRegion>` e con le informazioni.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Installare innanzitutto [la versione più recente di PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) in modo da poter abilitare condivisioni file di grandi dimensioni.

Per creare un account di archiviazione con condivisioni file di grandi dimensioni abilitate, usare il comando seguente. Sostituire `<yourStorageAccountName>` `<yourResourceGroup>`, `<yourDesiredRegion>` e con le informazioni.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>Abilitare condivisioni file di grandi dimensioni in un account esistenteEnable large files shares on an existing account

È inoltre possibile abilitare condivisioni file di grandi dimensioni per gli account esistenti. Se si abilitano condivisioni di file di grandi dimensioni, non sarà possibile eseguire la conversione in FORMATO GRS, GRS o RA-GRS. L'abilitazione di condivisioni file di grandi dimensioni è irreversibile in questo account di archiviazione.

### <a name="portal"></a>Portale

1. Aprire il portale di [Azure](https://portal.azure.com)e passare all'account di archiviazione in cui si vuole abilitare condivisioni file di grandi dimensioni.
1. Aprire l'account di archiviazione e selezionare **Configurazione**.
1. Selezionare **Abilitato** nelle **condivisioni file**di grandi dimensioni , quindi **selezionare Salva**.
1. Selezionare **Panoramica** e quindi **Aggiorna**.

![Selezione del pulsante di opzione Abilitato in un account di archiviazione esistente nel portale di AzureSelecting the Enabled option button on an existing storage account in the Azure portal](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Sono state abilitate condivisioni file di grandi dimensioni nell'account di archiviazione. Successivamente, è necessario aggiornare la quota della condivisione esistente per sfruttare i vantaggi di una maggiore capacità e scalabilità.

Se viene visualizzato il messaggio di errore "Le condivisioni file di grandi dimensioni non sono ancora disponibili per l'account", l'area potrebbe trovarsi nel mezzo del completamento dell'implementazione. Contattare il supporto tecnico se si ha un'urgenza di condivisioni di file di grandi dimensioni.

### <a name="cli"></a>CLI

Per abilitare condivisioni file di grandi dimensioni nell'account esistente, utilizzare il comando seguente. Sostituire `<yourStorageAccountName>` `<yourResourceGroup>` e con le informazioni.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Per abilitare condivisioni file di grandi dimensioni nell'account esistente, utilizzare il comando seguente. Sostituire `<yourStorageAccountName>` `<yourResourceGroup>` e con le informazioni.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Creare una condivisione file di grandi dimensioniCreate a large file share

Dopo aver abilitato condivisioni file di grandi dimensioni nell'account di archiviazione, è possibile creare condivisioni file in tale account con quote più elevate. 

### <a name="portal"></a>Portale

La creazione di una condivisione file di grandi dimensioni è quasi identica alla creazione di una condivisione file standard. La differenza principale è che è possibile impostare una quota fino a 100 TiB.

1. Nell'account di archiviazione selezionare **Condivisioni file**.
1. Selezionare **: Condivisione file**.
1. Immettere un nome per la condivisione file. Puoi anche impostare la dimensione della quota che desideri, fino a 100 TiB. Quindi selezionare **Crea**. 

![Interfaccia utente del portale di Azure con le caselle Nome e Quota](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

Per creare una condivisione file di grandi dimensioni, utilizzare il comando seguente. Sostituire `<yourStorageAccountName>` `<yourStorageAccountKey>`, `<yourFileShareName>` e con le informazioni.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Per creare una condivisione file di grandi dimensioni, utilizzare il comando seguente. Sostituire `<YourStorageAccountName>` `<YourStorageAccountKey>`, `<YourStorageAccountFileShareName>` e con le informazioni.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Espandere le condivisioni file esistenti

Dopo aver abilitato condivisioni file di grandi dimensioni nell'account di archiviazione, è anche possibile espandere le condivisioni file esistenti in tale account alla quota superiore. 

### <a name="portal"></a>Portale

1. Nell'account di archiviazione selezionare **Condivisioni file**.
1. Fare clic con il pulsante destro del mouse sulla condivisione file e quindi scegliere **Quota**.
1. Immettere la nuova dimensione desiderata e quindi scegliere **OK**.

![L'interfaccia utente del portale di Azure con la quota delle condivisioni file esistentiThe Azure portal UI with Quota of existing file shares](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

Per impostare la quota sulla dimensione massima, utilizzare il comando seguente. Sostituire `<yourStorageAccountName>` `<yourStorageAccountKey>`, `<yourFileShareName>` e con le informazioni.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Per impostare la quota sulla dimensione massima, utilizzare il comando seguente. Sostituire `<YourStorageAccountName>` `<YourStorageAccountKey>`, `<YourStorageAccountFileShareName>` e con le informazioni.

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
* [Connettere e montare una condivisione file su LinuxConnect and mount a file share on Linux](storage-how-to-use-files-linux.md)
* [Connettere e montare una condivisione file in macOSConnect and mount a file share on macOS](storage-how-to-use-files-mac.md)
