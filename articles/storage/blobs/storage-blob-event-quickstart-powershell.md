---
title: Indirizzare gli eventi di archiviazione BLOB di Azure a un endpoint Web personalizzato - Powershell | Microsoft Docs
description: Usare la Griglia di eventi di Azure per sottoscrivere eventi di archiviazione BLOB.
services: storage,event-grid
keywords: ''
author: david-stanford
ms.author: dastanfo
ms.date: 07/05/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: c2db67738dddbc9e20eb8fe9d2a30c7a26bf07cd
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068921"
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-powershell"></a>Indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato con PowerShell

La griglia di eventi di Azure è un servizio di gestione degli eventi per il cloud. Questo articolo illustra come usare Azure PowerShell per sottoscrivere eventi di archiviazione BLOB, attivare un evento e visualizzare il risultato. 

In genere, si inviano eventi a un endpoint che elabora i dati dell'evento e intraprende azioni. Per maggiore semplicità, tuttavia, in questo articolo gli eventi vengono inviati a un'app Web che raccoglie e visualizza i messaggi.

Al termine, i dati degli eventi saranno stati inviati all'app Web.

![Visualizzare i risultati](./media/storage-blob-event-quickstart-powershell/view-results.png)

## <a name="setup"></a>Configurazione

Ai fini di questo articolo, è necessario eseguire la versione più recente di Azure PowerShell. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Connect-AzureRmAccount` e seguire le istruzioni visualizzate per eseguire l'autenticazione.

```powershell
Connect-AzureRmAccount
```

> [!NOTE]
> La disponibilità degli eventi di archiviazione è legata alla [disponibilità](../../event-grid/overview.md) di Griglia di eventi. Gli eventi di archiviazione saranno disponibili nelle aree geografiche in cui si renderà disponibile Griglia di eventi.

Questo esempio usa **westus2** e archivia la selezione in una variabile che verrà usata in tutta la procedura.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Gli argomenti della griglia di eventi sono risorse di Azure e devono essere inseriti in un gruppo di risorse di Azure. Un gruppo di risorse è una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

L'esempio seguente crea un gruppo di risorse denominato **gridResourceGroup** nella località **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Per usare eventi di archiviazione BLOB, è necessario un [account di archiviazione BLOB](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) o un [account di archiviazione per utilizzo generico versione 2](../common/storage-account-options.md#general-purpose-v2-accounts). Gli account di archiviazione per **utilizzo generico versione 2** supportano tutte le funzionalità di tutti i servizi di archiviazione, inclusi quelli relativi a BLOB, file, code e tabelle. Gli **account di archiviazione BLOB** sono account specializzati per l'archiviazione di dati non strutturati come BLOB (oggetti) in Archiviazione di Azure. Gli account di archiviazione BLOB sono simili agli account di archiviazione di uso generico e includono tutte le straordinarie caratteristiche di durabilità, disponibilità, scalabilità e prestazioni che si usano già normalmente, inclusa la coerenza API al 100% per i BLOB in blocchi e i BLOB di aggiunta. Per applicazioni che richiedono solo archivi BLOB in blocchi o BLOB di aggiunta, è consigliabile usare account di archiviazione BLOB.  

Creare un account di archiviazione BLOB con replica dell'archiviazione con ridondanza locale tramite [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) e quindi recuperare il contesto che definisce l'account di archiviazione da usare. Quando si usa un account di archiviazione, si può fare riferimento al contesto anziché fornire ripetutamente le credenziali. Questo esempio crea un account di archiviazione denominato **gridstorage** con archiviazione con ridondanza locale. 

> [!NOTE]
> I nomi degli account di archiviazione sono inclusi in uno spazio dei nomi globale ed è quindi necessario aggiungere alcuni caratteri casuali al nome specificato in questo script.

```powershell
$storageName = "gridstorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

$ctx = $storageAccount.Context
```

## <a name="create-a-message-endpoint"></a>Creare un endpoint del messaggio

Prima di sottoscrivere l'argomento, creare l'endpoint per il messaggio dell'evento. L'endpoint richiede in genere azioni basate sui dati degli eventi. Per semplificare questa guida introduttiva, si distribuisce un'[app Web preesistente](https://github.com/dbarkol/azure-event-grid-viewer) che visualizza i messaggi di evento. La soluzione distribuita include un piano di servizio app, un'app Web del servizio app e codice sorgente da GitHub.

Sostituire `<your-site-name>` con un nome univoco per l'app Web. Il nome dell'app Web deve essere univoco perché fa parte della voce DNS.

```powershell
$sitename="<your-site-name>"

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/dbarkol/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

Per il completamento della distribuzione possono essere necessari alcuni minuti. Dopo il completamento della distribuzione, visualizzare l'app Web per assicurarsi che sia in esecuzione. In un Web browser passare a: `https://<your-site-name>.azurewebsites.net`

Il sito dovrebbe essere visibile senza messaggi attualmente visualizzati.

[!INCLUDE [event-grid-register-provider-powershell.md](../../../includes/event-grid-register-provider-powershell.md)]

## <a name="subscribe-to-your-storage-account"></a>Sottoscrivere l'account di archiviazione

Si sottoscrive un argomento per indicare alla griglia di eventi gli eventi di cui si vuole tenere traccia. L'esempio seguente sottoscrive l'account di archiviazione creato e passa l'URL dell'app Web come endpoint per la notifica degli eventi. L'endpoint per l'app Web deve includere il suffisso `/api/updates/`.

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzureRmEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $endpoint `
  -ResourceId $storageId
```

Visualizzare nuovamente l'app Web e notare che all'app è stato inviato un evento di convalida della sottoscrizione. Selezionare l'icona a forma di occhio per espandere i dati dell'evento. Griglia di eventi invia l'evento di convalida in modo che l'endpoint possa verificare che voglia ricevere i dati dell'evento. L'app Web include il codice per convalidare la sottoscrizione.

![Visualizzare l'evento della sottoscrizione](./media/storage-blob-event-quickstart-powershell/view-subscription-event.png)

## <a name="trigger-an-event-from-blob-storage"></a>Attivare un evento dall'archiviazione BLOB

A questo punto, attivare un evento per vedere come la griglia di eventi distribuisce il messaggio nell'endpoint. È prima necessario creare un contenitore e un oggetto. Successivamente, l'oggetto viene caricato nel contenitore.

```powershell
$containerName = "gridcontainer"
New-AzureStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzureStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

È stato attivato l'evento e la griglia di eventi ha inviato il messaggio all'endpoint configurato al momento della sottoscrizione. Visualizzare l'app Web per vedere l'evento appena inviato.

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/gridcontainer/blobs/gridTestFile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "Azure-Storage-PowerShell-d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "application/octet-stream",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/gridcontainer/gridTestFile.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Pulire le risorse
Se si intende continuare a usare questo account di archiviazione e questa sottoscrizione di eventi, non è necessario pulire le risorse create in questo articolo. Se non si intende continuare, usare il comando seguente per eliminare le risorse create con questo articolo.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare argomenti e sottoscrizioni di eventi, è possibile approfondire le operazioni possibili con gli eventi di archiviazione BLOB e con la Griglia di eventi:

- [Reazione agli eventi di archiviazione BLOB](storage-blob-event-overview.md)
- [Informazioni sulla griglia di eventi](../../event-grid/overview.md)
