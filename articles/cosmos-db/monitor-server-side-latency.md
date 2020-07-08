---
title: Come monitorare la latenza lato server per le operazioni in Azure Cosmos DB
description: Informazioni su come monitorare la latenza del server per le operazioni nell'account Azure Cosmos DB o in un contenitore. I proprietari di un account Azure Cosmos DB possono comprendere i problemi di latenza lato server con gli account Azure Cosmos.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 5be2365fb5850c3f45b320d66c114fb791b22c3e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262702"
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

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Riquadro Metriche in Monitoraggio di Azure":::

1. Nel riquadro **Metriche** > **Selezionare una risorsa** > scegliere la **sottoscrizione** e il **gruppo di risorse** richiesti. Per **Tipo di risorsa** selezionare **Account Azure Cosmos DB**, scegliere uno degli account Azure Cosmos esistenti e selezionare **Applica**.
   
   :::image type="content" source="./media/monitor-server-side-latency/select-cosmos-db-account.png" alt-text="Scegliere l'account Azure Cosmos DB per visualizzare le metriche":::

1. Selezionare quindi la metrica **latenza lato server** nell'elenco delle metriche disponibili. Per informazioni dettagliate su tutte le metriche disponibili in questo elenco, vedere l'articolo [Metriche per categoria](monitor-cosmos-db-reference.md). In questo esempio, selezionare **latenza lato server** e **AVG** come valore di aggregazione. Oltre a questi dettagli, è anche possibile selezionare l'**Intervallo di tempo** e la **Granularità temporale** delle metriche. Al massimo, è possibile visualizzare le metriche degli ultimi 30 giorni.  Dopo aver applicato il filtro, viene visualizzato un grafico in base al filtro. È possibile visualizzare la latenza lato server al minuto per il periodo selezionato.  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="Scegliere la metrica di latenza lato server dalla portale di Azure":::

## <a name="filters-for-server-side-latency"></a>Filtri per la latenza lato server

È anche possibile filtrare le metriche e ottenere i grafici visualizzati da uno specifico **CollectionName**, **ConnectionMode**, **DatabaseName**, **OperationType**, **Region**e **PublicAPIType**. 

Per filtrare le metriche, selezionare **Aggiungi filtro** e scegliere la proprietà obbligatoria, ad esempio **PublicAPIType** , e selezionare il valore **SQL**. Aggiungere un altro filtro per **OperationType**. Il grafico Visualizza quindi la latenza lato server per diverse operazioni durante il periodo selezionato. Le operazioni eseguite tramite stored procedure non vengono registrate e quindi non sono disponibili nella metrica OperationType.

Le metriche relative alla **latenza lato server** per ogni operazione vengono visualizzate come illustrato nell'immagine seguente:

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="Filtri per le metriche di latenza lato server":::

È anche possibile raggruppare le metriche usando l'opzione **applica suddivisione** .  

## <a name="next-steps"></a>Passaggi successivi

* Monitorare i dati Azure Cosmos DB usando [le impostazioni di diagnostica](cosmosdb-monitor-resource-logs.md) in Azure.
* [Controllare le operazioni del piano di controllo Azure Cosmos DB](audit-control-plane-logs.md)
