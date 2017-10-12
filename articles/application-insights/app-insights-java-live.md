---
title: "Application Insights per le applicazioni web Java che sono già live"
description: "Avviare il monitoraggio di un'applicazione web che è già in esecuzione sul server"
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 12f3dbb9-915f-4087-87c9-807286030b0b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/10/2016
ms.author: bwren
ms.openlocfilehash: a2731e3e44f8f3d104d8abc7dbe71fe3a4c3a690
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>Application Insights per le applicazioni web Java che sono già live


Se è disponibile un'applicazione Web già in esecuzione nel server J2EE, è possibile avviare il monitoraggio con [Application Insights](app-insights-overview.md) senza dover apportare modifiche al codice o ricompilare il progetto. Con questa opzione è possibile ottenere informazioni sulle richieste HTTP inviate al server, le eccezioni non gestite e i contatori delle prestazioni.

È necessaria una sottoscrizione di [Microsoft Azure](https://azure.com).

> [!NOTE]
> La procedura in questa pagina consente di aggiungere il SDK all'applicazione web in fase di esecuzione. La strumentazione del runtime è utile se non si vuole aggiornare o ricompilare il codice sorgente. Ma se possibile, è consigliabile [aggiungere l’SDK al codice sorgente](app-insights-java-get-started.md) invece. Che offre ulteriori opzioni, ad esempio la scrittura di codice per tenere traccia delle attività dell'utente.
> 
> 

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Ottenere una chiave di strumentazione di Application Insights
1. Accedere al [portale di Microsoft Azure](https://portal.azure.com)
2. Creare una nuova risorsa di Application Insights e configurare il tipo di applicazione su applicazione Web Java.
   
    ![Inserire un nome, scegliere l'app Web Java e fare clic su Crea](./media/app-insights-java-live/02-create.png)

    La risorsa viene creata in pochi secondi.

4. Aprire la nuova risorsa e ottenere la rispettiva chiave di strumentazione. Questa chiave dovrà a breve essere incollata nel progetto di codice.
   
    ![Nella panoramica della nuova risorsa, fare clic su Proprietà e copiare la chiave di strumentazione](./media/app-insights-java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2. Scaricare l'SDK
1. Scaricare [Application Insights SDK per Java](https://aka.ms/aijavasdk). 
2. Sul server, estrarre i contenuti SDK nella directory da cui vengono caricati i file binari del progetto. Se si usa Tomcat, la directory si trova in genere in`webapps/<your_app_name>/WEB-INF/lib`

Si noti che è necessario ripetere questo passaggio per ogni istanza del server e per ogni app.

## <a name="3-add-an-application-insights-xml-file"></a>3. Aggiungere un file XML di Application Insights
Creare ApplicationInsights.xml nella cartella in cui è stato aggiunto il SDK. Copiarvi il seguente file XML.

Sostituire la chiave di strumentazione recuperata dal portale di Azure.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>
```

* La chiave di strumentazione viene inviata insieme a tutti gli elementi di dati di telemetria e indica ad Application Insights di visualizzarla nella risorsa.
* Il componente delle richieste HTTP è facoltativo. Invia automaticamente i dati di telemetria sulle richieste e tempi di risposta al portale.
* La correlazione di eventi è un'aggiunta al componente delle richieste HTTP. Assegna un identificatore a ogni richiesta ricevuta dal server e lo aggiunge come proprietà a ogni elemento di telemetria, come proprietà "Operation.Id". Consente di correlare i dati di telemetria associati a ogni richiesta impostando un filtro in [Ricerca diagnostica](app-insights-diagnostic-search.md).

## <a name="4-add-an-http-filter"></a>4. Aggiungere un filtro HTTP
Individuare e aprire il file web.xml nel progetto e unire il frammento di codice seguente al di sotto del nodo app-web, in cui sono configurati i filtri dell'applicazione.

Per ottenere risultati più accurati, il filtro deve essere mappato prima di tutti gli altri filtri.

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>
```

## <a name="5-check-firewall-exceptions"></a>5. Verificare le eccezioni del firewall
Potrebbe essere necessario [impostare le eccezioni per l'invio dei dati in uscita](app-insights-ip-addresses.md).

## <a name="6-restart-your-web-app"></a>6. Riavviare la propria app web.
## <a name="7-view-your-telemetry-in-application-insights"></a>7. Visualizzare i dati di telemetria in Application Insights
Tornare alla risorsa di Application Insights nel [portale di Microsoft Azure](https://portal.azure.com).

I dati di telemetria delle richieste HTTP vengono visualizzati nel pannello Panoramica. Se non sono visualizzati, attendere alcuni secondi e quindi fare clic su Aggiorna.

![dati di esempio](./media/app-insights-java-live/5-results.png)

Fare clic su qualsiasi grafico per visualizzare metriche più dettagliate. 

![](./media/app-insights-java-live/6-barchart.png)

E quando si visualizzano le proprietà di una richiesta, è possibile visualizzare gli eventi di telemetria associati, ad esempio le richieste e le eccezioni.

![](./media/app-insights-java-live/7-instance.png)

[Altre informazioni sulle metriche.](app-insights-metrics-explorer.md)

## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere la telemetria alle pagine Web](app-insights-javascript.md) per monitorare le visualizzazioni pagina e le metriche utente.
* [Configurare i test Web](app-insights-monitor-web-app-availability.md) in modo da assicurarsi che l'applicazione sia disponibile e reattiva.
* [Acquisire le tracce dei log](app-insights-java-trace-logs.md)
* [Cercare eventi e log](app-insights-diagnostic-search.md) per facilitare la diagnosi dei problemi.

