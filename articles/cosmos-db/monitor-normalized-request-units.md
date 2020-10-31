---
title: Monitorare le UR/sec normalizzate per un contenitore o un account di Azure Cosmos
description: Informazioni su come monitorare l'utilizzo normalizzato delle unità richiesta di un'operazione in Azure Cosmos DB. I proprietari di un account Azure Cosmos DB possono comprendere quali operazioni utilizzano più unità richiesta.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 06/25/2020
ms.openlocfilehash: dc47f2f7a0f1586b197d14015fe2167293c806c6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099335"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Come monitorare le UR/sec normalizzate per un contenitore o un account di Azure Cosmos
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Monitoraggio di Azure per Azure Cosmos DB fornisce una visualizzazione delle metriche per monitorare l'account e creare dashboard. Per impostazione predefinita, la metrica Azure Cosmos DB viene raccolta, per questa funzionalità non è necessario abilitare o configurare elementi in modo esplicito.

La metrica di **utilizzo delle UR normalizzata** viene usata per vedere quanto sono saturi gli intervalli di chiavi di partizione rispetto al traffico. Azure Cosmos DB distribuisce equamente la velocità effettiva in tutti gli intervalli di chiavi di partizione. Questa metrica offre una visualizzazione al secondo dell'utilizzo massimo della velocità effettiva per l'intervallo di chiavi di partizione. Usare questa metrica per calcolare l'utilizzo di Ur/s nell'intervallo di chiavi di partizione per il contenitore specificato. Utilizzando questa metrica, se viene visualizzata una percentuale elevata di utilizzo delle unità richiesta in tutti gli intervalli di chiavi di partizione in monitoraggio di Azure, è necessario aumentare la velocità effettiva per soddisfare le esigenze del carico di lavoro. Esempio: l'utilizzo normalizzato è definito come il valore massimo dell'utilizzo di Ur/s in tutti gli intervalli di chiavi di partizione. Si supponga, ad esempio, che la velocità effettiva massima sia di 20.000 UR/sec e che siano presenti due intervalli di chiavi di partizione, P_1 e P_2, ciascuno in grado di scalare fino a 10.000 UR/sec. In un secondo specifico, se P_1 ha usato 6000 UR e P_2 8000 UR, l'utilizzo normalizzato corrisponde a MAX(6000 UR / 10.000 UR, 8000 UR / 10.000 UR) = 0,8.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>Cosa aspettarsi e quando le UR/sec normalizzate sono più elevate

Quando il consumo di Ur/s normalizzato raggiunge il 100% per l'intervallo di chiavi di partizione specificato e se un client effettua ancora richieste nell'intervallo di tempo di un secondo per l'intervallo di chiavi di partizione specifico, riceve un errore di frequenza limitato. Il client deve rispettare il tempo di attesa suggerito e ripetere la richiesta. L'SDK semplifica la gestione di questa situazione ritentando i tempi preconfigurati attendendo in modo appropriato.  Non è necessario visualizzare l'errore di limitazione della frequenza ur solo perché l'unità richiesta normalizzata ha raggiunto il 100%. Questo perché l'unità richiesta normalizzata è un singolo valore che rappresenta l'utilizzo massimo di tutti gli intervalli di chiavi di partizione, un intervallo di chiavi di partizione può essere occupato, ma gli altri intervalli di chiavi di partizione possono soddisfare le richieste senza problemi. Ad esempio, una singola operazione, ad esempio un stored procedure che utilizza tutte le UR/s in un intervallo di chiavi di partizione, provocherà un breve picco nel consumo di Ur/s normalizzato. In questi casi, non si verificano errori di limitazione della frequenza immediata se la frequenza delle richieste è insufficiente o se le richieste vengono eseguite ad altre partizioni in intervalli di chiavi di partizione differenti. 

Le metriche di monitoraggio di Azure consentono di trovare le operazioni per ogni codice di stato per l'API SQL usando la metrica **Totale richieste** . Successivamente, è possibile filtrare in base a queste richieste con il codice di stato 429 e suddividerle in base al **tipo di operazione** .  

