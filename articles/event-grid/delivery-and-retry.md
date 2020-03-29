---
title: Recapito di Griglia di eventi di Azure e nuovi tentativi
description: Viene descritto in che modo Griglia di eventi di Azure recapita gli eventi e come gestisce i messaggi non recapitati.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: spelluru
ms.openlocfilehash: dda2fd98c4c0d330059156a5ec00baa97ffaf627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921063"
---
# <a name="event-grid-message-delivery-and-retry"></a>Recapito di messaggi di Griglia di eventi e nuovi tentativi

Questo articolo descrive in che modo Griglia di eventi di Azure gestisce gli eventi quando il recapito non viene confermato.

Griglia di eventi fornisce il recapito durevole. Ogni messaggio viene recapitato almeno una volta per ogni sottoscrizione. Gli eventi vengono inviati immediatamente all'endpoint registrato di ogni sottoscrizione. Se un endpoint non conferma la ricezione di un evento, Griglia di eventi esegue nuovi tentativi di recapito dell'evento.

## <a name="batched-event-delivery"></a>Recapito di eventi in batch

La griglia di eventi per impostazione predefinita invia ogni evento singolarmente ai sottoscrittori. Il sottoscrittore riceve una matrice con un singolo evento. È possibile configurare Griglia di eventi per gli eventi batch per il recapito per migliorare le prestazioni HTTP in scenari ad alta velocità effettiva.

Il recapito in batch ha due impostazioni:

* **Numero massimo di eventi per batch** - Numero massimo di eventi che griglia di eventi recapita per batch. Questo numero non verrà mai superato, tuttavia possono essere recapitati meno eventi se al momento della pubblicazione non sono disponibili altri eventi. Griglia di eventi non ritarda gli eventi per creare un batch se sono disponibili un numero inferiore di eventi. Deve essere compreso tra 1 e 5.000.
* **Dimensioni batch preferite in kilobyte** : limite di livello di destinazione per la dimensione del batch in kilobyte. Analogamente agli eventi max, la dimensione del batch può essere inferiore se non sono disponibili più eventi al momento della pubblicazione. È possibile che un batch sia maggiore della dimensione preferita *se* un singolo evento è maggiore della dimensione preferita. Ad esempio, se la dimensione preferita è 4 KB e un evento di 10 KB viene inviato a Griglia di eventi, l'evento di 10 KB verrà comunque recapitato nel proprio batch anziché essere eliminato.

Recapito in batch in configurato in base alla sottoscrizione per eventi tramite il portale, l'interfaccia della riga di comando, PowerShell o SDK.

