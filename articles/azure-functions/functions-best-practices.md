---
title: Procedure consigliate per Funzioni di Azure | Documentazione Microsoft
description: Informazioni su modelli e procedure consigliate per Funzioni di Azure.
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: funzioni di azure, modelli, procedure consigliate, funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/16/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c3ef75a2a157190b24c171309c4d5c39596b5045
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Ottimizzare le prestazioni e l'affidabilità delle funzioni di Azure

Questo articolo fornisce indicazioni per migliorare le prestazioni e l'affidabilità delle app per le funzioni [senza server](https://azure.microsoft.com/overview/serverless-computing/). 

## <a name="general-best-practices"></a>Procedure consigliate generali

Questo articolo definisce le procedure consigliate per creare e definire l'architettura di soluzioni senza server tramite Funzioni di Azure.

### <a name="avoid-long-running-functions"></a>Evitare funzioni con esecuzione prolungata

Le funzioni con esecuzione prolungata e di grandi dimensioni possono causare problemi di timeout imprevisti. Le dimensioni di una funzione possono diventare grandi a causa della presenza di molte dipendenze di Node.js. L'importazione delle dipendenze può anche fare aumentare i tempi di caricamento causando timeout imprevisti. Le dipendenze vengono caricate in modo sia esplicito che implicito. Un singolo modulo caricato dal codice potrebbe caricare i propri moduli aggiuntivi.  

Quando è possibile, suddividere le funzioni di grandi dimensioni in gruppi di funzioni più piccoli che possono interagire tra loro e restituire rapidamente le risposte. Ad esempio, un webhook o una funzione di trigger HTTP potrebbe richiedere una risposta di acknowledgment entro un determinato limite di tempo. È normale per i webhook richiedere una risposta immediata. È possibile passare il payload del trigger HTTP in una coda perché venga elaborato da una funzione di trigger della coda. Questo approccio consente di rinviare l'operazione effettiva e di restituire una risposta immediata.


### <a name="cross-function-communication"></a>Comunicazioni tra funzioni

Le [funzioni permanenti](durable-functions-overview.md) e le [app per la logica di Azure](../logic-apps/logic-apps-what-are-logic-apps.md) sono progettate per gestire transazioni di stato e comunicazioni tra più funzioni.

Se per integrare più funzioni non si usano queste due soluzioni, è opportuno in genere usare le code di archiviazione per la comunicazione tra funzioni.  Il motivo principale è che le code di archiviazione sono più economiche ed è molto più facile sottoporle a provisioning. 

Le dimensioni dei singoli messaggi in una coda di archiviazione sono limitate a 64 KB. Se è necessario passare i messaggi di grandi dimensioni tra le funzioni, una coda può essere utilizzata per supportare messaggi di Azure Service Bus con dimensioni fino a 256 KB nel livello Standard e fino a 1 MB nel livello Premium.

Gli argomenti del bus di servizio sono utili se è necessario filtrare i messaggi prima dell'elaborazione.

Gli hub eventi sono utili per supportare comunicazioni con volumi elevati.


### <a name="write-functions-to-be-stateless"></a>Scrivere le funzioni in modo che siano senza stato 

Le funzioni devono essere senza stato e idempotenti se possibile. Associare ai dati eventuali informazioni obbligatorie sullo stato. Ad esempio, un ordine in fase di elaborazione probabilmente ha un membro `state` associato. Una funzione può elaborare un ordine basato su tale stato rimanendo però una funzione senza stato. 

Le funzioni idempotenti sono consigliate in particolare con i trigger timer. Ad esempio, se si deve assolutamente eseguire un'azione una volta al giorno, scriverla in modo che possa essere eseguita a qualsiasi ora del giorno con gli stessi risultati. La funzione può essere chiusa quando non è presente alcun lavoro da svolgere per un determinato giorno. Anche se un'esecuzione precedente non è stata completata, l'esecuzione successiva riprenderà da dove era stata interrotta.


### <a name="write-defensive-functions"></a>Scrivere funzioni difensive

Si supponga che la funzione possa rilevare un'eccezione in qualsiasi momento. Progettare le funzioni con la possibilità di continuare da un punto di errore precedente durante l'esecuzione successiva. Si consideri uno scenario che richiede le azioni seguenti:

1. Query per 10.000 righe in un database.
2. Creare un messaggio in coda per ognuna delle righe da elaborare ulteriormente in un secondo tempo.
 
In base alla complessità del sistema, è possibile che i servizi downstream coinvolti non funzionino correttamente, che si verifichino interruzioni della rete, che vengano raggiunti i limiti di quota e così via. Tutti questi elementi possono influire sulla funzione in qualsiasi momento. È necessario progettare le funzioni in modo che siano preparate.

