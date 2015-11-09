<properties 
	pageTitle="Campionamento della telemetria in Application Insights" 
	description="Come tenere sotto controllo il volume della telemetria." 
	services="application-insights" 
    documentationCenter="windows"
	authors="vgorbenko" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015" 
	ms.author="awills"/>

#  Campionamento in Application Insights

*Application Insights è disponibile in anteprima.*


Il campionamento è un'opzione di Application Insights che consente di raccogliere e archiviare un set ridotto di telemetria mantenendo però un'analisi statisticamente corretta dei dati dell'applicazione. Di solito viene usato per ridurre il traffico ed evitare [limitazioni](app-insights-pricing.md#data-rate). I dati vengono filtrati in modo che gli elementi correlati vengano consentiti e sia quindi possibile condurre indagini diagnostiche con un set di dati ridotto. Il lato client e server coordinano automaticamente l'operazione di filtro degli elementi correlati. Quando i conteggi delle metriche vengono presentati nel portale, vengono nuovamente normalizzati tenendo in considerazione il campionamento, per ridurre al minimo gli effetti sulle statistiche.


Il campionamento attualmente è disponibile nella versione beta e in futuro potrebbe essere soggetto a modifiche.

## Configurazione del campionamento per l'applicazione

Il campionamento attualmente è disponibile per ASP.NET SDK o per [qualsiasi pagina Web](#other-web-pages).

### Server ASP.NET

1. Aggiornare i pacchetti NuGet del progetto all'ultima versione *preliminare* di Application Insights. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni, scegliere Gestisci pacchetti NuGet, selezionare **Includi versione preliminare** e cercare Microsoft.ApplicationInsights.Web. 

2. Aggiungere questo frammento ad ApplicationInsights.config:

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```

> [AZURE.NOTE]Come percentuale di campionamento, sceglierne una vicina a 100/N dove N è un numero intero. Il campionamento attualmente non supporta altri valori.

<a name="other-web-pages"></a>
### Pagine Web con JavaScript

È possibile configurare le pagine Web per il campionamento da qualsiasi server. Per i server ASP.NET, configurare sia il lato client che il lato server.

Quando si [configurano le pagine Web per Application Insights](app-insights-javascript.md), modificare il frammento ottenuto dal portale di Application Insights. In ASP.NET è possibile trovarlo in \_Layout.cshtml. Inserire una riga simile a `samplingPercentage: 10,` prima della chiave di strumentazione:

    <script>
	var appInsights= ... 
	}({ 

	samplingPercentage: 10, 

	instrumentationKey:...
	}); 
	
	window.appInsights=appInsights; 
	appInsights.trackPageView(); 
	</script> 

Assicurarsi di specificare in JavaScript la stessa percentuale di campionamento del lato server.

[Altre informazioni sull'API](app-insights-api-custom-events-metrics.md)


### In alternativa: impostare campionamento nel codice server


Invece di impostare il parametro di campionamento nel file .config, è possibile utilizzare il codice. In questo modo è possibile attivare o disattivare il campionamento.

*C#*

```C#

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // It's recommended to set SamplingPercentage in the .config file instead.

    // This configures sampling percentage at 10%:
    TelemetryConfiguration.Active.TelemetryChannel = new TelemetryChannelBuilder().UseSampling(10.0).Build();

```


## Quando usare il campionamento?

Per la maggior parte delle applicazioni di piccole e medie dimensioni, il campionamento non è necessario. Le informazioni di diagnostica più utili e le statistiche più accurate si ottengono raccogliendo dati su tutte le attività utente.

 
I motivi principali per usare il campionamento sono i seguenti:


* Il servizio Application Insights rimuove ("limita") i punti dati quando l'app invia una frequenza di telemetria molto elevata in un breve intervallo di tempo. 
* Non si vuole superare la [quota](app-insights-pricing.md) di punti dati per il proprio piano tariffario. 
* Ridurre il traffico di rete dalla raccolta di telemetria. 

## Come funziona il campionamento?

Dal punto di vista dell'applicazione, il campionamento è una funzionalità di Application Insights SDK. Si specifica la percentuale di tutti i punti dati da inviare al servizio Application Insights. A partire dalla versione 2.0.0 di Application Insights SDK è possibile controllare la percentuale di campionamento dal codice. Le versioni future dell'SDK consentiranno anche di configurare la percentuale di campionamento dal file ApplicationInsights.config.

L'SDK decide quali elementi della telemetria rimuovere e quali mantenere. La decisione sul campionamento si basa su alcune regole che hanno lo scopo di lasciare intatti tutti i punti dati correlati, mantenendo in Application Insights un'esperienza di diagnostica sfruttabile e affidabile anche con un set di dati ridotto. Se, ad esempio, per una richiesta non riuscita l'app invia elementi di telemetria aggiuntivi (come eccezioni e tracce registrate da questa richiesta), il campionamento non dividerà la richiesta e il resto della telemetria, ma conserverà o rimuoverà gli elementi tutti insieme. Di conseguenza, quando si osservano i dettagli della richiesta in Application Insights, è sempre possibile visualizzare la richiesta con gli elementi di telemetria associati.

Per le applicazioni che definiscono "user" (la maggior parte delle normali applicazioni Web), la decisione sul campionamento si basa sull'hash dell'ID utente, vale a dire che tutta la telemetria associata a un particolare utente viene conservata o rimossa. Per i tipi di applicazioni che non definiscono gli utenti (ad esempio, i servizi Web), la decisione sul campionamento si basa sull'ID operazione della richiesta. Infine, per gli elementi della telemetria per cui non è impostato né l'ID utente né l'ID operazione (ad esempio, gli elementi della telemetria segnalati da thread asincroni senza contesto http), il campionamento si limita ad acquisire una percentuale degli elementi della telemetria di ogni tipo.

Quando la telemetria viene ripresentata all'utente, il servizio Application Insights modifica le metriche in base alla stessa percentuale di campionamento usata in fase di raccolta, per compensare i punti dati mancanti. Quindi, quando osservano la telemetria in Application Insights, gli utenti visualizzano approssimazioni statisticamente corrette molto vicine ai numeri reali.

La precisione dell'approssimazione dipende in gran parte dalla percentuale di campionamento configurata. La precisione è anche maggiore per le applicazioni che gestiscono un volume elevato di richieste generalmente simili da una grande quantità di utenti. Per le applicazioni che non gestiscono un carico di lavoro significativo, invece, il campionamento non è necessario perché queste applicazioni in genere riescono a inviare tutta la telemetria senza superare la quota e senza causare perdite di dati dovute alla limitazione.

Si noti che Application Insights non campiona i tipi di telemetria relativi a metrica e sessioni, perché la riduzione della precisione per questi tipi non è consigliabile.

## Campionamento e JavaScript SDK

L'SDK lato client (JavaScript) partecipa al campionamento insieme all'SDK lato server. Le pagine instrumentate invieranno solo la telemetria lato client dagli stessi utenti per cui il lato server ha preso la decisione di "eseguire il campionamento internamente". Questa logica è concepita per mantenere l'integrità della sessione utente sui lati client e server. Di conseguenza, da un particolare elemento della telemetria in Application Insights è possibile trovare tutti gli altri elementi della telemetria per questa sessione utente.

*La telemetria lato e client e server non mostra i campioni coordinati, come descritto sopra.*

* Verificare di avere abilitato il campionamento sia sul server che sul client.
* Assicurarsi che la versione dell'SDK sia 2.0 o successiva.
* Controllare di avere impostato la stessa percentuale di campionamento sia nel client che nel server.


## Domande frequenti 

*Perché il campionamento non è una semplice "raccolta di percentuale X di ogni tipo di telemetria"?*

 *  Anche se questo approccio al campionamento offre una precisione davvero elevata nelle approssimazioni delle metriche, tuttavia non permette di correlare i dati diagnostici per utente, sessione e richiesta, come è indispensabile per la diagnostica. Il campionamento quindi funziona meglio come logica di "raccolta di tutti gli elementi della telemetria per una percentuale X di utenti dell'app" o di "raccolta di tutta la telemetria per una percentuale X di richieste app". Per gli elementi della telemetria non associati alle richieste (ad esempio, l'elaborazione asincrona in background), il fallback prevede la "raccolta di una percentuale X di tutti gli elementi per ogni tipo di telemetria". 

*La percentuale di campionamento può variare nel tempo?*

 * Nell'implementazione attuale di solito non si modifica la percentuale di campionamento dopo averla configurata all'avvio dell'applicazione. Anche avendo il controllo sul runtime della percentuale di campionamento, non è possibile determinare quale sarà la percentuale di campionamento ottimale che raccoglierà "solo la quantità corretta di volume di dati" prima che la logica di limitazione abbia effetto o venga raggiunta la quota del volume di dati. Le versioni future di Application Insights SDK includeranno il campionamento adattivo che aumenterà o ridurrà immediatamente la percentuale di campionamento, in base al volume attualmente osservato della telemetria e degli altri fattori. 

*Come stabilire quale sarà la percentuale di campionamento ideale per l'app?*

* Per ora è necessario usare l'intuito. Analizzare l'utilizzo della telemetria corrente in AI, osservare le eliminazioni di dati correlate alla limitazione e stimare il volume della telemetria raccolta. Questi tre input, insieme al piano tariffario selezionato, suggeriranno di quanto ridurre il volume della telemetria raccolta. Tuttavia, una variazione nel modello del volume della telemetria potrebbe invalidare la percentuale di campionamento ottimale configurata (ad esempio, un aumento del numero degli utenti). Una volta implementato, il campionamento adattivo imposterà automaticamente la percentuale di campionamento sul livello ottimale, in base al volume della telemetria osservato.

*Cosa accade se si configura una percentuale di campionamento troppo bassa?*

* Una percentuale di campionamento troppo bassa (campionamento eccessivamente aggressivo) ridurrà la precisione delle approssimazioni, quando Application Insights tenterà di compensare la visualizzazione dei dati per la riduzione del volume dei dati. Anche l'esperienza di diagnostica potrebbe risultare compromessa, perché potrebbero venire campionate alcune richieste lente o non riuscite.

*Cosa accade se si configura una percentuale di campionamento troppo alta?*

* Configurando una percentuale di campionamento troppo elevata (non abbastanza aggressiva), si otterrà una riduzione insufficiente del volume della telemetria raccolta. Potrebbe tuttavia verificarsi una perdita dei dati di telemetria correlata alla limitazione e il costo per l'uso di Application Insights potrebbe essere superiore al previsto per l'applicazione di tariffe aggiuntive.

*Su quali piattaforme è possibile usare il campionamento?*

* Il campionamento attualmente è disponibile per tutte le pagine Web e per i lati client e server delle applicazioni Web .NET.

*È possibile usare il campionamento con le app per dispositivo (app di Windows Phone, iOS, Android o desktop)?*

* No, il campionamento per le applicazioni per dispositivo al momento non è supportato. 

<!---HONumber=Nov15_HO1-->