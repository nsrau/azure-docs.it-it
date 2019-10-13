---
title: "Azure Cosmos DB: API .NET dell'executor in blocco, risorse & SDK"
description: Informazioni complete sull'SDK e sull'API .NET per BulkExecutor, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB BulkExecutor .NET SDK.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/11/2019
ms.author: ramkris
ms.openlocfilehash: 7d30b685d0debb0aeb0f79db8b078dcc165a8ca1
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286661"
---
# <a name="net-bulk-executor-library-download-information"></a>Libreria di esecuzioni bulk di .NET: informazioni sul download 

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
> * [Executor in blocco-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor in blocco-Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Descrizione**| La libreria di esecuzioni bulk di .NET consente alle applicazioni client di eseguire operazioni bulk sugli account Azure Cosmos DB. Questa libreria fornisce gli spazi dei nomi BulkImport, BulkUpdate e BulkDelete. Il modulo BulkImport può inserire in blocco i documenti in maniera ottimizzata, in modo che la velocità effettiva di cui è stato eseguito il provisioning per una raccolta venga utilizzata nella misura massima consentita. Il modulo BulkUpdate può aggiornare in blocco i dati esistenti in Azure Cosmos Containers come patch. Il modulo BulkDelete può eliminare in blocco i documenti in maniera ottimizzata, in modo che la velocità effettiva di cui è stato eseguito il provisioning per una raccolta venga utilizzata nella misura massima consentita.|
|**Download dell'SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Libreria Executor in blocco in GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Documentazione sull'API**|[Documentazione di riferimento API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Introduzione**|[Introduzione a .NET SDK per la libreria dell'executor bulk](bulk-executor-dot-net.md)|
| **Framework attualmente supportato**| Microsoft .NET Framework 4.5.2, 4.6.1 e .NET Standard 2.0 |

## <a name="release-notes"></a>Note sulla versione

### <a name="a-name241-preview241-preview"></a><a name="2.4.1-preview"/>2.4.1-Preview

* Correzione di TotalElapsedTime nella risposta di BulkDelete per misurare correttamente il tempo totale incluso eventuali tentativi.

### <a name="a-name240-preview240-preview"></a><a name="2.4.0-preview"/>2.4.0-Preview

* La dipendenza dell'SDK è stata modificata in > = 2.5.1

### <a name="a-name230-preview2230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* Aggiunta del supporto per l'esecutore bulk del grafo per accettare la durata (TTL) in vertici e bordi

### <a name="a-name220-preview2220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-preview2

* È stato risolto un problema che causava eccezioni durante il ridimensionamento elastico di Azure Cosmos DB durante l'esecuzione in modalità gateway. Questa correzione lo rende equivalente dal punto di vista funzionale alla versione 1.4.1.

### <a name="a-name210-preview2210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-preview2

* Aggiunto il supporto BulkDelete per gli account API SQL per accettare la chiave di partizione e le tuple ID documento da eliminare. Questa modifica lo rende equivalente dal punto di vista funzionale alla versione 1.4.0.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Incluso MongoBulkExecutor per il supporto di .NET Standard 2.0. Questa funzionalità lo rende funzionalmente equivalente alla versione 1.3.0, con l'aggiunta del supporto di .NET Standard 2.0 come framework di destinazione.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* Aggiunta di .NET Standard 2,0 come uno dei framework di destinazione supportati per far funzionare la libreria dell'executor bulk con le applicazioni .NET Core.

### <a name="a-name183183"></a><a name="1.8.3"/>1.8.3

* Correzione di TotalElapsedTime nella risposta di BulkDelete per misurare correttamente il tempo totale incluso eventuali tentativi.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Correzione dell'utilizzo elevato della CPU in determinati scenari.
* La traccia ora usa TraceSource. Gli utenti possono definire i listener per l'origine `BulkExecutorTrace`.
* Correzione di uno scenario raro che può causare un blocco quando si inviano documenti vicino a 2 MB di dimensioni.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Aggiornamento dell'executor bulk per l'utilizzo della versione più recente di Azure Cosmos DB .NET SDK (2.4.0)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0

* Aggiunta del supporto per l'esecutore bulk del grafo per accettare la durata (TTL) in vertici e bordi

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

* È stato risolto un problema che causava eccezioni durante il ridimensionamento elastico di Azure Cosmos DB durante l'esecuzione in modalità gateway.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

* Aggiunto il supporto BulkDelete per gli account API SQL per accettare la chiave di partizione e le tuple ID documento da eliminare.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* È stato risolto un problema che causava un problema di formattazione nell'agente utente usato dall'executor in blocco.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Miglioramento delle API di importazione e aggiornamento in blocco dell'Executor per adattarsi in modo trasparente alla scalabilità elastica del contenitore Cosmos quando l'archiviazione supera la capacità corrente senza generare eccezioni.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* La dipendenza di DocumentDB .NET SDK è stata incrementata alla versione 2.1.3.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* È stato risolto un problema che ha causato la generazione di un errore JSRT durante l'importazione in raccolte fisse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* È stato aggiunto il supporto per l'operazione BulkDelete per gli account API SQL di Azure Cosmos DB.
* È stato aggiunto il supporto per l'operazione BulkImport per gli account con API per MongoDB di Azure Cosmos DB.
* La dipendenza di DocumentDB .NET SDK è stata incrementata alla versione 2.0.0. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* È stato aggiunto il supporto per l'operazione BulkImport per gli account API Gremlin di Azure Cosmos DB.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Sono stati corretti bug secondari dell'operazione BulkImport per gli account API SQL di Azure Cosmos DB.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* È stato aggiunto il supporto per le operazioni BulkImport e BulkUpdate per gli account API SQL di Azure Cosmos DB.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla libreria Java per esecuzioni bulk, vedere l'articolo seguente:

[Java bulk Executor Library SDK e informazioni sulla versione](sql-api-sdk-bulk-executor-java.md)
