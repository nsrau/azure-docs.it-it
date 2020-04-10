---
title: Come monitorare la latenza lato server per le operazioni in Azure Cosmos DBHow to monitor the server-side latency for operations in Azure Cosmos DB
description: Informazioni su come monitorare la latenza del server per le operazioni nell'account di Azure Cosmos DB o in un contenitore. I proprietari di un account di database Cosmos di Azure possono comprendere i problemi di latenza sul lato server con gli account Cosmos di Azure.Owners of an Azure Cosmos DB account can understand the server-side latency issues with your Azure Cosmos accounts.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 62c10a2ada9ff7d3bf7090028dd9684192517d02
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991406"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Come monitorare la latenza lato server per le operazioni in un account o un contenitore di database Cosmos di AzureHow to monitor the server-side latency for operations in an Azure Cosmos DB container or account

Monitoraggio di Azure per Azure Cosmos DB offre una visualizzazione delle metriche per monitorare l'account e creare dashboard. Le metriche di Azure Cosmos DB vengono raccolte per impostazione predefinita, questa funzionalità non richiede di abilitare o configurare nulla in modo esplicito. La metrica di latenza lato server viene utilizzata per visualizzare la latenza lato server di un'operazione. Azure Cosmos DB offre un sla di meno di 10 ms per le operazioni di lettura/scrittura dei punti con connettività diretta. Per le operazioni di lettura e scrittura dei punti, i contratti di servizio vengono calcolati come descritto nel [documento del servizio SLA.](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)

Se viene visualizzata una latenza insolitamente grande per operazioni puntuali, ad esempio:If you see unusual large latency for point operations such as:

* Un'operazione get o set con chiave di partizione e ID in modalità diretta
* Un'operazione di lettura o scrittura o
* Una query

È possibile cercare il log di diagnostica per visualizzare le dimensioni dei dati restituiti. Se viene visualizzata una latenza elevata sostenuta per le operazioni di query, è possibile cercare nel log di diagnostica le dimensioni dei dati restituiti, la [velocità effettiva o](cosmosdb-monitor-resource-logs.md#diagnostic-queries) le RU utilizzate o il numero di tali operazioni in un determinato periodo. In questo modo, è possibile eseguire il debug dei problemi di latenza lato server.

## <a name="view-server-side-latency-metric"></a>Visualizzare la metrica di latenza lato serverView server-side latency metric

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **Monitor** nella barra di navigazione a sinistra e selezionare **Metriche**.

   ![Riquadro Metriche in Monitoraggio di Azure](./media/monitor-server-side-latency/monitor-metrics-blade.png)

1. Nel riquadro **Metriche** > **Selezionare una risorsa** > scegliere la **sottoscrizione**richiesta e il gruppo **di risorse**. Per **Tipo di risorsa**selezionare **Account di Azure Cosmos DB**, scegliere uno degli account Cosmos di Azure esistenti e selezionare **Applica**.
   
   ![Scegliere un account Cosmos DB per visualizzare le metriche](./media/monitor-server-side-latency/select-cosmos-db-account.png)

1. Selezionare quindi la **metrica Latenza lato server** dall'elenco delle metriche disponibili. Per informazioni dettagliate su tutte le metriche disponibili in questo elenco, vedere l'articolo [Metriche per categoria.](monitor-cosmos-db-reference.md) In questo esempio, selezioniamo **Latenza lato server** e **Avg** come valore di aggregazione. Oltre a questi dettagli, puoi anche selezionare **l'intervallo** di tempo e la **granularità temporale** delle metriche. Al massimo, puoi visualizzare le metriche per gli ultimi 30 giorni.  Dopo aver applicato il filtro, viene visualizzato un grafico basato sul filtro. È possibile visualizzare il numero medio di unità richiesta utilizzate al minuto per il periodo selezionato.  

   ![Scegliere la metrica Latenza lato server dal portale di AzureChoose the Server-Side Latency metric from the Azure portal](./media/monitor-server-side-latency/server-side-latency-metric.png)

## <a name="filters-for-server-side-latency"></a>Filtri per latenza lato server

È inoltre possibile filtrare le metriche e il grafico visualizzato da un **oggetto CollectionName**, **ConnectionMode**, **DatabaseName**, **OperationType**, **Region**e **PublicAPIType**. 

Per filtrare le metriche, selezionare **Aggiungi filtro** e scegliere la proprietà richiesta, ad esempio **PublicAPIType,** e selezionare il valore **sql**. Aggiungere un altro filtro per **OperationType**. Il grafico visualizza quindi la latenza lato server per le diverse operazioni durante il periodo selezionato. Le operazioni eseguite tramite Stored procedure non vengono registrate in modo che non siano disponibili nella metrica OperationType.

Le metriche **Latenza lato server** per ogni operazione vengono visualizzate come illustrato nell'immagine seguente:The Server Side Latency metrics for each operation are displayed as shown in the following image:

![Filtri per le metriche di latenza lato serverFilters for server-side latency metrics](./media/monitor-server-side-latency/server-side-latency-filters.png)

È inoltre possibile raggruppare le metriche utilizzando l'opzione **Applica divisione.**  

## <a name="next-steps"></a>Passaggi successivi

* Monitorare i dati del database Cosmos di Azure usando le impostazioni di diagnostica in AzureMonitor Azure Cosmos DB data by using [diagnostic settings](cosmosdb-monitor-resource-logs.md) in Azure
* [Controllare le operazioni del piano di controllo di Azure Cosmos DBAudit Azure Cosmos DB control plane operations](audit-control-plane-logs.md)