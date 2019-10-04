---
title: Recapito di Griglia di eventi di Azure e nuovi tentativi
description: Viene descritto in che modo Griglia di eventi di Azure recapita gli eventi e come gestisce i messaggi non recapitati.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: spelluru
ms.openlocfilehash: 0945b06f78ac34500f0b16a4a419cff12d1a4734
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812914"
---
# <a name="event-grid-message-delivery-and-retry"></a>Recapito di messaggi di Griglia di eventi e nuovi tentativi

Questo articolo descrive in che modo Griglia di eventi di Azure gestisce gli eventi quando il recapito non viene confermato.

Griglia di eventi fornisce il recapito durevole. Ogni messaggio viene recapitato almeno una volta per ogni sottoscrizione. Gli eventi vengono inviati immediatamente all'endpoint registrato di ogni sottoscrizione. Se un endpoint non conferma la ricezione di un evento, Griglia di eventi esegue nuovi tentativi di recapito dell'evento.

Attualmente, Griglia di eventi invia singolarmente ogni evento ai sottoscrittori. Il sottoscrittore riceve una matrice con un singolo evento.

## <a name="retry-schedule-and-duration"></a>Pianificazione e durata della ripetizione

Griglia di eventi attende 30 secondi per una risposta dopo la distribuzione di un messaggio. Dopo 30 secondi, se l'endpoint non ha risposto, il messaggio viene accodato per la ripetizione dei tentativi. Griglia di eventi usa criteri per i tentativi di tipo backoff esponenziale per il recapito degli eventi. Griglia di eventi di tentativi di recapito in base alla pianificazione seguente in base ad approssimazioni ottimali:

- 10 secondi
- 30 secondi
- 1 minuto
- 5 minuti
- 10 minuti
- 30 minuti
- 1 ora
- Su base oraria per fino a 24 ore

Se l'endpoint risponde entro 3 minuti, griglia di eventi proverà a rimuovere l'evento dalla coda di tentativi in base ad approssimazioni ottimali, ma è comunque possibile ricevere i duplicati.

Griglia di eventi aggiunge una piccola parte di casualità a tutti i passaggi di ripetizione dei tentativi e può, in base alle esigenze, ignorare determinati tentativi se un endpoint è coerente non integro, verso il basso per un lungo periodo o sembra essere sovraccaricato.

Per un comportamento deterministico, impostare l'ora dell'evento durata (TTL) e i tentativi di recapito massimo nel [i criteri di ripetizione dei tentativi di sottoscrizione](manage-event-delivery.md).

Per impostazione predefinita, Griglia di eventi fa scadere tutti gli eventi che non vengono recapitati entro 24 ore. Quando si crea una sottoscrizione di eventi, è possibile [personalizzare i criteri di ripetizione](manage-event-delivery.md). È necessario specificare il numero massimo di tentativi di recapito (il valore predefinito è 30) e la durata (TTL) dell'evento (il valore predefinito è 1440 minuti).

## <a name="delayed-delivery"></a>Recapito ritardato

Come un endpoint di esperienze di errori di recapito, griglia di eventi inizierà a ritardare il recapito e i nuovi tentativi di eventi verso tale endpoint. Ad esempio, se i primi dieci eventi pubblicati in un endpoint hanno esito negativo, griglia di eventi presupporrà che l'endpoint si è verificati problemi e ritarderà tutti i tentativi successivi *nuovi e* recapiti per un certo tempo, in alcuni casi fino a diverse ore .

Lo scopo di funzionalità di recapito ritardato consiste nella protezione endpoint non integri, nonché il sistema griglia di eventi. Senza eseguire il backoff e il ritardo della consegna agli endpoint non integri, criteri di ripetizione della griglia di eventi e le funzionalità di volume possono facilmente sovraccaricare un sistema.

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

Griglia di eventi considera **solo** i seguenti codici di risposta HTTP come recapiti riusciti. Tutti gli altri stato codici sono considerati non riusciti durante i recapiti e verranno ritentati o non recapitabile come appropriato. Dopo aver ricevuto un codice di stato di esito positivo, griglia di eventi considera il recapito completato.

- 200 - OK
- 201 Creato
- 202 - Accettato
- Informazioni non autorevoli 203
- 204 No Content (Nessun contenuto)

### <a name="failure-codes"></a>Codici di errore

Tutti gli altri codici non nel set precedente (200 204) vengono considerati come errori e verranno ritentate. Alcuni dispongono di criteri di ripetizione specifiche associati a tali procedure elencate di seguito, tutti gli altri segue il modello standard di esponenziale backoff. È importante tenere presente che, a causa della natura parallelizzazione elevata dell'architettura della griglia di eventi, il comportamento di ripetizione dei tentativi è non deterministico. 

| status code | Comportamento in caso di nuovo tentativo |
| ------------|----------------|
| 400 - Richiesta non valida | Nuovo tentativo dopo 5 minuti o più (messaggi non recapitabili immediatamente se il programma di installazione di messaggi non recapitabili) |
| 401 - Non autorizzato | Nuovo tentativo dopo 5 minuti o più |
| 403 - Accesso negato | Nuovo tentativo dopo 5 minuti o più |
| 404 - Non trovato | Nuovo tentativo dopo 5 minuti o più |
| 408 - Timeout richiesta | Nuovo tentativo dopo 2 minuti o più |
| 413 Entità della richiesta troppo grande | Riprova dopo 10 secondi o più (messaggi non recapitabili immediatamente se il programma di installazione di messaggi non recapitabili) |
| 503 - Servizio non disponibile | Nuovo tentativo dopo 30 secondi o più |
| Tutti gli altri | Riprova dopo 10 secondi o più |


## <a name="next-steps"></a>Passaggi successivi

* Per visualizzare lo stato di recapito degli eventi, vedere [Monitorare il recapito dei messaggio di Griglia di eventi di Azure](monitor-event-delivery.md).
* Per personalizzare le opzioni di recapito di eventi, vedere [Messaggi non recapitabili e criteri di ripetizione dei tentativi](manage-event-delivery.md).
