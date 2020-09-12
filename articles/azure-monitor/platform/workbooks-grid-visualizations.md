---
title: Visualizzazioni griglia cartella di lavoro di monitoraggio di Azure
description: Informazioni su tutte le visualizzazioni della griglia delle cartelle di lavoro di monitoraggio di Azure.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 114245030ba2356861b83a580f103d3b84c0eae3
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664279"
---
# <a name="grid-visualizations"></a>Visualizzazioni griglia

Le griglie o le tabelle sono un modo comune per presentare i dati agli utenti. Le cartelle di lavoro consentono agli utenti di applicare stili singolarmente alle colonne della griglia per fornire un'interfaccia utente avanzata per i report.

Nell'esempio seguente viene illustrata una griglia che combina icone, Heatmaps e barre Spark per presentare informazioni complesse. La cartella di lavoro fornisce anche l'ordinamento, una casella di ricerca e un pulsante Vai a analisi.

[![Screenshot della griglia basata su log](./media/workbooks-grid-visualizations/grid.png)](./media/workbooks-grid-visualizations/grid.png#lightbox)

## <a name="adding-a-log-based-grid"></a>Aggiunta di una griglia basata su log

1. Impostare la cartella di lavoro in modalità di modifica facendo clic sulla voce **Modifica** sulla barra degli strumenti.
2. Usare il collegamento **Aggiungi query** per aggiungere un controllo query di log alla cartella di lavoro.
3. Selezionare il tipo di query come **log**, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Usare l'editor di query per immettere il KQL per l'analisi, ad esempio le VM con memoria al di sotto di una soglia.
5. Impostare la visualizzazione su **griglia**
6. Se necessario, impostare altri parametri, ad esempio intervallo di tempo, dimensioni, tavolozza dei colori e legenda.

[![Screenshot della query sulla griglia basata su log](./media/workbooks-grid-visualizations/grid-query.png)](./media/workbooks-grid-visualizations/grid-query.png#lightbox)

## <a name="log-chart-parameters"></a>Parametri del grafico di log

| Parametro | Spiegazione | Esempio |
| ------------- |:-------------|:-------------|
| `Query Type` | Tipo di query da utilizzare. | Log, grafico delle risorse di Azure e così via. |
| `Resource Type` | Tipo di risorsa di destinazione. | Application Insights, Log Analytics o Azure-First |
| `Resources` | Set di risorse da cui ottenere il valore della metrica. | MyApp1 |
| `Time Range` | Intervallo di tempo per visualizzare il grafico dei log. | Ultima ora, ultime 24 ore e così via. |
| `Visualization` | Visualizzazione da usare. | Pannello Grid |
| `Size` | Dimensione verticale del controllo. | Small, medium, large o full |
| `Query` | Qualsiasi query KQL che restituisce i dati nel formato previsto dalla visualizzazione del grafico. | _richieste \| riepiloga richieste = conteggio () per nome_ |

## <a name="simple-grid"></a>Griglia semplice

Le cartelle di lavoro di possono eseguire il rendering dei risultati di KQL come tabella semplice. La griglia seguente mostra il numero di richieste e di utenti univoci per ogni tipo di richiesta in un'app.

```kusto
requests
| where name !endswith('.eot')
| summarize Requests = count(), Users = dcount(user_Id) by name
| order by Requests desc
```

[![Screenshot di una griglia basata su log in modalità di modifica](./media/workbooks-grid-visualizations/log-chart-simple-grid.png)](./media/workbooks-grid-visualizations/log-chart-simple-grid.png#lightbox)

## <a name="grid-styling"></a>Stile griglia

Mentre una tabella normale Mostra i dati, è difficile da leggere e le informazioni dettagliate non sono sempre evidenti. Lo stile della griglia consente di semplificare la lettura e l'interpretazione dei dati.

Di seguito è riportata la stessa griglia della sezione precedente con lo stile Heatmaps.

[![Screenshot di una griglia basata su log con colonne con stile Heatmaps](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png)](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png#lightbox)

Di seguito è riportata la stessa griglia con lo stile barre: [ ![ screenshot di una griglia basata su log con colonne con stile come barre](./media/workbooks-grid-visualizations/log-chart-grid-bar.png)](./media/workbooks-grid-visualizations/log-chart-grid-bar.png#lightbox)

### <a name="styling-a-grid-column"></a>Applicazione di stili a una colonna della griglia

1. Selezionare il pulsante **impostazione colonna** sulla barra degli strumenti del controllo query.
2. Nelle *Impostazioni modifica colonna*selezionare la colonna di cui applicare lo stile.
3. Scegliere un renderer di colonna (ad esempio mappa termica, bar, barra sotto e così via) e le impostazioni correlate per applicare uno stile alla colonna.

Di seguito è riportato un esempio che designa la colonna *Request* come una barra:

[![Screenshot di una griglia basata su log con una colonna richiesta con stile come barra.](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png)](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png#lightbox)

### <a name="column-renderers"></a>Renderer di colonna

| Renderer di colonna | Spiegazione | Opzioni aggiuntive |
|:------------- |:-------------|:-------------|
| `Automatic` | Il valore predefinito usa il renderer più appropriato in base al tipo di colonna.  |  |
| `Text` | Esegue il rendering dei valori di colonna come testo. | |
| `Right Aligned` | Simile al testo ad eccezione del fatto che è allineato a destra. | |
| `Date/Time` | Esegue il rendering di una stringa di data e ora leggibile. | |
| `Heatmap` | Colora le celle della griglia in base al valore della cella. | Tavolozza dei colori e valore min/max usato per la scalabilità. |
| `Bar` | Esegue il rendering di una barra accanto alla cella in base al valore della cella. | Tavolozza dei colori e valore min/max usato per la scalabilità. |
| `Bar underneath` | Esegue il rendering di una barra nella parte inferiore della cella in base al valore della cella. | Tavolozza dei colori e valore min/max usato per la scalabilità. |
| `Composite bar` | Esegue il rendering di una barra composita utilizzando le colonne specificate nella riga. Per informazioni dettagliate, vedere la [barra composita](workbooks-composite-bar.md) . | Colonne con colori corrispondenti per il rendering della barra e un'etichetta da visualizzare nella parte superiore della barra. |
| `Spark bars` | Esegue il rendering di una barra Spark nella cella in base ai valori di una matrice dinamica nella cella. Ad esempio, la colonna tendenza dello screenshot nella parte superiore. | Tavolozza dei colori e valore min/max usato per la scalabilità. |
| `Spark lines` | Esegue il rendering di una linea Spark nella cella in base ai valori di una matrice dinamica nella cella. | Tavolozza dei colori e valore min/max usato per la scalabilità. |
| `Icon` | Esegue il rendering delle icone in base ai valori di testo nella cella. I valori supportati sono: `cancelled` ,, `critical` `disabled` ,,, `error` `failed` `info` , `none` , `pending` , `stopped` , `question` , `success` , `unknown` , `warning` `uninitialized` , `resource` , `up` , `down` , `left` , `right` ,,,,,,,,,, `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` , `Sev4` , `Fired` , `Resolved` , `Available` ,,, `Unavailable` `Degraded` `Unknown` e `Blank` .|  |
| `Link` | Esegue il rendering di un collegamento che, quando si fa clic o esegue un'azione configurabile. Usare questa opzione se si vuole che l'elemento sia *solo* un collegamento.  Tutti gli altri tipi possono *anche* essere un collegamento usando l' `Make this item a link` impostazione. Per altre informazioni, vedere [azioni di collegamento](#link-actions) di seguito. |  |
| `Location` | Esegue il rendering di un nome descrittivo dell'area di Azure in base agli ID di un'area. |  |
| `Resource type` | Esegue il rendering di una stringa di tipo di risorsa intuitiva in base a un ID del tipo di risorsa  |  |
| `Resource` | Esegue il rendering di un nome descrittivo della risorsa e un collegamento in base a un ID risorsa  | Opzione per visualizzare l'icona del tipo di risorsa  |
| `Resource group` | Esegue il rendering di un nome e collegamento del gruppo di risorse descrittivo in base a un ID gruppo di risorse. Se il valore della cella non è un gruppo di risorse, verrà convertito in uno.  | Opzione per visualizzare l'icona del gruppo di risorse  |
| `Subscription` | Esegue il rendering di un nome descrittivo della sottoscrizione e un collegamento in base a un ID sottoscrizione.  Se il valore della cella non è una sottoscrizione, verrà convertito in uno.  | Opzione per visualizzare l'icona della sottoscrizione.  |
| `Hidden` | Nasconde la colonna nella griglia. Utile quando la query predefinita restituisce un numero di colonne maggiore di quello necessario, ma non è richiesto un progetto. |  |

### <a name="link-actions"></a>Azioni di collegamento

Se il `Link` renderer è selezionato o la casella *di controllo imposta questo elemento come collegamento* è selezionata, l'autore può configurare un'azione di collegamento che verrà eseguita durante la selezione della cella. Questo in genere porta l'utente in un'altra visualizzazione con contesto proveniente dalla cella o può aprire un URL.

### <a name="custom-formatting"></a>Formattazione personalizzata

Le cartelle di lavoro consentono inoltre agli utenti di impostare la formattazione dei numeri dei valori di cella. Questa operazione può essere eseguita facendo clic sulla casella di controllo *formattazione personalizzata* , quando disponibile.

| Opzione di formattazione | Spiegazione |
|:------------- |:-------------|
| `Units` | Unità per la colonna, diverse opzioni per percentuale, conteggi, ora, byte, conteggio/ora, byte/ora e così via. Ad esempio, l'unità per un valore di 1234 può essere impostata su millisecondi e ne viene eseguito il rendering come 1,234 s. |
| `Style` | Formato in cui eseguire il rendering come decimale, valuta, percentuale. |
| `Show group separator` | Casella di controllo per visualizzare i separatori di gruppo. Esegue il rendering di 1234 come 1.234 negli Stati Uniti. |
| `Minimum integer digits` | Numero minimo di cifre intere da usare (valore predefinito 1). |
| `Minimum fractional digits` | Numero minimo di cifre frazionarie da utilizzare (valore predefinito 0).  |
| `Maximum fractional digits` | Numero massimo di cifre frazionarie da utilizzare. |
| `Minimum significant digits` | Numero minimo di cifre significative da usare (valore predefinito 1). |
| `Maximum significant digits` | Numero massimo di cifre significative da usare. |
| `Custom text for missing values` | Quando un punto dati non ha un valore, Mostra questo testo personalizzato anziché uno spazio vuoto. |

### <a name="custom-date-formatting"></a>Formattazione data personalizzata

Quando l'autore ha specificato che una colonna è impostata sul renderer di data/ora, l'autore può specificare le opzioni di formattazione della data personalizzate utilizzando la casella di controllo *formattazione data personalizzata* .

| Opzione di formattazione | Spiegazione |
|:------------- |:-------------|
| `Style` | Formato in cui eseguire il rendering di una data come formati brevi, lunghi, completi o con tempi brevi o lunghi. |
| `Show time as` | Consente all'autore di decidere tra la visualizzazione dell'ora locale (impostazione predefinita) o l'ora UTC. A seconda dello stile del formato della data selezionato, è possibile che le informazioni sul fuso orario e UTC non vengano visualizzate. |

## <a name="custom-column-width-setting"></a>Impostazione larghezza colonna personalizzata

L'autore può personalizzare la larghezza di qualsiasi colonna nella griglia utilizzando il campo *larghezza colonna personalizzata* nelle *Impostazioni colonna*.

![Screenshot delle impostazioni delle colonne con il campo della larghezza della colonna personalizzata indicato in una casella rossa](./media/workbooks-grid-visualizations/custom-column-width-setting.png)

Se il campo viene lasciato nero, la larghezza verrà determinata automaticamente in base al numero di caratteri nella colonna e al numero di colonne visibili. L'unità predefinita è "ch" (caratteri).

Se si seleziona il pulsante blu **(larghezza corrente)** nell'etichetta, il campo di testo sarà riempito con la larghezza corrente della colonna selezionata. Se è presente un valore nel campo larghezza personalizzata senza unità di misura, verrà usato il valore predefinito.

Le unità di misura disponibili sono:

| Unità di misura | Definizione           |
|:--------------------|:---------------------|
| ch                  | caratteri (impostazione predefinita) |
| px                  | pixel               |
| fr                  | unità frazionarie     |
| %                   | percentuale           |

Convalida dell'input: se la convalida ha esito negativo, viene visualizzata una finestra di messaggio di guida rossa sotto il campo, ma l'utente può comunque applicare la larghezza. Se un valore è presente nell'input, verrà analizzato. Se non viene trovata alcuna unità di misura valida, verrà usato il valore predefinito.

Non esiste una larghezza minima o massima perché questa viene lasciata alla discrezione dell'autore. Il campo larghezza colonna personalizzata è disabilitato per le colonne nascoste.

## <a name="examples"></a>Esempi

### <a name="spark-lines-and-bar-underneath"></a>Righe e barre Spark sotto

Nell'esempio seguente vengono illustrati i conteggi delle richieste e la relativa tendenza in base al nome della richiesta.

```kusto
requests
| make-series Trend = count() default = 0 on timestamp from ago(1d) to now() step 1h by name
| project-away timestamp
| join kind = inner (requests
    | summarize Requests = count() by name
    ) on name
| project name, Requests, Trend
| order by Requests desc
```

[![Screenshot di una griglia basata su log con una barra sotto e una linea Spark](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png)](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png#lightbox)

### <a name="heatmap-with-shared-scales-and-custom-formatting"></a>Mappa termica con scale condivise e formattazione personalizzata

Questo esempio mostra diverse metriche di durata delle richieste e i relativi conteggi. Il renderer mappa termica utilizza i valori minimi impostati in Settings o calcola un valore minimo e massimo per la colonna e assegna un colore di sfondo dalla tavolozza selezionata per la cella in base al valore della cella rispetto al valore minimo e massimo della colonna.

```
requests
| summarize Mean = avg(duration), (Median, p80, p95, p99) = percentiles(duration, 50, 80, 95, 99), Requests = count() by name
| order by Requests desc
```

[![Screenshot di una griglia basata su log con una mappa termica con una scala condivisa tra colonne](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png#lightbox)

Nell'esempio precedente viene usata una tavolozza condivisa (verde o rosso) e una scala per colorare le colonne (media, mediana, P80, P95 e P99). Tavolozza separata (blu) utilizzato per la colonna della richiesta.

#### <a name="shared-scale"></a>Scala condivisa

Per ottenere una scala condivisa:

1. Usare espressioni regolari per selezionare più di una colonna a cui applicare un'impostazione. Ad esempio, impostare il nome della colonna su `Mean|Median|p80|p95|p99` per selezionarli tutti.
2. Elimina le impostazioni predefinite per le singole colonne.

In questo modo, la nuova impostazione per più colonne applica le impostazioni per includere una scala condivisa.

[![Screenshot di un'impostazione della griglia basata su log per ottenere una scala condivisa tra le colonne](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png#lightbox)

### <a name="icons-to-represent-status"></a>Icone per rappresentare lo stato

Nell'esempio seguente viene illustrato lo stato personalizzato delle richieste in base alla durata P95.

```
requests
| summarize p95 = percentile(duration, 95) by name
| order by p95 desc
| project Status = case(p95 > 5000, 'critical', p95 > 1000, 'error', 'success'), name, p95
```

[![Screenshot di una griglia basata su log con una mappa termica con una scala condivisa tra colonne usando la query precedente.](./media/workbooks-grid-visualizations/log-chart-grid-icons.png)](./media/workbooks-grid-visualizations/log-chart-grid-icons.png#lightbox)

I nomi delle icone supportati sono: `cancelled` ,, `critical` `disabled` , `error` , `failed` , `info` , `none` , `pending` , `stopped` , `question` , `success` , `unknown` , `warning` `uninitialized` , `resource` , `up` , `down` , `left` , `right` , `trendup` , `trenddown` , `4` ,,,, `3` `2` `1` `Sev0` , `Sev1` , `Sev2` , `Sev3` , `Sev4` , `Fired` , `Resolved` , `Available` , `Unavailable` , `Degraded` , `Unknown` e `Blank` .

### <a name="using-thresholds-with-links"></a>Utilizzo delle soglie con i collegamenti

Nelle istruzioni riportate di seguito viene illustrato come utilizzare le soglie con i collegamenti per assegnare icone e aprire cartelle di lavoro diverse. Ogni collegamento nella griglia apre un modello di cartella di lavoro diverso per la risorsa Application Insights.

1. Passare alla modalità di modifica della cartella di lavoro selezionando *modifica* elemento della barra degli strumenti.
2. Selezionare **Aggiungi** e quindi *Aggiungi query*.
3. Impostare l' *origine dati* su "JSON" e la *visualizzazione* su "Grid".
4. Immettere la query seguente.

```json
[ 
    { "name": "warning", "link": "Community-Workbooks/Performance/Performance Counter Analysis" },
    { "name": "info", "link": "Community-Workbooks/Performance/Performance Insights" },
    { "name": "error", "link": "Community-Workbooks/Performance/Apdex" }
]
```

5. Eseguire la query.
6. Selezionare le **impostazioni delle colonne** per aprire le impostazioni.
7. Selezionare "nome" dalle *colonne*.
8. In *renderer colonna*scegliere "soglie".
9.  Immettere e scegliere le seguenti *impostazioni di soglia*.
   
    | Operatore | Valore   | Icone   |
    |----------|---------|---------|
    | ==       | warning | Avviso |
    | ==       | error   | Operazione non riuscita  |

    ![Screenshot della scheda Modifica impostazioni colonna con le impostazioni precedenti.](./media/workbooks-grid-visualizations/column-settings.png)

    Mantieni la riga predefinita così come è. È possibile immettere il testo desiderato. La colonna di testo accetta un formato stringa come input e lo popola con il valore della colonna e l'unità, se specificato. Ad esempio, se Warning è il valore della colonna, il testo può essere " {0} {1} link!", verrà visualizzato come "Warning link!".
10. Selezionare la casella *impostare questo elemento come collegamento* .
    1. In *Visualizza per aprire*scegliere "cartella di lavoro (modello)".
    2. In *valore collegamento deriva da*scegliere "collegamento".
    3. Selezionare la casella *Apri collegamento nel* pannello del contesto.
    4. Scegliere le impostazioni seguenti nelle *impostazioni del collegamento alla cartella di lavoro*
        1. In *ID modello deriva da*scegliere "colonna".
        2. In *colonna* scegliere "collegamento".

    ![Screenshot delle impostazioni del collegamento con le impostazioni precedenti.](./media/workbooks-grid-visualizations/make-this-item-a-link.png)

11. Selezionare "collega" da *colonne*. In Impostazioni accanto a *colonna renderer*selezionare **(Nascondi colonna)**.
1. Per modificare il nome visualizzato della colonna "nome", selezionare la scheda **etichette** . Nella riga con "nome" come *ID colonna*, in * etichetta colonna immettere il nome che si desidera visualizzare.
2. Selezionare **applica**

![Screenshot di una soglia nella griglia con le impostazioni precedenti](./media/workbooks-grid-visualizations/thresholds-workbooks-links.png)

## <a name="fractional-units-percentages"></a>Percentuali di unità frazionarie

L'unità frazionaria (FR) è un'unità dinamica di misurazione comunemente utilizzata in vari tipi di griglie. Con la modifica delle dimensioni o della risoluzione della finestra, viene modificato anche lo spessore di fr.

Nella schermata seguente viene illustrata una tabella con otto colonne con larghezza 1FR e tutte le stesse larghezze. Quando le dimensioni della finestra cambiano, la larghezza di ogni colonna cambia proporzionalmente.

[![Screenshot delle colonne nella griglia con valore della larghezza di colonna pari a 1FR ogni](./media/workbooks-grid-visualizations/custom-column-width-fr.png)](./media/workbooks-grid-visualizations/custom-column-width-fr.png#lightbox)

Nell'immagine seguente viene mostrata la stessa tabella, ma la prima colonna è impostata sulla larghezza del 50%. In questo modo, la colonna verrà impostata sulla metà della larghezza totale della griglia. Il ridimensionamento della finestra continuerà a mantenere la larghezza del 50% a meno che le dimensioni della finestra non risultino troppo ridotte. Queste colonne dinamiche hanno una larghezza minima in base al relativo contenuto. Il restante 50% della griglia è diviso per le otto unità frazionarie totali. La colonna "Kind" riportata di seguito è impostata su 2FR, quindi occupa un quarto dello spazio rimanente. Poiché le altre colonne sono 1FR ognuna, ciascuna di esse prende un ottavo della metà destra della griglia.

[![Screenshot delle colonne nella griglia con un valore di larghezza della colonna pari al 50% e il resto come 1FR ogni](./media/workbooks-grid-visualizations/custom-column-width-fr2.png)](./media/workbooks-grid-visualizations/custom-column-width-fr2.png#lightbox)

La combinazione di larghezze fr,%, px e ch è possibile e funziona in modo analogo agli esempi precedenti. Le larghezze impostate dalle unità statiche (ch e px) sono costanti rigide che non cambiano anche se la finestra o la risoluzione viene modificata. Le colonne impostate da% utilizzeranno la percentuale in base alla larghezza della griglia totale (potrebbe non essere esatta a causa della larghezza minima precedente). Le colonne impostate con fr divideranno semplicemente lo spazio della griglia rimanente in base al numero di unità frazionarie assegnate.

[![Screenshot delle colonne nella griglia con l'assortimento di unità di larghezza diverse usate](./media/workbooks-grid-visualizations/custom-column-width-fr3.png)](./media/workbooks-grid-visualizations/custom-column-width-fr3.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare un [albero nelle cartelle di lavoro di](workbooks-tree-visualizations.md).
* Informazioni su come creare [parametri di testo della cartella di lavoro](workbooks-text.md).