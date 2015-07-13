<properties 
	pageTitle="Informazioni su Azure Application Insights" 
	description="Tenere traccia dell'utilizzo e delle prestazioni dell'applicazione Web live o del dispositivo. Rilevare, valutare e diagnosticare i problemi. Monitorare continuamente e migliorare l'uso con gli utenti." 
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
	ms.date="04/22/2015" 
	ms.author="awills"/>
 
# Informazioni su Azure Application Insights

Visual Studio Application Insights consente di tenere traccia delle prestazioni e dell'utilizzo dell'applicazione Web live o del dispositivo.

* [Rileva, valuta e diagnostica][detect] i problemi di prestazioni e li risolve prima che vengano individuati dalla maggior parte degli utenti.
 *  Avvisa sulle modifiche delle prestazioni o sugli arresti anomali.
 *  Metriche per diagnosticare problemi di prestazioni, ad esempio tempi di risposta, utilizzo della CPU, traccia delle dipendenze.
 *  Test di disponibilità per le app Web.
 *  Arresto anomalo del sistema, report e avvisi di eccezioni
 *  Ricerca di log di diagnostica avanzate (incluse le tracce del log dal framework di registrazione preferito).
* [Migliora costantemente l'applicazione][knowUsers] comprendendo l'uso che ne fanno gli utenti. 
 * Conteggi delle visualizzazioni pagina, utenti nuovi e abituali e altre analisi di utilizzo dei core
 * Tiene traccia di eventi personalizzati per valutare i modelli di utilizzo e la riuscita di ogni funzionalità.

## Come funziona?

Si installa un piccolo SDK nell'applicazione e si configura un account nel portale di Application Insights. SDK consente di monitorare l'app e invia i dati di telemetria al portale. Il portale consente di visualizzare grafici statistici e offre strumenti di ricerca avanzati che consentono di diagnosticare i problemi.

![Application Insights SDK nell'app invia dati di telemetria alla risorsa di Application Insights nel portale di Azure.](./media/app-insights-overview/01-scheme.png)

SDK include diversi moduli che raccolgono i dati di telemetria, ad esempio per contare gli utenti, le sessioni e le prestazioni. È anche possibile scrivere codice personalizzato per l'invio di dati di telemetria al portale. La telemetria personalizzata è particolarmente utile per tenere traccia delle storie utente: è possibile contare gli eventi, ad esempio, numero di clic sul pulsante, conseguimento di obiettivi specifici o errori dell'utente.

Per i server ASP.NET e le app Web di Azure, è anche possibile installare [Controllo status][redfield], che ha due usi. Consente di:

* Monitorare un'app Web senza doverla compilare o installare nuovamente.
* Tenere traccia delle chiamate a moduli dipendenti.

## Con quali tipi di applicazioni può essere usato?

Attualmente sono disponibili gli SDK per:

* App Web
 * [ASP.NET][greenbrown] in Azure o il server IIS
 * [Java][java] in ambiente JRE 
 * [Pagine Web][client]: HTML+JavaScript
* App per dispositivi
 * [Windows][windows]
 * [iOS][ios]
 * [Android][android]
 * Cordova
 * [Altre piattaforme][platforms]


## Per quali scopi usarlo?

* [Rilevare, valutare e diagnosticare i problemi][detect]
* [Analizzare l'utilizzo dell'app][knowUsers]


## Cosa è necessario per usarlo?

* Una sottoscrizione a Microsoft Azure. Application Insights è uno dei molti servizi cloud di Azure, che includono anche siti Web, database, macchine virtuali e altro ancora. È però possibile usare Application Insights per monitorare qualsiasi app, l'app non deve essere eseguita in Azure. 

 * L'organizzazione potrebbe avere un account.


## Come iniziare?

Scegliere la piattaforma dal menu Introduzione a sinistra.

In tutti i casi, la procedura di base è:

1. Creare una risorsa di Application Insights in [Azure][portal] (e ottenere la chiave di strumentazione).
2. Instrumentare l'applicazione con l'SDK appropriato (e configurarlo con la chiave di strumentazione).
3. Eseguire l'applicazione in modalità di debug o live.
4. Visualizzare i risultati nella risorsa in [Azure][portal].

In alcuni casi un plug-in è disponibile per l'IDE (ad esempio Visual Studio o Eclipse) che esegue i primi due passaggi. Ma si può sempre seguire la procedura manualmente.

Se l'app è un sito Web o un servizio, esistono alcune aggiunte e variazioni facoltative nella procedura di base:

* Aggiungere un SDK per l'applicazione sul lato server e al [dispositivo][windows] o [pagina Web][client] del client. I dati di telemetria vengono uniti nel portale in modo da poter correlare gli eventi in due parti.
* Configurare i test Web per monitorare la disponibilità del sito ovunque nel mondo.
* Instrumentare un'applicazione sul lato server già live senza compilarla o distribuirla nuovamente. Questa opzione è disponibile per i [server IIS][redfield] e le [app Web di Azure][azure].
* Consente di monitorare le chiamate di dipendenza eseguite dall'app ad altri componenti, ad esempio database o tramite API REST. Disponibile per i [server IIS][redfield] e le [app Web di Azure][azure].


<!--Link references-->

[android]: app-insights-android.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[ios]: app-insights-ios.md
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=62-->