---
title: Panoramica della limitazione del bus di servizio di Azure | Microsoft Docs
description: 'Panoramica della limitazione del bus di servizio: livelli standard e Premium.'
services: service-bus-messaging
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 10/01/2019
ms.author: aschhab
ms.openlocfilehash: 21a3bfd09e83571e489e15e9351e12220a99e563
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301256"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Operazioni di limitazione sul bus di servizio di Azure

Le soluzioni cloud native offrono una nozione di risorse illimitate che possono essere ridimensionate con il carico di lavoro. Sebbene questo concetto sia più vero nel cloud rispetto ai sistemi locali, esistono ancora alcune limitazioni nel cloud.

Queste limitazioni possono causare la limitazione delle richieste di applicazioni client nei livelli standard e Premium, come descritto in questo articolo. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Limitazione nel livello standard del bus di servizio di Azure

Il livello standard del bus di servizio di Azure funziona come una configurazione multi-tenant con un modello di determinazione prezzi con pagamento in base al consumo. Qui più spazi dei nomi nello stesso cluster condividono le risorse allocate. Il livello standard è la scelta consigliata per gli ambienti di sviluppo, test e controllo di qualità insieme a sistemi di produzione a bassa velocità effettiva.

In passato, il bus di servizio di Azure aveva limiti di limitazione grossolani strettamente dipendenti dall'utilizzo delle risorse. Tuttavia, esiste un'opportunità per perfezionare la logica di limitazione delle richieste e fornire un comportamento di limitazione prevedibile a tutti gli spazi dei nomi che condividono queste risorse.

Nel tentativo di garantire un utilizzo equo e la distribuzione delle risorse in tutti gli spazi dei nomi standard del bus di servizio di Azure che usano le stesse risorse, la logica di limitazione delle richieste è stata modificata per essere basata sul credito.

> [!NOTE]
> È importante notare che la limitazione non è una ***novità*** per il bus di servizio di Azure o per qualsiasi servizio cloud nativo.
>
> La limitazione basata sul credito sta semplicemente ridefinendo il modo in cui vari spazi dei nomi condividono le risorse in un ambiente di livello standard multi-tenant e consentendo così l'utilizzo equo da parte di tutti gli spazi dei nomi che condividono le risorse

### <a name="what-is-credit-based-throttling"></a>Che cos'è la limitazione basata sul credito?

La limitazione basata sul credito limita il numero di operazioni che possono essere eseguite in un determinato spazio dei nomi in un periodo di tempo specifico. 

Di seguito è riportato il flusso di lavoro per la limitazione basata sul credito- 

  * All'inizio di ogni periodo di tempo, viene fornito un certo numero di crediti per ogni spazio dei nomi.
  * Qualsiasi operazione eseguita dalle applicazioni client mittente o destinatario verrà conteggiata in base a questi crediti e sottratta dai crediti disponibili.
  * Se i crediti sono esauriti, le operazioni successive verranno limitate fino all'inizio del periodo di tempo successivo.
  * I crediti vengono riempiti all'inizio del periodo di tempo successivo.

### <a name="what-are-the-credit-limits"></a>Quali sono i limiti di credito?

I limiti di credito sono attualmente impostati su "1000" crediti ogni secondo (per spazio dei nomi).

Non tutte le operazioni vengono create uguali. Ecco i costi di credito di ogni operazione: 

| Operazione | Costo del credito|
|-----------|-----------|
| Operazioni sui dati (Send, SendAsync, Receive, ReceiveAsync, PEEK) |1 credito per messaggio |
| Operazioni di gestione (creazione, lettura, aggiornamento, eliminazione su code, argomenti, sottoscrizioni, filtri) | 10 crediti |

### <a name="how-will-i-know-that-im-being-throttled"></a>Come è possibile verificare che la limitazione è stata limitata?

Quando le richieste dell'applicazione client vengono limitate, la risposta al server seguente verrà ricevuta dall'applicazione e registrata.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Come è possibile evitare la limitazione?

Con le risorse condivise è importante applicare un tipo di utilizzo equo tra i vari spazi dei nomi del bus di servizio che condividono tali risorse. La limitazione garantisce che tutti i picchi in un singolo carico di lavoro non provochino la limitazione di altri carichi di lavoro sulle stesse risorse.

Come indicato più avanti in questo articolo, non vi è alcun rischio di limitazione perché gli SDK client (e altre offerte Azure PaaS) hanno i criteri di ripetizione dei tentativi predefiniti incorporati. Tutte le richieste limitate verranno ritentate con backoff esponenziali e verranno esaminate al termine del rifornimento dei crediti.

Comprensibilmente, alcune applicazioni potrebbero essere riservate alla limitazione. In tal caso, è consigliabile [eseguire la migrazione dello spazio dei nomi standard del bus di servizio corrente a Premium](service-bus-migrate-standard-premium.md). 

