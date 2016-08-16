<properties
	pageTitle="Analisi delle tendenze in Visual Studio | Microsoft Azure"
	description="Analizzare, visualizzare ed esaminare le tendenze nei dati di telemetria di Application Insights in Visual Studio."
	services="application-insights"
    documentationCenter=".net"
	authors="numberbycolors"
	manager="douge"/>  

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/14/2016"
	ms.author="daviste"/>  

# Analizzare le tendenze in Visual Studio

Lo strumento Tendenze di Application Insights visualizza la variazione nel tempo degli eventi di telemetria importanti dell'applicazione, consentendo di identificare rapidamente problemi e anomalie. Collegando l'utente a informazioni di diagnostica più dettagliate, Tendenze consente di migliorare le prestazioni dell'app, ricostruire le cause delle eccezioni e individuare informazioni utili dagli eventi personalizzati.

![Finestra Tendenze di esempio](./media/app-insights-trends/app-insights-trends-hero-750.png)  

> [AZURE.NOTE] Tendenze di Application Insights è disponibile in Visual Studio 2015 Update 3 e versioni successive o con l'[estensione Strumenti di analisi per sviluppatori](https://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a) versione 5.209 e successive.

## Aprire Tendenze di Application Insights

È possibile aprire la finestra Tendenze di Application Insights in uno dei modi seguenti:

* Dal pulsante della barra degli strumenti di Application Insights scegliere **Esplora tendenze di telemetria**.
* Dal menu di scelta rapida del progetto scegliere **Application Insights > Esplora tendenze di telemetria**.
* Sulla barra dei menu di Visual Studio scegliere **Visualizza > Altre finestre > Tendenze di Application Insights**.

Potrebbe essere visualizzata la richiesta di selezionare una risorsa. In tal caso, fare clic su **Seleziona una risorsa**, accedere con una sottoscrizione di Azure e quindi scegliere una risorsa di Application Insights di cui si vogliono analizzare le tendenze di telemetria.

## Scegliere un'analisi delle tendenze

![Menu dei tipi comuni di analisi delle tendenze](./media/app-insights-trends/app-insights-trends-1-750.png)  

Per iniziare, scegliere una di cinque analisi comuni delle tendenze, ognuna delle quali analizza i dati delle ultime 24 ore.

* **Analisi dei problemi di prestazioni relativi alle richieste server**: richieste effettuate al servizio, raggruppate in base ai tempi di risposta
* **Analisi degli errori nelle richieste server**: richieste effettuate al servizio, raggruppate in base al codice di risposta HTTP
* **Esame delle eccezioni presenti nell'applicazione**: eccezioni provenienti dal servizio, raggruppate in base al tipo di eccezione
* **Verifica delle prestazioni delle dipendenze dell'applicazione**: servizi chiamati dal servizio, raggruppati in base ai tempi di risposta
* **Esame degli eventi personalizzati**: eventi personalizzati configurati per il servizio, raggruppati in base al tipo di evento

Queste analisi predefinite sono accessibili successivamente dal pulsante **Visualizza tipi comuni di analisi dei dati di telemetria** nell'angolo superiore sinistro della finestra Tendenze.

## Visualizzare le tendenze nell'applicazione

Tendenze di Application Insights crea una visualizzazione basata su serie temporale dei dati di telemetria dell'app. Ogni visualizzazione basata su serie temporale contiene un tipo di dati di telemetria, raggruppati in base a una proprietà di tali dati, in uno specifico intervallo di tempo.

È ad esempio possibile visualizzare le richieste server, raggruppate per paese di provenienza, nelle ultime 24 ore. In questo esempio, ogni bolla nella visualizzazione rappresenta un conteggio delle richieste server di uno specifico paese durante un'ora.

Usare i controlli nella parte superiore della finestra per modificare i tipi di dati di telemetria visualizzati. Prima di tutto, scegliere i tipi di dati di telemetria a cui si è interessati.

* **Tipo di telemetria**: richieste server, eccezioni, dipendenze o eventi personalizzati
* **Intervallo di tempo**: qualsiasi intervallo dagli ultimi 30 minuti agli ultimi 3 giorni
* **Raggruppa per**: tipo di eccezione, ID problema, paese/area geografica e altro ancora

Fare quindi clic su **Analizza telemetria** per eseguire la query.

Per esplorare le bolle nella visualizzazione:

