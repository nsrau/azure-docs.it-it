<properties title="Application Insights" pageTitle="Application Insights: monitoraggio dell'integrità e dell'utilizzo di un'app" description="Analizzare l'uso, la disponibilità e le prestazioni dell'applicazione locale o Web di Microsoft Azure con Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />

# Application Insights: monitoraggio dell'integrità e dell'utilizzo di un'app

*Application Insights è disponibile in anteprima.*

Application Insights consente di monitorare un'applicazione live in base a:

* **Disponibilità**: esecuzione di test automatici degli URL ogni pochi minuti da tutto il mondo.
* **Prestazioni**: rilevamento e diagnostica dei problemi e delle eccezioni relativi alle prestazioni.
* **Utilizzo**: possibilità di sapere cosa fanno gli utenti con l'app, in modo da poterla migliorare.

La configurazione è molto semplice e i risultati saranno visibili in pochi minuti. Al momento le app Web ASP.NET sono supportate (sui propri server o su Azure).


## Introduzione

Iniziare con qualsiasi combinazione, in qualsiasi ordine, dei punti di ingresso a sinistra di questo grafico. Scegliere le route in base alle proprie esigenze. Se si sta sviluppando un'app Web ASP.NET, iniziare con l'aggiunta di Application Insights al progetto Web; gli altri bit potranno essere aggiunti facilmente in un secondo momento.

È necessario un account [Microsoft Azure](http://azure.com) (salvo il caso in cui si disponga della versione VSO).

<table >
<tr valign="top"><th>Elementi necessari</th><th colspan="2">Operazione da eseguire</th><th>Risultato</th></tr>
<tr valign="top"><td>Ottenere analisi relative alle prestazioni e all'utilizzo per l'app ASP.NET app</td><td colspan="2"><a href="../app-insights-start-monitoring-app-health-usage/">Aggiungere Application Insights al progetto Web project</a></td><td>Metriche delle prestazioni: conteggi di caricamenti, tempi di risposta, ...</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Inviare eventi e parametri dal codice del server</a></td><td>Personalizzare le analisi business</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">Inviare dati di telemetria relativi a traccia ed eccezioni dal server o acquisire dati di log di terze parti.</td><td>Diagnostica delle app del server. Dati dei log relativi a ricerca e filtri.</a></td></tr>
<tr valign="top"><td>Ottenere analisi di utilizzo dalle pagine Web (su qualsiasi piattaforma)</td><td colspan="2"><a href="../app-insights-web-track-usage/">Inserire lo script AI nelle pagine Web</a></td><td>Analisi di utilizzo: visualizzazioni pagine, utenti ricorrenti, conteggi sessioni</td></tr>
<tr valign="top"><td></td><td>&nbsp;&nbsp;</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Scrivere chiamate relative a eventi e metriche negli script delle pagine Web</a></td><td>Analisi dell'esperienza utente personalizzata</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">Scrivere chiamate relative a traccia e diagnostica negli script delle pagine Web</a></td><td>Dati dei log relativi a ricerca e filtri.</td></tr>
<tr valign="top"><td>Eseguire la diagnosi dei problemi in un'app ASP.NET già in esecuzione sul server Web</td><td colspan="2"><a href="../app-insights-monitor-performance-live-website-now/">Installare l'applicazione di monitoraggio dello stato sul server Web</a></td><td>Durate e conteggi delle chiamate relative alle dipendenze; CPU, contatori di memoria e rete; conteggi di caricamenti, tempi di risposta</td></tr>
<tr valign="top"><td>Monitorare la disponibilità di qualsiasi pagina Web</td><td colspan="2"><a href="../app-insights-monitor-web-app-availability/">Configurare i test Web in Application Insights</a></td><td>Monitoraggio e avvisi relativi alla disponibilità</td></tr>
<tr valign="top"><td>Ottenere analisi relative alle prestazioni e all'utilizzo per le app Windows Phone, le app Windows Store o i siti Web Java</td><td colspan="2"><a href="http://msdn.microsoft.com/library/dn481095.aspx">Per il momento, usare la precedente versione VSO di Application Insights</a></td><td>Analisi relative all'utilizzo e alle prestazioni. <a href="http://msdn.microsoft.com/library/dn793604.aspx">Nella versione per Azure vengono introdotte gradualmente altre funzionalità.</a></td></tr>
</table>


## <a name="video"></a>Video

#### Introduzione

> [AZURE.VIDEO application-insights-introduction]

#### Introduzione

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]