Come reagisce il codice in caso di errore dopo l'inserimento di 5.000 di tali elementi in una coda per l'elaborazione? Tenere traccia degli elementi in un set già completato. In caso contrario, è possibile inserirli di nuovo la volta successiva. Ciò può influire in modo negativo sul flusso di lavoro. 

Se un elemento della coda è già stato elaborato, consentire alla funzione di essere no-op.

Sfruttare le misure difensive già messe a disposizione per i componenti usati nella piattaforma Funzioni di Azure. Ad esempio, vedere **Gestione di messaggi della coda non elaborabili** nella documentazione relativa a [trigger e associazioni della coda di Archiviazione di Azure](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="scalability-best-practices"></a>Procedure consigliate per la scalabilità

Sulla scalabilità delle istanze dell'app per le funzioni influiscono vari tipi di fattori. Informazioni dettagliate sono disponibili nella documentazione relativa alla [scalabilità delle funzioni](functions-scale.md).  Di seguito sono descritte alcune procedure consigliate per garantire la scalabilità ottimale di un'app per le funzioni.

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Non combinare codice di test e di produzione nella stessa app per le funzioni

Le funzioni all'interno di un'app per le funzioni condividono le risorse. Ad esempio, la memoria è condivisa. Se si usa un'app per le funzioni nell'ambiente di produzione, non aggiungere funzioni e risorse relative ai test, per evitare possibili sovraccarichi imprevisti durante l'esecuzione del codice di produzione.

Prestare attenzione a quello che si carica nelle app per le funzioni di produzione. La memoria viene calcolata in ogni funzione nell'app.

Se si usa un assembly condiviso a cui si fa riferimento in più funzioni di .Net, inserirlo in una cartella condivisa comune. Fare riferimento all'assembly con un'istruzione simile all'esempio seguente se si usano script C# (con estensione csx): 

    #r "..\Shared\MyAssembly.dll". 

In caso contrario, è facile distribuire accidentalmente più versioni di prova dello stesso binario che si comportano in modo diverso nelle varie funzioni.

Non usare la registrazione dettagliata nel codice di produzione. Ha un impatto negativo sulle prestazioni.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Usare codice asincrono ma evitare di bloccare le chiamate

La programmazione asincrona è una procedura consigliata. Evitare sempre, tuttavia, di fare riferimento alla proprietà `Result` o di chiamare il metodo `Wait` su un'istanza di `Task`. Questo approccio può causare l'esaurimento di un thread.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Ricevere messaggi in batch, se possibile

Alcuni trigger come l'hub eventi consentono di ricevere un batch di messaggi in un'unica chiamata.  L'invio di messaggi in batch offre prestazioni notevolmente migliori.  È possibile configurare le dimensioni massime del batch nel file `functions.json` come descritto nella [documentazione di riferimento su host.json](functions-host-json.md)

Per le funzioni C# è possibile modificare il tipo in una matrice fortemente tipizzata.  Anziché `EventData sensorEvent`, la firma del metodo può essere, ad esempio, `EventData[] sensorEvent`.  Per altri linguaggi è necessario impostare in modo esplicito la proprietà della cardinalità di `function.json` in `many` per consentire l'invio in batch, [come illustrato di seguito](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Configurare i comportamenti degli host per migliorare la gestione della concorrenza

Il file `host.json` nell'app per le funzioni consente di configurare i comportamenti del trigger e del runtime dell'host.  Oltre ai comportamenti di invio in batch, è possibile gestire anche la concorrenza in una serie di trigger.  La modifica dei valori di queste opzioni consente spesso di applicare a ogni istanza la scalabilità appropriata per soddisfare le esigenze delle funzioni richiamate.

Le impostazioni del file hosts vengono applicate a tutte le funzioni all'interno dell'app, in una *singola istanza* della funzione. Se, ad esempio, si avesse un'app per le funzioni con due funzioni HTTP e le richieste simultanee fossero impostate su 25, anche una richiesta a un trigger HTTP verrebbe conteggiata come una delle 25 richieste simultanee condivise.  Se l'app per le funzioni venisse ridimensionata a 10 istanze, quindi, le due funzioni consentirebbero di fatto 250 richieste simultanee (10 istanze * 25 richieste simultanee per istanza).

**Opzioni dell'host per la concorrenza HTTP**

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

Altre opzioni di configurazione dell'host sono disponibili [nel documento di configurazione dell'host](functions-host-json.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le seguenti risorse:

Poiché Funzioni di Azure usa Servizio app di Azure, è consigliabile vedere anche le linee guida del servizio app.
* [Schemi e procedure per le ottimizzazioni delle prestazioni HTTP](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)
