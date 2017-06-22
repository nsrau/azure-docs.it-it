---
title: Introduzione all&quot;API di tabella di Azure Cosmos DB | Microsoft Docs
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
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: d1e1f977c9023f4727d8c444b5e490e8f5ba8d9e
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introduzione ad Azure Cosmos DB: API di tabella

[Azure Cosmos DB](introduction.md) è il servizio di database multimodello distribuito a livello globale di Microsoft per applicazioni cruciali. Azure Cosmos DB offre [distribuzione globale predefinita](distribute-data-globally.md), [scalabilità elastica in termini di archiviazione e velocità effettiva](partition-data.md) ovunque nel mondo, latenze pari a singole unità di millisecondi al 99° percentile, [cinque livelli di coerenza ben definiti](consistency-levels.md) e disponibilità elevata garantita, il tutto supportato da [contratti di servizio leader del settore](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indicizza automaticamente i dati](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) senza che sia necessario gestire manualmente indici e schemi. Si tratta di un database multimodello che supporta modelli di dati di documenti, coppie chiave-valore, grafi e colonne. 

![API di archiviazione tabelle di Azure e Azure Cosmos DB](./media/table-introduction/premium-tables.png) 

Azure Cosmos DB offre l'API di tabella per le applicazioni che necessitano di un archivio di coppie chiave-valore con uno schema flessibile. È possibile usare gli SDK e le API REST dell'[archiviazione tabelle di Azure](../storage/storage-introduction.md) insieme ad Azure Cosmos DB. È possibile usare Azure Cosmos DB per creare tabelle con requisiti di velocità effettiva elevata. Azure Cosmos DB supporta le tabelle ottimizzate per la velocità effettiva, chiamate in modo informale "tabelle Premium", attualmente disponibili in anteprima pubblica. 

È possibile continuare a usare l'archiviazione tabelle di Azure per le tabelle con requisiti di archiviazione elevata e velocità effettiva inferiore. Azure Cosmos DB introdurrà il supporto per le tabelle ottimizzate per l'archiviazione in uno dei prossimi aggiornamenti e gli account di archiviazione tabelle di Azure nuovi ed esistenti verranno aggiornati ad Azure Cosmos DB.

## <a name="premium-and-standard-table-apis"></a>API di tabella Premium e Standard
Se attualmente si usa l'archiviazione tabelle di Azure, è possibile ottenere i vantaggi seguenti passando alla "tabella Premium" in anteprima di Azure Cosmos DB:

|  | Archiviazione tabelle di Azure | Azure Cosmos DB: archiviazione tabelle (anteprima) |
| --- | --- | --- |
| Latency | Veloce, senza limiti superiori per la latenza | Latenza pari a singole unità di millisecondi per letture e scritture, supportata da latenza minore di 10 ms per le letture e di 15 ms per le scritture al 99° percentile, in base a qualsiasi livello di scalabilità e ovunque nel mondo |
| Velocità effettiva | Altamente scalabile, ma senza modello di velocità effettiva dedicato. Le tabelle hanno un limite di scalabilità di 20.000 operazioni al secondo | Altamente scalabile con [velocità effettiva riservata dedicata per tabella](request-units.md), supportata da contratti di servizio. Non esiste un limite superiore di velocità effettiva per gli account, che supportano oltre 10 milioni di operazioni al secondo per tabella |
| Distribuzione globale | Singola area con un'area di lettura secondaria leggibile facoltativa per la disponibilità elevata. Non è possibile avviare il failover | [Distribuzione globale predefinita](distribute-data-globally.md) da una delle oltre 30 aree e supporto per [failover automatici e manuali](regional-failover.md) in qualsiasi momento, ovunque nel mondo |
| Indicizzazione | Solo indice primario in PartitionKey e RowKey. Nessun indice secondario | Indicizzazione automatica e completa su tutte le proprietà, nessuna gestione degli indici |
| Query | L'esecuzione di query usa l'indice per la chiave primaria ed esegue l'analisi negli altri casi. | Le query possono trarre vantaggio dall'indicizzazione automatica sulle proprietà, per query con durata ridotta. Il motore di database di Azure Cosmos DB è in grado di supportare aggregazioni, ricerca geospaziale e ordinamento. |
| Coerenza | Elevata nell'area primaria, possibile con un'area secondaria | [Cinque livelli di coerenza ben definiti](consistency-levels.md) per bilanciare disponibilità, latenza, velocità effettiva e coerenza in base alle esigenze dell'applicazione |
| Prezzi | Ottimizzati per l'archiviazione  | Ottimizzati per la velocità effettiva |
| Contratti di servizio | Disponibilità del 99,9% | Disponibilità del 99,99% all'interno di una singola area, con possibilità di aggiungere altre aree per aumentare la disponibilità. [Contratti di servizio completi leader del settore](https://azure.microsoft.com/support/legal/sla/cosmos-db/) che regolano la disponibilità generale |

## <a name="how-to-get-started"></a>Attività iniziali

Creare un account Azure Cosmos DB nel [portale di Azure](https://portal.azure.com) e iniziare da [Quickstart for Table API using .NET](create-table-dotnet.md) (Avvio rapido per l'API di tabella con .NET). 

## <a name="next-steps"></a>Passaggi successivi

Ecco alcuni riferimenti per iniziare:
* Iniziare con l'[API di tabella di Azure Cosmos DB](create-table-dotnet.md) usando lo strumento .NET Table SDK esistente.
* Informazioni sulla [distribuzione globale con Azure Cosmos DB](distribute-data-globally.md).
* Informazioni sulla [velocità effettiva di provisioning in Azure Cosmos DB](request-units.md).
