---
title: Conversioni dei riquadri delle cartelle di lavoro di Progettazione viste di monitoraggio di Azure
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f07d15521c787dfd588c285bff57616059caa2f3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658627"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Conversioni dei riquadri di Progettazione viste di monitoraggio di Azure
[Progettazione](view-designer.md) viste è una funzionalità di monitoraggio di Azure che consente di creare visualizzazioni personalizzate che consentono di visualizzare i dati nell'area di lavoro log Analytics, con grafici, elenchi e sequenze temporali. Vengono eliminati e sostituiti con cartelle di lavoro che forniscono funzionalità aggiuntive. Questo articolo fornisce informazioni dettagliate per la conversione di riquadri diversi in cartelle di lavoro.

## <a name="donut--list-tile"></a>Riquadro elenco & Donut

![Elenco ad anello](media/view-designer-conversion-tiles/donut-list.png)

La ricreazione del riquadro dell'elenco di & ad anello nelle cartelle di lavoro prevede due visualizzazioni separate. Per la porzione di anello sono disponibili due opzioni.
Per iniziare, selezionare **Aggiungi query** e incollare la query originale da Progettazione viste alla cella.

**Opzione 1:** Selezionare un **grafico a torta** dall'elenco a discesa **visualizzazione** : ![ menu visualizzazione grafico a torta](media/view-designer-conversion-tiles/pie-chart.png)

**Opzione 2:** Selezionare **imposta per query** dall'elenco a discesa **visualizzazione** e aggiungere `| render piechart` alla query:

 ![Menu visualizzazione](media/view-designer-conversion-tiles/set-by-query.png)

**Esempio**

Query originale
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

Query aggiornata
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

Per creare un elenco e abilitare i grafici sparkline, vedere l'articolo relativo alle [attività comuni](view-designer-conversion-tasks.md).

Di seguito è riportato un esempio del modo in cui il riquadro dell'elenco di & ad anello può essere riinterpretato nelle cartelle di lavoro:

![Cartelle di lavoro elenco Donut](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Riquadro elenco & grafico a linee
![Elenco grafico a linee](media/view-designer-conversion-tiles/line-list.png) 

Per ricreare la parte relativa al grafico a linee, aggiornare la query nel modo seguente:

Query originale
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Query aggiornata
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

Sono disponibili due opzioni per la visualizzazione del grafico a linee

**Opzione 1:** Selezionare **grafico a linee** nell'elenco a discesa **visualizzazione** :
 
 ![Menu grafico a linee](media/view-designer-conversion-tiles/line-visualization.png)

**Opzione 2:** Selezionare **imposta per query** dall'elenco a discesa **visualizzazione** e aggiungere `| render linechart` alla query:

 ![Menu visualizzazione](media/view-designer-conversion-tiles/set-by-query.png)

**Esempio**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

Per creare un elenco e abilitare i grafici sparkline, vedere l'articolo relativo alle [attività comuni](view-designer-conversion-tasks.md).

Di seguito è riportato un esempio di come il grafico a linee & riquadro dell'elenco potrebbe essere reinterpretato nelle cartelle di lavoro:

![Cartelle di lavoro elenco grafico a linee](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>Riquadro elenco & numero

 ![Elenco affiancato](media/view-designer-conversion-tiles/tile-list-example.png)

Per il riquadro numero, aggiornare la query nel modo seguente:

Query originale
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

Query aggiornata
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

Modificare l'elenco a discesa Visualizzazione in **riquadri** e quindi selezionare **Impostazioni riquadro**.
 ![Visualizzazione affiancata](media/view-designer-conversion-tiles/tile-visualization.png)

Lasciare vuota la sezione **titolo** e selezionare **Left**. Modificare il valore di **USA colonna:** in **conteggio**e il **renderer della colonna** in **numero grande**:

![Impostazioni riquadro](media/view-designer-conversion-tiles/tile-settings.png)

 
Per creare un elenco e abilitare i grafici sparkline, vedere l'articolo relativo alle [attività comuni](view-designer-conversion-tasks.md).

Di seguito è riportato un esempio di come è possibile riinterpretare il riquadro elenco numero & nelle cartelle di lavoro:

![Cartelle di lavoro elenco numeri](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Sequenza temporale ed elenco

 ![Elenco sequenza temporale](media/view-designer-conversion-tiles/time-list.png)

Per la sequenza temporale, aggiornare la query nel modo seguente:

Query originale
```KQL
search * 
| sort by TimeGenerated desc
```

Query aggiornata
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

Sono disponibili due opzioni per la visualizzazione della query come grafico a barre:

**Opzione 1:** Selezionare un **grafico a barre** dall'elenco a discesa **visualizzazione** : ![ visualizzazione Barchart](media/view-designer-conversion-tiles/bar-visualization.png)
 
**Opzione 2:** Selezionare **imposta per query** dall'elenco a discesa **visualizzazione** e aggiungere `| render barchart` alla query:

 ![Menu visualizzazione](media/view-designer-conversion-tiles/set-by-query.png)

 
Per creare un elenco e abilitare i grafici sparkline, vedere l'articolo relativo alle [attività comuni](view-designer-conversion-tasks.md).

Di seguito è riportato un esempio di come è possibile riinterpretare la sequenza temporale & riquadro elenco nelle cartelle di lavoro:

![Cartelle di lavoro elenco sequenza temporale](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Progettazione viste alla transizione di cartelle di lavoro](view-designer-conversion-overview.md)
