---
title: Esercitazione - Creare un grafico delle metriche in Monitoraggio di Azure
description: Informazioni su come creare il primo grafico delle metriche con Esplora metriche di Azure.
author: bwren
ms.author: bwren
ms.subservice: metrics
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: d8f72471dd3109bf584d18f032ec2f4d949a4993
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79082813"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>Esercitazione: Creare un grafico delle metriche in Monitoraggio di Azure
Esplora metriche è una funzionalità di Monitoraggio di Azure nel portale di Azure che consente di creare grafici dai valori delle metriche, correlare le tendenze a livello visivo ed esaminare picchi e flessioni nei valori delle metriche. Usare Esplora metriche per esaminare l'integrità e l'utilizzo delle risorse di Azure o per tracciare grafici dalle metriche personalizzate. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Selezionare una metrica per cui si vuole tracciare un grafico
> * Eseguire aggregazioni diverse per i valori della metrica
> * Modificare l'intervallo di tempo e la granularità del grafico

Di seguito è riportato un video che illustra uno scenario più completo rispetto alla procedura descritta in questo articolo. Se non si ha familiarità con le metriche, è consigliabile leggere prima di tutto questo articolo e quindi visualizzare il video per vedere altre informazioni specifiche. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessaria una risorsa di Azure da monitorare. È possibile usare tutte le risorse della sottoscrizione di Azure che supportano le metriche. Per determinare se una risorsa supporta le metriche, passare al relativo menu nel portale di Azure e verificare se è presente un'opzione **Metriche** nella sezione **Monitoraggio** del menu.


## <a name="log-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="open-metrics-explorer-and-select-a-scope"></a>Aprire Esplora metriche e selezionare un ambito
È possibile aprire Esplora metriche dal menu Monitoraggio di Azure o dal menu di una risorsa nel portale di Azure. Sono disponibili le metriche di tutte le risorse indipendentemente dall'opzione usata. 

1. Selezionare **Metriche** dal menu **Monitoraggio di Azure** o dalla sezione **Monitoraggio** del menu di una risorsa.

1. Selezionare l'**Ambito**, ovvero la risorsa per cui si vogliono visualizzare le metriche. L'ambito è già popolato se si apre Esplora metriche dal menu di una risorsa.

    ![Selezionare un ambito](media/tutorial-metrics-explorer/scope-picker.png)

2. Selezionare uno **Spazio dei nomi** se l'ambito ne contiene più di uno. Lo spazio dei nomi è semplicemente un modo per organizzare le metriche e individuarle più facilmente. Ad esempio, gli account di archiviazione hanno spazi dei nomi distinti per l'archiviazione delle metriche di file, tabelle, BLOB e code. Molti tipi di risorse hanno solo uno spazio dei nomi.

3. Selezionare una metrica da un elenco di metriche disponibili per l'ambito e lo spazio dei nomi selezionati.

    ![Selezionare una metrica](media/tutorial-metrics-explorer/metric-picker.png)

4. Facoltativamente, modificare l'**Aggregazione** della metrica. Questa opzione consente di definire il modo in cui i valori della metrica verranno aggregati in base alla granularità temporale per il grafico. Se, ad esempio, la granularità temporale è impostata su 15 minuti e l'aggregazione è impostata sulla somma, ogni punto nel grafico sarà la somma di tutti i valori raccolti in ogni segmento di 15 minuti.

    ![Grafico](media/tutorial-metrics-explorer/chart.png)

5. Usare il pulsante **Aggiungi metrica** e ripetere questi passaggi per visualizzare più metriche tracciate nello stesso grafico. Per più grafici in un'unica visualizzazione, selezionare il pulsante **Nuovo grafico**.

## <a name="select-a-time-range-and-granularity"></a>Selezionare un intervallo di tempo e la granularità

Per impostazione predefinita, il grafico mostra le ultime 24 ore di dati della metrica. Usare il controllo selezione ora per modificare l'**Intervallo di tempo** per il grafico o la **Granularità temporale** che definisce l'intervallo di tempo per ogni punto dati. Nel grafico viene usata l'aggregazione specificata per calcolare tutti i valori campionati per la granularità temporale specificata.

![Pannello per la modifica dell'intervallo di tempo](media/tutorial-metrics-explorer/time-picker.png)


Usare **Time Brush** per esaminare un'area interessante del grafico, ad esempio un picco o una flessione. Posizionare il puntatore del mouse all'inizio dell'area, fare clic e tenendo premuto il pulsante sinistro del mouse, trascinare l'altro lato dell'area e rilasciare il pulsante. Il grafico ingrandirà tale intervallo di tempo. 

![Time Brush](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>Applicare i filtri e la divisione delle dimensioni
Vedere i riferimenti seguenti per le funzionalità avanzate che consentono di eseguire ulteriori analisi sulle metriche e identificare potenziali outlier nei dati.

- Il [filtro](../platform/metrics-charts.md#apply-filters-to-charts) consente di scegliere i valori di dimensione inclusi nel grafico. Ad esempio, potrebbe essere necessario visualizzare solo le richieste riuscite quando si crea un grafico per la metrica relativa al *tempo di risposta del server*. 

- La [divisione](../platform/metrics-charts.md#apply-splitting-to-a-chart) controlla se nel grafico vengono visualizzate righe separate per ogni valore di una dimensione o se i valori vengono aggregati in una singola riga. Ad esempio, potrebbe essere necessario visualizzare una riga per un tempo di risposta medio per tutte le istanze del server oppure visualizzare righe separate per ogni server. 

Vedere gli [esempi dei grafici](../platform/metric-chart-samples.md) a cui sono applicati il filtro e la divisione.

## <a name="advanced-chart-settings"></a>Impostazioni avanzate del grafico

È possibile personalizzare lo stile del grafico, il titolo e modificare le impostazioni avanzate del grafico. Al termine della personalizzazione, aggiungerlo a un dashboard per salvare le scelte. È anche possibile configurare gli avvisi relativi alle metriche. Per informazioni su queste e altre funzionalità avanzate di Esplora metriche di Monitoraggio di Azure, vedere [Funzionalità avanzate di Esplora metriche di Azure](../platform/metrics-charts.md#lock-boundaries-of-chart-y-axis).


## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso a usare le metriche in Monitoraggio di Azure, si potrà imparare a usare le metriche per inviare avvisi proattivi.

> [!div class="nextstepaction"]
> [Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure](../platform/alerts-metric.md)

