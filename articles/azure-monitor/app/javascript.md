---
title: applicazione Azure Insights per app Web JavaScript
description: Ottenere i conteggi delle visualizzazioni pagina e delle sessioni, i dati dei client Web, le applicazioni a pagina singola (SPA) e i modelli di utilizzo. Rilevare le eccezioni e i problemi di prestazioni nelle pagine Web JavaScript.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 4b3d489477a0ee0cc201d4383b5ed960de515c7d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517111"
---
# <a name="application-insights-for-web-pages"></a>Application Insights per pagine Web

Scoprire le prestazioni e l'utilizzo della pagina Web o dell'app. Se si aggiungono [Application Insights](app-insights-overview.md) allo script di pagina, si ottengono i tempi di caricamento delle pagine e le chiamate AJAX, i conteggi e i dettagli delle eccezioni del browser e degli errori Ajax, nonché i conteggi degli utenti e delle sessioni. Tutti questi elementi possono essere segmentati per pagina, sistema operativo client e versione del browser, posizione geografica e altre dimensioni. È possibile impostare avvisi relativi al numero di errori o rallentare il caricamento delle pagine. Inoltre, inserendo le chiamate di traccia nel codice JavaScript, è possibile rilevare come vengono usate le diverse funzionalità dell'applicazione della pagina Web.

Application Insights è compatibile con tutte le pagine Web, con una minima aggiunta di codice JavaScript. Se il servizio Web è [Java](java-get-started.md) o [ASP.NET](asp-net.md), è possibile usare gli SDK sul lato server insieme all'SDK JavaScript sul lato client per ottenere una conoscenza end-to-end delle prestazioni dell'applicazione.

## <a name="adding-the-javascript-sdk"></a>Aggiunta di JavaScript SDK

