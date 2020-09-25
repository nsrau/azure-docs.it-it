---
title: Monitorare i servizi Node.js con Azure Application Insights | Microsoft Docs
description: Monitorare le prestazioni e diagnosticare i problemi dei servizi Node.js con Application Insights.
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: 982adf6c6d7cd825d185802321ce30a04bd2f216
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323295"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Monitorare servizi e app Node.js con Application Insights

[Application Insights](./app-insights-overview.md) monitora i servizi e i componenti back-end dopo la distribuzione, per facilitare l'individuazione e la diagnosi rapida delle prestazioni e di altri problemi. È possibile usare Application Insights per i servizi Node.js ospitati nel Data Center, nelle macchine virtuali di Azure e nelle app Web e anche in altri cloud pubblici.

Per ricevere, archiviare ed esplorare i dati di monitoraggio, includere l'SDK nel codice e quindi configurare una risorsa di Application Insights corrispondente in Azure. L'SDK invia i dati a tale risorsa per ulteriori attività di analisi ed esplorazione.

Node.js SDK può monitorare automaticamente le richieste HTTP in ingresso e in uscita, le eccezioni e alcune metriche di sistema. A partire dalla versione 0,20, l'SDK può anche monitorare alcuni [pacchetti comuni di terze parti](https://github.com/microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers#currently-supported-modules), ad esempio MongoDB, MySQL e Redis. Tutti gli eventi relativi a una richiesta HTTP in ingresso vengono correlati per velocizzare la risoluzione dei problemi.

È possibile usare l'API TelemetryClient per instrumentare e monitorare manualmente altri aspetti dell'app e del sistema. L'API TelemetryClient viene descritta in modo più dettagliato più avanti nell'articolo.

## <a name="get-started"></a>Introduzione

Completare le attività seguenti per configurare il monitoraggio per un'app o un servizio.

### <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di avere una sottoscrizione di Azure oppure [ottenerne una nuova gratuitamente][azure-free-offer]. Se l'organizzazione ha già una sottoscrizione di Azure, un amministratore può aggiungere l'utente alla sottoscrizione seguendo [queste istruzioni][add-aad-user].

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: ../../active-directory/fundamentals/add-users-azure-active-directory.md

### <a name="set-up-an-application-insights-resource"></a><a name="resource"></a> Configurare una risorsa Application Insights

1. Accedere al [portale di Azure][portal].
2. [Creare una risorsa di Application Insights](create-new-resource.md)

### <a name="set-up-the-nodejs-sdk"></a><a name="sdk"></a> Configurare Node.js SDK

Includere l'SDK nell'app affinché possa raccogliere i dati.

1. Copiare la chiave di strumentazione della risorsa (detta anche *iKey*) dalla risorsa appena creata. Application Insights usa la chiave di strumentazione per eseguire il mapping dei dati alla risorsa di Azure. Affinché l'SDK possa usare la chiave di strumentazione, è necessario specificare tale chiave in una variabile di ambiente o nel codice.  

   ![Copiare la chiave di strumentazione](./media/nodejs/instrumentation-key-001.png)

2. Aggiungere la libreria Node.js SDK alle dipendenze dell'app tramite package.json. Dalla cartella radice dell'app eseguire:

   ```bash
   npm install applicationinsights --save
   ```

    > [!NOTE]
    > Se si usa TypeScript, non installare pacchetti "Typing" distinti. Questo pacchetto NPM contiene scritture predefinite.

3. Caricare in modo esplicito la libreria nel codice. Dato che l'SDK inserisce la strumentazione in molte altre librerie, caricare la libreria il prima possibile, anche prima di altre istruzioni `require`.

   ```javascript
   let appInsights = require('applicationinsights');
   ```
