---
title: Che cos'è Azure Application Insights? | Microsoft Docs
description: Gestione delle prestazioni delle applicazioni e monitoraggio dell'uso dell'applicazione Web live.  Rilevare, valutare e diagnosticare i problemi e comprendere la modalità di uso dell'app da parte degli utenti.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 379721d1-0f82-445a-b416-45b94cb969ec
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: overview
ms.custom: mvc
ms.date: 06/03/2019
ms.author: mbullwin
ms.openlocfilehash: 5e42c36e6a321775e6017a2318857e20b7dcaf0d
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876470"
---
# <a name="what-is-application-insights"></a>Informazioni su Azure Application Insights
Application Insights è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme, che consente di monitorare un'applicazione Web live. Il servizio rileva automaticamente le anomalie nelle prestazioni e include avanzati strumenti di analisi che consentono di diagnosticare i problemi e conoscere come viene effettivamente usata l'app dagli utenti.  Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità. Funziona per le app su un'ampia gamma di piattaforme, tra cui .NET, Node.js e Java EE, ospitate in locale, ibride o in un cloud pubblico. Si integra con il processo DevOps e offre punti di connessione per diversi altri strumenti di sviluppo. Può monitorare e analizzare i dati di telemetria da app per dispositivi mobili grazie all'integrazione con Visual Studio App Center.

## <a name="how-does-application-insights-work"></a>Come funziona Application Insights?
Si installa un piccolo pacchetto di strumentazione nell'applicazione e si configura una risorsa di Application Insights nel portale di Microsoft Azure. La strumentazione monitora l'app e invia i dati di telemetria al portale. L'applicazione può essere eseguita ovunque, non deve necessariamente essere ospitata in Azure.

È possibile instrumentare non solo l'applicazione di servizio web, ma anche i componenti in background e il codice JavaScript nelle pagine web stesse. 

![L'instrumentazione di Application Insights nell'app invia la telemetria alla risorsa di Application Insights.](./media/app-insights-overview/01-scheme.png)

Inoltre, è possibile effettuare il pull dei dati di telemetria dagli ambienti host, ad esempio i contatori delle prestazioni, la diagnostica di Azure o i log di Docker. È possibile configurare test web che inviano periodicamente richieste sintetiche al servizio web.

Tutti i flussi di dati di telemetria sono integrati nel portale di Azure, dove è possibile applicare potenti strumenti di analisi e ricerca ai dati non elaborati.


### <a name="whats-the-overhead"></a>Informazioni sul sovraccarico
L'impatto sulle prestazioni dell'app è molto ridotto. Le chiamate di rilevamento non sono bloccanti e vengono riunite in batch e inviate in un thread separato.

## <a name="what-does-application-insights-monitor"></a>Quali sono gli elementi monitorati da Application Insights?

Application Insights è destinato al team di sviluppo, a cui consente di comprendere le prestazioni e le modalità d'uso dell'app. Esegue il monitoraggio di:

* **Frequenza delle richieste, tempi di risposta e percentuali di errore**: trovare le pagine più visitate, gli orari di visita e la posizione degli utenti. Vedere quali pagine abbiano prestazioni migliori. Se i tempi di risposta e le percentuali di errore aumentano di pari passo con le richieste, è probabile che ci sia un problema di assegnazione delle risorse. 
* **Tassi di dipendenza, tempi di risposta e percentuali di errore**: trovare quali servizi esterni causino un rallentamento.
* **Eccezioni**: analizzare le statistiche aggregate o selezionare istanze specifiche e approfondire l'analisi dello stack e le richieste correlate. Vengono segnalate eccezioni di server e browser.
* **Visualizzazioni pagina e prestazioni di carico**, segnalate dai browser degli utenti.
* **Chiamate AJAX** dalle pagine Web: tassi, tempi di risposta e percentuali di errore.
* **Numeri di utenti e sessioni**.
* **Contatori delle prestazioni** dai computer server Windows o Linux, ad esempio l'uso di CPU, memoria e rete. 
* **Diagnostica dell'host** da Docker o Azure. 
* **Log di traccia di diagnostica** dall'app, in modo da poter correlare gli eventi di traccia con le richieste.
* **Eventi e metriche personalizzati** scritti dall'utente stesso nel codice del client o del server per tracciare eventi aziendali come gli articoli venduti o le partite vinte.

## <a name="where-do-i-see-my-telemetry"></a>Dove è possibile visualizzare i dati di telemetria?

È possibile esaminare i dati in numerosi modi. Vedere i seguenti articoli:

