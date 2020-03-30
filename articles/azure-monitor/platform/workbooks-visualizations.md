---
title: Visualizzazioni delle cartelle di lavoro di Monitoraggio di AzureAzure Monitor workbook visualizations
description: Informazioni su tutti i componenti delle visualizzazioni delle cartelle di lavoro di Monitoraggio di Azure, tra cui testo, grafici, griglie, alberi e grafici.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658031"
---
# <a name="azure-monitor-workbook-visualizations"></a>Visualizzazioni delle cartelle di lavoro di Monitoraggio di AzureAzure Monitor workbook visualizations

Le cartelle di lavoro di Monitoraggio di Azure supportano diversi stili di visualizzazioni per soddisfare le esigenze di creazione di report. In questo articolo vengono forniti esempi di ogni tipo di visualizzazione.

## <a name="text"></a>Testo

Le cartelle di lavoro consentono agli autori di includere blocchi di testo nelle cartelle di lavoro. Il testo può essere un'analisi umana della telemetria, informazioni per aiutare gli utenti a interpretare i dati, le intestazioni di sezione e così via.

![Screenshot della tabella di testo DiApdex](./media/workbooks-visualizations/apdex.png)

Il testo viene aggiunto tramite un controllo Markdown che fornisce il controllo di formattazione completo.

