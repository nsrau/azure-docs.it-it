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
	ms.date="05/07/2016" 
	ms.author="awills"/>

#  Campionamento in Application Insights

*Application Insights è disponibile in anteprima.*


Il campionamento è una funzionalità di Application Insights che consente di raccogliere e archiviare un set ridotto di telemetria mantenendo però un'analisi statisticamente corretta dei dati dell'applicazione. Riduce il traffico e consente di evitare la [limitazione della larghezza di banda della rete](app-insights-pricing.md#data-rate). I dati vengono filtrati in modo che gli elementi correlati vengano consentiti e sia quindi possibile spostarsi tra gli elementi quando si conducono indagini diagnostiche. Quando i conteggi delle metriche vengono presentati nel portale, vengono nuovamente normalizzati tenendo in considerazione il campionamento, per ridurre al minimo gli effetti sulle statistiche.

Il campionamento attualmente è disponibile nella versione beta e in futuro potrebbe essere soggetto a modifiche.

## In breve:

* Il campionamento mantiene un record su *n* e rimuove il resto. Ad esempio, potrebbe mantenere 1 un evento su 5, corrispondente a una frequenza di campionamento del 20%.
* Se l'applicazione invia molti dati di telemetria, il campionamento si verifica automaticamente. Il campionamento automatico interviene solo in presenza di volumi elevati e solo in applicazioni server Web ASP.NET.
* È inoltre possibile impostare il campionamento manualmente, nella pagina del portale relativa ai prezzi (per ridurre il volume dei dati di telemetria mantenuti e rispettare la quota mensile) o nel file con estensione config di ASP.NET SDK, per ridurre anche il traffico di rete.
* La frequenza di campionamento corrente è una proprietà di ogni record. Nella finestra di ricerca aprire un evento, ad esempio una richiesta. Espandere tutte le proprietà tramite i puntini di sospensione "…" per trovare la proprietà "* count", ad esempio, "request count" o "event count", a seconda del tipo di telemetria. Se è > 1, il campionamento è in corso. Se è uguale a 3, il campionamento è al 33%: ogni record mantenuto rappresenta 3 record generati originariamente.
* Se si registrano eventi personalizzati e ci si vuole assicurare che gli eventi di un set vengano mantenuti o rimossi insieme, verificare che abbiano lo stesso valore OperationId.
* Se si scrivono query di Dati di analisi, è necessario [tener conto del campionamento](app-insights-analytics-tour.md#counting-sampled-data). In particolare, anziché eseguire semplicemente il conteggio dei record, è necessario usare `summarize sum(itemCount)`.


## Tipi di campionamento

Esistono tre diversi metodi di campionamento:

* **Campionamento adattivo**, che regola automaticamente il volume dei dati di telemetria inviati dall'SDK nell'app ASP.NET. Si tratta di un'opzione predefinita dell'SDK versione 2.0.0-beta3.
* **Campionamento a frequenza fissa**, che riduce il volume dei dati di telemetria inviati sia dal server ASP.NET che dai browser degli utenti. È necessario impostare la frequenza.
* **Campionamento per inserimento**, che riduce il volume dei dati di telemetria mantenuto dal servizio Application Insights in base a una frequenza impostata dall'utente. Non riduce il traffico di telemetria, ma consente all'utente di rispettare la quota mensile.

## Campionamento per inserimento

Questa forma di campionamento opera nel punto in cui i dati di telemetria di server Web, browser e dispositivi raggiungono l'endpoint di servizio di Application Insights. Anche se non riduce il traffico dei dati di telemetria inviato dall'app, riduce la quantità di dati elaborati, conservati e addebitati da Application Insights.

Usare questo tipo di campionamento se l'app spesso supera la quota mensile e non si ha la possibilità di usare uno dei tipi di campionamento basati sull'SDK.

Impostare la frequenza di campionamento nel pannello Quota + prezzi:

![Nel pannello Panoramica sull'applicazione fare clic su Impostazioni, Quota, Esempi e quindi selezionare una frequenza di campionamento e fare clic su Aggiorna.](./media/app-insights-sampling/04.png)

Come in altri tipi di campionamento, l'algoritmo consente di mantenere gli elementi di telemetria correlati. Ad esempio, quando si controllano i dati di telemetria nella ricerca, sarà possibile trovare la richiesta correlata a una particolare eccezione. I conteggi di metrica, ad esempio la frequenza delle richieste e delle eccezioni, vengono mantenuti correttamente.

I punti dati che vengono rimossi dal campionamento non sono disponibili in alcuna funzionalità di Application Insights, ad esempio nell'[esportazione continua](app-insights-export-telemetry.md).

Il campionamento per inserimento non funziona mentre è attivo il campionamento a frequenza fissa o adattivo basato sull'SDK. Se la frequenza di campionamento nell'SDK è inferiore al 100%, la frequenza di campionamento di inserimento impostata viene ignorata.


## Campionamento adattivo nel server Web

Il campionamento adattivo è abilitato per impostazione predefinita ed è disponibile in Application Insights SDK per ASP.NET, versione 2.0.0-beta3 o successiva.


Il campionamento adattivo riguarda il volume dei dati di telemetria inviati dall'app del server Web al servizio Application Insights. Il volume viene regolato automaticamente affinché rimanga in una frequenza massima specificata del traffico.

Non opera a bassi volumi di dati di telemetria, pertanto un'app per eseguire il debug o un sito Web con un basso utilizzo non saranno interessate.

Per ottenere il volume di destinazione, alcuni dei dati di telemetria generati vengono eliminati. Tuttavia, come in altri tipi di campionamento, l'algoritmo consente di mantenere gli elementi di telemetria correlati. Ad esempio, quando si controllano i dati di telemetria nella ricerca, sarà possibile trovare la richiesta correlata a una particolare eccezione.

I conteggi di metrica, ad esempio la frequenza delle richieste e delle eccezioni, vengono adattati per compensare la frequenza di campionamento, in modo che mostrino i valori corretti in Esplora metriche.

**Aggiornare i pacchetti del progetto NuGet** all'ultima versione *preliminare* di Application Insights: fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni, scegliere Gestisci pacchetti NuGet, selezionare **Includi versione preliminare** e cercare Microsoft.ApplicationInsights.Web.

In [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) è possibile regolare diversi parametri nel nodo `AdaptiveSamplingTelemetryProcessor`. Le cifre indicate sono i valori predefiniti:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`

    Frequenza di destinazione che l'algoritmo adattivo deve raggiungere **su un singolo host server**. Se l'app Web viene eseguita su più host, sarà opportuno ridurre questo valore per non superare la frequenza di destinazione del traffico nel portale di Application Insights.

* `<EvaluationInterval>00:00:15</EvaluationInterval>`

    Intervallo in base al quale la frequenza corrente della telemetria viene rivalutata. La valutazione viene eseguita come media mobile. Potrebbe essere necessario ridurre questo intervallo se la telemetria è responsabile di burst improvvisi.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`

    Quando il valore della percentuale di campionamento cambia, periodo di tempo dopo il quale è consentito ridurre nuovamente la percentuale di campionamento per acquisire meno dati.

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`

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

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    
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


## Campionamento a frequenza fissa per siti Web ASP.NET

Il campionamento a frequenza fissa riduce il traffico inviato dal server e dai Web browser. A differenza del campionamento adattivo, riduce i dati di telemetria a una frequenza fissa definita dall'utente. Sincronizza inoltre il campionamento del client e del server in modo che gli elementi correlati vengano mantenuti, ad esempio in modo che se si esamina una pagina di ricerca, è possibile trovare la richiesta correlata.

L'algoritmo di campionamento mantiene gli elementi correlati. Per ciascun evento di richiesta HTTP, l'algoritmo e gli eventi correlati vengono eliminati o trasmessi.

In Esplora metriche, frequenze quali il numero di richieste ed eccezioni vengono moltiplicate per un fattore in modo da compensare la frequenza di campionamento ed essere quindi corretti.

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

> [AZURE.NOTE] Come percentuale di campionamento, sceglierne una vicina a 100/N dove N è un numero intero. Il campionamento attualmente non supporta altri valori.



### Alternativa: abilitare il campionamento a frequenza fissa nel codice del server locale


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

Il campionamento adattivo viene automaticamente abilitato se si usa ASP.NET SDK versione 2.0.0-beta3 o successiva. Nel server Microsoft è possibile usare il campionamento per inserimento indipendentemente dalla versione dell'SDK in uso.

Per la maggior parte delle applicazioni di piccole e medie dimensioni, il campionamento non è necessario. Le informazioni di diagnostica più utili e le statistiche più accurate si ottengono raccogliendo dati su tutte le attività utente.

 
I vantaggi principali del campionamento sono:

* Il servizio Application Insights rimuove ("limita") i punti dati quando l'app invia una frequenza di telemetria molto elevata in un breve intervallo di tempo.
* Non superare la [quota](app-insights-pricing.md) di punti dati per il proprio piano tariffario.
* Ridurre il traffico di rete dalla raccolta di telemetria.

### Quale tipo di campionamento è opportuno usare?


**Usare il campionamento per inserimento se:**

* Si supera spesso la quota mensile dei dati di telemetria.
* Si usa una versione dell'SDK che non supporta il campionamento, ad esempio Java SDK o versioni di ASP.NET precedenti alla 2.
* Si ricevono grandi quantità di dati di telemetria dal Web browser degli utenti.

**Usare il campionamento a frequenza fissa se:**

* Si usa Application Insights SDK per i servizi Web ASP.NET versione 2.0.0 o successiva e
* È necessario il campionamento sincronizzato tra client e server, in modo che, quando si esaminano gli eventi in [Cerca](app-insights-diagnostic-search.md), sia possibile spostarsi tra gli eventi correlati nel client e nel server, ad esempio visualizzazioni pagina e richieste http.
* Se è certi della percentuale di campionamento appropriata per l'app. Deve essere abbastanza elevata da ottenere metriche accurate, ma al di sotto della frequenza che fa superare la quota di prezzo e le soglie di limitazione.


**Usare il campionamento adattivo:**

In caso contrario, è consigliabile il campionamento adattivo. Questo tipo di campionamento è abilitato per impostazione predefinita nell'SDK del server ASP.NET versione 2.0.0-beta3. Non riduce il traffico fino a una determinata frequenza minima, in modo da non avere effetto su un sito poco usato.


## Come funziona il campionamento?

Il campionamento a frequenza fissa e il campionamento adattivo sono funzionalità dell'SDK nella versione ASP.NET 2.0.0 o successiva. Il campionamento per inserimento è una funzionalità del servizio Application Insights ed è operativo se l'SDK non sta eseguendo un altro campionamento.

L'algoritmo di campionamento decide quali elementi di telemetria eliminare e quali mantenere, sia che venga eseguito nell'SDK o nel servizio Application Insights. La decisione sul campionamento si basa su alcune regole che hanno lo scopo di lasciare intatti tutti i punti dati correlati, mantenendo in Application Insights un'esperienza di diagnostica sfruttabile e affidabile anche con un set di dati ridotto. Se, ad esempio, per una richiesta non riuscita l'app invia elementi di telemetria aggiuntivi (come eccezioni e tracce registrate da questa richiesta), il campionamento non dividerà la richiesta e il resto della telemetria, ma conserverà o rimuoverà gli elementi tutti insieme. Di conseguenza, quando si osservano i dettagli della richiesta in Application Insights, è sempre possibile visualizzare la richiesta con gli elementi di telemetria associati.

Per le applicazioni che definiscono "user" (la maggior parte delle normali applicazioni Web), la decisione sul campionamento si basa sull'hash dell'ID utente, vale a dire che tutta la telemetria associata a un particolare utente viene conservata o rimossa. Per i tipi di applicazioni che non definiscono gli utenti (ad esempio, i servizi Web), la decisione sul campionamento si basa sull'ID operazione della richiesta. Infine, per gli elementi della telemetria per cui non è impostato né l'ID utente né l'ID operazione (ad esempio, gli elementi della telemetria segnalati da thread asincroni senza contesto http), il campionamento si limita ad acquisire una percentuale degli elementi della telemetria di ogni tipo.

Quando la telemetria viene ripresentata all'utente, il servizio Application Insights modifica le metriche in base alla stessa percentuale di campionamento usata in fase di raccolta, per compensare i punti dati mancanti. Quindi, quando osservano la telemetria in Application Insights, gli utenti visualizzano approssimazioni statisticamente corrette molto vicine ai numeri reali.

La precisione dell'approssimazione dipende in gran parte dalla percentuale di campionamento configurata. La precisione è anche maggiore per le applicazioni che gestiscono un volume elevato di richieste generalmente simili da una grande quantità di utenti. Per le applicazioni che non gestiscono un carico di lavoro significativo, invece, il campionamento non è necessario perché queste applicazioni in genere riescono a inviare tutta la telemetria senza superare la quota e senza causare perdite di dati dovute alla limitazione.

Si noti che Application Insights non campiona i tipi di telemetria relativi a metrica e sessioni, perché la riduzione della precisione per questi tipi non è consigliabile.

### Campionamento adattivo

Il campionamento adattivo aggiunge un componente che monitora la frequenza corrente di trasmissione dall'SDK e regola la percentuale di campionamento per cercare di rimanere entro la frequenza massima di destinazione. La rettifica viene ricalcolata a intervalli regolari e si basa su una media mobile della frequenza di trasmissione in uscita.

## Campionamento e JavaScript SDK

L'SDK lato client (JavaScript) partecipa al campionamento a frequenza fissa insieme all'SDK lato server. Le pagine instrumentate invieranno solo la telemetria lato client dagli stessi utenti per cui il lato server ha preso la decisione di "eseguire il campionamento internamente". Questa logica è concepita per mantenere l'integrità della sessione utente sui lati client e server. Di conseguenza, da un particolare elemento della telemetria in Application Insights è possibile trovare tutti gli altri elementi della telemetria per questa sessione utente.

*La telemetria lato e client e server non mostra i campioni coordinati, come descritto sopra.*

* Verificare di avere abilitato il campionamento a frequenza fissa sia sul server che sul client.
* Assicurarsi che la versione dell'SDK sia 2.0 o successiva.
* Controllare di avere impostato la stessa percentuale di campionamento sia nel client che nel server.


## Domande frequenti 

*Perché il campionamento non è una semplice "raccolta di percentuale X di ogni tipo di telemetria"?*

 *  Anche se questo approccio al campionamento offre una precisione davvero elevata nelle approssimazioni delle metriche, tuttavia non permette di correlare i dati diagnostici per utente, sessione e richiesta, come è indispensabile per la diagnostica. Il campionamento quindi funziona meglio come logica di "raccolta di tutti gli elementi della telemetria per una percentuale X di utenti dell'app" o di "raccolta di tutta la telemetria per una percentuale X di richieste app". Per gli elementi della telemetria non associati alle richieste (ad esempio, l'elaborazione asincrona in background), il fallback prevede la "raccolta di una percentuale X di tutti gli elementi per ogni tipo di telemetria".

*La percentuale di campionamento può variare nel tempo?*

 * Sì, il campionamento adattivo modifica gradualmente la percentuale di campionamento, in base al volume attualmente osservato della telemetria.

 

*Se si usa il campionamento a frequenza fissa, come stabilire quale sarà la percentuale di campionamento ideale per l'app?*

* Una modalità è quella di iniziare con il campionamento adattivo, scoprire quale frequenza è impostata (vedere la domanda precedente) e quindi cambiarla a campionamento a frequenza fissa usando quella frequenza.

    In caso contrario, è necessario usare l'intuito. Analizzare l'utilizzo della telemetria corrente in AI, osservare le possibili limitazioni in corso e stimare il volume della telemetria raccolta. Questi tre input, insieme al piano tariffario selezionato, suggeriranno di quanto ridurre il volume della telemetria raccolta. Tuttavia, un aumento nel numero degli utenti o qualsiasi altra migrazione nel volume di telemetria potrebbe invalidare la stima.

*Cosa accade se si configura una percentuale di campionamento troppo bassa?*

* Una percentuale di campionamento troppo bassa (campionamento eccessivamente aggressivo) ridurrà la precisione delle approssimazioni, quando Application Insights tenterà di compensare la visualizzazione dei dati per la riduzione del volume dei dati. Anche l'esperienza di diagnostica potrebbe risultare compromessa, perché potrebbero venire campionate alcune richieste lente o non riuscite.

*Cosa accade se si configura una percentuale di campionamento troppo alta?*

* Configurando una percentuale di campionamento troppo elevata (non abbastanza aggressiva), si otterrà una riduzione insufficiente del volume della telemetria raccolta. Potrebbe tuttavia verificarsi una perdita dei dati di telemetria correlata alla limitazione e il costo per l'uso di Application Insights potrebbe essere superiore al previsto per l'applicazione di tariffe aggiuntive.

*Su quali piattaforme è possibile usare il campionamento?*

* Se nell'SDK non è impostato il campionamento, si verifica automaticamente il campionamento per inserimento per i dati di telemetria superiori a un determinato volume. Si verifica, ad esempio, se l'app usa un server Java o se si esegue una versione precedente dell'SDK ASP.NET.

* Se si usa l'SDK ASP.NET versione 2.0.0 o successiva, ospitato in Azure o sul server in uso, per impostazione predefinita viene eseguito il campionamento adattivo, ma è comunque possibile passare al campionamento a frequenza fissa, come descritto in precedenza. Con il campionamento a frequenza fissa, l'SDK del browser si sincronizza automaticamente con gli eventi correlati al campione.

*Esistono alcuni eventi rari che si vuole visualizzare sempre. Come è possibile passarli al modulo di campionamento?*

 * Inizializzare un'istanza separata di TelemetryClient con una nuova TelemetryConfiguration (non con quello predefinito attivo). Usarla per inviare gli eventi rari.

<!---HONumber=AcomDC_0817_2016-->