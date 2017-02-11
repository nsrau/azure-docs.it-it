---
title: "Sfruttare al meglio le funzionalità di Application Insights | Microsoft Docs"
description: "Dopo l&quot;introduzione a Application Insights, ecco un riepilogo delle funzionalità che è possibile esplorare."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: e1f9c62765e414e194330419f26c2b7437da21b3
ms.openlocfilehash: 79cfe90bb883b5cadf707272d31a990c8886dbe4


---
# <a name="more-telemetry-from-application-insights"></a>Altri dati di telemetria da Application Insights
Dopo aver [aggiunto Application Insights al codice ASP.NET](app-insights-asp-net.md), è possibile eseguire alcune operazioni per ottenere altri dati di telemetria. 

## <a name="if-your-app-runs-on-your-iis-server-"></a>App in esecuzione nel server IIS
Se l'app è ospitata in server IIS sotto il proprio controllo, installare Application Insights Status Monitor nei server. Se è già installato, non è necessario eseguire alcuna operazione.

1. In ogni server Web IIS accedere con le credenziali di amministratore.
2. Scaricare e installare il [programma di installazione di Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).
3. Nell'Installazione guidata accedere a Microsoft Azure.

Non è necessario eseguire altre operazioni, ma è possibile verificare che per l'app sia abilitato il monitoraggio.

![Estensione in Azure](./media/app-insights-asp-net-more/025.png)

È possibile usare Status Monitor per [abilitare il monitoraggio in fase di esecuzione](app-insights-monitor-performance-live-website-now.md)anche se le app non sono state instrumentate in Visual Studio.

### <a name="what-do-you-get"></a>Vantaggi
Se nei computer server è installato Status Monitor, si ottengono alcuni dati di telemetria aggiuntivi:

