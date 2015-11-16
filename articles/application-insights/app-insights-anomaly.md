<properties 
	pageTitle="Application Insights: rilevamento proattivo delle anomalie" 
	description="Application Insights esegue un'analisi approfondita dei dati di telemetria dell'app e segnalare potenziali problemi." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/02/2015" 
	ms.author="awills"/>

#  Application Insights: Avvisi proattivi

*Application Insights è disponibile in anteprima.*


Application Insights esegue un'analisi approfondita dei dati di telemetria dell'app e può segnalare potenziali problemi di prestazioni. È probabile che si stia leggendo questo articolo perché si è ricevuto un avviso proattivo tramite posta elettronica.

## Informazioni sugli avvisi proattivi

* *Perché ho ricevuto questo avviso?*
 * Application Insights analizza periodicamente i dati usando regole per il riconoscimento di schemi e ricerca anomalie che possono indicare problemi di prestazioni nell'applicazione.
* *La notifica indica la sicura presenza di un problema?*
 * No. Invita semplicemente a controllare con attenzione un determinato componente. 
* *Cosa devo fare?*
 * [Esaminare i dati presentati](#responding-to-an-alert) e valutare se possono effettivamente indicare un problema. Se non indicano problemi, si può continuare senza intervenire.
* *Il servizio implica l'accesso manuale ai dati da parte di Microsoft?*
 * No. Il servizio è completamente automatico. L'utente riceve le notifiche, ma i suoi dati rimangono [privati](app-insights-data-retention-privacy.md).


## Informazioni sul processo di rilevamento

* *Quali tipi di anomalie vengono rilevati?*
 * Vengono analizzati quegli schemi che sarebbe troppo lungo verificare manualmente, ad esempio prestazioni insufficienti per una combinazione specifica di località, ora del giorno e piattaforma.
* *Si possono creare regole personalizzate di rilevamento delle anomalie?*
 * Per il momento no. È però possibile:
 * [Impostare avvisi](app-insights-alerts.md) per essere informati quando una determinata metrica supera una soglia.
 * [Esportare dati di telemetria](app-insights-export-telemetry.md) in un [database](app-insights-code-sample-export-sql-stream-analytics.md) o in [PowerBI](app-insights-export-power-bi.md) oppure in [altri](app-insights-code-sample-export-telemetry-sql-database.md) strumenti, in cui è possibile analizzare personalmente i dati.
* *Con quale frequenza viene eseguita l'analisi?*
 * L'analisi non viene eseguita su una risorsa dell'app che non restituisce un numero elevato di dati di telemetria. È quindi improbabile che vengano ricevuti avvisi relativi alle sessioni di debug.


## Come rispondere a un avviso

Aprire il report delle anomalie dal messaggio di posta elettronica o dall'elenco delle anomalie.

![](./media/app-insights-anomaly/02.png)

L'avviso include:

* La descrizione
* Informazioni sull'impatto, ad esempio il numero di utenti interessati o la frequenza con cui si verifica il problema.

Fare clic su un grafico per aprire un pannello con ulteriori dettagli.

Modificare l'intervallo di tempo e i filtri per esplorare i dati di telemetria.

## Gli avvisi sono molto utili, ma cosa si può fare per migliorare le prestazioni?

Come si sarà potuto notare, le risposte lente o con errori rappresentano una delle frustrazioni principali per gli utenti di siti Web. È quindi importante risolvere questo problema.

### Valutazione

Occorre prima di tutto stabilire l'impatto del problema. Se il caricamento di una pagina è sempre lento, ma solo l'1% degli utenti del sito deve visualizzarla, forse non si tratta di un problema particolarmente rilevante. Se invece solo l'1% degli utenti la apre ma la pagina genera eccezioni a ogni apertura, è consigliabile esaminare il problema.

Usare le informazioni sull'impatto disponibili nel messaggio di posta elettronica come indicazioni generali, ma ricordare che sono necessari approfondimenti. Raccogliere altre informazioni per confermare.

Esaminare i parametri del problema. Se dipende dall'area geografica, configurare [test di disponibilità](app-insights-monitor-web-app-availability.md) che includono quell'area: è possibile che si stiano semplicemente verificando problemi di rete nell'area specifica.

### Diagnosi dei caricamenti lenti delle pagine 

Dove si verifica il problema? La risposta del server è lenta, la pagina è molto lunga o il browser non la visualizza facilmente?

Aprire il pannello delle metriche del browser. La [visualizzazione segmentata del tempo di caricamento pagina del browser](app-insights-javascript.md#explore-your-data) mostra indica il tempo necessario per ogni operazione.

* Se il valore di **Tempo per l'invio della richiesta** è elevato, la risposta del server è lenta o la richiesta è un post con molti dati. Esaminare le [metriche delle prestazioni](app-insights-web-monitor-performance.md#metrics) per analizzare i tempi di risposta. 
* Configurare il [rilevamento delle dipendenze](app-insights-dependencies.md) per verificare se la lentezza dipende da servizi esterni o dal database.
* Se il valore **Tempo per la ricezione della risposta** è predominante, la pagina e le relative parti dipendenti, ovvero JavaScript, CSS, immagini e così via (ma non i dati caricati in modo asincrono) sono molto lunghe. Configurare un [test di disponibilità](app-insights-monitor-web-app-availability.md) e assicurarsi di impostare l'opzione per il caricamento delle parti dipendenti. Quando si ottengono risultati, aprire i dettagli di un risultato ed espanderli per verificare i tempi di caricamento dei diversi file.
* Un valore elevato per **Tempo di elaborazione client** indica che l'esecuzione degli script è lenta. Se le cause non sono evidenti, prendere in considerazione l'aggiunta di codice relativo ai tempi e inviare i valori temporali in chiamate trackMetric.

### Migliorare le pagine lente

Sul Web sono disponibili molti suggerimenti per migliorare le risposte del server e i tempi di caricamento delle pagine, quindi non occorre riportarli in questo articolo. Ecco alcuni suggerimenti probabilmente già noti, ma che possono risultare utili:

* Caricamento lento a causa di file di grandi dimensioni: caricare gli script e altre parti in modo asincrono. Usare la creazione di bundle di script. Suddividere la pagina principale in widget che caricano separatamente i rispettivi dati. Non inviare semplice codice HTML per tabelle lunghe. Usare invece uno script per richiedere i dati come JSON o un altro formato compatto, quindi compilare la tabella sul posto. Per semplificare queste operazioni, sono disponibili utili framework, che ovviamente includono script di grandi dimensioni.
* Dipendenze lente del server: esaminare le posizioni geografiche dei componenti. Ad esempio, se si usa Azure, assicurarsi che il server Web e il database si trovino nella stessa area geografica. Le query recuperano una quantità di informazioni superiore al necessario? La memorizzazione nella cache o l'invio in batch possono essere utili?
* Problemi di capacità: esaminare le metriche del server relative ai tempi di risposta e al numero delle richieste. Se i picchi dei tempi di risposta non sono proporzionati ai picchi del numero di richieste, è probabile che le capacità dei server siano insufficienti. 


## Messaggi di posta elettronica di notifica

* *È necessario sottoscrivere questo servizio per ricevere le notifiche?*
 * No. L'apposito bot analizza periodicamente i dati di tutti gli utenti di Application Insights e invia le notifiche se vengono rilevati problemi.
* *È possibile annullare la sottoscrizione oppure ottenere le notifiche inviate ai colleghi?*
 * Fare clic sul collegamento nell'avviso o nel messaggio di posta elettronica. Aprire le impostazioni delle anomalie.
 
    ![](./media/app-insights-anomaly/01.png)

    Attualmente vengono inviate agli utenti che hanno [accesso in scrittura alla risorsa Application Insights](app-insights-resources-roles-access-control.md).
* *Non voglio ricevere un numero elevato di messaggi.*
 * Ogni giorno vengono ricevuti al massimo tre messaggi senza alcuna ripetizione dello stesso messaggio.
* *Se non eseguo alcuna operazione, riceverò un promemoria?*
 * No, il messaggio relativo a un singolo problema viene ricevuto una sola volta.
* *Non trovo più il messaggio di posta elettronica. Dove trovo le notifiche nel portale?*
 * Nel pannello di panoramica dell'app in Application Insights fare clic sul riquadro **Anomalie**. 






 

<!---HONumber=Nov15_HO2-->