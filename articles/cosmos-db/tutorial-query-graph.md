---
title: Procedura per l'esecuzione di query sui dati grafo in SQL in Azure Cosmos DB | Microsoft Docs
description: Informazioni sull'esecuzione di query sui dati grafo in Azure Cosmos DB
services: cosmos-db
author: luisbosquez
manager: kfile
editor: ''
tags: ''
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: ad38976f439c399d839d6c5ee9dcd6ade7726c71
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081908"
---
# <a name="tutorial-query-azure-cosmos-db-graph-api-by-using-gremlin"></a>Esercitazione: Eseguire query nell'API Graph di Azure Cosmos DB con Gremlin

L'[API Graph](graph-introduction.md) di Azure Cosmos DB supporta le query [Gremlin](https://github.com/tinkerpop/gremlin/wiki). Questo articolo include esempi di documenti e query per iniziare. L'articolo relativo al [supporto Gremlin](gremlin-support.md) include riferimenti Gremlin dettagliati.

Questo articolo illustra le attività seguenti: 

> [!div class="checklist"]
> * Esecuzione di query sui dati con Gremlin

## <a name="prerequisites"></a>Prerequisiti

Per il funzionamento di queste query è necessario disporre di un account Azure Cosmos DB e nel contenitore devono essere presenti dati grafo. Questi requisiti non sono disponibili? Completare la [Guida introduttiva di 5 minuti](create-graph-dotnet.md) o l'[esercitazione per sviluppatori](tutorial-query-graph.md) per creare un account e popolare il database. È possibile eseguire le query seguenti usando la [console Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) o il driver Gremlin preferito.

## <a name="count-vertices-in-the-graph"></a>Numero di vertici nel grafo

Il frammento seguente illustra come contare il numero di vertici nel grafo:

```
g.V().count()
```

## <a name="filters"></a>Filtri

È possibile applicare filtri usando i comandi `has` e `hasLabel` di Gremlin e combinarli usando `and`, `or` e `not` per creare filtri più complessi. Per velocizzare le query, Azure DB Cosmos consente l'indicizzazione senza schema di tutte le proprietà all'interno di vertici e gradi.

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Proiezione

È possibile proiettare determinate proprietà nei risultati della query usando il comando `values`:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Trovare vertici e archi correlati

Finora sono stati esaminati solo gli operatori di query che è possibile usare in qualsiasi database. I grafi sono veloci ed efficienti per le operazioni di attraversamento quando è necessario passare agli archi e ai vertici correlati. Verranno ora individuati tutti gli amici di Thomas. Questa operazione viene eseguita usando il comando `outE` di Gremlin per individuare tutti gli archi in uscita da Thomas, quindi attraversando i vertici in ingresso da tali archi usando il comando `inV` di Gremlin:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

La query successiva esegue due passaggi per trovare tutti "gli amici di amici" di Thomas chiamando `outE` e `inV` due volte. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

È possibile creare query più complesse e implementare la potente logica di attraversamento di grafi usando Gremlin, incluse la combinazione di espressioni di filtro, l'esecuzione di cicli con il comando `loop` e l'implementazione dello spostamento condizionale usando il comando `choose`. Altre informazioni sulle operazioni che è possibile eseguire con il [supporto per Gremlin](gremlin-support.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * È stato appreso come eseguire una query usando l'API Graph 

È ora possibile passare alla sezione Concetti per altre informazioni su Cosmos DB.

> [!div class="nextstepaction"]
> [Distribuzione globale](distribute-data-globally.md) 

