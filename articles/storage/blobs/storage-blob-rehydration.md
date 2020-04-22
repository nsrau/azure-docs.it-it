---
title: Reidratare i dati BLOB dal livello di archiviazioneRehydrate blob data from the archive tier
description: Reidratare i BLOB dall'archivio in modo da poter accedere ai dati.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 82ea4ad23e3207f5641ade196f69595cd1e7b323
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684078"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Reidratare i dati BLOB dal livello di archiviazioneRehydrate blob data from the archive tier

Mentre un BLOB si trova nel livello di accesso all'archivio, viene considerato offline e non può essere letto o modificato. I metadati del BLOB rimangono online e disponibili, consentendo di elencare il BLOB e le relative proprietà. La lettura e la modifica dei dati BLOB sono disponibili solo con i livelli online, ad esempio hot o cool. Sono disponibili due opzioni per recuperare e accedere ai dati archiviati nel livello di accesso all'archivio.

1. [Reidratare un BLOB archiviato in un livello online:](#rehydrate-an-archived-blob-to-an-online-tier) reidratare un BLOB di archiviazione in caldo o freddo modificandone il livello tramite l'operazione [Imposta livello BLOB.](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)
2. [Copiare un BLOB archiviato in un livello online:](#copy-an-archived-blob-to-an-online-tier) creare una nuova copia di un BLOB di archiviazione usando l'operazione [Copia BLOB.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) Specificare un nome di BLOB diverso e un livello di destinazione con un valore hot o cool.

 Per altre informazioni sui livelli, vedere Archiviazione BLOB di Azure: livelli di [accesso a caldo, ad accesso a freddo e archiviazione.](storage-blob-storage-tiers.md)

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Reidratare un BLOB archiviato in un livello onlineRehydrate an archived blob to an online tier

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Copiare un BLOB archiviato in un livello online

Se non si vuole reidratare il BLOB di archivio, è possibile scegliere di eseguire un'operazione [di copia BLOB.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) Il BLOB originale rimarrà invariato nell'archivio mentre viene creato un nuovo BLOB nel livello a caldo o freddo online su cui lavorare. Nell'operazione Copia BLOB è inoltre possibile impostare la proprietà facoltativa *x-ms-rehydrate-priority* su Standard o High per specificare la priorità con cui si vuole creare la copia BLOB.

Il completamento della copia di un BLOB dall'archivio può richiedere ore a seconda della priorità di reidrato selezionata. Dietro le quinte, l'operazione **Copia BLOB** legge il BLOB di origine dell'archivio per creare un nuovo BLOB online nel livello di destinazione selezionato. Il nuovo BLOB potrebbe essere visibile quando si elencano i BLOB, ma i dati non sono disponibili fino al completamento della lettura dal BLOB di archiviazione di origine e fino a quando non viene scritta la lettura dal BLOB di archiviazione di origine e i dati vengono scritti nel nuovo BLOB di destinazione online. Il nuovo BLOB è come copia indipendente e qualsiasi modifica o eliminazione non influisce sul BLOB di archivio di origine.

I BLOB di archiviazione possono essere copiati solo nei livelli di destinazione online all'interno dello stesso account di archiviazione. La copia di un BLOB di archivio in un altro BLOB di archivio non è supportata. Nella tabella seguente vengono indicate le funzionalità di CopyBlob.The following table indicates CopyBlob's capabilities.

|                                           | **Origine livello a caldoHot tier source**   | **Fonte del livello Cool** | **Origine livello di archiviazione**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Destinazione a livello caldo**                  | Supportato             | Supportato            | Supportato all'interno dello stesso account; reidratarsi in sospeso               |
| **Destinazione livello Cool**                 | Supportato             | Supportato            | Supportato all'interno dello stesso account; reidratarsi in sospeso               |
| **Destinazione livello di archiviazione**              | Supportato             | Supportato            | Non supportato         |

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

I BLOB di reidratazione non archiviati in livelli a caldo o freddi vengono addebitati come operazioni di lettura e recupero dei dati. L'utilizzo di Priorità alta prevede costi di funzionamento e recupero dati più elevati rispetto alla priorità standard. La reidratazione ad alta priorità viene visualizzata come un elemento pubblicitario separato sulla fattura. Se una richiesta con priorità alta per restituire un BLOB di archivio di pochi gigabyte richiede più di 5 ore, non verrà addebitata la velocità di recupero con priorità alta. Tuttavia, i tassi di recupero standard si applicano ancora quando la reidratazione è stata classificata in priorità rispetto ad altre richieste.

La copia dei BLOB dall'archivio in livelli hot o cool vengono addebitati come operazioni di lettura e recupero dei dati. Un'operazione di scrittura viene addebitata per la creazione della nuova copia BLOB. Le tariffe di eliminazione anticipata non si applicano quando si copia in un BLOB online perché il BLOB di origine rimane non modificato nel livello di archiviazione. Se selezionato, vengono applicati costi di recupero ad alta priorità.

I BLOB nel livello di archiviazione devono essere archiviati per un minimo di 180 giorni. L'eliminazione o la reidratazione dei BLOB archiviati prima di 180 giorni comporterà costi di eliminazione anticipata.

> [!NOTE]
> Per altre informazioni sui prezzi per i BLOB in blocchi e la reidratazione dei dati, vedere Prezzi di Archiviazione di Azure.For more information about pricing for block blobs and data rehydration, see [Azure Storage Pricing.](https://azure.microsoft.com/pricing/details/storage/blobs/) Per ulteriori informazioni sugli addebiti per il trasferimento dei dati in uscita, vedere [Dettagli sui prezzi dei trasferimenti](https://azure.microsoft.com/pricing/details/data-transfers/)di dati .

## <a name="quickstart-scenarios"></a>Scenari introduttivi

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Reidratare un BLOB di archiviazione a un livello onlineRehydrate an archive blob to an online tier
# <a name="portal"></a>[Portale](#tab/azure-portal)
1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel portale di Azure cercare e selezionare **Tutte le risorse**.

1. Selezionare l'account di archiviazione.

1. Selezionare il contenitore e quindi selezionare il BLOB.

1. In **Proprietà BLOB**selezionare **Cambia livello**.

1. Selezionare il livello di accesso **Caldo** o **Freddo.** 

1. Selezionare una priorità **di** reidratazione standard o **alta**.

1. Seleziona **Salva** in basso.

![Modificare il](media/storage-tiers/blob-access-tier.png)
![livello dell'account di archiviazione Controllare lo stato di reidratoChange storage account tier Check rehydrate status](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Lo script di PowerShell seguente può essere usato per modificare il livello BLOB di un BLOB di archiviazione. La `$rgName` variabile deve essere inizializzata con il nome del gruppo di risorse. La `$accountName` variabile deve essere inizializzata con il nome dell'account di archiviazione. La `$containerName` variabile deve essere inizializzata con il nome del contenitore. La `$blobName` variabile deve essere inizializzata con il nome del BLOB. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", “Standard”)
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Copiare un BLOB di archiviazione in un nuovo BLOB con un livello onlineCopy an archive blob to a new blob with an online tier
Lo script di PowerShell seguente può essere usato per copiare un BLOB di archiviazione in un nuovo BLOB all'interno dello stesso account di archiviazione. La `$rgName` variabile deve essere inizializzata con il nome del gruppo di risorse. La `$accountName` variabile deve essere inizializzata con il nome dell'account di archiviazione. Le `$srcContainerName` `$destContainerName` variabili e devono essere inizializzate con i nomi dei contenitori. Le `$srcBlobName` `$destBlobName` variabili e devono essere inizializzate con i nomi blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sui livelli di archiviazione BLOBLearn about Blob Storage Tiers](storage-blob-storage-tiers.md)
* [Controllare i prezzi di accesso frequente, accesso sporadico e archivio negli account di archiviazione BLOB e per utilizzo generico v2 in base all'area](https://azure.microsoft.com/pricing/details/storage/)
* [Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)
* [Verificare i prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/)