* Fare clic per selezionare una bolla. In questo modo vengono aggiornati i filtri nella parte inferiore della finestra in modo da riepilogare gli eventi che si sono verificati durante uno specifico periodo di tempo.
* Fare doppio clic su una bolla per passare allo strumento Ricerca e visualizzare tutti i singoli eventi di telemetria che si sono verificati durante tale periodo di tempo.
* Premere **CTRL** e quindi fare clic su una bolla per deselezionarla nella visualizzazione.

> [AZURE.TIP] Gli strumenti Analisi e Ricerca consentono di individuare gli eventi di telemetria che causano problemi nel servizio. Se in un pomeriggio i clienti notano che l'app ha una velocità di risposta inferiore, ad esempio, è possibile iniziare il processo di analisi del problema con Tendenze. Analizzare le richieste effettuate al servizio nelle ultime ore, raggruppate in base al tempo di risposta, e osservare se sono presenti gruppi insolitamente consistenti di richieste lente. Fare quindi clic sulla bolla per passare allo strumento Ricerca, che sarà filtrato in base a tali eventi di richiesta. Da Ricerca è possibile esaminare il contenuto di tali richieste e trovare il codice coinvolto per poter risolvere il problema.

## Filtrare tendenze specifiche

I controlli di filtro nella parte inferiore della finestra consentono di individuare tendenze più specifiche. Per applicare un filtro, fare clic sul relativo nome. È possibile passare rapidamente da un filtro all'altro per individuare tendenze eventualmente nascoste in una determinata dimensione della telemetria. Se si applica un filtro in una dimensione, ad esempio Tipo di eccezione, i filtri nelle altre dimensioni rimangono selezionabili anche se visualizzati in grigio. Per annullare l'applicazione di un filtro, fare clic di nuovo su di esso. Premere **CTRL** e quindi fare clic per selezionare più filtri nella stessa dimensione.

![Filtri delle tendenze](./media/app-insights-trends/TrendsFiltering-750.png)  

Per applicare più filtri:

1. Applicare il primo filtro.
2. Fare clic sul pulsante **Apply selected filters and query again** (Applica filtri selezionati e ripeti query) accanto al nome della dimensione del primo filtro. Verrà così eseguita di nuovo la query sui dati di telemetria per gli specifici eventi corrispondenti al primo filtro.
3. Applicare un secondo filtro.
4. Ripetere il processo per trovare tendenze in specifici sottoinsiemi dei dati di telemetria. È ad esempio possibile eseguire una query per le richieste server denominate "GET Home/Index" provenienti dalla Germania che hanno ricevuto un codice di stato 500.

Per annullare l'applicazione di uno di questi filtri, fare clic sul pulsante **Remove selected filters and query again** (Rimuovi filtri selezionati e ripeti query) della dimensione.

![Filtri multipli](./media/app-insights-trends/TrendsFiltering2-750.png)  

## Trovare le anomalie

Lo strumento Tendenze consente di evidenziare bolle di eventi che presentano anomalie rispetto ad altre bolle nella stessa serie temporale. Nel menu a discesa **Tipo di visualizzazione** scegliere **Conteggi nell'intervallo di tempo (evidenziazione delle anomalie)** o **Percentuali nell'intervallo di tempo (evidenziazione delle anomalie)**. Le bolle di colore rosso sono anomale.

Si definiscono anomalie le bolle con conteggi/percentuali che superano di 2,1 volte la deviazione standard dei conteggi o delle percentuali negli ultimi due intervalli di tempo (ad esempio 48 ore se si visualizzano le ultime 24 ore).

![I punti colorati indicano anomalie](./media/app-insights-trends/TrendsAnomalies-750.png)

> [AZURE.TIP] Il processo di evidenziazione delle anomalie può essere particolarmente utile per trovare in una serie temporale di piccole bolle outlier che potrebbero altrimenti sembrare di dimensioni simili.

## <a name="next"></a>Passaggi successivi


[Uso di Application Insights in Visual Studio](app-insights-visual-studio.md): ricerca sui dati di telemetria, visualizzazione dei dati in CodeLens e configurazione di Application Insights.

[Altri dati di telemetria da Application Insights](app-insights-asp-net-more.md): monitorare l'utilizzo, la disponibilità, le dipendenze e le eccezioni, integrare le tracce dei framework di registrazione e scrivere telemetria personalizzata.

[Navigazione e dashboard nel portale Application Insights](app-insights-dashboards.md): dashboard, strumenti avanzati di diagnostica e di analisi, avvisi, mappa attiva delle dipendenze dell'applicazione ed esportazione dei dati di telemetria.

<!---HONumber=AcomDC_0810_2016-->