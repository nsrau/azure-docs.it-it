---
title: Monitorare l'utilizzo della velocità effettiva di un'operazione in Azure Cosmos DBMonitor the throughput usage of an operation in Azure Cosmos DB
description: Informazioni su come monitorare la velocità effettiva o richiedere l'utilizzo dell'unità di un'operazione in Azure Cosmos DB. I proprietari di un account di database Cosmos di Azure possono comprendere quali operazioni stanno assumendo più unità di richiesta.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: bc39ef199a5d40d3eaa75023277d3e00b93e131a
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115430"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Come monitorare la velocità effettiva o richiedere l'utilizzo di un'unità di misura di un'operazione in Azure Cosmos DBHow to monitor throughput or request unit usage of an operation in Azure Cosmos DB

Monitoraggio di Azure per Azure Cosmos DB offre una visualizzazione delle metriche per monitorare l'account e creare dashboard. Le metriche di Azure Cosmos DB vengono raccolte per impostazione predefinita, questa funzionalità non richiede di abilitare o configurare nulla in modo esplicito. La metrica **Unità richiesta totali** viene utilizzata per ottenere l'utilizzo delle unità richiesta per diversi tipi di operazioni. Successivamente è possibile analizzare quali operazioni hanno utilizzato la maggior parte della velocità effettiva. Per impostazione predefinita, i dati di velocità effettiva vengono aggregati a intervalli di un minuto. Tuttavia, è possibile modificare l'unità di aggregazione modificando l'opzione di granularità del tempo.

Esistono due modi per analizzare i dati di utilizzo dell'unità richiesta:There are two ways to analyze the request unit usage data:

* Entro l'intervallo di tempo specificato quali operazioni stanno prendendo più unità di richiesta.
* Operazioni in generale dominano il carico di lavoro utilizzando più unità di richiesta.
Questa analisi consente di concentrarsi su operazioni quali inserimento, upsert e osservare la loro indicizzazione. È possibile scoprire se si sta sopra/sotto l'indicizzazione di campi specifici e modificare i [criteri di indicizzazione](index-policy.md#include-exclude-paths) per includere o escludere i percorsi.

Se si nota che alcune query stanno prendendo più unità di richiesta, è possibile eseguire azioni come:

* Riconsiderare se si richiede la giusta quantità di dati.
* Modificare la query in modo da utilizzare index con clausola di filtro.
* Eseguire chiamate di funzione UDF meno costose.
* Definire le chiavi di partizione per ridurre al minimo la ventola fuori query in partizioni diverse.
* È anche possibile usare le metriche di query restituite nella risposta alla chiamata, i dettagli del log di diagnostica e fare riferimento all'articolo [sull'ottimizzazione delle prestazioni](sql-api-query-metrics.md) delle query per altre informazioni sull'esecuzione della query.
* È possibile iniziare dalla somma e quindi esaminare l'utilizzo avg utilizzando la dimensione corretta.

## <a name="view-the-total-request-unit-usage-metric"></a>Visualizzare la metrica di utilizzo totale dell'unità richiesta

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **Monitor** nella barra di navigazione a sinistra e selezionare **Metriche**.

   ![Riquadro Metriche in Monitoraggio di Azure](./media/monitor-request-unit-usage/monitor-metrics-blade.png)

1. Nel riquadro **Metriche** > **Selezionare una risorsa** > scegliere la **sottoscrizione**richiesta e il gruppo **di risorse**. Per **Tipo di risorsa**selezionare **Account di Azure Cosmos DB**, scegliere uno degli account Cosmos di Azure esistenti e selezionare **Applica**.

   ![Scegliere l'account azure Cosmos DB per visualizzare le metricheChoose the Azure Cosmos DB account to view metrics](./media/monitor-request-unit-usage/select-cosmos-db-account.png)

1. Selezionare quindi la metrica **Unità richiesta totali** dall'elenco delle metriche disponibili. Per informazioni dettagliate su tutte le metriche disponibili in questo elenco, vedere l'articolo [Metriche per categoria.](monitor-cosmos-db-reference.md) In questo esempio, selezioniamo **Total Request Units** e **Avg** come valore di aggregazione. Oltre a questi dettagli, puoi anche selezionare **l'intervallo** di tempo e la **granularità temporale** delle metriche. Al massimo, puoi visualizzare le metriche per gli ultimi 30 giorni.  Dopo aver applicato il filtro, viene visualizzato un grafico basato sul filtro. È possibile visualizzare il numero medio di unità richiesta utilizzate al minuto per il periodo selezionato.  

   ![Scegliere una metrica dal portale di AzureChoose a metric from the Azure portal](./media/monitor-request-unit-usage/request-unit-usage-metric.png)

## <a name="filters-for-request-unit-usage"></a>Filtri per l'utilizzo delle unità di richiestaFilters for request unit usage

È inoltre possibile filtrare le metriche e ottenere i grafici visualizzati da un **oggetto CollectionName**, **DatabaseName**, **OperationType**, **Region**, **Status**e **StatusCode**. Le opzioni **Aggiungi filtro** e **Applica divisione** consentono di filtrare l'utilizzo dell'unità richiesta e raggruppare le metriche.

Per ottenere l'utilizzo dell'unità richiesta di ogni operazione per totale (somma) o media, selezionare **Applica divisione** e scegliere **Tipo di** operazione e il valore del filtro, come illustrato nell'immagine seguente:

   ![Cosmos DB Unità di richiesta per le operazioni in Monitoraggio di Azure](./media/monitor-request-unit-usage/request-unit-usage-operations.png)

Se si desidera visualizzare l'utilizzo dell'unità richiesta per raccolta, selezionare **Applica divisione** e scegliere il nome della raccolta come filtro. Vedrai una chat simile alla seguente con una scelta di raccolte all'interno della dashboard. È quindi possibile selezionare un nome di raccolta specifico per visualizzare ulteriori dettagli:You can then select a specific collection name to view more details:

   ![Cosmos DB Unità richieste per tutte le operazioni dalla raccolta in Monitoraggio di Azure](./media/monitor-request-unit-usage/request-unit-usage-collection.png)

## <a name="next-steps"></a>Passaggi successivi

* Monitorare i dati del database Cosmos di Azure usando le impostazioni di diagnostica in Azure.Monitor Azure Cosmos DB data by using [diagnostic settings](cosmosdb-monitor-resource-logs.md) in Azure.
* [Controllare le operazioni del piano di controllo di Azure Cosmos DBAudit Azure Cosmos DB control plane operations](audit-control-plane-logs.md)