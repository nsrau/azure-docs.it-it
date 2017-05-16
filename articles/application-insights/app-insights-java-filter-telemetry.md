---
title: Filtrare i dati di telemetria di Azure Application Insights nell&quot;App Web Java | Microsoft Docs
description: "Ridurre il traffico di telemetria escludendo gli eventi che non è necessario monitorare."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 9a3df0ad2483471023ebb954d613bc5cad8fb7bf
ms.openlocfilehash: cd09b7c5d45d07a3fbcc5d6f0c02400dcd36d61b
ms.contentlocale: it-it
ms.lasthandoff: 02/02/2017


---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtrare i dati di telemetria nell'App Web Java

I filtri consentono di selezionare i dati di telemetria [inviati dall'App Web Java ad Application Insights](app-insights-java-get-started.md). Esistono alcuni filtri pronti da usare che è possibile applicare, inoltre è possibile creare filtri personalizzati.

I filtri pronti da usare includono:

* Livello di gravità della traccia
* URL, parole chiave o codici di risposta specifici
* Risposte rapide, vale a dire richieste a cui l'app ha risposto rapidamente
* Nomi degli eventi specifici

> [!NOTE]
> I filtri alterano le metriche dell'app. Ad esempio, si potrebbe decidere che, per diagnosticare un problema di risposte lente, verrà impostato un filtro per ignorare i tempi di risposta rapidi. È necessario però tenere presente che i tempi medi di risposta segnalati da Application Insights saranno così più lenti rispetto alla velocità effettiva e che il numero di richieste sarà inferiore rispetto al numero reale.
> Se questo rappresenta un problema, usare il [campionamento](app-insights-sampling.md).

## <a name="setting-filters"></a>Impostazione di filtri

In ApplicationInsights.xml aggiungere una sezione `TelemetryProcessors` come in questo esempio:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```




[Esaminare l'insieme completo dei processori incorporati](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor).

## <a name="built-in-filters"></a>Filtri incorporati

### <a name="metric-telemetry-filter"></a>Filtro Dati di telemetria metrica

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded`: elenco delimitato da virgole con i nomi delle metriche personalizzate.


### <a name="page-view-telemetry-filter"></a>Filtro Dati di telemetria visualizzazione di pagina

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS`: la durata si riferisce al tempo impiegato per caricare la pagina. Se è impostato, le pagine caricate più velocemente del tempo definito non vengono segnalate.
* `NotNeededNames`: elenco delimitato da virgole con i nomi delle pagine.
* `NotNeededUrls`: elenco delimitato da virgole con i frammenti di URL. Ad esempio, `"home"` esclude tutte le pagine il cui URL include il termine "home".


### <a name="request-telemetry-filter"></a>Filtro Dati di telemetria richiesta


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Filtro Origine sintetica

Esclude tutti i dati di telemetria che dispongono di valori nella proprietà SyntheticSource. Questi dati includono le richieste da bot, spider e test di disponibilità.

Escludere i dati di telemetria per tutte le richieste sintetiche:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Escludere i dati di telemetria per le origini sintetiche specifiche:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded`: elenco delimitato da virgole con i nomi delle origini sintetiche.

### <a name="telemetry-event-filter"></a>Filtro Eventi di telemetria

Esclude gli eventi personalizzati (registrati tramite [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames`: elenco delimitato da virgole con i nomi degli eventi.


### <a name="trace-telemetry-filter"></a>Filtro Dati di telemetria traccia

Esclude le tracce di log (registrate tramite [TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) o un [agente di raccolta del framework di registrazione](app-insights-java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* I valori `FromSeverityLevel` validi sono:
 *  OFF             - Esclude TUTTE le tracce
 *  TRACE           - Non applica alcun filtro. Corrisponde al livello Trace
 *  INFO            - Esclude il livello TRACE
 *  WARN            - Esclude TRACE e INFO
 *  ERROR           - Esclude WARN, INFO, TRACE
 *  CRITICAL        - Esclude tutto tranne CRITICAL


## <a name="custom-filters"></a>Filtri personalizzati

### <a name="1-code-your-filter"></a>1. Codificare il filtro

Nel codice creare una classe che implementa `TelemetryProcessor`:

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful)
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. Richiamare il filtro nel file di configurazione

In ApplicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

## <a name="troubleshooting"></a>Risoluzione dei problemi

*Il filtro non funziona.*

* Verificare che siano stati specificati valori di parametro validi. Ad esempio, la durata deve essere espressa da numeri interi. Se i valori non sono validi, il filtro verrà ignorato. Se il filtro personalizzato genera un'eccezione da un costruttore o un metodo impostato, verrà ignorato.

## <a name="next-steps"></a>Passaggi successivi

* [Campionamento](app-insights-sampling.md): prendere in considerazione il campionamento come un'alternativa che non altera le metriche.

