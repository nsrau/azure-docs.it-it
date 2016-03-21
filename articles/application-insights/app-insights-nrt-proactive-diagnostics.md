<properties 
	pageTitle="Diagnostica proattiva quasi in tempo reale in Application Insights" 
	description="La diagnostica proattiva quasi in tempo reale informa automaticamente l'utente se il tempo di risposta del server presenta un comportamento insolito. Non è necessaria alcuna configurazione." 
	services="application-insights" 
    documentationCenter=""
	authors="yorac" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2016" 
	ms.author="awills"/>
 
# Diagnostica proattiva quasi in tempo reale

[Visual Studio Application Insights](app-insights-overview.md) avvisa automaticamente l'utente quasi in tempo reale se la frequenza delle richieste non riuscite dell'app Web aumenta in modo significativo. Per poter valutare e diagnosticare il problema, la notifica include un'analisi delle caratteristiche delle richieste non riuscite e i dati di telemetria correlati. Sono disponibili anche collegamenti al portale di Application Insights per un'ulteriore diagnosi. La funzionalità non necessita di alcuna installazione o configurazione, perché usa algoritmi di Machine Learning per stimare la normale percentuale di errori di base. Per poter funzionare, è necessario un determinato volume di traffico minimo.

Questa funzionalità funziona per le app Web Java e ASP.NET ospitate nel cloud o nei server aziendali. Funziona anche per qualsiasi app che genera dati di telemetria di richiesta, ad esempio, un ruolo di lavoro che chiama [TrackRequest()](app-insights-api-custom-events-metrics.md#track-request).

La diagnostica proattiva viene attivata appena si imposta [Application Insights per il progetto](app-insights-get-started.md), purché l'app generi una quantità minima di dati di telemetria.

Ecco un avviso di esempio:

![Esempio di avviso intelligente che l'analisi del cluster riguardo all'errore](./media/app-insights-nrt-proactive-diagnostics/010.png)

Le informazioni fornite includono:

* Il numero di utenti interessati, per far comprendere la portata del problema.
* Un modello caratteristico associato agli errori. In questo esempio è presente un nome di richiesta particolare (URL richiesto), che indica immediatamente in quale punto del codice cercare. In alternativa, si può usare un tipo di dispositivo client o un browser specifico.
* Eccezioni, tracce dei log ed errori nelle dipendenze (database o altri componenti esterni) che sembrano associati alle richieste non riuscite.
* Collegamenti diretti alle ricerche rilevanti nei dati di telemetria in Application Insights.

I normali [avvisi relativi alla metrica](app-insights-alerts.md) indicano che potrebbe essersi verificato un problema. Tuttavia, la funzionalità di diagnostica proattiva quasi in tempo reale avvia automaticamente i processi di diagnostica ed esegue diverse analisi che altrimenti l'utente dovrebbe eseguire manualmente. Si ottengono risultati ben strutturati che consentono individuare rapidamente le cause del problema.

## Funzionamento

La funzionalità di diagnostica proattiva quasi in tempo reale monitora i dati di telemetria ricevuti dall'app e, in particolare, la frequenza delle richieste non riuscite. Questa metrica indica in genere il numero di richieste HTTP che hanno restituito un codice di risposta pari o superiore a 400, a meno che non sia stato scritto codice personalizzato per applicare un [filtro](app-insights-api-filtering-sampling.md#filtering) o generare chiamate [TrackRequest](app-insights-api-custom-events-metrics.md#track-request) personalizzate.

Le prestazioni dell'applicazione hanno un modello di comportamento tipico. Alcune richieste saranno più soggette a errori rispetto ad altre e la percentuale complessiva di errori può aumentare con l'incremento del carico. La funzionalità di diagnostica proattiva quasi in tempo reale usa Machine Learning per individuare queste correlazioni.

Man mano che i dati di telemetria vengono inviati dall'app Web ad Application Insights, la funzionalità di diagnostica proattiva quasi in tempo reale confronta il comportamento corrente con i modelli rilevati negli ultimi giorni. Se la percentuale di errori aumenta in modo significativo rispetto alla previsione stimata in base alle prestazioni precedenti, viene generato un avviso.

Quando viene generato un avviso, il servizio esegue un'analisi a cluster su più dimensioni delle richieste, per provare a identificare un modello dei valori che caratterizzano gli errori. Nell'esempio precedente l'analisi ha rilevato che la maggior parte degli errori riguarda un nome della richiesta specifico, rilevando tuttavia che il numero di errori è indipendente dell'istanza dell'host o del server.

L'analizzatore cerca quindi le eccezioni e gli errori di dipendenza associati alle richieste identificati nel cluster, insieme a un esempio degli eventuali log di traccia associati a queste richieste.

I risultati dell'analisi vengono inviati all'utente con un messaggio di posta elettronica, a meno che sia stato configurato di non eseguire questa operazione.

Analogamente agli [avvisi impostati manualmente](app-insights-alerts.md), è possibile esaminare lo stato dell'avviso e configurarlo nel pannello Avvisi della risorsa di Application Insights. A differenza degli altri avvisi, tuttavia, non è necessario impostare o configurare la funzionalità di diagnostica proattiva quasi in tempo reale. Se necessario, è possibile disabilitarlo o modificare gli indirizzi di posta elettronica di destinazione.

## Valutazione e diagnosi di un avviso

Un avviso indica che è stata rilevata un'anomalia nella frequenza delle richieste non riuscite. È probabile che si sia verificato un problema con l'app o il relativo ambiente.

Dalla percentuale delle richieste e dal numero di utenti interessati è possibile decidere il livello di priorità del problema. Nell'esempio precedente, la percentuale di errori del 15% viene confrontata alla percentuale normale dell'1,3% ed evidenzia sicuramente un'anomalia. L'errore interessava 22 utenti distinti in una particolare operazione. Se si fosse trattato di un'app personale, sarebbe stato più semplice valutare la gravità del problema.

In molti casi, è possibile diagnosticare rapidamente il problema in base al nome della richiesta, alle eccezioni, alle dipendenze e ad altri dati di traccia forniti.

Esistono alcune altre indicazioni. Ad esempio, la percentuale di errori di dipendenza in questo esempio è uguale alla percentuale di eccezioni (89,3%). Ne consegue che l'eccezione è collegata direttamente all'errore di dipendenza e questo indica chiaramente il punto di partenza per la ricerca dell'errore nel codice.

Per approfondire l'analisi, i collegamenti in ogni sezione consentono di passare direttamente a una [pagina di ricerca](app-insights-diagnostic-search.md) filtrata in base alle richieste, all'eccezione, alle dipendenze o alla traccia rilevante. In alternativa, è possibile aprire il [portale di Azure](https://portal.azure.com), passare alla risorsa di Application Insights per l'app e aprire il pannello Errori.

In questo esempio, facendo clic sul collegamento di ricerca si apre il pannello di ricerca di Application Insights nell'istruzione SQL con la causa radice: i valori Null sono stati forniti nei campi obbligatori e non hanno superato la convalida durante l'operazione di salvataggio.


![Ricerca diagnostica](./media/app-insights-nrt-proactive-diagnostics/050.png)

## Esaminare gli avvisi recenti

Per esaminare gli avvisi nel portale, aprire **Impostazioni, Log di controllo**.

![Riepilogo degli avvisi](./media/app-insights-nrt-proactive-diagnostics/040.png)

Fare clic su un avviso per visualizzarne i dettagli completi.


## Configurare gli avvisi 

Aprire la pagina degli avvisi. L'avviso di errore adattivo è disponibile insieme agli eventuali avvisi impostati manualmente e si può vedere se attualmente è in uno stato di avviso.

![Nella pagina Panoramica fare clic sul riquadro Avvisi. In alternativa, in qualsiasi pagina di Metrica fare clic su pulsante Avvisi.](./media/app-insights-nrt-proactive-diagnostics/020.png)

Fare clic sull'avviso per configurarlo.

![Configurazione](./media/app-insights-nrt-proactive-diagnostics/030.png)

Si noti che è possibile disabilitare l'avviso di errore adattivo, ma non eliminarlo o crearne un altro.


## Qual è la differenza

La diagnostica proattiva quasi in tempo reale integra altre funzionalità simili ma distinte di Application Insights.

* Gli [avvisi relativi alla metrica](app-insights-alerts.md) vengono impostati dall'utente e possono monitorare un'ampia gamma di metriche, ad esempio utilizzo della CPU, frequenza delle richieste, tempi di caricamento delle pagine e così via. È possibile usarli per avvisare l'utente, ad esempio, se è necessario aggiungere altre risorse. Al contrario, la diagnostica proattiva quasi in tempo reale copre una gamma ristretta di metriche critiche, attualmente solo la frequenza delle richieste non riuscite, progettate per inviare notifiche quasi in tempo reale dopo l'aumento significativo della frequenza delle richieste non riuscite dell'app Web rispetto al comportamento normale della stessa app Web.

    La funzionalità di diagnostica proattiva quasi in tempo reale adatta automaticamente la soglia in risposta alle condizioni attuali.

    La funzionalità di diagnostica proattiva quasi in tempo reale avvia automaticamente il processo di diagnostica. 
* Anche il [rilevamento proattivo](app-insights-proactive-detection.md) usa l'intelligenza artificiale per individuare modelli insoliti nella metrica, senza richiedere alcuna configurazione. A differenza della diagnostica proattiva quasi in tempo reale, lo scopo del rilevamento proattivo consiste nel trovare i segmenti dei molteplici modi di utilizzo che potrebbero essere gestiti in modo non corretto, ad esempio da pagine specifiche in un tipo specifico di browser. L'analisi viene eseguita ogni giorno e se viene trovato un risultato, è probabilmente molto meno urgente di un avviso. Al contrario, viene eseguita l'analisi per la diagnostica proattiva quasi in tempo reale viene eseguita continuamente sui dati di telemetria in ingresso e l'utente riceverà una notifica entro pochi minuti se la frequenza degli errori del server sono maggiori del previsto.

## Se si riceve un avviso della funzionalità di diagnostica proattiva quasi in tempo reale

*Perché ho ricevuto questo avviso?*

*	È stato rilevato un aumento anomalo delle richieste non riuscite rispetto al valore normale del periodo precedente. Dopo aver analizzato errori e dati di telemetria associati, si consiglia di esaminare più approfonditamente l'eventuale problema. 

*La notifica indica la sicura presenza di un problema?*

*	Gli avvisi vengono emessi in caso di interruzione dell'app o di riduzione delle prestazioni, ma solo l'utente può comprendere completamente la semantica e l'impatto del problema sull'app o gli utenti.

*Il servizio implica l'accesso manuale ai dati da parte di Microsoft?*

*	No. Il servizio è completamente automatico. L'utente riceve le notifiche, ma i suoi dati rimangono [privati](app-insights-data-retention-privacy.md).

*Va eseguita una sottoscrizione a questo avviso?*

*	No. Ogni applicazione che invia dati di telemetria di richiesta include questa regola di avviso.

*È possibile annullare la sottoscrizione oppure ottenere le notifiche inviate ai colleghi?*

*	Sì. In Regole di avviso fare clic sulla regola Diagnostica proattiva quasi in tempo reale per configurarla. È possibile disabilitare l'avviso o modificare i destinatari dell'avviso. 

*Non trovo più il messaggio di posta elettronica. Dove trovo le notifiche nel portale?*

*	In Log di controllo. Fare clic su Impostazioni, Log di controllo, quindi su un avviso per visualizzarne i dettagli completi.

*Alcuni avvisi segnalano problemi noti che è inutile ricevere.*

*	Nel backlog è disponibile una funzionalità per eliminare gli avvisi.


## Inviare commenti e suggerimenti

*L'invio di commenti e suggerimenti sarà molto apprezzato. Usare l'indirizzo di posta elettronica:* [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com).

<!---HONumber=AcomDC_0309_2016-->