Quando si esegue la migrazione, è possibile allocare risorse dedicate allo spazio dei nomi del bus di servizio e ridimensionare le risorse in modo appropriato se è presente un picco nel carico di lavoro e ridurre le probabilità di limitazione. Inoltre, quando il carico di lavoro viene ridotto ai livelli normali, è possibile ridurre le risorse allocate allo spazio dei nomi.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Limitazione nel livello Premium del bus di servizio di Azure

Il livello [Premium del bus di servizio di Azure](service-bus-premium-messaging.md) alloca risorse dedicate, in termini di unità di messaggistica, a ogni configurazione dello spazio dei nomi da parte del cliente. Queste risorse dedicate forniscono velocità effettiva prevedibile e latenza e sono consigliate per i sistemi con velocità effettiva elevata o livello di produzione sensibile.

Inoltre, il livello Premium consente ai clienti di aumentare la capacità di velocità effettiva quando si verificano picchi nel carico di lavoro.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Come funziona la limitazione delle richieste nel bus di servizio Premium?

Con l'allocazione di risorse esclusiva per il bus di servizio Premium, la limitazione è determinata solo dalle limitazioni delle risorse allocate allo spazio dei nomi.

Se il numero di richieste è superiore a quello delle risorse correnti, le richieste verranno limitate.

### <a name="how-will-i-know-that-im-being-throttled"></a>Come è possibile verificare che la limitazione è stata limitata?

Esistono diversi modi per identificare la limitazione in Azure Service Bus Premium: 
  * Le **richieste limitate** vengono visualizzate nelle metriche delle richieste di [monitoraggio di Azure](service-bus-metrics-azure-monitor.md#request-metrics) per identificare il numero di richieste limitate.
  * Un **utilizzo elevato della CPU** indica che l'allocazione delle risorse correnti è elevata e le richieste possono essere limitate se il carico di lavoro corrente non viene ridotto.
  * L' **utilizzo** elevato della memoria indica che l'allocazione delle risorse correnti è elevata e le richieste possono essere limitate in caso di riduzione del carico di lavoro corrente.

### <a name="how-can-i-avoid-being-throttled"></a>Come è possibile evitare la limitazione?

Poiché lo spazio dei nomi premium del bus di servizio dispone già di risorse dedicate, è possibile ridurre il numero di unità di messaggistica allocate allo spazio dei nomi nell'evento (o anticipazione) di un picco nel carico di lavoro.

Per ottenere la scalabilità verticale, è possibile creare [manuali operativi](../automation/automation-create-alert-triggered-runbook.md) che possono essere attivati dalle modifiche apportate alle metriche precedenti.

## <a name="faqs"></a>Domande frequenti

### <a name="how-does-throttling-affect-my-application"></a>In che modo la limitazione influisce sull'applicazione?

Quando una richiesta viene limitata, significa che il servizio è occupato perché si trova ad affrontare un numero maggiore di richieste rispetto a quelle consentite dalle risorse. Se la stessa operazione viene ritentata dopo alcuni istanti, una volta che il servizio ha lavorato con il carico di lavoro corrente, la richiesta può essere rispettata.

Poiché la limitazione è il comportamento previsto di qualsiasi servizio cloud nativo, la logica di ripetizione dei tentativi è stata compilata nell'SDK del bus di servizio di Azure. Il valore predefinito è impostato su ripetizione automatica con un back-off esponenziale per assicurarsi che la stessa richiesta venga limitata ogni volta.

La logica di ripetizione dei tentativi predefinita viene applicata a ogni operazione.

### <a name="does-throttling-result-in-data-loss"></a>La limitazione comporta la perdita di dati?

Il bus di servizio di Azure è ottimizzato per la persistenza. si garantisce che tutti i dati inviati al bus di servizio siano vincolati all'archiviazione prima che il servizio riconosca la riuscita della richiesta.

Una volta che la richiesta è stata "ACK" (riconosciuta) dal bus di servizio, significa che il bus di servizio ha elaborato correttamente la richiesta. Se il bus di servizio restituisce un'NACK ' (errore), significa che il bus di servizio non è stato in grado di elaborare la richiesta e che l'applicazione client deve ripetere la richiesta.

Tuttavia, quando una richiesta viene limitata, il servizio implica che non può accettare ed elaborare la richiesta in questo momento a causa di limitazioni delle risorse. Questo non implica alcun tipo di perdita di dati perché il bus di **servizio non ha** esaminato la richiesta. In questo caso, basandosi sui criteri di ripetizione dei tentativi predefiniti di Service Bus SDK si garantisce che la richiesta venga elaborata.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi e informazioni sull'uso della messaggistica del bus di servizio, vedere gli argomenti avanzati seguenti:

* [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
* [Avvio rapido: Inviare e ricevere messaggi usando il portale di Azure e .NET](service-bus-quickstart-portal.md)
* [Esercitazione: Aggiornare l'inventario usando il portale di Azure e argomenti/sottoscrizioni](service-bus-tutorial-topics-subscriptions-portal.md)

