---
title: Configurare l'analisi di app Web per ASP.NET con Application Insights | Microsoft Docs
description: Configurare l'analisi delle prestazioni, della disponibilità e dell'uso per un sito Web ASP.NET, ospitato in locale o in Azure.
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/09/2016
ms.author: daviste

---
# Installare Application Insights per ASP.NET
[Visual Studio Application Insights](app-insights-overview.md) consente di monitorare un'applicazione live per [rilevare e diagnosticare i problemi di prestazioni e le eccezioni](app-insights-detect-triage-diagnose.md) e [individuare la modalità di uso dell'app](app-insights-overview-usage.md). Può essere usato per app ospitate nei server IIS locali o in VM del cloud oppure per app Web di Azure.

## Prima di iniziare
Sono necessari:

* Visual Studio 2013 Update 3 o versioni successive. È preferibile una versione successiva.
* Una sottoscrizione a [Microsoft Azure](http://azure.com). Se il team o l'organizzazione ha una sottoscrizione di Azure, il proprietario potrà aggiungere l'utente alla sottoscrizione usando il rispettivo [account Microsoft](http://live.com).

Se si è interessati, sono disponibili articoli alternativi sugli argomenti seguenti:

* [Strumentazione di un'app Web in fase di esecuzione](app-insights-monitor-performance-live-website-now.md)
* [Servizi cloud di Azure](app-insights-cloudservices.md)

## <a name="ide"></a> 1. Aggiungere Application Insights SDK
### Se è un nuovo progetto...
Quando si crea un nuovo progetto in Visual Studio, verificare che Application Insights sia selezionato.

![Creazione di un progetto ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

### Se si tratta di un progetto esistente
Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Aggiungi Application Insights Telemetry** o **Configura Application Insights**.

![Choose Add Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* Se si usa un progetto ASP.NET Core, [seguire queste istruzioni per correggere alcune righe di codice](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs).

## <a name="run"></a> 2. Eseguire l'app
Eseguire l'applicazione con F5 e provarla aprendo pagine diverse per generare alcuni dati di telemetria.

In Visual Studio verrà visualizzato il conteggio degli eventi che sono stati registrati.

![In Visual Studio il pulsante Application Insights viene visualizzato durante il debug.](./media/app-insights-asp-net/54.png)

## 3\. Visualizzare i dati di telemetria
### In Visual Studio
Aprire la finestra di Application Insights in Visual Studio facendo clic sul pulsante Application Insights oppure facendo clic con il pulsante destro del mouse sul progetto in Esplora soluzioni:

![In Visual Studio il pulsante Application Insights viene visualizzato durante il debug.](./media/app-insights-asp-net/55.png)

Questa visualizzazione contiene i dati di telemetria generati sul lato server dell'app. Sperimentare i filtri e fare clic su qualsiasi evento per visualizzare altri dettagli.

[Uso di Application Insights in Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a>

### Nel portale
Se non è stata scelta l'opzione *Installa solo SDK*, i dati di telemetria possono essere visualizzati anche nel portale Web di Application Insights.

Il portale offre un maggior numero di grafici, strumenti di analisi e dashboard rispetto a Visual Studio.

Aprire la risorsa Application Insights nel [portale di Azure](https://portal.azure.com/).

![Fare clic con il pulsante destro del mouse sul progetto e aprire il portale di Azure.](./media/app-insights-asp-net/appinsights-04-openPortal.png)

Nel portale verrà visualizzata la telemetria dell'app: ![](./media/app-insights-asp-net/66.png)

* I primi dati di telemetria vengono visualizzati in [Flusso metriche attive](app-insights-metrics-explorer.md#live-metrics-stream).
* I singoli eventi vengono visualizzati in **Cerca** (1). La visualizzazione dei dati potrebbe richiedere alcuni minuti. Fare clic su qualsiasi evento per visualizzarne le proprietà.
* Le metriche aggregate vengono visualizzate nei grafici (2). Potrebbe essere necessario qualche minuto prima che i dati vengano visualizzati qui. Fare clic su qualsiasi grafico per aprire un pannello con altri dettagli.

[Altre informazioni sull'uso di Application Insights nel portale di Azure](app-insights-dashboards.md).

## 4\. Pubblicare l'app
Pubblicare l'app nel server IIS o in Azure. Verificare in [Flusso metriche attive](app-insights-metrics-explorer.md#live-metrics-stream) che tutto funzioni correttamente.

Nel portale di Application Insights verrà creata la telemetria e sarà possibile monitorare le metriche, eseguire ricerche sui dati di telemetria e configurare i [dashboard](app-insights-dashboards.md), nonché usare l'avanzato [linguaggio di query di Analisi](app-insights-analytics.md) per analizzare l'utilizzo e le prestazioni e trovare eventi specifici.

È anche possibile continuare ad analizzare i dati di telemetria in [Visual Studio](app-insights-visual-studio.md) con strumenti come la ricerca diagnostica e [Tendenze](app-insights-visual-studio-trends.md).

> [!NOTE]
> Se la quantità di dati di telemetria inviata dall'app sta per raggiungere le [soglie di limitazione](app-insights-pricing.md#limits-summary), viene attivato il [campionamento](app-insights-sampling.md) automatico. Il campionamento riduce la quantità di dati di telemetria inviata dall'app mantenendo i dati correlati per scopi diagnostici.
> 
> 

## <a name="land"></a> Funzione del comando "Aggiungi Application Insights"
Application Insights invia i dati di telemetria dell'app al portale di Application Insights, ospitato in Microsoft Azure:

![](./media/app-insights-asp-net/01-scheme.png)

Il comando esegue quindi tre operazioni:

1. Aggiunge il pacchetto NuGet della versione Web di Application Insights SDK al progetto. In Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere Gestisci pacchetti NuGet.
2. Crea una risorsa di Application Insights nel [portale di Azure](https://portal.azure.com/). È qui che verranno visualizzati i dati. Recupera la *chiave di strumentazione*, che identifica la risorsa.
3. Inserisce la chiave di strumentazione in `ApplicationInsights.config`, in modo che l'SDK possa inviare dati di telemetria al portale.

Se si vuole, è possibile eseguire questi passaggi manualmente per [ASP.NET 4](app-insights-windows-services.md) o [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).

### Per eseguire l'aggiornamento a future versioni di SDK
Per eseguire l'aggiornamento a una [nuova versione dell'SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), riaprire Gestione pacchetti NuGet e filtrare i pacchetti installati. Selezionare Microsoft.ApplicationInsights.Web e scegliere Aggiorna

Se sono state eseguite tutte le personalizzazioni apportate al file ApplicationInsights.config, salvarne una copia prima di eseguire l'aggiornamento e, successivamente, unire le modifiche nella nuova versione.

## Passaggi successivi
|  |
| --- | --- |
| **[Uso di Application Insights in Visual Studio](app-insights-visual-studio.md)**<br/>Debug con telemetria, ricerca diagnostica e drill-through nel codice. |
| **[Navigazione e dashboard nel portale Application Insights](app-insights-dashboards.md)**<br/>Dashboard, strumenti avanzati di diagnostica e di analisi, avvisi, mappa attiva delle dipendenze dell'applicazione ed esportazione dei dati di telemetria. |
| **[Altri dati di telemetria da Application Insights](app-insights-asp-net-more.md)**<br/>Monitorare l'utilizzo, la disponibilità, le dipendenze e le eccezioni, integrare le tracce dei framework di registrazione e scrivere telemetria personalizzata. |

<!---HONumber=AcomDC_0907_2016-->