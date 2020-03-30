---
title: Introduzione a Azure Metrics Explorer
description: Informazioni su come creare il primo grafico delle metriche con Esplora metriche di Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 920ff19b162843d1c2d631ad192ce1b3c4273a68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248775"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Introduzione a Esplora metriche di Azure

## <a name="where-do-i-start"></a>Da dove comincio
Esplora metriche di Monitoraggio di Azure è un componente del portale di Microsoft Azure che consente di tracciare grafici, correlare visivamente le tendenze ed esaminare i picchi e i cali nei valori delle metriche. Usare Esplora metriche per esaminare l'integrità e l'utilizzo delle risorse. Iniziare nel seguente ordine:

1. [Scegli una risorsa e una metrica](#create-your-first-metric-chart) per visualizzare un grafico di base. Quindi [selezionare un intervallo](#select-a-time-range) di tempo rilevante per l'indagine.

1. Provare ad applicare i [filtri di dimensione e la divisione.](#apply-dimension-filters-and-splitting) I filtri e la divisione consentono di analizzare quali segmenti della metrica contribuiscono al valore metrico complessivo e identificare i possibili outlier.

1. Utilizzare [le impostazioni avanzate](#advanced-chart-settings) per personalizzare il grafico prima di aggiungerlo ai dashboard. [Configurare gli avvisi](alerts-metric-overview.md) per ricevere notifiche quando il valore della metrica supera o scende al di sotto di una soglia.

## <a name="create-your-first-metric-chart"></a>Creare il primo grafico delle metriche

Per creare un grafico metrico, dalla visualizzazione risorsa, gruppo di risorse, sottoscrizione o Monitoraggio di Azure aprire la scheda **Metriche** e eseguire la procedura seguente:To create a metric chart, from your resource, resource group, subscription, or Azure Monitor view, open the Metrics tab and follow these steps:

1. Usando la selezione risorse, selezionare la risorsa per la quale si desidera visualizzare le metriche. (La risorsa è preselezionata se sono state aperte **metriche** nel contesto di una risorsa specifica).

    > ![Selezionare una risorsa](./media/metrics-getting-started/resource-picker.png)

2. Per alcune risorse, è necessario selezionare uno spazio dei nomi. Lo spazio dei nomi è semplicemente un modo per organizzare le metriche e individuarle più facilmente. Ad esempio, gli account di archiviazione hanno spazi dei nomi distinti per l'archiviazione delle metriche di file, tabelle, BLOB e code. Molti tipi di risorse hanno solo uno spazio dei nomi.

3. Selezionare una metrica da un elenco di metriche disponibili.

    > ![Selezionare una metrica](./media/metrics-getting-started/metric-picker.png)

4. Facoltativamente, è possibile modificare l'aggregazione metrica. Ad esempio, è possibile che si desideri che il grafico mostri i valori minimi, massimi o medi della metrica.

> [!NOTE]
> Usare il pulsante **Aggiungi metrica** e ripetere questi passaggi per visualizzare più metriche tracciate nello stesso grafico. Per più grafici in una visualizzazione, selezionare il pulsante **Aggiungi grafico** in alto.

## <a name="select-a-time-range"></a>Selezionare un intervallo di tempo

Per impostazione predefinita, il grafico mostra le ultime 24 ore di dati della metrica. Usa il pannello **di selezione del tempo** per modificare l'intervallo di tempo, ingrandire o ridurre il grafico. 

![Pannello per la modifica dell'intervallo di tempo](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> Utilizzare il **pennello temporale** per esaminare un'area interessante del grafico (spike o tuffo). Posizionare il puntatore del mouse all'inizio dell'area, fare clic e tenere premuto il pulsante sinistro del mouse, trascinare verso l'altro lato dell'area e quindi rilasciare il pulsante. Il grafico ingrandirà tale intervallo di tempo. 

## <a name="apply-dimension-filters-and-splitting"></a>Applicare i filtri e la divisione delle dimensioni

[Il filtro](metrics-charts.md#apply-filters-to-charts) e la [suddivisione](metrics-charts.md#apply-splitting-to-a-chart) sono potenti strumenti diagnostici per le metriche con dimensioni. Queste funzionalità mostrano l'impatto dei vari segmenti di metrica ("valori di dimensione") sul valore complessivo della metrica e consentono di identificare i possibili outlier.

- Il **filtro** consente di scegliere i valori di dimensione inclusi nel grafico. Ad esempio, è possibile visualizzare le richieste riuscite durante la creazione di un grafico della metrica del tempo di risposta del *server.* È necessario applicare il filtro sulla dimensione *richiesta riuscita.* 

- La **divisione** controlla se nel grafico vengono visualizzate righe separate per ogni valore di una dimensione o se i valori vengono aggregati in una singola riga. Ad esempio, è possibile visualizzare una riga per un tempo di risposta medio in tutte le istanze del server o visualizzare righe separate per ogni server. È necessario applicare la divisione nella dimensione *dell'istanza* del server per visualizzare linee separate.

Vedere gli [esempi dei grafici](metric-chart-samples.md) a cui sono applicati il filtro e la divisione. L'articolo mostra i passaggi sono stati utilizzati per configurare i grafici.

## <a name="advanced-chart-settings"></a>Impostazioni avanzate del grafico

È possibile personalizzare lo stile del grafico, il titolo e modificare le impostazioni avanzate del grafico. Al termine della personalizzazione, aggiungerlo a un dashboard per salvare le scelte. È anche possibile configurare gli avvisi relativi alle metriche. Seguire la [documentazione del prodotto](metrics-charts.md) per informazioni su queste e altre funzionalità avanzate di Esplora metriche di Monitoraggio di Azure.Follow product documentation to learn about these and other advanced features of Azure Monitor metrics explorer.

## <a name="next-steps"></a>Passaggi successivi

* [Scopri le funzionalità avanzate di Esplora metriche](metrics-charts.md)
* [Risoluzione dei problemi di Esplora metriche](metrics-troubleshoot.md)
* [Elenco di metriche disponibili per i servizi di Azure](metrics-supported.md)
* [Esempi di grafici configurati](metric-chart-samples.md)
