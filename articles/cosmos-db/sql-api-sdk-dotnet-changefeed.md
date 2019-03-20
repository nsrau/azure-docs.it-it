---
title: 'Azure Cosmos DB: risorse, SDK e API del processore dei feed di modifiche .NET'
description: Tutte le informazioni sull'SDK e sull'API del processore dei feed delle modifiche, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le versioni dell'SDK del processore dei feed delle modifiche .NET.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: a878ab1937b06f06a27b18f793fc1bfa190969ed
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090339"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET Change Feed Processor SDK: download e note sulla versione
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed delle modifiche .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**Download dell'SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Documentazione sull'API**|[Documentazione di riferimento sull'API della libreria del processore dei feed delle modifiche](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Introduzione**|[Introduzione all'SDK del processore dei feed delle modifiche .NET](change-feed.md)|
|**Framework attualmente supportato**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Note sulla versione

### <a name="v2-builds"></a>Build della seconda versione

### <a name="a-name226226"></a><a name="2.2.6"/>2.2.6
* Gestione delle eccezioni di Observer migliorata.
* Informazioni più dettagliate sugli errori di Observer:
  * Quando un'istanza di Observer viene chiusa a causa di un'eccezione generata da ProcessChangesAsync di Observer, CloseAsync riceverà il parametro del motivo impostato su ChangeFeedObserverCloseReason.ObserverError.
  * Sono state aggiunte funzionalità di analisi per identificare errori nel codice utente in un'istanza di Observer.

### <a name="a-name225225"></a><a name="2.2.5"/>2.2.5
* Aggiunta del supporto per la gestione della suddivisione nelle raccolte che usano la velocità effettiva per database condivisi.
  * Questa versione corregge un problema che poteva verificarsi durante la suddivisione in raccolte che usano la velocità effettiva per database condivisi quando la suddivisione ha come risultato un nuovo bilanciamento delle partizioni e la creazione di un solo intervallo di chiavi di partizione figlio, anziché di due. Quando ciò si verifica, il processore dei feed di modifiche può rimanere bloccato durante l'eliminazione del lease per l'intervallo di chiavi di partizione precedente e non creare nuovi lease. Il problema è stato corretto in questa versione.

### <a name="a-name224224"></a><a name="2.2.4"/>2.2.4
* Nuova proprietà aggiunta ChangeFeedProcessorOptions.StartContinuation per supportare l'avvio del feed di modifica dal token di continuazione della richiesta. Viene utilizzato solo quando la raccolta di lease è vuota o un lease non ha impostato ContinuationToken. Per i lease nella raccolta di lease con ContinuationToken impostato, viene usato ContinuationToken e viene ignorato ChangeFeedProcessorOptions.StartContinuation.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* Aggiunto il supporto per l'uso dell’archivio personalizzato per rendere persistenti i token di continuazione per ogni partizione.
  * Ad esempio, un archivio personalizzato lease può essere raccolta di lease Azure Cosmos DB partizionata in modo personalizzato.
  * Gli archivi di lease personalizzati possono utilizzare il nuovo punto di estensibilità ChangeFeedProcessorBuilder.WithLeaseStoreManager (ILeaseStoreManager) e l’interfaccia pubblica ILeaseStoreManager.
  * L'interfaccia di ILeaseManager è stata trasformata in interfacce di ruolo multiple.
* Modifica di rilievo secondaria: rimosso il punto di estensibilità ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager), viene utilizzato ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) al suo posto.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Questa versione corregge un problema che si verifica durante l'elaborazione di una divisione nella raccolta monitorata e l'utilizzo di una raccolta di lease partizionata. Durante l'elaborazione di un lease per partizione divisa, il lease corrispondente a quella partizione non può essere eliminato. Il problema è stato corretto in questa versione.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Correzione del calcolo di stima per gli account Multimaster e nuovo formato del Token di sessione.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Aggiunta del supporto per le raccolte partizionate di lease. La chiave di partizione deve essere definita come /id.
* Modifica che causa un'interruzione minore: i metodi dell'interfaccia IChangeFeedDocumentClient e la classe ChangeFeedDocumentClient sono stati modificati per includere i parametri RequestOptions e CancellationToken. IChangeFeedDocumentClient è un punto di estendibilità avanzata che consente di offrire un'implementazione personalizzata del client di documenti da usare con il processore dei feed delle modifiche, ad esempio, decorare DocumentClient e intercettare tutte le chiamate a esso per eseguire operazioni di analisi, gestione degli errori, e così via, extra. Con questo aggiornamento, il codice che implementa IChangeFeedDocumentClient dovrà essere modificato per includere i nuovi parametri nell'implementazione.
* Miglioramenti della diagnostica secondari.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* È stata aggiunta una nuova API, Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt; &gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(), utilizzabile per ottenere il lavoro stimato per ogni partizione.
* Supporta Microsoft.Azure.DocumentDB SDK 2.0. Richiede Microsoft.Azure.DocumentDB 2.0 o versione successiva.

### <a name="a-name206206"></a><a name="2.0.6"/>2.0.6
* È stata aggiunta la proprietà pubblica ChangeFeedEventHost.HostName per la compatibilità con la versione 1.

### <a name="a-name205205"></a><a name="2.0.5"/>2.0.5
* È stata corretta una race condition che si verifica durante la suddivisione della partizione. Per effetto della race condition è possibile che il lease venga acquisito e immediatamente perso durante la suddivisione della partizione, provocando una contesa. Con questa versione il problema della race condition è stato risolto.

### <a name="a-name204204"></a><a name="2.0.4"/>2.0.4
* SDK con disponibilità generale

### <a name="a-name203-prerelease203-prerelease"></a><a name="2.0.3-prerelease"/>Versione provvisoria 2.0.3
* Sono stati corretti i problemi seguenti:
  * Quando si verifica la suddivisione della partizione, può verificarsi l'elaborazione duplicati dei documenti modificati prima della divisione.
  * L'API GetEstimatedRemainingWork ha restituito 0 se non era presente alcun lease nella raccolta di lease.

* Le eccezioni seguenti sono rese pubbliche. Le estensioni che implementano IPartitionProcessor possono generare queste eccezioni.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-prerelease
* Modifiche API secondarie:
  * Rimozione di ChangeFeedProcessorOptions.IsAutoCheckpointEnabled, contrassegnato come obsoleto.

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
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

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* Aggiunta di maggiore registrazione.
* Correzione di un problema DocumentClient quando si chiama più volte la stima del lavoro in sospeso.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* Correzioni per la stima del lavoro in sospeso.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Miglioramenti della stabilità.
  * Correzione di gestione delle attività annullate che potrebbe causare agli Observer arrestate in alcune partizioni.
* Supporto per la creazione di checkpoint manuale.
* Compatibile con [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21 e versioni successive.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Aggiunge il supporto per .NET Standard 2.0. Il pacchetto ora supporta i moniker framework `netstandard2.0` e `net451`.
* Compatibile con [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.17.0 e versioni successive.
* Compatibile con [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 1.5.1 e versioni successive.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Correzione di un problema relativo al calcolo della stima del lavoro rimanente quando il feed di modifiche è vuoto o non ci sono lavori in sospeso.
* Compatibile con [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 e versioni successive.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Aggiungere un metodo per ottenere una stima di quanto resta da elaborare nel feed delle modifiche.
* Compatibile con [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 e versioni successive.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK con disponibilità generale
* Compatibile con [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.14.1 e versioni precedenti.


## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente. È quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima. 

Qualsiasi richiesta inviata a Cosmos DB con un SDK ritirato verrà rifiutata dal servizio.

<br/>

| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
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

