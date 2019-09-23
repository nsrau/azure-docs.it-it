---
title: Campionamento della telemetria in Azure Application Insights | Documentazione Microsoft
description: Come tenere sotto controllo il volume della telemetria.
services: application-insights
documentationcenter: windows
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.reviewer: vitalyg
ms.author: cithomas
ms.openlocfilehash: d43fe7f1f0fc63ab50821a345802a9e7e62881b2
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169485"
---
# <a name="sampling-in-application-insights"></a>Campionamento in Application Insights

Il campionamento è una funzionalità di [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). È l'approccio consigliato per ridurre il traffico e l'archiviazione della telemetria mantenendo però un'analisi statisticamente corretta dei dati dell'applicazione. Il filtro seleziona gli elementi correlati per poter passare dall'uno all'altro nel corso delle indagini diagnostiche.
Quando vengono presentati i conteggi delle metriche nel portale, vengono rinormalizzati per tenere conto del campionamento. Questa operazione riduce al minimo gli effetti sulle statistiche.

Il campionamento riduce i costi del traffico e dei dati e consente di evitare la limitazione.

## <a name="in-brief"></a>In breve:

* Il campionamento mantiene un record su *n* e rimuove il resto. Ad esempio, potrebbe conservarne uno in cinque eventi, una frequenza di campionamento del 20%. 
* Il campionamento adattivo è abilitato per impostazione predefinita in tutta la versione più recente di ASP.NET e ASP.NET Core Software Development Kit (SDK).
* È anche possibile impostare il campionamento manualmente. Questa configurazione può essere configurata nel portale nella *pagina utilizzo e costi stimati*, in ASP.NET SDK nel file ApplicationInsights. config, in ASP.NET Core SDK tramite codice o in Java SDK nel file ApplicationInsights. XML.
* Se si registrano eventi personalizzati ed è necessario assicurarsi che un set di eventi venga mantenuto o rimosso insieme, gli eventi devono avere lo stesso valore OperationId.
* Il divisore di campionamento *n* è indicato in ogni record nella proprietà `itemCount`, visualizzata nella ricerca con il nome descrittivo "Conteggio delle richieste" o "Conteggio degli eventi". `itemCount==1`Quando il campionamento non è in esecuzione.
* Se si scrivono query di Dati di analisi, è necessario [tener conto del campionamento](../../azure-monitor/log-query/aggregations.md). In particolare, anziché eseguire semplicemente il conteggio dei record, è necessario usare `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Tipi di campionamento

Esistono tre diversi metodi di campionamento:

* Il **campionamento adattivo** regola automaticamente il volume dei dati di telemetria inviati dall'SDK nell'app ASP.NET/ASP.NET Core. Questo è il campionamento predefinito da ASP.NET Web SDK versione 2.0.0-beta3 e versioni successive e Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0-beta1.  Il campionamento adattativo è attualmente disponibile solo per la telemetria lato server di ASP.NET.

* Il **campionamento a frequenza fissa** riduce il volume dei dati di telemetria inviati dal server ASP.NET o ASP.NET Core o Java e dai browser degli utenti. È necessario impostare la frequenza. Il client e il server sincronizzeranno il rispettivo campionamento in modo che nella ricerca sia possibile spostarsi tra le visualizzazioni pagina e le richieste correlate.

* **Campionamento** inserimento Funziona nel portale di Azure. Rimuove alcuni dati di telemetria provenienti dall'app, a una velocità di campionamento impostata. Non riduce il traffico di telemetria inviato dall'app, ma consente all'utente di rispettare la quota mensile. Il vantaggio principale del campionamento per inserimento è che è possibile impostare la frequenza di campionamento senza ridistribuire l'app. Il campionamento per inserimento funziona in modo uniforme per tutti i server e i client.

Se è in esecuzione il campionamento adattivo o a frequenza fissa, il campionamento per inserimento è disabilitato.


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>Campionamento adattivo nelle applicazioni Web ASP.NET/ASP.NET Core

Il campionamento adattivo è disponibile per Application Insights SDK per ASP.NET versione 2.0.0-beta3 e versioni successive, Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0-beta1 e versioni successive ed è abilitato per impostazione predefinita.

Il campionamento adattivo riguarda il volume dei dati di telemetria inviati dall'app del server Web all'endpoint del servizio Application Insights. Il volume viene regolato automaticamente per rimanere entro una frequenza massima specificata di traffico e viene controllato tramite l'impostazione `MaxTelemetryItemsPerSecond`. Se l'applicazione produce una quantità ridotta di dati di telemetria, ad esempio durante il debug o a causa di un utilizzo ridotto, gli elementi non verranno eliminati dal processore di `MaxTelemetryItemsPerSecond`campionamento purché il volume sia inferiore. Man mano che aumenta il volume dei dati di telemetria, la frequenza di campionamento viene regolata in modo da ottenere il volume di destinazione.

Per ottenere il volume di destinazione, alcuni dei dati di telemetria generati vengono eliminati. Tuttavia, come in altri tipi di campionamento, l'algoritmo consente di mantenere gli elementi di telemetria correlati. Ad esempio, quando si controllano i dati di telemetria nella ricerca, sarà possibile trovare la richiesta correlata a una particolare eccezione.

I conteggi di metrica, ad esempio la frequenza delle richieste e delle eccezioni, vengono adattati per compensare la frequenza di campionamento, in modo che mostrino i valori corretti in Esplora metriche.

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Configurazione del campionamento adattivo per le applicazioni ASP.NET

[Informazioni sulla configurazione](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) del campionamento adattivo per applicazioni ASP.NET Core. 

In [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) è possibile regolare diversi parametri nel nodo `AdaptiveSamplingTelemetryProcessor`. Le cifre indicate sono i valori predefiniti:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Frequenza di destinazione che l'algoritmo adattivo deve raggiungere **su ogni host server**. Se l'app Web viene eseguita su più host, ridurre questo valore per non superare la frequenza di destinazione del traffico nel portale di Application Insights.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Intervallo in corrispondenza del quale viene rivalutata la frequenza corrente dei dati di telemetria. La valutazione viene eseguita come media mobile. Potrebbe essere necessario ridurre questo intervallo se la telemetria è responsabile di burst improvvisi.
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
  
    Valore assegnato quando l'app è appena stata avviata. Non ridurre il valore durante il debug.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Elenco dei tipi da non campionare delimitato dal punto e virgola. I tipi riconosciuti sono: Dependency, Event, Exception, PageView, Request, Trace. Tutte le istanze dei tipi specificati vengono trasmesse; i tipi non specificati vengono campionati.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Elenco dei tipi da campionare delimitato dal punto e virgola. I tipi riconosciuti sono: Dependency, Event, Exception, PageView, Request, Trace. I tipi specificati vengono campionati; tutte le istanze degli altri tipi vengono sempre trasmesse.


**Per** disattivare il campionamento adattivo, rimuovere il nodo o i nodi AdaptiveSamplingTelemetryProcessor da applicationinsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativa: configurare il campionamento adattivo nel codice

Invece di impostare il parametro di campionamento nel file con estensione config è possibile impostare questi valori a livello di codice.

1. Rimuovere tutti i `AdaptiveSamplingTelemetryProcessor` nodi dal file config.
2. Usare il frammento di codice seguente per configurare il campionamento adattivo.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Informazioni sui processori di telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

È anche possibile regolare la frequenza di campionamento per ogni tipo di telemetria singolarmente oppure escludere determinati tipi dall'esempio. 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Configurazione del campionamento adattivo per applicazioni ASP.NET Core.

Non esiste alcuna `ApplicationInsights.Config` per ASP.NET Core applicazioni, quindi ogni configurazione viene eseguita tramite codice.
Il campionamento adattivo è abilitato per impostazione predefinita in tutte le applicazioni ASP.NET Core, ma può essere disabilitato o è possibile personalizzarne il comportamento.

### <a name="turning-off-adaptive-sampling"></a>Disabilitazione del campionamento adattivo

La funzionalità di campionamento predefinita può essere disabilitata durante l'aggiunta Application Insights servizio ```ConfigureServices```nel metodo ```ApplicationInsightsServiceOptions``` usando all' `Startup.cs` interno del file:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
    //...
}
```

