---
title: .NET SDK e Risorse dell'API Graph di Azure Cosmos DB | Documentazione Microsoft
description: Tutte le informazioni sull'API Graph di Azure Cosmos DB, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra tutte le versioni.
services: cosmos-db
documentationcenter: .net
author: luisbosquez
manager: jhubbard
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/17/2017
ms.author: lbosq
ms.openlocfilehash: 28f926d8d1f4c6006a348ba9c8289b885aef7641
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-cosmos-db-graph-net-api-download-and-release-notes"></a>API .NET Graph di Azure Cosmos DB : Download e note sulla versione

|   |   |
|---|---|
|**Download dell'SDK**|[NuGet](https://aka.ms/acdbgraphnuget)|
|**Documentazione sull'API**|[Documentazione di riferimento API .NET](https://aka.ms/acdbgraphapiref)|
|**Guida introduttiva**|[Azure Cosmos DB: creare una graph app tramite .NET e l'API Graph](create-graph-dotnet.md)|
|**Esercitazione**|[Azure CosmosDB: Create a container with the Graph API](tutorial-develop-graph-dotnet.md) (CosmosDB di Azure: creare un contenitore con l'API Graph)|
|**Framework attualmente supportato**| [Microsoft .NET Framework 4.6.1](https://www.microsoft.com/en-us/download/details.aspx?id=49981)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |


## <a name="release-notes"></a>Note sulla versione

### <a name="a-name031-preview031-preview"></a><a name="0.3.1-preview"/>0.3.1-preview

#### <a name="bug-fixes"></a>Correzioni di bug
* Correggere per caricare facoltativamente `appsettings.json` (`netstandard1.6`)

#### <a name="whats-new"></a>Novità
* Passare da Microsoft.Azure.Graphs alla piattaforma di destinazione AnyCPU.
* Rimuovere l'assembly di Mono dal manifesto del pacchetto `net461`.

### <a name="a-name030-preview030-preview"></a><a name="0.3.0-preview"/>0.3.0-preview

#### <a name="whats-new"></a>Novità
* Aggiunta del supporto per `.netstandard 1.6`
  * Richiede `Microsoft.Azure.DocumentDB.Core >= 1.5.1`
* Aggiunta di un nuovo parser `gremlin-groovy` in sostituzione di un parser esistente. Questo parser supporta un subset della sintassi Tinkerpop `gremlin-groovy` e include:
  * Prestazioni raddoppiate a livello di analisi
  * Sono stati risolti alcuni problemi relativi ai caratteri di escape nelle stringhe, all'errata gestione dei valori letterali e altre irregolarità nel vecchio parser.
* Aggiunta di ottimizzazioni per attraversamenti con predicati Edge.
  *  Gli hop trasversali con filtri dovrebbero rilevare questo miglioramento, ad esempio: `g.V('1').outE().has('name', 'marko').inV()`.
* Aggiunta di ottimizzazioni per attraversamenti con passaggio `limit()`.

#### <a name="breaking-changes"></a>Modifiche di rilievo
* Rimozione del supporto per .NET Framework 4.5.1

* Il nuovo parser è conforme alla sintassi `gremlin-groovy`. Pertanto, alcune espressioni precedentemente funzionanti ora risultano ambigue per il nuovo parser. Di seguito viene illustrato un caso rilevante:
  * `in` e `as` sono parole chiave riservate in `gremlin-groovy`. Per evitare errori di sintassi, questi passaggi devono pertanto essere qualificati mediante `.in()` o `.as()`. Ad esempio: `g.V().repeat(in()).times(2)` -> _genera un errore di sintassi_  
 `g.V().repeat(__.in()).times(2)` -> _ha esito positivo_

### <a name="a-name024-preview024-preview"></a><a name="0.2.4-preview"/>0.2.4-preview

### <a name="a-name022-preview022-preview"></a><a name="0.2.2-preview"/>0.2.2-preview

### <a name="a-name021-preview021-preview"></a><a name="0.2.1-preview"/>0.2.1-preview

### <a name="a-name020-preview020-preview"></a><a name="0.2.0-preview"/>0.2.0-preview

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview
* Versione di anteprima iniziale.

## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente. È quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima. 

Qualsiasi richiesta inviata a Cosmos DB di Azure con un SDK ritirato verrà rifiutata dal servizio.

<br/>

| Version | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [0.3.1-preview](#0.3.1-preview) |17 ottobre 2017 |--- |
| [0.3.0-preview](#0.3.0-preview) |2 ottobre 2017 |--- |
| [0.2.4-preview](#0.2.4-preview) |4 agosto 2017 |--- |
| [0.2.2-preview](#0.2.2-preview) |23 giugno 2017 |--- |
| [0.2.1-preview](#0.2.2-preview) |8 giugno 2017 |--- |
| [0.2.0-preview](#0.2.2-preview) |10 maggio 2017 |--- |
| [0.1.0-preview](#0.1.0-preview) |8 maggio 2017 |--- |

## <a name="see-also"></a>Vedere anche 
Per altre informazioni sull'API Graph di Azure Cosmos DB, vedere [Introduction to Azure Cosmos DB: Graph API](graph-introduction.md) (Introduzione ad Azure Cosmos DB: API Graph). 
