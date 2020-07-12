---
title: Introduzione a Esplora metriche di Azure
description: Informazioni su come creare il primo grafico delle metriche con Esplora metriche di Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 14f21b1c0bff5f4d5f77b1547c09ddd6c370ece0
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86276431"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Introduzione a Esplora metriche di Azure

## <a name="where-do-i-start"></a>Da dove iniziare
Esplora metriche di Monitoraggio di Azure è un componente del portale di Microsoft Azure che consente di tracciare grafici, correlare visivamente le tendenze ed esaminare i picchi e i cali nei valori delle metriche. Usare Esplora metriche per esaminare l'integrità e l'utilizzo delle risorse. Iniziare nell'ordine seguente:

1. [Selezionare una risorsa e una metrica](#create-your-first-metric-chart) per visualizzare un grafico di base. Quindi [selezionare un intervallo di tempo](#select-a-time-range) pertinente per l'analisi.

1. Provare [a applicare i filtri e la suddivisione delle dimensioni](#apply-dimension-filters-and-splitting). I filtri e la suddivisione consentono di analizzare quali segmenti della metrica contribuiscono al valore della metrica complessiva e di identificare i possibili outlier.

1. Usare [le impostazioni avanzate](#advanced-chart-settings) per personalizzare il grafico prima di aggiungerlo ai dashboard. [Configurare gli avvisi](alerts-metric-overview.md) per ricevere notifiche quando il valore della metrica supera o scende al di sotto di una soglia.

## <a name="create-your-first-metric-chart"></a>Creare il primo grafico delle metriche

Per creare un grafico delle metriche, da una risorsa, un gruppo di risorse, una sottoscrizione o una vista di monitoraggio di Azure, aprire la scheda **metriche** e seguire questa procedura:

1. Usando il selettore di risorse, selezionare la risorsa per cui si vogliono visualizzare le metriche. La risorsa è preselezionata se si aprono le **metriche** nel contesto di una risorsa specifica.

    > ![Selezionare una risorsa](./media/metrics-getting-started/resource-picker.png)

2. Per alcune risorse, è necessario selezionare uno spazio dei nomi. Lo spazio dei nomi è semplicemente un modo per organizzare le metriche e individuarle più facilmente. Ad esempio, gli account di archiviazione hanno spazi dei nomi distinti per l'archiviazione delle metriche di file, tabelle, BLOB e code. Molti tipi di risorse hanno solo uno spazio dei nomi.

3. Selezionare una metrica da un elenco di metriche disponibili.

    > ![Selezionare una metrica](./media/metrics-getting-started/metric-picker.png)

4. Facoltativamente, è possibile [modificare l'aggregazione delle metriche](metrics-charts.md#changing-aggregation). È ad esempio possibile fare in modo che il grafico mostri i valori minimo, massimo o medio della metrica.

> [!NOTE]
> Usare il pulsante **Aggiungi metrica** e ripetere questi passaggi per visualizzare più metriche tracciate nello stesso grafico. Per più grafici in una visualizzazione, selezionare il pulsante **Aggiungi grafico** nella parte superiore.

## <a name="select-a-time-range"></a>Selezionare un intervallo di tempo

Per impostazione predefinita, il grafico mostra le ultime 24 ore di dati della metrica. Usare il pannello **selezione ora** per modificare l'intervallo di tempo, ingrandire o ridurre il grafico. 

![Pannello per la modifica dell'intervallo di tempo](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> Usare il **pennello ora** per esaminare un'area interessante del grafico (picco o DIP). Posizionare il puntatore del mouse all'inizio dell'area, fare clic e tenendo premuto il pulsante sinistro del mouse, trascinare l'altro lato dell'area, quindi rilasciare il pulsante. Il grafico ingrandirà tale intervallo di tempo. 

## <a name="apply-dimension-filters-and-splitting"></a>Applicare i filtri e la divisione delle dimensioni

Il [filtro](metrics-charts.md#apply-filters-to-charts) e la [suddivisione](metrics-charts.md#apply-splitting-to-a-chart) sono strumenti di diagnostica avanzati per le metriche con dimensioni. Queste funzionalità mostrano il modo in cui i vari segmenti di metrica ("valori di dimensione") influiscano sul valore complessivo della metrica e consentono di identificare possibili outlier.

- Il **filtro** consente di scegliere i valori di dimensione inclusi nel grafico. Ad esempio, potrebbe essere necessario visualizzare le richieste riuscite quando si crea un grafico della metrica del *tempo di risposta del server* . È necessario applicare il filtro in caso *di esito positivo della dimensione della richiesta* . 

- La **divisione** controlla se nel grafico vengono visualizzate righe separate per ogni valore di una dimensione o se i valori vengono aggregati in una singola riga. Ad esempio, è possibile visualizzare una riga per un tempo di risposta medio per tutte le istanze del server o vedere righe separate per ogni server. Per visualizzare righe separate, è necessario applicare la suddivisione alla dimensione dell' *istanza del server* .

Vedere gli [esempi dei grafici](metric-chart-samples.md) a cui sono applicati il filtro e la divisione. In questo articolo vengono illustrati i passaggi usati per configurare i grafici.

## <a name="advanced-chart-settings"></a>Impostazioni avanzate del grafico

È possibile personalizzare lo stile del grafico, il titolo e modificare le impostazioni avanzate del grafico. Al termine della personalizzazione, aggiungerlo a un dashboard per salvare le scelte. È anche possibile configurare gli avvisi relativi alle metriche. Seguire la [documentazione del prodotto](metrics-charts.md) per informazioni su queste e altre funzionalità avanzate di Esplora metriche di monitoraggio di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulle funzionalità avanzate di Esplora metriche](metrics-charts.md)
* [Risoluzione dei problemi di Esplora metriche](metrics-troubleshoot.md)
* [Elenco di metriche disponibili per i servizi di Azure](metrics-supported.md)
* [Esempi di grafici configurati](metric-chart-samples.md)
