---
title: Panoramica della limitazione del bus di servizio di Azure - Documenti Microsoft
description: Panoramica della limitazione delle richieste del bus di servizio - livelli Standard e Premium.Overview of Service Bus throttling - Standard and Premium tiers.
services: service-bus-messaging
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 10/01/2019
ms.author: aschhab
ms.openlocfilehash: f852ad70b2eb97e2b8b3e40d086e98b3836c3592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598290"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Limitazione delle operazioni nel bus di servizio di AzureThrottling operations on Azure Service Bus

Le soluzioni native cloud offrono una nozione di risorse illimitate che possono essere scalate con il carico di lavoro. Anche se questa nozione è più vera nel cloud che con i sistemi locali, esistono ancora limitazioni nel cloud.

Queste limitazioni possono causare la limitazione delle richieste di applicazioni client nei livelli Standard e Premium, come descritto in questo articolo. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Limitazione nel livello Standard del bus di servizio di AzureThrottling in Azure Service Bus Standard tier

Il livello Standard del bus di servizio di Azure funziona come una configurazione multi-tenant con un modello di prezzo con pagamento in base al consumo. In questo caso più spazi dei nomi nello stesso cluster condividono le risorse allocate. Il livello Standard è la scelta consigliata per gli ambienti di sviluppo, test e controllo qualità insieme a sistemi di produzione a bassa velocità effettiva.

In passato, i limiti di limitazione del bus di Azure erano limitati a dipendere esclusivamente dall'utilizzo delle risorse. Tuttavia, è possibile perfezionare la logica di limitazione e fornire un comportamento di limitazione prevedibile a tutti gli spazi dei nomi che condividono queste risorse.

Nel tentativo di garantire un utilizzo equo e la distribuzione delle risorse in tutti gli spazi dei nomi standard del bus di servizio di Azure che usano le stesse risorse, la logica di limitazione è stata modificata per essere basata sul credito.

> [!NOTE]
> È importante notare che la limitazione delle richieste non è ***una novità*** del bus di servizio di Azure o di qualsiasi servizio nativo cloud.
>
> La limitazione basata sul credito consiste semplicemente nel perfezionare il modo in cui vari spazi dei nomi condividono le risorse in un ambiente di livello Standard multi-tenant e consentono quindi un utilizzo equo da parte di tutti gli spazi dei nomi che condividono le risorse.

### <a name="what-is-credit-based-throttling"></a>Che cos'è la limitazione basata sul credito?

La limitazione basata sul credito limita il numero di operazioni che possono essere eseguite su un determinato spazio dei nomi in un periodo di tempo specifico. 

Di seguito è riportato il flusso di lavoro per la limitazione delle richieste basate sul credito - 

  * All'inizio di ogni periodo di tempo, forniamo un certo numero di crediti a ogni spazio dei nomi.
  * Tutte le operazioni eseguite dalle domande del mittente o del client ricevente saranno conteggiate rispetto a questi crediti (e sottratte dai crediti disponibili).
  * Se i crediti sono esauriti, le operazioni successive verranno limitate fino all'inizio del periodo di tempo successivo.
  * I crediti vengono riforniti all'inizio del periodo di tempo successivo.

### <a name="what-are-the-credit-limits"></a>Quali sono i limiti di credito?

I limiti di credito sono attualmente impostati su crediti '1000' ogni secondo (per spazio dei nomi).

Non tutte le operazioni sono uguali. Di seguito sono riportati i costi di credito di ciascuna delle operazioni - 

| Operazione | Costo del credito|
|-----------|-----------|
| Operazioni sui dati (Send, SendAsync, Receive, ReceiveAsync, Peek) |1 credito per messaggio |
| Operazioni di gestione (Creazione, Lettura, Aggiornamento, Elimina nelle code, Argomenti, Sottoscrizioni, Filtri) | 10 crediti |

> [!NOTE]
> Si noti che quando si invia a un argomento, ogni messaggio viene valutato rispetto ai filtri prima di essere reso disponibile nella sottoscrizione.
> Ogni valutazione del filtro viene conteggiata anche rispetto al limite di credito (ad esempio 1 credito per valutazione del filtro).
>

### <a name="how-will-i-know-that-im-being-throttled"></a>Come faccio a sapere che mi stanno sopeglio?

Quando le richieste dell'applicazione client vengono limitate, la risposta del server sottostante verrà ricevuta dall'applicazione e registrata.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Come posso evitare di essere limitato?

Con le risorse condivise, è importante applicare una sorta di utilizzo equo tra vari spazi dei nomi del bus di servizio che condividono tali risorse. La limitazione delle richieste garantisce la limitazione di qualsiasi picco in un singolo carico di lavoro.

Come accennato più avanti in questo articolo, non vi è alcun rischio di limitazione perché gli SDK client (e altre offerte di Azure PaaS) hanno il criterio di ripetizione dei tentativi predefinito incorporato in essi. Eventuali richieste limitate verranno ritentate con backoff esponenziale e alla fine passeranno attraverso quando i crediti vengono riforniti.

Comprensibilmente, alcune applicazioni possono essere sensibili alla limitazione. In tal caso, è consigliabile [eseguire la migrazione dello spazio dei](service-bus-migrate-standard-premium.md)nomi Standard del bus di servizio corrente a Premium . 

