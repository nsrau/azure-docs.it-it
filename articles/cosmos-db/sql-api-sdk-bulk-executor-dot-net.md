---
title: "Azure Cosmos DB: API, SDK e risorse dell'executor bulk .NET"
description: Informazioni complete sull'SDK e sull'API .NET per l'executor bulk, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB Bulk Executor .NET SDK.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/19/2018
ms.author: ramkris
ms.openlocfilehash: 4f2d8b3246901f139695998224dfe036cccb9833
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855785"
---
# <a name="net-bulk-executor-library-download-information"></a>Libreria dell'executor bulk .NET: informazioni sul download 

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
> * [Executor bulk - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor bulk - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Descrizione**| La libreria dell'executor bulk consente alle applicazioni client di eseguire operazioni in blocco su account di Azure Cosmos DB. La libreria dell'executor bulk rende disponibili gli spazi dei nomi BulkImport, BulkUpdate e BulkDelete. Il modulo BulkImport può inserire in blocco i documenti in maniera ottimizzata, in modo che la velocità effettiva di cui è stato eseguito il provisioning per una raccolta venga utilizzata nella misura massima consentita. Il modulo BulkUpdate può aggiornare in blocco i dati esistenti nei contenitori di Azure Cosmos DB come patch. Il modulo BulkDelete può eliminare in blocco i documenti in maniera ottimizzata, in modo che la velocità effettiva di cui è stato eseguito il provisioning per una raccolta venga utilizzata nella misura massima consentita.|
|**Download dell'SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Libreria BulkExecutor in GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Documentazione sull'API**|[Documentazione di riferimento API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Introduzione**|[Introduzione a .NET SDK per la libreria dell'executor bulk](bulk-executor-dot-net.md)|
| **Framework attualmente supportato**| Microsoft .NET Framework 4.5.2, 4.6.1 e .NET Standard 2.0 |

## <a name="release-notes"></a>Note sulla versione

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Incluso MongoBulkExecutor per il supporto di .NET Standard 2.0. Questa funzionalità lo rende funzionalmente equivalente alla versione 1.3.0, con l'aggiunta del supporto di .NET Standard 2.0 come framework di destinazione.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* È stato aggiunto .NET Standard 2.0 come uno dei framework di destinazione supportati per il funzionamento della libreria di BulkExecutor con le applicazioni .NET Core.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* È stato aggiunto un overload per l'operazione BulkDelete per fare in modo che gli account API SQL accettino la chiave di partizione e le tuple di ID documento da eliminare.
* È stato aggiunto un overload per l'operazione BulkDelete per fare in modo che gli account API SQL accettino RequestOptions contenenti la chiave di partizione che specifica il valore della chiave di partizione, oltre a usarla come filtro nella query di input che specifica i documenti da eliminare.
* È stato risolto un problema che ha causato un problema di formattazione nell'agente utente usato da BulkExecutor.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Miglioramenti delle API di importazione ed esportazione di BulkExecutor per l'adattamento trasparente al ridimensionamento elastico del contenitore Cosmos DB quando l'archiviazione supera la capacità corrente senza generare eccezioni.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* La dipendenza di DocumentDB .NET SDK è stata incrementata alla versione 2.1.3.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* È stato risolto un problema a causa del quale BulkExecutor generava un errore JSRT durante l'importazione in raccolte fisse.

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

Per altre informazioni sulla libreria Java BulkExecutor, vedere l'articolo seguente:

[Libreria Java BulkExecutor: informazioni sul download](sql-api-sdk-bulk-executor-java.md)
