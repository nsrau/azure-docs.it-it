<properties title="Track usage in web applications with Application Insights" pageTitle="Monitorare l'uso nelle applicazioni Web" description="Log user activities." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />
 
# Monitorare l'uso delle applicazioni Web

È possibile scoprire in che modo viene usata l'applicazione Web. Configurare l'analisi di utilizzo per sapere quali sono le pagine visualizzate dagli utenti, chi sono gli utenti ricorrenti e con quale frequenza visitano il sito. Aggiungere alcuni [eventi e metriche personalizzati][rilevamento] per analizzare in dettaglio le funzionalità usate con maggiore frequenza, scoprire quali sono gli errori più comuni e ottimizzare l'uso dell'app da parte degli utenti.

I dati di telemetria vengono raccolti sia dal client che dal server. I dati del client vengono raccolti da tutti i browser Web moderni e i dati del server possono essere raccolti se si usa la piattaforma ASP.NET (non deve necessariamente essere eseguita in Azure). 

* [Configurare l'analisi di utilizzo Web](#webclient)
* [Analisi dell'utilizzo](#usage)
* [Conteggi di pagine personalizzati per app con singole pagine](#spa)
* [Esame di singoli eventi di pagina](#inspect)
* [Rilevamento dettagliato con eventi e metriche personalizzati](#custom)
* [Video](#video)

## <a name="webclient"></a> Configurazione di analisi di client Web

#### Aggiunta di una risorsa Application Insights in Azure

**Se si sta sviluppando un'app ASP.NET**, [aggiungere Application Insights al progetto Web][start] se questa operazione non è ancora stata eseguita. 

**Se la piattaforma del sito Web non è ASP.NET:** accedere a [Microsoft Azure](http://azure.com), passare al [portale di Anteprima](https://portal.azure.com) e aggiungere una risorsa Application Insights

![](./media/appinsights/appinsights-11newApp.png)

(è possibile tornarvi in un secondo momento con il pulsante Sfoglia).



#### Aggiungere lo script alle pagine Web

In Avvio rapido, ottenere lo script per le pagine Web.

![](./media/appinsights/appinsights-06webcode.png)

Inserire lo script poco prima del tag </head> di ogni pagina di cui si vuole tenere traccia. Se il sito Web presenta una pagina master, è possibile inserire lo script in tale posizione. Ad esempio, in un progetto ASP.NET MVC inserire lo script in View\Shared\_Layout.cshtml

## <a name="usage"></a>Analisi dell'utilizzo

Eseguire il sito Web, usarlo per generare dati di telemetria e attendere 1-2 minuti. È possibile eseguirlo con F5 sul computer di sviluppo o distribuirlo nel server.

Nella falda della panoramica dell'applicazione verranno visualizzati i seguenti riquadri di utilizzo:

![](./media/appinsights/appinsights-47usage.png)

*Se i dati non sono ancora visualizzati, fare clic su **Aggiorna** nella parte superiore della pagina.*

* **Sessioni per browser**

    Una *sessione* è un periodo che inizia quando un utente apre una pagina del sito Web e termina dopo un timeout di 30 minuti dall'ultimo invio di una richiesta Web da parte dell'utente. 

    Fare clic per fare zoom avanti sul grafico.

* **Visualizzazioni delle pagine più importanti**

    Mostra i conteggi totali nelle ultime 24 ore.

    Fare clic sulle visualizzazioni di pagina per ottenere una cronologia più dettagliata.

![](./media/appinsights/appinsights-49usage.png)

Fare clic su Intervallo di tempo per visualizzare una cronologia più lunga che arriva fino a sette giorni.

Fare clic su un grafico per vedere le altre metriche che è possibile visualizzare.

![](./media/appinsights/appinsights-63usermetrics.png)

> [AZURE.NOTE] Deselezionare *tutte* le metriche per abilitarle tutte. Le metriche possono essere visualizzate solo in alcune combinazioni. Quando si seleziona una metrica, quelle incompatibili vengono disabilitate.



## <a name="spa"></a> Conteggi di pagine personalizzati per app con singole pagine

Per impostazione predefinita, viene conteggiata una pagina ogni volta che una nuova pagina viene caricata nel browser client.  Potrebbero tuttavia essere utile conteggiare visualizzazioni di pagina aggiuntive. Ad esempio, il contenuto di una pagina potrebbe essere visualizzato in schede e si desidera conteggiare una pagina quando l'utente cambia scheda oppure un codice JavaScript nella pagina potrebbe caricare nuovi contenuti senza cambiare l'URL del browser. 

Inserire una chiamata JavaScript simile a questa nel punto appropriato nel codice del client:

    appInsights.trackPageView(myPageName);

Il nome della pagina può contenere gli stessi caratteri di un URL, ma i caratteri successivi a "#" o "?" verranno ignorati.


## <a name="inspect"></a> Esame di singoli eventi di visualizzazione di pagina

In genere la telemetria delle visualizzazioni di pagina viene analizzata da Application Insights e vengono quindi visualizzati solo i report cumulativi, mediati su tutti gli utenti del sito. A scopo di debug, tuttavia, è possibile visualizzare anche singoli eventi di visualizzazione di pagina.

Nella falda di ricerca diagnostica, impostare Filtri su Visualizzazione pagina.

![](./media/appinsights/appinsights-51searchpageviews.png)

Selezionare qualsiasi evento per visualizzare altri dettagli.

> [AZURE.NOTE] Se si usa la [ricerca][diagnostica], tenere presente che è necessario cercare parole intere: "Circ" e "irca" non troveranno "Circa", ma "Circ* " sì. Inoltre, non è possibile iniziare un termine di ricerca con un carattere jolly. Ad esempio, se si cerca "*irc" non si troverà "Circa". 

> [Altre informazioni sulla ricerca diagnostica][diagnostica]

## <a name="custom"></a> Rilevamento dettagliato con eventi e metriche personalizzati

Per sapere in che modo gli utenti usano l'app, inserire le chiamate nel codice del client e del server per inviare i dati di telemetria ad Application Insights. È possibile, ad esempio, scoprire quanti utenti creano ordini senza completarli, quali sono gli errori di convalida che si verificano con maggiore frequenza oppure qual è il punteggio medio di un gioco.

[Altre informazioni sull'API per sugli eventi e le metriche personalizzati][rilevamento].

## <a name="video"></a> Video: monitoraggio dell'utilizzo

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a>Passaggi successivi

[Tenere traccia dell'utilizzo con eventi e metriche personalizzati][rilevamento]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]



