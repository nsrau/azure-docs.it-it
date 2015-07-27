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
	ms.date="07/14/2015" 
	ms.author="awills"/>


# Application Insights per ASP.NET

*Application Insights è disponibile in anteprima.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


Visual Studio Application Insights consente di monitorare un'applicazione live per [rilevare e diagnosticare i problemi di prestazioni e le eccezioni][detect] e [individuare la modalità di uso dell'app][knowUsers]. È possibile usarlo con una vasta gamma di tipi di applicazioni. Può essere usato per app ospitate nei server IIS locali dell'utente o in macchine virtuali di Azure oppure per app Web di Azure, [oltre che per app per dispositivi e server Java][start].

![Grafici di monitoraggio delle prestazioni di esempio](./media/app-insights-asp-net/10-perf.png)

Per molti tipi di applicazioni [Visual Studio può aggiungere Application Insights all'app](#ide) quasi automaticamente. Tuttavia, poiché si sta leggendo questo articolo per una migliore comprensione, verranno illustrati i passaggi manuali.

#### Prima di iniziare

È necessario:

* Una sottoscrizione a [Microsoft Azure](http://azure.com). Se il team o l'organizzazione ha una sottoscrizione di Azure, il proprietario potrà aggiungere l'utente alla sottoscrizione usando il rispettivo [account Microsoft](http://live.com).
* Visual Studio 2013 o versione successiva.

## <a name="ide"></a> Aggiungere Application Insights al progetto in Visual Studio

#### Se è un nuovo progetto...

Quando si crea un nuovo progetto in Visual Studio, assicurarsi che Application Insights sia selezionato.


![Creazione di un progetto ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


#### o se è un progetto esistente

Fare clic con il pulsante destro del mouse in Esplora soluzioni e scegliere Aggiungi Application Insights.

![Scegliere Aggiungi Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)





#### Opzioni di configurazione

Se si tratta del primo utilizzo, verrà richiesto di accedere o di iscriversi all'anteprima di Microsoft Azure. Questo account è diverso dall'account Visual Studio Online.

Se l'app fa parte di un'applicazione di maggiori dimensioni, potrebbe essere utile usare le impostazioni di configurazione per inserirla nello stesso gruppo di risorse degli altri componenti.


#### Funzione del comando 'Aggiungi Application Insights'

Il comando permette di eseguire due operazioni, che possono anche essere eseguite manualmente, se si preferisce:

* Creazione di una risorsa Application Insights nel [portale di Azure][portal]. I dati verranno visualizzati in questa risorsa. Recupera la *chiave di strumentazione*, che identifica la risorsa.
* Aggiunta del pacchetto NuGet Application Insights Web SDK al progetto e inserimento della chiave in `ApplicationInsights.config`.


## <a name="run"></a> Eseguire il progetto

Eseguire l'applicazione con F5 e provarla aprendo pagine diverse per generare alcuni dati di telemetria.

In Visual Studio verrà visualizzato il conteggio degli eventi che sono stati inviati.

![](./media/app-insights-asp-net/appinsights-09eventcount.png)

## <a name="monitor"></a> Aprire Application Insights

Aprire la risorsa Application Insights nel [portale di Azure][portal].

![Fare clic con il pulsante destro del mouse sul progetto e aprire il portale di Azure](./media/app-insights-asp-net/appinsights-04-openPortal.png)


Cercare i dati nei grafici Panoramica. All'inizio si vedranno solo uno o due punti. ad esempio:

![Fare clic per visualizzare altri dati](./media/app-insights-asp-net/12-first-perf.png)

Fare clic su qualsiasi grafico per visualizzare metriche più dettagliate. [Altre informazioni sulle metriche.][perf]

Ora distribuire l'applicazione e osservare l'accumulo dei dati.


Quando si esegue la modalità debug, la telemetria viene velocizzata nella pipeline, quindi i dati vengono visualizzati in pochi secondi. Quando si distribuisce l'app, i dati si accumulano più lentamente.

#### Dati non visualizzati

* Aprire il riquadro [Ricerca][diagnostic] per visualizzare i singoli eventi.
* Usare l'applicazione, aprendo pagine diverse in modo da generare alcuni dati di telemetria.
* Attendere alcuni secondi e fare clic su Aggiorna.
* Vedere [Risoluzione dei problemi][qna].

#### Problemi del server di compilazione

Vedere [questa sezione sulla risoluzione dei problemi](app-insights-troubleshoot-faq.md#NuGetBuild).


## Aggiungere il monitoraggio del browser

Il monitoraggio sul lato browser o client fornisce dati su utenti, sessioni, visualizzazioni di pagina ed eventuali eccezioni o arresti anomali verificatisi nel browser.

![Scegliere Nuovo, quindi Servizi per gli sviluppatori, Application Insights.](./media/app-insights-asp-net/16-page-views.png)

Sarà anche possibile scrivere codice personalizzato per tenere traccia del modi in cui gli utenti interagiscono con l'app, a un livello di dettaglio che include i clic e le sequenze di tasto.

#### Se i client sono Web browser

Se l'app visualizza pagine Web, aggiungere un frammento di codice JavaScript in ogni pagina. Ottenere il codice dalla risorsa di Application Insights:

![Nell'app Web scegliere Avvio rapido, quindi ottenere il codice per monitorare le pagine Web.](./media/app-insights-asp-net/02-monitor-web-page.png)

Si noti che il codice contiene la chiave di strumentazione che identifica la risorsa dell'applicazione.

[Altre informazioni sul rilevamento delle pagine Web.](app-insights-web-track-usage.md)

#### Se i client sono app per dispositivi

Se l'applicazione soddisfa le richieste di client quali telefono o altri dispositivi, aggiungere l'[SDK appropriato](app-insights-platforms.md) all'app per dispositivi.

Se si configura l'SDK client con la stessa chiave di strumentazione dell'SDK del server, i due flussi verranno integrati e sarà possibile visualizzarli insieme.

## Monitoraggio dell'utilizzo

Dopo avere fornito una nuova storia utente, si vuole determinare quanto viene usata dai clienti e se i clienti riescono a raggiungere i propri obiettivi o se incontrano difficoltà. È possibile ottenere un'immagine dettagliata dell'attività utente inserendo TrackEvent() e altre chiamate nel codice, sia nel client che nel server.

[Usare l'API per tenere traccia dell'utilizzo][api]


## Log di diagnostica

È possibile [acquisire le tracce dei log][netlogs] dal framework di registrazione preferito per semplificare la diagnosi di qualsiasi problema. Le voci del log verranno visualizzate nella [ricerca diagnostica][diagnostic] insieme agli eventi di Application Insights Telemetry.

## Pubblicare l'app

Se l'app non è stata ancora pubblicata, dopo l'aggiunta di Application Insights, pubblicarla ora. È possibile verificare la crescita dei dati nei grafici man mano che gli utenti usano l'app.

### Mantenere risorse separate per sviluppo, test e rilascio

Per un'applicazione importante è consigliabile inviare dati di telemetria da debug, test e produzione a [risorse separate](app-insights-separate-resources.md).



## Aggiungere il rilevamento delle dipendenze

Queste [metriche relative alle dipendenze](app-insights-dependencies.md) possono essere estremamente utili per semplificare la diagnosi dei problemi di prestazioni. Misurano le chiamate dall'app a database, API REST e altri componenti esterni.

![](./media/app-insights-asp-net/04-dependencies.png)

#### Se l'app è in esecuzione nel server IIS

Accedere al server con diritti di amministratore, quindi installare [Application Insights Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).

È anche possibile usare Status Monitor per [instrumentare un'app già in esecuzione](app-insights-monitor-performance-live-website-now.md), anche se non è stata compilata con l'SDK.

#### Se l'app è un'app Web di Azure

Nel pannello di controllo dell'app Web di Azure aggiungere l'estensione di Application Insights.

![Nell'app Web scegliere Impostazioni, Estensioni, Aggiungi, Application Insights](./media/app-insights-asp-net/05-extend.png)

Questa estensione è applicabile solo a un'app compilata con l'SDK. A differenza di Status Monitor, non è in grado di instrumentare un'app esistente.

## Test Web di disponibilità

È possibile [configurare test Web][availability] per eseguire test esterni e verificare che l'applicazione sia attiva e reattiva.


![](./media/app-insights-asp-net/appinsights-10webtestresult.png)






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
[start]: app-insights-get-started.md

 

<!---HONumber=July15_HO3-->