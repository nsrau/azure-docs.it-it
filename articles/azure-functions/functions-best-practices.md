---
title: Procedure consigliate per Funzioni di Azure
description: Informazioni su modelli e procedure consigliate per Funzioni di Azure.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a41a5828a82d81c5e7e8749fee70cd15e17bb9d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277778"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Ottimizzare le prestazioni e l'affidabilità delle funzioni di Azure

Questo articolo fornisce indicazioni per migliorare le prestazioni e l'affidabilità delle app per le funzioni [senza server](https://azure.microsoft.com/solutions/serverless/).  

## <a name="general-best-practices"></a>Procedure consigliate generali

Questo articolo definisce le procedure consigliate per creare e definire l'architettura di soluzioni senza server tramite Funzioni di Azure.

### <a name="avoid-long-running-functions"></a>Evitare funzioni con esecuzione prolungata

Le funzioni con esecuzione prolungata e di grandi dimensioni possono causare problemi di timeout imprevisti. Per altre informazioni sui timeout per un determinato piano di hosting, vedere Durata del [timeout dell'app per](functions-scale.md#timeout)le funzioni. 

Una funzione può diventare grande a causa di molte dipendenze Node.js.A function can become large because of many Node.js dependencies. L'importazione delle dipendenze può anche fare aumentare i tempi di caricamento causando timeout imprevisti. Le dipendenze vengono caricate in modo sia esplicito che implicito. Un singolo modulo caricato dal codice potrebbe caricare i propri moduli aggiuntivi. 

Quando è possibile, suddividere le funzioni di grandi dimensioni in gruppi di funzioni più piccoli che possono interagire tra loro e restituire rapidamente le risposte. Ad esempio, una funzione trigger webhook o HTTP potrebbe richiedere una risposta di riconoscimento entro un determinato limite di tempo; è comune che i webhook richiedano una risposta immediata. È possibile passare il payload del trigger HTTP in una coda perché venga elaborato da una funzione di trigger della coda. Questo approccio consente di rinviare il lavoro effettivo e restituire una risposta immediata.


### <a name="cross-function-communication"></a>Comunicazioni tra funzioni

Le [funzioni permanenti](durable/durable-functions-overview.md) e le [app per la logica di Azure](../logic-apps/logic-apps-overview.md) sono progettate per gestire transazioni di stato e comunicazioni tra più funzioni.

Se non si usano funzioni durevoli o app per la logica per l'integrazione con più funzioni, è consigliabile usare le code di archiviazione per la comunicazione tra funzioni. Il motivo principale è che le code di archiviazione sono più economiche e molto più facili da eseguire il provisioning rispetto ad altre opzioni di archiviazione. 

Le dimensioni dei singoli messaggi in una coda di archiviazione sono limitate a 64 KB. Se è necessario passare messaggi di dimensioni superiori tra le funzioni, è possibile usare una coda del bus di servizio di Azure per supportare dimensioni dei messaggi fino a 256 kB al livello Standard e fino a 1 MB al livello Premium.

Gli argomenti del bus di servizio sono utili se è necessario filtrare i messaggi prima dell'elaborazione.

Gli hub eventi sono utili per supportare comunicazioni con volumi elevati.


### <a name="write-functions-to-be-stateless"></a>Scrivere le funzioni in modo che siano senza stato 

Le funzioni devono essere senza stato e idempotenti se possibile. Associare ai dati eventuali informazioni obbligatorie sullo stato. Ad esempio, un ordine in fase di elaborazione probabilmente ha un membro `state` associato. Una funzione può elaborare un ordine basato su tale stato rimanendo però una funzione senza stato. 

Le funzioni idempotenti sono consigliate in particolare con i trigger timer. Ad esempio, se si dispone di qualcosa che deve assolutamente essere eseguito una volta al giorno, scriverlo in modo che possa essere eseguito in qualsiasi momento durante il giorno con gli stessi risultati. La funzione può uscire quando non c'è lavoro per un determinato giorno. Anche se un'esecuzione precedente non è stata completata, l'esecuzione successiva riprenderà da dove era stata interrotta.


### <a name="write-defensive-functions"></a>Scrivere funzioni difensive

Si supponga che la funzione possa rilevare un'eccezione in qualsiasi momento. Progettare le funzioni con la possibilità di continuare da un punto di errore precedente durante l'esecuzione successiva. Si consideri uno scenario che richiede le azioni seguenti:

1. Query per 10.000 righe in un database.
2. Creare un messaggio in coda per ognuna delle righe da elaborare ulteriormente in un secondo tempo.
 
A seconda di quanto sia complesso il sistema, è possibile che i servizi downstream coinvolti si comportino male, interruzioni della rete o raggiunti limiti di quota e così via. Tutti questi possono influenzare la funzione in qualsiasi momento. È necessario progettare le funzioni in modo che siano preparate.

Come reagisce il codice in caso di errore dopo l'inserimento di 5.000 di tali elementi in una coda per l'elaborazione? Tenere traccia degli elementi in un set già completato. In caso contrario, è possibile inserirli di nuovo la volta successiva. Questo doppio inserimento può avere un grave impatto sul flusso di lavoro, in modo da [rendere le funzioni idempotenti](functions-idempotent.md). 

Se un elemento della coda è già stato elaborato, consentire alla funzione di essere no-op.

Sfruttare le misure difensive già messe a disposizione per i componenti usati nella piattaforma Funzioni di Azure. Ad esempio, vedere **Gestione di messaggi della coda non elaborabili** nella documentazione relativa a [trigger e associazioni della coda di Archiviazione di Azure](functions-bindings-storage-queue-trigger.md#poison-messages). 

## <a name="scalability-best-practices"></a>Procedure consigliate per la scalabilità

Esistono diversi fattori che influiscono sulla scalabilità delle istanze dell'app per le funzioni. Informazioni dettagliate sono disponibili nella documentazione relativa alla [scalabilità delle funzioni](functions-scale.md).  Di seguito sono descritte alcune procedure consigliate per garantire la scalabilità ottimale di un'app per le funzioni.

### <a name="share-and-manage-connections"></a>Condividere e gestire le connessioni

Se possibile, riutilizzare le connessioni a risorse esterne. Vedere [come gestire le connessioni in Funzioni di Azure](./manage-connections.md).

### <a name="avoid-sharing-storage-accounts"></a>Evitare di condividere gli account di archiviazioneAvoid sharing storage accounts

Quando si crea un'app per le funzioni, è necessario associarla a un account di archiviazione. La connessione all'account di archiviazione viene mantenuta [nell'impostazione dell'applicazione AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Non combinare codice di test e di produzione nella stessa app per le funzioni

Le funzioni all'interno di un'app per le funzioni condividono le risorse. Ad esempio, la memoria è condivisa. Se si usa un'app per le funzioni nell'ambiente di produzione, non aggiungere funzioni e risorse relative ai test, per evitare possibili sovraccarichi imprevisti durante l'esecuzione del codice di produzione.

Prestare attenzione a quello che si carica nelle app per le funzioni di produzione. La memoria viene calcolata in ogni funzione nell'app.

Se si dispone di un assembly condiviso a cui si fa riferimento in più funzioni .NET, inserirlo in una cartella condivisa comune. In caso contrario, è possibile distribuire accidentalmente più versioni dello stesso file binario che si comportano in modo diverso tra le funzioni.

Non usare la registrazione dettagliata nel codice di produzione, che ha un impatto negativo sulle prestazioni.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Usare codice asincrono ma evitare di bloccare le chiamate

La programmazione asincrona è una procedura consigliata, soprattutto quando sono coinvolte operazioni di I/O di blocco.

Nel linguaggio C, evitare `Result` sempre `Wait` di fare `Task` riferimento alla proprietà o al metodo di chiamata su un'istanza. Questo approccio può causare l'esaurimento di un thread.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Utilizzare più processi di lavoro

Per impostazione predefinita, qualsiasi istanza host per Funzioni utilizza un singolo processo di lavoro. Per migliorare le prestazioni, in particolare con i runtime a thread singolo come Python, usare il [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) per aumentare il numero di processi di lavoro per host (fino a 10). Funzioni di Azure tenta quindi di distribuire in modo uniforme le chiamate di funzione simultanee tra questi worker. 

Il FUNCTIONS_WORKER_PROCESS_COUNT si applica a ogni host creato da Funzioni durante la scalabilità orizzontale dell'applicazione per soddisfare la domanda. 

### <a name="receive-messages-in-batch-whenever-possible"></a>Ricevere messaggi in batch, se possibile

Alcuni trigger come l'hub eventi consentono di ricevere un batch di messaggi in un'unica chiamata.  L'invio di messaggi in batch offre prestazioni notevolmente migliori.  È possibile configurare le dimensioni massime del batch nel file `host.json` come descritto nella [documentazione di riferimento su host.json](functions-host-json.md)

Per le funzioni di C, è possibile modificare il tipo in una matrice fortemente tipizzata.  Anziché `EventData sensorEvent`, la firma del metodo può essere, ad esempio, `EventData[] sensorEvent`.  Per altre lingue, è necessario impostare in modo `function.json` `many` esplicito la proprietà cardinality in a per abilitare l'invio in batch [come illustrato di seguito.](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10)

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Configurare i comportamenti degli host per migliorare la gestione della concorrenza

Il file `host.json` nell'app per le funzioni consente di configurare i comportamenti del trigger e del runtime dell'host.  Oltre ai comportamenti di invio in batch, è possibile gestire anche la concorrenza in una serie di trigger. La modifica dei valori di queste opzioni consente spesso di applicare a ogni istanza la scalabilità appropriata per soddisfare le esigenze delle funzioni richiamate.

Le impostazioni nel file host.json si applicano a tutte le funzioni all'interno dell'app, all'interno di una *singola istanza* della funzione. Ad esempio, se si dispone di un'app per le funzioni con due funzioni e [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) richieste HTTP impostate su 25, una richiesta a uno dei trigger HTTP verrebbe conteggiata per le 25 richieste simultanee condivise.  Quando l'app per le funzioni viene ridimensionata a 10 istanze, le due funzioni consentono in modo efficace 250 richieste simultanee (10 istanze e 25 richieste simultanee per istanza). 

Altre opzioni di configurazione host sono disponibili nell'articolo sulla [configurazione host.json.](functions-host-json.md)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Come gestire le connessioni in Funzioni di Azure](manage-connections.md)
* [Procedure consigliate per il servizio app](../app-service/app-service-best-practices.md)