1. Per prima cosa è necessaria una risorsa Application Insights. Se non si dispone già di una risorsa e di una chiave di strumentazione, seguire le [istruzioni per creare una nuova risorsa](create-new-resource.md).
2. Copiare la _chiave di strumentazione_ (nota anche come "iKey") per la risorsa in cui si vuole inviare i dati di telemetria JavaScript (dal passaggio 1). Che verrà aggiunto all' `instrumentationKey` impostazione di Application Insights JavaScript SDK.
3. Aggiungere Application Insights JavaScript SDK all'app o alla pagina Web tramite una delle due opzioni seguenti:
    * [Installazione di NPM](#npm-based-setup)
    * [Frammento JavaScript](#snippet-based-setup)

> [!IMPORTANT]
> Usare un solo metodo per aggiungere JavaScript SDK all'applicazione. Se si usa il programma di installazione di NPM, non usare il frammento e viceversa.

> [!NOTE]
> Il programma di installazione di NPM installa JavaScript SDK come dipendenza dal progetto, abilitando IntelliSense, mentre il frammento recupera l'SDK in fase di esecuzione. Entrambi supportano le stesse funzionalità. Tuttavia, gli sviluppatori che desiderano un maggior numero di eventi personalizzati e la configurazione scelgono generalmente la configurazione di NPM, mentre gli utenti che cercano di abilitare rapidamente le analisi Web predefinite optano per il frammento di codice.

### <a name="npm-based-setup"></a>configurazione basata su NPM

Installare tramite NPM.

```sh
npm i --save @microsoft/applicationinsights-web
```

> *Nota:* **le digitazioni sono incluse in questo pacchetto**, pertanto **non** è necessario installare un pacchetto di tipi separati.
    
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

Se l'app non usa NPM, è possibile instrumentare direttamente le pagine Web con Application Insights incollando il frammento di codice nella parte superiore di ogni pagina. Preferibilmente, dovrebbe essere il primo script nella sezione, in `<head>` modo da poter monitorare eventuali problemi potenziali con tutte le dipendenze e, facoltativamente, eventuali errori di JavaScript. Se si usa l'app Server blazer, aggiungere il frammento di codice nella parte superiore del file `_Host.cshtml` nella `<head>` sezione.

Per semplificare il rilevamento della versione del frammento usata dall'applicazione, a partire dalla versione 2.5.5, l'evento di visualizzazione pagina includerà un nuovo tag "ai. Internal. snippet" che conterrà la versione del frammento identificata.

Il frammento di codice corrente (elencato di seguito) verrà identificato come versione "3".

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,u="script",k="instrumentationKey",D="ingestionendpoint",C="disableExceptionTracking",E="ai.device.",I="toLowerCase",b="crossOrigin",w="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"4",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[I](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,p,l,u;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][I]()]=i[1])}if(!e[D]){var r=e.endpointsuffix,o=r?e.location:null;e[D]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[k]||d[k]||"",p=s[D],l=p?p+"/v2/track":config.endpointUrl,(u=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=l,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),u.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,l)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:w,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(w,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(u,l))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(u);n.src=h;var e=y[b];return!e&&""!==e||"undefined"==n[b]||(n[b]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(u)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[C]&&!0!==s[C]){method="onerror",t(["_"+method]);var c=T[method];T[method]=function(e,t,n,a,i){var r=c&&c(e,t,n,a,i);return!0!==r&&m["_"+method]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);(T[t]=n).queue&&0===n.queue.length&&n.trackPageView({})}(window,document,{
src: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js", // The SDK URL Source
//name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
//ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
//useXhr: 1, // Use XHR instead of fetch to report failures (if available),
//crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag 
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    /* ...Other Configuration Options... */
}});
</script>
```

> [!NOTE]
> Per migliorare la leggibilità e ridurre i possibili errori di JavaScript, tutte le opzioni di configurazione possibili sono elencate in una nuova riga nel codice del frammento precedente, se non si vuole modificare il valore di una riga commentata, è possibile rimuoverla.


#### <a name="reporting-script-load-failures"></a>Segnalazione degli errori di caricamento degli script

Questa versione del frammento rileva e segnala errori durante il caricamento dell'SDK dalla rete CDN come eccezione al portale di monitoraggio di Azure (nel &gt; browser eccezioni degli errori &gt; ), questa eccezione fornisce la visibilità degli errori di questo tipo, in modo da tenere presente che l'applicazione non segnala i dati di telemetria (o altre eccezioni) come previsto. Questo segnale rappresenta una misura importante per capire che i dati di telemetria sono stati persi perché l'SDK non è stato caricato o inizializzato, il che può comportare:
- Sottosegnalazione del modo in cui gli utenti utilizzano o tentano di utilizzare il sito;
- Dati di telemetria mancanti sul modo in cui gli utenti finali usano il sito;
- Errori JavaScript mancanti che potrebbero potenzialmente impedire agli utenti finali di usare correttamente il sito.

Per informazioni dettagliate su questa eccezione, vedere la pagina relativa alla risoluzione dei problemi di [caricamento dell'SDK](javascript-sdk-load-failure.md) .

La creazione di report di questo errore come eccezione per il portale non usa l'opzione ```disableExceptionTracking``` di configurazione della configurazione di Application Insights e, di conseguenza, se si verifica questo errore, verrà sempre segnalato dal frammento, anche se il supporto di Window. OnError è disabilitato.

La creazione di report degli errori di caricamento SDK non è supportata in IE 8 (o meno). Questo consente di ridurre la dimensione minimizzati del frammento di codice supponendo che la maggior parte degli ambienti non sia esclusivamente di IE 8 o meno. Se si dispone di questo requisito e si desidera ricevere queste eccezioni, è necessario includere una versione fetch Poly Fill o create your own snippet che utilizza ```XDomainRequest``` anziché ```XMLHttpRequest``` , è consigliabile utilizzare il [codice sorgente del frammento fornito](https://github.com/microsoft/ApplicationInsights-JS/blob/master/AISKU/snippet/snippet.js) come punto di partenza.

> [!NOTE]
> Se si usa una versione precedente del frammento di codice, è consigliabile eseguire l'aggiornamento alla versione più recente in modo da ricevere questi problemi precedentemente non segnalati.

#### <a name="snippet-configuration-options"></a>Opzioni di configurazione del frammento

Tutte le opzioni di configurazione sono state spostate verso la fine dello script per evitare di introdurre accidentalmente errori JavaScript che non limitano il caricamento dell'SDK, ma anche la disabilitazione della segnalazione dell'errore.

Ogni opzione di configurazione è illustrata sopra in una nuova riga, se non si vuole eseguire l'override del valore predefinito di un elemento elencato come [facoltativo] è possibile rimuovere tale riga per ridurre al minimo le dimensioni risultanti della pagina restituita.

Le opzioni di configurazione disponibili sono 

| Nome | Type | Descrizione
|------|------|----------------
| src | stringa **[obbligatorio]** | URL completo da cui caricare l'SDK. Questo valore viene usato per l'attributo "src" di uno &lt; script/tag aggiunto dinamicamente &gt; . È possibile usare il percorso di rete CDN pubblico o un host privato.
| name | String *[facoltativo]* | Nome globale per l'SDK inizializzato. l'impostazione predefinita è appInsights. ```window.appInsights```Sarà quindi un riferimento all'istanza inizializzata. Nota: se si specifica un valore di nome o un'istanza precedente sembra essere assegnata (tramite il nome globale appInsightsSDK), il valore del nome verrà definito anche nello spazio dei nomi globale come ```window.appInsightsSDK=<name value>``` , necessario per il codice di inizializzazione dell'SDK per assicurarsi che l'inizializzazione e l'aggiornamento dei metodi del frammento e del proxy corretti.
| LD | numero in MS *[facoltativo]* | Definisce il ritardo di caricamento da attendere prima di provare a caricare l'SDK. Il valore predefinito è 0ms e qualsiasi valore negativo aggiungerà immediatamente un tag di script &lt; all' &gt; area principale della pagina, che bloccherà l'evento di caricamento della pagina fino a quando lo script non viene caricato (o non riesce).
| useXhr | booleano *[facoltativo]* | Questa impostazione viene utilizzata solo per gli errori di caricamento di report SDK. La creazione di report tenterà innanzitutto di utilizzare FETCH () se disponibile e quindi di eseguire il fallback a XHR, impostando questo valore su true, viene semplicemente ignorato il controllo di recupero. L'uso di questo valore è obbligatorio solo se l'applicazione viene usata in un ambiente in cui il recupero non riesce a inviare gli eventi di errore.
| crossOrigin | String *[facoltativo]* | Includendo questa impostazione, il tag script aggiunto per scaricare l'SDK includerà l'attributo crossOrigin con il valore stringa. Quando non è definito (impostazione predefinita), non viene aggiunto alcun attributo crossOrigin. I valori consigliati non sono definiti (impostazione predefinita). ""; o "anonimo" (per tutti i valori validi, vedere l' [attributo HTML:](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) documentazione di crossorigin)
| config. | oggetto **[obbligatorio]** | Configurazione passata a Application Insights SDK durante l'inizializzazione.

### <a name="sending-telemetry-to-the-azure-portal"></a>Invio di dati di telemetria al portale di Azure

Per impostazione predefinita, Application Insights JavaScript SDK raccoglie un numero di elementi di telemetria utili per determinare l'integrità dell'applicazione e l'esperienza utente sottostante. Queste includono:

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
- **Informazioni sul dispositivo** (ad esempio browser, sistema operativo, versione, lingua, modello)
- **Informazioni sulla sessione**

### <a name="telemetry-initializers"></a>Inizializzatori di telemetria
Gli inizializzatori di telemetria vengono usati per modificare il contenuto dei dati di telemetria raccolti prima di essere inviati dal browser dell'utente. Possono anche essere usati per impedire l'invio di alcuni dati di telemetria, restituendo `false` . È possibile aggiungere più inizializzatori di telemetria all'istanza di Application Insights e vengono eseguiti in ordine di aggiunta.

L'argomento di input per `addTelemetryInitializer` è un callback che accetta [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) come argomento e restituisce `boolean` o `void` . Se restituisce `false` , l'elemento di telemetria non viene inviato, altrimenti passa all'inizializzatore di telemetria successivo, se presente, o viene inviato all'endpoint della raccolta dei dati di telemetria.

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
La maggior parte dei campi di configurazione è denominata in modo che sia possibile impostarla su false. Tutti i campi sono facoltativi ad eccezione di `instrumentationKey` .

| Nome | Predefinito | Descrizione |
|------|---------|-------------|
| instrumentationKey | Null | **Richiesto**<br>Chiave di strumentazione ottenuta dal portale di Azure. |
| accountId | Null | ID account facoltativo, se l'app raggruppa gli utenti in account. Spazi, virgole, punti e virgola, uguali o barre verticali |
| sessionRenewalMs | 1,8 milioni | Una sessione viene registrata se l'utente è inattivo per questo periodo di tempo in millisecondi. Il valore predefinito è 30 minuti. |
| sessionExpirationMs | 86,4 milioni | Una sessione viene registrata se continua per questo periodo di tempo in millisecondi. Il valore predefinito è 24 ore |
| maxBatchSizeInBytes | 10000 | Dimensioni massime del batch di telemetria. Se un batch supera questo limite, viene immediatamente inviato e viene avviato un nuovo batch |
| maxBatchInterval | 15000 | Durata della telemetria batch per prima dell'invio (millisecondi) |
| disableExceptionTracking | false | Se true, le eccezioni non sono autocollected. L'impostazione predefinita è false. |
| disableTelemetry | false | Se true, i dati di telemetria non vengono raccolti o inviati. L'impostazione predefinita è false. |
| enableDebug | false | Se true, i dati di debug **interni** vengono generati come eccezione **anziché** essere registrati, indipendentemente dalle impostazioni di registrazione dell'SDK. L'impostazione predefinita è false. <br>***Nota:*** Se si abilita questa impostazione, la telemetria verrà eliminata ogni volta che si verifica un errore interno. Questo può essere utile per identificare rapidamente i problemi con la configurazione o l'utilizzo dell'SDK. Se non si vogliono perdere i dati di telemetria durante il debug, provare `consoleLoggingLevel` a usare o `telemetryLoggingLevel` invece di `enableDebug` . |
| loggingLevelConsole | 0 | Registra gli errori **interni** di Application Insights alla console. <br>0: disattivato, <br>1: solo errori critici, <br>2: tutto (errori & avvisi) |
| loggingLevelTelemetry | 1 | Invia errori **interni** di Application Insights come dati di telemetria. <br>0: disattivato, <br>1: solo errori critici, <br>2: tutto (errori & avvisi) |
| diagnosticLogInterval | 10000 | interno Intervallo di polling (in MS) per la coda di registrazione interna |
| samplingPercentage | 100 | Percentuale di eventi che verranno inviati. Il valore predefinito è 100, ovvero tutti gli eventi vengono inviati. Impostare questa impostazione se si desidera mantenere il limite di dati per le applicazioni su larga scala. |
| autoTrackPageVisitTime | false | Se true, in una pagina di visualizzazione, il tempo di visualizzazione della pagina instrumentata precedente viene rilevato e inviato come dati di telemetria e viene avviato un nuovo timer per la visualizzazione corrente. L'impostazione predefinita è false. |
| disableAjaxTracking | false | Se il valore è true, le chiamate AJAX non vengono raccolte. L'impostazione predefinita è false. |
| disableFetchTracking | true | Se il valore è true, le richieste di recupero non vengono raccolte. Il valore predefinito è true. |
| overridePageViewDuration | false | Se true, il comportamento predefinito di trackPageView viene modificato per registrare la fine dell'intervallo di durata della visualizzazione pagina quando viene chiamato trackPageView. Se false e non viene fornita alcuna durata personalizzata a trackPageView, le prestazioni della visualizzazione pagina vengono calcolate usando l'API di intervallo di navigazione. L'impostazione predefinita è false. |
| maxAjaxCallsPerView | 500 | Default 500-controlla il numero di chiamate AJAX che verranno monitorate per ogni visualizzazione pagina. Impostare su-1 per monitorare tutte le chiamate AJAX (illimitate) nella pagina. |
| disableDataLossAnalysis | true | Se false, i buffer dei mittenti di telemetria interni verranno controllati all'avvio per gli elementi non ancora inviati. |
| disableCorrelationHeaders | false | Se false, l'SDK aggiungerà due intestazioni (' Request-ID ' è request-context ') a tutte le richieste di dipendenza per metterle in correlazione con richieste corrispondenti sul lato server. L'impostazione predefinita è false. |
| correlationHeaderExcludedDomains |  | Disabilitare le intestazioni di correlazione per domini specifici |
| correlationHeaderDomains |  | Abilitare le intestazioni di correlazione per domini specifici |
| disableFlushOnBeforeUnload | false | Valore predefinito false. Se true, il metodo Flush non verrà chiamato quando viene attivato l'evento onBeforeUnload |
| enableSessionStorageBuffer | true | Valore predefinito true. Se true, il buffer con tutti i dati di telemetria non inviati viene archiviato nell'archiviazione della sessione. Il buffer viene ripristinato al caricamento della pagina |
| isCookieUseDisabled | false | Valore predefinito false. Se true, l'SDK non archivia né legge i dati dai cookie.|
| cookieDomain | Null | Dominio cookie personalizzato. Questa operazione è utile se si desidera condividere Application Insights cookie tra sottodomini. |
| isRetryDisabled | false | Valore predefinito false. Se false, riprovare su 206 (operazione parzialmente riuscita), 408 (timeout), 429 (troppe richieste), 500 (errore interno del server), 503 (servizio non disponibile) e 0 (offline, solo se rilevato) |
| isStorageUseDisabled | false | Se true, l'SDK non archivia né legge i dati dall'archiviazione locale e della sessione. L'impostazione predefinita è false. |
| isBeaconApiDisabled | true | Se false, l'SDK invierà tutti i dati di telemetria usando l' [API Beacon](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | false | Valore predefinito false. Quando la scheda è chiusa, l'SDK invierà tutti i dati di telemetria rimanenti usando l' [API Beacon](https://www.w3.org/TR/beacon) |
| sdkExtension | Null | Imposta il nome dell'estensione SDK. Sono consentiti solo caratteri alfabetici. Il nome dell'estensione viene aggiunto come prefisso al tag ' ai. Internal. sdkVersion ' (ad esempio,' ext_javascript: 2.0.0'). Il valore predefinito è Null. |
| isBrowserLinkTrackingEnabled | false | L'impostazione predefinita è false. Se true, l'SDK tiene traccia di tutte le richieste di [browser link](/aspnet/core/client-side/using-browserlink) . |
| appId | Null | AppId viene utilizzato per la correlazione tra le dipendenze AJAX che si verificano sul lato client con le richieste lato server. Quando l'API Beacon è abilitata, non può essere usata automaticamente, ma può essere impostata manualmente nella configurazione. Il valore predefinito è null |
| enableCorsCorrelation | false | Se true, l'SDK aggiungerà due intestazioni (' Request-ID ' è request-context ') a tutte le richieste CORS per correlare le dipendenze AJAX in uscita con le richieste corrispondenti sul lato server. Il valore predefinito è false. |
| namePrefix | Non definito | Valore facoltativo che verrà usato come nome suffisso per localStorage e il nome del cookie.
| enableAutoRouteTracking | false | Rilevare automaticamente le modifiche del route nelle applicazioni a pagina singola (SPA). Se true, ogni modifica della route invierà una nuova visualizzazione a Application Insights. Anche le modifiche della route hash ( `example.com/foo#bar` ) vengono registrate come nuove visualizzazioni di pagina.
| enableRequestHeaderTracking | false | Se true, vengono rilevate le intestazioni della richiesta di recupero & AJAX, il valore predefinito è false.
| enableResponseHeaderTracking | false | Se true, vengono rilevate le intestazioni di risposta della richiesta di recupero & AJAX, il valore predefinito è false.
| distributedTracingMode | `DistributedTracingModes.AI` | Imposta la modalità di traccia distribuita. Se è impostata la modalità AI_AND_W3C o W3C, le intestazioni del contesto di traccia W3C (traceparent/tracestate) verranno generate e incluse in tutte le richieste in uscita. AI_AND_W3C viene fornito per la compatibilità con le versioni precedenti di tutti i servizi Application Insights instrumentati. Vedere l'esempio [qui](./correlation.md#enable-w3c-distributed-tracing-support-for-web-apps).
| enableAjaxErrorStatusText | false | Valore predefinito false. Se true, includere il testo dei dati degli errori di risposta nell'evento di dipendenza per le richieste AJAX non riuscite.
| enableAjaxPerfTracking | false | Valore predefinito false. Flag per abilitare la ricerca e l'inclusione della finestra del browser aggiuntiva. intervalli di prestazioni nelle metriche segnalate di AJAX (XHR e fetch).
| maxAjaxPerfLookupAttempts | 3 | Il valore predefinito è 3. Il numero massimo di volte in cui cercare la finestra. tempistiche delle prestazioni (se disponibili), questa operazione è necessaria perché non tutti i browser popolano la finestra. prestazioni prima di segnalare la fine della richiesta XHR e per le richieste di recupero questo viene aggiunto dopo il completamento.
| ajaxPerfLookupDelay | 25 | Il valore predefinito è 25 ms. Tempo di attesa prima di ritentare di trovare le finestre. tempi di prestazioni per una richiesta AJAX. il tempo è espresso in millisecondi e viene passato direttamente a timeout ().
| enableUnhandledPromiseRejectionTracking | false | Se true, i rifiuti di Promise non gestiti verranno raccolti e segnalati come errori JavaScript. Quando disableExceptionTracking è true (non tenere traccia delle eccezioni), il valore di configurazione verrà ignorato e i rifiuti di promessa non gestita non verranno segnalati.

