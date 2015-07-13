<properties 
	pageTitle="Esplorare i log di traccia .NET in Application Insights" 
	description="Cercare i log generati con Trace, NLog o Log4Net." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/27/2015" 
	ms.author="awills"/>
 
# Esplorare i log di traccia .NET in Application Insights  

Se si usa NLog, log4Net o System.Diagnostics.Trace per l'analisi diagnostica nell'applicazione ASP.NET, è possibile fare in modo che i log vengano inviati a [Visual Studio Application Insights][start], dove è possibile esplorarli ed eseguirvi ricerche. I log verranno uniti con gli altri eventi di telemetria provenienti dall'applicazione, in modo da potere identificare le tracce associate alla gestione di ogni richiesta dell'utente e metterle in correlazione con altri eventi e i report di eccezioni.

È anche possibile scrivere tracce di log e report di eventi ed eccezioni con Application Insights SDK.

Se non si è ancora [configurato Application Insights per il progetto][start], è possibile farlo ora.


##  Installare un adattatore per il framework di registrazione

Se si usa un framework di registrazione, ad esempio log4Net, NLog o System.Diagnostics.Trace, è possibile installare un adattatore che invia questi log ad Application Insights insieme ad altri eventi di telemetria.

1. Se si intende usare log4Net o NLog, installarlo nel progetto. 
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.
3. Selezionare Online > Tutti, quindi selezionare **Includi versione preliminare** e cercare "Microsoft.ApplicationInsights".

    ![Ottenere la versione preliminare dell'adattatore appropriato](./media/app-insights-asp-net-trace-logs/appinsights-36nuget.png)

4. Selezionare il pacchetto appropriato tra:
  + Microsoft.ApplicationInsights.TraceListener (per acquisire le chiamate System.Diagnostics.Trace)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

Il pacchetto NuGet installa gli assembly necessari e modifica inoltre web.config o app.config.

#### Inserire chiamate di log di diagnostica

Se si usa System.Diagnostics.Trace, una tipica chiamata sarà simile alla seguente:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se si preferisce log4net o NLog:

    logger.Warn("Slow response - database01");


## Uso diretto dell'API di traccia

È possibile chiamare direttamente l'API di traccia di Application Insights. Gli adattatori di registrazione usano questa API.

Ad esempio:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");




## Esplorare i log

Nel pannello Panoramica dell'app nel [portale di Application Insights][portal] scegliere [Cerca][diagnostic].

![In Application Insights scegliere Cerca](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![Ricerca diagnostica](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Ad esempio, è possibile:

* Filtrare in base alle tracce dei log o agli elementi con proprietà specifiche
* Esaminare un elemento specifico in modo dettagliato
* Trovare altri eventi di telemetria relativi alla stessa richiesta dell'utente (ovvero, con lo stesso valore OperationId) 
* Salvare la configurazione di questa pagina come preferita


## Passaggi successivi

[Diagnosticare errori ed eccezioni in ASP.NET][exceptions]

[Altre informazioni sulla ricerca diagnostica][diagnostic].



## Risoluzione dei problemi

### <a name="emptykey"></a>Viene visualizzato l'errore: "La chiave di strumentazione non può essere vuota"

Risulta che l'utente abbia installato il pacchetto Nuget dell'adattatore di registrazione senza aver installato Application Insights.

In Esplora soluzioni fare clic con il pulsante destro del mouse su `ApplicationInsights.config` e scegliere **Aggiorna Application Insights**. Verrà visualizzata una finestra di dialogo che invita ad accedere ad Azure e a creare una risorsa Application Insights o a riusarne una esistente. Il problema verrà in tal modo risolto.

### Nella ricerca diagnostica vengono visualizzate le tracce ma non gli altri eventi

Talvolta la visualizzazione di tutti gli eventi e le richieste nella pipeline può richiedere un po' di tempo.

### <a name="limits"></a>Quanti dati vengono conservati?

Fino a 500 eventi al secondo da ciascuna applicazione. Gli eventi vengono conservati per sette giorni.

## <a name="add"></a>Passaggi successivi

* [Configurare i test di disponibilità e velocità di risposta][availability]
* [Risoluzione dei problemi][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-get-started.md

 

<!---HONumber=62-->