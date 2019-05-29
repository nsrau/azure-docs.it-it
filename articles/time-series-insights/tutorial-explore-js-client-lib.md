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
ms.openlocfilehash: 7208c0d42cba0e7f04fc6876bf3ada9fa65a00d9
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991447"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Esercitazione: Esplorare la libreria client JavaScript per Azure Time Series Insights

La libreria client per Azure Time Series Insights basata su JavaScript D3 è stata sviluppata per consentire agli sviluppatori Web di eseguire query e visualizzare i dati archiviati in Time Series Insights. Questa esercitazione descrive la libreria client per Time Series Insights e il modello di programmazione tramite un'app di esempio ospitata.

Illustra come usare la libreria, come accedere ai dati Time Series Insights e come usare i controlli grafico per eseguire il rendering e la visualizzazione dei dati. Descrive inoltre come sperimentare con diversi tipi di grafici per visualizzare i dati. Al termine dell'esercitazione sarà possibile usare la libreria client per incorporare le funzionalità Time Series Insights in un'app Web personalizzata.

In particolare, il contenuto dell'esercitazione include:

> [!div class="checklist"]
> * L'applicazione di esempio Time Series Insights
> * La libreria client JavaScript per Time Series Insights
> * Il modo in cui l'applicazione di esempio usa la libreria per visualizzare i dati di Time Series Insights

> [!NOTE]
> * L'esercitazione usa una [demo Web di Time Series Insights](https://insights.timeseries.azure.com/clientsample) gratuita e ospitata.
> * I file di origine dell'app di esempio Time Series Insights sono reperibili nel [repository di esempi di GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Vedere la [documentazione di riferimento del client Time Series Insights](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

## <a name="video"></a>Video

Questo video descrive come usare JavaScript SDK open source per Time Series Insights:
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione usa la funzionalità **Strumenti di sviluppo** del browser. I Web browser moderni, come [Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/) e altri, solitamente forniscono l'accesso alla visualizzazione **Web Inspector** tramite il tasto di scelta rapida F12. Un altro modo per accedere alla vista è fare clic su una pagina Web e quindi scegliere **Esamina elemento**.

## <a name="time-series-insights-sample-application"></a>Applicazione di esempio Time Series Insights

Questa esercitazione illustra come usare l'applicazione di esempio gratuita ospitata Time Series Insights per esplorare il codice sorgente alla base dell'applicazione e la libreria client JavaScript per Time Series Insights. Descrive come interagire con Time Series Insights in JavaScript e visualizzare i dati tramite grafici.

