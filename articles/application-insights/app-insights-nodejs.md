---
title: Monitorare i servizi Node.js con Azure Application Insights | Microsoft Docs
description: Monitorare le prestazioni e diagnosticare i problemi dei servizi Node.js con Application Insights.
services: application-insights
documentationcenter: nodejs
author: mrbullwinkle
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/01/2017
ms.author: mbullwin
ms.openlocfilehash: 8f7a2344b6676a9067cf0adff04f49a73ce457fc
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Monitorare servizi e app Node.js con Application Insights

[Azure Application Insights](app-insights-overview.md) monitora i componenti e i servizi back-end dopo la distribuzione, per consentire di [individuare e diagnosticare rapidamente i problemi di prestazioni e di altro tipo](app-insights-detect-triage-diagnose.md). È possibile usare Azure Application Insights per i servizi Node.js ospitati nel data center locale, in app Web, in VM di Azure e anche in altri cloud pubblici.

Per ricevere, archiviare ed esplorare i dati di monitoraggio, includere l'SDK nel codice e quindi configurare una risorsa di Application Insights corrispondente in Azure. L'SDK invia i dati a tale risorsa per ulteriori attività di analisi ed esplorazione.

Node.js SDK può monitorare automaticamente le richieste HTTP in ingresso e in uscita, le eccezioni e alcune metriche di sistema. A partire dalla versione 0.20, l'SDK può anche monitorare alcuni pacchetti comuni di terze parti, ad esempio MongoDB, MySQL e Redis. Tutti gli eventi relativi a una richiesta HTTP in ingresso vengono correlati per velocizzare la risoluzione dei problemi.

È possibile usare l'API TelemetryClient per instrumentare e monitorare manualmente altri aspetti dell'app e del sistema. L'API TelemetryClient viene descritta in modo più dettagliato più avanti nell'articolo.

![Grafici di monitoraggio delle prestazioni di esempio](./media/app-insights-nodejs/10-perf.png)

## <a name="get-started"></a>Introduzione

Completare le attività seguenti per configurare il monitoraggio per un'app o un servizio.

### <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di avere una sottoscrizione di Azure oppure [ottenerne una nuova gratuitamente][azure-free-offer]. Se l'organizzazione ha già una sottoscrizione di Azure, un amministratore può aggiungere l'utente alla sottoscrizione seguendo [queste istruzioni][add-aad-user].

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: https://docs.microsoft.com/azure/active-directory/active-directory-users-create-azure-portal


### <a name="resource"></a> Configurare una risorsa di Application Insights


1. Accedere al [portale di Azure][portal].
2. Selezionare **Nuovo** > **Strumenti di sviluppo** > **Application Insights**. La risorsa include un endpoint per la ricezione dei dati di telemetria, l'archiviazione di tali dati, dei report salvati e dei dashboard, la configurazione di regole e avvisi e altro ancora.

  ![Creare una risorsa Application Insights](./media/app-insights-nodejs/03-new_appinsights_resource.png)

3. Nella pagina di creazione della risorsa scegliere **Applicazione Node.js** nella casella **Tipo di applicazione**. Il tipo di app determina i dashboard e i report predefiniti che vengono creati. Qualsiasi risorsa di Application Insights può raccogliere dati da qualsiasi linguaggio e piattaforma.

  ![Modulo per la nuova risorsa di Application Insights](./media/app-insights-nodejs/04-create_appinsights_resource.png)

### <a name="sdk"></a> Configurare Node.js SDK

Includere l'SDK nell'app affinché possa raccogliere i dati. 

1. Copiare la chiave di strumentazione della risorsa (detta anche *ikey*) dal portale di Azure. Application Insights usa la chiave di strumentazione per eseguire il mapping dei dati alla risorsa di Azure. Affinché l'SDK possa usare la chiave di strumentazione, è necessario specificare tale chiave in una variabile di ambiente o nel codice.  

  ![Copiare la chiave di strumentazione](./media/app-insights-nodejs/05-appinsights_ikey_portal.png)

2. Aggiungere la libreria Node.js SDK alle dipendenze dell'app tramite package.json. Dalla cartella radice dell'app eseguire:

  ```bash
  npm install applicationinsights --save
  ```

