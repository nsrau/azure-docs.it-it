---
title: Monitorare l'utilizzo della velocità effettiva di un'operazione in Azure Cosmos DB
description: Informazioni su come monitorare la velocità effettiva o l'utilizzo delle unità richiesta di un'operazione in Azure Cosmos DB. I proprietari di un account Azure Cosmos DB possono comprendere quali operazioni stanno prendendo altre unità richiesta.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: e3ec3bc0e0c6028eb5d7a90ee1920938d792941b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85260747"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Come monitorare la velocità effettiva o l'utilizzo delle unità richiesta di un'operazione in Azure Cosmos DB

Monitoraggio di Azure per Azure Cosmos DB fornisce una visualizzazione delle metriche per monitorare l'account e creare dashboard. Per impostazione predefinita, la metrica Azure Cosmos DB viene raccolta, per questa funzionalità non è necessario abilitare o configurare elementi in modo esplicito. La metrica **totale unità richiesta** viene utilizzata per ottenere l'utilizzo delle unità richiesta per diversi tipi di operazioni. In un secondo momento è possibile analizzare le operazioni che hanno utilizzato la maggior parte della velocità effettiva. Per impostazione predefinita, i dati relativi alla velocità effettiva sono aggregati a intervalli di un minuto. Tuttavia, è possibile modificare l'unità di aggregazione modificando l'opzione di granularità dell'ora.

Esistono due modi per analizzare i dati di utilizzo delle unità richiesta:

* Nell'intervallo di tempo specificato, le operazioni che richiedono più unità richiesta.
* Quali operazioni in generale dominano il carico di lavoro utilizzando più unità richiesta.
Questa analisi consente di concentrarsi sulle operazioni, ad esempio INSERT, Upsert ed esaminare l'indicizzazione. È possibile verificare se sono presenti più o meno indicizzazione di campi specifici e modificare i [criteri di indicizzazione](index-policy.md#include-exclude-paths) per includere o escludere i percorsi.

Se si nota che alcune query stanno prendendo altre unità richiesta, è possibile eseguire azioni come le seguenti:

* Riconsiderare se si richiede la quantità corretta di dati.
* Modificare la query per utilizzare l'indice con la clausola di filtro.
* Eseguire chiamate di funzione UDF meno dispendiose.
* Definire le chiavi di partizione per ridurre al minimo la ventola della query in partizioni diverse.
* È anche possibile usare le metriche di query restituite nella risposta alla chiamata, i dettagli del log di diagnostica e fare riferimento all'articolo [ottimizzazione delle prestazioni delle query](sql-api-query-metrics.md) per altre informazioni sull'esecuzione delle query.
* È possibile iniziare da Sum e quindi esaminare l'utilizzo di AVG utilizzando la dimensione corretta.

## <a name="view-the-total-request-unit-usage-metric"></a>Visualizza la metrica di utilizzo totale delle unità richiesta

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **monitoraggio** nella barra di spostamento a sinistra e selezionare **metrica**.

   :::image type="content" source="./media/monitor-request-unit-usage/monitor-metrics-blade.png" alt-text="Riquadro Metriche in Monitoraggio di Azure":::

1. Nel riquadro **Metriche** > **Selezionare una risorsa** > scegliere la **sottoscrizione** e il **gruppo di risorse** richiesti. Per **Tipo di risorsa** selezionare **Account Azure Cosmos DB**, scegliere uno degli account Azure Cosmos esistenti e selezionare **Applica**.

   :::image type="content" source="./media/monitor-request-unit-usage/select-cosmos-db-account.png" alt-text="Riquadro Metriche in Monitoraggio di Azure":::

1. Selezionare quindi la metrica **totale unità richiesta** nell'elenco delle metriche disponibili. Per informazioni dettagliate su tutte le metriche disponibili in questo elenco, vedere l'articolo [Metriche per categoria](monitor-cosmos-db-reference.md). In questo esempio, selezionare **Total request Units** e **AVG** come valore di aggregazione. Oltre a questi dettagli, è anche possibile selezionare l'**Intervallo di tempo** e la **Granularità temporale** delle metriche. Al massimo, è possibile visualizzare le metriche degli ultimi 30 giorni.  Dopo aver applicato il filtro, viene visualizzato un grafico in base al filtro. È possibile visualizzare il numero medio di unità richiesta utilizzate al minuto per il periodo selezionato.  

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-metric.png" alt-text="Riquadro Metriche in Monitoraggio di Azure":::

## <a name="filters-for-request-unit-usage"></a>Filtri per l'utilizzo delle unità richiesta

È anche possibile filtrare le metriche e ottenere i grafici visualizzati da uno specifico **CollectionName**, **DatabaseName**, **OperationType**, **Region**, **status**e **statusCode**. Le opzioni **Aggiungi filtro** e **applica suddivisione** consentono di filtrare l'utilizzo delle unità richiesta e di raggruppare le metriche.

Per ottenere l'utilizzo delle unità richiesta di ogni operazione in base al totale (somma) o alla media, selezionare **applica suddivisione** e scegli **tipo di operazione** e il valore filtro come illustrato nell'immagine seguente:

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-operations.png" alt-text="Riquadro Metriche in Monitoraggio di Azure":::

Se si desidera visualizzare l'utilizzo delle unità richiesta per raccolta, selezionare **applica suddivisione** e scegliere il nome della raccolta come filtro. Viene visualizzata una chat simile alla seguente con una scelta di raccolte all'interno del dashboard. È quindi possibile selezionare un nome di raccolta specifico per visualizzare altri dettagli:

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-collection.png" alt-text="Riquadro Metriche in Monitoraggio di Azure":::

## <a name="next-steps"></a>Passaggi successivi

* Monitorare i dati Azure Cosmos DB usando [le impostazioni di diagnostica](cosmosdb-monitor-resource-logs.md) in Azure.
* [Controllare le operazioni del piano di controllo Azure Cosmos DB](audit-control-plane-logs.md)
