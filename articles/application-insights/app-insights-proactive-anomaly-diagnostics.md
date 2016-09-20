<properties 
	pageTitle="Application Insights: diagnostica proattiva delle anomalie | Microsoft Azure" 
	description="Application Insights esegue un'analisi approfondita dei dati di telemetria dell'app e segnalare potenziali problemi." 
	services="application-insights" 
    documentationCenter="windows"
	authors="antonfrMSFT" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/31/2016" 
	ms.author="awills"/>  

#  Diagnostica proattiva delle anomalie

*Application Insights è disponibile in anteprima.*

[Visual Studio Application Insights](app-insights-overview.md) esegue un'analisi approfondita dei dati di telemetria dell'app e può segnalare potenziali problemi di prestazioni. È probabile che si stia leggendo questo articolo perché si è ricevuto un avviso proattivo tramite posta elettronica.

Questa funzionalità non richiede alcuna configurazione e viene attivata automaticamente quando l'app genera dati di telemetria sufficienti.


## Informazioni sul rilevamento proattivo delle anomalie

Il rilevamento proattivo delle anomalie individua modelli insoliti nelle prestazioni dell'app analizzando i dati di telemetria inviati ad Application Insights.

In particolare, rileva i problemi di prestazioni che riguardano solo alcuni utenti o che riguardano gli utenti solo in alcuni casi.

Ad esempio, è possibile avvisare l'utente se le pagine dell'app si caricano molto più lentamente su un tipo di browser rispetto ad altri o se le richieste vengono eseguite più lentamente da un server specifico. Inoltre è possibile rilevare problemi legati alle combinazioni di proprietà, ad esempio caricamenti lenti della pagine in un'area geografica in orari specifici del giorno.

Anomalie come queste sono molto difficili da rilevare semplicemente esaminando i dati, ma sono più comuni di quanto si pensi. Spesso emergono solo quando i clienti si lamentano. A quel punto è troppo tardi: gli utenti interessati sono già passati alla concorrenza.

Attualmente, gli algoritmi esaminano i tempi di caricamento delle pagine, i tempi di risposta richiesti al server e i tempi di risposta delle dipendenze.

Non è necessario impostare regole di soglia o configurare regole. Per rilevare modelli anomali vengono utilizzati Machine Learning e algoritmi di data mining.

I commenti e suggerimenti degli utenti sono molto graditi. Inviare commenti su come migliorare il rilevamento proattivo e le funzionalità aggiuntive da aggiungere. È possibile inviare commenti e suggerimenti tramite Invia smile/Invia faccia imbronciata nel portale oppure mediante un messaggio di posta elettronica all'indirizzo AppInsightsML@microsoft.com.

## Informazioni sugli avvisi proattivi

* *Perché ho ricevuto questo messaggio di posta elettronica?*
 * Il rilevamento proattivo ha analizzato i dati di telemetria dell'applicazione inviati ad Application Insights e ha rilevato un problema a livello delle prestazioni dell'applicazione.
* *La notifica indica la sicura presenza di un problema?*
 * No. Invita semplicemente a controllare con attenzione un determinato componente.
