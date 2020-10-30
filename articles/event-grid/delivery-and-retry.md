---
title: Recapito di Griglia di eventi di Azure e nuovi tentativi
description: Viene descritto in che modo Griglia di eventi di Azure recapita gli eventi e come gestisce i messaggi non recapitati.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 483a868022d4ae8f7c564e51344dfbede4314232
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042954"
---
# <a name="event-grid-message-delivery-and-retry"></a>Recapito di messaggi di Griglia di eventi e nuovi tentativi

Questo articolo descrive in che modo Griglia di eventi di Azure gestisce gli eventi quando il recapito non viene confermato.

Griglia di eventi fornisce il recapito durevole. Ogni messaggio viene recapitato almeno una volta per ogni sottoscrizione. Gli eventi vengono inviati immediatamente all'endpoint registrato di ogni sottoscrizione. Se un endpoint non conferma la ricezione di un evento, Griglia di eventi esegue nuovi tentativi di recapito dell'evento.

## <a name="batched-event-delivery"></a>Recapito di eventi in batch

Per impostazione predefinita, griglia di eventi invia ogni singolo evento ai sottoscrittori. Il sottoscrittore riceve una matrice con un singolo evento. È possibile configurare griglia di eventi per eseguire il batch degli eventi per il recapito per migliorare le prestazioni HTTP in scenari con velocità effettiva elevata.

Il recapito in batch ha due impostazioni:

* Numero massimo di **eventi per batch** : numero massimo di eventi che griglia di eventi recapita per batch. Questo numero non verrà mai superato, tuttavia è possibile recapitare un numero minore di eventi se non sono disponibili altri eventi al momento della pubblicazione. Griglia di eventi non ritarda gli eventi per creare un batch se sono disponibili meno eventi. Deve essere compreso tra 1 e 5.000.
* **Dimensioni batch preferite in kilobyte** : limite massimo di destinazione per le dimensioni del batch, in kilobyte. Analogamente agli eventi Max, le dimensioni del batch possono essere minori se al momento della pubblicazione non sono disponibili più eventi. *Se* un singolo evento è più grande della dimensione preferita, è possibile che un batch superi le dimensioni del batch preferite. Se, ad esempio, la dimensione preferita è 4 KB e viene effettuato il push di un evento da 10 KB a griglia di eventi, l'evento di 10 KB verrà comunque recapitato nel proprio batch anziché essere eliminato.

Il recapito in batch viene configurato in base a una sottoscrizione per ogni evento tramite il portale, l'interfaccia della riga di comando, PowerShell o gli SDK.

