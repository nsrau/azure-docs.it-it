---
title: 'Database Cosmos di Azure: BULK executor .NET API, SDK & risorse'
description: Informazioni complete sull'SDK e sull'API .NET per BulkExecutor, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB BulkExecutor .NET SDK.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/16/2020
ms.author: ramkris
ms.openlocfilehash: 1a8040fc397b526b540ce9343baa985cab49e2b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169406"
---
# <a name="net-bulk-executor-library-download-information"></a>Libreria dell'executor bulk .NET: informazioni sul download 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed di modifiche .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Esecutore in blocco - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk executor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Descrizione**| La libreria dell'executor bulk .Net consente alle applicazioni client di eseguire operazioni bulk sugli account di Azure Cosmos DB. Questa libreria fornisce gli spazi dei nomi BulkImport, BulkUpdate e BulkDelete.This library provides BulkImport, BulkUpdate, and BulkDelete namespaces. Il modulo BulkImport può inserire in blocco i documenti in maniera ottimizzata, in modo che la velocità effettiva di cui è stato eseguito il provisioning per una raccolta venga utilizzata nella misura massima consentita. Il modulo BulkUpdate può aggiornare in blocco i dati esistenti nei contenitori Cosmos di Azure come patch. Il modulo BulkDelete può eliminare in blocco i documenti in maniera ottimizzata, in modo che la velocità effettiva di cui è stato eseguito il provisioning per una raccolta venga utilizzata nella misura massima consentita.|
|**Download dell'SDK**| [Nuget](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Bulk executor library in GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Documentazione API**|[Documentazione di riferimento per le API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Introduzione**|[Introduzione a .NET SDK per la libreria dell'executor bulk](bulk-executor-dot-net.md)|
| **Framework attualmente supportato**| Microsoft .NET Framework 4.5.2, 4.6.1 e .NET Standard 2.0 |

> [!NOTE]
> Se si utilizza l'esecutore bulk, vedere la versione 3.x più recente di [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md), che dispone di esecutore bulk incorporato nell'SDK. 

## <a name="release-notes"></a>Note sulla versione

### <a name="241-preview"></a><a name="2.4.1-preview"/>2.4.1-anteprima

* TotalElapsedTime fisso nella risposta di BulkDelete per misurare correttamente il tempo totale inclusi eventuali tentativi.

### <a name="240-preview"></a><a name="2.4.0-preview"/>2.4.0-anteprima

* Modificata la dipendenza dell'SDK in >- 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-anteprima2

* Aggiunto il supporto per l'esecutore di massa del grafico per accettare ttl su vertici e bordi

### <a name="220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-anteprima2

* Risolto un problema che causava eccezioni durante il ridimensionamento elastico del database Cosmos di Azure durante l'esecuzione in modalità Gateway.Fixed an issue, which caused exceptions during elastic scaling of Azure Cosmos DB when running in Gateway mode. Questa correzione lo rende funzionalmente equivalente alla versione 1.4.1.

### <a name="210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-anteprima2

* Aggiunto il supporto BulkDelete per gli account API SQL per accettare la chiave di partizione, le tuple id documento da eliminare. Questa modifica lo rende funzionalmente equivalente alla versione 1.4.0.This change makes it functionally equivalent to 1.4.0 release.

### <a name="200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Incluso MongoBulkExecutor per il supporto di .NET Standard 2.0. Questa funzionalità lo rende funzionalmente equivalente alla versione 1.3.0, con l'aggiunta del supporto di .NET Standard 2.0 come framework di destinazione.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* Aggiunto .NET Standard 2.0 come uno dei framework di destinazione supportati per far funzionare la libreria dell'esecutore bulk con le applicazioni .NET Core.

### <a name="188"></a><a name="1.8.8"/>1.8.8

* Risolto un problema in MongoBulkExecutor che aumentava le dimensioni del documento in modo imprevisto aggiungendo spaziatura interna e, in alcuni casi, superando il limite massimo di dimensioni del documento.

### <a name="187"></a><a name="1.8.7"/>1.8.7

