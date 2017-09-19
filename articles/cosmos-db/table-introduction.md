---
title: Introduzione all'API di tabella di Azure Cosmos DB | Microsoft Docs
description: Informazioni su come usare Azure Cosmos DB per archiviare volumi elevati di dati di coppie chiave/valore ed eseguire query su di essi con bassa latenza tramite le diffuse API MongoDB OSS.
services: cosmos-db
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/29/2017
ms.author: arramac
ms.translationtype: HT
ms.sourcegitcommit: f2ac16c2f514aaa7e3f90fdf0d0b6d2912ef8485
ms.openlocfilehash: 2073948d44ccc4b9b83e4eaf4f250dc272e46292
ms.contentlocale: it-it
ms.lasthandoff: 09/08/2017

---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introduzione ad Azure Cosmos DB: API di tabella

[Azure Cosmos DB](introduction.md) offre l'API di tabella (anteprima) per applicazioni scritte per l'archiviazione tabelle di Azure e che necessitano di funzionalità Premium, come:

* [Distribuzione globale chiavi in mano](distribute-data-globally.md).
* [Velocità effettiva dedicata](partition-data.md) in tutto il mondo.
* Latenze pari a singole unità di millisecondi al 99° percentile.
* Disponibilità elevata garantita.
* [Indicizzazione secondaria automatica](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

È possibile eseguire la migrazione di queste applicazioni in Azure Cosmos DB usando l'API di tabella senza modifiche al codice e sfruttare quindi i vantaggi offerti dalle funzionalità Premium.

È consigliabile guardare il video riportato di seguito, in cui Aravind Ramachandran illustra come iniziare a usare l'API di tabella per Azure Cosmos DB:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Table-API-for-Azure-Cosmos-DB/player]
> 
> 

## <a name="premium-and-standard-table-apis"></a>API di tabella Premium e Standard
Se attualmente si usa l'archiviazione tabelle, è possibile ottenere i vantaggi seguenti passando alla "tabella Premium" in anteprima di Azure Cosmos DB:

| | Archiviazione tabelle di Azure | Azure Cosmos DB: archiviazione tabelle (anteprima) |
| --- | --- | --- |
| Latency | Veloce, senza limiti superiori per la latenza. | Latenza pari a singole unità di millisecondi per letture e scritture, supportata da latenza minore di 10 ms per le letture e di 15 ms per le scritture al 99° percentile, in base a qualsiasi livello di scalabilità e ovunque nel mondo. |
| Velocità effettiva | Modello a velocità effettiva variabile. Le tabelle hanno un limite di scalabilità di 20.000 operazioni al secondo. | Altamente scalabile con [velocità effettiva riservata dedicata per tabella](request-units.md), supportata da contratti di servizio. Non esiste un limite superiore di velocità effettiva per gli account, che supportano oltre 10 milioni di operazioni al secondo per tabella. |
| Distribuzione globale | Singola area con un'area di lettura secondaria leggibile facoltativa per la disponibilità elevata. Non è possibile avviare il failover. | [Distribuzione globale chiavi in mano](distribute-data-globally.md) da una a 30+ aree. Supporto per [failover automatici e manuali](regional-failover.md) in qualsiasi momento, ovunque nel mondo. |
| Indicizzazione | Solo indice primario in PartitionKey e RowKey. Nessun indice secondario. | Indicizzazione automatica e completa su tutte le proprietà, nessuna gestione degli indici. |
| Query | L'esecuzione di query usa l'indice per la chiave primaria ed esegue l'analisi negli altri casi. | Le query possono trarre vantaggio dall'indicizzazione automatica sulle proprietà, per query con durata ridotta. Il motore di database di Azure Cosmos DB può supportare aggregazioni, ricerca geospaziale e ordinamento. |
| Coerenza | Assoluta entro l'area primaria. Eventuale entro l'area secondaria. | [Cinque livelli di coerenza ben definiti](consistency-levels.md) per bilanciare disponibilità, latenza, velocità effettiva e coerenza in base alle esigenze dell'applicazione. |
| Prezzi | Ottimizzati per l'archiviazione. | Ottimizzati per la velocità effettiva. |
| Contratti di servizio | Disponibilità del 99.99%. | Disponibilità del 99,99% all'interno di una singola area, con possibilità di aggiungere altre aree per aumentare la disponibilità. [Contratti di servizio completi leader del settore](https://azure.microsoft.com/support/legal/sla/cosmos-db/) che regolano la disponibilità generale. |

## <a name="get-started"></a>Attività iniziali

Creare un account Azure Cosmos DB nel [portale di Azure](https://portal.azure.com). Vedere quindi [Avvio rapido per l'API di tabella con .NET](create-table-dotnet.md). 

## <a name="next-steps"></a>Passaggi successivi

Ecco alcuni riferimenti per iniziare:
* [Creare un'applicazione .NET tramite l'API di tabella](create-table-dotnet.md)
* [Sviluppare con l'API di tabella in .NET](tutorial-develop-table-dotnet.md)
* [Eseguire query sui dati della tabella tramite l'API di tabella](tutorial-query-table.md)
* [Informazioni su come configurare la distribuzione globale di Azure Cosmos DB tramite l'API di tabella](tutorial-global-distribution-table.md)
* [Azure Cosmos DB Table API SDK per .NET](table-sdk-dotnet.md)

