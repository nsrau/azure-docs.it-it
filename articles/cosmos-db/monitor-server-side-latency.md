---
title: Come monitorare la latenza lato server per le operazioni in Azure Cosmos DB
description: Informazioni su come monitorare la latenza del server per le operazioni nell'account Azure Cosmos DB o in un contenitore. I proprietari di un account Azure Cosmos DB possono comprendere i problemi di latenza lato server con gli account Azure Cosmos.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 0f1e6d07afb3b7b4d26081bc9e34ac257b280d0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113917"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Come monitorare la latenza lato server per le operazioni in un contenitore Azure Cosmos DB o in un account

Monitoraggio di Azure per Azure Cosmos DB fornisce una visualizzazione delle metriche per monitorare l'account e creare dashboard. Per impostazione predefinita, la metrica Azure Cosmos DB viene raccolta, per questa funzionalità non è necessario abilitare o configurare elementi in modo esplicito. La metrica di latenza lato server viene usata per visualizzare la latenza lato server di un'operazione. Azure Cosmos DB fornisce un contratto di manutenzione inferiore a 10 ms per operazioni di lettura/scrittura di punti con connettività diretta. Per le operazioni di lettura e scrittura di punti, i contratti di contratto vengono calcolati come descritto in dettaglio nel [documento del contratto di contratto](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)

Se viene visualizzata una latenza insolitamente elevata per le operazioni su punti, ad esempio:

* Operazione get o set con la chiave di partizione e l'ID in modalità diretta
* Operazione di lettura o scrittura o
* Una query

È possibile cercare il log di diagnostica per visualizzare le dimensioni dei dati restituiti. Se viene visualizzata una latenza elevata per le operazioni di query, è possibile cercare il log di diagnostica per le dimensioni dei dati restituiti, la [velocità effettiva o le UR/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) usati o il numero di tali operazioni in un determinato periodo. In questo modo, è possibile eseguire il debug dei problemi di latenza lato server.

## <a name="view-the-server-side-latency-metric"></a>Visualizzare la metrica di latenza lato server

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **monitoraggio** nella barra di spostamento a sinistra e selezionare **metrica**.

   ![Riquadro metriche in monitoraggio di Azure](./media/monitor-server-side-latency/monitor-metrics-blade.png)

1. Dal riquadro **metriche** > **selezionare una risorsa** > scegliere la **sottoscrizione**e il **gruppo di risorse**richiesti. Per il **tipo di risorsa**selezionare **account Azure Cosmos DB**, scegliere uno degli account Azure Cosmos esistenti e selezionare **applica**.
   
   ![Scegliere l'account Azure Cosmos DB per visualizzare le metriche](./media/monitor-server-side-latency/select-cosmos-db-account.png)

1. Selezionare quindi la metrica **latenza lato server** nell'elenco delle metriche disponibili. Per informazioni dettagliate su tutte le metriche disponibili in questo elenco, vedere l'articolo [metriche per categoria](monitor-cosmos-db-reference.md) . In questo esempio, selezionare **latenza lato server** e **AVG** come valore di aggregazione. Oltre a questi dettagli, è anche possibile selezionare l' **intervallo di tempo** e la **granularità temporale** delle metriche. Al massimo, è possibile visualizzare le metriche negli ultimi 30 giorni.  Dopo aver applicato il filtro, viene visualizzato un grafico in base al filtro. È possibile visualizzare la latenza lato server al minuto per il periodo selezionato.  

   ![Scegliere la metrica di latenza lato server dalla portale di Azure](./media/monitor-server-side-latency/server-side-latency-metric.png)

## <a name="filters-for-server-side-latency"></a>Filtri per la latenza lato server

È anche possibile filtrare le metriche e ottenere i grafici visualizzati da uno specifico **CollectionName**, **ConnectionMode**, **DatabaseName**, **OperationType**, **Region**e **PublicAPIType**. 

Per filtrare le metriche, selezionare **Aggiungi filtro** e scegliere la proprietà obbligatoria, ad esempio **PublicAPIType** , e selezionare il valore **SQL**. Aggiungere un altro filtro per **OperationType**. Il grafico Visualizza quindi la latenza lato server per diverse operazioni durante il periodo selezionato. Le operazioni eseguite tramite la stored procedure non vengono registrate in modo che non siano disponibili nella metrica OperationType.

Le metriche relative alla **latenza lato server** per ogni operazione vengono visualizzate come illustrato nell'immagine seguente:

![Filtri per le metriche di latenza lato server](./media/monitor-server-side-latency/server-side-latency-filters.png)

È anche possibile raggruppare le metriche usando l'opzione **applica suddivisione** .  

## <a name="next-steps"></a>Passaggi successivi

* Monitorare i dati Azure Cosmos DB usando [le impostazioni di diagnostica](cosmosdb-monitor-resource-logs.md) in Azure.
* [Controllare le operazioni del piano di controllo Azure Cosmos DB](audit-control-plane-logs.md)