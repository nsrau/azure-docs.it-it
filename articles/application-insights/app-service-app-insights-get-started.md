<properties 
	pageTitle="Application Insights in Microsoft Azure" 
	description="Rilevare, valutare e diagnosticare problemi in un’applicazione web live o del dispositivo. Monitorare continuamente e migliorare l'uso con gli utenti." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/25/2015" 
	ms.author="awills"/>
 
# Visual Studio Application Insights

Application Insights è un servizio di analisi dei dati estendibile che consente di monitorare l'applicazione attiva. Consente di rilevare e diagnosticare problemi di prestazioni e comprendere ciò che gli utenti effettivamente fanno con l'applicazione. È stato progettato per aiutare gli sviluppatori a migliorare continuamente le prestazioni e la facilità di utilizzo dell'app.

![Creare grafici relativi alle statistiche sulle attività degli utenti o eseguire il drill-down in eventi specifici.](./media/app-service-app-insights-get-started/00-sample.png)

Può essere usato con app Web e autonome su una vasta gamma di piattaforme, ovvero .NET o J2EE, con hosting in locale o sul cloud.

Application Insights è destinato ai team di sviluppo. Consente di eseguire le operazioni seguenti:

* [Analizzare i modelli di utilizzo][knowUsers] per una migliore comprensione degli utenti e per il miglioramento continuo dell'app. 
 * Conteggi delle visualizzazioni pagina, utenti nuovi e abituali, georilevazione, piattaforme e altre statistiche di utilizzo principali.
 * Rilevamento dei percorsi di utilizzo per valutare la riuscita di ogni funzionalità.
* [Rileva, valuta e diagnostica][detect] i problemi di prestazioni e li risolve prima che vengano individuati dalla maggior parte degli utenti.
 *  Avvisa sulle modifiche delle prestazioni o sugli arresti anomali.
 *  Metriche per diagnosticare problemi di prestazioni, ad esempio tempi di risposta, utilizzo della CPU, traccia delle dipendenze.
 *  Test di disponibilità per le app Web.
 *  Arresto anomalo del sistema, report e avvisi di eccezioni
 *  Ricerca di log di diagnostica avanzate (incluse le tracce del log dal framework di registrazione preferito).

L'SDK per ogni piattaforma include una vasta gamma di moduli predefiniti per il monitoraggio immediato dell'app. È anche possibile codificare la telemetria personalizzata per ottenere analisi più dettagliate e specifiche.

I dati di telemetria raccolti dall'applicazione vengono archiviati e analizzati nel portale di Azure, in cui sono disponibili visualizzazioni intuitive e strumenti avanzati per diagnosi e analisi veloci.



Per un'analisi più approfondita [Esportare](app-insights-export-telemetry.md) i dati [in SQL](app-insights-code-sample-export-telemetry-sql-database.md), [Power BI](app-insights-export-power-bi.md) o in strumenti personalizzati.

![Visualizzare dati in Power BI](./media/app-service-app-insights-get-started/210.png)

## Piattaforme e linguaggi

Sono disponibili SDK per una gamma crescente di piattaforme. Attualmente l'elenco include:

 * [Server ASP.NET][greenbrown] in Azure o nel server IIS
 * [Servizi cloud di Azure](app-insights-cloudservices.md)
 * [Server J2EE][java]
 * [Pagine Web][client]\: HTML+JavaScript
 * [Servizi Windows, ruoli di lavoro e app desktop][desktop]
 * [Altre piattaforme][platforms]\: Node.js, PHP, Python, Ruby, Joomla, SharePoint, WordPress

Application Insights può ottenere dati di telemetria anche dalle app Web ASP.NET esistenti su IIS, senza ricompilarle.

Se l'app include componenti client, server e di altro tipo, sarà possibile instrumentarli tutti. I dati verranno integrati nel portale di Application Insights, in modo che, ad esempio, sia possibile correlare eventi a livello client con eventi a livello server.


## Funzionamento

Si installa un piccolo SDK nell'applicazione e si configura un account nel portale di Application Insights. SDK consente di monitorare l'app e invia i dati di telemetria al portale. Il portale consente di visualizzare grafici statistici e offre strumenti di ricerca avanzati che consentono di diagnosticare i problemi.

![Application Insights SDK nell'app invia dati di telemetria alla risorsa di Application Insights nel portale di Azure.](./media/app-service-app-insights-get-started/01-scheme.png)

SDK include diversi moduli che raccolgono i dati di telemetria, ad esempio per contare gli utenti, le sessioni e le prestazioni. È anche possibile scrivere codice personalizzato per l'invio di dati di telemetria al portale. La telemetria personalizzata è particolarmente utile per tenere traccia delle storie utente: è possibile contare gli eventi, ad esempio, numero di clic sul pulsante, conseguimento di obiettivi specifici o errori dell'utente.

Per i server ASP.NET e le app Web di Azure, è anche possibile installare [Controllo status][redfield], che ha due usi. Consente di:

* Monitorare un'app Web senza doverla compilare o installare nuovamente.
* Tenere traccia delle chiamate a moduli dipendenti.



### Informazioni sul sovraccarico

L'impatto sulle prestazioni è molto ridotto. Le chiamate di rilevamento non sono bloccanti e vengono riunite in batch e inviate in un thread separato.



## Attività iniziali

1. È necessaria una sottoscrizione a [Microsoft Azure](http://azure.com). È possibile crearla gratuitamente e quindi scegliere il [piano tariffario](https://azure.microsoft.com/pricing/details/application-insights/) gratuito di Application Insights.

2. Se si utilizza Visual Studio:

 * Nuovo progetto: controllare la casella **Aggiungi Application Insights**.
 * Progetto esistente: Fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi Application Insights**.

Se non si utilizza Visual Studio o se tali opzioni non sono disponibili per il progetto, [cercare qui la propria piattaforma](app-insights-platforms.md).

3. Eseguire l'app (in modalità di sviluppo, o pubblicarla) e controllare i dati inseriti nel [portale di Azure](https://portal.azure.com).

## Codice


[Esempi e procedure dettagliate](app-insights-code-samples.md)

[Laboratori SDK](https://www.myget.org/gallery/applicationinsights-sdk-labs) - pacchetti NuGet è possibile installare (e disinstallare) come aggiunte Application Insights SDK. Provarli e inviare i commenti!


## Supporto, commenti e suggerimenti

* Domande e problemi:
 * [Risoluzione dei problemi][qna]
 * [Forum di MSDN](https://social.msdn.microsoft.com/Forums/vstudio/it-IT/home?forum=ApplicationInsights)
 * [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* Bug:
 * [Connessione](https://connect.microsoft.com/VisualStudio/Feedback/LoadSubmitFeedbackForm?FormID=6076)
* Suggerimenti:
 * [Suggerimenti dell'utente](http://visualstudio.uservoice.com/forums/121579-visual-studio/category/77108-application-insights)


## Video


> [AZURE.VIDEO 218]

> [AZURE.VIDEO usage-monitoring-application-insights]

> [AZURE.VIDEO performance-monitoring-application-insights]


<!--Link references-->

[android]: https://github.com/Microsoft/ApplicationInsights-Android
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-asp-net.md
[ios]: https://github.com/Microsoft/ApplicationInsights-iOS
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_1203_2015-->