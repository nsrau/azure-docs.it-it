<properties
	pageTitle="Application Insights per app Web JavaScript | Microsoft Azure"
	description="Ottenere i conteggi delle visualizzazioni pagina e delle sessioni, i dati client Web e la traccia dei modelli di utilizzo. Rilevare le eccezioni e i problemi di prestazioni nelle pagine Web JavaScript."
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/19/2016"
	ms.author="awills"/>

# Application Insights per pagine Web

[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Scoprire le prestazioni e l'utilizzo della pagina Web o dell'app. Aggiungere Visual Studio Application Insights allo script di pagina per ottenere gli intervalli di tempo di caricamento delle pagine e delle chiamate AJAX, i conteggi e i dettagli delle eccezioni del browser e degli errori AJAX, nonché i conteggi relativi a utenti e sessioni. Tutti questi elementi possono essere segmentati per pagina, sistema operativo client e versione del browser, posizione geografica e altre dimensioni. È anche possibile impostare avvisi relativi al numero di errori o rallentare il caricamento delle pagine.

Application Insights è compatibile con tutte le pagine Web, con una minima aggiunta di codice JavaScript. Se il servizio web è [Java](app-insights-java-get-started.md) o [ASP.NET](app-insights-asp-net.md), è possibile integrare i dati di telemetria dal server e dai client.

È necessaria una sottoscrizione a [Microsoft Azure](https://azure.com). Se il team ha una sottoscrizione per l'organizzazione, chiedere al proprietario di aggiungere l'account Microsoft dell'utente. Lo sviluppo e un uso su scala ridotta rientrano nel piano tariffario gratuito disponibile.


## Installare Application Insights per la pagina Web

Potrebbe essere già presente. Se l'app è un nuovo progetto ASP.NET e si sceglie di aggiungere Application Insights nella finestra di dialogo Nuovo progetto in Visual Studio, lo script è già stato aggiunto in questa fase.

In caso contrario, è necessario aggiungere un frammento di codice alle pagine Web, come indicato di seguito.

### Aprire una risorsa di Application Insights

La risorsa di Application Insights è dove vengono visualizzati i dati sulle prestazioni e l'utilizzo della pagina.

Accedere al [portale di Azure](https://portal.azure.com).

Se è già stato configurato il monitoraggio per il lato server dell'applicazione, si disporrà già di una risorsa:

![Scegliere Sfoglia, quindi Servizi per gli sviluppatori, Application Insights](./media/app-insights-javascript/01-find.png)

Se non si ha un account, crearlo:

![Scegliere Nuovo, quindi Servizi per gli sviluppatori, Application Insights.](./media/app-insights-javascript/01-create.png)


*Altre domande?* [Altre informazioni sulla creazione di una risorsa](app-insights-create-new-resource.md).


### Aggiungere lo script SDK per l'app o pagine Web

In Avvio rapido ottenere lo script per le pagine Web:

![Nel pannello di panoramica delle app, scegliere Avvio rapido, quindi ottenere il codice per monitorare le pagine Web. Copiare lo script.](./media/app-insights-javascript/02-monitor-web-page.png)

Inserire lo script poco prima del tag `<head>` di ogni pagina di cui si vuole tenere traccia. Se il sito Web presenta una pagina master, è possibile inserire lo script in tale posizione. Ad esempio:

* In un progetto ASP.NET MVC inserire lo script in `View\Shared\_Layout.cshtml`
* Nel pannello di controllo di un sito di SharePoint aprire [Impostazioni sito/pagina Master](app-insights-sharepoint.md).

Lo script contiene la chiave di strumentazione che indirizza i dati alla risorsa di Application Insights.

[Spiegazione più approfondita dello script](http://apmtips.com/blog/2015/03/18/javascript-snippet-explained/).

*(Se si usa un framework di pagine Web noto, cercare adattatori Application Insights. Ad esempio, [un modulo AngularJS](http://ngmodules.org/modules/angular-appinsights)).*


## Configurazione dettagliata

Sono disponibili diversi [parametri](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) che è possibile impostare, anche se nella maggior parte dei casi non è necessario. Ad esempio, è possibile disabilitare o limitare il numero di chiamate AJAX segnalate per visualizzazione pagina allo scopo di ridurre il traffico oppure è possibile impostare la modalità di debug per velocizzare lo spostamento dei dati di telemetria nella pipeline senza che vengano divisi in batch.

Per impostare questi parametri, cercare la riga seguente nel frammento di codice e aggiungere altri elementi delimitati da virgole dopo la riga:

    })({
      instrumentationKey: "..."
      // Insert here
    });

I [parametri disponibili](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) includono:

    // Send telemetry immediately without batching.
    // Remember to remove this when no longer required, as it
    // can affect browser performance.
    enableDebug: true,

    // Don't log browser exceptions.
    disableExceptionTracking: true,

    // Limit number of Ajax calls logged, to reduce traffic.
    maxAjaxCallsPerView: 10, // default is 500

    // Set these dynamically for an authenticated user.
    appUserId: string,
    accountId: string,



## <a name="run"></a>Eseguire l'app

Eseguire l'app Web, usarla un periodo di tempo per generare dati di telemetria e attendere alcuni secondi. È possibile eseguirla premendo **F5** sul computer di sviluppo o pubblicarla e metterla a disposizione degli utenti.

Se si desidera controllare i dati di telemetria che un'app Web sta inviando ad Application Insights, usare gli strumenti di debug del browser (**F12** in molti browser). I dati vengono inviati a dc.services.visualstudio.com.

## Esplorare i dati sulle prestazioni del browser

Aprire il pannello Browser per visualizzare i dati sulle prestazioni aggregati relativi ai browser degli utenti.

![In portal.azure.com aprire la risorsa dell'app e fare clic su Impostazioni, Browser](./media/app-insights-javascript/03.png)


*Ancora nessun dato? Fare clic su **Aggiorna** nella parte superiore della pagina. Ancora niente di fatto? Vedere [Risoluzione dei problemi](app-insights-troubleshoot-faq.md).*

Il pannello Browser è un [pannello di Esplora metriche](app-insights-metrics-explorer.md) con filtri predefiniti e selezioni di grafici. È possibile modificare l'intervallo di tempo, i filtri e la configurazione del grafico e salvare il risultato tra i preferiti. Fare clic su **Ripristina impostazioni predefinite** per tornare alla configurazione originale del pannello.

## Prestazioni di caricamento delle pagine

Nella parte superiore è presente un grafico segmentato relativo ai tempi di caricamento delle pagine. L'altezza totale del grafico rappresenta il tempo medio di caricamento e visualizzazione delle pagine dall'app nel browser degli utenti. Il tempo viene misurato dal momento in cui il browser invia la richiesta HTTP iniziale fino al termine dell'elaborazione di tutti gli eventi di caricamento sincrono, inclusi gli script di esecuzione e layout. Non sono incluse le attività asincrone, ad esempio il caricamento di Web part da chiamate AJAX.

Il grafico segmenta il tempo di caricamento totale delle pagine negli [intervalli di tempo standard definiti da W3C](http://www.w3.org/TR/navigation-timing/#processing-model).

![](./media/app-insights-javascript/08-client-split.png)

Si noti che il tempo di *connessione di rete* è spesso più lento del previsto, perché è una media di tutte le richieste dal browser al server. Molte richieste singole hanno un tempo di connessione pari a 0 perché esiste già una connessione attiva sul server.

### Caricamento lento

Il caricamento lento delle pagine è tra le principali cause di insoddisfazione per gli utenti. Se il grafico indica caricamenti lenti delle pagine, è possibile eseguire delle semplici ricerche diagnostiche.

Il grafico mostra la media di tutti i caricamenti di pagina nell'app. Per verificare se il problema è limitato a determinate pagine, vedere la griglia segmentata in base agli URL delle pagine più in basso nel pannello:

![](./media/app-insights-javascript/09-page-perf.png)

Si noti il conteggio delle visualizzazioni pagina e la deviazione standard. Se il conteggio delle pagine è molto basso, il problema non influisce molto sugli utenti. Una deviazione standard elevata, paragonabile alla media stessa, indica grandi differenze tra le singole misure.

**Ingrandire un URL e una visualizzazione pagina.** Fare clic su un nome di pagina per visualizzare un pannello relativo ai grafici del browser filtrati in base a tale URL e quindi fare clic sull'istanza di una visualizzazione pagina.

![](./media/app-insights-javascript/35.png)

Fare clic su `...` per un elenco completo delle proprietà per tale evento o controllare le chiamate AJAX e gli eventi correlati. La lentezza delle chiamate AJAX influisce sul tempo di caricamento totale delle pagine se le chiamate sono sincrone. Gli eventi correlati includono le richieste server per lo stesso URL, se Application Insights è stato configurato nel server Web.


**Prestazioni delle pagine nel tempo.** Tornare al pannello Browser e trasformare la griglia del tempo di caricamento della visualizzazione pagina in un grafico a linee per verificare se ci sono stati picchi in orari specifici:

![Fare clic sull'intestazione della griglia e selezionare un nuovo tipo di grafico](./media/app-insights-javascript/10-page-perf-area.png)

**Segmentare in base ad altre dimensioni.** Il caricamento delle pagine potrebbe risultare più lento in un determinato browser, un sistema operativo client o una località utente. Aggiungere un nuovo grafico e provare la dimensione **Raggruppa in base a**.

![](./media/app-insights-javascript/21.png)


## Prestazioni AJAX

Assicurarsi che le prestazioni di tutte le chiamate AJAX nelle pagine Web siano soddisfacenti. Spesso vengono usate per riempire parti della pagina in modo asincrono. Anche se la pagina in generale viene caricata tempestivamente, gli utenti potrebbero non essere soddisfatti dei tempi di caricamento e visualizzazione troppo lenti di alcune Web part.

Le chiamate AJAX eseguite dalla pagina Web vengono visualizzate nel pannello Browser come dipendenze.

Sono disponibili grafici di riepilogo nella parte superiore del pannello:

![](./media/app-insights-javascript/31.png)

e griglie dettagliate più in basso:

![](./media/app-insights-javascript/33.png)

Fare clic su una riga per visualizzare i dettagli specifici.


> [AZURE.NOTE] Se si elimina il filtro Browser nel pannello, le dipendenze AJAX e server verranno incluse entrambe in questi grafici. Fare clic su Ripristina impostazioni predefinite per riconfigurare il filtro.

**Per esaminare le chiamate AJAX non riuscite**, scorrere verso il basso fino alla griglia degli errori di dipendenza e quindi fare clic su una riga per visualizzare le istanze specifiche.

![](./media/app-insights-javascript/37.png)

Fare clic su `...` per visualizzare i dati di telemetria completi per una chiamata AJAX.

### Nessuna chiamata AJAX segnalata

Le chiamate AJAX includono tutte le chiamate HTTP inviate dallo script della pagina Web. Se non vengono segnalate, assicurarsi che nel frammento di codice non vengano impostati i [parametri](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) `disableAjaxTracking` o `maxAjaxCallsPerView`.

## Eccezioni del browser

Nel pannello Browser è presente un grafico di riepilogo delle eccezioni, una griglia dei tipi di eccezione è disponibile più in basso nel pannello.

![](./media/app-insights-javascript/39.png)

Se le eccezioni del browser non vengono segnalate, assicurarsi che nel frammento di codice non venga impostato il [parametro](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) `disableExceptionTracking`.

## Esaminare singoli eventi di visualizzazione pagina

In genere la telemetria delle visualizzazioni di pagina viene analizzata da Application Insights e vengono quindi visualizzati solo i report cumulativi, mediati su tutti gli utenti del sito. A scopo di debug, tuttavia, è possibile visualizzare anche singoli eventi di visualizzazione di pagina.

Nel pannello di ricerca diagnostica, impostare Filtri su Visualizzazione pagina.

![](./media/app-insights-javascript/12-search-pages.png)

Selezionare qualsiasi evento per visualizzare altri dettagli. Nella pagina dei dettagli fare clic su "..." per visualizzare altri dettagli.

> [AZURE.NOTE] Se si usa [Ricerca](app-insights-diagnostic-search.md), si noti che è necessaria la corrispondenza di tutta la parola: "Informaz" e "Info" non corrispondono a "Informazioni", a differenza di "Informa*". Inoltre, non è possibile iniziare un termine di ricerca con un carattere jolly. Ad esempio, se si cerca "*irc" non si troverà "Circa".

> [Altre informazioni sulla ricerca diagnostica](app-insights-diagnostic-search.md)

### Proprietà delle visualizzazioni di pagina

* **Durata della visualizzazione pagina**: tempo necessario per caricare la pagina e iniziare a eseguire gli script. In particolare, indica l'intervallo tra l'avvio del caricamento della pagina e l'esecuzione di trackPageView. Se si sposta trackPageView dalla posizione consueta dopo l'inizializzazione dello script, rifletterà un valore diverso.


## Conteggi di pagina personalizzati

Per impostazione predefinita, viene conteggiata una pagina ogni volta che una nuova pagina viene caricata nel browser client. Potrebbero tuttavia essere utile conteggiare visualizzazioni di pagina aggiuntive. Ad esempio, il contenuto di una pagina potrebbe essere visualizzato in schede e si desidera conteggiare una pagina quando l'utente cambia scheda oppure un codice JavaScript nella pagina potrebbe caricare nuovi contenuti senza cambiare l'URL del browser.

Inserire una chiamata JavaScript simile a questa nel punto appropriato nel codice del client:

    appInsights.trackPageView(myPageName);

Il nome della pagina può contenere gli stessi caratteri di un URL, ma i caratteri successivi a "#" o "?" verranno ignorati.



## Monitoraggio dell'utilizzo


Per sapere in che modo gli utenti usano l'app,

* [Informazioni sul monitoraggio dell'utilizzo](app-insights-web-track-usage.md)
* [Altre informazioni sull'API per gli eventi e le metriche personalizzati](app-insights-api-custom-events-metrics.md).


#### <a name="video"></a> Video: Tenere traccia dell'utilizzo

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> Passaggi successivi

* [Tenere traccia dell'utilizzo](app-insights-web-track-usage.md)
* [Metriche ed eventi personalizzati](app-insights-api-custom-events-metrics.md)
* [Build-measure-learn](app-insights-overview-usage.md)

<!------HONumber=AcomDC_0224_2016---->