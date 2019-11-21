---
title: Procedure consigliate per Funzioni di Azure
description: Informazioni su modelli e procedure consigliate per Funzioni di Azure.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 10/16/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa85f636233a067713d127938d674b359bd03696
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227376"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Ottimizzare le prestazioni e l'affidabilità delle funzioni di Azure

Questo articolo fornisce indicazioni per migliorare le prestazioni e l'affidabilità delle app per le funzioni [senza server](https://azure.microsoft.com/solutions/serverless/).  

## <a name="general-best-practices"></a>Procedure consigliate generali

Questo articolo definisce le procedure consigliate per creare e definire l'architettura di soluzioni senza server tramite Funzioni di Azure.

### <a name="avoid-long-running-functions"></a>Evitare funzioni con esecuzione prolungata

Le funzioni con esecuzione prolungata e di grandi dimensioni possono causare problemi di timeout imprevisti. To learn more about the timeouts for a given hosting plan, see [function app timeout duration](functions-scale.md#timeout). 

A function can become large because of many Node.js dependencies. L'importazione delle dipendenze può anche fare aumentare i tempi di caricamento causando timeout imprevisti. Le dipendenze vengono caricate in modo sia esplicito che implicito. Un singolo modulo caricato dal codice potrebbe caricare i propri moduli aggiuntivi. 

Quando è possibile, suddividere le funzioni di grandi dimensioni in gruppi di funzioni più piccoli che possono interagire tra loro e restituire rapidamente le risposte. For example, a webhook or HTTP trigger function might require an acknowledgment response within a certain time limit; it's common for webhooks to require an immediate response. È possibile passare il payload del trigger HTTP in una coda perché venga elaborato da una funzione di trigger della coda. This approach lets you defer the actual work and return an immediate response.


### <a name="cross-function-communication"></a>Comunicazioni tra funzioni

Le [funzioni permanenti](durable/durable-functions-overview.md) e le [app per la logica di Azure](../logic-apps/logic-apps-overview.md) sono progettate per gestire transazioni di stato e comunicazioni tra più funzioni.

If not using Durable Functions or Logic Apps to integrate with multiple functions, it's best to use storage queues for cross-function communication. The main reason is that storage queues are cheaper and much easier to provision than other storage options. 

Le dimensioni dei singoli messaggi in una coda di archiviazione sono limitate a 64 KB. Se è necessario passare messaggi di dimensioni superiori tra le funzioni, è possibile usare una coda del bus di servizio di Azure per supportare dimensioni dei messaggi fino a 256 kB al livello Standard e fino a 1 MB al livello Premium.

Gli argomenti del bus di servizio sono utili se è necessario filtrare i messaggi prima dell'elaborazione.

Gli hub eventi sono utili per supportare comunicazioni con volumi elevati.


### <a name="write-functions-to-be-stateless"></a>Scrivere le funzioni in modo che siano senza stato 

Le funzioni devono essere senza stato e idempotenti se possibile. Associare ai dati eventuali informazioni obbligatorie sullo stato. Ad esempio, un ordine in fase di elaborazione probabilmente ha un membro `state` associato. Una funzione può elaborare un ordine basato su tale stato rimanendo però una funzione senza stato. 

Le funzioni idempotenti sono consigliate in particolare con i trigger timer. For example, if you have something that absolutely must run once a day, write it so it can run anytime during the day with the same results. The function can exit when there's no work for a particular day. Anche se un'esecuzione precedente non è stata completata, l'esecuzione successiva riprenderà da dove era stata interrotta.


### <a name="write-defensive-functions"></a>Scrivere funzioni difensive

Si supponga che la funzione possa rilevare un'eccezione in qualsiasi momento. Progettare le funzioni con la possibilità di continuare da un punto di errore precedente durante l'esecuzione successiva. Si consideri uno scenario che richiede le azioni seguenti:

1. Query for 10,000 rows in a database.
2. Creare un messaggio in coda per ognuna delle righe da elaborare ulteriormente in un secondo tempo.
 
Depending on how complex your system is, you may have: involved downstream services behaving badly, networking outages, or quota limits reached, etc. All of these can affect your function at any time. È necessario progettare le funzioni in modo che siano preparate.

Come reagisce il codice in caso di errore dopo l'inserimento di 5.000 di tali elementi in una coda per l'elaborazione? Tenere traccia degli elementi in un set già completato. In caso contrario, è possibile inserirli di nuovo la volta successiva. This double-insertion can have a serious impact on your work flow, so [make your functions idempotent](functions-idempotent.md). 

Se un elemento della coda è già stato elaborato, consentire alla funzione di essere no-op.

Sfruttare le misure difensive già messe a disposizione per i componenti usati nella piattaforma Funzioni di Azure. Ad esempio, vedere **Gestione di messaggi della coda non elaborabili** nella documentazione relativa a [trigger e associazioni della coda di Archiviazione di Azure](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="scalability-best-practices"></a>Procedure consigliate per la scalabilità

There are a number of factors that impact how instances of your function app scale. Informazioni dettagliate sono disponibili nella documentazione relativa alla [scalabilità delle funzioni](functions-scale.md).  Di seguito sono descritte alcune procedure consigliate per garantire la scalabilità ottimale di un'app per le funzioni.

### <a name="share-and-manage-connections"></a>Condividere e gestire le connessioni

Reuse connections to external resources whenever possible.  Vedere [come gestire le connessioni in Funzioni di Azure](./manage-connections.md).

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Non combinare codice di test e di produzione nella stessa app per le funzioni

Le funzioni all'interno di un'app per le funzioni condividono le risorse. Ad esempio, la memoria è condivisa. Se si usa un'app per le funzioni nell'ambiente di produzione, non aggiungere funzioni e risorse relative ai test, per evitare possibili sovraccarichi imprevisti durante l'esecuzione del codice di produzione.

Prestare attenzione a quello che si carica nelle app per le funzioni di produzione. La memoria viene calcolata in ogni funzione nell'app.

If you have a shared assembly referenced in multiple .NET functions, put it in a common shared folder. Otherwise, you could accidentally deploy multiple versions of the same binary that behave differently between functions.

Don't use verbose logging in production code, which has a negative performance impact.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Usare codice asincrono ma evitare di bloccare le chiamate

La programmazione asincrona è una procedura consigliata. Evitare sempre, tuttavia, di fare riferimento alla proprietà `Result` o di chiamare il metodo `Wait` su un'istanza di `Task`. Questo approccio può causare l'esaurimento di un thread.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Ricevere messaggi in batch, se possibile

Alcuni trigger come l'hub eventi consentono di ricevere un batch di messaggi in un'unica chiamata.  L'invio di messaggi in batch offre prestazioni notevolmente migliori.  È possibile configurare le dimensioni massime del batch nel file `host.json` come descritto nella [documentazione di riferimento su host.json](functions-host-json.md)

For C# functions, you can change the type to a strongly-typed array.  Anziché `EventData sensorEvent`, la firma del metodo può essere, ad esempio, `EventData[] sensorEvent`.  For other languages, you'll need to explicitly set the cardinality property in your `function.json` to `many` in order to enable batching [as shown here](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Configurare i comportamenti degli host per migliorare la gestione della concorrenza

Il file `host.json` nell'app per le funzioni consente di configurare i comportamenti del trigger e del runtime dell'host.  Oltre ai comportamenti di invio in batch, è possibile gestire anche la concorrenza in una serie di trigger. La modifica dei valori di queste opzioni consente spesso di applicare a ogni istanza la scalabilità appropriata per soddisfare le esigenze delle funzioni richiamate.

Settings in the host.json file apply across all functions within the app, within a *single instance* of the function. For example, if you had a function app with two HTTP functions and [`maxConcurrentRequests`](functions-bindings-http-webhook.md#hostjson-settings) requests set to 25, a request to either HTTP trigger would count towards the shared 25 concurrent requests.  When that function app is scaled to 10 instances, the two functions effectively allow 250 concurrent requests (10 instances * 25 concurrent requests per instance). 

Other host configuration options are found in the [host.json configuration article](functions-host-json.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Come gestire le connessioni in Funzioni di Azure](manage-connections.md)
* [Procedure consigliate per il servizio app](../app-service/app-service-best-practices.md)
