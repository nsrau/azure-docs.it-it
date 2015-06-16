<properties 
	pageTitle="Impostare gli avvisi in Application Insights" 
	description="Ricevere messaggi di posta elettronica per arresti anomali, eccezioni, modifiche alla metrica." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/17/2015" 
	ms.author="awills"/>
 
# Impostare gli avvisi in Application Insights

[Visual Studio Application Insights][start] può segnalare le modifiche nelle metriche delle prestazioni o di utilizzo nell'app.

Application Insights monitora l'app attiva in un'[ampia varietà di piattaforme][platforms] per consentire di diagnosticare i problemi di prestazioni e comprendere i criteri di utilizzo.

Vi sono due tipi di avvisi:
 
* I **test Web** indicano quando il sito non è disponibile su Internet o risponde lentamente. [Altre informazioni][availability]
* Gli **avvisi delle metriche** indicano quando una qualsiasi metrica supera un valore di soglia per un determinato periodo, ad esempio, numero di errori, memoria o visualizzazioni di pagina. 

Esiste una [pagina separata sui test Web][availability], pertanto qui vengono illustrati gli avvisi delle metriche.

## Avvisi delle metriche

Se non è stato ancora configurato Application Insights per l'app [eseguire prima questa operazione][start].

Per ricevere un messaggio di posta elettronica quando una metrica supera una soglia, iniziare da Esplora metriche o dal riquadro Regole avvisi nel pannello Panoramica.

![Nel pannello Regole avvisi scegliere Aggiungi avviso. Impostare l'app come risorsa per misurare, specificare un nome per l'avviso e scegliere una metrica.](./media/app-insights-alerts/01-set-metric.png)

Impostare la risorsa prima delle altre proprietà. Non scegliere le risorse webtest se si desidera impostare avvisi sulle metriche relative a prestazioni o utilizzo.

Prendere nota delle unità in cui viene chiesto di immettere il valore soglia.

*Il pulsante Aggiungi avviso non viene visualizzato*: si sta usando un account aziendale? È possibile impostare gli avvisi se si ha l'accesso come proprietario o come collaboratore a questa risorsa dell'applicazione. Controllare in Impostazioni -> Utenti. [Informazioni sul controllo di accesso][roles].

## Visualizzare gli avvisi

Gli avvisi vengono inviati all'utente tramite posta elettronica.

Vengono visualizzati anche nel log degli eventi delle operazioni:

![Nella parte inferiore del pannello Panoramica fare clic su 'Eventi nell'ultima settimana'](./media/app-insights-alerts/09-alerts.png)

*Questi "eventi" sono correlati a eventi di telemetria o a eventi personalizzati?*

* No. Questi eventi operativi sono semplicemente un log delle operazioni che si sono verificate in questa risorsa dell'applicazione. 

## Avvisi di disponibilità

È possibile configurare i test Web che testano qualsiasi sito Web ovunque nel mondo. [Altre informazioni][availability]

## Quali sono gli avvisi corretti da impostare?

Dipende dall'applicazione. Per cominciare, è consigliabile non impostare un numero eccessivo di metriche. Esaminare i grafici delle metriche mentre è in esecuzione l'app per avere un'idea del comportamento normale. Sarà così possibile trovare un modo per migliorare le prestazioni. Impostare quindi gli avvisi affinché indichino quando le metriche superano la zona normale.

Tra gli avvisi più diffusi sono compresi:

* I [test Web][availability] che sono importanti se l'applicazione è un sito Web o un servizio Web visibile sulla rete Internet pubblica. Indicano se il sito è inattivo o se risponde lentamente, anche se è un problema del gestore telefonico anziché dell'app. Sono tuttavia test sintetici, per cui non misurano l'esperienza effettiva degli utenti.
* Le [metriche del browser][client], soprattutto i tempi di caricamento delle pagine del browser, che sono ideali per le applicazioni Web. Se la pagina presenta molti script, è opportuno fare attenzione alle eccezioni del browser. Per ottenere queste metriche e avvisi, è necessario configurare il [monitoraggio delle pagine Web][client].
* Il tempo di risposta del server e le richieste non riuscite per il lato server delle applicazioni Web. Oltre a impostare gli avvisi, prestare attenzione a queste metriche per vedere se variano in modo sproporzionato con frequenza delle richieste elevata: questa situazione potrebbe indicare che l'app sta esaurendo le risorse.



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md

<!---HONumber=58--> 