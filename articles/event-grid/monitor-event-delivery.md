---
title: Monitorare il recapito dei messaggio di Griglia di eventi di Azure
description: Descrive come monitorare il recapito dei messaggi di Griglia di eventi di Azure.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: f9719bb1f1563c55537c7ef32278411a2034bd75
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorare il recapito dei messaggio di Griglia di eventi 

Questo articolo descrive come usare il portale per controllare i recapiti di eventi.

Griglia di eventi fornisce il recapito durevole. Ogni messaggio viene recapitato almeno una volta per ogni sottoscrizione. Gli eventi vengono inviati immediatamente al webhook registrato di ogni sottoscrizione. Se un webhook non conferma la ricezione di un evento entro 60 secondi dal primo tentativo di recapito, Griglia di eventi esegue un nuovo tentativo di recapito dell'evento.

Per informazioni sul recapito di eventi e sui nuovi tentativi, vedere [Recapito di messaggi di Griglia di eventi e nuovi tentativi](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Metriche di recapito

Il portale visualizza le metriche per lo stato di recapito dei messaggi di evento.

Per gli argomenti le metriche sono:

* **Publish Succeeded** (Pubblicazione riuscita): l'evento è stato inviato all'argomento ed elaborato con una risposta 2xx.
* **Publish Failed** (Pubblicazione non riuscita): l'evento è stato inviato all'argomento, ma è stato rifiutato con un codice errore.
* **Unmatched** (Non abbinato): l'evento è stato pubblicato nell'argomento, ma non abbinato a una sottoscrizione di eventi. L'evento è stato eliminato.

Per le sottoscrizioni le metriche sono:

* **Delivery Succeeded** (Recapito riuscito): l'evento è stato recapitato all'endpoint della sottoscrizione ed è stato ricevuta una risposta 2xx.
* **Delivery Failed** (Recapito non riuscito): l'evento è stato inviato all'endpoint della sottoscrizione, ma è stata ricevuta una risposta 4xx o 5xx.
* **Expired Events** (Eventi scaduti): l'evento non è stato recapitato e sono inviati tutti i tentativi di ripetizione del recapito. L'evento è stato eliminato.
* **Matched Events** (Eventi abbinati): l'evento nell'argomento è stato abbinato dalla sottoscrizione di eventi.

## <a name="event-subscription-status"></a>Stato della sottoscrizione di eventi

Per visualizzare le metriche per una sottoscrizione di eventi, cercare **Sottoscrizioni Griglia di eventi** nei servizi disponibili e selezionarla.

![Cercare sottoscrizioni di eventi](./media/monitor-event-delivery/select-event-subscriptions.png)

Filtrare in base al tipo di evento, alla sottoscrizione e alla località. Selezionare **Metrica** per la sottoscrizione da visualizzare.

![Filtrare sottoscrizioni di eventi](./media/monitor-event-delivery/filter-events.png)

Visualizzare le metriche per l'argomento e la sottoscrizione di eventi.

![Visualizzare le metriche degli eventi](./media/monitor-event-delivery/subscription-metrics.png)

## <a name="custom-event-status"></a>Stato dell'evento personalizzato

Se è stato pubblicato un argomento personalizzato, è possibile visualizzare le relative metriche. Selezionare il gruppo di risorse contenente l'argomento e selezionare l'argomento.

![Selezionare l'argomento personalizzato](./media/monitor-event-delivery/select-custom-topic.png)

Visualizzare le metriche per l'argomento dell'evento personalizzato.

![Visualizzare le metriche degli eventi](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul recapito di eventi e sui nuovi tentativi, vedere [Recapito di messaggi di Griglia di eventi e nuovi tentativi](delivery-and-retry.md).
* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).