|  |  |
| --- | --- |
| [**Rilevamento intelligente e avvisi manuali**](../../azure-monitor/app/proactive-diagnostics.md)<br/>Gli avvisi automatici si adattano ai modelli normali di telemetria dell'app e si attivano quando i dati si discostano dal modello consueto. È anche possibile [impostare avvisi](../../azure-monitor/app/alerts.md) per determinati livelli di metriche standard o personalizzate. |![Esempio di avviso](./media/app-insights-overview/alerts-tn.png) |
| [**Mappa delle applicazioni**](../../azure-monitor/app/app-map.md)<br/>I componenti dell'applicazione, con le metriche e gli avvisi chiave. |![Mappa delle applicazioni](./media/app-insights-overview/appmap-tn.png)  |
| [**Profiler**](../../azure-monitor/app/profiler.md)<br/>Ispezionare i profili di esecuzione delle richieste campionate. |![Profiler](./media/app-insights-overview/profiler.png) |
| [**Analisi dell'utilizzo**](../../azure-monitor/app/usage-overview.md)<br/>Analizzare la segmentazione e la conservazione degli utenti.|![Strumento Conservazione](./media/app-insights-overview/retention.png) |
| [**Ricerca diagnostica dei dati dell'istanza**](../../azure-monitor/app/diagnostic-search.md)<br/>Cercare e filtrare eventi come richieste, eccezioni, chiamate a dipendenze, tracce di log e visualizzazioni di pagina.  |![Ricerca nella telemetria](./media/app-insights-overview/search-tn.png) |
| [**Esplora metriche per i dati aggregati**](../../azure-monitor/app/metrics-explorer.md)<br/>Esaminare, filtrare e segmentare dati aggregati come frequenza delle richieste, errori, eccezioni, tempi di risposta e tempi di caricamento delle pagine. |![Metriche](./media/app-insights-overview/metrics-tn.png) |
| [**Dashboard**](../../azure-monitor/app/overview-dashboard.md)<br/>Combinare dati di più risorse e condividerli con altri utenti. Ideale per le applicazioni multi-componente e per la visualizzazione continua negli spazi del team. |![Esempio di dashboard](./media/app-insights-overview/dashboard-tn.png) |
| [**Flusso di metriche in tempo reale**](../../azure-monitor/app/live-stream.md)<br/>Quando si distribuisce una nuova build, controllare questi indicatori delle prestazioni in tempo quasi reale per verificare che tutto funzioni come previsto. |![Esempio di metriche live](./media/app-insights-overview/live-metrics-tn.png) |
| [**Analisi**](../../azure-monitor/app/analytics.md)<br/>Questo avanzato linguaggio di query consente di trovare risposta a domande approfondite sull'utilizzo e sulle prestazioni dell'app. |![Esempio di analisi](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](../../azure-monitor/app/visual-studio.md)<br/>Vedere i dati sulle prestazioni nel codice. Passare al codice dall'analisi dello stack.|![Visual Studio](./media/app-insights-overview/visual-studio-tn.png) |
| [**Debugger di snapshot**](../../azure-monitor/app/snapshot-debugger.md)<br/>Eseguire il debug di snapshot campionati da operazioni attive, con i valori dei parametri.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](../../azure-monitor/app/export-power-bi.md )<br/>Integrare le metriche di uso con altra business intelligence.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**API REST**](https://dev.applicationinsights.io/)<br/>Scrivere codice per eseguire query su metriche e dati non elaborati.| ![API REST](./media/app-insights-overview/rest-tn.png) |
| [**Esportazione continua**](../../azure-monitor/app/export-telemetry.md)<br/>Eseguire l'esportazione bulk dei dati non elaborati nell'archivio non appena arrivano. |![Esportazione](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Come si usa Application Insights?

### <a name="monitor"></a>Monitorare
Installare Application Insights nell'app, impostare i [test Web di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) e:

* Vedere il [dashboard dell'applicazione](../../azure-monitor/app/overview-dashboard.md) predefinito per la chat team, in modo da controllare carico, velocità di risposta e prestazioni delle dipendenze, caricamenti di pagina e chiamate AJAX.
* Scoprire quali sono le richieste più lente e con un maggior numero di errori.
* Guardare [Live Stream](../../azure-monitor/app/live-stream.md) quando si distribuisce una nuova versione, in modo da rendersi immediatamente conto di eventuali riduzioni delle prestazioni.

### <a name="detect-diagnose"></a>Rilevare, eseguire una diagnosi
Quando si riceve un avviso o si individua un problema:

* Individuare il numero di utenti interessati.
* Correlare gli errori con eccezioni, chiamate alle dipendenze e tracce.
* Esaminare profiler, snapshot, dump dello stack e log di traccia.

### <a name="build-measure-learn"></a>Compilare, misurare, acquisire informazioni
[Misurare l'efficacia](../../azure-monitor/app/usage-overview.md) di ogni nuova funzionalità che si distribuisce.

* Pianificare la misurazione del modo in cui i clienti usano nuove funzionalità aziendali o per l'esperienza utente.
* Scrivere dati di telemetria personalizzati nel codice.
* Basare il ciclo di sviluppo successivo sulle salde prove derivate dai dati di telemetria.

## <a name="get-started"></a>Attività iniziali
Application Insights è uno dei molti servizi ospitati in Microsoft Azure e riceve i dati di telemetria per l’analisi e la presentazione. Prima di eseguire qualsiasi altra operazione, è necessario ottenere una sottoscrizione a [Microsoft Azure](https://azure.com). L'iscrizione è gratuita e, se si sceglie il [piano tariffario](https://azure.microsoft.com/pricing/details/application-insights/) Basic di Application Insights, la sottoscrizione resta gratuita finché l'applicazione non cresce fino a un uso sostanziale. Se l'organizzazione ha già una sottoscrizione, può aggiungervi l'account Microsoft dell'utente.

È possibile iniziare in diversi modi. Iniziare con il metodo più adatto alle proprie esigenze. È possibile aggiungere gli altri in un secondo momento.

* **In fase di esecuzione, instrumentare l'app Web sul server.** Ideale per le applicazioni già distribuite. È così possibile evitare qualsiasi aggiornamento del codice.
  * [**Applicazioni ASP.NET o ASP.NET Core ospitate in App Web di Azure**](../../azure-monitor/app/azure-web-apps.md)
  * [**Applicazioni ASP.NET ospitate in IIS in macchine virtuali di Azure o set di scalabilità di macchine virtuali di Azure**](../../azure-monitor/app/azure-vm-vmss-apps.md)
  * [**Applicazioni ASP.NET ospitate in IIS in macchine virtuali locali**](../../azure-monitor/app/monitor-performance-live-website-now.md)
* **In fase di sviluppo, aggiungere Application Insights al codice.** Consente di personalizzare la raccolta dei dati di telemetria e inviare dati di telemetria aggiuntivi.
  * [Applicazioni ASP.NET](../../azure-monitor/app/asp-net.md)
  * [Applicazioni ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
  * [Applicazioni console .NET](../../azure-monitor/app/console.md)
  * [Java](../../azure-monitor/app/java-get-started.md)
  * [Node.js](../../azure-monitor/app/nodejs.md)
  * [Altre piattaforme](../../azure-monitor/app/platforms.md)
* **[Instrumentare le pagine Web](../../azure-monitor/app/javascript.md)** per visualizzazione pagina, AJAX e altri dati di telemetria sul lato client.
* **[Analizzare l'uso di app per dispositivi mobili](../../azure-monitor/learn/mobile-center-quickstart.md)** tramite l'integrazione con Visual Studio App Center.
* **[Test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)** : effettuare regolarmente il ping del sito Web dai server.

## <a name="next-steps"></a>Passaggi successivi
Introduzione in fase di esecuzione a:

* [App ospitate in IIS in macchine virtuali di Azure e set di scalabilità di macchine virtuali di Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Server IIS](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [App Web di Azure](../../azure-monitor/app/azure-web-apps.md)

Introduzione in fase di sviluppo a:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="support-and-feedback"></a>Supporto, commenti e suggerimenti
* Domande e problemi:
  * [Risoluzione dei problemi][qna]
  * [Forum MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* Suggerimenti:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blog:
  * [Blog di Application Insights](https://azure.microsoft.com/blog/tag/application-insights)

## <a name="videos"></a>Video

- Video esterno: [configurazione di Application Insights con un'applicazione ASP.NET](https://www.youtube.com/watch?v=blnGAVgMAfA).
- Video esterno: [configurazione di Application Insights con ASP.NET Core e Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t).
- Video esterno: [configurazione di Application Insights con ASP.NET Core e Visual Studio Code](https://youtu.be/ygGt84GDync).

<!--Link references-->

[android]: ../../azure-monitor/learn/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ../../azure-monitor/app/javascript.md
[desktop]: ../../azure-monitor/app/windows-desktop.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[ios]: ../../azure-monitor/learn/mobile-center-quickstart.md
[java]: ../../azure-monitor/app/java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ../../azure-monitor/app/platforms.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
