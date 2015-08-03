<properties 
	pageTitle="Application Insights SDK JavaScript API" 
	description="Documentazione di riferimento" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="awills"/>
 

# Application Insights SDK JavaScript API

L’SDK JavaScript viene caricato nella pagina web quando si imposta la[pagina web di rilevamento](app-insights-javascript.md)in[Application Insights](https://azure.microsoft.com/services/application-insights/).

[Panoramica dell'API ed esempi](app-insights-api-custom-events-metrics.md)

## classe AppInsights

La parte anteriore di reparto per il SDK invia i dati di telemetria ad Application Insights.

In una pagina web in cui è stata[Impostata la pagina web di rilevamento](app-insights-javascript.md)è possibile utilizzare l'istanza`appInsights`. ad esempio:
    
    appInsights.trackPageView("page1");



### trackPageView

    trackPageView(name?: string, url?: string, properties?:{[string]:string}, measurements?: {[string]:number})

Registri che una pagina o un contenitore simile è stato visualizzato all'utente.

 | | 
---|---|---
`name` | `? string` | Nome utilizzato per identificare la pagina del portale. Il valore predefinito è il titolo del documento.
`url` | `? string` | URL relativo o assoluto che identifica la pagina o un elemento simile. Il valore predefinito è la posizione della finestra.
`properties` | `? {[string]:string}` | Dati aggiuntivi utilizzati per filtrare le pagine e metriche nel portale. Il valore predefinito è vuoto.
`measurements` | `? {[string]:number}` | Metriche associate a questa pagina visualizzata in Esplora metriche nel portale. Il valore predefinito è vuoto.


### trackEvent

    trackEvent(name: string, properties?: {[string]:string}, measurements?: {[string]:string})

Registrare un'azione dell'utente o altre occorrenze.

Nel portale, è possibile selezionare gli eventi per nome, e[visualizzare grafici contarli o visualizzare le misure associate](app-insights-metrics-explorer.md).

È inoltre possibile cercare e[visualizzare singoli eventi](app-insights-diagnostic-search.md).

 | | 
---|---|---
 `name` | `string` | Identifica l'evento. Gli eventi con lo stesso nome vengono contati e possono essere rappresentati in[Explorer metrica](app-insights-metrics-explorer.md).
`properties` | `? {[string]:string}` | Dati aggiuntivi utilizzati per filtrare le pagine e metriche nel portale. Il valore predefinito è vuoto.
`measurements` | `? {[string]:number}` | Metriche associate a questa pagina visualizzata in Esplora metriche nel portale. Il valore predefinito è vuoto.


### trackMetric

    trackMetric(name: string, average: number, sampleCount?: number, min?: number, max?: number)


Accedere a un valore numerico che non è associato a un evento specifico. In genere utilizzato per inviare rapporti periodici di indicatori di prestazioni.

Nel portale, è possibile selezionare metriche tramite il nome a[grafico i valori nel tempo](app-insights-metrics-explorer.md). È possibile eseguire la ricerca o visualizzare singoli trackMetric chiamate.

Per inviare una singola misura, utilizzare solo i primi due parametri. Se si adottano misure molto spesso è possibile ridurre la larghezza di banda di telemetria di aggregazione di più parametri di misurazione ed inviare la media risultante a intervalli.

 | | 
---|---|---
`name` | `string` | Stringa che identifica la metrica. Nel portale, è possibile selezionare metriche da visualizzare per nome.
`average` | ` number` | Una singola misura o la media delle misurazioni diverse.
`sampleCount` | `? number` | Conteggio delle misurazioni rappresentato per la Media. Assume il valore predefinito 1.
`min` | `? number` | La misurazione più piccola dell'esempio. Il valore predefinito è la Media.
`max` | `? number` | La misura più grande nell'esempio. Il valore predefinito è la Media.

### trackException

    trackException(exception: Error, handledAt?: string, properties?: Object, measurements?: Object)

Registrare un'eccezione che si è rilevata. (Eccezioni intercettate da browser vengono inoltre registrate.)

Nel portale, è possibile procedere ad [ricerca nel tipo di eccezione e visualizzare il](app-insights-diagnostic-search.md)il tipo di messaggio e traccia dello stack di singole istanze.

 | | 
---|---|---
`exception` | `Error` | Un errore da una clausola catch.  
`handledAt` | `? string` | Il valore predefinito è "non gestito".
`properties` | `? {[string]:string}` | Dati aggiuntivi utilizzati per filtrare le pagine e metriche nel portale. Il valore predefinito è vuoto.
`measurements` | `? {[string]:number}` | Metriche associate a questa pagina visualizzata in Esplora metriche nel portale. Il valore predefinito è vuoto.

### trackTrace

    trackTrace(message: string, properties?: Object, measurements?: Object)

Registrare un evento di diagnostica, ad esempio entrare o uscire da un metodo.

Nel portale, è possibile cercare contenuto del messaggio e[ visualizzare gli eventi singoli trackTrace](app-insights-diagnostic-search.md). (A differenza di`trackEvent`, non è possibile filtrare il contenuto del messaggio nel portale.)

 | | 
---|---|---
`message` | `string` | Dati di diagnostica. Può essere molto più di un nome.

### lo scaricamento

    flush()

Inviare immediatamente tutti i dati di telemetria in coda.

Utilizzare questa opzione alla chiusura della finestra.


### config

    config: IConfig

Valori che controllano la modalità di invio dei dati di telemetria.

    interface IConfig {
        instrumentationKey: string;
        endpointUrl: string;
        accountId: string;
        appUserId: string;
        sessionRenewalMs: number; // 30 mins. 
        sessionExpirationMs: number; // 24h. 
        maxPayloadSizeInBytes: number; // 200k
        maxBatchSizeInBytes: number; // 100k
        maxBatchInterval: number; // 15000
        enableDebug: boolean;
        autoCollectErrors: boolean; // true
        disableTelemetry: boolean; // false
        verboseLogging: boolean;
        diagnosticLogInterval: number; // 10 000
    }

Impostare questi valori nel[ frammento](app-insights-javascript-api.md)inserito nelle pagine web. Cercare la seguente riga e aggiungere altri elementi:

    })({
      instrumentationKey: "000...000"
    });

### contesto

    context: TelemetryContext

Informazioni che il SDK tenta di estrarre dall'ambiente su utente, dispositivo e posizione.


## classe TelemetryContext




        /**
         * Details of the app you're monitoring.
         */
        public application: Context.Application;

        /**
         * The device the app is running on.
         */
        public device: Context.Device;

        /**
         * The user currently signed in.
         */
        public user: Context.User;

        /**
         * The user session. A session represents a series
         * of user actions. A session starts with a user action.
         * It ends when there is no user activity for 
         * sessionRenewalMs, 
         * or if it lasts longer than sessionExpirationMs.
         */
        public session: Context.Session;

        /**
         * The geographical location of the user's device,
         * if available.
         */
        public location: Context.Location;

        /**
         * Represents the user request. Operation id is used
         * to tie together related events in diagnostic search.
         */
        public operation: Context.Operation;

        /**
         * Default measurements to be attached by default to
         * all events.
         */
        public measurements: any;

        /**
         * Default properties to be attached by default to
         * all events. 
         */
        public properties: any;

        /**
         * Send telemetry object to the endpoint.
         * Returns telemetryObject.
         */
        public track(envelope: Telemetry.Common.Envelope) ;


## Codice open source

Leggere o contribuire alla[codice per il SDK](https://github.com/Microsoft/ApplicationInsights-js).

<!---HONumber=July15_HO4-->