Il codice sopra riportato disabilita la funzionalità di campionamento. Seguire la procedura descritta di seguito per aggiungere al campionamento più opzioni di personalizzazione.

### <a name="configure-sampling-settings"></a>Configurare le impostazioni del campionamento

Usare i metodi di estensione di ```TelemetryProcessorChainBuilder```, come illustrato di seguito, per personalizzare il comportamento della funzionalità di campionamento.

> [!IMPORTANT]
> Se si usa questo metodo per configurare il campionamento, assicurarsi di usare le impostazioni aiOptions.EnableAdaptiveSampling = false; con AddApplicationInsightsTelemetry().

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.TelemetryProcessorChainBuilder;
    // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
    // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    builder.Build();

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    // ...
}

```

**Se si usa il metodo precedente per configurare il campionamento, assicurarsi ```aiOptions.EnableAdaptiveSampling = false;``` di usare le impostazioni con AddApplicationInsightsTelemetry ().**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-and-java-websites"></a>Campionamento a frequenza fissa per siti Web ASP.NET, ASP.NET Core e Java

Il campionamento a frequenza fissa riduce il traffico inviato dal server e dai Web browser. A differenza del campionamento adattivo, riduce i dati di telemetria a una frequenza fissa definita dall'utente. Sincronizza inoltre il campionamento del client e del server in modo che gli elementi correlati vengano mantenuti, ad esempio quando si esamina una pagina di ricerca, è possibile trovare la richiesta correlata.

Analogamente ad altre tecniche di campionamento, questo mantiene anche gli elementi correlati. Per ciascun evento di richiesta HTTP, la richiesta e gli eventi correlati vengono eliminati o trasmessi congiuntamente.

In Esplora metriche, frequenze quali il numero di richieste ed eccezioni vengono moltiplicate per un fattore in modo da compensare la frequenza di campionamento ed essere quindi corretti.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Configurazione del campionamento a frequenza fissa in ASP.NET

1. **Disabilitare il campionamento adattivo**: in [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) rimuovere o impostare come commento il nodo `AdaptiveSamplingTelemetryProcessor`.

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **Abilitare il modulo di campionamento a frequenza fissa.** Aggiungere questo frammento ad [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternativa: abilitare il campionamento a frequenza fissa nel codice del server locale
    
    Invece di impostare il parametro di campionamento nel file con estensione config è possibile impostare questi valori a livello di codice. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```
