---
title: Eliminazione temporanea per i BLOB di Archiviazione di Azure (anteprima) | Microsoft Docs
description: Archiviazione di Azure offre ora l'eliminazione temporanea (anteprima) per gli oggetti BLOB, grazie alla quale è possibile ripristinare più facilmente i dati nel caso in cui vengano erroneamente modificati o eliminati da un'applicazione o da un utente con un altro account di archiviazione.
services: storage
author: MichaelHauss
manager: vamshik
ms.service: storage
ms.topic: article
ms.date: 05/31/2018
ms.author: mihauss
ms.openlocfilehash: 93b60f8957a6ae225dbc5beb33a7de817ffc5bc2
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34701684"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Eliminazione temporanea per i BLOB di Archiviazione di Azure

## <a name="overview"></a>Panoramica

Archiviazione di Azure offre ora l'eliminazione temporanea per gli oggetti BLOB, per consentire di ripristinare più facilmente i dati nel caso in cui vengano erroneamente modificati o eliminati da un'applicazione o da un utente con un altro account di archiviazione.

## <a name="how-does-it-work"></a>Come funziona?

Se abilitata, l'eliminazione temporanea consente di salvare e ripristinare i dati quando si eliminano BLOB o snapshot di BLOB. Questa protezione si estende ai dati dei BLOB cancellati a seguito di un sovrascrittura.

Quando vengono eliminati, i dati passano in uno stato di eliminazione temporanea anziché essere cancellati in modo definitivo. Se l'eliminazione temporanea è abilitata e si sovrascrivono dei dati, viene generato uno snapshot eliminato temporaneamente in modo da salvare lo stato dei dati sovrascritti. Gli oggetti eliminati temporaneamente sono invisibili, a meno che non vengano elencati in modo esplicito.
È possibile configurare per quanto tempo i dati eliminati temporaneamente restano recuperabili prima che scadano definitivamente.

L'eliminazione temporanea è compatibile con le versioni precedenti e non è quindi necessario apportare modifiche alle applicazioni per usufruire dei meccanismi di protezione offerti da questa funzionalità. Il [ripristino dei dati](#recovery) introduce tuttavia una nuova API **Undelete Blob**.

### <a name="configuration-settings"></a>Impostazioni di configurazione

Quando si crea un nuovo account, l'eliminazione temporanea è disabilitata per impostazione predefinita. L'eliminazione temporanea è disabilitata per impostazione predefinita anche per gli account di archiviazione esistenti. È possibile abilitare e disabilitare questa funzionalità in qualsiasi momento durante il ciclo di vita di un account di archiviazione.

Se la funzionalità viene disabilitata, è comunque possibile accedere e ripristinare i dati eliminati temporaneamente, purché tali dati siano stati salvati quando la funzionalità era in precedenza abilitata. Quando si abilita l'eliminazione temporanea, è necessario anche configurare il periodo di conservazione.

Il periodo di conservazione indica per quanto tempo i dati eliminati temporaneamente restano memorizzati e disponibili per il ripristino. Per i BLOB e gli snapshot dei BLOB eliminati in modo esplicito, il calcolo del periodo di conservazione inizia nel momento in cui i dati vengono eliminati. Per gli snapshot eliminati temporaneamente generati in caso di sovrascrittura dei dati dalla funzionalità di eliminazione temporanea, il calcolo del tempo inizia nel momento in cui viene generato lo snapshot. Attualmente i dati eliminati temporaneamente possono essere conservati per un periodo compreso tra 1 e 365 giorni.

È possibile modificare il periodo di conservazione dell'eliminazione temporanea in qualsiasi momento. Il periodo di conservazione aggiornato è valido solo per i nuovi dati eliminati. In dati eliminati in precedenza scadono in base al periodo di conservazione che era configurato al momento dell'eliminazione dei dati. Il tentativo di eliminare un oggetto con eliminazione temporanea non influisce sull'ora di scadenza dell'oggetto stesso.

### <a name="saving-deleted-data"></a>Salvataggio dei dati eliminati

L'eliminazione temporanea preserva i dati in molte situazioni in cui i BLOB o gli snapshot dei BLOB vengono eliminati o sovrascritti.

