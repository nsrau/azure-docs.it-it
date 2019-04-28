---
title: Recapito di Griglia di eventi di Azure e nuovi tentativi
description: Viene descritto in che modo Griglia di eventi di Azure recapita gli eventi e come gestisce i messaggi non recapitati.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: spelluru
ms.openlocfilehash: 6dfa84eff8dcc104ae6f9c16262f3b1c697df6c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561999"
---
# <a name="event-grid-message-delivery-and-retry"></a>Recapito di messaggi di Griglia di eventi e nuovi tentativi

Questo articolo descrive in che modo Griglia di eventi di Azure gestisce gli eventi quando il recapito non viene confermato.

Griglia di eventi fornisce il recapito durevole. Ogni messaggio viene recapitato almeno una volta per ogni sottoscrizione. Gli eventi vengono inviati immediatamente all'endpoint registrato di ogni sottoscrizione. Se un endpoint non conferma la ricezione di un evento, Griglia di eventi esegue nuovi tentativi di recapito dell'evento.

Attualmente, Griglia di eventi invia singolarmente ogni evento ai sottoscrittori. Il sottoscrittore riceve una matrice con un singolo evento.

## <a name="retry-schedule-and-duration"></a>Pianificazione e durata della ripetizione

Griglia di eventi usa criteri per i tentativi di tipo backoff esponenziale per il recapito degli eventi. Se un endpoint non risponde o restituisce un codice di errore, griglia di eventi di tentativi di recapito in base alla pianificazione seguente in base ad approssimazioni ottimali:

1. 10 secondi
1. 30 secondi
1. 1 minuto
1. 5 minuti
1. 10 minuti
1. 30 minuti
1. 1 ora
1. Su base oraria per fino a 24 ore

Griglia di eventi aggiunge una piccola parte di casualità a tutti i passaggi di ripetizione dei tentativi e può, in base alle esigenze, ignorare determinati tentativi se un endpoint è coerente non integro, verso il basso per un lungo periodo o sembra essere sovraccaricato.

Per un comportamento deterministico, impostare l'ora dell'evento durata (TTL) e i tentativi di recapito massimo nel [i criteri di ripetizione dei tentativi di sottoscrizione](manage-event-delivery.md).

Per impostazione predefinita, Griglia di eventi fa scadere tutti gli eventi che non vengono recapitati entro 24 ore. Quando si crea una sottoscrizione di eventi, è possibile [personalizzare i criteri di ripetizione](manage-event-delivery.md). È necessario specificare il numero massimo di tentativi di recapito (il valore predefinito è 30) e la durata (TTL) dell'evento (il valore predefinito è 1440 minuti).

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

I codici di risposta HTTP seguenti indicano che un evento è stato recapitato correttamente al webhook. Griglia di eventi considera il recapito completato.

- 200 - OK
- 202 - Accettato

### <a name="failure-codes"></a>Codici di errore

I codici di risposta HTTP seguenti indicano che un tentativo di recapito di un evento non è riuscito.

- 400 - Richiesta non valida
- 401 - Non autorizzato
- 404 - Non trovato
- 408 - Timeout richiesta
- 413 Entità della richiesta troppo grande
- 414 - URI richiesta troppo lungo
- 429 - Numero eccessivo di richieste
- 500 - Errore interno del server
- 503 - Servizio non disponibile
- 504 - Timeout gateway

## <a name="next-steps"></a>Passaggi successivi

* Per visualizzare lo stato di recapito degli eventi, vedere [Monitorare il recapito dei messaggio di Griglia di eventi di Azure](monitor-event-delivery.md).
* Per personalizzare le opzioni di recapito di eventi, vedere [Messaggi non recapitabili e criteri di ripetizione dei tentativi](manage-event-delivery.md).