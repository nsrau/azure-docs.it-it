---
title: 'Esercitazione: Esplorare la libreria client JavaScript per Azure Time Series Insights | Microsoft Docs'
description: Informazioni sulla libreria client JavaScript per Azure Time Series Insights e sul modello di programmazione correlato.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 8cb1d06872f7eae04bac934220da9d58982d0f4b
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233723"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Esercitazione: Esplorare la libreria client JavaScript per Azure Time Series Insights

Per permettere agli sviluppatori Web di eseguire facilmente query e visualizzare i dati archiviati in Time Series Insights, è stata sviluppata una libreria client per Time Series Insights basata su JavaScript D3. Questa esercitazione descrive la libreria client TSI e il modello di programmazione tramite un'app di esempio ospitata.

Illustra come usare la libreria, come accedere ai dati TSI e come usare i controlli grafico per il rendering e la visualizzazione dei dati. Descrive inoltre come sperimentare con diversi tipi di grafici per visualizzare i dati. Al termine dell'esercitazione sarà possibile usare la libreria client per incorporare le funzionalità TSI in un'app Web personalizzata.

In particolare, il contenuto dell'esercitazione include:

> [!div class="checklist"]
> * Applicazione di esempio Time Series Insights.
> * Libreria client JavaScript per Time Series Insights.
> * Modo in cui l'applicazione di esempio usa la libreria per visualizzare i dati di Time Series Insights.

> [!NOTE]
> * L'esercitazione usa una [demo Web di Time Series Insights](https://insights.timeseries.azure.com/clientsample) gratuita e ospitata.
> * I file di origine dell'app di esempio Time Series Insights sono reperibili nel [repository di esempi di GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Leggere la [documentazione di riferimento sui client TSI](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

## <a name="video"></a>Video

### <a name="in-this-video-we-introduce-the-open-source-time-series-insights-javascript-sdkbr"></a>In questo video viene presentato JavaScript SDK open source per Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione usa la funzionalità **Strumenti di sviluppo** del browser. I Web browser moderni, come [Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/) e altri, solitamente forniscono l'accesso alla visualizzazione **Web Inspector** tramite il tasto di scelta rapida `F12`. Altrimenti, è possibile accedervi facendo clic con il pulsante destro del mouse in una pagina Web e selezionando **Esamina elemento**.

## <a name="time-series-insights-sample-application"></a>Applicazione di esempio Time Series Insights

Durante questa esercitazione viene usata l'applicazione di esempio Time Series Insights per esplorare il codice sorgente alla base dell'applicazione e la libreria client JavaScript per TSI. Verrà descritto come interagire con TSI in JavaScript e visualizzare i dati tramite grafici.

