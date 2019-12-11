---
title: Feed delle modifiche nell'archiviazione BLOB di Azure (anteprima) | Microsoft Docs
description: Informazioni sui log dei feed di modifiche nell'archivio BLOB di Azure e su come usarli.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 19a65e688d66738db0b6e4dcca383c6e4abed262
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974403"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Supporto del feed delle modifiche nell'archivio BLOB di Azure (anteprima)

Lo scopo del feed delle modifiche è fornire i log delle transazioni di tutte le modifiche apportate ai BLOB e ai metadati del BLOB nell'account di archiviazione. Il feed di modifiche fornisce un registro **ordinato**, **garantito**, **durevole**, non **modificabile**e di sola **lettura** di queste modifiche. Le applicazioni client possono leggere questi log in qualsiasi momento, in streaming o in modalità batch. Il feed delle modifiche consente di creare soluzioni efficienti e scalabili che elaborano gli eventi di modifica che si verificano nell'account di archiviazione BLOB a un costo ridotto.

Il feed delle modifiche viene archiviato come [BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) in un contenitore speciale nell'account di archiviazione al costo dei [prezzi di BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/) standard. È possibile controllare il periodo di conservazione di questi file in base ai requisiti (vedere le [condizioni](#conditions) della versione corrente). Gli eventi di modifica vengono aggiunti al feed delle modifiche come record nella specifica del formato [Apache avro](https://avro.apache.org/docs/1.8.2/spec.html) : un formato compatto, rapido e binario che fornisce strutture di dati avanzate con lo schema inline. Questo formato è largamente usato nell'ecosistema Hadoop, dall'analisi di flusso e da Azure Data Factory.

