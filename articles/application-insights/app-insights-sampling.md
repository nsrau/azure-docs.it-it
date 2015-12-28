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
	ms.date="11/23/2015" 
	ms.author="awills"/>

#  Campionamento in Application Insights

*Application Insights è disponibile in anteprima.*


Il campionamento è una funzionalità di Application Insights che consente di raccogliere e archiviare un set ridotto di telemetria mantenendo però un'analisi statisticamente corretta dei dati dell'applicazione. Riduce il traffico e consente di evitare la [limitazione della larghezza di banda della rete](app-insights-pricing.md#data-rate). I dati vengono filtrati in modo che gli elementi correlati vengano consentiti e sia quindi possibile spostarsi tra gli elementi quando si conducono indagini diagnostiche. Quando i conteggi delle metriche vengono presentati nel portale, vengono nuovamente normalizzati tenendo in considerazione il campionamento, per ridurre al minimo gli effetti sulle statistiche.

Il campionamento adattivo è abilitato per impostazione predefinita in Application Insights SDK per ASP.NET, versione 2.0.0-beta3 o successiva. Il campionamento attualmente è disponibile nella versione beta e in futuro potrebbe essere soggetto a modifiche.

Esistono due moduli di campionamento alternativi:

* Il campionamento adattivo regola automaticamente la percentuale di campionamento per raggiungere un volume specifico di richieste. Attualmente disponibile solo per la telemetria lato server di ASP.NET.  
* È disponibile anche il campionamento a frequenza fissa. Specificare la percentuale di campionamento. Disponibile per il codice dell'app Web ASP.NET e per le pagine Web JavaScript. Il client e il server sincronizzeranno il rispettivo campionamento in modo che nella ricerca sia possibile spostarsi tra le visualizzazioni pagina e le richieste correlate.

## Abilitazione del campionamento adattivo

**Aggiornare i pacchetti del progetto NuGet** all'ultima versione *preliminare* di Application Insights: fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni, scegliere Gestisci pacchetti NuGet, selezionare **Includi versione preliminare** e cercare Microsoft.ApplicationInsights.Web.

In [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) è possibile regolare diversi parametri nel nodo `AdaptiveSamplingTelemetryProcessor`. Le cifre indicate sono i valori predefiniti:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`

    Frequenza di destinazione che l'algoritmo adattivo deve raggiungere **su un singolo host server**. Se l'app Web viene eseguita su più host, sarà opportuno ridurre questo valore per non superare la frequenza di destinazione del traffico nel portale di Application Insights.

* `<EvaluationInterval>00:00:15</EvaluationInterval>`

    Intervallo in base al quale la frequenza corrente della telemetria viene rivalutata. La valutazione viene eseguita come media mobile. Potrebbe essere necessario ridurre questo intervallo se la telemetria è responsabile di burst improvvisi.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`

    Quando il valore della percentuale di campionamento cambia, periodo di tempo dopo il quale è consentito ridurre nuovamente la percentuale di campionamento per acquisire meno dati.

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageDecreaseTimeout>`

    Quando il valore della percentuale di campionamento cambia, periodo di tempo dopo il quale è consentito aumentare nuovamente la percentuale di campionamento per acquisire più dati.

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`

    Quando la percentuale di campionamento varia, valore minimo che è consentito impostare.

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`

    Quando la percentuale di campionamento varia, valore massimo che è consentito impostare.

* `<MovingAverageRatio>0.25</MovingAverageRatio>`

    Nel calcolo della media mobile, peso assegnato al valore più recente. Usare un valore uguale o inferiore a 1. I valori più bassi rendono l'algoritmo meno reattivo alle modifiche improvvise.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`

    Valore assegnato quando l'app è appena stata avviata. Non ridurlo durante il debug.

### Alternativa: configurare il campionamento adattivo nel codice

Invece di regolare il campionamento nel file .config, è possibile utilizzare il codice. Ciò consente di specificare una funzione di callback che viene richiamata ogni volta che si valuta nuovamente la frequenza di campionamento. È possibile utilizzarlo, ad esempio, per scoprire quale frequenza di campionamento si sta utilizzando.

Rimuovere il nodo `AdaptiveSamplingTelemetryProcessor` dal file .config.



*C#*

```C#

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    
    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Informazioni sui processori di telemetria](app-insights-api-filtering-sampling.md#filtering).)


<a name="other-web-pages"></a>
## Campionamento per pagine Web con JavaScript

È possibile configurare le pagine Web per il campionamento a frequenza fissa da qualsiasi server.

Quando si [configurano le pagine Web per Application Insights](app-insights-javascript.md), modificare il frammento ottenuto dal portale di Application Insights. Nelle app ASP.NET il frammento viene in genere salvato in \_Layout.cshtml. Inserire una riga simile a `samplingPercentage: 10,` prima della chiave di strumentazione:

    <script>
	var appInsights= ... 
	}({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
	samplingPercentage: 10, 

	instrumentationKey:...
	}); 
	
	window.appInsights=appInsights; 
	appInsights.trackPageView(); 
	</script> 

Come percentuale di campionamento, sceglierne una vicina a 100/N dove N è un numero intero. Il campionamento attualmente non supporta altri valori.

Se si abilita il campionamento a frequenza fissa nel server, i client e il server si sincronizzeranno in modo che nella ricerca sia possibile spostarsi tra le visualizzazioni pagina e le richieste correlate.


## Abilitazione del campionamento a frequenza fissa nel server

1. **Aggiornare i pacchetti NuGet del progetto** all'ultima versione *preliminare* di Application Insights. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni, scegliere Gestisci pacchetti NuGet, selezionare **Includi versione preliminare** e cercare Microsoft.ApplicationInsights.Web. 

2. **Disabilitare il campionamento adattivo**: in [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) rimuovere o impostare come commento il nodo `AdaptiveSamplingTelemetryProcessor`.

    ```xml

    <TelemetryProcessors>
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    

    ```

2. **Abilitare il modulo di campionamento a frequenza fissa.** Aggiungere questo frammento ad [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

    ```XML

    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>

    ```

> [AZURE.NOTE]Come percentuale di campionamento, sceglierne una vicina a 100/N dove N è un numero intero. Il campionamento attualmente non supporta altri valori.



### Alternativa: abilitare il campionamento a frequenza fissa nel codice del server


Invece di impostare il parametro di campionamento nel file .config, è possibile utilizzare il codice.

*C#*

```C#

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Informazioni sui processori di telemetria](app-insights-api-filtering-sampling.md#filtering).)

## Quando usare il campionamento?

Il campionamento adattivo viene automaticamente abilitato se si usa ASP.NET SDK versione 2.0.0-beta3 o successiva.

Per la maggior parte delle applicazioni di piccole e medie dimensioni, il campionamento non è necessario. Le informazioni di diagnostica più utili e le statistiche più accurate si ottengono raccogliendo dati su tutte le attività utente.

 
I vantaggi principali del campionamento sono:

* Il servizio Application Insights rimuove ("limita") i punti dati quando l'app invia una frequenza di telemetria molto elevata in un breve intervallo di tempo. 
* Non superare la [quota](app-insights-pricing.md) di punti dati per il proprio piano tariffario. 
* Ridurre il traffico di rete dalla raccolta di telemetria. 

### Campionamento fisso o adattivo?

Usare il campionamento a frequenza fissa se:

* È necessario il campionamento sincronizzato tra client e server, in modo che, quando si esaminano gli eventi in [Cerca](app-insights-diagnostic-search.md), sia possibile spostarsi tra gli eventi correlati nel client e nel server, ad esempio visualizzazioni pagina e richieste http.
* Se è certi della percentuale di campionamento appropriata per l'app. Deve essere abbastanza elevata da ottenere metriche accurate, ma al di sotto della frequenza che fa superare la quota di prezzo e le soglie di limitazione. 
* Non si deve eseguire il debug dell'app. Quando si preme F5 e si provano alcune pagine dell'app, sarà preferibile visualizzare tutta la telemetria.

In caso contrario, è consigliabile il campionamento adattivo.

## Come funziona il campionamento?

Dal punto di vista dell'applicazione, il campionamento è una funzionalità di Application Insights SDK. Si specifica la percentuale di tutti i punti dati da inviare al servizio Application Insights. A partire dalla versione 2.0.0 di Application Insights SDK è possibile controllare la percentuale di campionamento dal codice. Le versioni future dell'SDK consentiranno anche di configurare la percentuale di campionamento dal file ApplicationInsights.config.

L'SDK decide quali elementi della telemetria rimuovere e quali mantenere. La decisione sul campionamento si basa su alcune regole che hanno lo scopo di lasciare intatti tutti i punti dati correlati, mantenendo in Application Insights un'esperienza di diagnostica sfruttabile e affidabile anche con un set di dati ridotto. Se, ad esempio, per una richiesta non riuscita l'app invia elementi di telemetria aggiuntivi (come eccezioni e tracce registrate da questa richiesta), il campionamento non dividerà la richiesta e il resto della telemetria, ma conserverà o rimuoverà gli elementi tutti insieme. Di conseguenza, quando si osservano i dettagli della richiesta in Application Insights, è sempre possibile visualizzare la richiesta con gli elementi di telemetria associati.

Per le applicazioni che definiscono "user" (la maggior parte delle normali applicazioni Web), la decisione sul campionamento si basa sull'hash dell'ID utente, vale a dire che tutta la telemetria associata a un particolare utente viene conservata o rimossa. Per i tipi di applicazioni che non definiscono gli utenti (ad esempio, i servizi Web), la decisione sul campionamento si basa sull'ID operazione della richiesta. Infine, per gli elementi della telemetria per cui non è impostato né l'ID utente né l'ID operazione (ad esempio, gli elementi della telemetria segnalati da thread asincroni senza contesto http), il campionamento si limita ad acquisire una percentuale degli elementi della telemetria di ogni tipo.

Quando la telemetria viene ripresentata all'utente, il servizio Application Insights modifica le metriche in base alla stessa percentuale di campionamento usata in fase di raccolta, per compensare i punti dati mancanti. Quindi, quando osservano la telemetria in Application Insights, gli utenti visualizzano approssimazioni statisticamente corrette molto vicine ai numeri reali.

La precisione dell'approssimazione dipende in gran parte dalla percentuale di campionamento configurata. La precisione è anche maggiore per le applicazioni che gestiscono un volume elevato di richieste generalmente simili da una grande quantità di utenti. Per le applicazioni che non gestiscono un carico di lavoro significativo, invece, il campionamento non è necessario perché queste applicazioni in genere riescono a inviare tutta la telemetria senza superare la quota e senza causare perdite di dati dovute alla limitazione.

Si noti che Application Insights non campiona i tipi di telemetria relativi a metrica e sessioni, perché la riduzione della precisione per questi tipi non è consigliabile.

### Campionamento adattivo

Il campionamento adattivo aggiunge un componente che monitora la frequenza corrente di trasmissione dall'SDK e regola la percentuale di campionamento per cercare di rimanere entro la frequenza massima di destinazione. La rettifica viene ricalcolata a intervalli regolari e si basa su una media mobile della frequenza di trasmissione in uscita.

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

 * Sì, il campionamento adattivo modifica gradualmente la percentuale di campionamento, in base al volume attualmente osservato della telemetria.

*È possibile conoscere la frequenza di campionamento usata dal campionamento adattivo?*

 * Sì - utilizzare il metodo del codice di configurazione del campionamento adattivo, e sarà possibile fornire un callback che ottiene la frequenza di campionamento.

*Se si usa il campionamento a frequenza fissa, come stabilire quale sarà la percentuale di campionamento ideale per l'app?*

* Una modalità è quella di iniziare con il campionamento adattivo, scoprire quale frequenza è impostata (vedere la domanda precedente) e quindi cambiarla a campionamento a frequenza fissa usando quella frequenza. 

    In caso contrario, è necessario usare l'intuito. Analizzare l'utilizzo della telemetria corrente in AI, osservare le possibili limitazioni in corso e stimare il volume della telemetria raccolta. Questi tre input, insieme al piano tariffario selezionato, suggeriranno di quanto ridurre il volume della telemetria raccolta. Tuttavia, un aumento nel numero degli utenti o qualsiasi altra migrazione nel volume di telemetria potrebbe invalidare la stima.

*Cosa accade se si configura una percentuale di campionamento troppo bassa?*

* Una percentuale di campionamento troppo bassa (campionamento eccessivamente aggressivo) ridurrà la precisione delle approssimazioni, quando Application Insights tenterà di compensare la visualizzazione dei dati per la riduzione del volume dei dati. Anche l'esperienza di diagnostica potrebbe risultare compromessa, perché potrebbero venire campionate alcune richieste lente o non riuscite.

*Cosa accade se si configura una percentuale di campionamento troppo alta?*

* Configurando una percentuale di campionamento troppo elevata (non abbastanza aggressiva), si otterrà una riduzione insufficiente del volume della telemetria raccolta. Potrebbe tuttavia verificarsi una perdita dei dati di telemetria correlata alla limitazione e il costo per l'uso di Application Insights potrebbe essere superiore al previsto per l'applicazione di tariffe aggiuntive.

*Su quali piattaforme è possibile usare il campionamento?*

* Il campionamento adattivo è attualmente disponibile per il lato server delle app Web ASP.NET (ospitate in Azure o sul proprio server). Il campionamento a frequenza fissa è disponibile per tutte le pagine Web e per i lati client e server delle applicazioni Web .NET.

*Esistono alcuni eventi rari che si vuole visualizzare sempre. Come è possibile passarli al modulo di campionamento?*

 * Inizializzare un'istanza separata di TelemetryClient con una nuova TelemetryConfiguration (non con quello predefinito attivo). Usarla per inviare gli eventi rari.

<!---HONumber=AcomDC_1217_2015-->