### <a name="azure-portal"></a>Portale di Azure: 
![Impostazioni di recapito batch](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Quando si crea una sottoscrizione di eventi, usare i parametri seguenti: 

- **Max-Events-per-batch** -numero massimo di eventi in un batch. Deve essere un numero compreso tra 1 e 5000.
- **preferenza-batch-dimensioni-in-kilobyte** -dimensioni batch preferite, in kilobyte. Deve essere un numero compreso tra 1 e 1024.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Per altre informazioni sull'uso dell'interfaccia della riga di comando di Azure con griglia di eventi, vedere [indirizzare eventi di archiviazione ad endpoint Web con l'interfaccia della riga](../storage/blobs/storage-blob-event-quickstart.md)

## <a name="retry-schedule-and-duration"></a>Pianificazione e durata della ripetizione

Griglia di eventi attende 30 secondi per una risposta dopo il recapito di un messaggio. Dopo 30 secondi, se l'endpoint non ha risposto, il messaggio viene accodato per riprovare. Griglia di eventi usa criteri per i tentativi di tipo backoff esponenziale per il recapito degli eventi. Griglia di eventi esegue un nuovo tentativo di recapito in base alla pianificazione seguente, in base al modo migliore:

- 10 secondi
- 30 secondi
- 1 minuto
- 5 minuti
- 10 minuti
- 30 minuti
- 1 ora
- Ogni ora per un massimo di 24 ore

Se l'endpoint risponde entro 3 minuti, griglia di eventi tenterà di rimuovere l'evento dalla coda dei tentativi in base a un massimo sforzo, ma i duplicati potrebbero comunque essere ricevuti.

Griglia di eventi aggiunge una piccola sequenza casuale a tutti i passaggi di ripetizione dei tentativi e può opportunisticamente ignorare alcuni tentativi se un endpoint è costantemente non integro, inattivo per un lungo periodo o sembra essere sovraccarico.

Per il comportamento deterministico, impostare la durata dell'evento su Live e i tentativi di recapito massimi nei [criteri di ripetizione della sottoscrizione](manage-event-delivery.md).

Per impostazione predefinita, Griglia di eventi fa scadere tutti gli eventi che non vengono recapitati entro 24 ore. Quando si crea una sottoscrizione di eventi, è possibile [personalizzare i criteri di ripetizione](manage-event-delivery.md). È necessario specificare il numero massimo di tentativi di recapito (il valore predefinito è 30) e la durata (TTL) dell'evento (il valore predefinito è 1440 minuti).

## <a name="delayed-delivery"></a>Recapito ritardato

Quando si verificano errori di recapito di un endpoint, griglia di eventi inizia a ritardare il recapito e riprovare gli eventi in tale endpoint. Se, ad esempio, i primi 10 eventi pubblicati in un endpoint hanno esito negativo, griglia di eventi presuppone che l'endpoint stia riscontrando problemi e ritarderà tutti i tentativi successivi *e i nuovi* recapiti per un certo periodo di tempo, in alcuni casi fino a diverse ore.

Lo scopo funzionale del recapito ritardato consiste nel proteggere gli endpoint non integri e il sistema di griglia di eventi. Senza il ritardi e il ritardo del recapito a endpoint non integri, i criteri di ripetizione dei tentativi e le funzionalità del volume di griglia di eventi possono sovraccaricare facilmente un sistema.

## <a name="dead-letter-events"></a>Eventi relativi ai messaggi non recapitabili
Quando griglia di eventi non è in grado di recapitare un evento entro un determinato periodo di tempo o dopo il tentativo di recapitare l'evento un certo numero di volte, può inviare l'evento non recapitato a un account di archiviazione. Questo processo è noto come **messaggio non recapitabile** . Griglia di eventi non recapitabili un evento quando viene soddisfatta **una delle condizioni seguenti** . 

- L'evento non viene recapitato entro il periodo **di durata (TTL** ). 
- Il **numero di tentativi** di recapitare l'evento ha superato il limite.

Se viene soddisfatta una delle condizioni, l'evento viene eliminato o non recapitabile.  Per impostazione predefinita, Griglia di eventi non attiva l'inserimento nella coda di eventi non recapitabili. Per abilitarlo, è necessario specificare che un account di archiviazione deve contenere gli eventi non recapitati durante la sottoscrizione di eventi. Per risolvere le operazioni di recapito, viene eseguito il pull degli eventi da questo account di archiviazione.

Griglia di eventi invia un evento nel percorso dell'evento non recapitato quando ha eseguito tutti i tentativi di ripetizione. Se Griglia di eventi riceve un codice di risposta 400 (Richiesta non valida) o 413 (Entità richiesta troppo grande), invia immediatamente l'evento per l'endpoint di messaggi non recapitabili. Questi codici di risposta indicano che recapito dell'evento non avrà mai esito positivo.

La scadenza della durata (TTL) viene controllata solo al successivo tentativo di recapito pianificato. Pertanto, anche se la durata (TTL) scade prima del successivo tentativo di recapito pianificato, la scadenza dell'evento viene verificata solo al momento del successivo recapito e successivamente a un messaggio non recapitabile. 

Tra l'ultimo tentativo di recapitare un evento e il momento in cui questo viene recapitato alla posizione dei messaggi non recapitabili, trascorre un intervallo di cinque minuti. Tale intervallo è volto a ridurre il numero di operazioni nell'archiviazione BLOB. Se la posizione dei messaggi non recapitabili non è disponibile per quattro ore, l'evento viene eliminato.

Prima di impostare la posizione dei messaggi non recapitabili, è necessario avere un account di archiviazione con un contenitore. L'endpoint di questo contenitore deve essere specificato durante la creazione della sottoscrizione di eventi. Di seguito è indicato il formato dell'endpoint: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Si potrebbe voler ricevere una notifica quando un evento è stato inviato al percorso di messaggi non recapitabili. Per usare Griglia di eventi per rispondere a eventi non recapitati, [creare una sottoscrizione di eventi](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) per l'archivio BLOB di eventi non recapitabili. Ogni volta che l'archivio BLOB riceve un evento non recapitato, Griglia di eventi invia una notifica al gestore. Il gestore risponde con le azioni da eseguire per la riconciliazione degli eventi non recapitati. Per un esempio di impostazione di un percorso di messaggi non recapitabili e di criteri di ripetizione dei tentativi, vedere la pagina relativa ai [criteri di ripetizione e](manage-event-delivery.md)

## <a name="delivery-event-formats"></a>Formati degli eventi di recapito
In questa sezione vengono forniti esempi di eventi e di eventi non recapitabili in formati dello schema di recapito diversi, ovvero schema di griglia di eventi, schema CloudEvents 1,0 e schema personalizzato. Per altre informazioni su questi formati, vedere gli articoli dello schema [griglia di eventi](event-schema.md) e [eventi Cloud 1,0](cloud-event-schema.md) . 

### <a name="event-grid-schema"></a>Schema di Griglia di eventi

#### <a name="event"></a>Evento 
```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/000000000-0000-0000-0000-00000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    }
}
```

#### <a name="dead-letter-event"></a>Evento del messaggio non recapitabile

```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    },

    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T17:18:14.0265758Z",
    "lastDeliveryAttemptTime": "2020-08-13T17:18:14.0465788Z" 
}
```

### <a name="cloudevents-10-schema"></a>Schema CloudEvents 1,0

#### <a name="event"></a>Evento

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    }
}
```

#### <a name="dead-letter-event"></a>Evento del messaggio non recapitabile

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    },

    "deadletterreason": "MaxDeliveryAttemptsExceeded",
    "deliveryattempts": 1,
    "lastdeliveryoutcome": "NotFound",
    "publishtime": "2020-08-13T21:21:36.4018726Z",
}
```

