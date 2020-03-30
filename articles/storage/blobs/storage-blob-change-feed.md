---
title: Modificare il feed in Archiviazione BLOB di Azure (anteprima)Change feed in Azure Blob Storage (Preview) Documenti Microsoft
description: Informazioni sui log dei feed di modifiche in Archiviazione BLOB di Azure e su come usarli.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: ac111b06d578a0e9af8581ef2e8caeccfc4a291e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536888"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Modificare il supporto dei feed in Archiviazione BLOB di Azure (anteprima)Change feed support in Azure Blob Storage (Preview)

Lo scopo del feed di modifiche è fornire i log delle transazioni di tutte le modifiche apportate ai BLOB e ai metadati BLOB nell'account di archiviazione. Il feed di modifiche fornisce **un** log **ordinato,** **garantito,** **durevole,** **non modificabile**di queste modifiche. Le applicazioni client possono leggere questi log in qualsiasi momento, in streaming o in modalità batch. Il feed di modifiche consente di creare soluzioni efficienti e scalabili che elaborano gli eventi di modifica che si verificano nell'account di archiviazione BLOB a un costo basso.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Il feed di modifiche viene archiviato come [BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) in un contenitore speciale nell'account di archiviazione al costo standard dei [prezzi dei BLOB.](https://azure.microsoft.com/pricing/details/storage/blobs/) È possibile controllare il periodo di conservazione di questi file in base alle proprie esigenze (vedere le [condizioni](#conditions) della versione corrente). Gli eventi di modifica vengono aggiunti al feed di modifiche come record nella specifica di formato [Apache Avro:](https://avro.apache.org/docs/1.8.2/spec.html) un formato binario compatto, veloce e che fornisce strutture di dati avanzate con schema inline. Questo formato è largamente usato nell'ecosistema Hadoop, dall'analisi di flusso e da Azure Data Factory.

È possibile elaborare questi log in modo asincrono, incrementale o completo. Qualsiasi numero di applicazioni client può leggere in modo indipendente il feed di modifiche, in parallelo e al proprio ritmo. Le applicazioni di analisi come [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) o [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) possono utilizzare i log direttamente come file Avro, che consentono di elaborarli a basso costo, con larghezza di banda elevata e senza dover scrivere un'applicazione personalizzata.

Il supporto dei feed di modifiche è adatto per gli scenari che elaborano i dati in base agli oggetti modificati. Ad esempio, le applicazioni possono:

  - Aggiornare un indice secondario, sincronizzarsi con una cache, un motore di ricerca o qualsiasi altro scenario di gestione dei contenuti.
  
  - Estrarre le informazioni dettagliate e le metriche di analisi business, in base alle modifiche apportate agli oggetti, in modalità di streaming o in batch.
  
  - Archivia, controlla e analizza le modifiche ai tuoi oggetti, in qualsiasi periodo di tempo, per sicurezza, conformità o intelligence per la gestione dei dati aziendali.

  - Crea soluzioni per eseguire il backup, il mirroring o la replica dello stato degli oggetti nel tuo account per la gestione delle emergenze o la conformità.

  - Compilare pipeline di applicazioni connesse che reagiscono a eventi di modifica o pianificano le esecuzioni in base all'oggetto creato o modificato.

> [!NOTE]
> Il feed modifiche fornisce un modello di log ordinato e durevole delle modifiche che si verificano in un BLOB. Le modifiche vengono scritte e rese disponibili nel log del feed di modifiche entro un ordine di pochi minuti dalla modifica. Se l'applicazione deve reagire agli eventi molto più rapidamente, è consigliabile usare invece gli eventi di [archiviazione BLOB.](storage-blob-event-overview.md) [Gli eventi](storage-blob-event-overview.md) di archiviazione BLOB forniscono eventi una tantera in tempo reale che consentono alle funzioni o alle applicazioni di Azure di reagire rapidamente alle modifiche che si verificano in un BLOB. 

## <a name="enable-and-disable-the-change-feed"></a>Abilitare e disabilitare il feed di modifiche