* *Cosa devo fare?*
 * [Esaminare i dati riportati](#responding-to-an-alert). Usare Esplora metriche per esaminare le prestazioni nel tempo e analizzare in dettaglio le altre metriche. Usare la ricerca per filtrare eventi specifici che consentono di identificare la causa radice.
* *Il servizio implica l'accesso manuale ai dati da parte di Microsoft?*
 * No. Il servizio è completamente automatico. L'utente riceve le notifiche, ma i suoi dati rimangono [privati](app-insights-data-retention-privacy.md).


## Informazioni sul processo di rilevamento

* *Quali tipi di anomalie vengono rilevati?*
 * Vengono analizzati quegli schemi che sarebbe troppo lungo verificare manualmente, ad esempio prestazioni insufficienti per una combinazione specifica di località, ora del giorno e piattaforma.
* *Tutti i dati raccolti vengono analizzati da Application Insights?*
 * Attualmente no. Al momento vengono analizzati il tempo di risposta alla richiesta, il tempo di risposta della dipendenza e il tempo di caricamento della pagina. L'analisi di altre metriche verrà implementata a breve.
* *Si possono creare regole personalizzate di rilevamento delle anomalie?*
 * Per il momento no. È però possibile:
 * [Impostare avvisi](app-insights-alerts.md) per essere informati quando una determinata metrica supera una soglia.
 * [Esportare dati di telemetria](app-insights-export-telemetry.md) in un [database](app-insights-code-sample-export-sql-stream-analytics.md) o in [PowerBI](app-insights-export-power-bi.md) oppure in [altri](app-insights-code-sample-export-telemetry-sql-database.md) strumenti, in cui è possibile analizzare personalmente i dati.
* *Con quale frequenza viene eseguita l'analisi?*
 * L'analisi viene eseguita giornalmente sui dati di telemetria dal giorno precedente.
* *Ciò sostituisce gli [avvisi delle metriche](app-insights-alerts.md)?
 * No. Non viene eseguito il rilevamento di tutti i comportamenti presumibilmente ritenuti anomali dall'utente.

## Analisi dei problemi evidenziati dal rilevamento proattivo

Aprire il report delle anomalie dal messaggio di posta elettronica o dall'elenco delle anomalie.

![Nell'avviso di posta elettronica fare clic sul collegamento per aprire il report delle anomalie in Azure](./media/app-insights-proactive-anomaly-diagnostics/03.png)  


* Nell'area **Quando** è visualizzata l'ora in cui è stato rilevato il problema.
* Nell'area **Informazioni approfondite** vengono visualizzate le informazioni seguenti:
 * Il problema rilevato;
 * Le caratteristiche della serie di eventi in cui è stato rilevato il comportamento anomalo.
* Nella tabella viene confrontata la serie di eventi caratterizzata da prestazioni scadenti con il comportamento medio di tutti gli altri eventi.

Fare clic sui collegamenti per aprire Esplora metriche ed eseguire ricerche nei report rilevanti, filtrati in base all'ora e alle proprietà della serie di eventi con prestazioni scadenti.

Modificare l'intervallo di tempo e i filtri per esplorare i dati di telemetria.

## In che modo è possibile migliorare le prestazioni?

Come si sarà potuto notare, le risposte non riuscite o lente rappresentano una delle frustrazioni principali per gli utenti di siti Web. È quindi importante risolvere questo problema.

### Valutazione

Occorre prima di tutto stabilire l'impatto del problema. Se il caricamento di una pagina è sempre lento, ma solo l'1% degli utenti del sito deve visualizzarla, forse non si tratta di un problema particolarmente rilevante. Se invece solo l'1% degli utenti la apre ma la pagina genera eccezioni a ogni apertura, è consigliabile esaminare il problema.

Usare le informazioni sull'impatto disponibili nel messaggio di posta elettronica come indicazioni generali, ma ricordare che sono necessari approfondimenti. Raccogliere altre informazioni per confermare.

Esaminare i parametri del problema. Se dipende dall'area geografica, configurare [test di disponibilità](app-insights-monitor-web-app-availability.md) che includono quell'area: è possibile che si stiano semplicemente verificando problemi di rete nell'area specifica.

### Diagnosi dei caricamenti lenti delle pagine 

Dove si verifica il problema? La risposta del server è lenta, la pagina è molto lunga o il browser non la visualizza facilmente?

Aprire il pannello delle metriche del browser. La [visualizzazione segmentata del tempo di caricamento pagina del browser](app-insights-javascript.md#explore-your-data) mostra il tempo necessario per ogni operazione.

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
 * Fare clic sul collegamento di annullamento della sottoscrizione nell'avviso o nel messaggio di posta elettronica.
 
    Attualmente vengono inviate agli utenti che hanno [accesso in scrittura alla risorsa Application Insights](app-insights-resources-roles-access-control.md).

    È anche possibile modificare l'elenco dei destinatari nel pannello relativo al rilevamento proattivo.
* *Non voglio ricevere un numero elevato di messaggi.*
 * Sono limitati a uno al giorno con il problema più importante che non è ancora stato segnalato. senza alcuna ripetizione dello stesso messaggio.
* *Se non eseguo alcuna operazione, riceverò un promemoria?*
 * No, il messaggio relativo a un singolo problema viene ricevuto una sola volta.
* *Non trovo più il messaggio di posta elettronica. Dove trovo le notifiche nel portale?*
 * Nel pannello di panoramica dell'app in Application Insights fare clic sul riquadro **Rilevamento proattivo**. Si potranno trovare tutte le notifiche di backup per 7 giorni precedenti.


## Passaggi successivi

Gli strumenti di diagnostica seguenti consentono di controllare la telemetria dall'app:

* [Esplora metriche](app-insights-metrics-explorer.md)
* [Esplora ricerche](app-insights-diagnostic-search.md)
* [Linguaggio avanzato di query di Analisi](app-insights-analytics-tour.md)

Diagnostica proattiva è completamente automatica, tuttavia è possibile configurare avvisi aggiuntivi, se necessario.

* [Configurare manualmente gli avvisi relativi alle metriche](app-insights-alerts.md)
* [Test Web di disponibilità](app-insights-monitor-web-app-availability.md)

<!---HONumber=AcomDC_0907_2016-->