![Screenshot del markdown non elaborato che crea la tabella di cui è stato eseguito il rendering](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Aggiungere un controllo di testoAdd a text control

1. Impostare la modalità di modifica della cartella di lavoro facendo clic sull'elemento **modifica** della barra degli strumenti.
2. Utilizzare il collegamento **Aggiungi testo** per aggiungere un controllo di testo alla cartella di lavoro.
3. Aggiungere Markdown al controllo.
4. Fare clic sul pulsante **Modifica completata** per visualizzare il testo formattato.

> [!TIP]
> Utilizzare questo [foglio di trucco Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) per informazioni sulle diverse opzioni di formattazione.

## <a name="charts"></a>Grafici

Le cartelle di lavoro consentono di presentare i dati di monitoraggio come grafici. I tipi di grafico supportati includono linea, barra, barra classificata, area, grafici a dispersione, torta e ora. Gli autori possono scegliere di personalizzare l'altezza, la larghezza, la tavolozza dei colori, la legenda, i titoli, il messaggio senza dati e così via del grafico.

Le cartelle di lavoro supportano grafici sia per i log che per le origini dati delle metriche. 

### <a name="adding-a-log-chart"></a>Aggiunta di un grafico a log

1. Impostare la modalità di modifica della cartella di lavoro facendo clic sull'elemento **modifica** della barra degli strumenti.
2. Utilizzare il collegamento **Aggiungi query** per aggiungere un controllo query di log alla cartella di lavoro.
3. Selezionare il tipo di query come **Log**, tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per immettere il [KQL](https://docs.microsoft.com/azure/kusto/query/) per l'analisi (ad esempio, l'andamento delle richieste).
5. Impostare la visualizzazione su uno dei seguenti: **Area**, **Barra**, **Barra (categoria)**, **Linea**, **Torta**, **Dispersione**o **Ora**.
6. Se necessario, imposta altri parametri, come l'intervallo di tempo, la visualizzazione, le dimensioni, la tavolozza dei colori e la legenda.

![Screenshot del grafico di registro in modalità di modifica](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Parametri del grafico di log

| Parametro | Spiegazione | Esempio |
| ------------- |:-------------|:-------------|
| `Query Type` | Tipo di query da utilizzare | Log, Azure Resource Graph e così via. |
| `Resource Type` | Tipo di risorsa di destinazione | Application Insights, Log Analytics o Azure-first |
| `Resources` | Un set di risorse per ottenere il valore delle metriche | MyApp1 |
| `Time Range` | L'intervallo di tempo per visualizzare il grafico di log | Ultima ora, Ultime 24 ore, ecc. |
| `Visualization` | Visualizzazione da utilizzare | Area, Barra, Linea, Torta, Dispersione, Tempo, Barra categorica |
| `Size` | La dimensione verticale del controllo | Piccola, media, grande o piena |
| `Color palette` | Tavolozza dei colori da utilizzare nel grafico. Ignorato in modalità multimetrica o segmentata. | Blu, verde, rosso, ecc. |
| `Legend` | Funzione di aggregazione da utilizzare per la legenda | Somma o Media dei valori o Max, Min, Primo, Ultimo valore |
| `Query` | Qualsiasi query KQL che restituisce i dati nel formato previsto dalla visualizzazione del grafico | _richieste \| make-series Richieste : count() default : 0 su timestamp da fa(1d) a now() passo 1h_ |

### <a name="adding-a-metric-chart"></a>Aggiunta di un grafico metrico

1. Impostare la modalità di modifica della cartella di lavoro facendo clic sull'elemento **modifica** della barra degli strumenti.
2. Utilizzare il collegamento **Aggiungi metrica** per aggiungere un controllo metrico alla cartella di lavoro.
3. Selezionare un tipo di risorsa, ad esempio Account di archiviazione, le risorse di destinazione, lo spazio dei nomi e il nome della metrica e l'aggregazione da usare.
4. Se necessario, impostare altri parametri, ad esempio intervallo di tempo, divisione, visualizzazione, dimensioni e tavolozza dei colori.

![Screenshot del grafico metrico in modalità di modifica](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Parametri del grafico metrico

| Parametro | Spiegazione | Esempio |
| ------------- |:-------------|:-------------|
| `Resource Type` | Tipo di risorsa di destinazione | Archiviazione o macchina virtuale. |
| `Resources` | Un set di risorse per ottenere il valore delle metriche | Memoriapersonale1 |
| `Namespace` | Lo spazio dei nomi con la metrica | Archiviazione > BLOBStorage > Blob |
| `Metric` | La metrica per visualizzare | Storage > Blob > Transactions |
| `Aggregation` | La funzione di aggregazione da applicare alla metrica | Somma, Conteggio, Media, ecc. |
| `Time Range` | L'intervallo di tempo per visualizzare la metrica in | Ultima ora, Ultime 24 ore, ecc. |
| `Visualization` | Visualizzazione da utilizzare | Area, Barra, Linea, Dispersione, Griglia |
| `Split By` | Facoltativamente, dividere la metrica in una dimensione | Transazioni per tipo di Geo |
| `Size` | La dimensione verticale del controllo | Piccola, media o grande |
| `Color palette` | Tavolozza dei colori da utilizzare nel grafico. Ignorato `Split by` se il parametro viene utilizzato | Blu, verde, rosso, ecc. |

## <a name="grids"></a>Griglie

Le griglie o le tabelle sono un modo comune per presentare i dati agli utenti. Le cartelle di lavoro consentono agli utenti di applicare uno stile individuale alle colonne della griglia per fornire un'interfaccia utente avanzata per i report.

L'esempio seguente mostra una griglia che combina icone, mappe di calore e barre di scintilla per presentare informazioni complesse. La cartella di lavoro fornisce anche l'ordinamento, una casella di ricerca e un pulsante Go-to-analytics.

![Schermata della griglia basata su log](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Aggiunta di una griglia basata su log

1. Impostare la modalità di modifica della cartella di lavoro facendo clic sull'elemento **modifica** della barra degli strumenti.
2. Utilizzare il collegamento **Aggiungi query** per aggiungere un controllo query di log alla cartella di lavoro.
3. Selezionare il tipo di query come **Log**, tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Usare l'editor di query per immettere il KQL per l'analisi (ad esempio, macchine virtuali con memoria inferiore a una soglia)
5. Impostare la visualizzazione su **Griglia**
6. Impostare altri parametri se necessario - come intervallo di tempo, dimensioni, tavolozza dei colori e legenda.

![Screenshot della query griglia basata su log](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Riquadri

I riquadri sono un modo molto utile per presentare i dati di riepilogo nelle cartelle di lavoro. L'immagine seguente mostra un caso d'uso comune dei riquadri - Riepilogo a livello di app sopra una griglia dettagliata.

![Screenshot della visualizzazione Riepilogo riquadri](./media/workbooks-visualizations/tiles-summary.png)

I riquadri della cartella di lavoro supportano la visualizzazione di un titolo, un sottotitolo, testo di grandi dimensioni, icone, sfumature basate su metriche, linee di spark/barre, piè di pagina e così via.

### <a name="adding-a-tile"></a>Aggiunta di un riquadro

1. Impostare la modalità di modifica della cartella di lavoro facendo clic sull'elemento _modifica_ della barra degli strumenti.
2. Utilizzare il collegamento **Aggiungi query** per aggiungere un controllo query di log alla cartella di lavoro. 
3. Selezionare il tipo di query come **Log**, tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per accedere al KQL per l'analisi
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. Impostare Dimensione su **Completo**
6. Impostare la visualizzazione su **Riquadri**
7. Fare clic sul pulsante **Impostazioni riquadro** per aprire il riquadro delle impostazioni
8. In **Campi affiancato**, impostare:
    * Titolo: `name`
    * Sinistra: `Requests`, Renderer: `Big Number`, `Green to Red`Tavolozza colori: , Valore minimo:`0`
    * Fondoschiena:`appName`
9. Fare clic sul pulsante **Salva e chiudi** nella parte inferiore del riquadro.

![Screenshot della visualizzazione Riepilogo riquadri](./media/workbooks-visualizations/tile-settings.png)

Questo è come i piastrelle apparirà in modalità di lettura:

![Screenshot della visualizzazione Riepilogo riquadri](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Trees

Le cartelle di lavoro supportano visualizzazioni gerarchiche tramite griglie ad albero. Gli alberi consentono ad alcune righe di essere espandibili nel livello successivo per un'esperienza di drill-down.

L'esempio seguente mostra le metriche di integrità del contenitore (dimensioni del working set) visualizzate come una griglia ad albero. I nodi di primo livello sono nodi azure Kubernetes Service (AKS), il livello successivo sono pod e il livello finale sono contenitori. Si noti che è comunque possibile formattare le colonne come in una griglia (heatmap, icone, collegamento). L'origine dati sottostante in questo caso è un'area di lavoro di Log Analytics con log AKS.

![Screenshot della visualizzazione Riepilogo riquadri](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Aggiunta di una griglia ad albero
1. Impostare la modalità di modifica della cartella di lavoro facendo clic sull'elemento _modifica_ della barra degli strumenti.
2. Utilizzare il collegamento **Aggiungi query** per aggiungere un controllo query di log alla cartella di lavoro. 
3. Selezionare il tipo di query come **Log**, tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per accedere al KQL per l'analisi
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
5. Impostare la visualizzazione su **Griglia**
6. Fare clic sul pulsante **Impostazioni colonna** per aprire il riquadro delle impostazioni
7. Nella sezione **Albero/Raggruppa per impostazioni** in basso, imposta:
    * Tipo di albero:`Parent/Child`
    * Id Campo:`Id`
    * Campo ID padre:`ParentId`
    * Mostra l'espanditore su:`Name`
    * Espandere il livello superiore dell'albero:`checked`
8. Nella sezione _Colonne_ nella parte superiore, imposta:
    * _Id_ - Rendering colonne:`Hidden`
    * _ID padre_ - Renderer colonna:`Hidden`
    * _Richieste_ - Rendering `Bar`colonne: `Blue`, Colore: , Valore minimo:`0`
9. Fare clic sul pulsante _Salva e chiudi_ nella parte inferiore del riquadro.    

![Screenshot della visualizzazione Riepilogo riquadri](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Impostazioni albero

| Impostazione | Spiegazione |
|:------------- |:-------------|
| `Id Field` | ID univoco di ogni riga della griglia |
| `Parent Id Field` | Id dell'elemento padre della riga corrente |
| `Show the expander on` | Colonna in cui visualizzare l'espansore dell'albero. È comune per le griglie ad albero nascondere il campo id e id padre perché non sono molto leggibili. Al contrario, l'espansore viene visualizzato in un campo con un valore più leggibile, ad esempio il nome dell'entità |
| `Expand the top level of the tree` | Se selezionata, la griglia della struttura verrà espansa al livello superiore. Utile se si desidera visualizzare ulteriori informazioni per impostazione predefinita |

## <a name="graphs"></a>Grafici

Le cartelle di lavoro supportano la visualizzazione di grafici arbitrari in base ai dati dei log per mostrare le relazioni tra le entità di monitoraggio.

Il grafico seguente mostra i dati che scorrono in/fuori da un computer tramite varie porte da/verso computer esterni. È colorato per tipo (computer vs porta rispetto all'IP esterno) e le dimensioni del bordo corrispondono alla quantità di dati che scorrono nel mezzo. I dati sottostanti provengono da query KQL destinate a connessioni VM.

![Screenshot della visualizzazione Riepilogo riquadri](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Aggiunta di un grafico
1. Impostare la modalità di modifica della cartella di lavoro facendo clic sull'elemento _modifica_ della barra degli strumenti.
2. Utilizzare il collegamento **Aggiungi query** per aggiungere un controllo query di log alla cartella di lavoro. 
3. Selezionare il tipo di query come **Log**, tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per accedere al KQL per l'analisi
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
7. Impostare la visualizzazione **su Grafico**
8. Fare clic sul pulsante **Impostazioni grafico** per aprire il riquadro delle impostazioni
9. In _Campi layout_ nella parte inferiore, impostare:
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. In Impostazioni formato nodo nella parte superiore, impostare:In _Node Format Settings_ at the top, set:
    * _Contenuto superiore_- `Name`Usa colonna: , Rendering delle colonne:`Text`
    * _Contenuto centrale_- `Calls`Usa colonna: `Big Number`, Rendering colonne: , Tavolozza colori:`None`
    * _Contenuto inferiore_- `Kind`Usa colonna: , Rendering delle colonne:`Text`
10. Fare clic sul pulsante _Salva e chiudi_ nella parte inferiore del riquadro.

![Screenshot della visualizzazione Riepilogo riquadri](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire](workbooks-automate.md) cartelle di lavoro con Azure Resource Manager.Deploy workbooks with Azure Resource Manager.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