È possibile elaborare questi log in modo asincrono, in modo incrementale o completo. Un numero qualsiasi di applicazioni client può leggere in modo indipendente il feed delle modifiche, in parallelo e con il proprio ritmo. Le applicazioni di analisi come [Apache drill](https://drill.apache.org/docs/querying-avro-files/) o [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) possono usare i log direttamente come file Avro, che consentono di elaborarli a costi ridotti, con larghezza di banda elevata e senza dover scrivere un'applicazione personalizzata.

Il supporto del feed di modifiche è particolarmente adatto per gli scenari in cui vengono elaborati i dati in base agli oggetti modificati. Ad esempio, le applicazioni possono:

  - Aggiornare un indice secondario, sincronizzarlo con una cache, un motore di ricerca o qualsiasi altro scenario di gestione del contenuto.
  
  - Consente di estrarre informazioni dettagliate e metriche di analisi business in base alle modifiche apportate agli oggetti in modalità flusso o in modalità batch.
  
  - Archivia, controlla e analizza le modifiche apportate agli oggetti, in qualsiasi periodo di tempo, per la sicurezza, la conformità o l'Intelligence per la gestione dei dati aziendali.

  - Consente di creare soluzioni per eseguire il backup, il mirroring o la replica dello stato dell'oggetto nell'account per la gestione o la conformità di emergenza.

  - Creare pipeline di applicazioni connesse che reagiscono a eventi di modifica o pianificano le esecuzioni in base all'oggetto creato o modificato.

> [!NOTE]
> Il feed di modifiche fornisce un modello di log durevole e ordinato delle modifiche che si verificano in un BLOB. Le modifiche vengono scritte e rese disponibili nel log del feed delle modifiche entro un ordine di pochi minuti della modifica. Se l'applicazione deve rispondere agli eventi in modo molto più rapido, provare a usare [gli eventi di archiviazione BLOB](storage-blob-event-overview.md) . [Gli eventi di archiviazione BLOB](storage-blob-event-overview.md) offrono eventi monouso in tempo reale che consentono alle applicazioni o alle funzioni di Azure di rispondere rapidamente alle modifiche apportate a un BLOB. 

## <a name="enable-and-disable-the-change-feed"></a>Abilitare e disabilitare il feed delle modifiche

È necessario abilitare il feed delle modifiche nell'account di archiviazione per avviare l'acquisizione e la registrazione delle modifiche. Disabilitare il feed delle modifiche per arrestare l'acquisizione delle modifiche. È possibile abilitare e disabilitare le modifiche usando Azure Resource Manager modelli nel portale o in PowerShell.

Ecco alcuni aspetti da tenere presenti quando si Abilita il feed delle modifiche.

- Esiste solo un feed di modifiche per il servizio BLOB in ogni account di archiviazione e viene archiviato nel contenitore **$blobchangefeed** .

- Le modifiche create, Update e DELETE vengono acquisite solo a livello di servizio BLOB.

- Il feed delle modifiche acquisisce *tutte* le modifiche per tutti gli eventi disponibili che si verificano nell'account. Le applicazioni client possono filtrare i tipi di evento in modo obbligatorio. (Vedere le [condizioni](#conditions) della versione corrente).

- Solo gli account di archiviazione BLOB e GPv2 possono abilitare il feed delle modifiche. Gli account BlockBlobStorage Premium e gli account abilitati per gli spazi dei nomi gerarchici non sono attualmente supportati. Gli account di archiviazione utilizzo generico V1 non sono supportati, ma possono essere aggiornati a GPv2 senza tempi di inattività. per altre informazioni, vedere [eseguire l'aggiornamento a un account di archiviazione GPv2](../common/storage-account-upgrade.md) .

> [!IMPORTANT]
> Il feed delle modifiche è in anteprima pubblica ed è disponibile nelle aree **westcentralus** e **westus2** . Vedere la sezione [condizioni](#conditions) di questo articolo. Per iscriversi all'anteprima, vedere la sezione [registrare la sottoscrizione](#register) di questo articolo. Per poter abilitare il feed delle modifiche negli account di archiviazione, è necessario registrare la sottoscrizione.

### <a name="portaltabazure-portal"></a>[di Microsoft Azure](#tab/azure-portal)

Abilitare il feed delle modifiche nell'account di archiviazione usando portale di Azure:

1. Nella [portale di Azure](https://portal.azure.com/)selezionare l'account di archiviazione. 

2. Passare all'opzione **protezione dati** in **servizio BLOB**.

3. Fare clic su **abilitato** in **feed modifiche BLOB**

4. Scegliere il pulsante **Salva** per confermare le impostazioni di protezione dati

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Abilitare il feed delle modifiche usando PowerShell:

1. Installare la versione più recente di PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Chiudere e riaprire la console di PowerShell.

3. Installare il modulo **AZ. storage** Preview.

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate per eseguire l'autenticazione.

   ```powershell
   Connect-AzAccount
   ```

5. Abilitare il feed delle modifiche per l'account di archiviazione.

   ```powershell
   Update-AzStorageBlobServiceProperty -ResourceGroupName -StorageAccountName -EnableChangeFeed $true
   ```

### <a name="templatetabtemplate"></a>[Modello](#tab/template)
Usare un modello di Azure Resource Manager per abilitare il feed delle modifiche nell'account di archiviazione esistente tramite portale di Azure:

1. Nella portale di Azure scegliere **Crea una risorsa**.

2. In **Cerca nel Marketplace** digitare **distribuzione modelli** e quindi premere **INVIO**.

3. Scegliere **[Distribuisci un modello personalizzato](https://portal.azure.com/#create/Microsoft.Template)** , quindi scegliere **Compila modello personalizzato nell'editor**.

4. Nell'editor dei modelli incollare il codice JSON seguente. Sostituire il segnaposto `<accountName>` con il nome del proprio account di archiviazione.

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
    
5. Scegliere il pulsante **Salva** , specificare il gruppo di risorse dell'account, quindi scegliere il pulsante **Acquista** per distribuire il modello e abilitare il feed delle modifiche.

---

## <a name="consume-the-change-feed"></a>Utilizzare il feed delle modifiche

Il feed di modifiche produce diversi metadati e file di log. Questi file si trovano nel contenitore **$blobchangefeed** dell'account di archiviazione. 

> [!NOTE]
> Nella versione corrente il contenitore **$blobchangefeed** non è visibile in Azure Storage Explorer o portale di Azure. Attualmente non è possibile visualizzare il contenitore $blobchangefeed quando si chiama l'API ListContainers, ma è possibile chiamare l'API ListBlobs direttamente sul contenitore per visualizzare i BLOB.

Le applicazioni client possono utilizzare il feed delle modifiche utilizzando la libreria del processore dei feed delle modifiche BLOB fornita con l'SDK del processore dei feed delle modifiche. 

Vedere [elaborare i log dei feed delle modifiche nell'archivio BLOB di Azure](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Informazioni sull'organizzazione del feed delle modifiche

<a id="segment-index"></a>

### <a name="segments"></a>Segmenti

Il feed di modifiche è un log delle modifiche organizzate in *segmenti* orari, ma aggiunte e aggiornate a intervalli di pochi minuti. Questi segmenti vengono creati solo quando sono presenti eventi di modifica del BLOB che si verificano in quell'ora. Ciò consente all'applicazione client di utilizzare le modifiche che si verificano in intervalli di tempo specifici senza dover eseguire ricerche nell'intero log. Per altre informazioni, vedere le [specifiche](#specifications).

Un segmento orario disponibile del feed di modifiche viene descritto in un file manifesto che specifica i percorsi dei file del feed delle modifiche per il segmento. L'elenco della directory virtuale `$blobchangefeed/idx/segments/` Mostra questi segmenti ordinati in base all'ora. Il percorso del segmento descrive l'inizio dell'intervallo di tempo orario rappresentato dal segmento. È possibile utilizzare tale elenco per filtrare i segmenti di log che interessano.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> Il `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` viene creato automaticamente quando si Abilita il feed delle modifiche. Il file può essere ignorato in modo sicuro. Si tratta di un file di inizializzazione sempre vuoto. 

Il file manifesto del segmento (`meta.json`) Mostra il percorso dei file del feed delle modifiche per il segmento nella proprietà `chunkFilePaths`. Di seguito è riportato un esempio di un file manifesto del segmento.

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
> Il contenitore `$blobchangefeed` viene visualizzato solo dopo aver abilitato la funzionalità del feed delle modifiche nell'account. È necessario attendere alcuni minuti dopo aver abilitato il feed di modifiche prima di poter elencare i BLOB nel contenitore. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Modificare i record degli eventi

I file del feed delle modifiche contengono una serie di record di eventi di modifica. Ogni record dell'evento di modifica corrisponde a una modifica a un singolo BLOB. I record vengono serializzati e scritti nel file usando la specifica del formato [Apache avro](https://avro.apache.org/docs/1.8.2/spec.html) . È possibile leggere i record utilizzando la specifica del formato di file avro. Sono disponibili diverse librerie per elaborare i file in tale formato.

I file del feed di modifiche vengono archiviati nella directory virtuale `$blobchangefeed/log/` come [BLOB di Accodamento](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). Il primo file del feed delle modifiche in ogni percorso avrà `00000` nel nome del file, ad esempio `00000.avro`. Il nome di ogni file di log successivo aggiunto a tale percorso aumenterà di 1 (ad esempio: `00001.avro`).

Di seguito è riportato un esempio di record dell'evento di modifica dal file del feed delle modifiche convertito in JSON.

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

Per una descrizione di ogni proprietà, vedere [schema di eventi di griglia di eventi di Azure per l'archiviazione BLOB](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties).

> [!NOTE]
> I file del feed di modifiche per un segmento non vengono visualizzati immediatamente dopo la creazione di un segmento. La durata del ritardo rientra nell'intervallo normale di latenza di pubblicazione del feed di modifiche che è entro pochi minuti dalla modifica.

<a id="specifications"></a>

## <a name="specifications"></a>Specifiche

- I record degli eventi di modifica vengono aggiunti solo al feed delle modifiche. Una volta accodati, i record non sono modificabili e la posizione dei record è stabile. Le applicazioni client possono mantenere il proprio Checkpoint sulla posizione di lettura del feed di modifiche.

- I record degli eventi di modifica vengono aggiunti entro un ordine di pochi minuti della modifica. Le applicazioni client possono scegliere di utilizzare i record man mano che vengono accodati per l'accesso in streaming o in blocco in qualsiasi altro momento.

- I record degli eventi di modifica sono ordinati in base all'ordine di modifica **per BLOB**. L'ordine delle modifiche nei BLOB non è definito nell'archivio BLOB di Azure. Tutte le modifiche apportate a un segmento precedente sono prima delle modifiche nei segmenti successivi.

- I record degli eventi di modifica vengono serializzati nel file di log usando la specifica di formato [1.8.2 di Apache avro](https://avro.apache.org/docs/1.8.2/spec.html) .

- Modificare i record degli eventi in cui i `eventType` hanno un valore di `Control` sono record di sistema interni e non riflettono una modifica agli oggetti nell'account. È possibile ignorare tali record in modo sicuro.

- I valori nel contenitore delle proprietà `storageDiagnonstics` sono solo per uso interno e non sono progettati per l'uso da parte dell'applicazione. Le applicazioni non devono avere una dipendenza contrattuale sui dati. È possibile ignorare queste proprietà in modo sicuro.

- L'ora rappresentata dal segmento è **approssimativa** con limiti di 15 minuti. Quindi, per garantire l'utilizzo di tutti i record entro un periodo di tempo specificato, utilizzare il segmento consecutivo precedente e ora successiva.

- Ogni segmento può avere un numero diverso di `chunkFilePaths` a causa del partizionamento interno del flusso di log per gestire la velocità effettiva di pubblicazione. I file di log in ogni `chunkFilePath` possono contenere BLOB che si escludono a vicenda e possono essere utilizzati ed elaborati in parallelo senza violare l'ordinamento delle modifiche per ogni BLOB durante l'iterazione.

- I segmenti iniziano con lo stato `Publishing`. Una volta completata l'aggiunta dei record al segmento, questo verrà `Finalized`. I file di log in qualsiasi segmento con data successiva alla data della proprietà `LastConsumable` nel file di `$blobchangefeed/meta/Segments.json` non devono essere utilizzati dall'applicazione. Di seguito è riportato un esempio della proprietà `LastConsumable`in un file di `$blobchangefeed/meta/Segments.json`:

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

## <a name="register-your-subscription-preview"></a>Registrare la sottoscrizione (anteprima)

Poiché il feed delle modifiche è solo in anteprima pubblica, è necessario registrare la sottoscrizione per l'uso della funzionalità.

### <a name="register-by-using-powershell"></a>Eseguire la registrazione tramite PowerShell

In una console di PowerShell eseguire questi comandi:

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>Registrare usando l'interfaccia della riga di comando di Azure

In Azure Cloud Shell eseguire questi comandi:

```cli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>Condizioni e problemi noti (anteprima)

In questa sezione vengono descritti i problemi noti e le condizioni nell'anteprima pubblica corrente del feed di modifiche. 
- Per l'anteprima, è necessario prima [registrare la sottoscrizione](#register) prima di poter abilitare il feed delle modifiche per l'account di archiviazione nelle aree westcentralus o westus2. 
- Il feed delle modifiche acquisisce solo le operazioni di creazione, aggiornamento, eliminazione e copia. Gli aggiornamenti dei metadati non sono attualmente acquisiti nell'anteprima.
- I record degli eventi di modifica per ogni singola modifica possono apparire più di una volta nel feed di modifiche.
- Non è ancora possibile gestire la durata dei file di log del feed di modifiche impostando i criteri di conservazione basati sul tempo su di essi e non è possibile eliminare i BLOB 
- La proprietà `url` del file di log è attualmente sempre vuota.
- La proprietà `LastConsumable` del file Segments. JSON non elenca il primo segmento finalizzato dal feed di modifiche. Questo problema si verifica solo dopo la finalizzazione del primo segmento. Tutti i segmenti successivi dopo la prima ora vengono acquisiti in modo accurato nella proprietà `LastConsumable`.
- Attualmente non è possibile visualizzare il contenitore **$blobchangefeed** quando si chiama l'API ListContainers e il contenitore non viene visualizzato in portale di Azure o Storage Explorer
- Gli account di archiviazione che hanno avviato in precedenza un [failover dell'account](../common/storage-disaster-recovery-guidance.md) possono avere problemi con il file di log che non viene visualizzato. Eventuali failover futuri degli account potrebbero influito anche sul file di log durante l'anteprima.

## <a name="faq"></a>FAQ

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Qual è la differenza tra il feed delle modifiche e la registrazione Analisi archiviazione?
I log di Analytics includono record di tutte le operazioni di lettura, scrittura, elenco ed eliminazione con richieste riuscite e non riuscite in tutte le operazioni. I log di analisi sono il massimo sforzo e non è garantito alcun ordine.

Il feed delle modifiche è una soluzione che fornisce un log transazionale di mutazioni o modifiche riuscite al proprio account, ad esempio creazione, modifica ed eliminazione di BLOB. Il feed delle modifiche garantisce la registrazione e la visualizzazione di tutti gli eventi nell'ordine in cui sono state apportate modifiche per BLOB, pertanto non è necessario filtrare il rumore da un volume elevato di operazioni di lettura o di richieste non riuscite. Il feed di modifiche è progettato e ottimizzato per lo sviluppo di applicazioni che richiedono determinate garanzie.

### <a name="should-i-use-change-feed-or-storage-events"></a>È consigliabile usare il feed delle modifiche o gli eventi di archiviazione?
È possibile sfruttare entrambe le funzionalità perché [gli eventi di archiviazione BLOB](storage-blob-event-overview.md) e feed di modifiche forniscono le stesse informazioni con la stessa garanzia di affidabilità del recapito, con la differenza principale tra la latenza, l'ordinamento e l'archiviazione dei record degli eventi. Il feed di modifiche pubblica i record nel log entro pochi minuti dalla modifica e garantisce anche l'ordine delle operazioni di modifica per BLOB. Gli eventi di archiviazione vengono inseriti in tempo reale e potrebbero non essere ordinati. Gli eventi del feed di modifiche vengono archiviati in modo durevole nell'account di archiviazione come log stabili di sola lettura con la conservazione definita, mentre gli eventi di archiviazione sono temporanei che devono essere utilizzati dal gestore eventi a meno che non vengano archiviati in modo esplicito. Con il feed delle modifiche, un numero qualsiasi di applicazioni può utilizzare i log con le proprie comodità usando API o SDK BLOB. 

## <a name="next-steps"></a>Passaggi successivi

- Vedere un esempio di come leggere il feed delle modifiche usando un'applicazione client .NET. Vedere [elaborare i log dei feed delle modifiche nell'archivio BLOB di Azure](storage-blob-change-feed-how-to.md).
- Informazioni su come reagire agli eventi in tempo reale. Vedere [reagire a eventi di archiviazione BLOB](storage-blob-event-overview.md)
- Altre informazioni sulla registrazione dettagliata per le operazioni riuscite e non riuscite per tutte le richieste. Vedere [registrazione di analisi archiviazione di Azure](../common/storage-analytics-logging.md)
