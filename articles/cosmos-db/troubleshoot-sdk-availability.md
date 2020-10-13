---
title: Diagnosticare e risolvere i problemi relativi alla disponibilità di Azure Cosmos SDK in ambienti multiarea
description: Informazioni sul comportamento di disponibilità di Azure Cosmos SDK quando si opera in ambienti con più aree.
author: ealsur
ms.service: cosmos-db
ms.date: 10/05/2020
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 400795d20b6e7ad919f5cbbfa6078987bb65297e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743965"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>Diagnosticare e risolvere i problemi relativi alla disponibilità di Azure Cosmos SDK in ambienti multiarea

Questo articolo descrive il comportamento della versione più recente di Azure Cosmos SDK quando viene riscontrato un problema di connettività a una determinata area o quando si verifica un failover in un'area.

Tutti gli SDK di Azure Cosmos offrono un'opzione per personalizzare le preferenze internazionali. In SDK diversi vengono usate le proprietà seguenti:

* Proprietà [ConnectionPolicy. PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) in .NET v2 SDK.
* Proprietà [CosmosClientOptions. ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) o [CosmosClientOptions. ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) in .NET V3 SDK.
* Metodo [CosmosClientBuilder. preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) in Java v4 SDK.
* Il parametro [CosmosClient.preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) in Python SDK.
* Il parametro [CosmosClientOptions. ConnectionPolicy. preferredLocations](/javascript/api/@azure/cosmos/connectionpolicy#preferredlocations) in JS SDK.

Quando si imposta la preferenza a livello di area, il client si connetterà a un'area come indicato nella tabella seguente:

|Tipo di account |Letture |Scritture |
|------------------------|--|--|
| Singola area di scrittura | Area preferita | Area primaria  |
| Più aree di scrittura | Area preferita | Area preferita  |

Se non si imposta un'area preferita:

|Tipo di account |Letture |Scritture |
|------------------------|--|--|
| Singola area di scrittura | Area primaria | Area primaria |
| Più aree di scrittura | Area primaria  | Area primaria  |

> [!NOTE]
> L'area primaria si riferisce alla prima area nell' [elenco delle aree dell'account Azure Cosmos](distribute-data-globally.md)

Quando si verifica uno degli scenari seguenti, il client che usa Azure Cosmos SDK espone i log e include le informazioni sui tentativi come parte delle **informazioni di diagnostica dell'operazione**:

* Proprietà *RequestDiagnosticsString* sulle risposte in .NET v2 SDK.
* Proprietà di *diagnostica* sulle risposte e sulle eccezioni in .NET V3 SDK.
* Metodo *Getdiagnostics ()* sulle risposte e sulle eccezioni in Java v4 SDK.

Quando si determina l'area successiva in ordine di preferenza, il client SDK utilizzerà l'elenco dell'area account, assegnando la priorità alle aree preferite (se presenti).

Per informazioni dettagliate sulle garanzie dei contratti di contratto durante questi eventi, vedere [contratti di stato per la disponibilità](high-availability.md#slas-for-availability).

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>Rimozione di un'area dall'account

Quando si rimuove un'area da un account Azure Cosmos, qualsiasi client SDK che usa attivamente l'account rileverà la rimozione dell'area tramite un codice di risposta back-end. Il client contrassegna quindi l'endpoint a livello di area come non disponibile. Il client ritenta l'operazione corrente e tutte le operazioni future vengono indirizzate in modo permanente all'area successiva in ordine di preferenza.

## <a name="adding-a-region-to-an-account"></a>Aggiunta di un'area a un account

Ogni 5 minuti il client Azure Cosmos SDK legge la configurazione dell'account e aggiorna le aree di cui è a conoscenza.

Se si rimuove un'area e successivamente la si aggiunge di nuovo all'account, se l'area aggiunta ha un ordine di preferenza a livello di area superiore nella configurazione dell'SDK rispetto all'area connessa corrente, l'SDK tornerà a usare questa area in modo permanente. Dopo che l'area aggiunta è stata rilevata, tutte le richieste future verranno indirizzate a tale area.

Se si configura il client in modo che si connetta preferibilmente a un'area che non è presente nell'account Azure Cosmos, l'area preferita viene ignorata. Se si aggiunge tale area in un secondo momento, il client lo rileverà e passerà definitivamente a tale area.

## <a name="failover-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>Failover dell'area di scrittura in un singolo account di area di scrittura

Se si avvia un failover dell'area di scrittura corrente, la richiesta di scrittura successiva avrà esito negativo con una risposta back-end nota. Quando viene rilevata questa risposta, il client esegue una query sull'account per apprendere la nuova area di scrittura e procede per ritentare l'operazione corrente e indirizzare definitivamente tutte le operazioni di scrittura future alla nuova area.

## <a name="regional-outage"></a>Interruzione a livello di area

Se l'account è un'area di scrittura singola e l'interruzione a livello di area si verifica durante un'operazione di scrittura, il comportamento è simile a quello di un [failover manuale](#manual-failover-single-region). Per le richieste di lettura o per più aree di scrittura, il comportamento è simile alla [rimozione di un'area](#remove-region).

## <a name="session-consistency-guarantees"></a>Garanzie di coerenza della sessione

Quando si usa la [coerenza di sessione](consistency-levels.md#guarantees-associated-with-consistency-levels), il client deve garantire che possa leggere le proprie scritture. In una singola area di scrittura gli account in cui la preferenza dell'area di lettura è diversa dall'area di scrittura, è possibile che si verifichino casi in cui l'utente emette una scrittura e durante un'operazione di lettura da un'area locale, l'area locale non ha ancora ricevuto la replica dei dati (velocità di vincolo leggero). In questi casi, l'SDK rileva l'errore specifico nell'operazione di lettura e ritenta la lettura nell'area dell'hub per garantire la coerenza della sessione.

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>Problemi di connettività temporanei sul protocollo TCP

Negli scenari in cui il client Azure Cosmos SDK è configurato per l'uso del protocollo TCP, per una determinata richiesta potrebbero esserci situazioni in cui le condizioni della rete influiscono temporaneamente sulla comunicazione con un endpoint specifico. Queste condizioni di rete temporanea possono emergere come timeout TCP. Il client tenterà di ritentare la richiesta localmente sullo stesso endpoint per alcuni secondi.

Se l'utente ha configurato un elenco di aree preferite con più di un'area e l'account Azure Cosmos è costituito da più aree di scrittura o da un'area di scrittura singola e l'operazione è una richiesta di lettura, il client ritenterà la singola operazione nell'area successiva dall'elenco delle preferenze.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare i [contratti di Service disponibili](high-availability.md#slas-for-availability).
* Usare la versione più recente di [.NET SDK](sql-api-sdk-dotnet-standard.md)
* Usare la versione più recente di [Java SDK](sql-api-sdk-java-v4.md)
* Usare la versione più recente di [Python SDK](sql-api-sdk-python.md)
* Usare la versione più recente di [node SDK](sql-api-sdk-node.md)
