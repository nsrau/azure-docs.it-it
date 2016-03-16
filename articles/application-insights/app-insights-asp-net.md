<properties 
	pageTitle="Analisi di app Web per ASP.NET con Application Insights" 
	description="Analisi delle prestazioni, della disponibilità e dell'utilizzo per il sito Web ASP.NET, ospitato in locale o in Azure." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="03/06/2016" 
	ms.author="awills"/>


# Installare Application Insights per ASP.NET


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Application Insights SDK invia dati di telemetria dall'applicazione Web attiva al portale di Azure, a cui è possibile accedere e visualizzare i grafici di utilizzo e delle prestazioni dell'app.

![Grafici di monitoraggio delle prestazioni di esempio](./media/app-insights-asp-net/10-perf.png)

Sarà inoltre possibile analizzare e correlare richieste specifiche, eccezioni e registrare eventi. È possibile utilizzare l'API per aggiungere dati di telemetria per il monitoraggio delle prestazioni e dell'utilizzo in dettaglio.

#### Prima di iniziare

È necessario:

* Una sottoscrizione a [Microsoft Azure](http://azure.com). Se il team o l'organizzazione ha una sottoscrizione di Azure, il proprietario potrà aggiungere l'utente alla sottoscrizione usando il rispettivo [account Microsoft](http://live.com).
* Visual Studio 2013 Update 3 o versioni successive.

## <a name="ide"></a> Aggiungere Application Insights al progetto in Visual Studio

#### Se è un nuovo progetto...

Quando si crea un nuovo progetto in Visual Studio, assicurarsi che Application Insights sia selezionato.


![Creazione di un progetto ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

Selezionare un account con accesso ad Azure. Potrebbe essere richiesto di immettere nuovamente le credenziali. Se non si esegue l'accesso, verrà aggiunto il codice dell'SDK e sarà possibile configurarlo in un secondo momento.


#### Se si tratta di un progetto esistente

Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Aggiungi Application Insights** o **Configura Application Insights**.

![Scegliere Aggiungi Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)


#### Opzioni di configurazione

Se si tratta del primo utilizzo, verrà richiesto di accedere o di iscriversi a Microsoft Azure.

Se l'app fa parte di un'applicazione di maggiori dimensioni, potrebbe essere utile usare le impostazioni di configurazione per inserirla nello stesso gruppo di risorse degli altri componenti.


####<a name="land"></a> Cosa fa il comando 'Aggiungi Application Insights'

Il comando esegue le seguenti fasi (che potrebbero invece essere eseguite [manualmente](app-insights-start-monitoring-app-health-usage.md) se si preferisce):

1. Aggiunge il pacchetto NuGet di SDK di Application Insights Web al progetto. In Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere Gestisci pacchetti NuGet.
2. Crea una risorsa Application Insights nel [portale di Azure][portal]. È qui che verranno visualizzati i dati. Recupera la *chiave di strumentazione*, che identifica la risorsa.
3. Inserisce la chiave di strumentazione in `ApplicationInsights.config`, in modo che l'SDK possa inviare dati di telemetria al portale.

Se inizialmente non si esegue l'accesso ad Azure, l'SDK verrà installato senza che venga connesso a una risorsa. Sarà possibile visualizzare e cercare i dati di telemetria di Application Insights nella finestra di ricerca di Visual Studio durante il debug. Gli altri passaggi possono essere completati in un secondo momento.

## <a name="run"></a> Eseguire il progetto

Eseguire l'applicazione con F5 e provarla aprendo pagine diverse per generare alcuni dati di telemetria.

In Visual Studio verrà visualizzato il conteggio degli eventi che sono stati registrati.

![In Visual Studio il pulsante Application Insights viene visualizzato durante il debug.](./media/app-insights-asp-net/appinsights-09eventcount.png)

Fare clic su questo pulsante per aprire la ricerca diagnostica.


### Ricerca diagnostica

La finestra di ricerca mostra gli eventi che sono stati registrati. Se è stato eseguito l'accesso ad Azure durante la configurazione di Application Insights, sarà possibile cercare gli stessi eventi nel portale.

![Fare clic con il pulsante destro del mouse sul progetto e scegliere Application Insights, Cerca.](./media/app-insights-asp-net/34.png)

La ricerca di testo libero funziona in tutti i campi degli eventi. Ad esempio, è possibile cercare parte dell'URL di una pagina, il valore di una proprietà, come la città del client, o parole specifiche in un log di traccia.


[Altre informazioni sulla ricerca](app-insights-diagnostic-search.md)

### Eccezioni

Se è stato [impostato il monitoraggio delle eccezioni](app-insights-asp-net-exceptions.md), i report di eccezione verranno visualizzati nella finestra di ricerca.

Fare clic su un'eccezione per ottenere un'analisi dello stack. Se il codice dell'app è aperto in Visual Studio, è possibile fare clic nell'analisi dello stack per visualizzare la relativa riga del codice.



## <a name="monitor"></a> Aprire Application Insights

Aprire la risorsa Application Insights nel [portale di Azure][portal].

![Fare clic con il pulsante destro del mouse sul progetto e aprire il portale di Azure.](./media/app-insights-asp-net/appinsights-04-openPortal.png)

Se non è stato eseguito l'accesso ad Azure durante l'aggiunta di Application Insights all'app, accedere ora. Selezionare **Configura Application Insights**. Questa operazione consente di continuare a visualizzare i dati di telemetria dall'app attiva dopo averla distribuita. I dati di telemetria verranno visualizzati nel portale di Application Insights.

### Metriche: dati aggregati

Cercare i dati nei grafici Panoramica. All'inizio si vedranno solo uno o due punti. ad esempio:

![Fare clic per visualizzare altri dati.](./media/app-insights-asp-net/12-first-perf.png)

Fare clic su qualsiasi grafico per visualizzare metriche più dettagliate. [Altre informazioni sulle metriche.][perf]

* *Nessun dato utente o pagina?* - [Aggiungere i dati utente e pagina](app-insights-web-track-usage.md)

### Ricerca: singoli eventi

Aprire la ricerca per esaminare le singole richieste e i relativi eventi associati.

![Nel pannello di ricerca, cercare i nomi di pagina o altre proprietà.](./media/app-insights-asp-net/21-search.png)

[Altre informazioni sulla ricerca](app-insights-diagnostic-search.md)

* *Nessun evento associato?* Impostare le [eccezioni del server](app-insights-asp-net-exceptions.md) e le [dipendenze](app-insights-asp-net-dependencies.md).


## Dati non visualizzati

* In Visual Studio assicurarsi che l'app stia inviando dati di telemetria. Le tracce dovrebbero essere visibili nella finestra Output e in Hub diagnostica.
* Assicurarsi di osservare i dati corretti in Azure. Accedere al [portale di Azure](https://portal.azure.com), fare clic su "Sfoglia >", "Application Insights" e quindi selezionare l'app.
* Usare l'applicazione, aprendo pagine diverse in modo da generare alcuni dati di telemetria.
* Aprire il pannello [Ricerca][diagnostic] per visualizzare i singoli eventi. Talvolta gli eventi richiedono un po' più di tempo per passare attraverso la pipeline delle metriche.
* Attendere alcuni secondi e fare clic su Aggiorna.
* Vedere [Risoluzione dei problemi][qna].



## Pubblicare l'app

Ora distribuire l'applicazione e osservare l'accumulo dei dati.

Quando si esegue la modalità debug, la telemetria viene velocizzata nella pipeline, quindi i dati vengono visualizzati in pochi secondi. Quando si distribuisce l'app, i dati si accumulano più lentamente.

#### Problemi del server di compilazione

Vedere [questa sezione sulla risoluzione dei problemi](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [AZURE.NOTE] Se l'app genera molti dati di telemetria (e si usa ASP.NET SDK versione 2.0.0-beta3 o successiva), il modulo di campionamento adattivo riduce automaticamente il volume che viene inviato al portale inviando solo una frazione rappresentativa di eventi. Tuttavia, gli eventi che fanno parte della stessa richiesta verranno selezionati o deselezionati come gruppo, per rendere possibile lo spostamento tra eventi correlati. [Informazioni sul campionamento](app-insights-sampling.md).


## Debug della telemetria

### Hub diagnostica

In Visual Studio 2015 o versione successiva Hub diagnostica mostra i dati di telemetria del server Application Insights man mano che vengono generati. Questo avviene anche se si è scelto di installare solo l'SDK, senza connetterlo a una risorsa nel portale di Azure.

![Aprire la finestra Strumenti di diagnostica e controllare gli eventi di Application Insights.](./media/app-insights-asp-net/31.png)

È particolarmente utile se si vuole eseguire il debug di alcuni [dati di telemetria personalizzati](app-insights-api-custom-events-metrics.md) prima di inviarli al portale.

* *Application Insights è stato inizialmente configurato per l'invio dei dati di telemetria al portale. Ora però si vuole fare in modo che i dati di telemetria vengano visualizzati solo in Visual Studio.*

    Impostare come commento la riga `<instrumentationkey>...` dal file ApplicationInsights.config. Quando si è pronti a inviare nuovamente i dati di telemetria al portale, rimuovere il commento.



## Passaggi successivi

- [Dati utente e pagina](../article/application-insights/app-insights-javascript.md#selector1)
- [Eccezioni](../article/application-insights/app-insights-asp-net-exceptions.md#selector1)
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

 

<!---HONumber=AcomDC_0309_2016-->