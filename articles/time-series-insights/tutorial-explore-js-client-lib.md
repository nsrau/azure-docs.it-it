---
title: Esplorare la libreria client JavaScript per Azure Time Series Insights
description: Informazioni sulla libreria client JavaScript per Azure Time Series Insights e sul modello di programmazione correlato.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: anshan
ms.openlocfilehash: 85d6ac0084343dc821a4eaf91c8053c9885ccfeb
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366047"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Esercitazione: Esplorare la libreria client JavaScript per Azure Time Series Insights

Per permettere agli sviluppatori Web di eseguire facilmente query e visualizzare i dati archiviati in Time Series Insights, è stata sviluppata una libreria client per Time Series Insights basata su JavaScript D3.  Usando un'applicazione Web di esempio, questa esercitazione permette di esplorare la libreria client per Time Series Insights e il modello di programmazione correlato.

Gli argomenti trattati offrono alcune opportunità di sperimentare la libreria, acquisire una comprensione approfondita di come accedere ai dati di Time Series Insights e usare controlli grafico per il rendering e la visualizzazione dei dati. L'obiettivo è fornire dettagli sufficienti in modo da usare la libreria nell'applicazione Web personale.

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
> * Applicazione di esempio Time Series Insights.
> * Libreria client JavaScript per Time Series Insights.
> * Modo in cui l'applicazione di esempio usa la libreria per visualizzare i dati di Time Series Insights.

## <a name="video"></a>Video: 

### <a name="in-this-video-we-introduce-the-open-source-time-series-insights-javascript-sdkbr"></a>In questo video viene presentato JavaScript SDK open source per Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione usa la funzionalità "Strumenti di sviluppo" (nota anche come DevTools o F12), disponibile nella maggior parte dei Web browser moderni, come [Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/) e altri. Se non si ha già familiarità con questa funzionalità, potrebbe essere utile esplorarla nel browser prima di continuare.

## <a name="time-series-insights-sample-application"></a>Applicazione di esempio Time Series Insights

Durante questa esercitazione viene usata l'applicazione di esempio Time Series Insights per esplorare il codice sorgente alla base dell'applicazione, tra cui l'utilizzo della libreria client JavaScript per Time Series Insights. L'esempio è un'applicazione Web a pagina singola che dimostra l'uso della libreria. Nell'esempio viene illustrato come eseguire una query e visualizzare i dati da un ambiente Time Series Insights di esempio.