4.  È anche possibile fornire un iKey tramite la variabile di ambiente `APPINSIGHTS_INSTRUMENTATIONKEY` , anziché passarlo manualmente a  `setup()` o `new appInsights.TelemetryClient()` . Questa procedura consente di non includere le chiavi di strumentazione nel codice sorgente sottoposto a commit e di specificare chiavi di strumentazione diverse per ambienti diversi. Per configurare la chiamata manuale `appInsights.setup('[your ikey]');` .

    Per altre opzioni di configurazione, vedere le sezioni seguenti.

    È possibile provare l'SDK senza inviare i dati di telemetria impostando `appInsights.defaultClient.config.disableAppInsights = true`.

5. Avviare la raccolta e l'invio automatici dei dati chiamando `appInsights.start();` .

### <a name="monitor-your-app"></a><a name="monitor"></a> Monitorare l'app

L'SDK raccoglie automaticamente i dati di telemetria relativi al runtime Node.js e ad alcuni moduli comuni di terze parti. Usare l'applicazione per generare alcuni di questi dati.

Nel [portale di Azure][portal] passare quindi alla risorsa di Application Insights creata in precedenza. In **Panoramica sequenza temporale** cercare i primi punti dati. Per visualizzare altri dati dettagliati, selezionare diversi componenti nei grafici.

Per visualizzare la topologia individuata per l'app, è possibile usare la [mappa delle applicazioni](app-map.md).

#### <a name="no-data"></a>Nessun dato

Dato che l'SDK esegue l'invio dei dati in batch, potrebbe verificarsi un ritardo nella visualizzazione degli elementi nel portale. Se i dati non sono visibili nella risorsa, provare alcune delle correzioni seguenti:

* Continuare a usare l'applicazione. Eseguire altre azioni per generare dati di telemetria aggiuntivi.
* Fare clic su **Aggiorna** nella visualizzazione della risorsa nel portale. I grafici si aggiornano periodicamente in automatico, ma l'aggiornamento manuale ha effetto immediato.
* Verificare che le [porte in uscita necessarie](./ip-addresses.md) siano aperte.
* Usare l'opzione [Cerca](./diagnostic-search.md) per cercare eventi specifici.
* Vedere le [domande frequenti][FAQ].

## <a name="basic-usage"></a>Utilizzo di base

Per la raccolta predefinita di richieste HTTP, gli eventi della libreria di terze parti più diffusi, le eccezioni non gestite e le metriche di sistema:

```javascript

let appInsights = require("applicationinsights");
appInsights.setup("[your ikey]").start();

```

> [!NOTE]
> Se la chiave di strumentazione è impostata nella variabile di ambiente `APPINSIGHTS_INSTRUMENTATIONKEY` , `.setup()` può essere chiamata senza argomenti. Questo semplifica l'uso di iKey diversi per ambienti diversi.

Caricare la Libreria Application Insights, `require("applicationinsights")` , il prima possibile negli script prima di caricare altri pacchetti. Questa operazione è necessaria in modo che la Libreria Application Insights possa preparare i pacchetti successivi per il rilevamento. Se si verificano conflitti con altre librerie che effettuano una preparazione simile, provare a caricare la Libreria Application Insights dopo questi.