### <a name="azure-portal"></a>Portale di Azure: 
![Impostazioni di recapito batch](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Quando si crea una sottoscrizione di eventi, utilizzare i parametri seguenti:When creating an event subscription, use the following parameters: 

- **max-events-per-batch** - Numero massimo di eventi in un batch. Deve essere un numero compreso tra 1 e 5000.
- **preferred-batch-size-in-kilobytes** - Dimensione batch preferita in kilobyte. Deve essere un numero compreso tra 1 e 1024.

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

Per altre informazioni sull'uso dell'interfaccia della riga di comando di Azure con Griglia di eventi, vedere [Instradare gli eventi di archiviazione all'endpoint Web con l'interfaccia della riga di comando](../storage/blobs/storage-blob-event-quickstart.md)di Azure.For more information on using Azure CLI with Event Grid, see Route storage events to web endpoint with Azure CLI.

## <a name="retry-schedule-and-duration"></a>Pianificazione e durata della ripetizione

Griglia di eventi attende 30 secondi per una risposta dopo il recapito di un messaggio. Dopo 30 secondi, se l'endpoint non ha risposto, il messaggio viene accodato per un nuovo tentativo. Griglia di eventi usa criteri per i tentativi di tipo backoff esponenziale per il recapito degli eventi. Griglia di eventi ritenta il recapito in base alla pianificazione seguente in base al massimo sforzo:

- 10 secondi
- 30 secondi
- 1 minuto
- 5 minuti
- 10 minuti
- 30 minuti
- 1 ora
- Ogni ora per un massimo di 24 ore

Se l'endpoint risponde entro 3 minuti, Griglia di eventi tenterà di rimuovere l'evento dalla coda dei tentativi con il massimo sforzo, ma potrebbero comunque essere ricevuti duplicati.

Griglia di eventi aggiunge una piccola randomizzazione a tutti i passaggi di ripetizione dei tentativi e può opportunisticamente ignorare alcuni tentativi se un endpoint è costantemente integro, verso il basso per un lungo periodo o sembra essere sopraffatto.

Per un comportamento deterministico, impostare il tempo di visualizzazione dell'evento per visualizzare o max i tentativi di recapito nei [criteri di ripetizione dei tentativi](manage-event-delivery.md)di sottoscrizione.

Per impostazione predefinita, Griglia di eventi fa scadere tutti gli eventi che non vengono recapitati entro 24 ore. Quando si crea una sottoscrizione di eventi, è possibile [personalizzare i criteri di ripetizione](manage-event-delivery.md). È necessario specificare il numero massimo di tentativi di recapito (il valore predefinito è 30) e la durata (TTL) dell'evento (il valore predefinito è 1440 minuti).

## <a name="delayed-delivery"></a>Consegna ritardata

Quando un endpoint si verifica errori di recapito, Griglia di eventi inizierà a ritardare il recapito e riprovare gli eventi a tale endpoint. Ad esempio, se i primi 10 eventi pubblicati in un endpoint hanno esito negativo, Griglia di eventi presupporrà che l'endpoint stia riscontrando problemi e ritarderà tutti i tentativi successivi e i *nuovi* recapiti per un certo periodo di tempo, in alcuni casi fino a diverse ore.

Lo scopo funzionale del recapito ritardato è proteggere gli endpoint non integri e il sistema Griglia di eventi. Senza il back-off e il ritardo del recapito a endpoint non integri, i criteri di ripetizione dei tentativi e le funzionalità di volume di Griglia di eventi possono facilmente sovraccaricare un sistema.

## <a name="dead-letter-events"></a>Eventi relativi ai messaggi non recapitabili

Quando Griglia di eventi non riesce a eseguire un'operazione di recapito, l'evento non recapitato può essere inviato a un account di archiviazione. Questo processo è noto come inserimento nella coda di eventi non recapitabili. Per impostazione predefinita, Griglia di eventi non attiva l'inserimento nella coda di eventi non recapitabili. Per abilitarlo, è necessario specificare che un account di archiviazione deve contenere gli eventi non recapitati durante la sottoscrizione di eventi. Per risolvere le operazioni di recapito, viene eseguito il pull degli eventi da questo account di archiviazione.

Griglia di eventi invia un evento nel percorso dell'evento non recapitato quando ha eseguito tutti i tentativi di ripetizione. Se Griglia di eventi riceve un codice di risposta 400 (Richiesta non valida) o 413 (Entità richiesta troppo grande), invia immediatamente l'evento per l'endpoint di messaggi non recapitabili. Questi codici di risposta indicano che recapito dell'evento non avrà mai esito positivo.

Tra l'ultimo tentativo di recapitare un evento e il momento in cui questo viene recapitato alla posizione dei messaggi non recapitabili, trascorre un intervallo di cinque minuti. Tale intervallo è volto a ridurre il numero di operazioni nell'archiviazione BLOB. Se la posizione dei messaggi non recapitabili non è disponibile per quattro ore, l'evento viene eliminato.

Prima di impostare la posizione dei messaggi non recapitabili, è necessario avere un account di archiviazione con un contenitore. L'endpoint di questo contenitore deve essere specificato durante la creazione della sottoscrizione di eventi. Di seguito è indicato il formato dell'endpoint: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Si potrebbe voler ricevere una notifica quando un evento è stato inviato al percorso di messaggi non recapitabili. Per usare Griglia di eventi per rispondere a eventi non recapitati, [creare una sottoscrizione di eventi](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) per l'archivio BLOB di eventi non recapitabili. Ogni volta che l'archivio BLOB riceve un evento non recapitato, Griglia di eventi invia una notifica al gestore. Il gestore risponde con le azioni da eseguire per la riconciliazione degli eventi non recapitati.

Per un esempio di configurazione di un percorso di messaggi non recapitabili, vedere [Messaggi non recapitabili e criteri di ripetizione dei tentativi](manage-event-delivery.md).

## <a name="message-delivery-status"></a>Stato di recapito del messaggio

Griglia di eventi usa i codici di risposta HTTP per confermare la ricezione degli eventi. 

### <a name="success-codes"></a>Codici di riuscita

Griglia di eventi considera **solo** i seguenti codici di risposta HTTP come recapiti riusciti. Tutti gli altri codici di stato sono considerati consegne non riuscite e verranno ritentati o non recapitati in base alle esigenze. Dopo aver ricevuto un codice di stato corretto, Griglia di eventi considera il recapito completato.

- 200 - OK
- 201 Creato
- 202 - Accettato
- 203 Informazioni non autorevoli
- 204 No Content (Nessun contenuto)

### <a name="failure-codes"></a>Codici di errore

Tutti gli altri codici non presenti nel set precedente (200-204) sono considerati errori e verranno ritentati. Alcuni hanno specifici criteri di ripetizione dei tentativi legati a loro descritti di seguito, tutti gli altri seguono il modello di back-off esponenziale standard. È importante tenere presente che, a causa della natura altamente parallelizzata dell'architettura di Griglia di eventi, il comportamento di ripetizione dei tentativi non è deterministico. 

| Codice di stato | Comportamento in caso di nuovo tentativo |
| ------------|----------------|
| 400 - Richiesta non valida | Riprova dopo 5 minuti o più (Deadletter immediatamente se la configurazione del deadletter) |
| 401 - Non autorizzato | Riprova dopo 5 minuti o più |
| 403 - Accesso negato | Riprova dopo 5 minuti o più |
| 404 - Non trovato | Riprova dopo 5 minuti o più |
| 408 - Timeout richiesta | Riprova dopo 2 minuti o più |
| 413 Entità della richiesta troppo grande | Riprova dopo 10 secondi o più (Deadletter immediatamente se la configurazione del deadletter) |
| 503 - Servizio non disponibile | Riprova dopo 30 secondi o più |
| Tutti gli altri | Riprova dopo 10 secondi o più |


## <a name="next-steps"></a>Passaggi successivi

* Per visualizzare lo stato di recapito degli eventi, vedere [Monitorare il recapito dei messaggio di Griglia di eventi di Azure](monitor-event-delivery.md).
* Per personalizzare le opzioni di recapito di eventi, vedere [Messaggi non recapitabili e criteri di ripetizione dei tentativi](manage-event-delivery.md).
