<properties 
	pageTitle="Diagnostica proattiva quasi in tempo reale in Application Insights" 
	description="Avvisa su modelli di errore insoluti nell'app e fornisce analisi diagnostica. Non è necessaria alcuna configurazione." 
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
	ms.date="03/15/2016" 
	ms.author="awills"/>
 
# Diagnostica proattiva quasi in tempo reale

[Visual Studio Application Insights](app-insights-overview.md) avvisa automaticamente l'utente quasi in tempo reale se viene rilevato un incremento anomalo delle richieste non riuscite. Per poter valutare e diagnosticare il problema, la notifica include un'analisi delle caratteristiche delle richieste non riuscite e i dati di telemetria correlati. Sono disponibili anche collegamenti al portale di Application Insights per un'ulteriore diagnosi. La funzionalità non necessita di alcuna installazione o configurazione, perché usa algoritmi di Machine Learning per stimare la normale frequenza degli errori.

Questa funzionalità funziona per le app Web Java e ASP.NET ospitate nel cloud o nei server aziendali. Funziona anche per qualsiasi app che genera dati di telemetria di richiesta, ad esempio, un ruolo di lavoro che chiama [TrackRequest()](app-insights-api-custom-events-metrics.md#track-request).

Dopo aver configurato [Application Insights per il progetto](app-insights-get-started.md) e non appena l’applicazione genera una determinata quantità minima di dati di telemetria, la funzionalità di diagnostica attiva quasi in tempo reale impiega 24 ore per apprendere il comportamento normale dell'app prima di essere attivata e inviare avvisi.

Ecco un avviso di esempio:

![Esempio di avviso intelligente che l'analisi del cluster riguardo all'errore](./media/app-insights-nrt-proactive-diagnostics/010.png)

Le informazioni fornite includono:

* La frequenza degli errori confrontata al comportamento normale dell’applicazione.
* Il numero di utenti interessati, per far comprendere la portata del problema.
* Un modello caratteristico associato agli errori. In questo esempio sono presenti un codice di risposta, un nome di richiesta (operazione) e una versione dell’applicazione specifici. che indica immediatamente in quale punto del codice cercare. In alternativa si può usare un browser o un sistema operativo client specifico.
* Eccezioni, tracce di log ed errori di dipendenza (database o altri componenti esterni) che sembrano associati alle particolari richieste non riuscite.
* Collegamenti diretti alle ricerche rilevanti nei dati di telemetria in Application Insights.

I normali [avvisi relativi alla metrica](app-insights-alerts.md) indicano che potrebbe essersi verificato un problema. Tuttavia, la funzionalità di diagnostica proattiva quasi in tempo reale avvia automaticamente i processi di diagnostica ed esegue diverse analisi che altrimenti l'utente dovrebbe eseguire manualmente. Si ottengono risultati ben strutturati che consentono individuare rapidamente le cause del problema.

## Funzionamento

La funzionalità di diagnostica proattiva quasi in tempo reale monitora i dati di telemetria ricevuti dall'app e, in particolare, la frequenza delle richieste non riuscite. Questa metrica conta il numero di richieste per cui la proprietà `Successful request` è false. Per impostazione predefinita `Successful request== (resultCode < 400)` (a meno che sia stato scritto codice personalizzato per [filtrare](app-insights-api-filtering-sampling.md#filtering) o generare particolari chiamate [TrackRequest](app-insights-api-custom-events-metrics.md#track-request)).

Le prestazioni dell'applicazione hanno un modello di comportamento tipico. Alcune richieste saranno più soggette a errori rispetto ad altre e la percentuale complessiva di errori può aumentare con l'incremento del carico. La funzionalità di diagnostica attiva quasi in tempo reale si avvale dell'apprendimento automatico per trovare queste anomalie.

Man mano che i dati di telemetria vengono inviati dall'app Web ad Application Insights, la funzionalità di diagnostica proattiva quasi in tempo reale confronta il comportamento corrente con i modelli rilevati negli ultimi giorni. Se viene osservato un incremento anomalo della frequenza degli errori rispetto alle prestazioni precedenti, viene attivata un’analisi.

Quando l’analisi viene attivata, il servizio esegue un'analisi cluster della richiesta non riuscita per provare a identificare un modello dei valori che caratterizzano gli errori. Nell'esempio precedente l'analisi ha rivelato che la maggior parte degli errori riguardano un codice risultato, un nome richiesta, un URL del server host e un’istanza del ruolo specifici. L’analisi ha anche determinato che la proprietà sistema operativo client è distribuita su più valori e perciò non è presente nell’elenco.

Quando il servizio viene instrumentato con questi dati telemetrici, l'analizzatore cerca un’eccezione e un errore di dipendenza associati alle richieste identificate nel cluster, insieme a un esempio di qualsiasi log di traccia associato a queste richieste.

I risultati dell'analisi vengono inviati all'utente come avviso, a meno che la configurazione attiva preveda di non eseguire questa operazione.

Analogamente agli [avvisi impostati manualmente](app-insights-alerts.md), è possibile esaminare lo stato dell'avviso e configurarlo nel pannello Avvisi della risorsa di Application Insights. A differenza degli altri avvisi, tuttavia, non è necessario impostare o configurare la funzionalità di diagnostica attiva quasi in tempo reale. Se necessario, è possibile disabilitarlo o modificare gli indirizzi di posta elettronica di destinazione.

## Valutazione e diagnosi di un avviso

Un avviso indica che è stato rilevato un incremento anomalo nella frequenza delle richieste non riuscite. È probabile che si sia verificato un problema con l'app o il relativo ambiente.

Dalla percentuale delle richieste e dal numero di utenti interessati è possibile decidere il livello di priorità del problema. Nell'esempio precedente si ha una percentuale di errori del 15% rispetto alla percentuale normale dell'1,3% e ciò evidenzia sicuramente un'anomalia. L'errore interessava 22 utenti distinti in una particolare operazione. Se si fosse trattato di un'app personale, sarebbe stato più semplice valutare la gravità del problema.

In molti casi è possibile diagnosticare rapidamente il problema in base al nome della richiesta, alle eccezioni, agli errori di dipendenza e ad altri dati di traccia forniti.

Esistono alcune altre indicazioni. Ad esempio, la percentuale di errori di dipendenza in questo esempio è uguale alla percentuale di eccezioni (89,3%). Ne consegue che l'eccezione è collegata direttamente all'errore di dipendenza e questo indica chiaramente il punto di partenza per la ricerca dell'errore nel codice.

Per approfondire l'analisi, i collegamenti in ogni sezione consentono di passare direttamente a una [pagina di ricerca](app-insights-diagnostic-search.md) filtrata in base alle richieste, all'eccezione, alla dipendenza o alla traccia pertinenti. In alternativa è possibile aprire il [portale di Azure](https://portal.azure.com), passare alla risorsa di Application Insights per l'app e aprire il pannello Errori.

In questo esempio facendo clic sul collegamento per la visualizzazione dei dettagli degli errori di dipendenza si apre il pannello di ricerca di Application Insights nell'istruzione SQL con la causa radice: i valori Null sono stati forniti nei campi obbligatori e non hanno superato la convalida durante l'operazione di salvataggio.


![Ricerca diagnostica](./media/app-insights-nrt-proactive-diagnostics/051.png)

## Esaminare gli avvisi recenti

Per esaminare gli avvisi nel portale, aprire **Impostazioni, Log di controllo**.

![Riepilogo degli avvisi](./media/app-insights-nrt-proactive-diagnostics/040.png)

Fare clic su un avviso per visualizzarne i dettagli completi.


## Configurare gli avvisi 

Aprire la pagina degli avvisi. La funzionalità di diagnostica attiva è disponibile insieme agli eventuali avvisi impostati manualmente e si può vedere se attualmente si trova in uno stato di avviso.

![Nella pagina Panoramica fare clic sul riquadro Avvisi. In alternativa, in qualsiasi pagina di Metrica fare clic su pulsante Avvisi.](./media/app-insights-nrt-proactive-diagnostics/021.png)

Fare clic sull'avviso per configurarlo.

![Configurazione](./media/app-insights-nrt-proactive-diagnostics/031.png)

Si noti che è possibile disabilitare la diagnostica attiva, ma non eliminarla (o crearne un'altra).


## Qual è la differenza

La diagnostica proattiva quasi in tempo reale integra altre funzionalità simili ma distinte di Application Insights.

* Gli [avvisi relativi alla metrica](app-insights-alerts.md) vengono impostati dall'utente e possono monitorare un'ampia gamma di metriche, ad esempio utilizzo della CPU, frequenza delle richieste, tempi di caricamento delle pagine e così via. È possibile usarli per avvisare l'utente, ad esempio, se è necessario aggiungere altre risorse. Al contrario, la diagnostica proattiva quasi in tempo reale copre una gamma ristretta di metriche critiche, attualmente solo la frequenza delle richieste non riuscite, progettate per inviare notifiche quasi in tempo reale dopo l'aumento significativo della frequenza delle richieste non riuscite dell'app Web rispetto al comportamento normale della stessa app Web.

    La funzionalità di diagnostica proattiva quasi in tempo reale adatta automaticamente la soglia in risposta alle condizioni attuali.

    La funzionalità di diagnostica proattiva quasi in tempo reale avvia automaticamente il processo di diagnostica. 
* Anche il [rilevamento proattivo](app-insights-proactive-detection.md) usa l'intelligenza artificiale per individuare modelli insoliti nella metrica, senza richiedere alcuna configurazione. A differenza della diagnostica proattiva quasi in tempo reale, lo scopo del rilevamento proattivo consiste nel trovare i segmenti dei molteplici modi di utilizzo che potrebbero essere gestiti in modo non corretto, ad esempio da pagine specifiche in un tipo specifico di browser. L'analisi viene eseguita ogni giorno e se viene trovato un risultato, è probabilmente molto meno urgente di un avviso. Al contrario, viene eseguita l'analisi per la diagnostica proattiva quasi in tempo reale viene eseguita continuamente sui dati di telemetria in ingresso e l'utente riceverà una notifica entro pochi minuti se la frequenza degli errori del server sono maggiori del previsto.

## Se si riceve un avviso della funzionalità di diagnostica proattiva quasi in tempo reale

*Perché ho ricevuto questo avviso?*

*	È stato rilevato un incremento anomalo delle richieste non riuscite rispetto al valore normale del periodo precedente. Dopo aver analizzato errori e dati di telemetria associati, si consiglia di esaminare più approfonditamente l'eventuale problema. 

*La notifica indica la sicura presenza di un problema?*

*	Gli avvisi vengono emessi in caso di interruzione dell'app o di riduzione delle prestazioni, ma solo l'utente può comprendere completamente la semantica e l'impatto del problema sull'app o gli utenti.

*Il servizio implica l'accesso manuale ai dati da parte di Microsoft?*

*	No. Il servizio è completamente automatico. L'utente riceve le notifiche, ma i suoi dati rimangono [privati](app-insights-data-retention-privacy.md).

*Va eseguita una sottoscrizione a questo avviso?*

*	No. Ogni applicazione che invia dati di telemetria di richiesta include questa regola di avviso.

*È possibile annullare la sottoscrizione oppure ottenere le notifiche inviate ai colleghi?*

*	Sì. In Regole di avviso fare clic sulla regola Diagnostica proattiva per configurarla. È possibile disabilitare l'avviso o modificare i destinatari dell'avviso. 

*Non trovo più il messaggio di posta elettronica. Dove trovo le notifiche nel portale?*

*	In Log di controllo. Fare clic su Impostazioni, Log di controllo e poi su qualsiasi avviso per vedere le sue occorrenze, ma con una visualizzazione limitata dei dettagli.

*Alcuni avvisi segnalano problemi noti che è inutile ricevere.*

*	Nel backlog è disponibile una funzionalità per eliminare gli avvisi.


## Inviare commenti e suggerimenti

*L'invio di commenti e suggerimenti sarà molto apprezzato. Usare l'indirizzo di posta elettronica:* [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com).

<!---HONumber=AcomDC_0504_2016-->