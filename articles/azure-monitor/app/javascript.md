---
title: Azure Application Insights per app Web JavaScript | Microsoft Docs
description: Ottenere i conteggi delle visualizzazioni pagina e delle sessioni, i dati client Web e la traccia dei modelli di utilizzo. Rilevare le eccezioni e i problemi di prestazioni nelle pagine Web JavaScript.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2019
ms.openlocfilehash: 6bb61f419f4c6d277a9b1c666db92595642cb0e6
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706603"
---
# <a name="application-insights-for-web-pages"></a>Application Insights per pagine Web

Scoprire le prestazioni e l'utilizzo della pagina Web o dell'app. Se si aggiungono [Application Insights](app-insights-overview.md) allo script di pagina, si ottengono i tempi di caricamento delle pagine e le chiamate AJAX, i conteggi e i dettagli delle eccezioni del browser e degli errori Ajax, nonché i conteggi degli utenti e delle sessioni. Tutti questi elementi possono essere segmentati per pagina, sistema operativo client e versione del browser, posizione geografica e altre dimensioni. È possibile impostare avvisi relativi al numero di errori o rallentare il caricamento delle pagine. Inoltre, inserendo le chiamate di traccia nel codice JavaScript, è possibile rilevare come vengono usate le diverse funzionalità dell'applicazione della pagina Web.

Application Insights è compatibile con tutte le pagine Web, con una minima aggiunta di codice JavaScript. Se il servizio Web è [Java](java-get-started.md) o [ASP.NET](asp-net.md), è possibile usare gli SDK sul lato server insieme all'SDK JavaScript sul lato client per ottenere una conoscenza end-to-end delle prestazioni dell'applicazione.

## <a name="adding-the-javascript-sdk"></a>Aggiunta di JavaScript SDK