([Informazioni sui processori di telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>Configurazione del campionamento a frequenza fissa in ASP.NET Core

1. **Disabilitare il campionamento adattivo**:  È possibile apportare modifiche nel metodo ```ConfigureServices```usando: ```ApplicationInsightsServiceOptions```

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    //...
    }
    ```

2. **Abilitare il modulo di campionamento a frequenza fissa.** Le modifiche possono essere apportate ```Configure``` nel metodo, come illustrato nel frammento di codice seguente:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.TelemetryProcessorChainBuilder;
        // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
        // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }

    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>Configurazione del campionamento a frequenza fissa in JAVA ###

1. Scaricare e configurare l'applicazione Web con l'[SDK per Java di Application Insights](../../azure-monitor/app/java-get-started.md) più recente

2. **Abilitare il modulo di campionamento a frequenza fissa** aggiungendo il frammento di codice seguente al file ApplicationInsights.xml.

    ```XML
        <TelemetryProcessors>
            <BuiltInProcessors>
                <Processor type = "FixedRateSamplingTelemetryProcessor">
                    <!-- Set a percentage close to 100/N where N is an integer. -->
                    <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                    <Add name = "SamplingPercentage" value = "50" />
                </Processor>
            </BuiltInProcessors>
        </TelemetryProcessors>
    ```

3. È possibile includere o escludere determinati tipi di dati di telemetria dal campionamento usando i tag seguenti all'interno del tag del processore "FixedRateSamplingTelemetryProcessor"
    ```XML
        <ExcludedTypes>
            <ExcludedType>Request</ExcludedType>
        </ExcludedTypes>

        <IncludedTypes>
            <IncludedType>Exception</IncludedType>
        </IncludedTypes>
    ```

I tipi di dati di telemetria che possono essere inclusi o esclusi dal campionamento sono: Dipendenza, evento, eccezione, visualizzazione pagina, richiesta e traccia.

> [!NOTE]
> Come percentuale di campionamento, sceglierne una vicina a 100/N dove N è un numero intero.  Il campionamento attualmente non supporta altri valori.
> 
> 

<a name="other-web-pages"></a>



## <a name="ingestion-sampling"></a>Campionamento per inserimento

Questa forma di campionamento opera nel punto in cui i dati di telemetria di server Web, browser e dispositivi raggiungono l'endpoint di servizio di Application Insights. Anche se non riduce il traffico dei dati di telemetria inviato dall'app, riduce la quantità di dati elaborati, conservati e addebitati da Application Insights.

Usare questo tipo di campionamento se l'app spesso supera la quota mensile e non si ha la possibilità di usare uno dei tipi di campionamento basati sull'SDK. 

Impostare la frequenza di campionamento nella pagina Utilizzo e costi stimati:

![Nel pannello Panoramica sull'applicazione fare clic su Impostazioni, Quota, Esempi e quindi selezionare una frequenza di campionamento e fare clic su Aggiorna.](./media/sampling/04.png)

Come in altri tipi di campionamento, l'algoritmo consente di mantenere gli elementi di telemetria correlati. Ad esempio, quando si controllano i dati di telemetria nella ricerca, sarà possibile trovare la richiesta correlata a una particolare eccezione. I conteggi di metrica, ad esempio la frequenza delle richieste e delle eccezioni, vengono mantenuti correttamente.

I punti dati che vengono rimossi dal campionamento non sono disponibili in alcuna funzionalità di Application Insights, ad esempio nell' [esportazione continua](../../azure-monitor/app/export-telemetry.md).

Il campionamento per inserimento non funziona mentre è attivo il campionamento a frequenza fissa o adattivo basato sull'SDK. Il campionamento adattivo è abilitato per impostazione predefinita quando ASP.NET/ASP.NET Core SDK è abilitato in Visual Studio o abilitato nelle estensioni delle app Web di Azure o tramite Status Monitor e il campionamento per inserimento è disabilitato. Se la frequenza di campionamento nell'SDK è inferiore al 100% (ovvero gli elementi vengono campionati) la frequenza di campionamento di inserimento impostata viene ignorata.

> [!WARNING]
> Il valore visualizzato nel riquadro indica il valore impostato per il campionamento per inserimento. Non rappresenta la frequenza di campionamento effettiva se il campionamento dell'SDK è in funzione.
>
>
## <a name="sampling-for-web-pages-with-javascript"></a>Campionamento per pagine Web con JavaScript
È possibile configurare le pagine Web per il campionamento a frequenza fissa da qualsiasi server. 

Quando si [configurano le pagine Web per Application Insights](../../azure-monitor/app/javascript.md), modificare il frammento JavaScript ottenuto dal portale di Application Insights. Nelle app ASP.NET il frammento viene in genere salvato in _Layout.cshtml.  Inserire una riga simile a `samplingPercentage: 10,` prima della chiave di strumentazione:

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

Come percentuale di campionamento, sceglierne una vicina a 100/N dove N è un numero intero.  Il campionamento attualmente non supporta altri valori.

Se si abilita il campionamento a frequenza fissa nel server, i client e il server si sincronizzeranno in modo che nella ricerca sia possibile spostarsi tra le visualizzazioni pagina e le richieste correlate.

## <a name="when-to-use-sampling"></a>Quando usare il campionamento?

Il campionamento adattivo viene abilitato automaticamente negli SDK più recenti di .NET e .NET Core. Indipendentemente dalla versione di SDK usata, è possibile abilitare il campionamento per inserimento in modo da consentire ad Application Insights di campionare i dati raccolti.

Per impostazione predefinita non è abilitato alcun campionamento nell'SDK per Java. Attualmente è supportato solo il campionamento a frequenza fissa. Il campionamento adattivo non è supportato nell'SDK di Java.

In linea generale, per la maggior parte delle applicazioni di piccole e medie dimensioni il campionamento non è necessario. Le informazioni di diagnostica più utili e le statistiche più accurate si ottengono raccogliendo dati su tutte le attività utente. 

I vantaggi principali del campionamento sono:

* Il servizio Application Insights rimuove ("limita") i punti dati quando l'app invia una frequenza di telemetria molto elevata in un breve intervallo di tempo. 
* Non superare la [quota](../../azure-monitor/app/pricing.md) di punti dati per il proprio piano tariffario. 
* Ridurre il traffico di rete dalla raccolta di telemetria. 

### <a name="which-type-of-sampling-should-i-use"></a>Quale tipo di campionamento è opportuno usare?

**Usare il campionamento per inserimento se:**

* Si supera spesso la quota mensile dei dati di telemetria.
* Si usa una versione dell'SDK che non supporta il campionamento, ad esempio le versioni di ASP.NET precedenti alla 2.
* Si stanno ottenendo troppi dati di telemetria dai Web browser degli utenti.

**Usare il campionamento a frequenza fissa se:**

* Si usa l'SDK per Application Insights per i servizi Web di ASP.NET versione 2.0.0 o successiva oppure l'SDK per Java v2.0.1 o versione successiva, e
* È necessario il campionamento sincronizzato tra client e server, in modo che, quando si esaminano gli eventi in [Cerca](../../azure-monitor/app/diagnostic-search.md), sia possibile spostarsi tra gli eventi correlati nel client e nel server, ad esempio visualizzazioni pagina e richieste http.
* Se è certi della percentuale di campionamento appropriata per l'app. Deve essere abbastanza elevata da ottenere metriche accurate, ma al di sotto della frequenza che fa superare la quota di prezzo e le soglie di limitazione. 

**Usare il campionamento adattivo:**

Se le condizioni per l'uso di altre forme di campionamento non sono valide per uno scenario specifico, è consigliabile adottare il campionamento adattativo. Questa impostazione è abilitata per impostazione predefinita in ASP.NET/ASP.NET Core Server SDK. Non riduce il traffico fino al raggiungimento di una frequenza minima specificata. Di conseguenza, i siti con bassi livelli di uso non saranno interessati.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Come è possibile sapere se il campionamento è in esecuzione?

Per individuare la frequenza di campionamento effettiva indipendentemente dal punto in cui è stata applicata, usare una [query di Analisi](../../azure-monitor/app/analytics.md) simile alla seguente:

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Se RetainedPercentage per qualsiasi tipo è minore di 100, viene campionato tale elemento.

**Application Insights non campiona i tipi di dati di telemetria relativi a sessione, metriche e contatori delle prestazioni in tutte le tecniche di campionamento descritte in precedenza. Questi tipi sono sempre esclusi dal campionamento poiché la riduzione della precisione può essere estremamente indesiderata per questi tipi di telemetria**

## <a name="how-does-sampling-work"></a>Come funziona il campionamento?

Funzionalità di campionamento a frequenza fissa dell'SDK per ASP.NET a partire dalla versione 2.0.0 e dell'SDK di Java a partire dalla versione 2.0.1 in poi. Il campionamento adattivo è una funzionalità dell'SDK nella versione ASP.NET 2.0.0 o successiva. Il campionamento per inserimento è una funzionalità del servizio Application Insights ed è operativo se l'SDK non sta eseguendo un altro campionamento.

L'algoritmo di campionamento decide quali elementi di telemetria eliminare e quali mantenere, sia che venga eseguito nell'SDK o nel servizio Application Insights. La decisione sul campionamento si basa su alcune regole che hanno lo scopo di lasciare intatti tutti i punti dati correlati, mantenendo in Application Insights un'esperienza di diagnostica sfruttabile e affidabile anche con un set di dati ridotto. Se, ad esempio, per una richiesta non riuscita l'app invia elementi di telemetria aggiuntivi (come eccezioni e tracce registrate da questa richiesta), il campionamento non dividerà la richiesta e il resto della telemetria, ma conserverà o rimuoverà gli elementi tutti insieme. Di conseguenza, quando si osservano i dettagli della richiesta in Application Insights, è sempre possibile visualizzare la richiesta con gli elementi di telemetria associati. 

La decisione di campionamento è basata sull'ID operazione della richiesta, il che significa che tutti gli elementi di telemetria appartenenti a una determinata operazione vengono conservati o eliminati. Per gli elementi di telemetria che non hanno set di ID operazione (ad esempio, elementi di telemetria segnalati da thread asincroni senza contesto http) il campionamento acquisisce semplicemente una percentuale di elementi di telemetria di ogni tipo. Prima di 2.5.0-beta2 di .NET SDK e 2.2.0-beta3 di ASP.NET Core SDK, la decisione di campionamento è basata sull'hash dell'ID utente per le applicazioni che definiscono "User", ovvero la maggior parte delle applicazioni Web tipiche. Per i tipi di applicazioni che non definiscono gli utenti, ad esempio i servizi Web, la decisione di campionamento è basata sull'ID operazione della richiesta.

Quando la telemetria viene ripresentata all'utente, il servizio Application Insights modifica le metriche in base alla stessa percentuale di campionamento usata in fase di raccolta, per compensare i punti dati mancanti. Quindi, quando osservano la telemetria in Application Insights, gli utenti visualizzano approssimazioni statisticamente corrette molto vicine ai numeri reali.

La precisione dell'approssimazione dipende in gran parte dalla percentuale di campionamento configurata. La precisione è anche maggiore per le applicazioni che gestiscono un volume elevato di richieste generalmente simili da una grande quantità di utenti. Per le applicazioni che non gestiscono un carico di lavoro significativo, invece, il campionamento non è necessario perché queste applicazioni in genere riescono a inviare tutti i dati di telemetria senza superare la quota e senza causare perdite di dati dovute alla limitazione. 

> [!WARNING]
> Application Insights non esegue il campionamento di metriche e tipi di dati di telemetria delle sessioni. La riduzione della precisione può risultare estremamente inappropriata per questi tipi di dati di telemetria.
> 

### <a name="adaptive-sampling"></a>Campionamento adattivo

Il campionamento adattivo aggiunge un componente che monitora la frequenza corrente di trasmissione dall'SDK e regola la percentuale di campionamento per cercare di rimanere entro la frequenza massima di destinazione. La rettifica viene ricalcolata a intervalli regolari e si basa su una media mobile della frequenza di trasmissione in uscita.

## <a name="sampling-and-the-javascript-sdk"></a>Campionamento e JavaScript SDK

L'SDK lato client (JavaScript) partecipa al campionamento a frequenza fissa insieme all'SDK lato server. Le pagine instrumentate invieranno solo la telemetria lato client dagli stessi utenti per cui il lato server ha preso la decisione di "eseguire il campionamento internamente". Questa logica è concepita per mantenere l'integrità della sessione utente sui lati client e server. Di conseguenza, da un particolare elemento della telemetria in Application Insights è possibile trovare tutti gli altri elementi della telemetria per questa sessione utente. 

*La telemetria lato e client e server non mostra i campioni coordinati, come descritto sopra.*

* Verificare di avere abilitato il campionamento a frequenza fissa sia sul server che sul client.
* Assicurarsi che la versione dell'SDK sia 2.0 o successiva.
* Controllare di avere impostato la stessa percentuale di campionamento sia nel client che nel server.

### <a name="sampling-in-azure-functions"></a>Campionamento in funzioni di Azure

Seguire le istruzioni di questo per configurare [il](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) campionamento per le app in esecuzione in funzioni di Azure.

## <a name="frequently-asked-questions"></a>Domande frequenti

*Qual è il comportamento di campionamento predefinito in ASP.NET e ASP.NET Core SDK?*

* Se si usa una delle versioni più recenti dell'SDK precedente, il campionamento adattivo è abilitato per impostazione predefinita con cinque elementi di telemetria al secondo.
  Sono stati aggiunti 2 AdaptiveSamplingTelemetryProcessors per impostazione predefinita e uno include il tipo di evento nel campionamento e l'altro esclude il tipo di evento dal campionamento. Questa configurazione significa che l'SDK tenterà di limitare gli elementi di telemetria a cinque elementi di telemetria dei tipi di evento e cinque elementi di telemetria di tutti gli altri tipi combinati, garantendo in tal modo che gli eventi vengano campionati separatamente dagli altri tipi di telemetria. Gli eventi vengono in genere usati per la telemetria aziendale e probabilmente non dovrebbero essere interessati dai volumi di telemetria di diagnostica.
  
  Di seguito viene illustrato il file ApplicationInsights. config predefinito generato. Come descritto, sono stati aggiunti due nodi AdaptiveSamplingTelemetryProcessor distinti, uno esclusi i tipi di evento e l'altro che lo include. In ASP.NET Core, nel codice è abilitato esattamente lo stesso comportamento predefinito. Usare gli esempi nella sezione precedente del documento per modificare questo comportamento predefinito.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*È possibile campionare i dati di telemetria più di una volta?*

* No. SamplingTelemetryProcessors ignora gli elementi dalle considerazioni di campionamento se l'elemento è già campionato. Lo stesso vale anche per il campionamento di inserimento, che non applica il campionamento a tali elementi già campionati nell'SDK stesso.

*Perché il campionamento non è una semplice "raccolta di percentuale X di ogni tipo di telemetria"?*

* Sebbene questo approccio di campionamento fornisca un livello elevato di precisione nelle approssimazioni delle metriche, potrebbe interrompere la possibilità di correlare i dati di diagnostica per utente, sessione e richiesta, che è fondamentale per la diagnostica. Il campionamento quindi funziona meglio come logica di "raccolta di tutti gli elementi della telemetria per una percentuale X di utenti dell'app" o di "raccolta di tutta la telemetria per una percentuale X di richieste app". Per gli elementi di telemetria non associati alle richieste, ad esempio l'elaborazione asincrona in background, il fallback consiste nel "raccogliere la percentuale X di tutti gli elementi per ogni tipo di telemetria". 

*La percentuale di campionamento può variare nel tempo?*

* Sì, il campionamento adattivo modifica gradualmente la percentuale di campionamento, in base al volume attualmente osservato della telemetria.

*Se si usa il campionamento a frequenza fissa, come stabilire quale sarà la percentuale di campionamento ideale per l'app?*

* Una modalità è quella di iniziare con il campionamento adattivo, scoprire quale frequenza è impostata (vedere la domanda precedente) e quindi cambiarla a campionamento a frequenza fissa usando quella frequenza. 
  
    In caso contrario, è necessario usare l'intuito. Analizzare l'uso della telemetria corrente in Application Insights, osservare le possibili limitazioni in corso e stimare il volume della telemetria raccolta. Questi tre input, insieme al piano tariffario selezionato, suggeriscono di quanto ridurre il volume dei dati di telemetria raccolti. Tuttavia, un aumento nel numero degli utenti o qualsiasi altra migrazione nel volume di telemetria potrebbe invalidare la stima.

*Cosa accade se si configura una percentuale di campionamento troppo bassa?*

* Una percentuale di campionamento troppo bassa (campionamento eccessivamente aggressivo) ridurrà la precisione delle approssimazioni, quando Application Insights tenterà di compensare la visualizzazione dei dati per la riduzione del volume dei dati. Anche l'esperienza di diagnostica potrebbe risultare compromessa, perché potrebbero venire campionate alcune richieste lente o non riuscite.

*Cosa accade se si configura una percentuale di campionamento troppo alta?*

* Configurando una percentuale di campionamento troppo elevata (non abbastanza aggressiva), si otterrà una riduzione insufficiente del volume dei dati di telemetria raccolti. Potrebbe tuttavia verificarsi una perdita dei dati di telemetria correlata alla limitazione e il costo per l'uso di Application Insights potrebbe essere superiore al previsto per l'applicazione di tariffe aggiuntive.

*Su quali piattaforme è possibile usare il campionamento?*

* Se nell'SDK non è impostato il campionamento, si verifica automaticamente il campionamento per inserimento per i dati di telemetria superiori a un determinato volume. Questa configurazione funziona, ad esempio, se si usa una versione precedente di ASP.NET SDK o una versione precedente di Java SDK (1.0.10 o precedente).
* Se si usano ASP.NET SDK versioni 2.0.0 e successive o ASP.NET CORE SDK versione 2.2.0 e successive (ospitate in Azure o nel proprio server), per impostazione predefinita si ottiene il campionamento adattivo, ma è possibile passare a una frequenza fissa come descritto in precedenza. Con il campionamento a frequenza fissa, l'SDK del browser si sincronizza automaticamente con gli eventi correlati al campione. 
* Se si usa Java SDK versione 2.0.1 o successiva, è possibile configurare ApplicationInsights. XML per attivare il campionamento a frequenza fissa. Il campionamento viene disattivato per impostazione predefinita. Con il campionamento a frequenza fissa, l'SDK del browser si sincronizza automaticamente con gli eventi correlati al campione.

*Esistono alcuni eventi rari che si vuole visualizzare sempre. Come è possibile passarli al modulo di campionamento?*

* Il modo migliore per ottenere questo risultato è scrivere un [personalizzata telemetryinitializer](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer)personalizzato, che imposta `SamplingPercentage` su 100 sull'elemento di telemetria che si vuole mantenere, come illustrato di seguito. Poiché gli inizializzatori sono garantiti per l'esecuzione prima dei processori di telemetria (incluso il campionamento), ciò garantisce che tutte le tecniche di campionamento ignoreranno questo elemento da eventuali considerazioni di campionamento.

```csharp
     public class MyTelemetryInitializer : ITelemetryInitializer
      {
         public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)item).SamplingPercentage = 100;
            }
        }
      }
```

## <a name="next-steps"></a>Passaggi successivi

* [applicazione di filtri](../../azure-monitor/app/api-filtering-sampling.md) può garantire un controllo più rigoroso sui dati inviati dall'SDK.
* Vedere l'articolo sulla rete [per sviluppatori ottimizzare la telemetria con Application Insights](https://msdn.microsoft.com/magazine/mt808502.aspx).
