---
title: Visualizzazioni del grafico di cartelle di lavoro di monitoraggio di Azure
description: Informazioni su tutte le visualizzazioni dei grafici di cartelle di lavoro di monitoraggio di Azure.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 139c8cdb1a227595d728d2acc0b09cf1eb210715
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90006381"
---
# <a name="chart-visualizations"></a>Visualizzazioni grafico

Le cartelle di lavoro consentono di presentare i dati di monitoraggio come grafici. I tipi di grafico supportati includono linea, barra, barra categorica, area, grafici a dispersione, torta e ora. Gli autori possono scegliere di personalizzare l'altezza, la larghezza, la tavolozza dei colori, la legenda, i titoli, i messaggi di nessun dato e così via e personalizzare i tipi di asse e i colori delle serie usando le impostazioni del grafico.

Le cartelle di lavoro supportano i grafici per i log e le origini dati delle metriche.

## <a name="log-charts"></a>Grafici di log

Log di monitoraggio di Azure offre ai proprietari delle risorse informazioni dettagliate sui lavori delle app e dell'infrastruttura. Diversamente dalle metriche, le informazioni di log non vengono raccolte per impostazione predefinita e richiedono un certo tipo di raccolta. Tuttavia, quando i log presenti forniscono una grande quantità di informazioni sullo stato della risorsa e sui dati utili per la diagnostica. Le cartelle di lavoro consentono di presentare i dati di log come grafici visivi per l'analisi degli utenti.

### <a name="adding-a-log-chart"></a>Aggiunta di un grafico di log

L'esempio seguente illustra la tendenza delle richieste a un'app nei giorni precedenti.

1. Passare alla modalità di modifica della cartella di lavoro selezionando la voce della barra degli strumenti **modifica** .
2. Usare il collegamento **Aggiungi query** per aggiungere un controllo query di log alla cartella di lavoro.
3. Selezionare il tipo di query come **log**, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per immettere il [KQL](/azure/kusto/query/) per l'analisi (ad esempio, la tendenza delle richieste).
5. Impostare la visualizzazione su uno dei seguenti elementi: **area**, a **barre**, a **barre (categorico)**, a **linee**, a **torta**, a **dispersione**o **ora**.
6. Se necessario, impostare altri parametri, ad esempio intervallo di tempo, visualizzazione, dimensioni, tavolozza dei colori e legenda.