1. Per prima cosa è necessaria una risorsa Application Insights. Se non si dispone già di una risorsa e di una chiave di strumentazione, seguire le [istruzioni per creare una nuova risorsa](create-new-resource.md).
2. Copiare la chiave di strumentazione dalla risorsa in cui si vogliono inviare i dati di telemetria JavaScript.
3. Aggiungere Application Insights JavaScript SDK all'app o alla pagina Web tramite una delle due opzioni seguenti:
    * [Installazione di NPM](#npm-based-setup)
    * [Frammento JavaScript](#snippet-based-setup)

> [!IMPORTANT]
> È sufficiente usare uno dei metodi seguenti per aggiungere la Application Insights JavaScript SDK all'applicazione. Se si usa l'installazione basata su NPM, non usare l'installazione basata sul frammento. Lo stesso vale per lo scenario inverso quando si usa l'approccio basato su frammenti di codice, non usare anche la configurazione basata su NPM. 

### <a name="npm-based-setup"></a>configurazione basata su NPM

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Configurazione basata su frammenti

Se l'app non usa NPM, è possibile instrumentare direttamente le pagine Web con Application Insights incollando il frammento di codice nella parte superiore di ogni pagina. Preferibilmente, dovrebbe essere il primo script nella sezione `<head>`, in modo da poter monitorare eventuali problemi potenziali con tutte le dipendenze. Se si usa l'app Server blazer, aggiungere il frammento di codice nella parte superiore del file `_Host.cshtml` nella sezione `<head>`.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Invio di dati di telemetria al portale di Azure

Per impostazione predefinita, Application Insights JavaScript SDK raccoglie un numero di elementi di telemetria utili per determinare l'integrità dell'applicazione e l'esperienza utente sottostante. Le aree includono:

- **Eccezioni non rilevate** nell'app, incluse informazioni su
    - Analisi dello stack
    - Dettagli dell'eccezione e messaggio che accompagna l'errore
    - Numero di colonna & riga di errore
    - URL in cui è stato generato un errore
- Richieste di **dipendenza di rete** effettuate dall'app **XHR** e **Fetch** (fetch Collection è disabilitato per impostazione predefinita) richieste, include informazioni su
    - URL dell'origine di dipendenza
    - Comando & metodo usato per richiedere la dipendenza
    - Durata della richiesta
    - Codice risultato e stato di esito positivo della richiesta
    - ID (se presente) dell'utente che effettua la richiesta
    - Contesto di correlazione (se presente) in cui viene effettuata la richiesta
- **Informazioni utente** (ad esempio, località, rete, IP)
- **Informazioni sul dispositivo** (ad esempio browser, sistema operativo, versione, lingua, risoluzione, modello)
- **Informazioni sulla sessione**

### <a name="telemetry-initializers"></a>Inizializzatori di telemetria
Gli inizializzatori di telemetria vengono usati per modificare il contenuto dei dati di telemetria raccolti prima di essere inviati dal browser dell'utente. Possono anche essere usati per impedire l'invio di alcuni dati di telemetria, restituendo `false`. È possibile aggiungere più inizializzatori di telemetria all'istanza di Application Insights e vengono eseguiti in ordine di aggiunta.

L'argomento di input per `addTelemetryInitializer` è un callback che accetta un [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) come argomento e restituisce un `boolean` o `void`. Se viene restituito `false`, l'elemento di telemetria non viene inviato, in caso contrario procede al successivo inizializzatore di telemetria, se presente, o viene inviato all'endpoint della raccolta dei dati di telemetria.

Esempio di uso degli inizializzatori di telemetria:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```
## <a name="configuration"></a>Configurazione
La maggior parte dei campi di configurazione è denominata in modo che sia possibile impostarla su false. Tutti i campi sono facoltativi tranne `instrumentationKey`.

| name | Predefinito | Description |
|------|---------|-------------|
| instrumentationKey | Null | **Obbligatorio**<br>Chiave di strumentazione ottenuta dal portale di Azure. |
| accountId | Null | ID account facoltativo, se l'app raggruppa gli utenti in account. Spazi, virgole, punti e virgola, uguali o barre verticali |
| sessionRenewalMs | 1,8 milioni | Una sessione viene registrata se l'utente è inattivo per questo periodo di tempo in millisecondi. Il valore predefinito è 30 minuti |
| sessionExpirationMs | 86,4 milioni | Una sessione viene registrata se continua per questo periodo di tempo in millisecondi. Il valore predefinito è 24 ore |
| maxBatchSizeInBytes | 10000 | Dimensioni massime del batch di telemetria. Se un batch supera questo limite, viene immediatamente inviato e viene avviato un nuovo batch |
| maxBatchInterval | 15000 | Durata della telemetria batch per prima dell'invio (millisecondi) |
| disableExceptionTracking | false | Se true, le eccezioni non sono autocollected. Il valore predefinito è false. |
| disableTelemetry | false | Se true, i dati di telemetria non vengono raccolti o inviati. Il valore predefinito è false. |
| enableDebug | false | Se true, i dati di debug **interni** vengono generati come eccezione **anziché** essere registrati, indipendentemente dalle impostazioni di registrazione dell'SDK. Il valore predefinito è false. <br>***Nota:*** Se si abilita questa impostazione, la telemetria verrà eliminata ogni volta che si verifica un errore interno. Questo può essere utile per identificare rapidamente i problemi con la configurazione o l'utilizzo dell'SDK. Se non si vogliono perdere i dati di telemetria durante il debug, provare a usare `consoleLoggingLevel` o `telemetryLoggingLevel` invece di `enableDebug`. |
| loggingLevelConsole | 0 | Registra gli errori **interni** di Application Insights alla console. <br>0: disattivato, <br>1: solo errori critici, <br>2: tutto (errori & avvisi) |
| loggingLevelTelemetry | 1 | Invia errori **interni** di Application Insights come dati di telemetria. <br>0: disattivato, <br>1: solo errori critici, <br>2: tutto (errori & avvisi) |
| diagnosticLogInterval | 10000 | interno Intervallo di polling (in MS) per la coda di registrazione interna |
| samplingPercentage | 100 | Percentuale di eventi che verranno inviati. Il valore predefinito è 100, ovvero tutti gli eventi vengono inviati. Impostare questa impostazione se si desidera mantenere il limite di dati per le applicazioni su larga scala. |
| autoTrackPageVisitTime | false | Se true, in una pagina di visualizzazione, il tempo di visualizzazione della pagina instrumentata precedente viene rilevato e inviato come dati di telemetria e viene avviato un nuovo timer per la visualizzazione corrente. Il valore predefinito è false. |
| disableAjaxTracking | false | Se il valore è true, le chiamate AJAX non vengono raccolte. Il valore predefinito è false. |
| disableFetchTracking | true | Se il valore è true, le richieste di recupero non vengono raccolte. Il valore predefinito è true |
| overridePageViewDuration | false | Se true, il comportamento predefinito di trackPageView viene modificato per registrare la fine dell'intervallo di durata della visualizzazione pagina quando viene chiamato trackPageView. Se false e non viene fornita alcuna durata personalizzata a trackPageView, le prestazioni della visualizzazione pagina vengono calcolate usando l'API di intervallo di navigazione. Il valore predefinito è false. |
| maxAjaxCallsPerView | 500 | Default 500-controlla il numero di chiamate AJAX che verranno monitorate per ogni visualizzazione pagina. Impostare su-1 per monitorare tutte le chiamate AJAX (illimitate) nella pagina. |
| disableDataLossAnalysis | true | Se false, i buffer dei mittenti di telemetria interni verranno controllati all'avvio per gli elementi non ancora inviati. |
| disableCorrelationHeaders | false | Se false, l'SDK aggiungerà due intestazioni (' Request-ID ' è request-context ') a tutte le richieste di dipendenza per metterle in correlazione con richieste corrispondenti sul lato server. Il valore predefinito è false. |
| correlationHeaderExcludedDomains |  | Disabilitare le intestazioni di correlazione per domini specifici |
| correlationHeaderDomains |  | Abilitare le intestazioni di correlazione per domini specifici |
| disableFlushOnBeforeUnload | false | Valore predefinito false. Se true, il metodo Flush non verrà chiamato quando viene attivato l'evento onBeforeUnload |
| enableSessionStorageBuffer | true | Valore predefinito true. Se true, il buffer con tutti i dati di telemetria non inviati viene archiviato nell'archiviazione della sessione. Il buffer viene ripristinato al caricamento della pagina |
| isCookieUseDisabled | false | Valore predefinito false. Se true, l'SDK non archivia né legge i dati dai cookie.|
| cookieDomain | Null | Dominio cookie personalizzato. Questa operazione è utile se si desidera condividere Application Insights cookie tra sottodomini. |
| isRetryDisabled | false | Valore predefinito false. Se false, riprovare su 206 (operazione parzialmente riuscita), 408 (timeout), 429 (troppe richieste), 500 (errore interno del server), 503 (servizio non disponibile) e 0 (offline, solo se rilevato) |
| isStorageUseDisabled | false | Se true, l'SDK non archivia né legge i dati dall'archiviazione locale e della sessione. Il valore predefinito è false. |
| isBeaconApiDisabled | true | Se false, l'SDK invierà tutti i dati di telemetria usando l' [API Beacon](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | false | Valore predefinito false. Quando la scheda è chiusa, l'SDK invierà tutti i dati di telemetria rimanenti usando l' [API Beacon](https://www.w3.org/TR/beacon) |
| sdkExtension | Null | Imposta il nome dell'estensione SDK. Sono consentiti solo caratteri alfabetici. Il nome dell'estensione viene aggiunto come prefisso al tag ' ai. Internal. sdkVersion ' (ad esempio,' ext_javascript: 2.0.0'). Il valore predefinito è Null. |
| isBrowserLinkTrackingEnabled | false | Il valore predefinito è false. Se true, l'SDK tiene traccia di tutte le richieste di [browser link](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) . |
| appId | Null | AppId viene utilizzato per la correlazione tra le dipendenze AJAX che si verificano sul lato client con le richieste lato server. Quando l'API Beacon è abilitata, non può essere usata automaticamente, ma può essere impostata manualmente nella configurazione. Il valore predefinito è null |
| enableCorsCorrelation | false | Se true, l'SDK aggiungerà due intestazioni (' Request-ID ' è request-context ') a tutte le richieste CORS per correlare le dipendenze AJAX in uscita con le richieste corrispondenti sul lato server. Il valore predefinito è false |
| namePrefix | non definito | Valore facoltativo che verrà usato come nome suffisso per localStorage e il nome del cookie.
| enableAutoRouteTracking | false | Rilevare automaticamente le modifiche del route nelle applicazioni a pagina singola (SPA). Se true, ogni modifica della route invierà una nuova visualizzazione a Application Insights. Anche le modifiche della route hash (`example.com/foo#bar`) vengono registrate come nuove visualizzazioni di pagina.
| enableRequestHeaderTracking | false | Se true, vengono rilevate le intestazioni della richiesta di recupero & AJAX, il valore predefinito è false.
| enableResponseHeaderTracking | false | Se true, vengono rilevate le intestazioni di risposta della richiesta di recupero & AJAX, il valore predefinito è false.
| distributedTracingMode | `DistributedTracingModes.AI` | Imposta la modalità di traccia distribuita. Se è impostata la modalità AI_AND_W3C o W3C, le intestazioni del contesto di traccia W3C (traceparent/tracestate) verranno generate e incluse in tutte le richieste in uscita. AI_AND_W3C viene fornito per la compatibilità con le versioni precedenti di tutti i servizi Application Insights instrumentati.

## <a name="single-page-applications"></a>Applicazioni a pagina singola

Per impostazione predefinita, questo SDK **non** gestirà la modifica della Route basata sullo stato che si verifica nelle applicazioni a pagina singola. Per abilitare il rilevamento automatico delle modifiche della route per l'applicazione a pagina singola, è possibile aggiungere `enableAutoRouteTracking: true` alla configurazione di installazione.

Attualmente si offre un plug-in [React](#react-extensions) separato che è possibile inizializzare con questo SDK. Verrà anche eseguito il rilevamento delle modifiche delle route, oltre a raccogliere altri dati di [telemetria specifici di React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md).

## <a name="react-extensions"></a>Estensioni React

| Estensioni |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [React Native](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Esplorare i dati sul lato client e sul browser

Per visualizzare i dati del browser/lato client, passare a **metriche** e aggiungere singole metriche a cui si è interessati: 

![](./media/javascript/page-view-load-time.png)

È anche possibile visualizzare i dati da JavaScript SDK tramite l'esperienza del browser nel portale.

Selezionare **browser** , quindi scegliere **errori** o **prestazioni**.

![](./media/javascript/browser.png)

### <a name="performance"></a>Performance 

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Dipendenze

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analytics 

Per eseguire una query sui dati di telemetria raccolti da JavaScript SDK, selezionare il pulsante **Visualizza nei log (Analytics)** . Con l'aggiunta di un `where` dichiarazione di `client_Type == "Browser"`, verranno visualizzati solo i dati di JavaScript SDK e tutti i dati di telemetria sul lato server raccolti da altri SDK verranno esclusi.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Supporto della mappa di origine

Il minimizzati stack dei dati di telemetria delle eccezioni può essere unminified nel portale di Azure. Tutte le integrazioni esistenti nel pannello Dettagli eccezione funzioneranno con il nuovo unminified stack. Il trascinamento della selezione della mappa di origine unminifying supporta tutti gli SDK JS esistenti e futuri (+ node. JS), pertanto non è necessario aggiornare la versione dell'SDK. Per visualizzare il stack unminified,
1. Selezionare un elemento di telemetria delle eccezioni nel portale di Azure per visualizzare i relativi dettagli della transazione end-to-end.
2. Identificare i mapping di origine corrispondenti a questo stack di chiamate. La mappa di origine deve corrispondere al file di origine di un stack frame, ma con suffisso `.map`
3. Trascinare e rilasciare i mapping di origine nello stack di chiamate nel portale di Azure ![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web Basic

Per un'esperienza semplificata, è invece possibile installare la versione di base di Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Questa versione include il numero minimo di caratteristiche e funzionalità e si basa su di esso per compilarlo nel modo appropriato. Ad esempio, non esegue alcuna raccolta (eccezioni non rilevate, AJAX e così via). Le API per inviare determinati tipi di dati di telemetria, ad esempio `trackTrace`, `trackException`e così via, non sono incluse in questa versione, quindi è necessario fornire il proprio wrapper. L'unica API disponibile è `track`. Un [esempio](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) è disponibile qui.

## <a name="examples"></a>esempi

Per esempi eseguibili, vedere [esempi di Application Insights JavaScript SDK](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Aggiornamento dalla versione precedente di Application Insights

Modifiche di rilievo nella versione SDK v2:
- Per consentire una migliore firma API, alcune delle chiamate API, ad esempio trackPageView, trackexception sono state aggiornate. L'esecuzione in IE8 o versioni precedenti del browser non è supportata.
- La busta di telemetria presenta modifiche al nome del campo e alla struttura a causa degli aggiornamenti dello schema dati.
- Spostamento `context.operation` `context.telemetryTrace`. Sono stati modificati anche alcuni campi (`operation.id` --> `telemetryTrace.traceID`)
  - Se si vuole aggiornare manualmente l'ID di visualizzazione corrente (ad esempio, nelle app SPA), questa operazione può essere eseguita con `appInsights.properties.context.telemetryTrace.traceID = Util.newId()`

Se si usa l'SDK di produzione di Application Insights (1.0.20) corrente e si vuole verificare se il nuovo SDK funziona in fase di esecuzione, aggiornare l'URL a seconda dello scenario di caricamento dell'SDK corrente.

- Scaricare tramite lo scenario della rete CDN: aggiornare il frammento di codice attualmente usato per puntare all'URL seguente:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- scenario NPM: chiamare `downloadAndSetup` per scaricare lo script ApplicationInsights completo dalla rete CDN e inizializzarlo con la chiave di strumentazione:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Eseguire il test in un ambiente interno per verificare che la telemetria del monitoraggio funzioni come previsto. Se tutto funziona, aggiornare le firme dell'API in modo appropriato alla versione SDK v2 e distribuirle negli ambienti di produzione.

## <a name="sdk-performanceoverhead"></a>Prestazioni/overhead SDK

A soli 25 KB gzippato e impiegando solo ~ 15 ms per l'inizializzazione, Application Insights aggiunge una quantità trascurabile di LoadTime al sito Web. Usando il frammento di codice, vengono caricati rapidamente i componenti minimi della libreria. Nel frattempo, lo script completo viene scaricato in background.

Durante il download dello script dalla rete CDN, tutte le tracce della pagina vengono accodate. Al termine dell'inizializzazione asincrona dello script scaricato, verranno rilevati tutti gli eventi accodati. Di conseguenza, non si perderanno i dati di telemetria durante l'intero ciclo di vita della pagina. Questo processo di installazione fornisce alla pagina un sistema di analisi trasparente, invisibile agli utenti.

> Riepilogo:
> - gzippato di **25 KB**
> - tempo totale di inizializzazione di **15 ms**
> - **Nessun** rilevamento perso durante il ciclo di vita della pagina

## <a name="browser-support"></a>Supporto browser

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
✔ Più recenti di Chrome |  ✔ Più recenti di Firefox | Internet Explorer 9 + & Edge ✔ | Opera più recente ✔ | Safari più recenti ✔ |

## <a name="open-source-sdk"></a>SDK open source

Il Application Insights JavaScript SDK è open source per visualizzare il codice sorgente o per contribuire al progetto visitare il [repository GitHub ufficiale](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next"></a> Passaggi successivi
* [Tenere traccia dell'utilizzo](usage-overview.md)
* [Metriche ed eventi personalizzati](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)