* Telemetria delle dipendenze (chiamate SQL e chiamate REST effettuate dall'app) per le app .NET 4.5. Per le versioni successive di .NET, per la telemetria delle dipendenze non è necessario Status Monitor. 
* Analisi dello stack delle eccezioni più dettagliate.
* Contatori delle prestazioni. In Application Insights, tali contatori vengono visualizzati nel pannello Server. 

![Estensione in Azure](./media/app-insights-asp-net-more/070.png)

Per aumentare o ridurre il numero di contatori visualizzati, [modificare i grafici](app-insights-metrics-explorer.md). Se il contatore delle prestazioni desiderato non è incluso nel set disponibile, è possibile [aggiungerlo al set raccolto dal modulo dei contatori delle prestazioni](app-insights-performance-counters.md).

## <a name="if-its-an-azure-web-app-"></a>App Web di Azure
Se l'app viene eseguita come App Web di Azure, passare al pannello di controllo di Azure per l'app o la VM e aprire il pannello Application Insights. 

### <a name="what-do-you-get"></a>Vantaggi
* Analisi dello stack delle eccezioni più dettagliate.
* Telemetria delle dipendenze (chiamate SQL e chiamate REST effettuate dall'app) per le app .NET 4.5. Per le versioni successive di .NET, per la telemetria delle dipendenze non è necessaria l'estensione. 

![Estensione in Azure](./media/app-insights-asp-net-more/080.png)

È possibile usare questo metodo per [abilitare il monitoraggio delle prestazioni in fase di esecuzione](app-insights-monitor-performance-live-website-now.md)anche se le app non sono state instrumentate in Visual Studio.

## <a name="client-side-monitoring"></a>Monitoraggio sul lato client
È stato installato l'SDK che invia i dati di telemetria dal lato server (back-end) dell'applicazione. Ora è possibile aggiungere il monitoraggio sul lato client, che fornisce dati su utenti, sessioni, visualizzazioni di pagina ed eventuali eccezioni o arresti anomali verificatisi nel browser. Sarà anche possibile scrivere codice personalizzato per tenere traccia del modi in cui gli utenti interagiscono con l'app, a un livello di dettaglio che include i clic e le sequenze di tasto.

Per ottenere dati di telemetria dai browser client, aggiungere il frammento JavaScript di Application Insights a ogni pagina Web.

1. In Azure aprire la risorsa di Application Insights per l'app.
2. Aprire Guida introduttiva, quindi Monitoraggio e diagnosi dell'applicazione lato client e copiare il frammento.
3. Incollarlo in modo che risulti incluso nell'intestazione di ogni pagina Web. In genere si ottiene questo risultato incollando il frammento nella pagina di layout master.

![Estensione in Azure](./media/app-insights-asp-net-more/100.png)

Si noti che il codice contiene la chiave di strumentazione che identifica la risorsa dell'applicazione.

### <a name="what-do-you-get"></a>Vantaggi
* Possibilità di scrivere codice JavaScript per inviare [dati di telemetria personalizzati dalle pagine Web](app-insights-api-custom-events-metrics.md), ad esempio per tenere traccia dei clic sui pulsanti.
* In [Analisi](app-insights-analytics.md) si ottengono dati in `pageViews`, in AJAX si ottengono dati in `dependencies`. 
* [Dati sull'utilizzo e sulle prestazioni dei client](app-insights-javascript.md) nel pannello Browser.

![Estensione in Azure](./media/app-insights-asp-net-more/090.png)

[Altre informazioni sul rilevamento delle pagine Web.](app-insights-web-track-usage.md)

## <a name="track-application-version"></a>Rilevare la versione dell’applicazione
Assicurarsi che `buildinfo.config` sia generato dal processo di MSBuild. Nel file con estensione csproj, aggiungere:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

Quando ha le informazioni di compilazione, il modulo Web di Application Insights aggiunge automaticamente la **versione dell'applicazione** come proprietà a ogni elemento dei dati di telemetria. Questo consente di filtrare in base alla versione quando si eseguono [ricerche diagnostiche](app-insights-diagnostic-search.md) o quando si [esplorano le metriche](app-insights-metrics-explorer.md). 

Si noti tuttavia che il numero di versione di build viene generato solo da Build MS, non dallo sviluppatore di build in Visual Studio.

## <a name="availability-web-tests"></a>Test Web di disponibilità
Inviare richieste HTTP dell'app Web a intervalli regolari da tutto il mondo. Se la risposta è lenta o non affidabile, l'utente riceve un avviso.

Nella risorsa di Application Insights per l'app fare clic sul riquadro Disponibilità per aggiungere, modificare e visualizzare test Web.

È possibile aggiungere più test eseguiti in più località.

![Estensione in Azure](./media/app-insights-asp-net-more/110.png)

[Altre informazioni](app-insights-monitor-web-app-availability.md)

## <a name="custom-telemetry-and-logging"></a>Telemetria personalizzata e registrazione
I pacchetti di Application Insights aggiunti al codice forniscono un'API che può essere chiamata dall'applicazione.

* [Generare metriche ed eventi personalizzati](app-insights-api-custom-events-metrics.md), ad esempio per conteggiare eventi aziendali o monitorare le prestazioni.
* [Acquisire le tracce di log](app-insights-asp-net-trace-logs.md) da Log4Net, NLog o System.Diagnostics.Trace.
* [Filtrare, modificare o aumentare](app-insights-api-filtering-sampling.md) i dati di telemetria standard inviati dall'app scrivendo processori di telemetria. 

## <a name="powerful-analysis-and-presentation"></a>Analisi e presentazione efficienti
È possibile esaminare i dati in numerosi modi. Se si è iniziato da poco a usare Application Insights, vedere gli articoli seguenti:

|  |  |
| --- | --- |
| [**Ricerca diagnostica dei dati dell'istanza**](app-insights-visual-studio.md)<br/>Cercare e filtrare eventi come richieste, eccezioni, chiamate a dipendenze, tracce di log e visualizzazioni di pagina. In Visual Studio, passare al codice dall'analisi dello stack. |![Visual Studio](./media/app-insights-asp-net-more/61.png) |
| [**Esplora metriche per i dati aggregati**](app-insights-metrics-explorer.md)<br/>Esaminare, filtrare e segmentare dati aggregati come frequenza delle richieste, errori, eccezioni, tempi di risposta e tempi di caricamento delle pagine. |![Visual Studio](./media/app-insights-asp-net-more/060.png) |
| [**Dashboard**](app-insights-dashboards.md#dashboards)<br/>Combinare dati di più risorse e condividerli con altri utenti. Ideale per le applicazioni multi-componente e per la visualizzazione continua negli spazi del team. |![Esempio di dashboard](./media/app-insights-asp-net-more/62.png) |
| [**Flusso di metriche in tempo reale**](app-insights-metrics-explorer.md#live-metrics-stream)<br/>Quando si distribuisce una nuova build, controllare questi indicatori delle prestazioni in tempo quasi reale per verificare che tutto funzioni come previsto. |![Esempio di analisi](./media/app-insights-asp-net-more/050.png) |
| [**Analisi**](app-insights-analytics.md)<br/>Questo avanzato linguaggio di query consente di trovare risposta a domande approfondite sull'utilizzo e sulle prestazioni dell'app. |![Esempio di analisi](./media/app-insights-asp-net-more/010.png) |
| [**Avvisi automatici e manuali**](app-insights-alerts.md)<br/>Gli avvisi automatici si adattano ai modelli normali di telemetria dell'app e si attivano quando i dati si discostano dal modello consueto. È anche possibile impostare avvisi su livelli particolari delle metriche standard o personalizzate. |![Esempio di avviso](./media/app-insights-asp-net-more/020.png) |

## <a name="data-management"></a>Gestione dati
|  |  |
| --- | --- |
| [**Esportazione continua**](app-insights-export-telemetry.md)<br/>Copiare tutti i dati di telemetria in una risorsa di archiviazione per poterli analizzare nel modo preferito. | |
| **API di accesso ai dati**<br/>Presto disponibile. | |
| [**Campionamento**](app-insights-sampling.md)<br/>Riduce la frequenza di dati e consente di rimanere entro il limite del proprio piano tariffario. |![Riquadro del campionamento](./media/app-insights-asp-net-more/030.png) |




<!--HONumber=Dec16_HO3-->