A causa del modo in cui JavaScript gestisce i callback, è necessario lavoro aggiuntivo per tenere traccia di una richiesta tra le dipendenze esterne e i callback successivi. Per impostazione predefinita, questo rilevamento aggiuntivo è abilitato; disabilitare `setAutoDependencyCorrelation(false)` il nome chiamando come descritto nella sezione di [configurazione](#sdk-configuration) riportata di seguito.

## <a name="migrating-from-versions-prior-to-022"></a>Migrazione da versioni precedenti alla 0,22

Sono state apportate modifiche di rilievo tra le versioni precedenti alla versione 0,22 e successive. Queste modifiche sono progettate per garantire coerenza con altri SDK Application Insights e consentire un'estendibilità futura.

In generale, è possibile eseguire la migrazione con quanto segue:

- Sostituire i riferimenti a `appInsights.client` con `appInsights.defaultClient` .
- Sostituisci riferimenti a `appInsights.getClient()` con `new appInsights.TelemetryClient()`
- Sostituire tutti gli argomenti con i metodi client. Track * con un singolo oggetto contenente le proprietà denominate come argomenti. Per l'oggetto ad eccezione di ogni tipo di telemetria, vedere Hint di tipo incorporato o [TelemetryTypes](https://github.com/Microsoft/ApplicationInsights-node.js/tree/develop/Declarations/Contracts/TelemetryTypes) dell'IDE.

Se si accede alle funzioni di configurazione dell'SDK senza concatenarle a `appInsights.setup()` , è ora possibile trovare queste funzioni in `appInsights.Configurations` (ad esempio, `appInsights.Configuration.setAutoCollectDependencies(true)` ). Esaminare le modifiche apportate alla configurazione predefinita nella sezione successiva.

## <a name="sdk-configuration"></a>Configurazione dell'SDK

L' `appInsights` oggetto fornisce una serie di metodi di configurazione. Sono elencati nel frammento di codice seguente con i relativi valori predefiniti.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true, true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .setSendLiveMetrics(false)
    .setDistributedTracingMode(appInsights.DistributedTracingModes.AI)
    .start();
```

Per correlare completamente gli eventi in un servizio, assicurarsi di impostare `.setAutoDependencyCorrelation(true)`. Con questa opzione impostata, l'SDK può tenere traccia del contesto tra callback asincroni in Node.js.

Esaminare le descrizioni nell'hint di tipo incorporato dell'IDE oppure [applicationinsights. TS](https://github.com/microsoft/ApplicationInsights-node.js/blob/develop/applicationinsights.ts) per informazioni dettagliate su questi controlli e sugli argomenti secondari facoltativi.

> [!NOTE]
>  Per impostazione predefinita, `setAutoCollectConsole` è configurato per *escludere* le chiamate a `console.log` (e altri metodi Console). Verranno raccolte solo le chiamate a logger di terze parti supportati (ad esempio, Winston e Bunyan). È possibile modificare questo comportamento per includere le chiamate ai `console` metodi tramite `setAutoCollectConsole(true, true)` .

### <a name="sampling"></a>campionamento

Per impostazione predefinita, l'SDK invierà tutti i dati raccolti al servizio Application Insights. Se si raccolgono moltissimi dati, potrebbe essere necessario abilitare il campionamento per ridurre la quantità di dati inviati. `samplingPercentage` `config` Per eseguire questa operazione, impostare il campo nell'oggetto di un client. Impostando `samplingPercentage` su 100 (impostazione predefinita) verranno inviati tutti i dati e 0 indica che non verrà inviato alcun elemento.

Se si utilizza la correlazione automatica, tutti i dati associati a una singola richiesta verranno inclusi o esclusi come unità.

Aggiungere codice simile al seguente per abilitare il campionamento:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.config.samplingPercentage = 33; // 33% of all telemetry will be sent to Application Insights
appInsights.start();
```

### <a name="multiple-roles-for-multi-components-applications"></a>Più ruoli per le applicazioni multicomponente

Se l'applicazione è costituita da più componenti che si vuole instrumentare tutti con la stessa chiave di strumentazione e questi componenti vengono comunque visualizzati come unità separate nel portale, come se fossero utilizzate chiavi di strumentazione separate (ad esempio, come nodi separati nella mappa delle applicazioni), potrebbe essere necessario configurare manualmente il campo roleName per distinguere i Application Insights dati di telemetria di un componente da altri componenti che inviano

Usare il comando seguente per impostare il campo roleName:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.context.tags[appInsights.defaultClient.context.keys.cloudRole] = "MyRoleName";
appInsights.start();
```

### <a name="automatic-third-party-instrumentation"></a>Strumentazione automatica di terze parti

Per tenere traccia del contesto tra le chiamate asincrone, sono necessarie alcune modifiche nelle librerie di terze parti, ad esempio MongoDB e Redis. Per impostazione predefinita, Application Insights utilizzerà [`diagnostic-channel-publishers`](https://github.com/Microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers) per la correzione di alcune di queste librerie. Questa operazione può essere disabilitata impostando la `APPLICATION_INSIGHTS_NO_DIAGNOSTIC_CHANNEL` variabile di ambiente.

> [!NOTE]
> Impostando la variabile di ambiente, è possibile che gli eventi non siano più associati correttamente all'operazione corretta.

 Le singole patch Monkey possono essere disabilitate impostando la `APPLICATION_INSIGHTS_NO_PATCH_MODULES` variabile di ambiente su un elenco delimitato da virgole di pacchetti da disabilitare, ad esempio, `APPLICATION_INSIGHTS_NO_PATCH_MODULES=console,redis` per evitare l'applicazione di patch ai `console` `redis` pacchetti e.

Attualmente sono disponibili nove pacchetti instrumentati: `bunyan` , `console` , `mongodb` , `mongodb-core` , `mysql` , `redis` , `winston` , `pg` e `pg-pool` . Per informazioni sulla versione di questi pacchetti con patch, vedere il [file Leggimi relativo a Channel-Publishers](https://github.com/Microsoft/node-diagnostic-channel/blob/master/src/diagnostic-channel-publishers/README.md) .

Le `bunyan` `winston` patch, e `console` generano Application Insights eventi di traccia a seconda che `setAutoCollectConsole` sia abilitato. Rest genererà Application Insights eventi di dipendenza a seconda che `setAutoCollectDependencies` sia abilitato.

### <a name="live-metrics"></a>Metriche attive    

Per abilitare l'invio di metriche in tempo reale dall'app ad Azure, usare `setSendLiveMetrics(true)` . Il filtro di metriche attive nel portale non è al momento supportato.

### <a name="extended-metrics"></a>Metriche estese

> [!NOTE]
> La possibilità di inviare metriche native estese è stata aggiunta nella versione 1.4.0

Per abilitare l'invio di metriche native estese dall'app ad Azure, installare il pacchetto di metriche native separate. L'SDK verrà caricato automaticamente al momento dell'installazione e inizierà a raccogliere Node.js metriche native.

```bash
npm install applicationinsights-native-metrics
```

Attualmente, il pacchetto di metriche native esegue la raccolta di Garbage Collection tempo CPU, i cicli del ciclo di eventi e l'utilizzo dell'heap:

- **Garbage Collection**: quantità di tempo di CPU impiegato per ogni tipo di Garbage Collection e numero di occorrenze di ogni tipo.
- **Ciclo di eventi**: numero di cicli verificatisi e quantità di tempo di CPU impiegato in totale.
- **Heap e non heap**: quantità di utilizzo della memoria dell'app nell'heap o non heap.

### <a name="distributed-tracing-modes"></a>Modalità di traccia distribuita

Per impostazione predefinita, l'SDK invierà le intestazioni riconosciute da altri servizi o applicazioni instrumentati con un SDK Application Insights. Facoltativamente, è possibile abilitare l'invio e la ricezione delle intestazioni del [contesto di traccia W3C](https://github.com/w3c/trace-context) oltre alle intestazioni di intelligenza artificiale esistenti, in modo che non si interrompa la correlazione con nessuno dei servizi legacy esistenti. L'abilitazione delle intestazioni W3C consentirà all'app di essere correlata ad altri servizi non instrumentati con Application Insights, ma di adottare questo standard W3C.

```Javascript
const appInsights = require("applicationinsights");
appInsights
  .setup("<your ikey>")
  .setDistributedTracingMode(appInsights.DistributedTracingModes.AI_AND_W3C)
  .start()
```

## <a name="telemetryclient-api"></a>API TelemetryClient

Per una descrizione completa dell'API TelemetryClient, vedere [API di Application Insights per metriche ed eventi personalizzati](./api-custom-events-metrics.md).

Con Application Insights Node.js SDK è possibile tenere traccia di qualsiasi richiesta, evento, metrica o eccezione. L'esempio di codice seguente illustra alcune API che è possibile usare:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var. start() can be omitted to disable any non-custom data
let client = appInsights.defaultClient;
client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Tenere traccia delle dipendenze

Usare il codice seguente per tenere traccia delle dipendenze:

```javascript
let appInsights = require("applicationinsights");
let client = new appInsights.TelemetryClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:duration, resultCode:0, success: true, dependencyTypeName: "ZSQL"});;
```

Utilità `trackMetric` di esempio che usa per misurare il tempo necessario per la pianificazione del ciclo di eventi:  

```javascript
function startMeasuringEventLoop() {
  var startTime = process.hrtime();
  var sampleSum = 0;
  var sampleCount = 0;

  // Measure event loop scheduling delay
  setInterval(() => {
    var elapsed = process.hrtime(startTime);
    startTime = process.hrtime();
    sampleSum += elapsed[0] * 1e9 + elapsed[1];
    sampleCount++;
  }, 0);

  // Report custom metric every second
  setInterval(() => {
    var samples = sampleSum;
    var count = sampleCount;
    sampleSum = 0;
    sampleCount = 0;

    if (count > 0) {
      var avgNs = samples / count;
      var avgMs = Math.round(avgNs / 1e6);
      client.trackMetric({name: "Event Loop Delay", value: avgMs});
    }
  }, 1000);
}
```

### <a name="add-a-custom-property-to-all-events"></a>Aggiungere una proprietà personalizzata a tutti gli eventi

Usare il codice seguente per aggiungere una proprietà personalizzata a tutti gli eventi:

```javascript
appInsights.defaultClient.commonProperties = {
  environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Tenere traccia delle richieste HTTP GET

Usare il codice seguente per tenere traccia manualmente delle richieste HTTP GET:

> [!NOTE]
> Per impostazione predefinita, tutte le richieste vengono rilevate. Per disabilitare la raccolta automatica, chiamare. setAutoCollectRequests (false) prima di chiamare Start ().

```javascript
appInsights.defaultClient.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});
```

In alternativa, è possibile tenere traccia delle richieste tramite il `trackNodeHttpRequest` Metodo:

```javascript
var server = http.createServer((req, res) => {
  if ( req.method === "GET" ) {
      appInsights.defaultClient.trackNodeHttpRequest({request:req, response:res});
  }
  // other work here....
  res.end();
});
```

### <a name="track-server-startup-time"></a>Tenere traccia del tempo di avvio del server

Usare il codice seguente per tenere traccia dell'ora di avvio del server:

```javascript
let start = Date.now();
server.on("listening", () => {
  let duration = Date.now() - start;
  appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

### <a name="preprocess-data-with-telemetry-processors"></a>Pre-elaborare i dati con processori di telemetria

È possibile elaborare e filtrare i dati raccolti prima che vengano inviati per la conservazione usando *processori di telemetria*. I processori di telemetria vengono chiamati uno alla volta nell'ordine in cui sono stati aggiunti prima che l'elemento di telemetria venga inviato al cloud.

```javascript
public addTelemetryProcessor(telemetryProcessor: (envelope: Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean)
```

Se un processore di telemetria restituisce false, l'elemento di telemetria non verrà inviato.

Tutti i processori di telemetria ricevono i dati di telemetria e la busta per ispezionarli e modificarli. Ricevono anche un oggetto context. Il contenuto di questo oggetto è definito dal `contextObjects` parametro quando si chiama un metodo Track per la telemetria rilevata manualmente. Per i dati di telemetria raccolti automaticamente, questo oggetto viene compilato con le informazioni sulle richieste disponibili e il contenuto della richiesta permanente fornito da `appInsights.getCorrelationContext()` (se è abilitata la correlazione automatica delle dipendenze).

Il tipo di TypeScript per un processore di telemetria è:

```javascript
telemetryProcessor: (envelope: ContractsModule.Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean;
```

Ad esempio, un processore che rimuove i dati di traccia degli stack dalle eccezioni può essere scritto e aggiunto come segue:

```javascript
function removeStackTraces ( envelope, context ) {
  if (envelope.data.baseType === "Microsoft.ApplicationInsights.ExceptionData") {
    var data = envelope.data.baseData;
    if (data.exceptions && data.exceptions.length > 0) {
      for (var i = 0; i < data.exceptions.length; i++) {
        var exception = data.exceptions[i];
        exception.parsedStack = null;
        exception.hasFullStack = false;
      }
    }
  }
  return true;
}

appInsights.defaultClient.addTelemetryProcessor(removeStackTraces);
```

## <a name="use-multiple-instrumentation-keys"></a>Usare più chiavi di strumentazione

È possibile creare più risorse Application Insights e inviare dati diversi usando le rispettive chiavi di strumentazione ("iKey").

 Ad esempio:

```javascript
let appInsights = require("applicationinsights");

// configure auto-collection under one ikey
appInsights.setup("_ikey-A_").start();

// track some events manually under another ikey
let otherClient = new appInsights.TelemetryClient("_ikey-B_");
otherClient.trackEvent({name: "my custom event"});
```

## <a name="advanced-configuration-options"></a>Opzioni di configurazione avanzate

L'oggetto client contiene una `config` proprietà con molte impostazioni facoltative per gli scenari avanzati. Questi possono essere impostati come segue:

```javascript
client.config.PROPERTYNAME = VALUE;
```

Queste proprietà sono specifiche del client, pertanto è possibile configurare `appInsights.defaultClient` separatamente dai client creati con `new appInsights.TelemetryClient()` .

| Proprietà                        | Descrizione                                                                                                |
| ------------------------------- |------------------------------------------------------------------------------------------------------------|
| instrumentationKey              | Identificatore per la risorsa Application Insights.                                                      |
| endpointUrl                     | Endpoint di inserimento a cui inviare i payload di telemetria.                                                      |
| quickPulseHost                  | Host Live Metrics Stream a cui inviare i dati di telemetria delle metriche attive.                                            |
| proxyHttpUrl                    | Un server proxy per il traffico HTTP SDK (facoltativo, il pull predefinito dalla `http_proxy` variabile di ambiente).     |
| proxyHttpsUrl                   | Un server proxy per il traffico HTTPS SDK (facoltativo, il pull predefinito dalla `https_proxy` variabile di ambiente).   |
| httpAgent                       | Un http. Agent da usare per il traffico HTTP SDK (facoltativo, predefinito non definito).                                   |
| httpsAgent                      | Un HTTPS. Agent da usare per il traffico HTTPS SDK (facoltativo, predefinito non definito).                                 |
| maxBatchSize                    | Numero massimo di elementi di telemetria da includere in un payload per l'endpoint di inserimento (impostazione predefinita `250` ).   |
| maxBatchIntervalMs              | Tempo massimo di attesa per un payload per raggiungere maxBatchSize (impostazione predefinita `15000` ).               |
| disableAppInsights              | Flag che indica se la trasmissione della telemetria è disabilitata (impostazione predefinita `false` ).                                 |
| samplingPercentage              | Percentuale di elementi di telemetria rilevati che devono essere trasmessi (impostazione predefinita `100` ).                      |
| correlationIdRetryIntervalMs    | Tempo di attesa prima di tentare di recuperare l'ID per la correlazione tra componenti (impostazione predefinita `30000` ).     |
| correlationHeaderExcludedDomains| Elenco di domini da escludere dall'inserimento di intestazioni di correlazione tra componenti (per impostazione predefinita, vedere [config. TS](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Config.ts)).|

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare i dati di telemetria nel portale](./overview-dashboard.md)
* [Presentazione dello strumento Analisi in Application Insights](../log-query/get-started-portal.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../faq.md

