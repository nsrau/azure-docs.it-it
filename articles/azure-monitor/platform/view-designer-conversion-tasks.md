---
title: Progettazione visualizzazione di Monitoraggio di Azure in cartelle di lavoro conversione attività comuni
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5559dac916262998d621b40757398088ec613609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658745"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>Visualizzare le attività comuni di conversione da finestra di progettazione a cartella di lavoro
[Progettazione viste](view-designer.md) è una funzionalità di Monitoraggio di Azure che consente di creare visualizzazioni personalizzate che consentono di visualizzare i dati nell'area di lavoro di Log Analytics, con grafici, elenchi e sequenze temporali. Essi vengono gradualmente eliminati e sostituiti con cartelle di lavoro che forniscono funzionalità aggiuntive. In questo articolo vengono descritte in dettaglio le attività comuni alla conversione di visualizzazioni in cartelle di lavoro.


## <a name="quickstart-with-preset-view-designer-templates"></a>Guida introduttiva con modelli di finestra di progettazione di viste preimpostate

Le cartelle di lavoro nelle aree di lavoro di Log Analytics dispongono già di modelli in base ad alcune delle visualizzazioni nella finestra di progettazione delle viste. Nella categoria **Guide di View Designer,** selezionare Guida alla transizione di **View Designer** per informazioni sulle opzioni disponibili o selezionare uno dei modelli predefiniti.

![Modelli di esempio](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>Abilitazione del filtro dell'intervallo di tempo
Progettazione viste dispone di un filtro dell'intervallo di tempo predefinito predefinito incorporato, tuttavia nelle cartelle di lavoro questa impostazione non è abilitata per impostazione predefinita. Le cartelle di lavoro consentono agli utenti di creare filtri dell'intervallo di tempo personalizzati che potrebbero essere più applicabili ai propri registri dati. I passaggi per generare il filtro sono elencati di seguito:

Selezionare l'opzione **Aggiungi parametri.** Lo **stile** predefinito è impostato su *Pillole*.

![Aggiungi param](media/view-designer-conversion-tasks/add-param.png)

 Selezionare il pulsante **Aggiungi parametro.**

![Aggiungi parametro](media/view-designer-conversion-tasks/add-parameter.png)

Dal menu della barra laterale, nella casella di testo **Nome parametro,** digitare *TimeRange*. Impostare **il tipo di parametro** come *selezione intervallo di tempo*. Selezionare la casella di controllo **Obbligatorio().**

![Menu Parametro](media/view-designer-conversion-tasks/parameter-menu.png)

Salva il parametro nell'angolo in alto a sinistra del menu della barra laterale. È possibile lasciare l'elenco a discesa come *non impostato per* impostazione predefinita o selezionare un valore **TimeRange** predefinito, ad esempio *24 ore.* Selezionare **Fine modifica**.

I parametri possono essere utilizzati nelle {} query aggiungendo parentesi graffe intorno al nome del parametro. Ulteriori dettagli sui parametri sono disponibili nella [documentazione delle cartelle di lavoro sui parametri](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md).

## <a name="updating-queries-with-the-timerange-parameter"></a>Aggiornamento delle query con il parametro TimeRange

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>Opzione 1: selezionare TimeRange dal menu a discesa Intervallo di tempoOption 1: Select TimeRange from the Time Range dropdown

![Parametro temporale](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>Opzione 2: Aggiornare le query di logOption 2: Update your log queries

Nella query aggiungere la `| where TimeGenerated {TimeRange}` riga: come nell'esempio seguente:

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
La maggior parte delle visualizzazioni di Progettazione viste include un elenco ed è possibile riprodurre questo elenco standard in una cartella di lavoro.

![Elenco riquadri](media/view-designer-conversion-tasks/tile-list.png)

Aggiungere una visualizzazione facendo clic su **Aggiungi query** dalle opzioni della cella.

![Aggiungi param](media/view-designer-conversion-tasks/add-param.png)

Progettazione viste utilizza una query predefinita che corrisponde alla sintassi dell'esempio Original. Questo può essere aggiornato modificando la query nel modulo aggiornato come nell'esempio seguente:

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

Verrà generato un elenco simile al seguente:This will generate a list that looks similar to the following:

![Esempio di elenco](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>Abilitazione dei grafici sparkline
Una caratteristica comune per le griglie consiste nell'aggiungere grafici sparkline per riepilogare vari modelli di dati nel tempo. Progettazione viste offre la funzionalità **Abilita grafici sparkline** per tutti gli elenchi, così come le cartelle di lavoro. Per includere grafici sparkline nei dati che corrispondono alla finestra di progettazione della visualizzazione, unire i dati con la query originale come nell'esempio seguente:

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

Selezionare **Impostazioni colonna**.
![Impostazioni colonna](media/view-designer-conversion-tasks/column-settings.png)

Aggiornare l'elenco a discesa **Renderer** colonna in modo che sia *un'area Spark*.
![Grafici sparkline](media/view-designer-conversion-tasks/sparkline.png)

Salvare le impostazioni ed eseguire nuovamente la query per aggiornare la tabella in modo da includere un grafico sparkline.

La griglia risultante sarà ![simile alla seguente: Esempio di grafico sparkline](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Impostazioni avanzate delle celle
Per eseguire il mirroring di Progettazione viste, è possibile eseguire attività quali la modifica delle dimensioni delle celle della cartella di lavoro o l'aggiunta di pin e collegamenti esterni ai log.

Per accedere a **Impostazioni avanzate,** selezionare l'icona a forma di ingranaggio nella parte inferiore di ogni cella.

![Impostazioni avanzate](media/view-designer-conversion-tasks/advanced-settings.png)

Verrà visualizzato un menu con varie opzioni:

![Impostazioni avanzate](media/view-designer-conversion-tasks/advanced-settings-settings.png)

Per aggiungere un segnaposto e un collegamento a una query esterna, selezionare le caselle di controllo corrispondenti. Per aggiungere un titolo alla cella, digitare il titolo desiderato nella sezione **Titolo grafico.**

Per impostazione predefinita, qualsiasi cella delle cartelle di lavoro è impostata per occupare l'intera larghezza della pagina, ma è possibile regolarla ridimensionando la cella verso il basso nella scheda **Stile** del menu **Impostazioni avanzate**

![Stile delle impostazioni avanzate](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>Parametri aggiuntivi
Selezionare **Aggiungi parametro** per creare un nuovo parametro nella cartella di lavoro. 

Per selezionare una sottoscrizione, digitare *Sottoscrizione* nel campo **Nome parametro** nel menu laterale e selezionare *Selezione sottoscrizione* dall'elenco a discesa Tipo **di parametro**

![Menu Abbonamento](media/view-designer-conversion-tasks/subscription-filter.png)

Per selezionare una risorsa, digitare *Risorsa* nel campo **Nome parametro** nel menu laterale e selezionare *Selezione risorse* dall'elenco a discesa Tipo **di parametro.**

![Menu Risorsa](media/view-designer-conversion-tasks/resource-filter.png)

In questo modo verranno inseriti elenchi a discesa per consentire l'accesso alle varie sottoscrizioni e risorse.

![Elenco a discesa delle risorse di sottoscrizioneSubscription Resource Dropdown](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>Passaggi successivi
- [Conversioni di riquadri](view-designer-conversion-tiles.md)
