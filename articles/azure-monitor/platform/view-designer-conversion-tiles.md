---
title: Progettazione della visualizzazione di Monitoraggio di Azure per le conversioni di sezioni delle cartelle di lavoroAzure Monitor view designer to workbooks tile conversions
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f07d15521c787dfd588c285bff57616059caa2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658627"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Conversioni di riquadri della finestra di progettazione di Azure MonitorAzure Monitor view tile conversions
[Progettazione viste](view-designer.md) è una funzionalità di Monitoraggio di Azure che consente di creare visualizzazioni personalizzate che consentono di visualizzare i dati nell'area di lavoro di Log Analytics, con grafici, elenchi e sequenze temporali. Essi vengono gradualmente eliminati e sostituiti con cartelle di lavoro che forniscono funzionalità aggiuntive. In questo articolo vengono fornite informazioni dettagliate per la conversione di riquadri diversi in cartelle di lavoro.

## <a name="donut--list-tile"></a>Riquadro dell'elenco & ciambella

![Elenco ciambelle](media/view-designer-conversion-tiles/donut-list.png)

La ricreazione dell'& riquadro dell'elenco delle & nelle cartelle di lavoro comporta due visualizzazioni separate. Per la parte ciambella ci sono due opzioni.
Per entrambi, selezionare **Aggiungi query** e incollare la query originale da Progettazione visualizzazioni nella cella.

**Opzione 1:** Selezionare **Grafico a torta** dal menu a discesa **Visualizzazione:** ![menu Visualizzazione grafico a torta](media/view-designer-conversion-tiles/pie-chart.png)

**Opzione 2:** Selezionare **Imposta per query** dall'elenco a discesa **Visualizzazione** e aggiungere `| render piechart` alla query:

 ![Menu Visualizzazione](media/view-designer-conversion-tiles/set-by-query.png)

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

Per la creazione di un elenco e l'abilitazione dei grafici sparkline, vedere l'articolo sulle [attività comuni](view-designer-conversion-tasks.md).

Di seguito è riportato un esempio di come il riquadro dell'elenco & ciambella potrebbe essere reinterpretato nelle cartelle di lavoro:

![Cartelle di lavoro per l'elenco delle ciambelle](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Riquadro elenco & grafico a linee
![Grafico a linee Elenco](media/view-designer-conversion-tiles/line-list.png) 

Per ricreare la parte del grafico a linee, aggiornare la query come segue:

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

Sono disponibili due opzioni per visualizzare il grafico a linee

**Opzione 1:** Selezionare **Grafico a linee** dal menu a discesa **Visualizzazione:**
 
 ![Menu Grafico a linee](media/view-designer-conversion-tiles/line-visualization.png)

**Opzione 2:** Selezionare **Imposta per query** dall'elenco a discesa **Visualizzazione** e aggiungere `| render linechart` alla query:

 ![Menu Visualizzazione](media/view-designer-conversion-tiles/set-by-query.png)

**Esempio**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

Per la creazione di un elenco e l'abilitazione dei grafici sparkline, vedere l'articolo sulle [attività comuni](view-designer-conversion-tasks.md).

Di seguito è riportato un esempio di come il grafico a linee & riquadro dell'elenco potrebbe essere reinterpretato nelle cartelle di lavoro:

![Cartelle di lavoro dell'elenco di grafici a linee](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>Numero & riquadro elenco

 ![Elenco riquadri](media/view-designer-conversion-tiles/tile-list-example.png)

Per il riquadro del numero, aggiornare la query come segue:

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

Modificare l'elenco a discesa Visualizzazione in **Riquadri,** quindi selezionare **Impostazioni riquadro**.
 ![Visualizzazione dei riquadri](media/view-designer-conversion-tiles/tile-visualization.png)

Lasciare vuota la sezione **Titolo** e selezionare **Sinistra**. Modificare il valore per **usa colonna:** a **Conteggio**e **Rendering colonne** in Numero **grande**:

![Impostazioni riquadro](media/view-designer-conversion-tiles/tile-settings.png)

 
Per la creazione di un elenco e l'abilitazione dei grafici sparkline, vedere l'articolo sulle [attività comuni](view-designer-conversion-tasks.md).

Di seguito è riportato un esempio di come il numero & riquadro dell'elenco potrebbe essere reinterpretato nelle cartelle di lavoro:

![Numero di cartelle di lavoro per elenchi](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Sequenza temporale ed elenco

 ![Elenco sequenza temporale](media/view-designer-conversion-tiles/time-list.png)

Per la sequenza temporale aggiornare la query come segue:

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

Sono disponibili due opzioni per visualizzare la query come grafico a barre:

**Opzione 1:** Selezionare **Grafico a barre** dall'elenco a discesa **Visualizzazione:** ![Visualizzazione grafico a barre](media/view-designer-conversion-tiles/bar-visualization.png)
 
**Opzione 2:** Selezionare **Imposta per query** dall'elenco a discesa **Visualizzazione** e aggiungere `| render barchart` alla query:

 ![Menu Visualizzazione](media/view-designer-conversion-tiles/set-by-query.png)

 
Per la creazione di un elenco e l'abilitazione dei grafici sparkline, vedere l'articolo sulle [attività comuni](view-designer-conversion-tasks.md).

Di seguito è riportato un esempio di come il riquadro & elenco della sequenza temporale potrebbe essere reinterpretato nelle cartelle di lavoro:

![Cartelle di lavoro dell'elenco cronologici](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della transizione da Progettazione viste a cartelle di lavoro](view-designer-conversion-overview.md)