Durante la migrazione, è possibile allocare risorse dedicate allo spazio dei nomi del bus di servizio e aumentare in modo appropriato le risorse se si verifica un picco nel carico di lavoro e ridurre la probabilità di essere limitati. Inoltre, quando il carico di lavoro si riduce a livelli normali, è possibile ridurre le risorse allocate allo spazio dei nomi.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Limitazione nel livello Premium del bus di servizio di AzureThrottling in Azure Service Bus Premium tier

Il livello [Azure Service Bus Premium](service-bus-premium-messaging.md) alloca risorse dedicate, in termini di unità di messaggistica, a ogni configurazione dello spazio dei nomi da parte del cliente. Queste risorse dedicate forniscono velocità effettiva e latenza prevedibili e sono consigliate per sistemi ad alta velocità effettiva o di grado di produzione sensibili.

Inoltre, il livello Premium consente inoltre ai clienti di aumentare la capacità di velocità effettiva quando si verificano picchi nel carico di lavoro.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Come funziona la limitazione in Service Bus Premium?

Con l'allocazione esclusiva delle risorse per Service Bus Premium, la limitazione delle richieste è puramente basata sulle limitazioni delle risorse allocate allo spazio dei nomi.

Se il numero di richieste è superiore a quello che le risorse correnti possono essere servite, le richieste verranno limitate.

### <a name="how-will-i-know-that-im-being-throttled"></a>Come faccio a sapere che mi stanno sopeglio?

Esistono vari modi per identificare la limitazione delle richieste in Azure Service Bus Premium - 
  * **Le richieste limitate** vengono visualizzate nelle [metriche delle](service-bus-metrics-azure-monitor.md#request-metrics) richieste di Monitoraggio di Azure per identificare il numero di richieste limitate.
  * **Utilizzo elevato della CPU** indica che l'allocazione delle risorse corrente è elevata e le richieste possono essere limitate se il carico di lavoro corrente non riduce.
  * **Utilizzo elevato della memoria** indica che l'allocazione delle risorse corrente è elevata e le richieste possono essere limitate se il carico di lavoro corrente non riduce.

### <a name="how-can-i-avoid-being-throttled"></a>Come posso evitare di essere limitato?

Poiché lo spazio dei nomi Service Bus Premium dispone già di risorse dedicate, è possibile ridurre la possibilità di ottenere limitato aumentando il numero di unità di messaggistica allocate allo spazio dei nomi nell'evento (o anticipazione) di un picco nel carico di lavoro.

La scalabilità verticale/verso il basso può essere ottenuta creando [runbook](../automation/automation-create-alert-triggered-runbook.md) che possono essere attivati dalle modifiche nelle metriche precedenti.

## <a name="faqs"></a>Domande frequenti

### <a name="how-does-throttling-affect-my-application"></a>In che modo la limitazione delle richieste influisce sull'applicazione?

Quando una richiesta viene limitata, implica che il servizio è occupato perché si trova ad affrontare più richieste di quelle consentite dalle risorse. Se la stessa operazione viene ritentata dopo alcuni istanti, una volta che il servizio ha eseguito il carico di lavoro corrente, la richiesta può essere rispettata.

Poiché la limitazione è il comportamento previsto di qualsiasi servizio nativo cloud, è stata creata la logica di ripetizione dei tentativi nell'SDK del bus di servizio di Azure.Since throttling is the expected behavior of any cloud native service, we have built the retry logic into the Azure Service Bus SDK itself. L'impostazione predefinita è impostata su tentativi automatici con un back-off esponenziale per garantire che la stessa richiesta non venga limitata ogni volta.

La logica di ripetizione dei tentativi predefinita verrà applicata a ogni operazione.

### <a name="does-throttling-result-in-data-loss"></a>La limitazione delle richieste comporta la perdita di dati?

Il bus di servizio di Azure è ottimizzato per la persistenza, ci assicuriamo che tutti i dati inviati al bus di servizio vengano impegnati nell'archiviazione prima che il servizio riconosca l'esito positivo della richiesta.

Una volta che la richiesta è stata correttamente 'ACK' (riconosciuto) dal bus di servizio, implica che il bus di servizio ha elaborato correttamente la richiesta. Se il bus di servizio restituisce un 'NACK' (errore), significa che il bus di servizio non è stato in grado di elaborare la richiesta e l'applicazione client deve ritentare la richiesta.

Tuttavia, quando una richiesta viene limitata, il servizio implica che non può accettare ed elaborare la richiesta in questo momento a causa delle limitazioni delle risorse. Ciò **non** implica alcun tipo di perdita di dati perché il bus di servizio semplicemente non ha esaminato la richiesta. In questo caso, basandosi sui criteri di ripetizione dei tentativi predefiniti dell'SDK del bus di servizio garantisce che la richiesta venga elaborata alla fine.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi e informazioni sull'uso della messaggistica del bus di servizio, vedere gli argomenti avanzati seguenti:

* [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
* [Guida introduttiva: Inviare e ricevere messaggi usando il portale di Azure e .NET](service-bus-quickstart-portal.md)
* [Esercitazione: Aggiornare l'inventario usando il portale di Azure e argomenti/sottoscrizioni](service-bus-tutorial-topics-subscriptions-portal.md)