Quando un BLOB viene sovrascritto tramite **Put Blob**, **Put Block**, **Put Block List** o **Copy Blob**, viene generato automaticamente uno snapshot dello stato del BLOB prima dell'operazione di scrittura. Lo snapshot è uno snapshot eliminato temporaneamente ed è invisibile a meno che gli oggetti eliminati temporaneamente non vengano elencati in modo esplicito. Vedere la sezione [Ripristino](#recovery) per informazioni su come elencare gli oggetti eliminati temporaneamente.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*I dati eliminati temporaneamente sono di colore grigio, mentre i dati attivi sono di colore blu. I dati scritti più di recente sono al di sotto di quelli più vecchi. Quando B0 viene sovrascritto con B1, viene generato uno snapshot di B0 eliminato temporaneamente. Quando B1 viene sovrascritto con B2, viene generato uno snapshot di B1 eliminato temporaneamente.*

> [!NOTE]
> L'eliminazione temporanea consente solo la protezione da sovrascrittura per le operazioni di copia quando è abilitata per l'account del BLOB di destinazione.

> [!NOTE]
> L'eliminazione temporanea non consente la protezione da sovrascrittura per i BLOB nel livello di archiviazione archivio. Se un BLOB nel livello di archiviazione archivio viene sovrascritto con un nuovo BLOB in qualsiasi livello, il BLOB sovrascritto scade definitivamente.

Quando viene chiamato **Delete Blob** su uno snapshot, lo snapshot viene contrassegnato come eliminato temporaneamente. Non viene generato un nuovo snapshot.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*I dati eliminati temporaneamente sono di colore grigio, mentre i dati attivi sono di colore blu. I dati scritti più di recente sono al di sotto di quelli più vecchi. Quando viene chiamato **Snapshot Blob**, B0 diventa uno snapshot e B1 rappresenta lo stato attivo del BLOB. Quando viene eliminato lo snapshot B0, viene contrassegnato come eliminato temporaneamente.*

Quando viene chiamato **Delete Blob** su un BLOB di base (qualsiasi BLOB che non è di per sé uno snapshot), tale BLOB viene contrassegnato come eliminato temporaneamente. Coerentemente con il comportamento precedente, una chiamata **Delete Blob** su un BLOB contenente snapshot attivi restituisce un errore. Una chiamata **Delete Blob** su un BLOB con snapshot eliminati temporaneamente non restituisce un errore. Quando l'eliminazione temporanea è abilitata, è comunque possibile eliminare un BLOB e tutti i relativi snapshot in un'unica operazione. In questo modo il BLOB di base e gli snapshot vengono contrassegnati come eliminati temporaneamente.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*I dati eliminati temporaneamente sono di colore grigio, mentre i dati attivi sono di colore blu. I dati scritti più di recente sono al di sotto di quelli più vecchi. Qui viene eseguita una chiamata **Delete Blob** per eliminare B2 e tutti gli snapshot associati. Il BLOB attivo, ovvero B2, e tutti gli snapshot associati vengono contrassegnati come eliminati temporaneamente.*

> [!NOTE]
> Quando un BLOB eliminato temporaneamente viene sovrascritto, viene generato automaticamente uno snapshot eliminato temporaneamente dello stato del BLOB prima dell'operazione di scrittura. Il nuovo BLOB eredita il livello del BLOB sovrascritto.

L'eliminazione temporanea non salva i dati in caso di eliminazioni di contenitori o account, né quando vengono sovrascritti i metadati e le proprietà del BLOB. Per proteggere un account di archiviazione in caso di errata eliminazione, è possibile configurare un blocco tramite Azure Resource Manager. Vedere l'articolo su Azure Resource Manager [Bloccare le risorse per impedire modifiche impreviste](../../azure-resource-manager/resource-group-lock-resources.md) per altre informazioni.

La tabella seguente illustra il comportamento previsto quando l'eliminazione temporanea è abilitata:

| Operazione API REST                                                                                                | Tipo di risorsa                 | DESCRIZIONE                                                                                                 | Modifica del comportamento                                                                                                                                                                                                                                                                                                                                                   |
|-------------------------------------------------------------------------------------------------------------------|-------------------------------|-------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Eliminazione](/rest/api/storagerp/StorageAccounts/Delete)              | Account                       | Elimina l'account di archiviazione, inclusi tutti i contenitori e i BLOB contenuti al suo interno.                           | Nessuna modifica. I contenitori e i BLOB contenuti nell'account eliminato non sono recuperabili.                                                                                                                                                                                                                                                                                          |
| [Delete Container](/rest/api/storageservices/fileservices/delete-container)       | Contenitore                     | Elimina il contenitore, inclusi tutti i BLOB contenuti al suo interno.                                                | Nessuna modifica. I BLOB contenuti nell'account eliminato non sono recuperabili.                                                                                                                                                                                                                                                                                                       |
| [Put Blob](/rest/api/storageservices/fileservices/put-blob)                       | BLOB in blocchi, di accodamento e di pagine | Crea un nuovo BLOB o sostituisce un BLOB esistente all'interno di un contenitore                                          | Se usata per sostituire un BLOB esistente, viene generato automaticamente uno snapshot dello stato del BLOB prima della chiamata. Questo vale anche per un BLOB eliminato temporaneamente in precedenza solo nel caso in cui venga sostituito da un BLOB dello stesso tipo (in blocchi, di accodamento o di pagina). Se viene sostituito da un BLOB di tipo diverso, tutti i dati eliminati temporaneamente esistenti scadranno definitivamente. |
| [Delete Blob](/rest/api/storageservices/fileservices/delete-blob)                 | BLOB in blocchi, di accodamento e di pagine | Contrassegna un BLOB o uno snapshot di BLOB per l'eliminazione. Il BLOB o lo snapshot verrà eliminato in seguito durante la Garbage Collection | Se usata per eliminare uno snapshot di BLOB, tale snapshot viene contrassegnato come eliminato temporaneamente. Se usata per eliminare un BLOB, tale BLOB viene contrassegnato come eliminato temporaneamente.                                                                                                                                                                                                                           |
| [Copy Blob](/rest/api/storageservices/fileservices/copy-blob)                     | BLOB in blocchi, di accodamento e di pagine | Copia un BLOB di origine in un BLOB di destinazione nello stesso account di archiviazione o in un altro account di archiviazione.       | Se usata per sostituire un BLOB esistente, viene generato automaticamente uno snapshot dello stato del BLOB prima della chiamata. Questo vale anche per un BLOB eliminato temporaneamente in precedenza solo nel caso in cui venga sostituito da un BLOB dello stesso tipo (in blocchi, di accodamento o di pagina). Se viene sostituito da un BLOB di tipo diverso, tutti i dati eliminati temporaneamente esistenti scadranno definitivamente. |
| [Put Block](/rest/api/storageservices/put-block)                                  | BLOB in blocchi                   | Crea un nuovo blocco di cui eseguire il commit come parte di un BLOB in blocchi.                                                | In caso di commit di un blocco per in un BLOB attivo, non avviene alcuna modifica. In caso di commit di un blocco in un BLOB eliminato temporaneamente, viene creato un nuovo BLOB e generato automaticamente uno snapshot per acquisire lo stato del BLOB eliminato temporaneamente.                                                                                                                      |
| [Elenco Put Block](/rest/api/storageservices/fileservices/put-block-list)           | BLOB in blocchi                   | Esegue il commit di un BLOB specificando il set di ID dei blocchi che compongono il BLOB in blocchi.                             | Se usata per sostituire un BLOB esistente, viene generato automaticamente uno snapshot dello stato del BLOB prima della chiamata. Questo vale anche per un BLOB eliminato temporaneamente in precedenza solo se si tratta di un BLOB in blocchi. Se viene sostituito da un BLOB di tipo diverso, tutti i dati eliminati temporaneamente esistenti scadranno definitivamente.                                                |
| [Put Page](/rest/api/storageservices/fileservices/put-page)                       | BLOB di pagine                    | Scrive un intervallo di pagine in un BLOB di pagine.                                                                     | Nessuna modifica. I dati del BLOB di pagine sovrascritti o cancellati con questa operazione non vengono salvati e non sono recuperabili.                                                                                                                                                                                                                                                   |
| [Append Block](/rest/api/storageservices/fileservices/append-block)               | BLOB di accodamento                  | Scrive un blocco di dati alla fine di un BLOB di accodamento                                                         | Nessuna modifica.                                                                                                                                                                                                                                                                                                                                                           |
| [Impostare le proprietà BLOB](/rest/api/storageservices/fileservices/set-blob-properties) | BLOB in blocchi, di accodamento e di pagine | Imposta i valori delle proprietà di sistema definite per un BLOB.                                                       | Nessuna modifica. Le proprietà del BLOB sovrascritto non sono recuperabili.                                                                                                                                                                                                                                                                                                          |
| [Set Blob Metadata](/rest/api/storageservices/fileservices/set-blob-metadata)     | BLOB in blocchi, di accodamento e di pagine | Imposta i metadati definiti dall'utente per il BLOB specificato come una o più coppie nome-valore.                          | Nessuna modifica. I metadati del BLOB sovrascritto non sono recuperabili.                                                                                                                                                                                                                                                                                                             |