* È stato risolto un problema con BulkDeleteAsync quando la raccolta ha percorsi di chiave di partizione annidati.

### <a name="186"></a><a name="1.8.6"/>1.8.6

* MongoBulkExecutor implementa ora IDisposable e deve essere eliminato dopo l'utilizzo.

### <a name="185"></a><a name="1.8.5"/>1.8.5

* Rimosso il blocco sulla versione SDK. Il pacchetto dipende ora dal >SDK - 2.5.1.

### <a name="184"></a><a name="1.8.4"/>1.8.4

* Correzione della gestione degli identificatori quando si chiama BulkImport con un elenco di oggetti POCO con valori numerici.

### <a name="183"></a><a name="1.8.3"/>1.8.3

* TotalElapsedTime fisso nella risposta di BulkDelete per misurare correttamente il tempo totale inclusi eventuali tentativi.

### <a name="182"></a><a name="1.8.2"/>1.8.2

* Utilizzo elevato della CPU in determinati scenari.
* La traccia ora usa TraceSource.Tracing now uses TraceSource. Gli utenti possono definire `BulkExecutorTrace` i listener per l'origine.
* Risolto uno scenario raro che poteva causare un blocco durante l'invio di documenti vicino a 2 MB di dimensioni.

### <a name="160"></a><a name="1.6.0"/>1.6.0

* Aggiornato l'esecutore bulk per usare ora la versione più recente di Azure Cosmos DB .NET SDK (2.4.0)

### <a name="150"></a><a name="1.5.0"/>1.5.0

* Aggiunto il supporto per l'esecutore di massa del grafico per accettare ttl su vertici e bordi

### <a name="141"></a><a name="1.4.1"/>1.4.1

* Risolto un problema che causava eccezioni durante il ridimensionamento elastico del database Cosmos di Azure durante l'esecuzione in modalità Gateway.Fixed an issue, which caused exceptions during elastic scaling of Azure Cosmos DB when running in Gateway mode.

### <a name="140"></a><a name="1.4.0"/>1.4.0

* Aggiunto il supporto BulkDelete per gli account API SQL per accettare la chiave di partizione, le tuple id documento da eliminare.

### <a name="130"></a><a name="1.3.0"/>1.3.0

* Risolto un problema, che causava un problema di formattazione nell'agente utente utilizzato dall'esecutore in blocco.

### <a name="120"></a><a name="1.2.0"/>1.2.0

* Miglioramento delle API di importazione e aggiornamento degli esecutori bulk per adattarsi in modo trasparente al ridimensionamento elastico del contenitore Cosmos quando lo storage supera la capacità corrente senza generare eccezioni.

### <a name="112"></a><a name="1.1.2"/>1.1.2

* La dipendenza di DocumentDB .NET SDK è stata incrementata alla versione 2.1.3.

### <a name="111"></a><a name="1.1.1"/>1.1.1

* Risolto un problema, che causava l'errore JSRT dell'esecutore bulk durante l'importazione in raccolte fisse.

### <a name="110"></a><a name="1.1.0"/>1.1.0

* È stato aggiunto il supporto per l'operazione BulkDelete per gli account API SQL di Azure Cosmos DB.
* È stato aggiunto il supporto per l'operazione BulkImport per gli account con API per MongoDB di Azure Cosmos DB.
* La dipendenza di DocumentDB .NET SDK è stata incrementata alla versione 2.0.0. 

### <a name="102"></a><a name="1.0.2"/>1.0.2

* È stato aggiunto il supporto per l'operazione BulkImport per gli account API Gremlin di Azure Cosmos DB.

### <a name="101"></a><a name="1.0.1"/>1.0.1

* Sono stati corretti bug secondari dell'operazione BulkImport per gli account API SQL di Azure Cosmos DB.

### <a name="100"></a><a name="1.0.0"/>1.0.0

* È stato aggiunto il supporto per le operazioni BulkImport e BulkUpdate per gli account API SQL di Azure Cosmos DB.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla libreria Java dell'executor bulk, vedere il seguente articolo:

[Java bulk executor library SDK e informazioni sulla versione](sql-api-sdk-bulk-executor-java.md)