1. Passare all'[applicazione di esempio Time Series Insights](https://insights.timeseries.azure.com/clientsample). Viene visualizzata una pagina simile all'immagine seguente con un prompt per eseguire l'accesso:

   ![Richiesta di accesso dell'esempio di client Time Series Insights](media/tutorial-explore-js-client-lib/tcs-sign-in.png)

2. Selezionare **Accedi** e immettere o selezionare le credenziali. Usare un account aziendale (Azure Active Directory) o un account personale (account Microsoft o account del servizio gestito).

   ![Richiesta di credenziali nell'esempio di client Time Series Insights](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)

3. Dopo l'accesso viene visualizzata una pagina simile all'immagine seguente. La pagina mostra diversi stili di grafici di esempio, popolati con dati di Time Series Insights. L'account utente e l'opzione di **disconnessione** sono visibili in alto a destra:

   ![Pagina principale dell'esempio di client Time Series Insights](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)

### <a name="page-source-and-structure"></a>Codice sorgente e struttura della pagina

Visualizzare prima di tutto il codice sorgente HTML e JavaScript alla base della pagina di cui viene eseguito il rendering nel browser. Non verranno esaminati tutti gli elementi, ma verranno descritte le sezioni principali per dare un'idea del funzionamento della pagina:

1. Aprire **Strumenti di sviluppo** nel browser. Esaminare gli elementi HTML che costituiscono la pagina corrente, chiamati anche albero DOM o HTML.

2. Espandere gli elementi `<head>` e `<body>` e osservare le sezioni seguenti:

   - Nell'elemento `<head>` si trovano gli elementi che effettuano il pull di altri file per supportare il funzionamento della pagina:
     - Un elemento `<script>` che viene usato per fare riferimento ad Azure Active Directory Authentication Library **adal.min.js** (noto anche come ADAL). Si tratta di una libreria JavaScript che fornisce l'autenticazione OAuth 2.0 (accesso) e l'acquisizione del token per accedere alle API.
     - Più elementi `<link>` per i fogli di stile (noti anche come CSS), come **sampleStyles.css** e **tsiclient.css**. I fogli di stile vengono usati per controllare i dettagli relativi allo stile visivo della pagina, come colori, tipi di carattere, spaziatura e così via.
     - Un `<script>` elemento che viene usato per fare riferimento alla libreria JavaScript per il client Time Series Insights **tsiclient.js**. La libreria è usata dalla pagina per chiamare le API del servizio Time Series Insights ed eseguire il rendering dei controlli grafico sulla pagina.

     >[!NOTE]
     > Il codice sorgente per la libreria JavaScript ADAL è disponibile nel [repository azure-activedirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > Il codice sorgente per la libreria JavaScript per il client Time Series Insights è disponibile nel [repository tsiclient](https://github.com/Microsoft/tsiclient).

   - Nell'elemento `<body>` si trovano gli elementi `<div>`, che fungono da contenitori per definire il layout degli elementi sulla pagina, e un altro elemento `<script>`:
     - Il primo elemento `<div>` specifica la finestra di dialogo di **accesso** (`id="loginModal"`).
     - Il secondo elemento `<div>` funge da elemento padre per:
       - Un elemento intestazione `<div>`, usato per i messaggi di stato e le informazioni di accesso nella parte superiore della pagina (`class="header"`).
       - Un elemento `<div>` per gli elementi del corpo della pagina rimanenti, inclusi tutti i grafici (`class="chartsWrapper"`).
       - Una sezione `<script>`, che contiene tutto il codice JavaScript usato per controllare la pagina.

   [![Esempio di client Time Series Insights con Strumenti di sviluppo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

3. Espandere l'elemento `<div class="chartsWrapper">` per visualizzare altri elementi figlio `<div>`. Questi elementi vengono usati per posizionare ogni esempio di controllo grafico. Notare la presenza di diverse coppie di elementi `<div>`, una per ogni esempio di grafico:

   - Il primo elemento (`class="rowOfCardsTitle"`) contiene un titolo descrittivo per riepilogare i dati rappresentati dai grafici. Ad esempio, "Static Line Charts With Full-Size Legends" (Grafico a linee statiche con legende a dimensione intera).
   - Il secondo elemento figlio(`class="rowOfCards"`) è un elemento padre, che contiene altri elementi `<div>` figlio che posizionano gli effettivi controlli grafico all'interno di una riga.

   ![Elementi div del corpo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)

4. Espandere ora l'elemento `<script type="text/javascript">` direttamente al di sotto dell'elemento `<div class="chartsWrapper">`. Notare l'inizio della sezione JavaScript a livello di pagina, usata per gestire tutta la logica della pagina: l'autenticazione, la chiamata delle API del servizio Time Series Insights, il rendering dei controlli grafico e altro ancora.

   ![Script del corpo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)

## <a name="tsi-javascript-client-library-concepts"></a>Concetti sulla libreria client JavaScript per Time Series Insights

Anche se questi argomenti non verranno esaminati in dettaglio, fondamentalmente la libreria per l'app client Time Series Insights **tsclient.js** fornisce un'astrazione per due importanti categorie:

- **Metodi wrapper per chiamare le API di query di Time Series Insights**: API REST che permettono di eseguire query per dati di Time Series Insights usando espressioni di aggregazione. I metodi sono organizzati all'interno dello spazio dei nomi `TsiClient.Server` della libreria.
- **Metodi per la creazione e il popolamento di diversi tipi di controlli di grafici**: usati per il rendering dei dati aggregati di Time Series Insights in una pagina Web. I metodi sono organizzati all'interno dello spazio dei nomi `TsiClient.UX` della libreria.

I concetti seguenti sono universali e applicabili alle API della libreria per l'app client Time Series Insights in generale.

### <a name="authentication"></a>Authentication

Come accennato in precedenza, questo esempio è un'applicazione a pagina singola che usa il supporto per OAuth 2.0 in ADAL per l'autenticazione utente. Ecco alcuni punti di interesse in questa sezione dello script:

1. Quando si usa ADAL per l'autenticazione, è necessario che l'applicazione client registri se stessa nel registro delle applicazioni di Azure Active Directory (Azure AD). In quanto applicazione a pagina singola, l'applicazione è registrata per usare il flusso di concessione di autorizzazioni OAuth 2.0 "implicito". Analogamente, l'applicazione specifica alcune delle proprietà di registrazione in fase di esecuzione, ad esempio il GUID dell'ID client (`clientId`) e l'URI di reindirizzamento (`postLogoutRedirectUri`), per partecipare al flusso.

2. Successivamente l'applicazione richiede un "token di accesso" di Azure AD. Il token di accesso viene generato per un set limitato di autorizzazioni per un identificatore di servizio/API specifico https://api.timeseries.azure.com. L'identificatore di servizio/API è noto anche come token "audience". Le autorizzazioni per il token vengono rilasciate per conto dell'utente connesso. L'identificatore per il servizio e/o l'API è ancora un'altra proprietà contenuta nella registrazione di Azure AD dell'applicazione. Quando ADAL restituisce il token di accesso all'applicazione, questo viene passato come "bearer token" durante l'accesso alle API del servizio Time Series Insights.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=145-204&highlight=4-9,36-39)]

### <a name="control-identification"></a>Identificazione dei controlli

Come descritto in precedenza, gli elementi `<div>` all'interno dell'elemento `<body>` forniscono il layout per tutti i controlli grafico mostrati sulla pagina. Ogni elemento `<div>` specifica le proprietà per gli attributi visivi e di posizionamento del controllo grafico, tra cui una proprietà `id`. La proprietà `id` fornisce un identificatore univoco, usato nel codice JavaScript per identificare ogni controllo per il rendering e l'aggiornamento e da associare a tali controlli.

### <a name="aggregate-expressions"></a>Espressioni di aggregazione

Le API della libreria per il client Time Series Insights fanno ampio uso delle espressioni di aggregazione. Un'espressione di aggregazione permette di costruire uno o più "termini di ricerca." Le API sono simili al modo in cui [Time Series Insights Explorer](https://insights.timeseries.azure.com/demo) usa gli intervalli di ricerca, con predicati, misurazioni e valori di divisione. La maggior parte delle API della libreria accetta una matrice di espressioni di aggregazione, usate dal servizio per creare una query di dati di Time Series Insights.

### <a name="call-pattern"></a>Modello di chiamata

Il popolamento e il rendering dei controlli grafico seguono un modello generale. Questo modello viene usato in tutto il codice JavaScript della pagina ed esegue il caricamento e la creazione di istanze dei controlli dell'applicazione di esempio Time Series Insights:

1. Dichiarare un `array` che includa una o più espressioni di aggregazione Time Series Insights:

   ```javascript
   var aes =  [];
   ```

2. Creare da 1 a n oggetti espressione di aggregazione e aggiungerli alla matrice di espressioni di aggregazione:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **Parametri aggregateExpression**

   | Parametro | DESCRIZIONE | Esempio |
   | --------- | ----------- | ------- |
   | `predicateObject` | Espressione di filtro dei dati. |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | Nome della proprietà della misura usata. | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | Aggregazioni desiderate della proprietà della misura. | `['avg', 'min']` |
   | `searchSpan`      | Dimensioni di durata e intervallo dell'espressione di aggregazione. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Proprietà stringa in base alla quale eseguire la divisione (facoltativa, può essere null). | `{property: 'Station', type: 'String'}` |
   | `color`         | Colore degli oggetti di cui eseguire il rendering. | `'pink'` |
   | `alias`           | Nome descrittivo per l'espressione di aggregazione. | `'Factory3Temperature'` |
   | `contextMenuActions` | Matrice di azioni da associare agli oggetti serie temporale in una visualizzazione (facoltativa). | Per altre informazioni, vedere [Menu di scelta rapida popup nella sezione Funzionalità avanzate](#popup-context-menus) |

3. Chiamare una query Time Series Insights usando le API `TsiClient.Server` per richiedere i dati aggregati:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **Parametri getAggregates**

   | Parametro | DESCRIZIONE | Esempio |
   | --------- | ----------- | ------- |
   | `token`     | Token di accesso per l'API di Time Series Insights. |  `authContext.getTsiToken()` Per altre informazioni, vedere la [sezione Autenticazione](#authentication). |
   | `envFQDN`   | Nome di dominio completo (FQDN) per l'ambiente Time Series Insights. | Ad esempio, dal portale di Azure: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Matrice di espressioni di query Time Series Insights. | Usare la variabile `aes` descritta in precedenza: `aes.map(function(ae){return ae.toTsx()}`. |

4. Trasformare in JSON il risultato compresso restituito dalla query Time Series Insights per la visualizzazione:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

5. Creare un controllo grafico usando le API `TsiClient.UX` e associarlo a uno degli elementi `<div>` nella pagina:

   ```javascript
   var lineChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

6. Popolare il controllo grafico con gli oggetti dati JSON trasformati ed eseguirne il rendering nella pagina:

   ```javascript
   lineChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Rendering dei controlli

Attualmente la libreria client per Time Series Insights espone otto controlli analitici univoci: un grafico a linee, un grafico a torta, un grafico a barre, una mappa termica, controlli gerarchici, una griglia accessibile, sequenze temporali di eventi discreti e sequenze temporali di transizioni di stato.

### <a name="line-bar-pie-chart-examples"></a>Esempi di grafici a linee, a barre e a torta

Esaminare il codice alla base di alcuni controlli grafico standard mostrati nell'applicazione e quindi i modelli di programmazione per la creazione dei controlli. In particolare, esaminare la sezione del codice HTML al di sotto del commento `// Example 3/4/5`, che esegue il rendering dei controlli con valori di ID `chart3`, `chart4` e `chart5`.

Come specificato nel passaggio 3 della sezione [Codice sorgente e struttura della pagina](#page-source-and-structure), i controlli grafico sono disposti in righe sulla pagina e per ognuno è presente una riga per un titolo descrittivo. In questo esempio i tre grafici vengono popolati sotto l'elemento `<div>` del titolo "Multiple Chart Types From the Same Data" (Più tipi di grafico dagli stessi dati) e sono associati ai tre elementi `<div>` sottostanti:

[!code-javascript[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

La sezione di codice JavaScript seguente usa i modelli descritti in precedenza per creare espressioni di aggregazione Time Series Insights, usarle per eseguire query per dati di Time Series Insights ed eseguire il rendering dei tre grafici. Notare i tre tipi usati all'interno dello spazio dei nomi `tsiClient.ux`: `LineChart`, `BarChart` e `PieChart`, per creare ed eseguire il rendering dei rispettivi grafici. Notare inoltre che tutti i tre grafici possono usare gli stessi dati delle espressioni di aggregazione, `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

I tre grafici vengono visualizzati in questo modo dopo il rendering:

[![Più tipi di grafico dagli stessi dati](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Funzionalità avanzate

La libreria client per Time Series Insights espone anche alcune funzionalità avanzate facoltative che possono rivelarsi utili.

### <a name="states-and-events"></a>Stati ed eventi

Un esempio delle funzionalità avanzate disponibili è la possibilità di aggiungere transizioni di stato ed eventi discreti ai grafici. Questa funzionalità è utile per evidenziare eventi imprevisti, per generare avvisi e per i passaggi di stato come attivazione/disattivazione.

Osservare il codice sottostante alla sezione del codice HTML sotto il commento `// Example 10`. Il codice esegue il rendering di un controllo linea sotto il titolo "Line Charts with Multiple Series Types" (Grafici a linee con più tipi di serie), associandolo all'elemento `<div>` con il valore di ID `chart10`.

1. Prima di tutto viene definita una struttura denominata `events4`, che conterrà gli elementi di modifica dello stato di cui tenere traccia. La struttura contiene:

   - Una chiave stringa denominata `Component States`.
   - Una matrice di oggetti valore che rappresentano gli stati. Ogni oggetto include:
     - Una chiave stringa che contiene un timestamp ISO JavaScript.
     - Una matrice che contiene le caratteristiche dello stato: un colore e una descrizione.

2. Viene quindi definita la struttura `events5` per "Incidents", che contiene una matrice di elementi evento di cui tenere traccia. La struttura della matrice ha la stessa forma di quella descritta per `events4`.

3. Infine viene eseguito il rendering del grafico a linee, passando le due strutture con i parametri delle opzioni del grafico `events:` e `states:`. Notare i parametri delle altre opzioni per specificare un elemento `tooltip:`, `theme:` o `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Visivamente, le finestre popup/i marcatori a forma di rombo usati per indicare gli eventi imprevisti e le finestre popup/barre colorate lungo la scala cronologica indicano modifiche di stato:

[![Grafici a linee con più tipi di serie](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Menu di scelta rapida popup

Un altro esempio di funzionalità avanzate è rappresentato dai menu di scelta rapida personalizzati. I menu di scelta rapida personalizzati sono utili per l'attivazione di azioni e passaggi logici successivi nell'ambito dell'applicazione.

Osservare il codice sottostante alla sezione del codice HTML sotto il commento `// Example 13/14/15`. Questo codice esegue inizialmente il rendering di un grafico a linee sotto il titolo "Line Chart with Context Menu to Create Pie/Bar Chart" (Grafico a linee con menu contestuale per creare un grafico a torta/a barre) e il grafico è associato all'elemento `<div>` con valore di ID `chart13`. Usando i menu di scelta rapida, il grafico a linee offre la possibilità di creare in modo dinamico un grafico a torta e un grafico a barre, associati a elementi `<div>` con ID `chart14` e `chart15`. Inoltre, i grafici a torta e i grafici a barre usano i menu a scelta rapida anche per abilitare funzionalità proprie: la possibilità rispettivamente di copiare dati dal grafico a torta al grafico a barre e stampare i dati del grafico a barre nella finestra della console del browser.

1. Prima di tutto, viene definita una serie di azioni personalizzate. Ogni azione contiene una matrice con uno o più elementi. Ogni elemento definisce una singola voce di menu di scelta rapida:

   - `barChartActions`: questa azione definisce il menu di scelta rapida per il grafico a torta, che contiene un elemento per definire una singola voce:
     - `name`: testo usato per la voce di menu, ad esempio "Stampa parametri nella console".
     - `action`: azione associata alla voce di menu. È sempre una funzione anonima che accetta tre argomenti in base all'espressione di aggregazione usata per creare il grafico. In questo caso, gli argomenti vengono scritti nella finestra della console del browser:
       - `ae`: matrice di espressioni di aggregazione.
       - `splitBy`: valore di divisione.
       - `timestamp`: timestamp.

   - `pieChartActions`: questa azione definisce il menu di scelta rapida per il grafico a barre, che contiene un elemento per definire una singola voce. La forma e lo schema sono gli stessi di quelli dell'elemento `barChartActions` precedente, ma si noti che la funzione `action` è notevolmente diversa, in quanto crea un'istanza del grafico a barre e ne esegue il rendering. Si noti inoltre che l'argomento `ae` viene usato per specificare la matrice di espressioni di aggregazione passata in fase di esecuzione all'apertura della voce di menu. La funzione imposta anche la proprietà `ae.contextMenu` con il menu di scelta rapida `barChartActions`.
   - `contextMenuActions`: questa azione definisce il menu di scelta rapida per il grafico a linee, che contiene tre elementi per definire tre voci di menu. La forma e lo schema per ogni elemento sono gli stessi di quelli precedenti descritti. Proprio come per l'elemento `barChartActions`, la prima voce scrive i tre argomenti della funzione nella finestra della console del browser. Analogamente all'elemento `pieChartActions`, le seconde due voci creano un'istanza rispettivamente dei grafici a torta e a barre e ne eseguono il rendering. Le seconde due voci impostano anche le relative proprietà `ae.contextMenu` rispettivamente con i menu di scelta rapida `pieChartActions` e `barChartActions`.

2. Viene quindi eseguito il push di due espressioni di aggregazione nella matrice di espressioni di aggregazione `aes`, specificando la matrice `contextMenuActions` per ogni voce. Queste espressioni vengono usate con il controllo grafico a linee.

3. Infine, inizialmente viene eseguito il rendering solo del grafico a linee, da cui è possibile eseguire il rendering del grafico a torta e del grafico a barre in fase di esecuzione.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Lo screenshot mostra i grafici, con i rispettivi menu di scelta rapida popup. I grafici a torta e a barre sono stati creati in modo dinamico, usando le opzioni del menu di scelta rapida del grafico a linee.

[![Grafico a linee con menu di scelta rapida per creare il grafico a torta/a barre](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pennelli

I pennelli vengono usati per definire l'ambito di un intervallo di tempo, in modo da stabilire azioni come l'ingrandimento e l'esplorazione.

Il codice usato per illustrare i pennelli viene mostrato nell'esempio "Line Chart with Context Menu to Create Pie/Bar Chart" (Grafico a linee con menu di scelta rapida per creare il grafico a torta/a barre) precedente, che descrive i [menu di scelta rapida popup](#popup-context-menus-section).

1. Le azioni dei pennelli sono simili a quelle dei menu di scelta rapida, per il fatto che definiscono una serie di azioni personalizzate per il pennello. Ogni azione contiene una matrice con uno o più elementi. Ogni elemento definisce una singola voce di menu di scelta rapida:
   - `name`: testo usato per la voce di menu, ad esempio "Stampa parametri nella console".
   - `action`: azione associata alla voce di menu, che è sempre una funzione anonima che accetta due argomenti. In questo caso, gli argomenti vengono scritti nella finestra della console del browser:
      - `fromTime`: timestamp per l'origine della selezione del pennello.
      - `toTime`: timestamp per la destinazione della selezione del pennello.

2. Le azioni dei pennelli vengono aggiunte come un'altra proprietà delle opzioni del grafico. Notare la proprietà `brushContextMenuActions: brushActions` passata alla chiamata `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

![Grafico a linee con menu di scelta rapida per creare il grafico a torta/a barre tramite pennelli](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Accedere ed esplorare l'applicazione di esempio Time Series Insights e il relativo codice sorgente.
> * Usare le API nella libreria client JavaScript per Time Series Insights.
> * Usare JavaScript per creare e popolare controlli grafico con dati di Time Series Insights.

Come descritto, l'applicazione di esempio Time Series Insights usa un set di dati dimostrativi. Per informazioni su come creare un ambiente Time Series Insights personalizzato e i relativi set di dati, passare all'articolo seguente:

> [!div class="nextstepaction"]
> [Esercitazione: Creare un ambiente Azure Time Series Insights](tutorial-create-populate-tsi-environment.md)