È importante notare che la chiamata "Put Page" per sovrascrivere o cancellare gli intervalli di un BLOB di pagine non causerà la generazione automatica di snapshot. I dischi delle macchine virtuali sono supportati dai BLOB di pagine e usano **Put Page** per la scrittura dei dati.

### <a name="recovery"></a>Ripristino

Per semplificare il ripristino dei dati eliminati, è stata introdotta una nuova API "Undelete Blob". Chiamando l'API Undelete su un BLOB di base eliminato temporaneamente, il BLOB e tutti gli snapshot eliminati temporaneamente associati ad esso vengono ripristinati come attivi. Chiamando l'API Undelete su un BLOB di base attivo, tutti gli snapshot eliminati temporaneamente associati ad esso vengono ripristinati come attivi. Quando gli snapshot vengono ripristinati come attivi, sono simili agli snapshot generati dall'utente e non sovrascrivono il BLOB di base.

Per ripristinare un BLOB a uno snapshot eliminato temporaneamente specifico, si può chiamare **Undelete Blob** sul BLOB di base e quindi copiare lo snapshot sul BLOB ora attivo. È anche possibile copiare lo snapshot in un nuovo BLOB.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*I dati eliminati temporaneamente sono di colore grigio, mentre i dati attivi sono di colore blu. I dati scritti più di recente sono al di sotto di quelli più vecchi. Qui viene chiamato **Undelete Blob** sul BLOB B, ripristinando quindi il BLOB di base, ovvero B1, e tutti gli snapshot associati, in questo caso solo B0, come attivi. Nel secondo passaggio, B0 viene copiato sul BLOB di base. Questa operazione di copia genera uno snapshot di B1 eliminato temporaneamente.*

Per visualizzare i BLOB e gli snapshot dei BOB eliminati temporaneamente, è possibile scegliere di includere i dati eliminati in **List Blobs**. Si può scegliere di visualizzare solo i BLOB di base eliminati temporaneamente o includere anche gli snapshot dei BLOB eliminati temporaneamente. Per tutti i dati eliminati temporaneamente, è possibile visualizzare l'ora di eliminazione, nonché il numero di giorni che mancano alla scadenza definitiva.

### <a name="example"></a>Esempio

