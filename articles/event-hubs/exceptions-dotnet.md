---
title: Hub eventi di Azure-eccezioni .NET
description: Questo articolo fornisce un elenco delle eccezioni di messaggistica .NET di hub eventi di Azure e le azioni consigliate.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/23/2020
ms.author: shvija
ms.openlocfilehash: 6a1d7c969d31033ae9d00d212cc8f1a45abbeda1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91344572"
---
# <a name="eventhubsexception---net"></a>EventHubsException-.NET
Un **EventHubsException** viene attivato quando un'operazione specifica di hub eventi ha causato un problema, inclusi entrambi gli errori nel servizio e specifici per il client. 

## <a name="exception-information"></a>Informazioni sulle eccezioni
L'eccezione include le seguenti informazioni contestuali per facilitare la comprensione del contesto dell'errore e della relativa gravità. 

- **Transient**: indica se l'eccezione è considerata reversibile o meno. Nel caso in cui venisse considerato temporaneo, i criteri di ripetizione dei tentativi appropriati sono già stati applicati e i tentativi hanno avuto esito negativo.
- **Motivo**: fornisce un set di motivi noti per l'errore che consente di categorizzare e chiarire la causa principale. Questi motivi hanno lo scopo di consentire l'applicazione di filtri eccezioni e di altre logiche quando si esamina il testo di un messaggio di eccezione non sarebbe la soluzione ideale. Di seguito sono riportati alcuni motivi di errore chiave:
    - **Client chiuso**: si verifica quando un client di hub eventi che è già stato chiuso o eliminato. Si consiglia di controllare il codice dell'applicazione per assicurarsi che gli oggetti della libreria client di hub eventi vengano creati e chiusi nell'ambito previsto.
    - **Timeout servizio**: indica che il servizio Hub eventi non ha risposto a un'operazione entro il periodo di tempo previsto. Questo problema potrebbe essere stato causato da un problema di rete temporaneo o da un problema del servizio. È possibile che il servizio Hub eventi non abbia completato o meno la richiesta. lo stato non è noto. Si consiglia di provare a verificare lo stato corrente e riprovare, se necessario.
    - **Quota superata**: indica che sono presenti troppe operazioni di lettura attive per un singolo gruppo di consumer. Questo limite dipende dal livello dello spazio dei nomi di hub eventi e può essere necessario eseguire il passaggio a un livello superiore. In alternativa, è possibile creare gruppi di consumer aggiuntivi e assicurarsi che il numero di letture client del consumer per qualsiasi gruppo sia compreso nel limite. Per altre informazioni, vedere [quote e limiti di hub eventi di Azure](event-hubs-quotas.md).
    - **Dimensione del messaggio superata**: dati evento come dimensioni massime consentite sia per un singolo evento che per un batch di eventi. Include i dati dell'evento e tutti i metadati associati e l'overhead di sistema. Per correggere l'errore, ridurre il numero di eventi inviati in un batch o ridurre le dimensioni dei dati inclusi nel messaggio. Poiché i limiti delle dimensioni sono soggetti a modifiche, vedere [le quote e i limiti di hub eventi di Azure per le](event-hubs-quotas.md) specifiche.
    - **Consumer disconnesso**: un client utente è stato disconnesso dal servizio Hub eventi dall'istanza di hub eventi. In genere si verifica quando un consumer con un livello di proprietario superiore dichiara la proprietà di una partizione e di un gruppo di consumer.
    - **Risorsa non trovata**: il servizio Hub eventi non è riuscito a trovare una risorsa, ad esempio un hub eventi, un gruppo di consumer o una partizione. È possibile che sia stato eliminato o che si sia verificato un problema con il servizio Hub eventi stesso.

## <a name="handling-exceptions"></a>Gestione delle eccezioni
È possibile reagire a un motivo di errore specifico per il **EventHubException**  in diversi modi. Un modo consiste nell'applicare una clausola di filtro eccezioni come parte del blocco catch.

```csharp
try
{
    // Read events using the consumer client
}
catch (EventHubsException ex) where 
    (ex.Reason == EventHubsException.FailureReason.ConsumerDisconnected)
{
    // Take action based on a consumer being disconnected
}
```

## <a name="next-steps"></a>Passaggi successivi
Sono presenti altre eccezioni documentate nell' [articolo legacy](event-hubs-messaging-exceptions.md). Alcune di esse si applicano solo alla libreria client .NET legacy di hub eventi.