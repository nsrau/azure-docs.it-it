---
title: 'Azure Cosmos DB: Note sulla versione su SDK e API del processore dei feed di modifiche .NET'
description: Tutte le informazioni sull'SDK e sull'API del processore dei feed di modifiche, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le versioni dell'SDK del processore dei feed di modifiche .NET.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: e4c2969db560ff20cae2ed7b9ffbe0cea206c7a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91611572"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET Change Feed Processor SDK: download e note sulla versione

> [!div class="op_single_selector"]
>
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Connettore Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api
> * [Provider di risorse REST](/rest/api
> * [SQL](sql-api-query-reference.md)
> * [Esecuzione bulk - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Esecuzione bulk - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**Download dell'SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Documentazione sull'API**|[Documentazione di riferimento sull'API della libreria del processore dei feed di modifiche](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet&preserve-view=true)|
|**Operazioni preliminari**|[Introduzione all'SDK del processore dei feed di modifiche .NET](change-feed.md)|
|**Framework attualmente supportato**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Se si usa il processore dei feed di modifiche, vedere la versione 3. x più recente di [.NET SDK](change-feed-processor.md), che include il feed di modifiche incorporato nell'SDK. 

## <a name="release-notes"></a>Note sulla versione

### <a name="v2-builds"></a>Build della seconda versione

### <a name="232"></a><a id="2.3.2"></a>2.3.2
* Aggiunta la compatibilità dell'archivio di lease con [l'SDK v3 che consente percorsi di migrazione critici. È possibile eseguire la migrazione all'SDK v3 e ritrasferirla alla libreria del processore del feed di modifiche senza perdere alcuno stato.

### <a name="231"></a><a id="2.3.1"></a>2.3.1
* Correzione di un caso per cui viene inviato il motivo di chiusura `FeedProcessing.ChangeFeedObserverCloseReason.Unknown` a `FeedProcessing.IChangeFeedObserver.CloseAsync` se non è possibile trovare la partizione o se la replica di destinazione non è aggiornata con la sessione di lettura. In questi casi vengono ora usati i motivi di chiusura `FeedProcessing.ChangeFeedObserverCloseReason.ResourceGone` e `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable`.
* Aggiunto un nuovo motivo di chiusura `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` inviato per chiudere l'osservatore del feed di modifiche quando la replica di destinazione non è aggiornata con la sessione di lettura.

### <a name="230"></a><a id="2.3.0"></a>2.3.0
* Aggiunta di un nuovo metodo `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` e dell'interfaccia pubblica corrispondente `ICheckpointPartitionProcessorFactory`. In questo modo un'implementazione dell'interfaccia `IPartitionProcessor` può usare il meccanismo di checkpoint incorporato. La nuova factory è simile all'elemento `IPartitionProcessorFactory` esistente, con la differenza che il metodo `Create` accetta anche il parametro `ILeaseCheckpointer`.
* Per la stessa istanza di `ChangeFeedProcessorBuilder` è possibile usare solo uno dei due metodi `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` e `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory`.

### <a name="228"></a><a id="2.2.8"></a>2.2.8
* Miglioramenti apportati alla stabilità e alla diagnostica:
  * Aggiunta del supporto per rilevare operazioni di lettura del feed di modifiche che richiedono molto tempo. Quando il tempo richiesto è superiore a quello specificato dalla proprietà `ChangeFeedProcessorOptions.ChangeFeedTimeout`, vengono eseguiti i passaggi seguenti:
    * L'operazione di lettura del feed di modifiche nella partizione problematica viene interrotta.
    * L'istanza del processore del feed di modifiche rilascia la proprietà del lease problematico. Il lease rilasciato verrà prelevato durante il passaggio di acquisizione del lease successivo che verrà eseguito dalla stessa istanza o da un'istanza diversa del processore del feed di modifiche. In questo modo, la lettura del feed delle modifiche potrà ricominciare.
    * Viene segnalato un problema al monitoraggio integrità. Il monitoraggio integrità predefinito invia tutti i problemi segnalati al log di traccia.
  * Aggiunta di una nuova proprietà pubblica: `ChangeFeedProcessorOptions.ChangeFeedTimeout`. Il valore predefinito di questa proprietà è 10 minuti.
  * Aggiunta di un nuovo valore di enumerazione pubblica: `Monitoring.MonitoredOperation.ReadChangeFeed`. Quando il valore di `HealthMonitoringRecord.Operation` è impostato su `Monitoring.MonitoredOperation.ReadChangeFeed`, il problema di integrità è correlato alla lettura del feed di modifiche.

### <a name="227"></a><a id="2.2.7"></a>2.2.7
* Migliorata la strategia di bilanciamento del carico per uno scenario in cui il recupero di tutti i lease richiede più tempo rispetto all'intervallo di scadenza dei lease, ad esempio a causa di problemi di rete:
  * In questo scenario l'algoritmo di bilanciamento del carico considerava erroneamente i lease come scaduti causando il furto dei lease dai proprietari attivi. Questa situazione poteva attivare il ribilanciamento superfluo di un numero elevato di lease.
  * Questo problema è stato risolto in questa versione, evitando nuovi tentativi in caso di conflitto durante l'acquisizione del lease scaduto che il proprietario non ha cambiato e posticipando l'acquisizione del lease scaduto alla successiva iterazione del bilanciamento del carico.

### <a name="226"></a><a id="2.2.6"></a>2.2.6
* Gestione delle eccezioni di Observer migliorata.
* Informazioni più dettagliate sugli errori di Observer:
  * Quando un'istanza di Observer viene chiusa a causa di un'eccezione generata da ProcessChangesAsync di Observer, CloseAsync riceverà il parametro del motivo impostato su ChangeFeedObserverCloseReason.ObserverError.
  * Sono state aggiunte funzionalità di analisi per identificare errori nel codice utente in un'istanza di Observer.

### <a name="225"></a><a id="2.2.5"></a>2.2.5
* Aggiunta del supporto per la gestione della suddivisione nelle raccolte che usano la velocità effettiva per database condivisi.
  * Questa versione corregge un problema che può verificarsi durante la divisione in raccolte che usano unità elaborate di database condivise, quando la divisione genera un ribilanciamento delle partizioni con la creazione di un solo intervallo di chiavi di partizione figlio, anziché di due. Quando ciò si verifica, il processore dei feed di modifiche può rimanere bloccato durante l'eliminazione del lease per l'intervallo di chiavi di partizione precedente e non creare nuovi lease. Il problema è stato corretto in questa versione.

### <a name="224"></a><a id="2.2.4"></a>2.2.4
* Nuova proprietà aggiunta ChangeFeedProcessorOptions.StartContinuation per supportare l'avvio del feed di modifica dal token di continuazione della richiesta. Viene utilizzato solo quando la raccolta di lease è vuota o un lease non ha impostato ContinuationToken. Per i lease nella raccolta di lease con ContinuationToken impostato, viene usato ContinuationToken e viene ignorato ChangeFeedProcessorOptions.StartContinuation.

### <a name="223"></a><a id="2.2.3"></a>2.2.3
* Aggiunto il supporto per l'uso dell’archivio personalizzato per rendere persistenti i token di continuazione per ogni partizione.
  * Ad esempio, un archivio personalizzato lease può essere raccolta di lease Azure Cosmos DB partizionata in modo personalizzato.
  * Gli archivi di lease personalizzati possono utilizzare il nuovo punto di estensibilità ChangeFeedProcessorBuilder.WithLeaseStoreManager (ILeaseStoreManager) e l’interfaccia pubblica ILeaseStoreManager.
  * L'interfaccia di ILeaseManager è stata trasformata in interfacce di ruolo multiple.
* Modifica di rilievo secondaria: rimosso il punto di estensibilità ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager), viene utilizzato ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) al suo posto.

### <a name="222"></a><a id="2.2.2"></a>2.2.2
* Questa versione corregge un problema che si verifica durante l'elaborazione di una divisione nella raccolta monitorata e l'utilizzo di una raccolta di lease partizionata. Durante l'elaborazione di un lease per partizione divisa, il lease corrispondente a quella partizione non può essere eliminato. Il problema è stato corretto in questa versione.

### <a name="221"></a><a id="2.2.1"></a>2.2.1
* Correzione del calcolo di stima per gli account con più aree di scrittura e nuovo formato di token di sessione.

### <a name="220"></a><a id="2.2.0"></a>2.2.0
* Aggiunta del supporto per le raccolte partizionate di lease. La chiave di partizione deve essere definita come /id.
* Modifica che causa un'interruzione minore: i metodi dell'interfaccia IChangeFeedDocumentClient e la classe ChangeFeedDocumentClient sono stati modificati per includere i parametri RequestOptions e CancellationToken. IChangeFeedDocumentClient è un punto di estendibilità avanzato che consente di offrire un'implementazione personalizzata del client di documenti da usare con il processore dei feed di modifiche, ad esempio per decorare DocumentClient e intercettare tutte le chiamate che riceve per l'esecuzione di operazioni aggiuntive di analisi, gestione degli errori, e così via. Con questo aggiornamento, il codice che implementa IChangeFeedDocumentClient dovrà essere modificato per includere i nuovi parametri nell'implementazione.
* Miglioramenti della diagnostica secondari.

### <a name="210"></a><a id="2.1.0"></a>2.1.0
* È stata aggiunta una nuova API, Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(), utilizzabile per ottenere il lavoro stimato per ogni partizione.
* Supporta Microsoft.Azure.DocumentDB SDK 2.0. Richiede Microsoft.Azure.DocumentDB 2.0 o versione successiva.

### <a name="206"></a><a id="2.0.6"></a>2.0.6
* È stata aggiunta la proprietà pubblica ChangeFeedEventHost.HostName per la compatibilità con la versione 1.

### <a name="205"></a><a id="2.0.5"></a>2.0.5
* È stata corretta una race condition che si verifica durante la suddivisione della partizione. Per effetto della race condition è possibile che il lease venga acquisito e immediatamente perso durante la suddivisione della partizione, provocando una contesa. Con questa versione il problema della race condition è stato risolto.

### <a name="204"></a><a id="2.0.4"></a>2.0.4
* SDK con disponibilità generale

### <a name="203-prerelease"></a><a id="2.0.3-prerelease"></a>Versione provvisoria 2.0.3
* Sono stati corretti i problemi seguenti:
  * Quando si verifica la suddivisione della partizione, può verificarsi l'elaborazione duplicati dei documenti modificati prima della divisione.
  * L'API GetEstimatedRemainingWork ha restituito 0 se non era presente alcun lease nella raccolta di lease.

* Le eccezioni seguenti sono rese pubbliche. Le estensioni che implementano IPartitionProcessor possono generare queste eccezioni.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a id="2.0.2-prerelease"></a>2.0.2-prerelease
* Modifiche API secondarie:
  * Rimozione di ChangeFeedProcessorOptions.IsAutoCheckpointEnabled, contrassegnato come obsoleto.

### <a name="201-prerelease"></a><a id="2.0.1-prerelease"></a>2.0.1-prerelease
* Miglioramenti della stabilità:
  * Gestione migliorata dell'inizializzazione dell'archivio dei lease. Quando l'archivio dei lease è vuoto, solo un'istanza del processore può inizializzarlo, mentre le altre rimarranno in attesa.
  * Rilascio/rinnovo di un lease più stabile/efficiente. Il rinnovo e il rilascio di un lease con una partizione è indipendente dal rinnovo degli altri. Nella versione v1 questa operazione veniva eseguita in sequenza per tutte le partizioni.
* Nuova API v2:
  * Modello di generatore per la costruzione flessibile del processore: classe ChangeFeedProcessorBuilder.
    * Può accettare qualsiasi combinazione di parametri.
    * Può accettare l'istanza DocumentClient per il monitoraggio e/o la raccolta di lease (non disponibile in v1).
  * IChangeFeedObserver.ProcessChangesAsync accetta ora CancellationToken.
  * IRemainingWorkEstimator: la stima del lavoro rimanente può essere usata separatamente dal processore.
  * Nuovi punti di estendibilità:
    * IPartitionLoadBalancingStrategy: per il bilanciamento del carico personalizzato di partizioni tra istanze del processore.
    * ILease, ILeaseManager: per la gestione personalizzata dei lease.
    * IPartitionProcessor: per modifiche di elaborazione personalizzate in una partizione.
* Registrazione: usa la libreria [LibLog](https://github.com/damianh/LibLog).
* 100% compatibile con l'API v1.
* Nuova codebase.
* Compatibile con [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21.1 e versioni successive.

### <a name="v1-builds"></a>Build della prima versione

### <a name="133"></a><a id="1.3.3"></a>1.3.3
* Aggiunta di maggiore registrazione.
* Correzione di un problema DocumentClient quando si chiama più volte la stima del lavoro in sospeso.

### <a name="132"></a><a id="1.3.2"></a>1.3.2
* Correzioni per la stima del lavoro in sospeso.

### <a name="131"></a><a id="1.3.1"></a>1.3.1
* Miglioramenti della stabilità.
  * Correzione per la gestione del problema delle attività annullate che potrebbe comportare l'arresto degli osservatori in alcune partizioni.
* Supporto per la creazione di checkpoint manuale.
* Compatibile con [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21 e versioni successive.

### <a name="120"></a><a id="1.2.0"></a>1.2.0
* Aggiunge il supporto per .NET Standard 2.0. Il pacchetto ora supporta i moniker framework `netstandard2.0` e `net451`.
* Compatibile con [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.17.0 e versioni successive.
* Compatibile con [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 1.5.1 e versioni successive.

### <a name="111"></a><a id="1.1.1"></a>1.1.1
* Correzione di un problema relativo al calcolo della stima del lavoro rimanente quando il feed di modifiche è vuoto o non ci sono lavori in sospeso.
* Compatibile con [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 e versioni successive.

### <a name="110"></a><a id="1.1.0"></a>1.1.0
* Aggiungere un metodo per ottenere una stima di quanto resta da elaborare nel feed di modifiche.
* Compatibile con [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 e versioni successive.

### <a name="100"></a><a id="1.0.0"></a>1.0.0
* SDK con disponibilità generale
* Compatibile con [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.14.1 e versioni precedenti.

## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro

Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata. Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente. È quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima.

> [!WARNING]
> Dopo il 31 agosto 2022, Azure Cosmos DB non eseguirà più correzioni dei bug, non aggiungerà nuove funzionalità, né fornirà supporto per le versioni 1.x dell'API Azure Cosmos DB .NET o .NET Core SDK per SQL. Se si preferisce non eseguire l'aggiornamento, le richieste inviate dalla versione 1.x dell'SDK continueranno a essere gestite dal servizio Azure Cosmos DB.

<br/>

| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [2.3.2](#2.3.2) |11 agosto 2020 |--- |
| [2.3.1](#2.3.1) |30 luglio 2020 |--- |
| [2.3.0](#2.3.0) |2 aprile 2020 |--- |
| [2.2.8](#2.2.8) |28 ottobre 2019 |--- |
| [2.2.7](#2.2.7) |14 maggio 2019 |--- |
| [2.2.6](#2.2.6) |29 gennaio 2019 |--- |
| [2.2.5](#2.2.5) |13 dicembre 2018 |--- |
| [2.2.4](#2.2.4) |29 novembre 2018 |--- |
| [2.2.3](#2.2.3) |19 novembre 2018 |--- |
| [2.2.2](#2.2.2) |31 ottobre 2018 |--- |
| [2.2.1](#2.2.1) |24 ottobre 2018 |--- |
| [1.3.3](#1.3.3) |8 maggio 2018 |--- |
| [1.3.2](#1.3.2) |18 aprile 2018 |--- |
| [1.3.1](#1.3.1) |13 marzo 2018 |--- |
| [1.2.0](#1.2.0) |31 ottobre 2017 |--- |
| [1.1.1](#1.1.1) |29 agosto 2017 |--- |
| [1.1.0](#1.1.0) |13 agosto 2017 |--- |
| [1.0.0](#1.0.0) |07 luglio 2017 |--- |

## <a name="faq"></a>Domande frequenti

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche

Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
