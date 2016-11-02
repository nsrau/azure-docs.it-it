<properties 
	pageTitle="Sfruttare al meglio le funzionalità di Application Insights | Microsoft Azure" 
	description="Dopo l'introduzione a Application Insights, ecco un riepilogo delle funzionalità che è possibile esplorare." 
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
	ms.date="08/30/2016" 
	ms.author="awills"/>

# Altri dati di telemetria da Application Insights

Dopo aver [aggiunto Application Insights al codice ASP.NET](app-insights-asp-net.md), è possibile eseguire alcune operazioni per ottenere altri dati di telemetria.

## App in esecuzione nel server IIS

Se l'app è ospitata in server IIS sotto il proprio controllo, installare Application Insights Status Monitor nei server. Se è già installato, non è necessario eseguire alcuna operazione.

1. In ogni server Web IIS accedere con le credenziali di amministratore.
2. Scaricare e installare il [programma di installazione di Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).
3. Nell'Installazione guidata accedere a Microsoft Azure.

Non è necessario eseguire altre operazioni, ma è possibile verificare che per l'app sia abilitato il monitoraggio.

![Estensione in Azure](./media/app-insights-asp-net-more/025.png)

È possibile usare Status Monitor per [abilitare il monitoraggio in fase di esecuzione](app-insights-monitor-performance-live-website-now.md) anche se le app non sono state instrumentate in Visual Studio.

### Vantaggi

Se nei computer server è installato Status Monitor, si ottengono alcuni dati di telemetria aggiuntivi:

* Telemetria delle dipendenze (chiamate SQL e chiamate REST effettuate dall'app) per le app .NET 4.5. Per le versioni successive di .NET, per la telemetria delle dipendenze non è necessario Status Monitor.
* Analisi dello stack delle eccezioni più dettagliate.
* Contatori delle prestazioni. In Application Insights, tali contatori vengono visualizzati nel pannello Server.

![Estensione in Azure](./media/app-insights-asp-net-more/070.png)

Per aumentare o ridurre il numero di contatori visualizzati, [modificare i grafici](app-insights-metrics-explorer.md). Se il contatore delle prestazioni desiderato non è incluso nel set disponibile, è possibile [aggiungerlo al set raccolto dal modulo dei contatori delle prestazioni](app-insights-web-monitor-performance.md#system-performance-counters).

## App Web di Azure

Se l'app viene eseguita come app Web di Azure, passare al pannello di controllo di Azure per l'app o la VM e aggiungere l'estensione Application Insights. In **Strumenti** aprire **Monitoraggio delle prestazioni** e configurare **Application Insights**. Quando viene richiesto, scegliere la risorsa di Application Insights già creata.

![Estensione in Azure](./media/app-insights-asp-net-more/05-extend.png)

### Vantaggi

* Analisi dello stack delle eccezioni più dettagliate.
* Telemetria delle dipendenze (chiamate SQL e chiamate REST effettuate dall'app) per le app .NET 4.5. Per le versioni successive di .NET, per la telemetria delle dipendenze non è necessaria l'estensione.

![Estensione in Azure](./media/app-insights-asp-net-more/080.png)

È possibile usare questo metodo per [abilitare il monitoraggio delle prestazioni in fase di esecuzione](app-insights-monitor-performance-live-website-now.md) anche se le app non sono state instrumentate in Visual Studio.

## Monitoraggio sul lato client

È stato installato l'SDK che invia i dati di telemetria dal lato server (back-end) dell'applicazione. Ora è possibile aggiungere il monitoraggio sul lato client, che fornisce dati su utenti, sessioni, visualizzazioni di pagina ed eventuali eccezioni o arresti anomali verificatisi nel browser. Sarà anche possibile scrivere codice personalizzato per tenere traccia del modi in cui gli utenti interagiscono con l'app, a un livello di dettaglio che include i clic e le sequenze di tasto.

Per ottenere dati di telemetria dai browser client, aggiungere il frammento JavaScript di Application Insights a ogni pagina Web.

1. In Azure aprire la risorsa di Application Insights per l'app.
2. Aprire Avvio rapido e quindi il monitoraggio dell'applicazione lato client e copiare il frammento.
3. Incollarlo in modo che risulti incluso nell'intestazione di ogni pagina Web. In genere si ottiene questo risultato incollando il frammento nella pagina di layout master.

![Estensione in Azure](./media/app-insights-asp-net-more/100.png)

Si noti che il codice contiene la chiave di strumentazione che identifica la risorsa dell'applicazione.

### Vantaggi

* Possibilità di scrivere codice JavaScript per inviare [dati di telemetria personalizzati dalle pagine Web](app-insights-api-custom-events-metrics.md), ad esempio per tenere traccia dei clic sui pulsanti.
* In [Analisi](app-insights-analytics.md), dati in `pageViews` e dati AJAX in `dependencies`.
* [Dati sull'utilizzo e sulle prestazioni dei client](app-insights-javascript.md) nel pannello Browser.

![Estensione in Azure](./media/app-insights-asp-net-more/090.png)


[Altre informazioni sul rilevamento delle pagine Web.](app-insights-web-track-usage.md)



## Rilevare la versione dell’applicazione

Assicurarsi che `buildinfo.config` sia generato dal processo di MSBuild. Nel file con estensione csproj, aggiungere:

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

Quando sono disponibili le informazioni di compilazione, il modulo Web di Application Insights aggiunge automaticamente la **versione dell'applicazione** come proprietà a ogni elemento di telemetria. Questo consente di filtrare in base alla versione quando si eseguono [ricerche diagnostiche](app-insights-diagnostic-search.md) o quando si [esplorano le metriche](app-insights-metrics-explorer.md).

Si noti tuttavia che il numero di versione di build viene generato solo da Build MS, non dallo sviluppatore di build in Visual Studio.


## Test Web di disponibilità

Inviare richieste HTTP dell'app Web a intervalli regolari da tutto il mondo. Se la risposta è lenta o non affidabile, l'utente riceve un avviso.

Nella risorsa di Application Insights per l'app fare clic sul riquadro Disponibilità per aggiungere, modificare e visualizzare test Web.

È possibile aggiungere più test eseguiti in più località.

![Estensione in Azure](./media/app-insights-asp-net-more/110.png)

[Altre informazioni](app-insights-monitor-web-app-availability.md)

## Telemetria personalizzata e registrazione

I pacchetti di Application Insights aggiunti al codice forniscono un'API che può essere chiamata dall'applicazione.

* [Generare metriche ed eventi personalizzati](app-insights-api-custom-events-metrics.md), ad esempio per conteggiare eventi aziendali o monitorare le prestazioni.
* [Acquisire le tracce di log](app-insights-asp-net-trace-logs.md) da Log4Net, NLog o System.Diagnostics.Trace.
* [Filtrare, modificare o aumentare](app-insights-api-filtering-sampling.md) i dati di telemetria standard inviati dall'app scrivendo processori di telemetria.


## Analisi e presentazione efficienti

È possibile esaminare i dati in numerosi modi. Se si è iniziato da poco a usare Application Insights, vedere gli articoli seguenti:

||
|---|---
|[**Uso di Application Insights in Visual Studio**](app-insights-visual-studio.md)<br/>Cercare e filtrare eventi come richieste, eccezioni, chiamate a dipendenze, tracce di log e visualizzazioni di pagina. In Visual Studio, passare al codice dall'analisi dello stack.|![Visual Studio](./media/app-insights-asp-net/61.png)
|[**Esaminare le metriche in Application Insights**](app-insights-metrics-explorer.md)<br/>Esaminare, filtrare e segmentare dati aggregati come frequenza delle richieste, errori, eccezioni, tempi di risposta e tempi di caricamento delle pagine.|![Visual Studio](./media/app-insights-asp-net-more/060.png)
|[**Dashboard**](app-insights-dashboards.md#dashboards)<br/>Combinare dati di più risorse e condividerli con altri utenti. Ideale per le applicazioni multi-componente e per la visualizzazione continua negli spazi del team. |![Esempio di dashboard](./media/app-insights-asp-net/62.png)
|[**Flusso metriche attive**](app-insights-metrics-explorer.md#live-metrics-stream)<br/>Quando si distribuisce una nuova build, controllare questi indicatori delle prestazioni in tempo quasi reale per verificare che tutto funzioni come previsto.|![Esempio di analisi](./media/app-insights-asp-net-more/050.png)
|[**Analisi in Application Insights**](app-insights-analytics.md)<br/>Questo avanzato linguaggio di query consente di trovare risposta a domande approfondite sull'utilizzo e sulle prestazioni dell'app.|![Esempio di analisi](./media/app-insights-asp-net-more/010.png)
|[**Impostare gli avvisi in Application Insights**](app-insights-alerts.md)<br/>Gli avvisi automatici si adattano ai modelli normali di telemetria dell'app e si attivano quando i dati si discostano dal modello consueto. È anche possibile impostare avvisi su livelli particolari delle metriche standard o personalizzate.|![Esempio di avviso](./media/app-insights-asp-net-more/020.png)

## Gestione dati

|||
|---|---|
|[**Esportare i dati di telemetria da Application Insights**](app-insights-export-telemetry.md)<br/>Copiare tutti i dati di telemetria in una risorsa di archiviazione per poterli analizzare nel modo preferito.|
|**API di accesso ai dati**<br/>Presto disponibile.|
|[**Campionamento in Application Insights**](app-insights-sampling.md)<br/>Riduce la frequenza di dati e consente di rimanere entro il limite del proprio piano tariffario.|![Riquadro del campionamento](./media/app-insights-asp-net-more/030.png)

<!---HONumber=AcomDC_0907_2016-->