[![Screenshot del grafico di log in modalità di modifica](./media/workbooks-chart-visualizations/log-chart.png)](./media/workbooks-chart-visualizations/log-chart.png#lightbox)

### <a name="log-chart-parameters"></a>Parametri del grafico di log

| Parametro | Spiegazione | Esempio |
| ------------- |:-------------|:-------------|
| `Query Type` | Tipo di query da utilizzare. | Log, grafico delle risorse di Azure e così via. |
| `Resource Type` | Tipo di risorsa di destinazione. | Application Insights, Log Analytics o Azure-First |
| `Resources` | Set di risorse da cui ottenere il valore della metrica. | MyApp1 |
| `Time Range` | Intervallo di tempo per visualizzare il grafico dei log. | Ultima ora, ultime 24 ore e così via. |
| `Visualization` | Visualizzazione da usare. | Area, a barre, a linee, a torta, a dispersione, ora, barra categorica |
| `Size` | Dimensione verticale del controllo. | Small, medium, large o full |
| `Color palette` | Tavolozza dei colori da utilizzare nel grafico. Ignorato in modalità multimetrica o segmentata. | Blu, verde, rosso e così via. |
| `Legend` | Funzione di aggregazione da utilizzare per la legenda. | Sum o Average of values o Max, min, First, Last value |
| `Query` | Qualsiasi query KQL che restituisce i dati nel formato previsto dalla visualizzazione del grafico. | _richieste \| make-Series richieste = Count () valore predefinito = 0 in timestamp da ago (1D) a Now () passaggio 1h_ |

### <a name="time-series-charts"></a>Grafici di serie temporali

I grafici di serie temporali come area, a barre, a linee, a dispersione e ora possono essere creati facilmente usando il controllo query nelle cartelle di lavoro di. Nel set di risultati della chiave sono presenti informazioni sul tempo e sulle metriche.

#### <a name="simple-time-series"></a>Serie temporali semplice

La query seguente restituisce una tabella con due colonne: *timestamp* e *richieste*. Il controllo query usa il *timestamp* per l'asse X e le *richieste* per l'asse Y.

```kusto
requests
| summarize Requests = count() by bin(timestamp, 1h)
```

[![Screenshot di un semplice grafico a linee di log della serie temporale.](./media/workbooks-chart-visualizations/log-chart-line-simple.png)](./media/workbooks-chart-visualizations/log-chart-line-simple.png#lightbox)

#### <a name="time-series-with-multiple-metrics"></a>Serie temporali con più metriche

La query seguente restituisce una tabella con tre colonne: *timestamp*, *richieste*e *utenti*. Il controllo query utilizza il *timestamp* per l'asse X e *richiede*  &  *agli utenti* una serie separata sull'asse Y.

```kusto
requests
| summarize Requests = count(), Users = dcount(user_Id) by bin(timestamp, 1h)
```

[![Screenshot di una serie temporale con più grafici a linee di log della metrica.](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png)](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png#lightbox)

#### <a name="segmented-time-series"></a>Serie temporale segmentata

La query seguente restituisce una tabella con tre colonne: *timestamp*, requests e *RequestName* *, dove* *RequestName* è una colonna categorica con i nomi delle richieste. Il controllo query usa il *timestamp* per l'asse X e aggiunge una serie per valore di *RequestName*.

```
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

[![Screenshot di un grafico a linee della serie temporale segmentato.](./media/workbooks-chart-visualizations/log-chart-line-segmented.png)](./media/workbooks-chart-visualizations/log-chart-line-segmented.png#lightbox)

### <a name="summarize-vs-make-series"></a>Riepilogo e serie di make

Gli esempi nella sezione precedente usano l' `summarize` operatore perché è più facile da comprendere. Tuttavia, il riepilogo presenta una limitazione importante perché omette la riga dei risultati se non sono presenti elementi nel bucket. È possibile che si stia spostando la finestra temporale del grafico a seconda che i bucket vuoti si trovino nella parte anteriore o posteriore dell'intervallo di tempo.

In genere, è preferibile usare l' `make-series` operatore per creare dati di serie temporali, con l'opzione di fornire valori predefiniti per i bucket vuoti.

Nella query seguente viene utilizzato l' `make-series` operatore.

```kusto
requests
| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h by RequestName = name
```

La query seguente mostra un grafico simile con l' `summarize` operatore

```kusto
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

Anche se il set di risultati sottostante è diverso. Tutti gli utenti devono fare in modo che la visualizzazione sia impostata su area, linea, barra o ora e le cartelle di lavoro si occupino del resto.

[![Screenshot di un grafico a linee di log creato da una query di serie](./media/workbooks-chart-visualizations/log-chart-line-make-series.png)](./media/workbooks-chart-visualizations/log-chart-line-make-series.png#lightbox)

### <a name="categorical-bar-chart-or-histogram"></a>Istogramma o grafico a barre categorico

I grafici categorici consentono agli utenti di rappresentare una dimensione o una colonna sull'asse X di un grafico. questa operazione è particolarmente utile negli istogrammi. Nell'esempio seguente viene illustrata la distribuzione delle richieste in base al relativo codice di risultato.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

La query restituisce due colonne: la metrica delle *richieste* e la categoria di *risultati* . Ogni valore della colonna *risultato* otterrà la propria barra nel grafico con altezza proporzionale alla *metrica delle richieste*.

[![Screenshot di un grafico a barre categorico per le richieste in base al codice risultato](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png)](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png#lightbox)

### <a name="pie-charts"></a>Grafici a torta

I grafici a torta consentono la visualizzazione della proporzione numerica. Nell'esempio seguente viene illustrata la proporzione di richieste in base al relativo codice di risultato.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

La query restituisce due colonne: la metrica delle *richieste* e la categoria di *risultati* . Ogni valore della colonna *risultato* otterrà una propria sezione della torta con dimensioni proporzionali alla metrica delle *richieste* .

[![Screenshot di un grafico a torta con sezioni che rappresentano il codice risultato](./media/workbooks-chart-visualizations/log-chart-pie-chart.png)](./media/workbooks-chart-visualizations/log-chart-pie-chart.png#lightbox)

## <a name="metric-charts"></a>Grafici delle metriche

La maggior parte delle risorse di Azure genera dati sulle metriche sullo stato e sull'integrità, ad esempio l'utilizzo della CPU, la disponibilità dell'archiviazione, il conteggio delle transazioni di database, le richieste di app non riuscite e così via. Le cartelle di lavoro consentono la visualizzazione dei dati come grafici di serie temporali.

### <a name="adding-a-metric-chart"></a>Aggiunta di un grafico delle metriche

Nell'esempio seguente viene mostrato il numero di transazioni in un account di archiviazione nell'ora precedente. In questo modo il proprietario dell'archiviazione potrà visualizzare la tendenza delle transazioni e cercare eventuali anomalie nel comportamento.

1. Passare alla modalità di modifica della cartella di lavoro selezionando la voce della barra degli strumenti **modifica** .
2. Usare il collegamento **Aggiungi metrica** per aggiungere un controllo metrica alla cartella di lavoro.
3. Selezionare un tipo di risorsa, ad esempio account di archiviazione, le risorse di destinazione, lo spazio dei nomi e il nome della metrica e l'aggregazione da usare.
4. Se necessario, impostare altri parametri, ad esempio intervallo di tempo, Split-by, visualizzazione, dimensioni e tavolozza dei colori.

[![Screenshot del grafico delle metriche in modalità di modifica](./media/workbooks-chart-visualizations/metric-chart.png)](./media/workbooks-chart-visualizations/metric-chart.png#lightbox)

### <a name="metric-chart-parameters"></a>Parametri del grafico delle metriche

| Parametro | Spiegazione | Esempio |
| ------------- |:-------------|:-------------|
| `Resource Type` | Tipo di risorsa di destinazione. | Archiviazione o macchina virtuale. |
| `Resources` | Set di risorse da cui ottenere il valore della metrica. | MyStorage1 |
| `Namespace` | Spazio dei nomi con la metrica. | BLOB > archiviazione |
| `Metric` | Metrica da visualizzare. | Archiviazione > BLOB > transazioni |
| `Aggregation` | Funzione di aggregazione da applicare alla metrica. | Somma, conteggio, media e così via. |
| `Time Range` | Intervallo di tempo in cui visualizzare la metrica. | Ultima ora, ultime 24 ore e così via. |
| `Visualization` | Visualizzazione da usare. | Area, a barre, a linee, a dispersione, a griglia |
| `Split By` | Suddivide facoltativamente la metrica in una dimensione. | Transazioni per tipo geografico |
| `Size` | Dimensione verticale del controllo. | Small, medium o large |
| `Color palette` | Tavolozza dei colori da utilizzare nel grafico. Viene ignorato se `Split by` viene usato il parametro. | Blu, verde, rosso e così via. |

### <a name="examples"></a>Esempi

Transazioni divise per nome API come grafico a linee:

[![Screenshot di un grafico a linee metrica per le transazioni di archiviazione divise per nome API](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png)](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png#lightbox)

Transazioni divise per tipo di risposta come grafico a barre grande:

[![Screenshot di un grafico a barre metriche di grandi dimensioni per le transazioni di archiviazione divise per tipo di risposta](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png)](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png#lightbox)

Latenza media come grafico a dispersione:

[![Screenshot di un grafico a dispersione metrica per la latenza di archiviazione](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png)](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png#lightbox)

## <a name="chart-settings"></a>Impostazioni grafico

Gli autori possono utilizzare le impostazioni del grafico per personalizzare i campi utilizzati negli assi del grafico, le unità dell'asse, la formattazione personalizzata, gli intervalli, i comportamenti di raggruppamento, le legende e i colori delle serie.

### <a name="the-settings-tab"></a>Scheda Settings (Impostazioni)

La scheda Impostazioni controlla:

- Impostazioni dell'asse, inclusi i campi, la formattazione personalizzata che consente agli utenti di impostare la formattazione dei numeri sui valori dell'asse e sugli intervalli personalizzati.
- Impostazioni di raggruppamento, incluso il campo, i limiti prima della creazione di un gruppo "altri".
- Impostazioni legenda, tra cui la visualizzazione delle metriche (nome della serie, colori e numeri) nella parte inferiore e/o una legenda (nomi e colori delle serie).

![Screenshot delle impostazioni del grafico.](./media/workbooks-chart-visualizations/chart-settings.png)

#### <a name="custom-formatting"></a>Formattazione personalizzata

Le opzioni di formattazione numerica includono:

| Opzione di formattazione             | Spiegazione                                                                                           |
|:---------------------------- |:-------------------------------------------------------------------------------------------------------|
| `Units`                      | Unità per la colonna, diverse opzioni per percentuale, conteggi, ora, byte, conteggio/ora, byte/ora e così via. Ad esempio, l'unità per un valore 1234 può essere impostata su millisecondi e viene sottoposta a rendering come 1,234 s.                                  |
| `Style`                      | Formato in cui eseguire il rendering come decimale, valuta, percentuale.                                               |
| `Show group separator`       | Casella di controllo per visualizzare i separatori di gruppo. Esegue il rendering di 1234 come 1.234 negli Stati Uniti.                                    |
| `Minimum integer digits`     | Numero minimo di cifre intere da usare (valore predefinito 1).                                                   |
| `Minimum fractional digits`  | Numero minimo di cifre frazionarie da utilizzare (valore predefinito 0).                                                |
| `Maximum fractional digits`  | Numero massimo di cifre frazionarie da utilizzare.                                                            |
| `Minimum significant digits` | Numero minimo di cifre significative da usare (valore predefinito 1).                                               |
| `Maximum significant digits` | Numero massimo di cifre significative da usare.                                                           |

![Screenshot delle impostazioni dell'asse X.](./media/workbooks-chart-visualizations/number-format-settings.png)

### <a name="the-series-tab"></a>Scheda serie

La scheda Impostazioni serie consente di modificare le etichette e i colori visualizzati per le serie nel grafico.

- Il `Series name` campo viene usato per trovare la corrispondenza con una serie di dati e, se corrisponde, verranno visualizzate l'etichetta e il colore visualizzati.
- Il `Comment` campo è utile per gli autori di modelli, perché questo commento può essere usato dai traduttori per localizzare le etichette di visualizzazione.

![Screenshot delle impostazioni della serie.](./media/workbooks-chart-visualizations/series-settings.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come creare un [riquadro nelle cartelle di lavoro](workbooks-tile-visualizations.md)di.
- Informazioni su come creare [cartelle di lavoro interattive](workbooks-interactive.md).