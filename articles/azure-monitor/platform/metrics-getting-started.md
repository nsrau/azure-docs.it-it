---
title: Introduzione a Esplora metriche di Azure
description: Informazioni su come creare il primo grafico delle metriche con Esplora metriche di Azure.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e611f1564896cfdecb3ce34ab7c5361e5200b48a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537338"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Introduzione a Esplora metriche di Azure

## <a name="where-do-i-start"></a>Da dove iniziare
Esplora metriche di Monitoraggio di Azure è un componente del portale di Microsoft Azure che consente di tracciare grafici, correlare visivamente le tendenze ed esaminare i picchi e i cali nei valori delle metriche. Usare Esplora metriche per analizzare l'utilizzo delle risorse e l'integrità. Si avviino nell'ordine seguente:

1. [Selezionare una risorsa e una metrica](#create-your-first-metric-chart) e viene visualizzato un grafico di base. Quindi [selezionare un intervallo di tempo](#select-a-time-range) che sono rilevanti per l'analisi.

1. Provare [applicando filtri di dimensione e la suddivisione](#apply-dimension-filters-and-splitting). I filtri e dividendo per analizzare i segmenti della metrica contribuiscono al valore complessivo delle metriche e identificare i possibili outlier.

1. Uso [impostazioni avanzate](#advanced-chart-settings-and-next-steps) per personalizzare il grafico prima di aggiungerlo al dashboard. [Configurare gli avvisi](alerts-metric-overview.md) per ricevere notifiche quando il valore della metrica supera o scende sotto una soglia.

## <a name="create-your-first-metric-chart"></a>Creare il primo grafico delle metriche

Per creare un grafico di metriche dalla risorsa, gruppo di risorse, sottoscrizione o visualizzazione di monitoraggio di Azure, aprire il **metriche** scheda e seguire questa procedura:

1. Utilizzando il selettore risorse, selezionare la risorsa per cui si desidera visualizzare le metriche. (La risorsa è già selezionata se è stata aperta **metriche** nel contesto di una risorsa specifica).

    > ![Selezionare una risorsa](./media/metrics-getting-started/resource-picker.png)

2. Per alcune risorse, è necessario selezionare uno spazio dei nomi. Lo spazio dei nomi è semplicemente un modo per organizzare le metriche in modo che è possibile trovarli con facilità. Ad esempio, gli account di archiviazione hanno diversi spazi dei nomi per archiviare le metriche di file, tabelle, BLOB e code. Molti tipi di risorse sono uno spazio dei nomi.

3. Selezionare una metrica da un elenco delle metriche disponibili.

    > ![Selezionare una metrica](./media/metrics-getting-started/metric-picker.png)

4. Facoltativamente, è possibile modificare l'aggregazione delle metriche. Ad esempio, è possibile che il grafico per visualizzare i valori minimi, massimi o medi della metrica.

> [!NOTE]
> Usare la **Aggiungi metrica** pulsante e ripetere questi passaggi se si desidera visualizzare più metriche tracciate indicano valori nello stesso grafico. Per più grafici in un'unica visualizzazione, selezionare la **Aggiungi grafico** pulsante nella parte superiore.

## <a name="select-a-time-range"></a>Selezionare un intervallo di tempo

Per impostazione predefinita, il grafico mostra le ultime 24 ore di dati delle metriche. Usare la **date time picker** pannello per modificare l'intervallo di tempo, lo zoom avanti o eseguire lo zoom indietro del grafico. 

![Pannello intervallo ora di modifica](./media/metrics-getting-started/time-picker.png)

## <a name="apply-dimension-filters-and-splitting"></a>Applicare i filtri di dimensione e la suddivisione

[Filtraggio](metrics-charts.md#apply-filters-to-charts) e [suddivisione](metrics-charts.md#apply-splitting-to-a-chart) sono potenti strumenti di diagnostica per le metriche con dimensioni. Queste funzionalità mostrano il valore complessivo della metrica delle varie impatto metrica segmenti ("valori di dimensione") e consentono di identificare gli outlier possibili.

- **Il filtro** consente di scegliere quali i valori della dimensione sono inclusi nel grafico. Ad esempio, è possibile visualizzare richieste con esito positivo quando la creazione di grafici il *tempo di risposta server* metrica. È necessario applicare il filtro sul *esito positivo della richiesta* dimensione. 

- **Suddivisione** controlli se il grafico visualizza separate le righe per ogni valore di una dimensione o aggregarne i valori in una singola riga. Ad esempio, è possibile visualizzare una riga per un tempo di risposta medio per tutte le istanze di server o vedere righe separate per ogni server. È necessario applicare nella suddivisione il *istanza del server* dimensione per visualizzare righe separate.

Visualizzare [esempi dei grafici](metric-chart-samples.md) che sono applicati filtri e dividendo. L'articolo illustra che i passaggi sono stati usati per configurare i grafici.

## <a name="advanced-chart-settings-and-next-steps"></a>Impostazioni avanzate del grafico e i passaggi successivi

È possibile personalizzare lo stile del grafico, titolo e modificare le impostazioni avanzate del grafico. Al termine con la personalizzazione, aggiungerla a un dashboard per salvare il lavoro. È anche possibile configurare gli avvisi delle metriche. Seguire [documentazione del prodotto](metrics-charts.md) per informazioni su questi e altri avanzate funzionalità di Esplora metriche di monitoraggio di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Visualizzare un elenco delle metriche disponibili per servizi di Azure](metrics-supported.md)
* [Informazioni sulle funzionalità avanzate di Esplora metriche](metrics-charts.md)
* [Vedere esempi grafici configurati](metric-chart-samples.md)
