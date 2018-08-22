---
title: Recapito di Griglia di eventi di Azure e nuovi tentativi
description: Viene descritto in che modo Griglia di eventi di Azure recapita gli eventi e come gestisce i messaggi non recapitati.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: tomfitz
ms.openlocfilehash: b34386a7b416d6f7d8b008a9cb5ef142948a370f
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005396"
---
# <a name="event-grid-message-delivery-and-retry"></a>Recapito di messaggi di Griglia di eventi e nuovi tentativi 

Questo articolo descrive in che modo Griglia di eventi di Azure gestisce gli eventi quando il recapito non viene confermato.

Griglia di eventi fornisce il recapito durevole. Ogni messaggio viene recapitato almeno una volta per ogni sottoscrizione. Gli eventi vengono inviati immediatamente al webhook registrato di ogni sottoscrizione. Se un webhook non conferma la ricezione di un evento entro 60 secondi dal primo tentativo di recapito, Griglia di eventi esegue un nuovo tentativo di recapito dell'evento. 

Attualmente, Griglia di eventi invia singolarmente ogni evento ai sottoscrittori. Il sottoscrittore riceve una matrice con un singolo evento.

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
- 414 - URI richiesta troppo lungo
- 429 - Numero eccessivo di richieste
- 500 - Errore interno del server
- 503 - Servizio non disponibile
- 504 - Timeout gateway

Se Griglia di eventi riceve un errore che indica che l'endpoint è temporaneamente non disponibile o che una richiesta futura potrebbe avere esito positivo, tenta nuovamente di inviare l'evento. Se Griglia di eventi riceve un errore che indica che il recapito non avrà esito positivo ed [è stato configurato un endpoint per i messaggi non recapitabili](manage-event-delivery.md), invia l'evento all'endpoint per i messaggi non recapitabili. 

## <a name="retry-intervals-and-duration"></a>Intervalli e durata dei tentativi

Griglia di eventi usa criteri per i tentativi di tipo backoff esponenziale per il recapito degli eventi. Se il webhook non risponde o restituisce un codice di errore, Griglia di eventi esegue un nuovo tentativo di recapito in base alla pianificazione seguente:

1. 10 secondi
2. 30 secondi
3. 1 minuto
4. 5 minuti
5. 10 minuti
6. 30 minuti
7. 1 ora

Griglia di eventi aggiunge una piccola parte di casualità a tutti gli intervalli tra tentativi. Dopo un'ora, il recapito degli eventi viene ripetuto una volta all'ora.

Per impostazione predefinita, Griglia di eventi fa scadere tutti gli eventi che non vengono recapitati entro 24 ore. Quando si crea una sottoscrizione di eventi, è possibile [personalizzare i criteri di ripetizione](manage-event-delivery.md). È necessario specificare il numero massimo di tentativi di recapito (il valore predefinito è 30) e la durata (TTL) dell'evento (il valore predefinito è 1440 minuti).

## <a name="dead-letter-events"></a>Eventi relativi ai messaggi non recapitabili

Quando Griglia di eventi non riesce a eseguire un'operazione di recapito, l'evento non recapitato può essere inviato a un account di archiviazione. Questo processo è noto come inserimento nella coda di eventi non recapitabili. Per visualizzare gli eventi non recapitabili, è possibile effettuare il pull dalla posizione degli eventi non recapitabili. Per altre informazioni, vedere [Messaggi non recapitabili e criteri di ripetizione dei tentativi](manage-event-delivery.md).

## <a name="next-steps"></a>Passaggi successivi

* Per visualizzare lo stato di recapito degli eventi, vedere [Monitorare il recapito dei messaggio di Griglia di eventi di Azure](monitor-event-delivery.md).
* Per personalizzare le opzioni di recapito degli eventi, vedere [Gestire le impostazioni di recapito di Griglia di eventi](manage-event-delivery.md).
* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).