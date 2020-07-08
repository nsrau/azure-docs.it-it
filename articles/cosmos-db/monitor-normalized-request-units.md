---
title: Monitorare le UR/sec normalizzate per un contenitore o un account di Azure Cosmos
description: Informazioni su come monitorare l'utilizzo normalizzato delle unità richiesta di un'operazione in Azure Cosmos DB. I proprietari di un account Azure Cosmos DB possono comprendere quali operazioni utilizzano più unità richiesta.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 06/25/2020
ms.openlocfilehash: 8709389208ba1320685b1834b20893f08ef33ed7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482905"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Come monitorare le UR/sec normalizzate per un contenitore o un account di Azure Cosmos

Monitoraggio di Azure per Azure Cosmos DB fornisce una visualizzazione delle metriche per monitorare l'account e creare dashboard. Per impostazione predefinita, la metrica Azure Cosmos DB viene raccolta, per questa funzionalità non è necessario abilitare o configurare elementi in modo esplicito.

La metrica di utilizzo delle unità richiesta **normalizzata** viene usata per verificare il livello di saturazione delle repliche rispetto al consumo delle unità richiesta negli intervalli di chiavi di partizione. Azure Cosmos DB distribuisce equamente la velocità effettiva tra tutte le partizioni fisiche. Questa metrica offre una visualizzazione al secondo dell'utilizzo massimo della velocità effettiva in un set di repliche. Usare questa metrica per calcolare l'utilizzo di Ur/s tra le partizioni per il contenitore specificato. Utilizzando questa metrica, se viene visualizzata una percentuale elevata di utilizzo delle unità richiesta, è necessario aumentare la velocità effettiva per soddisfare le esigenze del carico di lavoro.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>Cosa aspettarsi e quando le UR/sec normalizzate sono più elevate

Quando il consumo di Ur/s normalizzato raggiunge il 100%, il client riceve errori di limitazione della frequenza. Il client deve rispettare il tempo di attesa e riprovare. Se è presente un picco breve che raggiunge l'utilizzo del 100%, significa che la velocità effettiva della replica ha raggiunto il limite massimo delle prestazioni. Ad esempio, una singola operazione, ad esempio un stored procedure che utilizza tutte le UR/sec in una replica, provocherà un breve picco nell'utilizzo di Ur/s normalizzato. In questi casi, se la frequenza delle richieste è bassa, non saranno presenti errori di limitazione della frequenza immediata. Ciò è dovuto al fatto che Azure Cosmos DB consente alle richieste di addebitare un importo superiore alle UR/sec di cui è stato effettuato il provisioning per la richiesta specifica e altre richieste entro tale periodo di tempo sono limitate.

Le metriche di monitoraggio di Azure consentono di trovare le operazioni per ogni codice di stato usando la metrica **Total requests** . Successivamente, è possibile filtrare in base a queste richieste con il codice di stato 429 e suddividerle in base al **tipo di operazione**.

Per trovare le richieste con frequenza limitata, è consigliabile ottenere queste informazioni tramite i log di diagnostica.

Se è presente un picco continuo del 100% di utilizzo di Ur/s normalizzato o vicino al 100%, è consigliabile aumentare la velocità effettiva. È possibile scoprire quali operazioni sono pesanti e i loro picchi di utilizzo usando le metriche di monitoraggio di Azure e i log di monitoraggio di Azure.

La metrica di **consumo ur normalizzata** viene usata anche per vedere quale intervallo di chiavi di partizione è più caldo in termini di utilizzo; in questo modo si fornisce l'inclinazione della velocità effettiva verso un intervallo di chiavi di partizione. In un secondo momento, è possibile visualizzare il log di **PartitionKeyRUConsumption** nei log di monitoraggio di Azure per ottenere informazioni sulle chiavi di partizione logiche che sono a caldo in termini di utilizzo.

## <a name="view-the-normalized-request-unit-consumption-metric"></a>Visualizzare la metrica di consumo unità richiesta normalizzata

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Nel menu di spostamento a sinistra selezionare **Monitoraggio** e quindi selezionare **Metriche**.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Riquadro Metriche in Monitoraggio di Azure":::

3. Nel riquadro **Metriche** > **Selezionare una risorsa** > scegliere la **sottoscrizione** e il **gruppo di risorse** richiesti. Per **Tipo di risorsa** selezionare **Account Azure Cosmos DB**, scegliere uno degli account Azure Cosmos esistenti e selezionare **Applica**.

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="Scegliere un account Azure Cosmos per visualizzare le metriche":::

4. È quindi possibile selezionare una metrica dall'elenco delle metriche disponibili. È possibile selezionare metriche specifiche per unità richiesta, archiviazione, latenza, disponibilità, Cassandra e altre. Per informazioni dettagliate su tutte le metriche disponibili in questo elenco, vedere l'articolo [Metriche per categoria](monitor-cosmos-db-reference.md). In questo esempio, selezionare metrica **consumo ur normalizzato** e **Max** come valore di aggregazione.

   Oltre a questi dettagli, è anche possibile selezionare l'**Intervallo di tempo** e la **Granularità temporale** delle metriche. Al massimo, è possibile visualizzare le metriche degli ultimi 30 giorni.  Dopo aver applicato il filtro, viene visualizzato un grafico in base al filtro.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Scegliere una metrica dal portale di Azure":::

### <a name="filters-for-normalized-request-unit-consumption"></a>Filtri per il consumo di unità richiesta normalizzate

È anche possibile filtrare le metriche e il grafico visualizzato da uno specifico **CollectionName**, **DatabaseName**, **PartitionKeyRangeID**e **Region**. Per filtrare le metriche, selezionare **Aggiungi filtro** e scegliere la proprietà obbligatoria, ad esempio **CollectionName** e il valore corrispondente a cui si è interessati. Il grafico Visualizza quindi le unità di consumo ur normalizzate utilizzate per il contenitore per il periodo selezionato.  

È possibile raggruppare le metriche usando l'opzione **Applicare separazione**.  

La metrica di utilizzo delle unità richiesta normalizzata per ogni contenitore viene visualizzata come illustrato nell'immagine seguente:

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="Applicare i filtri alla metrica di utilizzo delle unità richiesta normalizzate":::

## <a name="next-steps"></a>Passaggi successivi

* Monitorare i dati Azure Cosmos DB usando [le impostazioni di diagnostica](cosmosdb-monitor-resource-logs.md) in Azure.
* [Controllare le operazioni del piano di controllo Azure Cosmos DB](audit-control-plane-logs.md)