Per trovare le richieste con frequenza limitata, è consigliabile ottenere queste informazioni tramite i log di diagnostica.

Se è presente un picco continuo del 100% di utilizzo di Ur/s normalizzato o vicino al 100% tra più intervalli di chiavi di partizione, è consigliabile aumentare la velocità effettiva. È possibile scoprire quali operazioni sono pesanti e i loro picchi di utilizzo usando le metriche di monitoraggio di Azure e i log di diagnostica di monitoraggio di Azure.

In sintesi, la metrica di **consumo ur normalizzata** viene usata per vedere quale intervallo di chiavi di partizione è più caldo in termini di utilizzo. In questo modo si ottiene l'inclinazione della velocità effettiva verso un intervallo di chiavi di partizione. In un secondo momento, è possibile visualizzare il log di **PartitionKeyRUConsumption** nei log di monitoraggio di Azure per ottenere informazioni sulle chiavi di partizione logiche che sono a caldo in termini di utilizzo. In questo modo si punterà a modificare la scelta della chiave di partizione o alla modifica della logica dell'applicazione. Per risolvere la limitazione della frequenza, distribuire il carico dei dati tra più partizioni o semplicemente aumentare la velocità effettiva in quanto è effettivamente necessaria. 



## <a name="view-the-normalized-request-unit-consumption-metric"></a>Visualizzare la metrica di consumo unità richiesta normalizzata

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Nel menu di spostamento a sinistra selezionare **Monitoraggio** e quindi selezionare **Metriche** .

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Riquadro Metriche in Monitoraggio di Azure":::

3. Nel riquadro **Metriche** > **Selezionare una risorsa** > scegliere la **sottoscrizione** e il **gruppo di risorse** richiesti. Per **Tipo di risorsa** selezionare **Account Azure Cosmos DB** , scegliere uno degli account Azure Cosmos esistenti e selezionare **Applica** .

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="Riquadro Metriche in Monitoraggio di Azure":::

4. È quindi possibile selezionare una metrica dall'elenco delle metriche disponibili. È possibile selezionare metriche specifiche per unità richiesta, archiviazione, latenza, disponibilità, Cassandra e altre. Per informazioni dettagliate su tutte le metriche disponibili in questo elenco, vedere l'articolo [Metriche per categoria](monitor-cosmos-db-reference.md). In questo esempio, selezionare metrica **consumo ur normalizzato** e **Max** come valore di aggregazione.

   Oltre a questi dettagli, è anche possibile selezionare l' **Intervallo di tempo** e la **Granularità temporale** delle metriche. Al massimo, è possibile visualizzare le metriche degli ultimi 30 giorni.  Dopo aver applicato il filtro, viene visualizzato un grafico in base al filtro.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Riquadro Metriche in Monitoraggio di Azure":::

### <a name="filters-for-normalized-request-unit-consumption"></a>Filtri per il consumo di unità richiesta normalizzate

È anche possibile filtrare le metriche e il grafico visualizzato da uno specifico **CollectionName** , **DatabaseName** , **PartitionKeyRangeID** e **Region** . Per filtrare le metriche, selezionare **Aggiungi filtro** e scegliere la proprietà obbligatoria, ad esempio **CollectionName** e il valore corrispondente a cui si è interessati. Il grafico Visualizza quindi le unità di consumo ur normalizzate utilizzate per il contenitore per il periodo selezionato.  

È possibile raggruppare le metriche usando l'opzione **Applicare separazione** .  

La metrica di utilizzo delle unità richiesta normalizzata per ogni contenitore viene visualizzata come illustrato nell'immagine seguente:

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="Riquadro Metriche in Monitoraggio di Azure":::

## <a name="next-steps"></a>Passaggi successivi

* Monitorare i dati Azure Cosmos DB usando [le impostazioni di diagnostica](cosmosdb-monitor-resource-logs.md) in Azure.
* [Controllare le operazioni del piano di controllo Azure Cosmos DB](audit-control-plane-logs.md)