### <a name="custom-schema"></a>Schema personalizzato

#### <a name="event"></a>Evento

```json
{
    "prop1": "my property",
    "prop2": 5,
    "myEventType": "fooEventType"
}

```

#### <a name="dead-letter-event"></a>Evento del messaggio non recapitabile
```json
{
    "id": "8bc07e6f-0885-4729-90e4-7c3f052bd754",
    "eventTime": "2020-08-13T18:11:29.4121391Z",
    "eventType": "myEventType",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/00000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.EventGrid/topics/myCustomSchemaTopic",
    "subject": "subjectDefault",
  
    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T18:11:29.4121391Z",
    "lastDeliveryAttemptTime": "2020-08-13T18:11:29.4277644Z",
  
    "data": {
        "prop1": "my property",
        "prop2": 5,
        "myEventType": "fooEventType"
    }
}
```


## <a name="message-delivery-status"></a>Stato di recapito del messaggio

Griglia di eventi usa i codici di risposta HTTP per confermare la ricezione degli eventi. 

### <a name="success-codes"></a>Codici di riuscita

Griglia di eventi considera **solo** i codici di risposta HTTP seguenti come recapiti riusciti. Tutti gli altri codici di stato sono considerati recapiti non riusciti e verranno ripetuti o non recapitabile in base alle esigenze. Alla ricezione di un codice di stato positivo, griglia di eventi considera il completamento del recapito.

- 200 - OK
- 201 Creato
- 202 - Accettato
- 203 informazioni non autorevoli
- 204 No Content (Nessun contenuto)

### <a name="failure-codes"></a>Codici di errore

Tutti gli altri codici non inclusi nel set precedente (200-204) vengono considerati errori e verranno ripetuti. Per alcuni sono disponibili criteri di ripetizione dei tentativi specifici, tutti gli altri seguono il modello di back-off esponenziale standard. È importante tenere presente che, a causa della natura altamente parallela dell'architettura di griglia di eventi, il comportamento di ripetizione dei tentativi è non deterministico. 

| Codice di stato | Comportamento in caso di nuovo tentativo |
| ------------|----------------|
| 400 - Richiesta non valida | Riprovare dopo 5 minuti o più (DeadLetter immediatamente se il programma di installazione di DeadLetter) |
| 401 - Non autorizzato | Riprovare dopo 5 minuti o più |
| 403 - Accesso negato | Riprovare dopo 5 minuti o più |
| 404 - Non trovato | Riprovare dopo 5 minuti o più |
| 408 - Timeout richiesta | Riprovare dopo 2 minuti o più |
| 413 Entità della richiesta troppo grande | Riprovare dopo 10 secondi o più (DeadLetter immediatamente se l'installazione di DeadLetter) |
| 503 - Servizio non disponibile | Riprovare dopo 30 secondi o più |
| Tutti gli altri | Riprovare dopo 10 secondi o più |


## <a name="next-steps"></a>Passaggi successivi

* Per visualizzare lo stato di recapito degli eventi, vedere [Monitorare il recapito dei messaggio di Griglia di eventi di Azure](monitor-event-delivery.md).
* Per personalizzare le opzioni di recapito di eventi, vedere [Messaggi non recapitabili e criteri di ripetizione dei tentativi](manage-event-delivery.md).