3. Caricare in modo esplicito la libreria nel codice. Dato che l'SDK inserisce la strumentazione in molte altre librerie, caricare la libreria il prima possibile, anche prima di altre istruzioni `require`. 

  All'inizio del primo file con estensione js aggiungere il codice seguente. Il metodo `setup` configura la chiave di strumentazione, e quindi la risorsa di Azure, da usare per impostazione predefinita per tutti gli elementi monitorati.

  ```javascript
  const appInsights = require("applicationinsights");
  appInsights.setup("<instrumentation_key>");
  appInsights.start();
  ```
   
  È anche possibile specificare una chiave di strumentazione tramite la variabile di ambiente APPINSIGHTS\_INSTRUMENTATIONKEY, invece di passarla manualmente a `setup()` o `new appInsights.TelemetryClient()`. Questa procedura consente di non includere le chiavi di strumentazione nel codice sorgente sottoposto a commit e di specificare chiavi di strumentazione diverse per ambienti diversi.

  Per altre opzioni di configurazione, vedere le sezioni seguenti.

  È possibile provare l'SDK senza inviare i dati di telemetria impostando `appInsights.defaultClient.config.disableAppInsights = true`.

### <a name="monitor"></a> Monitorare l'app

L'SDK raccoglie automaticamente dati di telemetria sul runtime Node.js e su alcuni moduli comuni di terze parti. Usare l'applicazione per generare alcuni di questi dati.

Nel [portale di Azure][portal] passare quindi alla risorsa di Application Insights creata in precedenza. In **Panoramica sequenza temporale** cercare i primi punti dati. Per visualizzare altri dati dettagliati, selezionare diversi componenti nei grafici.

![Primi punti dati](./media/app-insights-nodejs/12-first-perf.png)

Per visualizzare la topologia individuata per l'app, fare clic sul pulsante **Mappa delle applicazioni**. Selezionare i componenti nella mappa per vedere altri dettagli.

![Mappa app di esempio](./media/app-insights-nodejs/06-appinsights_appmap.png)

Per altre informazioni sull'app e per risolvere i problemi, nella sezione **RICERCA CAUSA** selezionare le altre visualizzazioni disponibili.

![Sezione Analisi](./media/app-insights-nodejs/07-appinsights_investigate_blades.png)

#### <a name="no-data"></a>Dati non visualizzati

Dato che l'SDK esegue l'invio dei dati in batch, potrebbe verificarsi un ritardo nella visualizzazione degli elementi nel portale. Se i dati non sono visibili nella risorsa, provare alcune delle correzioni seguenti:

* Continuare a usare l'applicazione. Eseguire altre azioni per generare dati di telemetria aggiuntivi.
* Fare clic su **Aggiorna** nella visualizzazione della risorsa nel portale. I grafici si aggiornano periodicamente in automatico, ma l'aggiornamento manuale ha effetto immediato.
* Verificare che le [porte in uscita necessarie](app-insights-ip-addresses.md) siano aperte.
* Usare l'opzione [Cerca](app-insights-diagnostic-search.md) per cercare eventi specifici.
* Vedere le [domande frequenti][FAQ].


## <a name="sdk-configuration"></a>Configurazione dell'SDK

I metodi di configurazione dell'SDK e i valori predefiniti sono indicati nell'esempio di codice seguente.

Per correlare completamente gli eventi in un servizio, assicurarsi di impostare `.setAutoDependencyCorrelation(true)`. Con questa opzione impostata, l'SDK può tenere traccia del contesto tra callback asincroni in Node.js.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .start();
```

## <a name="telemetryclient-api"></a>API TelemetryClient

Per una descrizione completa dell'API TelemetryClient, vedere [API di Application Insights per metriche ed eventi personalizzati](app-insights-api-custom-events-metrics.md).

Con Application Insights Node.js SDK è possibile tenere traccia di qualsiasi richiesta, evento, metrica o eccezione. L'esempio di codice seguente illustra alcune API che è possibile usare:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey is in env var
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
let client = appInsights.defaultClient;

var success = false;
let startTime = Date.now();
// Execute dependency call here...
let duration = Date.now() - startTime;
success = true;

client.trackDependency({dependencyTypeName: "dependency name", name: "command name", duration: duration, success: success});
```

### <a name="add-a-custom-property-to-all-events"></a>Aggiungere una proprietà personalizzata a tutti gli eventi

Usare il codice seguente per aggiungere una proprietà personalizzata a tutti gli eventi:

```javascript
appInsights.defaultClient.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Tenere traccia delle richieste HTTP GET

Usare il codice seguente per tenere traccia delle richieste HTTP GET:

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.defaultClient.trackNodeHttpRequest({request: req, response: res});
    }
    // Other work here...
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

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare i dati di telemetria nel portale](app-insights-dashboards.md)
* [Presentazione dello strumento Analisi in Application Insights](app-insights-analytics-tour.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: app-insights-troubleshoot-faq.md

