<properties
	pageTitle="Developer Analytics"
	description="DevOps con Visual Studio, Application Insights e HockeyApp"
	authors="alancameronwills"
	services="application-insights"
    documentationCenter=""
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="05/18/2016"
	ms.author="awills"/>

# Developer Analytics con Application Insights e HockeyApp

*Application Insights è disponibile in anteprima.*

Molti progetti sono caratterizzati da un ciclo [DevOps](https://en.wikipedia.org/wiki/DevOps) rapido. Compilano e distribuiscono le proprie applicazioni, ricevono commenti e suggerimenti sulle prestazioni e sull'uso da parte degli utenti e usano queste informazioni per pianificare altri cicli di sviluppo.

Per monitorare l'uso e le prestazioni, è importante avere a disposizione i dati di telemetria dell'applicazione attiva, nonché i commenti e i suggerimenti degli utenti.

Molti sistemi sono costituiti da più componenti: un servizio Web, processori di back-end o archivi dati e un software client in esecuzione nel browser dell'utente o come app in un telefono o altro dispositivo. I dati di telemetria provenienti dai diversi dispositivi devono essere raccolti tutti insieme.

In alcune versioni la distribuzione è limitata ai tester designati e vengono anche organizzati test delle distribuzioni di versioni in anteprima, ovvero di nuove funzionalità destinate a un numero limitato di utenti, e test paralleli di interfacce utente alternative, detti test A|B.

La gestione delle distribuzioni e l'integrazione del monitoraggio di più componenti client e server non sono attività semplici. Il processo è parte essenziale dell'architettura dell'applicazione: non è possibile creare un sistema di questo tipo senza un ciclo di sviluppo iterativo e strumenti di monitoraggio efficaci.

Questo articolo spiega come gli aspetti del monitoraggio del ciclo devOps si integrano con le altre parti del processo.

Di seguito viene illustrato come esempio specifico [un interessante case study](http://aka.ms/mydrivingdocs) dove vengono gestiti più componenti client e server.

## Un ciclo DevOps

Gli strumenti Visual Studio e Developer Analytics offrono un'esperienza di devOps perfettamente integrata. Ad esempio, questo è un ciclo tipico per un'applicazione Web, che può essere Java, Node.js o ASP.NET:

![Ciclo devops app Web](./media/app-insights-developer-analytics/040.png)

* Uno sviluppatore archivia i dati nel repository di codice o li unisce nel ramo principale. Il repository nell'illustrazione è Git, ma potrebbe essere [Controllo della versione di Team Foundation](https://www.visualstudio.com/docs/tfvc/overview).
* Le modifiche attivano compilazione e unit test. Il servizio di compilazione può essere in [Visual Studio Team Services o nella controparte locale, Team Foundation Server](https://www.visualstudio.com/docs/vsts-tfs-overview).
* Se compilazione e unit test hanno esito positivo possono [attivare una distribuzione automatica](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition). L'host di app Web può essere il server Web in uso o Microsoft Azure.
* I dati di telemetria provenienti dall'app attiva vengono inviati ad [Application Insights](app-insights-overview.md), sia dal server che [dai browser client](app-insights-javascript.md), dove è possibile analizzare le prestazioni dell'app e dei modelli di utilizzo. I potenti [strumenti di ricerca](app-insights-analytics.md) consentono di diagnosticare eventuali problemi. Gli [avvisi](app-insights-alerts.md) segnalano un problema non appena si presenta.
* Il ciclo di sviluppo successivo riceve le informazioni risultanti dall'analisi dei dati di telemetria attivi.

### Applicazioni per dispositivi e desktop

Per le applicazioni per dispositivi e desktop la parte della distribuzione del ciclo è leggermente diversa, perché i dati non vengono semplicemente caricati in uno o due server. Al contrario, l'esito positivo di compilazione e test unit può [attivare il caricamento di HockeyApp](https://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs). HockeyApp svolge il ruolo di supervisore della distribuzione al team di utenti test o, se si preferisce, al pubblico in generale.


![Ciclo devops app dispositivo](./media/app-insights-developer-analytics/030.png)

HockeyApp raccoglie anche dati sulle prestazioni e i dati di utilizzo nei formati seguenti:

* Commenti e suggerimenti degli utenti con schermate
* Report sugli arresti anomali
* Dati di telemetria personalizzati codificati dall'utente.

Anche in questo caso il ciclo devOps termina quando si elaborano i piani di sviluppo futuri in base al feedback acquisito.


## Configurazione di Developer Analytics

I passaggi sono essenzialmente gli stessi per ogni componente dell'applicazione, che può essere mobile, Web o desktop. Per molti tipi di app, Visual Studio esegue automaticamente alcuni di questi passaggi.

1. Aggiungere l'SDK appropriato all'app. Per le app per dispositivi è HockeyApp e per i servizi Web è Application Insights. Per ogni soluzione esistono alcune varianti per le diverse piattaforme. È anche possibile usare l'SDK per applicazioni desktop, ma è consigliabile usare HockeyApp.
2. Registrare l'app con il portale Application Insights o HockeyApp, in base all'SDK usato. Qui vengono visualizzate le analisi provenienti dall'app attiva. Si ha a disposizione una chiave, o un ID, di strumentazione o da configurare nell'app in modo tale che l'SDK sappia sove inviare i dati di telemetria.
3. Aggiungere codice personalizzato, se necessario, per registrare eventi o metriche, che agevolano la diagnostica e l'analisi di prestazioni o uso. Molte attività di monitoraggio sono incorporate, quindi non saranno necessarie nel primo ciclo.
3. App per dispositivi:
 * Caricare una compilazione di debug in HockeyApp. Da qui è possibile distribuirla a un team di utenti test. Ogni volta che si caricano le compilazioni successive, il team riceverà una notifica.
 * Quando si configura il servizio di compilazione continua, creare una definizione della versione che usi il passaggio del plug-in per il caricamento in HockeyApp.

### Analisi ed esportazione per la telemetria di HockeyApp

È possibile esaminare la telemetria dei log e personalizzata di HockeyApp con le funzionalità di analisi ed esportazione continua di Application Insights [configurando un bridge](app-insights-hockeyapp-bridge-app.md).



## Passaggi successivi
 
Di seguito sono riportate le istruzioni dettagliate per i diversi tipi di app:

* [App Web ASP.NET](app-insights-asp-net.md)
* [App Web Java](app-insights-java-get-started.md)
* [App Web Node.js](https://github.com/Microsoft/ApplicationInsights-node.js)
* [App iOS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [App Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [App Android](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [App Windows Universale](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [App Windows Phone 8 e 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [App Windows Presentation Foundation](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

<!---HONumber=AcomDC_0907_2016-->