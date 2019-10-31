---
title: Creare report interattivi con le visualizzazioni di cartelle di lavoro di monitoraggio di Azure | Microsoft docs
description: Informazioni su tutti i componenti delle visualizzazioni delle cartelle di lavoro di monitoraggio di Azure, tra cui testo, grafici, griglie, alberi e grafici.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d878e8b5dd49ba517f85ebb74332bc6a245c33ca
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165056"
---
# <a name="azure-monitor-workbook-visualizations"></a>Visualizzazioni delle cartelle di lavoro di monitoraggio di Azure

Le cartelle di lavoro di monitoraggio di Azure supportano una serie di stili diversi di visualizzazioni per soddisfare le esigenze di creazione di report. Questo articolo fornisce esempi di ogni tipo di visualizzazione.

## <a name="text"></a>Testo

Le cartelle di lavoro consentono agli autori di includere blocchi di testo nelle cartelle di lavoro di. Il testo può essere un'analisi umana dei dati di telemetria, informazioni che consentono agli utenti di interpretare i dati, le intestazioni di sezione e così via.

![Screenshot della tabella di testo Apdex](./media/workbooks-visualizations/apdex.png)

Il testo viene aggiunto tramite un controllo Markdown che fornisce il controllo completo della formattazione.

![Screenshot di Markdown non elaborati che compila la tabella sottoposta a rendering](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Aggiungere un controllo di testo

1. Passare alla modalità di modifica della cartella di lavoro facendo clic sull'elemento della barra degli strumenti **modifica** .
2. Utilizzare il collegamento **Aggiungi testo** per aggiungere un controllo di testo alla cartella di lavoro.
3. Aggiungere Markdown al controllo.
4. Fare clic sul pulsante **modifica completato** per visualizzare il testo formattato.

> [!TIP]
> Usare questo [foglio](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) informativo di Markdown per ottenere informazioni sulle diverse opzioni di formattazione.

## <a name="charts"></a>Grafici

Le cartelle di lavoro consentono di presentare i dati di monitoraggio come grafici. I tipi di grafico supportati includono linea, barra, barra categorica, area, grafici a dispersione, torta e ora. Gli autori possono scegliere di personalizzare l'altezza, la larghezza, la tavolozza dei colori, la legenda, i titoli, i messaggi di nessun dato e così via del grafico.

Le cartelle di lavoro supportano i grafici per i log e le origini dati delle metriche. 

### <a name="adding-a-log-chart"></a>Aggiunta di un grafico di log

1. Passare alla modalità di modifica della cartella di lavoro facendo clic sull'elemento della barra degli strumenti **modifica** .
2. Usare il collegamento **Aggiungi query** per aggiungere un controllo query di log alla cartella di lavoro.
3. Selezionare il tipo di query come **log**, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per immettere il [KQL](https://docs.microsoft.com/azure/kusto/query/) per l'analisi (ad esempio, la tendenza delle richieste).
5. Impostare la visualizzazione su uno dei seguenti elementi: **area**, a **barre**, a **barre (categorico)** , a **linee**, a **torta**, a **dispersione**o **ora**.
6. Se necessario, impostare altri parametri, ad esempio intervallo di tempo, visualizzazione, dimensioni, tavolozza dei colori e legenda.

![Screenshot del grafico di log in modalità di modifica](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Parametri del grafico di log

| Parametro | Spiegazione | Esempio |
| ------------- |:-------------|:-------------|
| `Query Type` | Tipo di query da utilizzare | Log, grafico delle risorse di Azure e così via. |
| `Resource Type` | Tipo di risorsa di destinazione | Application Insights, Log Analytics o Azure-First |
| `Resources` | Set di risorse da cui ottenere il valore della metrica | MyApp1 |
| `Time Range` | Intervallo di tempo per visualizzare il grafico dei log | Ultima ora, ultime 24 ore e così via. |
| `Visualization` | Visualizzazione da usare | Area, a barre, a linee, a torta, a dispersione, ora, barra categorica |
| `Size` | Dimensioni verticali del controllo | Small, medium, large o full |
| `Color palette` | Tavolozza dei colori da utilizzare nel grafico. Ignorato in modalità multimetrica o segmentata. | Blu, verde, rosso e così via. |
| `Legend` | Funzione di aggregazione da utilizzare per la legenda | Sum o Average of values o Max, min, First, Last value |
| `Query` | Qualsiasi query KQL che restituisce i dati nel formato previsto dalla visualizzazione del grafico | _richieste \| richieste della serie make = Count () default = 0 in timestamp da ago (1D) a Now () Step 1h_ |

### <a name="adding-a-metric-chart"></a>Aggiunta di un grafico delle metriche

1. Passare alla modalità di modifica della cartella di lavoro facendo clic sull'elemento della barra degli strumenti **modifica** .
2. Usare il collegamento **Aggiungi metrica** per aggiungere un controllo metrica alla cartella di lavoro.
3. Selezionare un tipo di risorsa, ad esempio account di archiviazione, le risorse di destinazione, lo spazio dei nomi e il nome della metrica e l'aggregazione da usare.
4. Se necessario, impostare altri parametri, ad esempio intervallo di tempo, Split-by, visualizzazione, dimensioni e tavolozza dei colori.

![Screenshot del grafico delle metriche in modalità di modifica](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Parametri del grafico delle metriche

| Parametro | Spiegazione | Esempio |
| ------------- |:-------------|:-------------|
| `Resource Type` | Tipo di risorsa di destinazione | Archiviazione o macchina virtuale. |
| `Resources` | Set di risorse da cui ottenere il valore della metrica | MyStorage1 |
| `Namespace` | Spazio dei nomi con la metrica | BLOB > archiviazione |
| `Metric` | Metrica da visualizzare | Archiviazione > BLOB > transazioni |
| `Aggregation` | Funzione di aggregazione da applicare alla metrica | Somma, conteggio, media e così via. |
| `Time Range` | Intervallo di tempo in cui visualizzare la metrica | Ultima ora, ultime 24 ore e così via. |
| `Visualization` | Visualizzazione da usare | Area, a barre, a linee, a dispersione, a griglia |
| `Split By` | Suddividere facoltativamente la metrica in una dimensione | Transazioni per tipo geografico |
| `Size` | Dimensioni verticali del controllo | Small, medium o large |
| `Color palette` | Tavolozza dei colori da utilizzare nel grafico. Viene ignorato se viene usato il parametro `Split by` | Blu, verde, rosso e così via. |

## <a name="grids"></a>Griglie

Le griglie o le tabelle sono un modo comune per presentare i dati agli utenti. Le cartelle di lavoro consentono agli utenti di applicare stili singolarmente alle colonne della griglia per fornire un'interfaccia utente avanzata per i report.

Nell'esempio seguente viene illustrata una griglia che combina icone, Heatmaps e barre Spark per presentare informazioni complesse. La cartella di lavoro fornisce anche l'ordinamento, una casella di ricerca e un pulsante Vai a analisi.

![Screenshot della griglia basata su log](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Aggiunta di una griglia basata su log

1. Passare alla modalità di modifica della cartella di lavoro facendo clic sull'elemento della barra degli strumenti **modifica** .
2. Usare il collegamento **Aggiungi query** per aggiungere un controllo query di log alla cartella di lavoro.
3. Selezionare il tipo di query come **log**, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Usare l'editor di query per immettere il KQL per l'analisi, ad esempio le VM con memoria al di sotto di una soglia.
5. Impostare la visualizzazione su **griglia**
6. Se necessario, impostare altri parametri, ad esempio intervallo di tempo, dimensioni, tavolozza dei colori e legenda.

![Screenshot della query sulla griglia basata su log](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Riquadri

I riquadri sono un modo molto utile per presentare i dati di riepilogo nelle cartelle di lavoro di. L'immagine seguente mostra un caso d'uso comune dei riquadri-riepilogo a livello di app sopra una griglia dettagliata.

![Screenshot della visualizzazione Riepilogo dei riquadri](./media/workbooks-visualizations/tiles-summary.png)

Il supporto dei riquadri della cartella di lavoro mostra un titolo, un sottotitolo, testo di grandi dimensioni, icone, sfumature basate su metriche, linee/barre Spark, piè di pagina e così via.

### <a name="adding-a-tile"></a>Aggiunta di un riquadro

1. Passare alla modalità di modifica della cartella di lavoro facendo clic sull'elemento della barra degli strumenti _modifica_ .
2. Usare il collegamento **Aggiungi query** per aggiungere un controllo query di log alla cartella di lavoro. 
3. Selezionare il tipo di query come **log**, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per immettere il KQL per l'analisi
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. Imposta dimensioni su **pieno**
6. Impostare la visualizzazione sui **riquadri**
7. Fare clic sul pulsante **Impostazioni riquadro** per aprire il riquadro Impostazioni.
8. In **campi del riquadro**, impostare:
    * Titolo: `name`
    * Left: `Requests`, renderer: `Big Number`, tavolozza colori: `Green to Red`, valore minimo: `0`
    * In basso: `appName`
9. Fare clic sul pulsante **Salva e Chiudi** nella parte inferiore del riquadro.

![Screenshot della visualizzazione Riepilogo dei riquadri](./media/workbooks-visualizations/tile-settings.png)

Questo è il modo in cui i riquadri appariranno in modalità di lettura:

![Screenshot della visualizzazione Riepilogo dei riquadri](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Alberi

Le cartelle di lavoro supportano le visualizzazioni gerarchiche tramite griglie di albero. Gli alberi consentono di espandere alcune righe nel livello successivo per un'esperienza di drill-down.

Nell'esempio seguente vengono illustrate le metriche di integrità del contenitore (dimensioni working set) visualizzate come griglia ad albero. I nodi di livello superiore sono i nodi del servizio Azure Kubernetes (AKS), il livello successivo sono i pod e il livello finale sono i contenitori. Si noti che è comunque possibile formattare le colonne come in una griglia (mappa termica, icons, link). In questo caso, l'origine dati sottostante è un'area di lavoro Log Analytics con log AKS.

![Screenshot della visualizzazione Riepilogo dei riquadri](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Aggiunta di una griglia ad albero
1. Passare alla modalità di modifica della cartella di lavoro facendo clic sull'elemento della barra degli strumenti _modifica_ .
2. Usare il collegamento **Aggiungi query** per aggiungere un controllo query di log alla cartella di lavoro. 
3. Selezionare il tipo di query come **log**, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per immettere il KQL per l'analisi
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Impostare la visualizzazione su **griglia**
6. Fare clic sul pulsante **Impostazioni colonna** per aprire il riquadro Impostazioni.
7. Nella sezione **Impostazioni albero/raggruppamento** in basso, impostare:
    * Tipo di albero: `Parent/Child`
    * Campo ID: `Id`
    * Campo ID padre: `ParentId`
    * Mostra espansore su: `Name`
    * Espandere il livello superiore della struttura ad albero: `checked`
8. Nella parte superiore della sezione _colonne_ impostare:
    * Renderer della colonna _ID_ : `Hidden`
    * Renderer della colonna _ID padre_ : `Hidden`
    * _Richieste_ -renderer di colonna: `Bar`, colore: `Blue`, valore minimo: `0`
9. Fare clic sul pulsante _Salva e Chiudi_ nella parte inferiore del riquadro.    

![Screenshot della visualizzazione Riepilogo dei riquadri](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Impostazioni albero

| Impostazione | Spiegazione |
|:------------- |:-------------|
| `Id Field` | ID univoco di ogni riga della griglia |
| `Parent Id Field` | ID dell'elemento padre della riga corrente. |
| `Show the expander on` | Colonna in cui visualizzare l'espansore della struttura ad albero. È normale che le griglie dell'albero nascondano il proprio ID e il campo ID padre perché non sono leggibili. Al contrario, l'espansore viene visualizzato in un campo con un valore più leggibile, ad esempio il nome dell'entità |
| `Expand the top level of the tree` | Se questa opzione è selezionata, la griglia dell'albero verrà espansa al livello superiore. Utile se si desidera visualizzare ulteriori informazioni per impostazione predefinita |

## <a name="graphs"></a>Grafici

Le cartelle di lavoro supportano la visualizzazione di grafici arbitrari basati sui dati dei log per mostrare le relazioni tra le entità di monitoraggio.

Il grafico riportato di seguito mostra il flusso di dati all'interno o all'esterno di un computer tramite varie porte da e verso computer esterni. È colorato in base al tipo (computer rispetto a porta e IP esterno) e le dimensioni dei bordi corrispondono alla quantità di dati che scorrono tra loro. I dati sottostanti provengono dalla query KQL destinata alle connessioni VM.

![Screenshot della visualizzazione Riepilogo dei riquadri](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Aggiunta di un grafico
1. Passare alla modalità di modifica della cartella di lavoro facendo clic sull'elemento della barra degli strumenti _modifica_ .
2. Usare il collegamento **Aggiungi query** per aggiungere un controllo query di log alla cartella di lavoro. 
3. Selezionare il tipo di query come **log**, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per immettere il KQL per l'analisi
    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```
7. Impostare la visualizzazione su **Graph**
8. Fare clic sul pulsante **Impostazioni grafico** per aprire il riquadro Impostazioni.
9. In _campi layout_ nella parte inferiore, impostare:
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. In _Impostazioni formato nodo_ nella parte superiore impostare:
    * Colonna content-use _principale_: `Name`, renderer di colonna: `Text`
    * Colonna content-use di _Center_: `Calls`, renderer di colonna: `Big Number`, tavolozza dei colori: `None`
    * Colonna contenuto-uso _inferiore_: `Kind`, renderer di colonna: `Text`
10. Fare clic sul pulsante _Salva e Chiudi_ nella parte inferiore del riquadro.

![Screenshot della visualizzazione Riepilogo dei riquadri](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire](workbooks-automate.md) cartelle di lavoro con Azure Resource Manager.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.