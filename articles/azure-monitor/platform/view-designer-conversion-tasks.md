---
title: Progettazione viste di monitoraggio di Azure per la conversione di cartelle di lavoro attività comuni
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5559dac916262998d621b40757398088ec613609
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658745"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>Progettazione viste in cartelle di lavoro conversione di attività comuni
[Progettazione](view-designer.md) viste è una funzionalità di monitoraggio di Azure che consente di creare visualizzazioni personalizzate che consentono di visualizzare i dati nell'area di lavoro log Analytics, con grafici, elenchi e sequenze temporali. Vengono eliminati e sostituiti con cartelle di lavoro che forniscono funzionalità aggiuntive. Questo articolo descrive in dettaglio le attività comuni per la conversione delle visualizzazioni in cartelle di lavoro.


## <a name="quickstart-with-preset-view-designer-templates"></a>Guida introduttiva ai modelli di progettazione visualizzazioni preimpostati

Per le cartelle di lavoro nelle aree di lavoro Log Analytics sono già stati creati modelli che corrispondono ad alcune viste in Progettazione viste. Nella categoria **guide Progettazione viste** selezionare **Visualizza Guida alla transizione di progettazione** per informazioni sulle opzioni o selezionare uno dei modelli preimpostati.

![Modelli di esempio](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>Abilitazione filtro intervallo di tempo
In Progettazione viste è disponibile un filtro predefinito per l'intervallo di tempo, tuttavia, nelle cartelle di lavoro di questa impostazione non è abilitata per impostazione predefinita. Le cartelle di lavoro consentono agli utenti di creare filtri di intervallo di tempo che potrebbero essere più applicabili ai log di dati. Di seguito sono elencati i passaggi per generare il filtro:

Selezionare l'opzione **Aggiungi parametri** . Lo **stile** predefinito è impostato su *pills*.

![Aggiungi param](media/view-designer-conversion-tasks/add-param.png)

 Selezionare il pulsante **Aggiungi parametro** .

![Aggiungi parametro](media/view-designer-conversion-tasks/add-parameter.png)

Dal menu sidebar digitare *TimeRange*nella casella di testo **nome parametro** . Imposta il **tipo di parametro** come *selezione intervallo di tempo*. Selezionare la casella di controllo **required?** .

![Menu parametri](media/view-designer-conversion-tasks/parameter-menu.png)

Salvare il parametro nell'angolo superiore sinistro del menu sidebar. Per impostazione predefinita, è possibile lasciare l' *elenco a discesa* o selezionare un valore predefinito di **TimeRange** , ad esempio *24 ore*. Selezionare **modifica eseguita**.

I parametri possono essere usati nelle query aggiungendo le parentesi graffe {} intorno al nome del parametro. Per ulteriori informazioni sui parametri, vedere la documentazione relativa alle [cartelle di lavoro relativa ai parametri](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md).

## <a name="updating-queries-with-the-timerange-parameter"></a>Aggiornamento di query con il parametro TimeRange

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>Opzione 1: selezionare TimeRange dall'elenco a discesa intervallo di tempo

![Parametro time](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>Opzione 2: aggiornare le query di log

Nella query aggiungere la riga: `| where TimeGenerated {TimeRange}` come nell'esempio seguente:

Query originale
```KQL
search * 
| summarize count() by Type
```

Query aggiornata
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>Inclusione di un elenco
La maggior parte delle visualizzazioni di Progettazione viste includono un elenco ed è possibile riprodurre questo elenco standard in una cartella di lavoro di.

![Elenco affiancato](media/view-designer-conversion-tasks/tile-list.png)

Per aggiungere una visualizzazione, fare clic su **Aggiungi query** dalle opzioni della cella.

![Aggiungi param](media/view-designer-conversion-tasks/add-param.png)

Progettazione viste utilizza una query predefinita che corrisponde alla sintassi dell'esempio originale. Questa operazione può essere aggiornata modificando la query nel form aggiornato come nell'esempio seguente:

Query originale
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Query aggiornata
```KQL
search * 
| summarize Count = count() by Type
```

Verrà generato un elenco simile al seguente:

![Esempio di elenco](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>Abilitazione di grafici sparkline
Una funzionalità comune per le griglie consiste nell'aggiungere grafici sparkline per riepilogare diversi modelli di dati nel tempo. Progettazione viste offre la funzionalità **Abilita grafici sparkline** per tutti gli elenchi, come le cartelle di lavoro di. Per includere grafici sparkline nei dati che corrispondono a Progettazione viste, unire i dati alla query originale come nell'esempio seguente:

Query originale
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

Query aggiornata
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

Selezionare **le impostazioni della colonna**.
![Impostazioni colonne](media/view-designer-conversion-tasks/column-settings.png)

Aggiornare l'elenco a discesa **renderer di colonna** in un' *area di Spark*.
![Grafici sparkline](media/view-designer-conversion-tasks/sparkline.png)

Salvare le impostazioni ed eseguire di nuovo la query per aggiornare la tabella in modo da includere un sparkline.

La griglia risultante sarà simile alla seguente: ![ esempio sparkline](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Impostazioni avanzate delle celle
Per la progettazione di viste con mirroring, è possibile eseguire attività quali la modifica delle dimensioni delle celle di una cartella di lavoro o l'aggiunta di pin e collegamenti esterni ai log.

Per accedere alle **Impostazioni avanzate** , selezionare l'icona a forma di ingranaggio nella parte inferiore di ogni cella.

![Impostazioni avanzate](media/view-designer-conversion-tasks/advanced-settings.png)

Verrà visualizzato un menu con diverse opzioni:

![Impostazioni avanzate impostazioni](media/view-designer-conversion-tasks/advanced-settings-settings.png)

Per aggiungere un PIN e un collegamento a una query esterna, selezionare le caselle di controllo corrispondenti. Per aggiungere un titolo alla cella, digitare il titolo desiderato nella sezione del **titolo del grafico** .

Per impostazione predefinita, una cella di cartelle di lavoro è impostata in modo da occupare tutta la larghezza della pagina, ma è possibile modificarla ridimensionando la cella sotto la scheda **stile** del menu **Impostazioni avanzate** .

![Stile impostazioni avanzate](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>Parametri aggiuntivi
Selezionare **Aggiungi parametro** per creare un nuovo parametro nella cartella di lavoro. 

Per selezionare una sottoscrizione, digitare *sottoscrizione* nel campo **nome parametro** nel menu laterale e selezionare *selezione sottoscrizione* dall'elenco a discesa **tipo di parametro** .

![Menu sottoscrizione](media/view-designer-conversion-tasks/subscription-filter.png)

Per selezionare una risorsa, digitare *Resource* nel campo **nome parametro** nel menu laterale e selezionare *selezione risorse* dall'elenco a discesa **tipo di parametro** .

![Menu delle risorse](media/view-designer-conversion-tasks/resource-filter.png)

Questa operazione consente di inserire elenchi a discesa che consentono di accedere a diverse sottoscrizioni e risorse.

![Elenco a discesa risorsa sottoscrizione](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>Passaggi successivi
- [Conversioni di riquadri](view-designer-conversion-tiles.md)
