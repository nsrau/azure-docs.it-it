---
title: Recapito di Griglia di eventi di Azure e nuovi tentativi
description: Viene descritto in che modo Griglia di eventi di Azure recapita gli eventi e come gestisce i messaggi non recapitati.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: spelluru
ms.openlocfilehash: 483b8251bf17eaa5fe7aa7cbd86299575535725d
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170063"
---
# <a name="event-grid-message-delivery-and-retry"></a>Recapito di messaggi di Griglia di eventi e nuovi tentativi

Questo articolo descrive in che modo Griglia di eventi di Azure gestisce gli eventi quando il recapito non viene confermato.

Griglia di eventi fornisce il recapito durevole. Ogni messaggio viene recapitato almeno una volta per ogni sottoscrizione. Gli eventi vengono inviati immediatamente all'endpoint registrato di ogni sottoscrizione. Se un endpoint non conferma la ricezione di un evento, Griglia di eventi esegue nuovi tentativi di recapito dell'evento.

## <a name="batched-event-delivery"></a>Recapito di eventi in batch

Per impostazione predefinita, griglia di eventi invia ogni singolo evento ai sottoscrittori. Il sottoscrittore riceve una matrice con un singolo evento. È possibile configurare griglia di eventi per eseguire il batch degli eventi per il recapito per migliorare le prestazioni HTTP in scenari con velocità effettiva elevata.

Il recapito in batch ha due impostazioni:

* Il numero massimo di **eventi per batch** è il numero massimo di eventi che griglia di eventi verrà recapitato per batch. Questo numero non verrà mai superato, tuttavia è possibile recapitare un numero minore di eventi se non sono disponibili altri eventi al momento della pubblicazione. Griglia di eventi non ritarda gli eventi per creare un batch se sono disponibili meno eventi. Deve essere compreso tra 1 e 5.000.
* **Dimensioni batch preferite in kilobyte** è il limite di destinazione per le dimensioni del batch, in kilobyte. Analogamente agli eventi Max, le dimensioni del batch possono essere minori se al momento della pubblicazione non sono disponibili più eventi. *Se* un singolo evento è più grande della dimensione preferita, è possibile che un batch superi le dimensioni del batch preferite. Se, ad esempio, la dimensione preferita è 4 KB e viene effettuato il push di un evento da 10 KB a griglia di eventi, l'evento di 10 KB verrà comunque recapitato nel proprio batch anziché essere eliminato.

Il recapito in batch viene configurato in base a una sottoscrizione per ogni evento tramite il portale, l'interfaccia della riga di comando, PowerShell o gli SDK.

![Impostazioni di recapito batch](./media/delivery-and-retry/batch-settings.png)

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

Quando Griglia di eventi non riesce a eseguire un'operazione di recapito, l'evento non recapitato può essere inviato a un account di archiviazione. Questo processo è noto come inserimento nella coda di eventi non recapitabili. Per impostazione predefinita, Griglia di eventi non attiva l'inserimento nella coda di eventi non recapitabili. Per abilitarlo, è necessario specificare che un account di archiviazione deve contenere gli eventi non recapitati durante la sottoscrizione di eventi. Per risolvere le operazioni di recapito, viene eseguito il pull degli eventi da questo account di archiviazione.

Griglia di eventi invia un evento nel percorso dell'evento non recapitato quando ha eseguito tutti i tentativi di ripetizione. Se Griglia di eventi riceve un codice di risposta 400 (Richiesta non valida) o 413 (Entità richiesta troppo grande), invia immediatamente l'evento per l'endpoint di messaggi non recapitabili. Questi codici di risposta indicano che recapito dell'evento non avrà mai esito positivo.

Tra l'ultimo tentativo di recapitare un evento e il momento in cui questo viene recapitato alla posizione dei messaggi non recapitabili, trascorre un intervallo di cinque minuti. Tale intervallo è volto a ridurre il numero di operazioni nell'archiviazione BLOB. Se la posizione dei messaggi non recapitabili non è disponibile per quattro ore, l'evento viene eliminato.

Prima di impostare la posizione dei messaggi non recapitabili, è necessario avere un account di archiviazione con un contenitore. L'endpoint di questo contenitore deve essere specificato durante la creazione della sottoscrizione di eventi. Di seguito è indicato il formato dell'endpoint: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Si potrebbe voler ricevere una notifica quando un evento è stato inviato al percorso di messaggi non recapitabili. Per usare Griglia di eventi per rispondere a eventi non recapitati, [creare una sottoscrizione di eventi](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) per l'archivio BLOB di eventi non recapitabili. Ogni volta che l'archivio BLOB riceve un evento non recapitato, Griglia di eventi invia una notifica al gestore. Il gestore risponde con le azioni da eseguire per la riconciliazione degli eventi non recapitati.

Per un esempio di configurazione di un percorso di messaggi non recapitabili, vedere [Messaggi non recapitabili e criteri di ripetizione dei tentativi](manage-event-delivery.md).

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
