---
title: Script di Azure PowerShell di esempio
description: Esempi che illustrano come usare il front-end tramite script di PowerShell
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: 831f09ecf7550a847c483fbe1678f1e4c3cecb61
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052283"
---
# <a name="example-powershell-scripts"></a>Script di Azure PowerShell di esempio

Rendering remoto di Azure include le due API REST seguenti:

- [API REST di conversione](../how-tos/conversion/conversion-rest-api.md)
- [API REST di sessione](../how-tos/session-rest-api.md)

Il [repository di esempi di Rendering remoto di Azure](https://github.com/Azure/azure-remote-rendering) contiene script di esempio nella cartella *Scripts* per interagire con le API REST del servizio. Questo articolo ne descrive l'utilizzo.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire gli script di esempio, è necessario procedere a un'installazione funzionale di [Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

1. Installare Azure PowerShell:
    1. Aprire una sessione di PowerShell con diritti di amministratore
    1. Eseguire: `Install-Module -Name Az -AllowClobber`

1. Se si ricevono errori relativi all'esecuzione di script, assicurarsi che i [criteri di esecuzione](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) siano impostati nel modo appropriato:
    1. Aprire una sessione di PowerShell con diritti di amministratore
    1. Eseguire: `Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [Preparare un account di archiviazione di Azure](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Accedere alla sottoscrizione contenente l'account di Rendering remoto di Azure:
    1. Aprire una sessione di PowerShell
    1. Eseguire `Connect-AzAccount` e seguire le istruzioni visualizzate.

> [!NOTE]
> Se l'organizzazione ha più di una sottoscrizione, può essere necessario specificare gli argomenti SubscriptionId e Tenant. Per i dettagli, vedere la [documentazione di Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).

1. Scaricare la cartella *Scripts* dal [repository GithHub Azure di Rendering remoto di Azure](https://github.com/Azure/azure-remote-rendering).

## <a name="configuration-file"></a>File di configurazione

Oltre ai file `.ps1`, è presente un file `arrconfig.json` che è necessario compilare:

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> Assicurarsi di inserire correttamente le barre rovesciate di escape nel percorso LocalAssetDirectoryPath usando due barre rovesciate, "\\\\", e le barre "/" in tutti gli altri percorsi, come inputFolderPath e inputAssetPath.

> [!CAUTION]
> È necessario specificare i valori facoltativi oppure rimuovere del tutto la chiave e il valore. Ad esempio, se non si usa il parametro `"outputAssetFileName"`, è necessario eliminare l'intera riga all'interno di `arrconfig.json`.

### <a name="accountsettings"></a>accountSettings

Per `arrAccountId` e `arrAccountKey`, vedere [Creare un account di Rendering remoto di Azure](../how-tos/create-an-account.md).
Per `region` vedere l'[elenco di aree disponibili](../reference/regions.md).

### <a name="renderingsessionsettings"></a>renderingSessionSettings

Questa struttura deve essere completata se si vuole eseguire **RenderingSession.ps1**.

- **vmSize:** selezionare le dimensioni della macchina virtuale, *Standard* o *Premium*. Arrestare le sessioni di rendering quando non sono più necessarie.
- **maxLeaseTime:** la durata desiderata del lease della VM. Verrà arrestata alla scadenza del lease. La durata del lease può essere prolungata in seguito (vedere più avanti).

### <a name="assetconversionsettings"></a>assetConversionSettings

Questa struttura deve essere completata se si vuole eseguire **Conversion.ps1**.

Per i dettagli, vedere [Preparare un account di archiviazione di Azure](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts).

## <a name="script-renderingsessionps1"></a>Script: RenderingSession. ps1

Questo script consente di creare, sottoporre a query e arrestare le sessioni di rendering.

> [!IMPORTANT]
> Assicurarsi di aver completato le sezioni *accountSettings* e *renderingSessionSettings* del file arrconfig.json.

### <a name="create-a-rendering-session"></a>Creare una sessione di rendering

Utilizzo normale con un file arrconfig.json completamente compilato:

```PowerShell
.\RenderingSession.ps1
```

Lo script chiamerà l'[API REST di gestione della sessione](../how-tos/session-rest-api.md) per avviare una macchina virtuale di rendering con le impostazioni specificate. Se l'operazione riesce, verrà recuperato il valore di *sessionId*. Verrà quindi eseguito il polling delle proprietà della sessione fino a quando la sessione non è pronta o si verifica un errore.

Per usare un file di **configurazione alternativo**:

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

È possibile eseguire l'**override di singole impostazioni** del file di configurazione:

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

Per limitarsi ad **avviare una sessione senza polling**, è possibile usare:

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

Il valore di *sessionId* recuperato dallo script deve essere passato alla maggior parte degli altri comandi della sessione.

### <a name="retrieve-session-properties"></a>Recuperare le proprietà della sessione

Per ottenere le proprietà di una sessione, eseguire:

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

Usare `-Poll` per restare in attesa fino a quando la sessione non è *pronta* o si verifica un errore.

### <a name="list-active-sessions"></a>Elencare le sessioni attive

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>Arrestare una sessione

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>Cambiare le proprietà della sessione

Al momento, è supportata solo la modifica del valore maxLeaseTime di una sessione.

> [!NOTE]
> La durata del lease viene sempre conteggiata dal momento in cui viene inizialmente creata la VM della sessione. Quindi, per estendere il lease della sessione di un'altra ora, aumentare il valore di *maxLeaseTime* di un'ora.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>Script: Conversion.ps1

Questo script consente di convertire i modelli di input nel formato di runtime specifico di Rendering remoto di Azure.

> [!IMPORTANT]
> Assicurarsi di aver completato le sezioni *accountSettings* e *assetConversionSettings* del file arrconfig.json.

Lo script illustra le due opzioni disponibili per usare gli account di archiviazione con il servizio:

- Account di archiviazione collegato all'account di Rendering remoto di Azure
- Accesso all'archiviazione tramite firme di accesso condiviso

### <a name="linked-storage-account"></a>Account di archiviazione collegato

Dopo aver compilato completamente il file arrconfig.json e collegato un account di archiviazione, è possibile usare il comando seguente. Il collegamento dell'account di archiviazione viene descritto in [Creare un account](../how-tos/create-an-account.md#link-storage-accounts).

L'uso di un account di archiviazione collegato è il modo consigliato per usare il servizio di conversione, perché non è necessario generare firme di accesso condiviso.

```PowerShell
.\Conversion.ps1
```

1. Tutti i file contenuti in `assetConversionSettings.modelLocation` vengono caricati nel contenitore BLOB di input nel percorso `inputFolderPath` specificato
1. Viene chiamata l'[API REST di conversione di modelli](../how-tos/conversion/conversion-rest-api.md) per avviare la [conversione del modello](../how-tos/conversion/model-conversion.md)
1. Viene eseguito il polling dello stato della conversione finché l'operazione non riesce o si verifica un errore
1. Vengono restituiti i dettagli della posizione del file convertito (account di archiviazione, contenitore di output, percorso del file nel contenitore)

### <a name="access-to-storage-via-shared-access-signatures"></a>Accedere all'archiviazione tramite firme di accesso condiviso

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

In tal modo, si verificheranno i seguenti eventi:

1. Il file locale viene caricato da `assetConversionSettings.localAssetDirectoryPath` al contenitore BLOB di input
1. Viene generato un URI di firma di accesso condiviso per il contenitore di input
1. Viene generato un URI di firma di accesso condiviso per il contenitore di output
1. Viene chiamata l'[API REST di conversione di modelli](../how-tos/conversion/conversion-rest-api.md) per avviare la [conversione del modello](../how-tos/conversion/model-conversion.md)
1. Viene eseguito il polling dello stato della conversione finché l'operazione non riesce o si verifica un errore
1. Vengono restituiti i dettagli della posizione del file convertito (account di archiviazione, contenitore di output, percorso del file nel contenitore)
1. Viene restituito un URI di firma di accesso condiviso del modello convertito nel contenitore BLOB di output

### <a name="additional-command-line-options"></a>Altre opzioni della riga di comando

Per usare un file di **configurazione alternativo**:

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Per limitarsi ad **avviare la conversione del modello senza polling**, è possibile usare:

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

È possibile **eseguire l'override delle singole impostazioni** del file di configurazione usando le opzioni della riga di comando seguenti:

* **Id:** ConversionId usato con GetConversionStatus
* **ArrAccountId:** arrAccountId di accountSettings
* **ArrAccountKey:** override per arrAccountKey di accountSettings
* **Region:** override per region di accountSettings
* **ResourceGroup:** override per resourceGroup di assetConversionSettings
* **StorageAccountName:** override per storageAccountName di assetConversionSettings
* **BlobInputContainerName:** override per blobInputContainer di assetConversionSettings
* **LocalAssetDirectoryPath:** override per localAssetDirectoryPath di assetConversionSettings
* **InputAssetPath:** override per inputAssetPath di assetConversionSettings
* **BlobOutputContainerName:** override per blobOutputContainerName di assetConversionSettings
* **OutputFolderPath:** override per outputFolderPath di assetConversionSettings
* **OutputAssetFileName:** override per outputAssetFileName di assetConversionSettings

È ad esempio possibile combinare una serie di opzioni specifiche come segue:

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>Eseguire le singole fasi di conversione

Se si vogliono eseguire singoli passaggi del processo, è possibile usare:

Caricare solo i dati dal percorso LocalAssetDirectoryPath specificato

```PowerShell
.\Conversion.ps1 -Upload
```

Avviare solo il processo di conversione di un modello già caricato nell'archiviazione BLOB (senza eseguire il caricamento, né il polling dello stato di conversione). Lo script restituisce *conversionId*.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

È inoltre possibile recuperare lo stato di questa conversione usando:

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

Usare `-Poll` per restare in attesa fino a quando la conversione non viene eseguita o si verifica un errore.

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Eseguire il rendering di un modello con Unity](../quickstarts/render-model.md)
- [Avvio rapido: Convertire un modello per il rendering](../quickstarts/convert-model.md)
- [Conversione di modelli](../how-tos/conversion/model-conversion.md)
