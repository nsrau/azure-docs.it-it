---
title: "Azure Cosmos DB: API.NET, SDK e risorse dell'esecuzione bulk"
description: Informazioni complete sull'SDK e sull'API .NET per BulkExecutor, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB BulkExecutor .NET SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/27/2020
ms.author: anfeldma
ms.openlocfilehash: 5fcde46738417a03cac8e5e99bea840ce2fd8bc6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392098"
---
# <a name="net-bulk-executor-library-download-information"></a>Libreria dell'esecuzione bulk .NET: informazioni sul download 

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [SDK di feed di modifiche .NET v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provider di risorse REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor in blocco-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Esecuzione bulk - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Descrizione**| La libreria dell'esecuzione bulk consente alle applicazioni client di eseguire operazioni in blocco su account di Azure Cosmos DB. Questa libreria rende disponibili gli spazi dei nomi BulkImport, BulkUpdate e BulkDelete. Il modulo BulkImport può inserire in blocco i documenti in maniera ottimizzata, in modo che la velocità effettiva di cui è stato eseguito il provisioning per una raccolta venga utilizzata nella misura massima consentita. Il modulo BulkUpdate può aggiornare in blocco i dati esistenti nei contenitori di Azure Cosmos come patch. Il modulo BulkDelete può eliminare in blocco i documenti in maniera ottimizzata, in modo che la velocità effettiva di cui è stato eseguito il provisioning per una raccolta venga utilizzata nella misura massima consentita.|
|**Download dell'SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Libreria di esecuzione bulk in GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Documentazione sull'API**|[Documentazione di riferimento API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Operazioni preliminari**|[Introduzione a .NET SDK per la libreria dell'executor bulk](bulk-executor-dot-net.md)|
| **Framework attualmente supportato**| Microsoft .NET Framework 4.5.2, 4.6.1 e .NET Standard 2.0 |

> [!NOTE]
> Se si usa l'esecuzione bulk, vedere la versione 3.x più recente di [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md), che include l'esecuzione bulk incorporata nell'SDK. 

## <a name="release-notes"></a>Note sulla versione

### <a name="241-preview"></a><a name="2.4.1-preview"></a>2.4.1-preview

* Correzione di TotalElapsedTime nella risposta di BulkDelete per misurare correttamente il tempo totale, incluso eventuali tentativi.

### <a name="240-preview"></a><a name="2.4.0-preview"></a>2.4.0-preview

* Dipendenza dell'SDK modificata in >= 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"></a>2.3.0-preview2

* Supporto aggiunto per consentire all'esecuzione bulk del grafo di accettare TTL in vertici e bordi

### <a name="220-preview2"></a><a name="2.2.0-preview2"></a>2.2.0-preview2

* Risolto un problema che causava eccezioni durante il ridimensionamento elastico di Azure Cosmos DB durante l'esecuzione in modalità gateway. Questa correzione lo rende equivalente dal punto di vista funzionale alla versione 1.4.1.

### <a name="210-preview2"></a><a name="2.1.0-preview2"></a>2.1.0-preview2

* Supporto BulkDelete aggiunto per fare in modo che gli account API SQL accettino la chiave di partizione e le tuple di ID documento da eliminare. Questa modifica lo rende equivalente dal punto di vista funzionale alla versione 1.4.0.

### <a name="200-preview2"></a><a name="2.0.0-preview2"></a>2.0.0-preview2

* Incluso MongoBulkExecutor per il supporto di .NET Standard 2.0. Questa funzionalità lo rende funzionalmente equivalente alla versione 1.3.0, con l'aggiunta del supporto di .NET Standard 2.0 come framework di destinazione.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-preview

* .NET Standard 2.0 aggiunto come uno dei framework di destinazione supportati per il funzionamento della libreria dell'esecuzione bulk con le applicazioni .NET Core.

### <a name="189"></a><a name="1.8.9"></a>1.8.9

* Correzione di un problema relativo a BulkDeleteAsync quando i valori con virgolette precedute sono passati come parametri di input.

### <a name="188"></a><a name="1.8.8"></a>1.8.8

* Problema risolto in MongoBulkExecutor che aumentava la dimensione dei documenti in modo imprevisto aggiungendo spaziatura interna e, in alcuni casi, superando il limite massimo consentito per le dimensioni dei documenti.

### <a name="187"></a><a name="1.8.7"></a>1.8.7

* Problema risolto relativo a BulkDeleteAsync quando la raccolta contiene percorsi di chiavi di partizione annidati.

### <a name="186"></a><a name="1.8.6"></a>1.8.6

* MongoBulkExecutor implementa ora IDisposable e viene eliminato dopo l'uso.

### <a name="185"></a><a name="1.8.5"></a>1.8.5

* Blocco rimosso nella versione dell'SDK. Il pacchetto è ora dipendente dall'SDK >= 2.5.1.

### <a name="184"></a><a name="1.8.4"></a>1.8.4

* Gestione degli identificatori corretta durante la chiamata di BulkImport con un elenco di oggetti POCO con valori numerici.

### <a name="183"></a><a name="1.8.3"></a>1.8.3

* Correzione di TotalElapsedTime nella risposta di BulkDelete per misurare correttamente il tempo totale, incluso eventuali tentativi.

### <a name="182"></a><a name="1.8.2"></a>1.8.2

* Uso elevato della CPU corretto in determinati scenari.
* La traccia ora usa TraceSource. Gli utenti possono definire listener per l'origine `BulkExecutorTrace`.
* Scenario raro corretto che poteva causare un blocco quando si inviavano documenti di dimensioni vicine ai 2 MB.

### <a name="160"></a><a name="1.6.0"></a>1.6.0

* Esecuzione bulk aggiornata per consentire l'uso della versione più recente di Azure Cosmos DB .NET SDK (2.4.0)

### <a name="150"></a><a name="1.5.0"></a>1.5.0

* Supporto aggiunto per consentire all'esecuzione bulk del grafo di accettare TTL in vertici e bordi

### <a name="141"></a><a name="1.4.1"></a>1.4.1

* Risolto un problema che causava eccezioni durante il ridimensionamento elastico di Azure Cosmos DB durante l'esecuzione in modalità gateway.

### <a name="140"></a><a name="1.4.0"></a>1.4.0

* Supporto BulkDelete aggiunto per fare in modo che gli account API SQL accettino la chiave di partizione e le tuple di ID documento da eliminare.

### <a name="130"></a><a name="1.3.0"></a>1.3.0

* Problema risolto che causava un problema di formattazione nell'agente utente usato dall'esecuzione bulk.

### <a name="120"></a><a name="1.2.0"></a>1.2.0

* Miglioramenti apportati alle API di importazione e aggiornamento dell'esecuzione bulk per l'adattamento trasparente al ridimensionamento elastico del contenitore Cosmos quando l'archiviazione supera la capacità corrente senza generare eccezioni.

### <a name="112"></a><a name="1.1.2"></a>1.1.2

* La dipendenza di DocumentDB .NET SDK è stata incrementata alla versione 2.1.3.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Problema risolto a causa del quale l'esecuzione bulk generava un errore JSRT durante l'importazione in raccolte fisse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* È stato aggiunto il supporto per l'operazione BulkDelete per gli account API SQL di Azure Cosmos DB.
* È stato aggiunto il supporto per l'operazione BulkImport per gli account con API per MongoDB di Azure Cosmos DB.
* La dipendenza di DocumentDB .NET SDK è stata incrementata alla versione 2.0.0. 

### <a name="102"></a><a name="1.0.2"></a>1.0.2

* È stato aggiunto il supporto per l'operazione BulkImport per gli account API Gremlin di Azure Cosmos DB.

### <a name="101"></a><a name="1.0.1"></a>1.0.1

* Sono stati corretti bug secondari dell'operazione BulkImport per gli account API SQL di Azure Cosmos DB.

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* È stato aggiunto il supporto per le operazioni BulkImport e BulkUpdate per gli account API SQL di Azure Cosmos DB.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla libreria Java di esecuzione bulk, vedere l'articolo seguente:

[Libreria Java di esecuzione bulk: informazioni sul download](sql-api-sdk-bulk-executor-java.md)
