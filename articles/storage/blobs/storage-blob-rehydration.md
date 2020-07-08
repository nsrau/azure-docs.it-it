---
title: Riattivare i dati BLOB dal livello archivio
description: Riattivare i BLOB dalla risorsa di archiviazione per poter accedere ai dati.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: cbaa1d34eb8fe44b1c367d8fa3f84687fe7568e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84433188"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Riattivare i dati BLOB dal livello archivio

Quando un BLOB si trova nel livello di accesso archivio, viene considerato offline e non può essere letto o modificato. I metadati del BLOB rimangono tuttavia online e disponibili ed è quindi possibile visualizzare il BLOB e le relative proprietà. La lettura e la modifica dei dati BLOB sono consentite solo con livelli online, come i livelli di archiviazione di accesso frequente o sporadico. Sono disponibili due opzioni per recuperare e accedere ai dati archiviati nel livello di accesso archivio.

1. [Riattivare un BLOB archiviato in un livello online](#rehydrate-an-archived-blob-to-an-online-tier) - Riattivare un BLOB dal livello archivio al livello di accesso frequente o sporadico modificandone il livello tramite l'operazione [Impostare il livello di BLOB](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier).
2. [Copiare un BLOB archiviato in un livello online](#copy-an-archived-blob-to-an-online-tier) - Creare una nuova copia di un BLOB di archivio usando l'operazione [Copia BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob). Specificare un nome di BLOB diverso e un livello di destinazione di accesso frequente o sporadico.

 Per altre informazioni sui livelli di accesso, vedere [Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Riattivare un BLOB archiviato in un livello online

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Copiare un BLOB archiviato in un livello online

Se non si vuole riattivare il BLOB di archivio, è possibile scegliere di eseguire l'operazione [Copia BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob). Il BLOB originale rimarrà invariato nell'archivio, mentre nel livello di accesso frequente o sporadico verrà creato un nuovo BLOB su cui poter operare. Nell'operazione di copia del BLOB è possibile anche impostare la proprietà facoltativa *x-ms-rehydrate-priority* su Standard o Alta per specificare la priorità in base alla quale si vuole creare la copia BLOB.

Per completare la copia di un BLOB dall'archivio possono essere necessarie alcune ore, a seconda della priorità di riattivazione selezionata. Dietro le quinte, l'operazione **Copia BLOB** legge il BLOB di origine dell'archivio per creare un nuovo BLOB online nel livello di destinazione selezionato. È possibile che il nuovo BLOB sia visibile quando si elencano i BLOB, ma i dati non risultano disponibili finché non viene completata la lettura dal BLOB di archiviazione di origine e la scrittura dei dati nel nuovo BLOB di destinazione online. Il nuovo BLOB è una copia indipendente e, pertanto, qualsiasi modifica o eliminazione apportata non viene riprodotta nel BLOB di archiviazione di origine.

> [!IMPORTANT]
> Non eliminare il BLOB di origine finché la copia non risulta correttamente completata nella destinazione. Se il BLOB di origine viene eliminato, è possibile che la copia del BLOB di destinazione non venga completata e che la copia risulti quindi vuota. È possibile controllare l'impostazione *x-ms-copy-status* per determinare lo stato dell'operazione di copia.

I BLOB di archiviazione possono essere copiati solo nei livelli di destinazione online che si trovano all'interno dello stesso account di archiviazione. Non è supportata la copia di un BLOB di archiviazione in un altro BLOB di archiviazione. La tabella seguente indica le opzioini di CopyBlob.

|                                           | **Origine nel livello di accesso frequente**   | **Origine nel livello di accesso sporadico** | **Origine nel livello archivio**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Destinazione nel livello di accesso frequente**                  | Supportato             | Supportato            | Supportato nello stesso account; riattivazione in sospeso               |
| **Destinazione nel livello di accesso sporadico**                 | Supportato             | Supportato            | Supportato nello stesso account; riattivazione in sospeso               |
| **Destinazione nel livello archivio**              | Supportato             | Supportato            | Non supportato         |

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

La riattivazione di un BLOB dal livello archivio al livelli di accesso frequente o sporadico viene addebitata come se si trattasse di operazioni di lettura e di recupero dati. La priorità Alta prevede costi di funzionamento e di recupero dati più elevati rispetto alla priorità Standard. In fattura, la riattivazione con priorità Alta viene visualizzata come una voce separata. Se una richiesta con priorità Alta di restituzione di un BLOB di archiviazione di pochi gigabyte richiede più di 5 ore, non verrà addebitata la tariffa per il recupero con priorità Alta. Verranno tuttavia applicate le tariffe di recupero Standard poiché alla riattivazione è stata assegnata la priorità rispetto ad altre richieste.

La copia di un BLOB dal livello archivio al livelli di accesso frequente o sporadico viene addebitata come se si trattasse di operazioni di lettura e di recupero dati. Per la creazione della nuova copia BLOB viene addebitata un'operazione di scrittura. Non vengono applicate le tariffe di eliminazione anticipata quando si esegue la copia in un BLOB online, perché il BLOB di origine rimane invariato nel livello archivio. Vengono invece applicate le tariffe di recupero con priorità Alta se viene selezionata questa opzione.

I BLOB che si trovano nel livello archivio devono rimanere archiviati per almeno 180 giorni. Se vengono eliminati o riattivati prima di 180 giorni, viene applicata la tariffa di eliminazione anticipata.

> [!NOTE]
> Per altre informazioni sui prezzi per la riattivazione dei dati o dei BLOB in bocchi, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Per altre informazioni sugli addebiti per i trasferimenti dati in uscita, vedere [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Scenari introduttivi

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Riattivare un BLOB di archivio in un livello online
# <a name="portal"></a>[Portale](#tab/azure-portal)
1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel portale di Azure cercare e selezionare **Tutte le risorse**.

1. Selezionare l'account di archiviazione.

1. Selezionare il contenitore e quindi il BLOB.

1. In **proprietà BLOB** selezionare **Modifica livello**.

1. Selezionare il livello di **accesso frequente** o di **accesso sporadico**. 

1. Selezionare una priorità di riattivazione scegliendo tra **Standard** e **Alta**.

1. Nella parte inferiore selezionare **Salva**.

![Modificare il livello dell'account di archiviazione](media/storage-tiers/blob-access-tier.png)
![Verificare lo stato di riattivazione](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
È possibile usare lo script di PowerShell seguente per modificare il livello di un BLOB di archiviazione. La variabile `$rgName` deve essere inizializzata con il nome del gruppo di risorse. La variabile `$accountName` deve essere inizializzata con il nome dell'account di archiviazione. La variabile `$containerName` deve essere inizializzata con il nome del contenitore. La variabile `$blobName` deve essere inizializzata con il nome del BLOB. 
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
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", "Standard")
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Copiare un BLOB di archivio in un nuovo BLOB con un livello online
È possibile usare lo script di PowerShell seguente per copiare un BLOB di archivio in un nuovo BLOB all'interno dello stesso account di archiviazione. La variabile `$rgName` deve essere inizializzata con il nome del gruppo di risorse. La variabile `$accountName` deve essere inizializzata con il nome dell'account di archiviazione. Le variabili `$srcContainerName` e `$destContainerName` devono essere inizializzate con il nome del contenitore. Le variabili `$srcBlobName` e `$destBlobName` devono essere inizializzate con i nomi dei BLOB. 
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

* [Informazioni sui livelli di archiviazione BLOB](storage-blob-storage-tiers.md)
* [Controllare i prezzi di accesso frequente, accesso sporadico e archivio negli account di archiviazione BLOB e per utilizzo generico v2 in base all'area](https://azure.microsoft.com/pricing/details/storage/)
* [Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)
* [Verificare i prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/)