## <a name="single-page-applications"></a>Applicazioni a pagina singola

Per impostazione predefinita, questo SDK **non** gestirà la modifica della Route basata sullo stato che si verifica nelle applicazioni a pagina singola. Per abilitare il rilevamento automatico delle modifiche della route per l'applicazione a pagina singola, è possibile aggiungere `enableAutoRouteTracking: true` alla configurazione di installazione.

Attualmente si offre un plug-in [React](#react-extensions)separato, che può essere inizializzato con questo SDK. Verrà anche eseguito il rilevamento delle modifiche delle route, oltre a raccogliere altri dati di [telemetria specifici di React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md).

> [!NOTE]
> Usare `enableAutoRouteTracking: true` solo se **non** si usa il plug-in React. Entrambi sono in grado di inviare nuove visualizzazioni di pagina quando la route cambia. Se entrambe le funzionalità sono abilitate, è possibile che vengano inviate pagine di visualizzazione duplicate.

## <a name="configuration-autotrackpagevisittime"></a>Configurazione: autoTrackPageVisitTime

Impostando `autoTrackPageVisitTime: true` , viene tenuta traccia del tempo impiegato da un utente in ogni pagina. In ogni nuova pagina di visualizzazione, la durata dell'utente nella pagina *precedente* viene inviata come [metrica personalizzata](../platform/metrics-custom-overview.md) denominata `PageVisitTime` . Questa metrica personalizzata è visualizzabile nel [Esplora metriche](../platform/metrics-getting-started.md) come "metrica basata su log".

## <a name="react-extensions"></a>Estensioni React

| Estensioni |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [React Native](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Esplorare i dati sul lato client e sul browser

Per visualizzare i dati del browser/lato client, passare a **metriche** e aggiungere singole metriche a cui si è interessati:

![Screenshot della pagina metrica in Application Insights che mostra la visualizzazione grafica dei dati di metrica per un'applicazione Web.](./media/javascript/page-view-load-time.png)

È anche possibile visualizzare i dati da JavaScript SDK tramite l'esperienza del browser nel portale.

Selezionare **browser** , quindi scegliere **errori** o **prestazioni**.

![Screenshot della pagina del browser in Application Insights che illustra come aggiungere errori del browser o prestazioni del browser alle metriche che è possibile visualizzare per l'applicazione Web.](./media/javascript/browser.png)

### <a name="performance"></a>Prestazioni

![Screenshot della pagina prestazioni in Application Insights che mostra la visualizzazione grafica delle metriche operative per un'applicazione Web.](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Dipendenze

![Screenshot della pagina prestazioni in Application Insights che mostra la visualizzazione grafica delle metriche delle dipendenze per un'applicazione Web.](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analytics

Per eseguire una query sui dati di telemetria raccolti da JavaScript SDK, selezionare il pulsante **Visualizza nei log (Analytics)** . Aggiungendo un' `where` istruzione di `client_Type == "Browser"` , verranno visualizzati solo i dati di JavaScript SDK e tutti i dati di telemetria sul lato server raccolti da altri SDK verranno esclusi.
 
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

Il minimizzati stack dei dati di telemetria delle eccezioni può essere unminified nel portale di Azure. Tutte le integrazioni esistenti nel pannello Dettagli eccezione funzioneranno con il nuovo unminified stack.

#### <a name="link-to-blob-storage-account"></a>Collegamento all'account di archiviazione BLOB

È possibile collegare la risorsa Application Insights al contenitore di archiviazione BLOB di Azure per unminify automaticamente gli stack di chiamate. Per iniziare, vedere [supporto automatico della mappa di origine](./source-map-support.md).

### <a name="drag-and-drop"></a>Trascinamento della selezione

1. Selezionare un elemento di telemetria delle eccezioni nel portale di Azure per visualizzare i relativi dettagli della transazione end-to-end.
2. Identificare i mapping di origine corrispondenti a questo stack di chiamate. La mappa di origine deve corrispondere al file di origine di un stack frame, ma con suffisso`.map`
3. Trascinare e rilasciare i mapping di origine nello stack di chiamate nella portale di Azure ![ un'immagine animata che Mostra come trascinare i file della mappa di origine da una cartella di compilazione nella finestra stack di chiamate della portale di Azure.](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web Basic

Per un'esperienza semplificata, è invece possibile installare la versione di base di Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Questa versione include il numero minimo di caratteristiche e funzionalità e si basa su di esso per compilarlo nel modo appropriato. Ad esempio, non esegue alcuna raccolta (eccezioni non rilevate, AJAX e così via). Le API per inviare determinati tipi di dati di telemetria, ad esempio `trackTrace` , `trackException` e così via, non sono incluse in questa versione, pertanto sarà necessario fornire un wrapper personalizzato. L'unica API disponibile è `track` . Un [esempio](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) è disponibile qui.

## <a name="examples"></a>Esempi

Per esempi eseguibili, vedere [esempi di Application Insights JavaScript SDK](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Aggiornamento dalla versione precedente di Application Insights

Modifiche di rilievo nella versione SDK v2:
- Per consentire una migliore firma API, alcune chiamate API, ad esempio trackPageView e trackexception, sono state aggiornate. L'esecuzione in Internet Explorer 8 e nelle versioni precedenti del browser non è supportata.
- La busta di telemetria presenta modifiche al nome del campo e alla struttura a causa degli aggiornamenti dello schema dei dati.
- Spostato `context.operation` in `context.telemetryTrace` . Sono stati modificati anche alcuni campi ( `operation.id`  -->  `telemetryTrace.traceID` ).
  - Per aggiornare manualmente l'ID di visualizzazione corrente (ad esempio, nelle app SPA), usare `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()` .
    > [!NOTE]
    > Per tenere l'ID di traccia univoco, in cui è stato usato in precedenza `Util.newId()` , ora usare `Util.generateW3CId()` . Entrambi finiscono in definitiva l'ID operazione.

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

A soli 36 KB gzippato e impiegando solo ~ 15 ms per l'inizializzazione, Application Insights aggiunge una quantità trascurabile di LoadTime al sito Web. Usando il frammento di codice, vengono caricati rapidamente i componenti minimi della libreria. Nel frattempo, lo script completo viene scaricato in background.

Durante il download dello script dalla rete CDN, tutte le tracce della pagina vengono accodate. Al termine dell'inizializzazione asincrona dello script scaricato, verranno rilevati tutti gli eventi accodati. Di conseguenza, non si perderanno i dati di telemetria durante l'intero ciclo di vita della pagina. Questo processo di installazione fornisce alla pagina un sistema di analisi trasparente, invisibile agli utenti.

> Riepilogo:
> - ![versione di NPM](https://badge.fury.io/js/%40microsoft%2Fapplicationinsights-web.svg)
> - ![dimensioni compresse gzip](https://img.badgesize.io/https://js.monitor.azure.com/scripts/b/ai.2.min.js.svg?compression=gzip)
> - tempo totale di inizializzazione di **15 ms**
> - **Nessun** rilevamento perso durante il ciclo di vita della pagina

## <a name="browser-support"></a>Supporto browser

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
✔ Più recenti di Chrome |  ✔ Più recenti di Firefox | Internet Explorer 9 + & Edge ✔<br>Internet Explorer 8-compatibile | Opera più recente ✔ | Safari più recenti ✔ |

## <a name="es3ie8-compatibility"></a>Compatibilità con ES3/IE8

Come SDK sono disponibili numerosi utenti che non possono controllare i browser usati dai clienti. Per questo è necessario assicurarsi che l'SDK continui a funzionare e non interrompa l'esecuzione di JS quando viene caricata da un browser precedente. Sebbene sarebbe ideale non supportare i browser IE8 e di generazione precedente (ES3), sono disponibili numerosi clienti/utenti di grandi dimensioni che continuano a richiedere le pagine per "lavorare" e come indicato possono o non possono controllare il browser che i loro utenti finali scelgono di usare.

Ciò non significa che verrà supportato solo il set di funzionalità più basso comune, solo che è necessario mantenere la compatibilità del codice ES3 e quando si aggiungono nuove funzionalità, sarà necessario aggiungerle in modo che non interrompa l'analisi JavaScript di ES3 e venga aggiunta come funzionalità facoltativa.

[Vedere GitHub per i dettagli completi sul supporto di IE8](https://github.com/Microsoft/ApplicationInsights-JS#es3ie8-compatibility)

## <a name="open-source-sdk"></a>SDK open source

Il Application Insights JavaScript SDK è open source per visualizzare il codice sorgente o per contribuire al progetto visitare il [repository GitHub ufficiale](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next-steps"></a><a name="next"></a> Passaggi successivi
* [Tenere traccia dell'utilizzo](usage-overview.md)
* [Metriche ed eventi personalizzati](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)
* [Risolvere gli errori di caricamento SDK](javascript-sdk-load-failure.md)