È necessario abilitare il feed di modifiche nell'account di archiviazione per avviare l'acquisizione e la registrazione delle modifiche. Disattivare il feed di modifiche per interrompere l'acquisizione delle modifiche. È possibile abilitare e disabilitare le modifiche usando i modelli di Azure Resource Manager nel portale o in Powershell.You can enable and disable changes by using Azure Resource Manager templates on Portal or Powershell.

Ecco alcuni aspetti da tenere presenti quando abiliti il feed di modifiche.

- È disponibile un solo feed di modifiche per il servizio BLOB in ogni account di archiviazione e viene archiviato nel contenitore **$blobchangefeed.**

- Le modifiche di creazione, aggiornamento ed eliminazione vengono acquisite solo a livello di servizio BLOB.

- Il feed di modifiche acquisisce *tutte le* modifiche per tutti gli eventi disponibili che si verificano nell'account. Le applicazioni client possono filtrare i tipi di evento in base alle esigenze. (Vedere le [condizioni](#conditions) della release corrente).

- Solo gli account di archiviazione GPv2 e BLOB possono abilitare il feed di modifica. Gli account Premium BlockBlobStorage e gli account gerarchici abilitati per lo spazio dei nomi non sono attualmente supportati. Gli account di archiviazione GPv1 non sono supportati, ma possono essere aggiornati a GPv2 senza tempi di inattività, vedere [Eseguire l'aggiornamento a un account di archiviazione GPv2](../common/storage-account-upgrade.md) per altre informazioni.

> [!IMPORTANT]
> Il feed di modifiche è disponibile nell'anteprima pubblica ed è disponibile nelle regioni **westcentralus** e **westus2.** Vedere la sezione [relativa alle condizioni](#conditions) di questo articolo. Per iscriversi all'anteprima, vedere la sezione [Registrare l'abbonamento](#register) di questo articolo. È necessario registrare la sottoscrizione prima di poter abilitare il feed di modifiche per gli account di archiviazione.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Abilitare il feed di modifiche nell'account di archiviazione tramite il portale di Azure:Enable change feed on your storage account by using Azure portal:

1. Nel [portale](https://portal.azure.com/)di Azure selezionare l'account di archiviazione. 

2. Passare all'opzione **Protezione dati** in **Servizio BLOB**.

3. Fare clic su **Abilitato** in **Feed di modifica BLOB**

4. Scegliere il pulsante **Salva** per confermare le impostazioni di Protezione dati

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Abilitare il feed di modifiche tramite PowerShell:Enable change feed by using PowerShell:

1. Installare la versione più recente di PowershellGet.Install the latest PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Chiudere e riaprire la console di PowerShell.Close, and then reopen the Powershell console.

3. Installare il modulo di anteprima **Az.Storage.Install the Az.Storage** preview module.

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate per eseguire l'autenticazione.

   ```powershell
   Connect-AzAccount
   ```

5. Abilitare il feed di modifiche per l'account di archiviazione.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Modello](#tab/template)
Usare un modello di Azure Resource Manager per abilitare Change feed nell'account di archiviazione esistente tramite il portale di Azure:Use an Azure Resource Manager template to enable Change feed on your existing storage account via Azure portal:

1. Nel portale di Azure scegliere **Crea una risorsa.**

2. In **Cerca nel Marketplace** digitare **distribuzione modelli** e quindi premere **INVIO**.

3. Scegliere **[Distribuisci un modello personalizzato](https://portal.azure.com/#create/Microsoft.Template)**, quindi Scegliere Crea modello personalizzato **nell'editor.**

4. Nell'editor dei modelli incollare il codice json seguente. Sostituire il segnaposto `<accountName>` con il nome del proprio account di archiviazione.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. Scegliere il pulsante **Salva,** specificare il gruppo di risorse dell'account, quindi scegliere il pulsante **Acquista** per distribuire il modello e abilitare il feed di modifiche.

---

## <a name="consume-the-change-feed"></a>Utilizzare il feed di modifiche

Il feed di modifiche produce diversi file di metadati e di log. Questi file si trovano nel contenitore **$blobchangefeed** dell'account di archiviazione. 

> [!NOTE]
> Nella versione corrente, il contenitore $blobchangefeed non è visibile in Azure Storage Explorer o nel portale di Azure.In the current release, the **container** container is not visible in Azure Storage Explorer or the Azure portal. Attualmente non è possibile visualizzare il contenitore $blobchangefeed quando si chiama l'API ListContainers, ma è possibile chiamare l'API ListBlobs direttamente nel contenitore per visualizzare i BLOB.

Le applicazioni client possono usare il feed di modifiche usando la libreria del processore del feed di modifiche BLOB fornita con l'SDK del processore del feed di modifiche. 

Vedere [Elaborare i log dei feed delle modifiche in Archiviazione BLOB](storage-blob-change-feed-how-to.md)di Azure.See Process change feed logs in Azure Blob Storage .

## <a name="understand-change-feed-organization"></a>Comprendere l'organizzazione del feed di modifiche

<a id="segment-index"></a>

### <a name="segments"></a>Segmenti

Il feed di modifiche è un registro delle modifiche che sono organizzate in *segmenti* **orari** ma aggiunte e aggiornate ogni pochi minuti. Questi segmenti vengono creati solo quando sono presenti eventi di modifica del BLOB che si verificano in quell'ora. Ciò consente all'applicazione client di utilizzare le modifiche che si verificano all'interno di intervalli di tempo specifici senza dover eseguire ricerche nell'intero log. Per ulteriori informazioni, vedere [le specifiche](#specifications).

Un segmento orario disponibile del feed di modifiche è descritto in un file manifesto che specifica i percorsi dei file del feed di modifiche per quel segmento. L'elenco `$blobchangefeed/idx/segments/` della directory virtuale mostra questi segmenti ordinati in base al tempo. Il percorso del segmento descrive l'inizio dell'intervallo orario rappresentato dal segmento. È possibile utilizzare tale elenco per filtrare i segmenti di log di interesse.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> L'oggetto `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` viene creato automaticamente quando si abilita il feed di modifiche. È possibile ignorare questo file. Si tratta di un file di inizializzazione sempre vuoto. 

Il file manifesto`meta.json`del segmento ( ) mostra il `chunkFilePaths` percorso dei file del feed di modifiche per quel segmento nella proprietà. Ecco un esempio di un file manifesto del segmento.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> Il `$blobchangefeed` contenitore viene visualizzato solo dopo aver abilitato la funzionalità di modifica del feed nel tuo account. È necessario attendere alcuni minuti dopo aver abilitato il feed di modifiche prima di poter elencare i BLOB nel contenitore. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Modificare i record degli eventi

I file del feed di modifiche contengono una serie di record di eventi di modifica. Ogni record di evento di modifica corrisponde a una modifica a un singolo BLOB. I record vengono serializzati e scritti nel file utilizzando la specifica di formato [Apache Avro.](https://avro.apache.org/docs/1.8.2/spec.html) I record possono essere letti utilizzando la specifica del formato di file Avro. Ci sono diverse librerie disponibili per elaborare i file in quel formato.

I file del feed `$blobchangefeed/log/` di modifiche vengono archiviati nella directory virtuale come [BLOB di accodamento.](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) Il primo file del feed `00000` di modifiche in ogni `00000.avro`percorso avrà il nome del file (ad esempio ). Il nome di ogni file di log successivo aggiunto a `00001.avro`tale percorso verrà incrementato di 1 (ad esempio: ).

Ecco un esempio di modifica del record dell'evento dal file del feed di modifiche convertito in Json.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

Per una descrizione di ogni proprietà, vedere Schema di eventi Griglia di eventi di [Azure per l'archiviazione BLOB.](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties)

> [!NOTE]
> I file di feed di modifiche per un segmento non vengono visualizzati immediatamente dopo la creazione di un segmento. La durata del ritardo è compresa nel normale intervallo di latenza di pubblicazione del feed di modifiche che si trova entro pochi minuti dalla modifica.

<a id="specifications"></a>

## <a name="specifications"></a>Specifiche

- I record degli eventi di modifica vengono aggiunti solo al feed di modifiche. Una volta aggiunti, questi record sono immutabili e la posizione di registrazione è stabile. Le applicazioni client possono gestire il proprio checkpoint nella posizione di lettura del feed di modifiche.

- I record degli eventi di modifica vengono aggiunti entro un ordine di pochi minuti dalla modifica. Le applicazioni client possono scegliere di utilizzare i record quando vengono aggiunti per l'accesso al flusso o in blocco in qualsiasi altro momento.

- I record degli eventi di modifica vengono ordinati in base all'ordine di modifica **per BLOB.** L'ordine delle modifiche tra i BLOB non è definito in Archiviazione BLOB di Azure.Order of changes across blobs is undefined in Azure Blob Storage. Tutte le modifiche in un segmento precedente sono prima di eventuali modifiche nei segmenti successivi.

- I record degli eventi di modifica vengono serializzati nel file di registro utilizzando la specifica di formato [Apache Avro 1.8.2.](https://avro.apache.org/docs/1.8.2/spec.html)

- Modificare i record `eventType` degli eventi `Control` in cui il ha un valore di sono record di sistema interni e non riflettono una modifica apportata agli oggetti nel tuo account. È possibile ignorare tali record.

- I valori `storageDiagnonstics` nel contenitore delle proprietà sono solo per uso interno e non sono progettati per l'utilizzo da parte dell'applicazione. Le applicazioni non devono avere una dipendenza contrattuale da tali dati. È possibile ignorare tali proprietà.

- Il tempo rappresentato dal segmento è **approssimativo** con limiti di 15 minuti. Pertanto, per garantire il consumo di tutti i record entro un tempo specificato, utilizzare il segmento dell'ora precedente e successiva consecutiva.

- Ogni segmento può avere `chunkFilePaths` un numero diverso di a causa del partizionamento interno del flusso di log per gestire la velocità effettiva di pubblicazione. I file di `chunkFilePath` log in ognuno di essi sono garantiti per contenere BLOB che si escludono a vicenda e possono essere utilizzati ed elaborati in parallelo senza violare l'ordine delle modifiche per BLOB durante l'iterazione.

- I segmenti iniziano `Publishing` con stato. Una volta completata l'aggiunta dei record al `Finalized`segmento, sarà . I file di log in qualsiasi segmento datato dopo la data della `LastConsumable` proprietà nel `$blobchangefeed/meta/Segments.json` file non devono essere utilizzati dall'applicazione. Di seguito è riportato un `$blobchangefeed/meta/Segments.json` esempio della proprietà in un file:Here's an example of the `LastConsumable`property in a file:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="register"></a>

## <a name="register-your-subscription-preview"></a>Registrare l'abbonamento (anteprima)Register your subscription (Preview)

Poiché il feed di modifiche è solo in anteprima pubblica, è necessario registrare l'abbonamento per usare la funzionalità.

### <a name="register-by-using-powershell"></a>Eseguire la registrazione tramite PowerShellRegister by using PowerShell

In una console di PowerShell eseguire questi comandi:In a PowerShell console, run these commands:

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>Eseguire la registrazione tramite l'interfaccia della riga di comando di AzureRegister by using

In Azure Cloud Shell eseguire questi comandi:In Azure Cloud Shell, run these commands:

```azurecli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>Condizioni e problemi noti (anteprima)

Questa sezione descrive i problemi noti e le condizioni nell'anteprima pubblica corrente del feed di modifiche. 
- Per l'anteprima, è necessario [registrare la sottoscrizione](#register) prima di poter abilitare il feed di modifiche per l'account di archiviazione nelle aree westcentralus o westus2. 
- Il feed di modifiche acquisisce solo le operazioni di creazione, aggiornamento, eliminazione e copia. Gli aggiornamenti dei metadati non sono attualmente acquisiti in anteprima.
- La modifica dei record degli eventi per ogni singola modifica potrebbe essere visualizzata più di una volta nel feed di modifiche.
- Non è ancora possibile gestire la durata dei file di log del feed di modifiche impostando criteri di conservazione basati sul tempo e non è possibile eliminare i BLOB 
- La `url` proprietà del file di registro è attualmente sempre vuota.
- La `LastConsumable` proprietà del file segments.json non elenca il primo segmento finalizzato dal feed di modifiche. Questo problema si verifica solo dopo la finalizzazione del primo segmento. Tutti i segmenti successivi alla prima ora `LastConsumable` vengono acquisiti con precisione nella proprietà.
- Attualmente non è possibile visualizzare il **contenitore $blobchangefeed** quando si chiama l'API ListContainers e il contenitore non viene visualizzato nel portale di Azure o Esplora archivi
- Gli account di archiviazione che in precedenza hanno avviato un [failover dell'account](../common/storage-disaster-recovery-guidance.md) potrebbero presentare problemi con il file di log che non viene visualizzato. Eventuali failover futuri dell'account possono influire anche sul file di log durante l'anteprima.

## <a name="faq"></a>Domande frequenti

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Qual è la differenza tra il feed delle modifiche e la registrazione di Analisi archiviazione?
I log di analytics hanno record di tutte le operazioni di lettura, scrittura, elenco ed eliminazione con richieste riuscite e non riuscite in tutte le operazioni. I log di Analytics sono il massimo sforzo e nessun ordinamento è garantito.

Il feed di modifiche è una soluzione che fornisce il log transazionale delle mutazioni riuscite o delle modifiche apportate all'account, ad esempio la creazione, la modifica e le eliminazioni di BLOB. Il feed di modifiche garantisce che tutti gli eventi vengano registrati e visualizzati nell'ordine delle modifiche riuscite per ogni BLOB, pertanto non è necessario filtrare il rumore proveniente da un volume enorme di operazioni di lettura o richieste non riuscite. Il feed di modifiche è fondamentalmente progettato e ottimizzato per lo sviluppo di applicazioni che richiedono determinate garanzie.

### <a name="should-i-use-change-feed-or-storage-events"></a>È consigliabile usare il feed di modifiche o gli eventi di archiviazione?
È possibile sfruttare entrambe le funzionalità in cui gli eventi Feed di modifica e [Archiviazione BLOB](storage-blob-event-overview.md) forniscono le stesse informazioni con la stessa garanzia di affidabilità di recapito, con la differenza principale che è la latenza, l'ordinamento e l'archiviazione dei record degli eventi. Il feed delle modifiche pubblica i record nel log entro pochi minuti dalla modifica e garantisce anche l'ordine delle operazioni di modifica per BLOB. Gli eventi di archiviazione vengono inseriti in tempo reale e potrebbero non essere ordinati. Gli eventi del feed di modifica vengono archiviati in modo permanente nell'account di archiviazione come log stabili di sola lettura con la propria conservazione definita, mentre gli eventi di archiviazione sono temporanei per essere utilizzati dal gestore eventi, a meno che non vengano archiviati in modo esplicito. Con Il feed di modifiche, un numero qualsiasi di applicazioni può usare i log a proprio piacimento usando API BLOB o SDK. 

## <a name="next-steps"></a>Passaggi successivi

- Vedere un esempio di come leggere il feed di modifiche utilizzando un'applicazione client .NET. Vedere [Elaborare i log dei feed delle modifiche in Archiviazione BLOB](storage-blob-change-feed-how-to.md)di Azure.See Process change feed logs in Azure Blob Storage .
- Scopri come reagire agli eventi in tempo reale. Vedere [Risposta agli eventi di archiviazione BLOBSee Reacting to Blob Storage events](storage-blob-event-overview.md)
- Ulteriori informazioni sulle informazioni di registrazione dettagliate per le operazioni riuscite e non riuscite per tutte le richieste. Vedere [Registrazione dell'analisi di Archiviazione di AzureSee Azure Storage analytics logging](../common/storage-analytics-logging.md)