Di seguito è riportato l'output della console di uno script .NET che carica, sovrascrive, genera gli snapshot, elimina e ripristina un BLOB denominato "HelloWorld" quando l'eliminazione temporanea è abilitata:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Vedere la sezione [Passaggi successivi](#next-steps) per un puntatore all'applicazione che ha generato questo output.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Tutti i dati eliminati temporaneamente vengono fatturati alla stessa tariffa dei dati attivi. Non si incorre in alcun addebito per i dati che vengono eliminati definitivamente al termine del periodo di conservazione configurato. Per approfondimenti sugli snapshot e sull'incremento dei costi dovuto agli snapshot, vedere [Informazioni sull'incremento dei costi dovuto agli snapshot](storage-blob-snapshots.md).

Non verrà addebitato alcun costo per le transazioni relative alla generazione automatica di snapshot. Verranno invece fatturate le transazioni di **Undelete Blob** alla tariffa delle operazioni di scrittura.

Per altri dettagli sui prezzi per Archiviazione BLOB di Azure in generale, consultare la [pagina dei prezzi di Archiviazione BLOB di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Quando si abilita inizialmente l'eliminazione temporanea, è consigliabile usare un periodo di conservazione ridotto per comprendere meglio in che modo la funzionalità influirà sui costi.

## <a name="quickstart"></a>Guida introduttiva

### <a name="azure-portal"></a>Portale di Azure
Per abilitare l'eliminazione temporanea, selezionare l'opzione **Eliminazione temporanea** in **Servizio BLOB**. Quindi fare clic su **Abilitata** e immettere il numero di giorni per cui si vogliono conservare i dati eliminati temporaneamente.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Per visualizzare i BLOB eliminati temporaneamente, selezionare la casella di controllo **Mostra BLOB eliminati**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Per visualizzare gli snapshot eliminati temporaneamente per un determinato BLOB, selezionare il BLOB, quindi fare clic su **Visualizza snapshot**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Verificare che la casella di controllo **Mostra snapshot eliminati** sia selezionata.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Quando si fa clic su un BLOB o uno snapshot eliminato temporaneamente, si notino le nuove proprietà del BLOB. Esse indicano quando l'oggetto è stato eliminato e il numero di giorni rimanenti fino alla scadenza definitiva del BLOB o dello snapshot del BLOB. Se l'oggetto eliminato temporaneamente non è uno snapshot, è anche possibile annullarne l'eliminazione.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Tenere presente che annullando l'eliminazione di un BLOB verrà annullata anche l'eliminazione di tutti gli snapshot associati ad esso. Per annullare l'eliminazione degli snapshot eliminati temporaneamente per un BLOB attivo, fare clic sul BLOB e selezionare **Undelete all snapshots** (Annulla l'eliminazione di tutti gli snapshot).

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Dopo aver annullato l'eliminazione degli snapshot di un BLOB, è possibile fare clic su **Alza di livello** per copiare uno snapshot sul BLOB radice, ripristinando in questo modo il BLOB allo snapshot.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>PowerShell
Per abilitare l'eliminazione temporanea, aggiornare le proprietà del servizio del client BLOB. L'esempio seguente abilita l'eliminazione temporanea per un subset di account in una sottoscrizione:

```powershell
Set-AzureRmContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzureRMStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzureStorageDeleteRetentionPolicy -RetentionDays 7
```

Per ripristinare i BLOB eliminati accidentalmente, chiamare Undelete su tali BLOB. Tenere presente che chiamando **Undelete Blob** sia sui BLOB attivi che su quelli eliminati temporaneamente, tutti gli snapshot eliminati temporaneamente associati ad essi verranno ripristinati come attivi. L'esempio seguente chiama Undelete su tutti i BLOB eliminati temporaneamente e attivi presenti in un contenitore:
```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzureStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure 
Per abilitare l'eliminazione temporanea, aggiornare le proprietà del servizio del client BLOB:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Per verificare se l'eliminazione temporanea è attivata, usare il comando seguente: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

### <a name="python-client-library"></a>Libreria client Python

Per abilitare l'eliminazione temporanea, aggiornare le proprietà del servizio del client BLOB:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(delete_retention_policy = DeleteRetentionPolicy(enabled = True, days = 7))
```

### <a name="net-client-library"></a>Libreria client .Net

Per abilitare l'eliminazione temporanea, aggiornare le proprietà del servizio del client BLOB:

```csharp
// Get the blob client’s service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client’s service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Per ripristinare i BLOB eliminati accidentalmente, chiamare Undelete su tali BLOB.
Tenere presente che chiamando **Undelete Blob** sia sui BLOB attivi che su quelli eliminati temporaneamente, tutti gli snapshot eliminati temporaneamente associati ad essi verranno ripristinati come attivi. L'esempio seguente chiama Undelete su tutti i BLOB eliminati temporaneamente e attivi presenti in un contenitore:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Per eseguire il ripristino a una versione di BLOB specifica, chiamare prima Undelete su un BLOB, quindi copiare lo snapshot desiderato su di esso. L'esempio seguente ripristina un BLOB in blocchi al relativo ultimo snapshot generato:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

## <a name="should-i-use-soft-delete"></a>È necessario usare l'eliminazione temporanea?

Se esiste la possibilità che i dati vengano accidentalmente modificati o eliminati da un'applicazione o da un utente con un altro account di archiviazione, è consigliabile abilitare l'eliminazione temporanea.
L'eliminazione temporanea fa parte di una strategia di protezione dati e può aiutare a evitare la perdita accidentale di dati.

## <a name="faq"></a>Domande frequenti

**Per quali tipi di risorse di archiviazione è possibile usare l'eliminazione temporanea?**

Attualmente l'eliminazione temporanea è disponibile solo per l'archiviazione BLOB (di oggetti).

**L'eliminazione temporanea è disponibile per tutti i tipi di account di archiviazione?**

Sì, l'eliminazione temporanea è disponibile per gli account di archiviazione BLOB, oltre che per i BLOB negli account di archiviazione per utilizzo generico (sia GPv1 che GPv2). Questo vale per gli account sia Standard che Premium. L'eliminazione temporanea non è disponibile per i dischi gestiti.

**L'eliminazione temporanea è disponibile per tutti i livelli di archiviazione?**

Sì, l'eliminazione temporanea è disponibile per tutti i livelli di archiviazione, inclusi quelli ad accesso frequente, ad accesso sporadico e archivio. L'eliminazione temporanea non consente tuttavia la protezione da sovrascrittura per i BLOB nel livello archivio.

**È possibile usare l'API Imposta livello BLOB per impostare il livello dei BLOB con gli snapshot con eliminazione temporanea?**

Sì. Gli snapshot con eliminazione temporanea rimangono nel livello originale, ma il BLOB di base viene spostato nel nuovo livello. 

**Gli account di archiviazione Premium hanno un limite di 100 snapshot per BLOB. Gli snapshot eliminati temporaneamente vengono considerati ai fini questo limite?**

No, gli snapshot eliminati temporaneamente non vengono considerati ai fini questo limite.

**È possibile abilitare l'eliminazione temporanea per gli account di archiviazione esistenti?**

Sì, l'eliminazione temporanea può essere configurata sia per gli account di archiviazione nuovi che per quelli esistenti.

**Se si elimina un intero account o un intero contenitore con l'eliminazione temporanea abilitata, tutti i BLOB associati verranno salvati?**

No, se si elimina un intero account o un intero contenitore, tutti i BLOB associati verranno eliminati definitivamente. Per informazioni su come proteggere un account di archiviazione da eliminazioni accidentali, vedere l'articolo su Azure Resource Manager [Bloccare le risorse per impedire modifiche impreviste](../../azure-resource-manager/resource-group-lock-resources.md).

**È possibile visualizzare le metriche relative alla capacità per i dati eliminati?**

I dati eliminati temporaneamente sono inclusi nella capacità totale dell'account di archiviazione. Per altre informazioni sulla verifica e il monitoraggio della capacità di archiviazione, vedere l'articolo [Analisi archiviazione](../common/storage-analytics.md).

**Se si disabilita l'eliminazione temporanea, è comunque possibile accedere ai dati eliminati temporaneamente?**

Sì, quando si disabilita l'eliminazione temporanea è comunque possibile accedere e ripristinare i dati eliminati temporaneamente.

**È possibile leggere e copiare gli snapshot del BLOB eliminati temporaneamente?**

Sì, ma prima è necessario chiamare Undelete sul BLOB.

**L'eliminazione temporanea è disponibile per tutti i tipi di BLOB?**

Sì, l'eliminazione temporanea è disponibile per i BLOB in blocchi, i BLOB di accodamento e i BLOB di pagine.

**L'eliminazione temporanea è disponibile per i dischi delle macchine virtuali?**

L'eliminazione temporanea è disponibile per i dischi non gestiti sia Standard che Premium. L'eliminazione temporanea aiuta solo a recuperare i dati eliminati tramite **Delete Blob**, **Put Blob**, **Put Block List**, **Put Block** e **Copy Blob**. I dati sovrascritti tramite una chiamata a **Put Page** non sono recuperabili.

**È necessario modificare le applicazioni esistenti per usare l'eliminazione temporanea?**

È possibile sfruttare i vantaggi dell'eliminazione temporanea indipendentemente dalla versione dell'API in uso. Tuttavia, per elencare e ripristinare i BLOB e gli snapshot dei BLOB eliminati temporaneamente, è necessario usare la versione 2017-07-29 dell'[API REST dei servizi di archiviazione](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) o una successiva. In generale, è sempre consigliabile usare la versione più recente indipendentemente dal fatto che usi o meno questa funzionalità.

## <a name="next-steps"></a>Passaggi successivi

* [Codice di esempio .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)

* [Blob Service REST API](/rest/api/storageservices/fileservices/blob-service-rest-api) (API REST del servizio BLOB)

* [Replica di Archiviazione di Azure](../common/storage-redundancy.md)

* [Progettazione di applicazioni a disponibilità elevata con RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

* [Cosa fare se si verifica un'interruzione di Archiviazione di Azure](../common/storage-disaster-recovery-guidance.md)
