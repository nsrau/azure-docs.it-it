---
title: Domande frequenti su Azure Application Insights | Microsoft Docs
description: Domande frequenti su Application Insights.
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 191d9e1197c3526d2f72b972b7fada76dee84447
ms.lasthandoff: 04/13/2017


---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: domande frequenti

## <a name="configuration-problems"></a>Problemi di configurazione
*Problemi nella configurazione di:*

* [App .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Monitoraggio di un'applicazione già in esecuzione](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
* [Diagnostica di Azure](app-insights-azure-diagnostics.md)
* [App Web Java](app-insights-java-troubleshoot.md)

*Non sono disponibili dati dal server*

* [Impostare le eccezioni del firewall](app-insights-ip-addresses.md)
* [Configurare un server ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Configurare un server Java](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Si può usare Application Insights con ...?

* [App Web in un server IIS locale o in una macchina virtuale](app-insights-asp-net.md)
* [App Web Java](app-insights-java-get-started.md)
* [App Node.js](app-insights-nodejs.md)
* [App Web in Azure](app-insights-azure-web-apps.md)
* [Servizi cloud in Azure](app-insights-cloudservices.md)
* [Server app eseguiti in Docker](app-insights-docker.md)
* [App Web a pagina singola](app-insights-javascript.md)
* [SharePoint](app-insights-sharepoint.md)
* [App desktop Windows](app-insights-windows-desktop.md)
* [Altre piattaforme](app-insights-platforms.md)

## <a name="is-it-free"></a>È gratuito?

Sì, per l'uso sperimentale. Nel piano tariffario Basic l'applicazione può inviare una determinata quantità di dati gratuitamente ogni mese. La capacità massima disponibile è sufficiente per lo sviluppo e la pubblicazione di un'app per un numero ridotto di utenti. È possibile impostare un limite per evitare l'elaborazione di una quantità di dati maggiore a quella specificata.

I volumi più grandi di telemetria vengono addebitati per GB. Vedere alcuni suggerimenti su come [limitare gli addebiti](app-insights-pricing.md).

Il piano Enterprise comporta un costo per ogni giorno in cui i singoli nodi del server Web inviano dati di telemetria. È appropriato se si vuole usare un'esportazione continua su larga scala.

[Leggere il piano tariffario](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>Quanto costa?

* Aprire la pagina **Funzionalità + prezzi** in una risorsa di Application Insights. È incluso un grafico dell'utilizzo recente. Se si vuole, è possibile impostare un limite di volume di dati.
* Aprire il [pannello Fatturazione di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) per visualizzare i costi di tutte le risorse.

## <a name="q14"></a>Quali modifiche apporta Application Insights al progetto?
Dipende dal tipo di progetto. Per un'applicazione Web:

* Aggiunge tre file al progetto:

  * ApplicationInsights.config.
  * ai.js
* Installa i pacchetti NuGet seguenti:

  * *Application Insights API* , ovvero l'API principale
  * *Application Insights API for Web Applications* , che consente di inviare i dati di telemetria dal server
  * *Application Insights API for JavaScript Applications* , che consente di inviare i dati di telemetria dal client

    I pacchetti includono gli assembly seguenti:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Inserire elementi in:

  * Web.config
  * packages.config
* (Solo per i muovi progetti. Se si [aggiunge Application Insights a un progetto esistente][start], l'operazione deve essere eseguita manualmente.) Inserisce frammenti nel codice client e server per inizializzarli con l'ID risorsa di Application Insights. Ad esempio, in un'app MVC il codice viene inserito nella pagina master Views/Shared/_Layout.cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>In che modo è possibile effettuare l'aggiornamento da versioni dell'SDK meno recenti?
Vedere le [note sulla versione](app-insights-release-notes.md) dell'SDK appropriato per il tipo di applicazione.

## <a name="update"></a>In che modo è possibile modificare la risorsa di Azure che riceve i dati del progetto?
In Esplora soluzioni fare clic con il pulsante destro del mouse su `ApplicationInsights.config` e scegliere **Aggiorna Application Insights**. È possibile inviare i dati a una risorsa nuova o esistente in Azure. L'aggiornamento guidato modifica la chiave di strumentazione in ApplicationInsights.config, che determina la destinazione dei dati inviati dall'SDK del server. A meno che non venga deselezionata l'opzione "Aggiorna tutto", modificherà anche la chiave in cui appare nelle pagine Web.

## <a name="what-is-status-monitor"></a>Che cos'è Status Monitor?

Un'app desktop che è possibile usare nel server Web IIS per configurare Application Insights nelle app Web. Non raccoglie dati di telemetria: è possibile interromperlo se non si sta configurando un'app. 

[Altre informazioni](app-insights-monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Quali dati di telemetria vengono raccolti da Application Insights?

Da app Web del server:

* Richieste HTTP
* [Dipendenze](app-insights-asp-net-dependencies.md). Chiamate a: database SQL, chiamate HTTP a servizi esterni, Azure DocumentDB, tabelle, archiviazione BLOB e code. 
* [Eccezioni](app-insights-asp-net-exceptions.md) e analisi dello stack.
* [Contatori delle prestazioni](app-insights-performance-counters.md): se si usa [Status Monitor](app-insights-monitor-performance-live-website-now.md), il monitoraggio di Azure (app-insights-azure-web-apps.md) o il [writer collectd di Application Insights](app-insights-java-collectd.md).
* [Metrica ed eventi personalizzati](app-insights-api-custom-events-metrics.md) codificati.
* [Log di traccia](app-insights-asp-net-trace-logs.md) se si configura l'agente di raccolta appropriato.

Da [pagine Web dei client](app-insights-javascript.md):

* [Conteggi delle visualizzazioni pagina](app-insights-web-track-usage.md)
* [Chiamate AJAX](app-insights-asp-net-dependencies.md): richieste effettuate da uno script in esecuzione.
* Dati di carico della visualizzazione pagina
* Conteggi di utenti e sessioni
* [ID utente autenticato](app-insights-api-custom-events-metrics.md#authenticated-users)

Da altre origini, se sono configurate:

* [Diagnostica di Azure](app-insights-azure-diagnostics.md)
* [Contenitori Docker](app-insights-docker.md)
* [Tabelle di importazione per Analytics](app-insights-analytics-import.md)
* [OMS (Log Analytics)](https://azure.microsoft.com/blog/omssolutionforappinsightspublicpreview/)
* [Logstash](app-insights-analytics-import.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>È possibile filtrare o modificare alcuni dati di telemetria?

Sì, nel server è possibile scrivere:

* Un processore di telemetria per filtrare o aggiungere proprietà agli elementi di telemetria selezionati prima che vengano inviati dall'app.
* Un inizializzatore di telemetria per aggiungere proprietà a tutti gli elementi della telemetria.

Vedere altre informazioni per [ASP.NET](app-insights-api-filtering-sampling.md) o [Java](app-insights-java-filter-telemetry.md).

## <a name="how-are-city-country-and-other-geo-location-data-calculated"></a>Come vengono calcolati i dati su città, paesi e altre aree geografiche?

Viene cercato l'indirizzo IP (IPv4 o IPv6) del client Web tramite [GeoLite2](http://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria del browser: vengono raccolti gli indirizzi IP del mittente.
* Telemetria del server: il modulo di Application Insights raccoglie l'indirizzo IP del client. L'indirizzo non viene raccolto se è impostato `X-Forwarded-For`.

È possibile configurare `ClientIpHeaderTelemetryInitializer` per ottenere l'indirizzo IP da un'intestazione diversa. Ad esempio, in alcuni sistemi viene spostato da un proxy, da un bilanciamento del carico o da una rete CDN a `X-Originating-IP`. [Altre informazioni](http://apmtips.com/blog/2016/07/05/client-ip-address/).

È possibile [usare Power BI](app-insights-export-power-bi.md) per visualizzare i dati di telemetria della richiesta in una mappa.


## <a name="data"></a>Per quanto tempo vengono conservati i dati nel portale? Tale conservazione è sicura?
Vedere l'argomento relativo a [conservazione dei dati e privacy][data].

## <a name="might-personally-identifiable-information-pii-be-sent-in-the-telemetry"></a>È possibile che vengano inviate informazioni personali (PII) nella telemetria?

Ciò si verifica se il codice invia tali dati. E può verificarsi anche se le variabili dell'analisi dello stack includono informazioni personali. Il team di sviluppo deve eseguire valutazioni dei rischi per garantire che le informazioni personali vengono gestite in maniera appropriata. Vedere [altre informazioni sulla conservazione e privacy dei dati](app-insights-data-retention-privacy.md).

Le ultime otto cifre dell'indirizzo Web del client vengono sempre impostate su 0 dopo l'inserimento dal portale.

## <a name="my-ikey-is-visible-in-my-web-page-source"></a>L'iKey dell'utente è visibile nell'origine della pagina Web. 

* Questo è una pratica comune nelle soluzioni di monitoraggio.
* Questo valore non può essere usato per appropriarsi dei dati personali.
* Potrebbe essere usato per alterare gli avvisi su dati o trigger.
* Nessun cliente ha segnalato tali problemi.

È possibile:

* Usare due valori iKey separati, ovvero due risorse separate di Application Insights, per i dati del client e del server. oppure
* Scrivere un proxy che viene eseguito nel server in modo che il client Web invii i dati tramite tale proxy.

## <a name="post"></a>Come visualizzare dati POST in Ricerca diagnostica?
I dati POST non vengono registrati automaticamente, ma è possibile usare una chiamata TrackTrace; inserire i dati nel parametro del messaggio, che ha limiti di dimensione più ampi rispetto alle proprietà della stringa, nonostante non sia possibile applicare filtri.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>È preferibile usare una o più risorse di Application Insights?

* [Vedere questo articolo](app-insights-separate-resources.md)
* [Esempio: servizio cloud con ruoli di lavoro e Web](app-insights-cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Come è possibile modificare dinamicamente la chiave di strumentazione?

* [Vedere questo articolo](app-insights-separate-resources.md)
* [Esempio: servizio cloud con ruoli di lavoro e Web](app-insights-cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Che cosa sono i conteggi utente e sessione?

* JavaScript SDK imposta un cookie utente nel client Web, per identificare gli utenti abituali, e un cookie di sessione per raggruppare le attività.
* Se non c'è uno script lato client, è possibile [impostare cookie per il server](http://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Se un utente reale usa il sito in browser diversi o esegue una navigazione privata o in incognito oppure usa computer diversi, viene inclusi più di una volta nei conteggi.
* Per identificare un utente connesso a più computer e browser, aggiungere una chiamata a [setAuthenticatedUserContect()](app-insights-api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a> In Application Insights sono state abilitate tutte le funzionalità?
| Elementi che dovrebbero essere visualizzati | Come ottenerli | Perché si vuole ottenerli |
| --- | --- | --- |
| Grafici di disponibilità |[Test Web](app-insights-monitor-web-app-availability.md) |Stabilire se l'app Web è attiva |
| Prestazioni dell'app server: tempi di risposta, ... |[Aggiungere Application Insights al progetto](app-insights-asp-net.md) o [installare Status Monitor di Application Insights nel server](app-insights-monitor-performance-live-website-now.md) (o scrivere il codice per [tenere traccia delle dipendenze](app-insights-api-custom-events-metrics.md#trackdependency)) |Rilevare i problemi di prestazioni |
| Telemetria di dipendenza |[Installare Status Monitor di Application Insights nel server](app-insights-monitor-performance-live-website-now.md) |Diagnosticare i problemi relativi a database o altri componenti esterni |
| Ricavare analisi dello stack dalle eccezioni |[Inserire chiamate TrackException nel codice](app-insights-asp-net-exceptions.md) (ma alcune sono segnalate automaticamente) |Rilevare e diagnosticare le eccezioni |
| Eseguire la ricerca di tracce dei log |[Aggiungere un adattatore di registrazione](app-insights-asp-net-trace-logs.md) |Diagnosticare le eccezioni, problemi di prestazioni |
| Nozioni di base dell'utilizzo del client: visualizzazioni pagina, sessioni, ... |[Inizializzatore JavaScript nelle pagine Web](app-insights-javascript.md) |Analisi dell'utilizzo |
| Metriche personalizzate client |[Rilevamento delle chiamate nelle pagine Web](app-insights-api-custom-events-metrics.md) |Migliorare l'esperienza utente |
| Metriche personalizzate server |[Rilevamento delle chiamate nel server](app-insights-api-custom-events-metrics.md) |Business intelligence |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Perché i conteggi nei grafici di ricerca e di metrica non sono uguali?

Il [campionamento](app-insights-sampling.md) riduce il numero di elementi di telemetria (richieste, eventi personalizzati e così via) inviati effettivamente dall'app al portale. Nel grafico di ricerca, viene visualizzato il numero di elementi effettivamente ricevuti. Nei grafici di metrica che visualizzano un conteggio degli eventi, è possibile vedere il numero di eventi originali che si sono verificati. 

Ogni elemento trasmesso include una proprietà `itemCount` che visualizza quanti eventi originali rappresenta tale elemento. Per osservare un campionamento in esecuzione, è possibile eseguire questa query in Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automazione

### <a name="configuring-application-insights"></a>Configurazione di Application Insights

È possibile [scrivere script di PowerShell](app-insights-powershell.md) tramite Monitoraggio risorse di Azure per:

* Creare e aggiornare risorse di Application Insights.
* Impostare il piano tariffario.
* Ottenere la chiave di strumentazione.
* Aggiungere un avviso di metrica.
* Aggiungere un test di disponibilità.

Non è possibile impostare un report di esplorazione delle metriche o impostare un'esportazione continua.

### <a name="querying-the-telemetry"></a>Esecuzione di query sui dati di telemetria

Usare l'[API REST](https://dev.applicationinsights.io/) per eseguire le query di [Analytics](app-insights-analytics.md).

## <a name="how-can-i-set-an-alert-on-an-event"></a>Come è possibile impostare un avviso per un evento?

Gli avvisi di Azure si applicano solo alle metriche. Creare una metrica personalizzata che supera una soglia di valori ogni volta che si verifica l'evento. Impostare quindi un avviso per la metrica. Si noti che verrà inviata una notifica ogni volta che la metrica supera la soglia in entrambe le direzioni. Non si riceverà una notifica fino al primo superamento, indipendentemente dal valore iniziale. C'è sempre una latenza di pochi minuti.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Ci sono costi per il trasferimento dati tra un'app Web di Azure e Application Insights?

* Se l'app Web di Azure è ospitata in un data center in cui è presente un endpoint di raccolta di Application Insights, non è previsto alcun addebito. 
* Se non c'è nessun endpoint nel data center host, la telemetria dell'app sarà soggetta agli [addebiti in uscita di Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Ciò non dipende da dove è ospitata la risorsa di Application Insights. Dipende solo dalla distribuzione degli endpoint.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>È possibile inviare dati di telemetria al portale di Application Insights?

È consigliabile usare gli SDK di Microsoft e usare l'API SDK (app-insights-api-custom-events-metrics.md). Sono disponibili varianti dell'SDK per diverse [piattaforme](app-insights-platforms.md). Gli SDK gestiscono bufferring, compressione, limitazione, tentativi e così via. Tuttavia, lo [schema di inserimento](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) e il [protocollo di endpoint](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) sono pubblici.

## <a name="can-i-monitor-an-intranet-web-server"></a>È possibile monitorare un server Web Intranet?

Di seguito vengono illustrati due metodi:

### <a name="firewall-door"></a>Porta firewall

Consentire al server Web di inviare dati di telemetria agli endpoint https://dc.services.visualstudio.com:443 e https://rt.services.visualstudio.com:443. 

### <a name="proxy"></a>Proxy

Instradare il traffico dal server a un gateway nella rete Intranet, impostando quanto segue in ApplicationInsights.config:

```XML
<TelemetryChannel>
    <EndpointAddress>your gateway endpoint</EndpointAddress>
</TelemetryChannel>
```

Il gateway deve indirizzare il traffico a https://dc.services.visualstudio.com:443/v2/track

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>È possibile eseguire test Web di disponibilità in un server Intranet?

I [test Web](app-insights-monitor-web-app-availability.md) vengono eseguiti in punti di presenza distribuiti in tutto il globo. Sono disponibili due soluzioni:

* Porta di firewall: consentire al server di ricevere richieste dall'[elenco esteso e modificabile degli agenti di test Web](app-insights-ip-addresses.md).
* Scrivere un codice personalizzato per inviare richieste periodiche al server dall'interno della rete Intranet. A tale scopo è anche possibile eseguire test Web di Visual Studio. Il tester può inviare i risultati ad Application Insights tramite l'API TrackAvailability().

## <a name="more-answers"></a>Altre risposte
* [Forum di Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