1. Passare all'[applicazione di esempio Time Series Insights](https://insights.timeseries.azure.com/clientsample). Verrà visualizzata la richiesta di accesso seguente:

   [![Richiesta di accesso dell'esempio di client TSI](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Selezionare **Accedi** per immettere o selezionare le credenziali. Usare un account aziendale (Azure Active Directory) o un account personale (account Microsoft o account del servizio gestito).

   [![Richiesta di credenziali nell'esempio di client TSI](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Dopo aver eseguito l'accesso, verrà visualizzata una pagina con diversi tipi di grafici popolati con dati TSI. L'account utente e l'opzione di **disconnessione** sono visibili in alto a destra:

   [![Pagina principale dell'esempio di client TSI dopo l'accesso](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Codice sorgente e struttura della pagina

Vediamo prima di tutto il [codice sorgente HTML e JavaScript](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) della pagina Web visualizzata:

1. Aprire **Strumenti di sviluppo** nel browser. Esaminare gli elementi HTML che costituiscono la pagina corrente, chiamati anche albero DOM o HTML.

1. Espandere gli elementi `<head>` e `<body>` e osservare le sezioni seguenti:

   * Nell'elemento `<head>` si trovano i metadati e le dipendenze della pagina che consentono l'esecuzione dell'app:
     * Un elemento `<script>` che viene usato per fare riferimento ad Azure Active Directory Authentication Library **adal.min.js** (noto anche come ADAL). Si tratta di una libreria JavaScript che fornisce l'autenticazione OAuth 2.0 (accesso) e l'acquisizione del token per accedere alle API.
     * Più elementi `<link>` per i fogli di stile (noti anche come CSS), come **sampleStyles.css** e **tsiclient.css**. I fogli di stile vengono usati per controllare i dettagli relativi allo stile visivo della pagina, come colori, tipi di carattere, spaziatura e così via.
     * Un `<script>` elemento che viene usato per fare riferimento alla libreria JavaScript per il client Time Series Insights **tsiclient.js**. La libreria è usata dalla pagina per chiamare le API del servizio Time Series Insights ed eseguire il rendering dei controlli grafico sulla pagina.

     >[!NOTE]
     > * Il codice sorgente per la libreria JavaScript ADAL è disponibile nel [repository azure-activedirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * Il codice sorgente per la libreria JavaScript per il client Time Series Insights è disponibile nel [repository tsiclient](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * Nell'elemento `<body>` si trovano gli elementi `<div>`, che consentono di definire il layout deli elementi della pagina, e un altro elemento `<script>`:
     * Il primo elemento `<div>` specifica la finestra di dialogo di **accesso** (`id="loginModal"`).
     * Il secondo elemento `<div>` funge da elemento padre per:
       * Un elemento intestazione `<div>`, usato per i messaggi di stato e le informazioni di accesso nella parte superiore della pagina (`class="header"`).
       * Un elemento `<div>` per gli elementi del corpo della pagina rimanenti, inclusi tutti i grafici (`class="chartsWrapper"`).
       * Una sezione `<script>`, che contiene tutto il codice JavaScript usato per controllare la pagina.

   [![Esempio di client Time Series Insights con Strumenti di sviluppo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Espandere l'elemento `<div class="chartsWrapper">` per visualizzare altri elementi `<div>` figlio. Questi elementi vengono usati per posizionare ogni esempio di controllo grafico. Notare la presenza di diverse coppie di elementi `<div>`, una per ogni esempio di grafico:

   * Il primo elemento (`class="rowOfCardsTitle"`) contiene un titolo descrittivo per riepilogare i dati rappresentati dai grafici. Ad esempio: `Static Line Charts With Full-Size Legends.`
   * Il secondo elemento figlio(`class="rowOfCards"`) è un elemento padre, che contiene altri elementi `<div>` figlio che posizionano gli effettivi controlli grafico all'interno di una riga.

   [![Elementi div del corpo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Espandere ora l'elemento `<script type="text/javascript">` direttamente al di sotto dell'elemento `<div class="chartsWrapper">`. Notare l'inizio della sezione JavaScript a livello di pagina, usata per gestire tutta la logica della pagina, ossia autenticazione, chiamata alle API del servizio TSI, rendering dei controlli grafico e altro ancora:

   [![Script del corpo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="tsi-javascript-client-library-concepts"></a>Concetti sulla libreria client JavaScript per Time Series Insights

La libreria client TSI (**tsclient.js**) fornisce astrazioni per due importanti funzionalità JavaScript:

* **Metodi wrapper per chiamare le API di query di Time Series Insights**: API REST che permettono di eseguire query per dati di Time Series Insights usando espressioni di aggregazione. I metodi sono organizzati all'interno dello spazio dei nomi `TsiClient.Server` della libreria.

* **Metodi per la creazione e il popolamento di diversi tipi di controllo dei grafici**: I metodi utilizzati per il rendering di Time Series Insights aggregano i dati in una pagina Web. I metodi sono organizzati all'interno dello spazio dei nomi `TsiClient.UX` della libreria.

Tramite queste semplificazioni, gli sviluppatori possono creare i componenti dei grafici dell'interfaccia utente in cui è possibile inserire più facilmente i dati TSI.

### <a name="authentication"></a>Authentication

L'[applicazione di esempio Time Series Insights](https://insights.timeseries.azure.com/clientsample) è un'app a pagina singola con il supporto per l'autenticazione degli utenti ADAL OAuth 2.0:

1. Se si usa ADAL per l'autenticazione, è necessario che l'app client sia registrata in Azure Active Directory. In effetti, l'app a pagina singola viene registrata per l'uso del [flusso di concessione implicita OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. Di conseguenza, l'applicazione deve specificare alcune proprietà di registrazione in fase di esecuzione, tra cui il GUID client (`clientId`) e l'URI di reindirizzamento (`postLogoutRedirectUri`).
1. Successivamente l'app richiede un **token di accesso** di Azure Active Directory. Il token di accesso viene generato per un set limitato di autorizzazioni per un identificatore di servizio/API specifico (`https://api.timeseries.azure.com`). Le autorizzazioni per il token vengono rilasciate per conto dell'utente connesso. L'identificatore per il servizio e/o l'API è un'altra proprietà contenuta nella registrazione di Azure Active Directory dell'applicazione.
1. Quando ADAL restituisce il token di accesso all'app, questo viene passato come **bearer token** durante l'accesso alle API del servizio TSI.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> Per altre informazioni sulle librerie ADAL supportate da Microsoft, vedere la [documentazione di riferimento di ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries).

### <a name="control-identification"></a>Identificazione dei controlli

In questo esempio gli elementi `<div>` sono disposti nell'elemento `<body>` padre per fornire un layout sensibile per tutti i controlli grafico visualizzati nella pagina.

Ogni elemento `<div>` specifica le proprietà per gli attributi visivi e di posizionamento dei controlli grafico. Le proprietà `id` dell'elemento HTML fungono da identificatori univoci da associare a specifici controlli per il rendering e l'aggiornamento dei dati visualizzati.

### <a name="aggregate-expressions"></a>Espressioni di aggregazione

Le API della libreria client TSI usano le espressioni di aggregazione:

* Un'espressione di aggregazione permette di costruire uno o più **termini di ricerca**.

* Le API client sono progettate per offrire funzionalità simili a un'altra app demo fornita ([Time Series Insights Explorer](https://insights.timeseries.azure.com/demo)), che usa gli intervalli di ricerca, con predicati, misurazioni e valori di divisione.

* La maggior parte delle API della libreria client accetta una matrice di espressioni di aggregazione, usate dal servizio per creare una query di dati di TSI.

### <a name="call-pattern"></a>Modello di chiamata

Il popolamento e il rendering dei controlli grafico seguono un modello generale. Questo modello generale può essere osservato nell'intera app di esempio e risulterà utile per la creazione della libreria client:

1. Dichiarare un `array` che includa una o più espressioni di aggregazione Time Series Insights:

   ```javascript
   var aes =  [];
   ```

1. Creare da *1* a *n* oggetti di espressioni di aggregazione. Quindi, aggiungerli alla matrice di espressioni di aggregazione.

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
   | `contextMenuActions` | Matrice di azioni da associare agli oggetti serie temporale in una visualizzazione (facoltativa). | Per altre informazioni, vedere [Menu di scelta rapida popup](#pop-up-context-menus) |

1. Chiamare una query Time Series Insights usando le API `TsiClient.Server` per richiedere i dati aggregati:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **Parametri getAggregates**

   | Parametro | DESCRIZIONE | Esempio |
   | --------- | ----------- | ------- |
   | `token`     | Token di accesso per l'API di Time Series Insights. |  `authContext.getTsiToken()` Per altre informazioni, vedere la [sezione Autenticazione](#authentication). |
   | `envFQDN`   | Nome di dominio completo (FQDN) per l'ambiente Time Series Insights. | Ad esempio, dal portale di Azure: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Matrice di espressioni di query Time Series Insights. | Usare la variabile `aes` descritta in precedenza: `aes.map(function(ae){return ae.toTsx()}`. |

1. Trasformare in JSON il risultato compresso restituito dalla query Time Series Insights per la visualizzazione:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Creare un controllo grafico usando le API `TsiClient.UX` e associarlo a uno degli elementi `<div>` nella pagina:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Popolare il controllo grafico con gli oggetti dati JSON trasformati ed eseguirne il rendering nella pagina:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Rendering dei controlli

La libreria client TSI fornisce otto controlli di analisi univoci e predefiniti:

* **grafico a linee**
* **grafico a torta**
* **grafico a barre**
* **heatmap**
* **controlli gerarchia**
* **griglia accessibile**
* **sequenze temporale di eventi discreti**
* **sequenze temporali di transizione di stato**

### <a name="line-bar-pie-chart-examples"></a>Esempi di grafici a linee, a barre e a torta

Esaminare il codice demo usato per il rendering del controllo grafico standard. Osservare il modello di programmazione e i modelli per la creazione di tali controlli. In particolare, esaminare la sezione del codice HTML al di sotto del commento `// Example 3/4/5`, che esegue il rendering dei controlli con valori di `id` HTML `chart3`, `chart4` e `chart5`.

Come specificato nel **passaggio 3** della sezione [Codice sorgente e struttura della pagina](#page-source-and-structure), i controlli grafico sono disposti in righe sulla pagina e per ognuno è presente una riga per un titolo descrittivo. In questo esempio i tre grafici vengono popolati sotto l'elemento `"Multiple Chart Types From the Same Data"` `<div>` e sono associati ai tre elementi `<div>` sottostanti:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

La sezione di codice JavaScript seguente usa i modelli descritti in precedenza per creare espressioni di aggregazione Time Series Insights, usarle per eseguire query per dati di Time Series Insights ed eseguire il rendering dei tre grafici. Notare i tre tipi usati all'interno dello spazio dei nomi `tsiClient.ux`: `LineChart`, `BarChart` e `PieChart`, per creare ed eseguire il rendering dei rispettivi grafici. Notare inoltre che tutti i tre grafici possono usare gli stessi dati delle espressioni di aggregazione, `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

I tre grafici vengono visualizzati in questo modo dopo il rendering:

[![Più tipi di grafico dagli stessi dati](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Funzionalità avanzate

La libreria client TSI include diverse funzionalità aggiuntive che è possibile usare per implementare le visualizzazioni dei dati in modo creativo.

### <a name="states-and-events"></a>Stati ed eventi

Una delle funzionalità avanzate disponibili è la possibilità di aggiungere transizioni di stato ed eventi discreti ai grafici. Questa funzionalità è utile per evidenziare eventi imprevisti, per generare avvisi e per creare passaggi di stato, ad esempio attivazione/disattivazione.

Esaminare il codice che circonda il commento `// Example 10`. Il codice esegue il rendering di un controllo linea sotto il titolo `"Line Charts with Multiple Series Types"`, associandolo all'elemento `<div>` con il valore di `id` HTML `chart10`.

1. Prima di tutto viene definita una struttura denominata `events4`, che conterrà gli elementi di modifica dello stato di cui tenere traccia. La struttura contiene:

   * Una chiave stringa denominata `Component States`.
   * Una matrice di oggetti valore che rappresentano gli stati. Ogni oggetto include:
     * Una chiave stringa che contiene un timestamp ISO JavaScript.
     * Una matrice che contiene le caratteristiche dello stato: un colore e una descrizione.

2. Viene quindi definita la struttura `events5` per `Incidents`, che contiene una matrice di elementi evento di cui tenere traccia. La struttura della matrice ha la stessa forma di quella descritta per `events4`.

3. Infine viene eseguito il rendering del grafico a linee, passando le due strutture con i parametri delle opzioni del grafico `events:` e `states:`. Notare i parametri delle altre opzioni per specificare un elemento `tooltip:`, `theme:` o `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Visivamente, le finestre popup/i marcatori a forma di rombo usati per indicare gli eventi imprevisti e le finestre popup/barre colorate lungo la scala cronologica indicano modifiche di stato:

[![Grafici a linee con più tipi di serie](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Menu di scelta rapida popup

Un'altra funzionalità avanzate è la possibilità di creare menu di scelta rapida personalizzati (menu a comparsa visualizzati con il pulsante destro del mouse). I menu di scelta rapida personalizzati sono utili per l'attivazione di azioni e passaggi logici successivi nell'ambito dell'applicazione.

Esaminare il codice intorno al commento `// Example 13/14/15`. Questo codice esegue inizialmente il rendering di un grafico a linee sotto il titolo `"Line Chart with Context Menu to Create Pie/Bar Chart"` e il grafico è associato all'elemento `<div>` con valore di `id` HTML `chart13`.

Usando i menu di scelta rapida, il grafico a linee offre la possibilità di creare in modo dinamico un grafico a torta e un grafico a barre, associati a elementi `<div>` con ID `chart14` e `chart15`. Inoltre, i grafici a torta e i grafici a barre usano i menu a scelta rapida anche per abilitare funzionalità proprie: la possibilità rispettivamente di copiare dati dal grafico a torta al grafico a barre e stampare i dati del grafico a barre nella finestra della console del browser.

1. Prima di tutto, viene definita una serie di azioni personalizzate. Ogni azione contiene una matrice con uno o più elementi. Ogni elemento definisce una singola voce di menu di scelta rapida:

   * `barChartActions`: Questa azione definisce il menu di scelta rapida per il grafico a torta, che contiene un elemento per definire una singola voce:
     * `name`: Il testo che viene usato per la voce di menu: "Stampa parametri nella console".
     * `action`: L'azione associata alla voce di menu. È sempre una funzione anonima che accetta tre argomenti in base all'espressione di aggregazione usata per creare il grafico. In questo caso, gli argomenti vengono scritti nella finestra della console del browser:
       * `ae`: La matrice di espressioni di aggregazione.
       * `splitBy`: il valore di `splitBy`.
       * `timestamp`: Il timestamp.

   * `pieChartActions`: Questa azione definisce il menu di scelta rapida per il grafico a barre, che contiene un elemento per definire una singola voce. La forma e lo schema sono gli stessi di quelli dell'elemento `barChartActions` precedente, ma si noti che la funzione `action` è notevolmente diversa, in quanto crea un'istanza del grafico a barre e ne esegue il rendering. Si noti inoltre che l'argomento `ae` viene usato per specificare la matrice di espressioni di aggregazione passata in fase di esecuzione all'apertura della voce di menu. La funzione imposta anche la proprietà `ae.contextMenu` con il menu di scelta rapida `barChartActions`.
   * `contextMenuActions`: Questa azione definisce il menu di scelta rapida per il grafico a linee, che contiene tre elementi per definire tre voci di menu. La forma e lo schema per ogni elemento sono gli stessi di quelli precedenti descritti. Proprio come per l'elemento `barChartActions`, la prima voce scrive i tre argomenti della funzione nella finestra della console del browser. Analogamente all'elemento `pieChartActions`, le seconde due voci creano un'istanza rispettivamente dei grafici a torta e a barre e ne eseguono il rendering. Le seconde due voci impostano anche le relative proprietà `ae.contextMenu` rispettivamente con i menu di scelta rapida `pieChartActions` e `barChartActions`.

2. Viene quindi eseguito il push di due espressioni di aggregazione nella matrice di espressioni di aggregazione `aes`, specificando la matrice `contextMenuActions` per ogni voce. Queste espressioni vengono usate con il controllo grafico a linee.

3. Infine, inizialmente viene eseguito il rendering solo del grafico a linee, da cui è possibile eseguire il rendering del grafico a torta e del grafico a barre in fase di esecuzione.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Lo screenshot mostra i grafici, con i rispettivi menu di scelta rapida popup. I grafici a torta e a barre sono stati creati in modo dinamico, usando le opzioni del menu di scelta rapida del grafico a linee.

[![Grafico a linee con menu di scelta rapida per creare il grafico a torta/a barre](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pennelli

I pennelli vengono usati per definire l'ambito di un intervallo di tempo, in modo da stabilire azioni come l'ingrandimento e l'esplorazione.

Il codice usato per illustrare i pennelli viene mostrato nell'esempio "Line Chart with Context Menu to Create Pie/Bar Chart" (Grafico a linee con menu di scelta rapida per creare il grafico a torta/a barre) precedente, che descrive i menu di scelta rapida popup.

1. Le azioni dei pennelli sono simili a quelle dei menu di scelta rapida, per il fatto che definiscono una serie di azioni personalizzate per il pennello. Ogni azione contiene una matrice con uno o più elementi. Ogni elemento definisce una singola voce di menu di scelta rapida:
   * `name`: Il testo che viene usato per la voce di menu: "Stampa parametri nella console".
   * `action`: Azione associata alla voce di menu, che è sempre una funzione anonima che accetta due argomenti. In questo caso, gli argomenti vengono scritti nella finestra della console del browser:
      * `fromTime`: il timestamp `from` la selezione del pennello.
      * `toTime`: il timestamp `to` la selezione del pennello.

2. Le azioni dei pennelli vengono aggiunte come un'altra proprietà delle opzioni del grafico. Notare la proprietà `brushContextMenuActions: brushActions` passata alla chiamata `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Grafico a linee con menu di scelta rapida per creare il grafico a torta/a barre tramite pennelli](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Accedere ed esplorare l'applicazione di esempio Time Series Insights e il relativo codice sorgente.
> * Usare le API nella libreria client JavaScript per Time Series Insights.
> * Usare JavaScript per creare e popolare controlli grafico con dati di Time Series Insights.

Come illustrato, l'applicazione di esempio Time Series Insights usa un set di dati dimostrativi. Per informazioni su come creare un ambiente Time Series Insights personalizzato e i relativi set di dati, passare all'articolo seguente:

> [!div class="nextstepaction"]
> [Esercitazione: Creare un ambiente Azure Time Series Insights](tutorial-create-populate-tsi-environment.md)

In alternativa, visualizzare i file di origine dell'applicazione di esempio Time Series Insights:

> [!div class="nextstepaction"]
> [Repository di app di esempio Time Series Insights](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Leggere la documentazione di riferimento dell'API client TSI:

> [!div class="nextstepaction"]
> [Documentazione di riferimento dell'API TSI](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