1. Passare all'[applicazione di esempio Time Series Insights](https://insights.timeseries.azure.com/clientsample). Verrà visualizzata la richiesta di accesso seguente:

   [![Richiesta di accesso di esempio del client Time Series Insights](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Selezionare **Accedi** per immettere o selezionare le credenziali. Usare un account aziendale (Azure Active Directory) o un account personale (account Microsoft).

   [![Richiesta di credenziali di esempio del client Time Series Insights](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Dopo l'accesso, verrà visualizzata una pagina contenente i grafici popolati con i dati di Time Series Insights. L'account utente e l'opzione di **disconnessione** sono visibili in alto a destra:

   [![Pagina principale di esempio del client Time Series Insights dopo l'accesso](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Codice sorgente e struttura della pagina

Vediamo prima di tutto il [codice sorgente HTML e JavaScript](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) della pagina Web visualizzata:

1. Aprire **Strumenti di sviluppo** nel browser. Esaminare gli elementi HTML che costituiscono la pagina corrente, chiamati anche albero DOM o HTML.

1. Espandere gli elementi `<head>` e `<body>` e osservare le sezioni seguenti:

   * Nell'elemento `<head>` si trovano i metadati e le dipendenze della pagina che consentono l'esecuzione dell'app:
     * Un elemento `<script>` che consente di fare riferimento al file *adal.min.js* di Azure Active Directory Authentication Library (ADAL). Si tratta di una libreria JavaScript che fornisce l'autenticazione OAuth 2.0 (accesso) e l'acquisizione del token per accedere alle API.
     * Più elementi `<link>` per i fogli di stile (noti anche come *CSS*), come *sampleStyles.css* e *tsiclient.css*. I fogli di stile consentono di controllare i dettagli relativi allo stile visivo della pagina, come colori, tipi di carattere e spaziatura.
     * Un elemento `<script>` usato per fare riferimento alla libreria client JavaScript *tsiclient.js* per Time Series Insights. La pagina usa la libreria per chiamare le API del servizio Time Series Insights ed eseguire il rendering di controlli del grafico nella pagina.

     >[!NOTE]
     > * Il codice sorgente per la libreria JavaScript ADAL è disponibile nel [repository azure-activedirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * Il codice sorgente della libreria client JavaScript per Time Series Insights è disponibile nel [repository tsiclient](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * Nell'elemento `<body>` si trovano gli elementi `<div>`, che consentono di definire il layout deli elementi della pagina, e un altro elemento `<script>`:
     * Il primo elemento `<div>` specifica la finestra di dialogo di **accesso** (`id="loginModal"`).
     * Il secondo elemento `<div>` funge da elemento padre per:
       * Un elemento intestazione `<div>`, usato per i messaggi di stato e le informazioni di accesso nella parte superiore della pagina (`class="header"`).
       * Un elemento `<div>` per gli elementi del corpo della pagina rimanenti, inclusi tutti i grafici (`class="chartsWrapper"`).
       * Una sezione `<script>`, che contiene il codice JavaScript usato per controllare la pagina.

   [![Esempio di client Time Series Insights con Strumenti di sviluppo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Espandere l'elemento `<div class="chartsWrapper">` per visualizzare altri elementi `<div>` figlio. Questi elementi vengono usati per posizionare ogni esempio di controllo grafico. Sono presenti diverse coppie di elementi `<div>`, una per ogni esempio di grafico:

   * Il primo elemento (`class="rowOfCardsTitle"`) contiene un titolo descrittivo per riepilogare i dati rappresentati dai grafici. Ad esempio: `Static Line Charts With Full-Size Legends.`
   * Il secondo elemento (`class="rowOfCards"`) è un elemento padre, che contiene altri elementi `<div>` figlio che posizionano gli effettivi controlli grafico all'interno di una riga.

   [![Elementi div del corpo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Espandere l'elemento `<script type="text/javascript">` direttamente al di sotto dell'elemento `<div class="chartsWrapper">`. L'inizio della sezione JavaScript a livello di pagina, usata per gestire tutta la logica della pagina, ossia autenticazione, chiamata alle API del servizio Time Series Insights, rendering dei controlli grafico e altro ancora:

   [![Script del corpo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="time-series-insights-javascript-client-library-concepts"></a>Concetti della libreria client JavaScript per Time Series Insights

La libreria client Time Series Insights (*tsclient.js*) fornisce astrazioni per due importanti funzionalità JavaScript:

* **Metodi wrapper per chiamare le API di query di Time Series Insights**: API REST che è possibile usare per eseguire query sui dati di Time Series Insights usando espressioni aggregate. I metodi sono organizzati all'interno dello spazio dei nomi TsiClient.Server della libreria.

* **Metodi per la creazione e il popolamento di diversi tipi di controllo dei grafici**: Metodi che è possibile usare per visualizzare dati aggregati di Time Series Insights in una pagina Web. I metodi sono organizzati all'interno dello spazio dei nomi TsiClient.UX della libreria.

Tramite queste semplificazioni, gli sviluppatori possono creare più facilmente i componenti dei grafici dell'interfaccia utente in cui è possibile inserire i dati TSI.

### <a name="authentication"></a>Authentication

L'[applicazione di esempio Time Series Insights](https://insights.timeseries.azure.com/clientsample) è un'app a pagina singola con il supporto per l'autenticazione degli utenti ADAL OAuth 2.0:

1. Se si usa ADAL per l'autenticazione, è necessario che l'app client sia registrata in Azure Active Directory (Azure AD). In effetti, l'app a pagina singola viene registrata per l'uso del [flusso di concessione implicita OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. L'applicazione deve specificare alcune proprietà di registrazione in fase di esecuzione, tra cui il GUID client (`clientId`) e l'URI di reindirizzamento (`postLogoutRedirectUri`).
1. Successivamente l'applicazione richiede un *token di accesso* di Azure AD. Il token di accesso viene generato per un set limitato di autorizzazioni per un identificatore di servizio o API specifico (https:\//api.timeseries.azure.com). Le autorizzazioni per il token vengono rilasciate per conto dell'utente connesso. L'identificatore per il servizio o l'API è un'altra proprietà contenuta nella registrazione di Azure AD dell'applicazione.
1. Quando ADAL restituisce il token di accesso all'app, questo viene passato come *bearer token* durante l'accesso alle API del servizio Time Series Insights.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> Per altre informazioni sulle librerie di autenticazione Azure AD supportate da Microsoft, vedere la [documentazione di riferimento di Azure Active Directory Authentication Library](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries).

### <a name="control-identification"></a>Identificazione dei controlli

In questo esempio gli elementi `<div>` sono disposti nell'elemento `<body>` padre per fornire un layout sensibile per i controlli grafico visualizzati nella pagina.

Ogni elemento `<div>` specifica le proprietà per gli attributi visivi e di posizionamento dei controlli grafico. Le proprietà `id` dell'elemento HTML fungono da identificatori univoci da associare a controlli specifici per il rendering e l'aggiornamento dei dati visualizzati.

### <a name="aggregate-expressions"></a>Espressioni di aggregazione

Le API della libreria client Time Series Insights usano le espressioni di aggregazione:

* Un'espressione di aggregazione permette di costruire uno o più *termini di ricerca*.

* Le API client sono progettate per offrire funzionalità simili a un'altra app demo ([Time Series Insights Explorer](https://insights.timeseries.azure.com/demo)), che usa intervalli di ricerca, predicati `where`, misurazioni e valori `splitBy`.

* La maggior parte delle API della libreria client accetta una matrice di espressioni di aggregazione usate dal servizio per creare una query di dati di Time Series Insights.

### <a name="call-pattern"></a>Modello di chiamata

Il popolamento e il rendering dei controlli grafico seguono un modello generale. Questo modello generale può essere osservato nell'intera app di esempio e risulterà utile quando si usa libreria client:

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
   | `predicateObject` | Espressione di filtro dei dati |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | Nome della proprietà della misura usata | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | Aggregazioni della proprietà della misura desiderate | `['avg', 'min']` |
   | `searchSpan`      | Dimensioni di durata e intervallo dell'espressione di aggregazione | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Proprietà stringa in base alla quale eseguire la divisione (facoltativa: può essere null) | `{property: 'Station', type: 'String'}` |
   | `color`         | Colore degli oggetti di cui eseguire il rendering | `'pink'` |
   | `alias`           | Nome descrittivo per l'espressione di aggregazione | `'Factory3Temperature'` |
   | `contextMenuActions` | Matrice di azioni da associare agli oggetti serie temporale in una visualizzazione (facoltativa) | Per altre informazioni, vedere [Menu di scelta rapida popup](#pop-up-context-menus). |

1. Chiamare una query Time Series Insights usando le API TsiClient.Server per richiedere i dati aggregati:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **Parametri getAggregates**

   | Parametro | DESCRIZIONE | Esempio |
   | --------- | ----------- | ------- |
   | `token`     | Token di accesso per l'API Time Series Insights |  `authContext.getTsiToken()`<br />Per altre informazioni, vedere [Autenticazione](#authentication). |
   | `envFQDN`   | Nome di dominio completo (FQDN) per l'ambiente Time Series Insights | Dal portale di Azure. Ad esempio: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Matrice di espressioni di query Time Series Insights | Usare la variabile `aes` descritta in precedenza: `aes.map(function(ae){return ae.toTsx()}`. |

1. Trasformare in JSON il risultato compresso restituito dalla query Time Series Insights per la visualizzazione:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Creare un controllo grafico usando le API TsiClient.UX. Associarlo a uno degli elementi `<div>` della pagina:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Popolare il controllo grafico con gli oggetti dati JSON trasformati ed eseguirne il rendering nella pagina:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="render-controls"></a>Rendering dei controlli

La libreria client Time Series Insights offre otto controlli di analisi univoci e predefiniti:

* **grafico a linee**
* **grafico a torta**
* **grafico a barre**
* **heatmap**
* **controlli gerarchia**
* **griglia accessibile**
* **sequenze temporale di eventi discreti**
* **sequenze temporali di transizione di stato**

### <a name="line-chart-bar-chart-and-pie-chart-examples"></a>Esempi di grafico a linee, grafico a barre e grafico a torta

Esaminare il codice demo usato per il rendering di alcuni controlli grafico standard. Osservare il modello di programmazione e i modelli per la creazione di tali controlli. In particolare, esaminare il codice HTML al di sotto del commento `// Example 3/4/5`, che esegue il rendering dei controlli con valori di `id` HTML `chart3`, `chart4` e `chart5`.

Come specificato nel passaggio 3 della sezione [Codice sorgente e struttura della pagina](#page-source-and-structure), i controlli grafico sono disposti in righe sulla pagina. Per ognuno è presente una riga per un titolo descrittivo. In questo esempio i tre grafici vengono popolati sotto l'elemento `Multiple Chart Types From the Same Data` titolo `<div>` e sono associati ai tre elementi `<div>` sottostanti:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

La sezione di codice JavaScript seguente usa i modelli descritti in precedenza per creare espressioni di aggregazione Time Series Insights, usarle per eseguire query per dati di Time Series Insights e successivamente eseguire il rendering dei tre grafici. I tre tipi di grafico sono usati dallo spazio dei nomi tsiClient.ux: `LineChart`, `BarChart` e `PieChart`. I tipi di grafico consentono di creare ed eseguire il rendering dei rispettivi grafici. Tutti i tre grafici possono usare gli stessi dati delle espressioni di aggregazione `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

I tre grafici vengono visualizzati in questo modo dopo il rendering:

[![Più tipi di grafico dagli stessi dati](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Funzionalità avanzate

La libreria client di Time Series Insights include diverse funzionalità aggiuntive che è possibile usare per implementare le visualizzazioni dei dati in modo creativo.

### <a name="states-and-events"></a>Stati ed eventi

Una delle funzionalità avanzate disponibili è la possibilità di aggiungere transizioni di stato ed eventi discreti ai grafici. Questa funzionalità è utile per evidenziare eventi imprevisti, per generare avvisi e per creare passaggi di stato, ad esempio attivazione/disattivazione.

Esaminare il codice che circonda il commento `// Example 10`. Il codice esegue il rendering di un controllo linea sotto il titolo `Line Charts with Multiple Series Types`, associandolo all'elemento `<div>` con il valore di `id` HTML `chart10`.

I passaggi seguenti descrivono il processo:

1. Viene definita una struttura denominata `events4`, che conterrà gli elementi di modifica dello stato di cui tenere traccia. La struttura contiene:

   * Una chiave stringa denominata `Component States`.
   * Una matrice di oggetti valore che rappresentano gli stati. Ogni oggetto include:
     * Una chiave stringa che contiene un timestamp ISO JavaScript.
     * Una matrice che contiene le caratteristiche dello stato: un colore e una descrizione.

1. Viene definita la struttura `events5` per `Incidents`, che contiene una matrice di elementi evento di cui tenere traccia. La struttura della matrice ha la stessa forma di quella descritta per `events4`.

1. Viene eseguito il rendering del grafico a linee, che passa nelle due strutture con i parametri delle opzioni del grafico: `events:` e `states:`. Notare i parametri delle altre opzioni, per specificare un elemento `tooltip:`, `theme:` o `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Le finestre popup/i marcatori a forma di rombo usati per indicare gli eventi imprevisti e le finestre popup/barre colorate lungo la scala cronologica indicano modifiche di stato:

[![Grafici a linee con più tipi di serie](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Menu di scelta rapida popup

Un'altra funzionalità avanzate è la possibilità di creare menu di scelta rapida personalizzati (menu a comparsa visualizzati con il pulsante destro del mouse). I menu di scelta rapida personalizzati sono utili per l'attivazione di azioni e passaggi logici successivi nell'ambito dell'applicazione.

Esaminare il codice intorno al commento `// Example 13/14/15`. Questo codice inizialmente esegue il rendering di un grafico a linee sotto il titolo `Line Chart with Context Menu to Create Pie/Bar Chart`. Il grafico è associato all'elemento `<div>` con il valore di `id` HTML `chart13`.

Usando i menu di scelta rapida, il grafico a linee offre la possibilità di creare in modo dinamico un grafico a torta e un grafico a barre, associati a elementi `<div>` con ID `chart14` e `chart15`. Sia il grafico a torta che il grafico a barre usano i menu a scelta rapida anche per abilitare funzionalità proprie: la possibilità rispettivamente di copiare dati dal grafico a torta al grafico a barre e stampare i dati del grafico a barre nella finestra della console del browser.

I passaggi seguenti descrivono il processo:

1. Viene definita una serie di azioni personalizzate. Ogni azione contiene una matrice con uno o più elementi. Ogni elemento definisce una singola voce di menu di scelta rapida:

   * `barChartActions`: Questa azione definisce il menu di scelta rapida per il grafico a torta, che contiene un elemento per definire una singola voce:
     * `name`: Il testo che viene usato per la voce di menu: "Stampa parametri nella console".
     * `action`: L'azione associata alla voce di menu. È sempre una funzione anonima che accetta tre argomenti in base all'espressione di aggregazione usata per creare il grafico. In questo caso, gli argomenti vengono scritti nella finestra della console del browser:
       * `ae`: La matrice di espressioni di aggregazione.
       * `splitBy`: il valore di `splitBy`.
       * `timestamp`: Il timestamp.

   * `pieChartActions`: Questa azione definisce il menu di scelta rapida per il grafico a barre, che contiene un elemento per definire una singola voce. La forma e lo schema sono gli stessi di quelli dell'elemento `barChartActions` descritto precedente, ma la funzione `action` è notevolmente diversa, in quanto crea un'istanza del grafico a barre e ne esegue il rendering. L'argomento `ae` viene usato per specificare la matrice di espressioni di aggregazione passata in fase di esecuzione all'apertura della voce di menu. La funzione imposta anche la proprietà `ae.contextMenu` con il menu di scelta rapida `barChartActions`.
   * `contextMenuActions`: Questa azione definisce il menu di scelta rapida per il grafico a linee, che contiene tre elementi per definire tre voci di menu. La forma e lo schema per ogni elemento sono gli stessi di quelli degli elementi descritti precedentemente. Proprio come per l'elemento `barChartActions`, la prima voce scrive i tre argomenti della funzione nella finestra della console del browser. Analogamente all'elemento `pieChartActions`, le seconde due voci creano un'istanza rispettivamente dei grafici a torta e a barre e ne eseguono il rendering. Le seconde due voci impostano anche le relative proprietà `ae.contextMenu` rispettivamente con i menu di scelta rapida `pieChartActions` e `barChartActions`.

1. Viene eseguito il push di due espressioni di aggregazione nella matrice di espressioni di aggregazione `aes`. Specificano la matrice `contextMenuActions` per ogni elemento. Queste espressioni vengono usate con il controllo grafico a linee.

1. Inizialmente viene eseguito il rendering solo del grafico a linee, da cui è possibile eseguire il rendering del grafico a torta e del grafico a barre in fase di esecuzione.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Lo screenshot seguente mostra i grafici, con i rispettivi menu di scelta rapida popup. Il grafico a torta e il grafico a barre sono stati creati in modo dinamico, usando le opzioni del menu di scelta rapida del grafico a linee.

[![Grafico a linee con menu di scelta rapida per creare il grafico a torta e il grafico a barre](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pennelli

È possibile usare i pennelli per definire l'ambito di un intervallo di tempo, in modo da stabilire azioni come l'ingrandimento e l'esplorazione.

Il codice usato per illustrare i pennelli è visualizzato nell'esempio `Line Chart with Context Menu to Create Pie/Bar Chart` in cui vengono descritti i menu di scelta rapida popup.

* Le azioni dei pennelli sono simili a quelle dei menu di scelta rapida, per il fatto che definiscono una serie di azioni personalizzate per il pennello. Ogni azione contiene una matrice con uno o più elementi. Ogni elemento definisce una singola voce di menu di scelta rapida:
   * `name`: Il testo che viene usato per la voce di menu: "Stampa parametri nella console".
   * `action`: Azione associata alla voce di menu, che è sempre una funzione anonima che accetta due argomenti. In questo caso, gli argomenti vengono scritti nella finestra della console del browser:
     * `fromTime`: il timestamp `from` la selezione del pennello.
     * `toTime`: il timestamp `to` la selezione del pennello.

* Le azioni dei pennelli vengono aggiunte come un'altra proprietà delle opzioni del grafico. La proprietà `brushContextMenuActions: brushActions` passata alla chiamata `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Grafico a linee con menu di scelta rapida per creare il grafico a torta e il grafico a barre usando i pennelli](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Accedere ed esplorare l'applicazione di esempio Time Series Insights e il relativo codice sorgente
> * Usare le API nella libreria client JavaScript per Time Series Insights
> * Usare JavaScript per creare e popolare controlli grafico con dati di Time Series Insights

L'applicazione di esempio Time Series Insights usa un set di dati demo. Per informazioni su come creare un ambiente Time Series Insights personalizzato e i relativi set di dati, leggere l'articolo seguente:

> [!div class="nextstepaction"]
> [Esercitazione: Creare un ambiente Azure Time Series Insights](tutorial-create-populate-tsi-environment.md)

In alternativa, visualizzare i file di origine dell'applicazione di esempio Time Series Insights:

> [!div class="nextstepaction"]
> [Repository dell'applicazione di esempio Time Series Insights](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Vedere la documentazione di riferimento dell'API del client Time Series Insights:

> [!div class="nextstepaction"]
> [Documentazione di riferimento dell'API del client Time Series Insights](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
