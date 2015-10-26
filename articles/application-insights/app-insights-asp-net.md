<properties 
	pageTitle="Application Insights per ASP.NET" 
	description="Analizzare l'uso, la disponibilità e le prestazioni dell'applicazione locale o Web di Microsoft Azure con Application Insights." 
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
	ms.date="10/13/2015" 
	ms.author="awills"/>


# Installare Application Insights per ASP.NET

*Application Insights è disponibile in anteprima.*

<a name="selector1"></a>

[Visual Studio Application Insights](http://azure.microsoft.com/services/application-insights) consente di monitorare un'applicazione live per [rilevare e diagnosticare i problemi di prestazioni e le eccezioni][detect] e [individuare la modalità di uso dell'app][knowUsers]. Funziona installando un SDK nell'applicazione. L'SDK invia i dati di telemetria relativi all'app al servizio Application Insights, dove è possibile analizzare e visualizzare il comportamento dell'app.


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Aggiungere l'SDK all'app in Visual Studio per ottenere grafici relativi a richieste del server, tempi di risposta ed errori.

![Esempi di grafici di monitoraggio delle prestazioni](./media/app-insights-asp-net/10-perf.png)

È anche possibile utilizzare l'API per monitorare l'uso in dettaglio.

#### Prima di iniziare

È necessario:

* Una sottoscrizione a [Microsoft Azure](http://azure.com). Se il team o l'organizzazione ha una sottoscrizione di Azure, il proprietario potrà aggiungere l'utente alla sottoscrizione usando il rispettivo [account Microsoft](http://live.com).
* Visual Studio 2013 Update 3 o versioni successive.

## <a name="ide"></a> Aggiungere Application Insights al progetto in Visual Studio

#### Se è un nuovo progetto...

Quando si crea un nuovo progetto in Visual Studio, assicurarsi che Application Insights sia selezionato.


![Creazione di un progetto ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


#### ...o se è un progetto esistente

Fare clic con il pulsante destro del mouse in Esplora soluzioni e scegliere Aggiungi Application Insights.

![Scegliere Aggiungi Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)





#### Opzioni di configurazione

Se si tratta del primo utilizzo, verrà richiesto di accedere o di iscriversi a Microsoft Azure.

Se l'app fa parte di un'applicazione di maggiori dimensioni, potrebbe essere utile usare le impostazioni di configurazione per inserirla nello stesso gruppo di risorse degli altri componenti.


####<a name="land"></a> Cosa fa il comando 'Aggiungi Application Insights'

Il comando esegue le seguenti fasi (che potrebbero invece essere eseguite [manualmente](app-insights-start-monitoring-app-health-usage.md) se si preferisce):

* Crea una risorsa Application Insights nel [portale di Azure][portal]. È qui che verranno visualizzati i dati. Recupera la *chiave di strumentazione*, che identifica la risorsa.
* Aggiunge il pacchetto NuGet di SDK di Application Insights Web al progetto. In Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere Gestisci pacchetti NuGet.
* Colloca la chiave di strumentazione in `ApplicationInsights.config`.


## <a name="run"></a> Eseguire il progetto

Eseguire l'applicazione con F5 e provarla aprendo pagine diverse per generare alcuni dati di telemetria.

In Visual Studio verrà visualizzato il conteggio degli eventi che sono stati inviati.

![](./media/app-insights-asp-net/appinsights-09eventcount.png)

## <a name="monitor"></a> Aprire Application Insights

Aprire la risorsa Application Insights nel [portale di Azure][portal].

![Fare clic con il pulsante destro del mouse sul progetto e aprire il portale di Azure](./media/app-insights-asp-net/appinsights-04-openPortal.png)

### Metriche: dati aggregati

Cercare i dati nei grafici Panoramica. All'inizio si vedranno solo uno o due punti. ad esempio:

![Fare clic per visualizzare altri dati](./media/app-insights-asp-net/12-first-perf.png)

Fare clic su qualsiasi grafico per visualizzare metriche più dettagliate. [Altre informazioni sulle metriche.][perf]

* *Nessun dato utente o pagina?* - [Aggiungere i dati utente e pagina](../article/application-insights/app-insights-asp-net-client.md)

### Ricerca: singoli eventi

Aprire la ricerca per esaminare le singole richieste e i relativi eventi associati.

![](./media/app-insights-asp-net/21-search.png)

[Altre informazioni sulla ricerca](app-insights-diagnostic-search.md)

* *Nessun evento associato?* Impostare le [eccezioni del server](../article/application-insights/app-insights-asp-net-exception-mvc.md) e le [dipendenze](../article/application-insights/app-insights-asp-net-dependencies.md).

### Dati non visualizzati

* Verificare che si sta osservando la cosa giusta. Accedere al [portale di Azure](https://portal.azure.com), fare clic su "Sfoglia >", "Application Insights" e quindi selezionare l'app.
* Usare l'applicazione, aprendo pagine diverse in modo da generare alcuni dati di telemetria.
* Aprire il pannello [Ricerca][diagnostic] per visualizzare i singoli eventi. Talvolta gli eventi richiedono un po' più di tempo per passare attraverso la pipeline delle metriche.
* Attendere alcuni secondi e fare clic su Aggiorna.
* Vedere [Risoluzione dei problemi][qna].


## Pubblicare l'app

Ora distribuire l'applicazione e osservare l'accumulo dei dati.

Quando si esegue la modalità debug, la telemetria viene velocizzata nella pipeline, quindi i dati vengono visualizzati in pochi secondi. Quando si distribuisce l'app, i dati si accumulano più lentamente.

#### Problemi del server di compilazione

Vedere [questa sezione sulla risoluzione dei problemi](app-insights-troubleshoot-faq.md#NuGetBuild).

## Passaggi successivi

- [Dati utente e pagina](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [Eccezioni](../article/application-insights/app-insights-asp-net-exception-mvc.md#selector1)
- [Dipendenze](../article/application-insights/app-insights-asp-net-dependencies.md#selector1)
- [Disponibilità](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)





## Per eseguire l'aggiornamento a future versioni di SDK

Per eseguire l'aggiornamento a una [nuova versione dell'SDK](app-insights-release-notes-dotnet.md), riaprire Gestione pacchetti NuGet e filtrare i pacchetti installati. Selezionare Microsoft.ApplicationInsights.Web e scegliere Aggiorna

Se sono state eseguite tutte le personalizzazioni apportate al file ApplicationInsights.config, salvarne una copia prima di eseguire l'aggiornamento e, successivamente, unire le modifiche nella nuova versione.



## <a name="video"></a>Video

> [AZURE.VIDEO getting-started-with-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=Oct15_HO3-->