---
title: Domande frequenti su Azure Application Insights | Microsoft Docs
description: Domande frequenti su Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: mbullwin
ms.openlocfilehash: ab1327b42a76a6e76183d84cb1750cce8b85228f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65604270"
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: Domande frequenti

## <a name="configuration-problems"></a>Problemi di configurazione
*Problemi nella configurazione di:*

* [App .NET](asp-net-troubleshoot-no-data.md)
* [Monitoraggio di un'applicazione già in esecuzione](monitor-performance-live-website-now.md#troubleshoot)
* [Diagnostica di Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [App Web Java](java-troubleshoot.md)

*Non sono disponibili dati dal server*

* [Impostare le eccezioni del firewall](ip-addresses.md)
* [Configurare un server ASP.NET](monitor-performance-live-website-now.md)
* [Configurare un server Java](java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Si può usare Application Insights con ...?

* [App Web in un server IIS locale o in una macchina virtuale](asp-net.md)
* [App Web Java](java-get-started.md)
* [App Node.js](nodejs.md)
* [App Web in Azure](azure-web-apps.md)
* [Servizi cloud in Azure](cloudservices.md)
* [Server app eseguiti in Docker](docker.md)
* [App Web a pagina singola](javascript.md)
* [SharePoint](sharepoint.md)
* [App desktop Windows](windows-desktop.md)
* [Altre piattaforme](platforms.md)

## <a name="is-it-free"></a>È gratuito?

Sì, per l'uso sperimentale. Nel piano tariffario Basic l'applicazione può inviare una determinata quantità di dati gratuitamente ogni mese. La capacità massima disponibile è sufficiente per lo sviluppo e la pubblicazione di un'app per un numero ridotto di utenti. È possibile impostare un limite per evitare l'elaborazione di una quantità di dati maggiore a quella specificata.

I volumi più grandi di telemetria vengono addebitati per GB. Vedere alcuni suggerimenti su come [limitare gli addebiti](pricing.md).

Il piano Enterprise comporta un costo per ogni giorno in cui i singoli nodi del server Web inviano dati di telemetria. È appropriato se si vuole usare un'esportazione continua su larga scala.

[Leggere il piano tariffario](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>Quanto costa?

* Aprire la pagina **Utilizzo e costi stimati** in una risorsa di Application Insights. È incluso un grafico dell'utilizzo recente. Se si vuole, è possibile impostare un limite di volume di dati.
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
Vedere le [note sulla versione](release-notes.md) dell'SDK appropriato per il tipo di applicazione.

## <a name="update"></a>In che modo è possibile modificare la risorsa di Azure che riceve i dati del progetto?
In Esplora soluzioni fare clic con il pulsante destro del mouse su `ApplicationInsights.config` e scegliere **Aggiorna Application Insights**. È possibile inviare i dati a una risorsa nuova o esistente in Azure. L'aggiornamento guidato modifica la chiave di strumentazione in ApplicationInsights.config, che determina la destinazione dei dati inviati dall'SDK del server. A meno che non venga deselezionata l'opzione "Aggiorna tutto", modificherà anche la chiave in cui appare nelle pagine Web.

## <a name="what-is-status-monitor"></a>Che cos'è Status Monitor?

Un'app desktop che è possibile usare nel server Web IIS per configurare Application Insights nelle app Web. Non raccoglie dati di telemetria: è possibile interromperlo se non si sta configurando un'app. 

[Altre informazioni](monitor-performance-live-website-now.md#questions)

## <a name="what-telemetry-is-collected-by-application-insights"></a>Quali dati di telemetria vengono raccolti da Application Insights?

Da app Web del server:

* Richieste HTTP
* [Dipendenze](asp-net-dependencies.md). Chiamate a: database SQL, chiamate HTTP a servizi esterni, Azure Cosmos DB, tabelle, archiviazione BLOB e code. 
* [Eccezioni](asp-net-exceptions.md) e analisi dello stack.
* [Contatori delle prestazioni](performance-counters.md), se si usa [Status Monitor](monitor-performance-live-website-now.md), il [monitoraggio di Azure](azure-web-apps.md) o il [writer collectd di Application Insights](java-collectd.md).
* [Metrica ed eventi personalizzati](api-custom-events-metrics.md) codificati.
* [Log di traccia](asp-net-trace-logs.md) se si configura l'agente di raccolta appropriato.

Da [pagine Web dei client](javascript.md):

* [Conteggi delle visualizzazioni pagina](usage-overview.md)
* [Chiamate AJAX](asp-net-dependencies.md): richieste effettuate da uno script in esecuzione.
* Dati di carico della visualizzazione pagina
* Conteggi di utenti e sessioni
* [ID utente autenticato](api-custom-events-metrics.md#authenticated-users)

Da altre origini, se sono configurate:

* [Diagnostica di Azure](../platform/diagnostics-extension-to-application-insights.md)
* [Dati di importazione in Analytics](../platform/data-collector-api.md)
* [Log Analytics](../platform/data-collector-api.md)
* [Logstash](../platform/data-collector-api.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>È possibile filtrare o modificare alcuni dati di telemetria?

Sì, nel server è possibile scrivere:

* Un processore di telemetria per filtrare o aggiungere proprietà agli elementi di telemetria selezionati prima che vengano inviati dall'app.
* Un inizializzatore di telemetria per aggiungere proprietà a tutti gli elementi della telemetria.

Vedere altre informazioni per [ASP.NET](api-filtering-sampling.md) o [Java](java-filter-telemetry.md).

## <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>In che modo vengono calcolati città, paese/area geografica e altri dati sulla località geografica?

Viene cercato l'indirizzo IP (IPv4 o IPv6) del client Web tramite [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria del browser: viene raccolto l'indirizzo IP del mittente.
* Telemetria del server: viene raccolto l'indirizzo IP del client. L'indirizzo non viene raccolto se è impostato `X-Forwarded-For`.

È possibile configurare `ClientIpHeaderTelemetryInitializer` per ottenere l'indirizzo IP da un'intestazione diversa. Ad esempio, in alcuni sistemi viene spostato da un proxy, da un bilanciamento del carico o da una rete CDN a `X-Originating-IP`. [Altre informazioni](https://apmtips.com/blog/2016/07/05/client-ip-address/)

È possibile [usare Power BI](export-power-bi.md ) per visualizzare i dati di telemetria della richiesta in una mappa.


## <a name="data"></a>Per quanto tempo vengono conservati i dati nel portale? Tale conservazione è sicura?
Vedere l'argomento relativo a [conservazione dei dati e privacy][data].

## <a name="could-personal-data-be-sent-in-the-telemetry"></a>È possibile che le informazioni personali vengano inviate nei dati di telemetria?

Ciò si verifica se il codice invia tali dati. Può succedere anche se le variabili delle analisi dello stack includono informazioni personali. Il team di sviluppo deve eseguire attività di valutazione dei rischi per garantire che le informazioni personali vengano gestite in maniera appropriata. Vedere [altre informazioni sulla conservazione e privacy dei dati](data-retention-privacy.md).

**Tutti** gli ottetti dell'indirizzo Web del client sono sempre impostati su 0 dopo che viene eseguita la ricerca degli attributi relativi alla località geografica.

## <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>La chiave di strumentazione è visibile nell'origine della pagina web. 

* Questo è una pratica comune nelle soluzioni di monitoraggio.
* Questo valore non può essere usato per appropriarsi dei dati personali.
* Potrebbe essere usato per alterare gli avvisi su dati o trigger.
* Nessun cliente ha segnalato tali problemi.

È possibile:

* Usare due distinti chiavi di strumentazione (separare le risorse di Application Insights), per i dati client e server. Oppure
* Scrivere un proxy che viene eseguito nel server in modo che il client Web invii i dati tramite tale proxy.

## <a name="post"></a>Come visualizzare dati POST in Ricerca diagnostica?
I dati POST non vengono registrati automaticamente, ma è possibile usare una chiamata TrackTrace; inserire i dati nel parametro del messaggio, che ha limiti di dimensione più ampi rispetto alle proprietà della stringa, nonostante non sia possibile applicare filtri.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>È preferibile usare una o più risorse di Application Insights?

Usare una singola risorsa per tutti i componenti o i ruoli in un singolo sistema aziendale. Usare risorse separate per sviluppo, test e versioni di rilascio e per applicazioni indipendenti.

* [Vedere questo articolo](separate-resources.md)
* [Esempio: servizio cloud con ruoli di lavoro e Web](cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Come è possibile modificare dinamicamente la chiave di strumentazione?

* [Vedere questo articolo](separate-resources.md)
* [Esempio: servizio cloud con ruoli di lavoro e Web](cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Che cosa sono i conteggi utente e sessione?

* JavaScript SDK imposta un cookie utente nel client Web, per identificare gli utenti abituali, e un cookie di sessione per raggruppare le attività.
* Se non c'è uno script lato client, è possibile [impostare cookie per il server](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Se un utente reale usa il sito in browser diversi o esegue una navigazione privata o in incognito oppure usa computer diversi, viene inclusi più di una volta nei conteggi.
* Per identificare un utente connesso a più computer e browser, aggiungere una chiamata a [setAuthenticatedUserContext()](api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a> In Application Insights sono state abilitate tutte le funzionalità?
| Elementi che dovrebbero essere visualizzati | Come ottenerli | Perché si vuole ottenerli |
| --- | --- | --- |
| Grafici di disponibilità |[Test Web](monitor-web-app-availability.md) |Stabilire se l'app Web è attiva |
| Prestazioni dell'app server: tempi di risposta, ... |[Aggiungere Application Insights al progetto](asp-net.md) o [installare Status Monitor di Application Insights nel server](monitor-performance-live-website-now.md) (o scrivere il codice per [tenere traccia delle dipendenze](api-custom-events-metrics.md#trackdependency)) |Rilevare i problemi di prestazioni |
| Telemetria di dipendenza |[Installare Status Monitor di Application Insights nel server](monitor-performance-live-website-now.md) |Diagnosticare i problemi relativi a database o altri componenti esterni |
| Ricavare analisi dello stack dalle eccezioni |[Inserire chiamate TrackException nel codice](asp-net-exceptions.md) (ma alcune sono segnalate automaticamente) |Rilevare e diagnosticare le eccezioni |
| Eseguire la ricerca di tracce dei log |[Aggiungere un adattatore di registrazione](asp-net-trace-logs.md) |Diagnosticare le eccezioni, problemi di prestazioni |
| Nozioni di base dell'utilizzo del client: visualizzazioni pagina, sessioni, ... |[Inizializzatore JavaScript nelle pagine Web](javascript.md) |Analisi dell'utilizzo |
| Metriche personalizzate client |[Rilevamento delle chiamate nelle pagine Web](api-custom-events-metrics.md) |Migliorare l'esperienza utente |
| Metriche personalizzate server |[Rilevamento delle chiamate nel server](api-custom-events-metrics.md) |Business intelligence |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Perché i conteggi nei grafici di ricerca e di metrica non sono uguali?

Il [campionamento](sampling.md) riduce il numero di elementi di telemetria (richieste, eventi personalizzati e così via) inviati effettivamente dall'app al portale. Nel grafico di ricerca, viene visualizzato il numero di elementi effettivamente ricevuti. Nei grafici di metrica che visualizzano un conteggio degli eventi, è possibile vedere il numero di eventi originali che si sono verificati. 

Ogni elemento trasmesso include una proprietà `itemCount` che visualizza il numero di eventi originali rappresentati da questo elemento. Per osservare un campionamento in esecuzione, è possibile eseguire questa query in Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automazione

### <a name="configuring-application-insights"></a>Configurazione di Application Insights

È possibile [scrivere script di PowerShell](powershell.md) tramite Monitoraggio risorse di Azure per:

* Creare e aggiornare risorse di Application Insights.
* Impostare il piano tariffario.
* Ottenere la chiave di strumentazione.
* Aggiungere un avviso di metrica.
* Aggiungere un test di disponibilità.

Non è possibile impostare un report di esplorazione delle metriche o impostare un'esportazione continua.

### <a name="querying-the-telemetry"></a>Esecuzione di query sui dati di telemetria

Usare l'[API REST](https://dev.applicationinsights.io/) per eseguire le query di [Analytics](analytics.md).

## <a name="how-can-i-set-an-alert-on-an-event"></a>Come è possibile impostare un avviso per un evento?

Gli avvisi di Azure si applicano solo alle metriche. Creare una metrica personalizzata che supera una soglia di valori ogni volta che si verifica l'evento. Impostare quindi un avviso per la metrica. Si noti che verrà inviata una notifica ogni volta che la metrica supera la soglia in entrambe le direzioni. Non si riceverà una notifica fino al primo superamento, indipendentemente dal valore iniziale. C'è sempre una latenza di pochi minuti.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Ci sono costi per il trasferimento dati tra un'app Web di Azure e Application Insights?

* Se l'app Web di Azure è ospitata in un data center in cui è presente un endpoint di raccolta di Application Insights, non è previsto alcun addebito. 
* Se non c'è nessun endpoint nel data center host, la telemetria dell'app sarà soggetta agli [addebiti in uscita di Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Ciò non dipende da dove è ospitata la risorsa di Application Insights. Dipende solo dalla distribuzione degli endpoint.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>È possibile inviare dati di telemetria al portale di Application Insights?

È consigliabile usare gli SDK di Microsoft e l'[API SDK](api-custom-events-metrics.md). Sono disponibili varianti dell'SDK per diverse [piattaforme](platforms.md). Gli SDK gestiscono bufferring, compressione, limitazione, tentativi e così via. Tuttavia, lo [schema di inserimento](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) e il [protocollo di endpoint](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) sono pubblici.

## <a name="can-i-monitor-an-intranet-web-server"></a>È possibile monitorare un server Web Intranet?

Sì, ma sarà necessario consentire il traffico ai servizi da eccezioni del firewall o reindirizzamenti del proxy.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Esaminare l'elenco completo dei servizi offerti e gli indirizzi IP in [questo articolo](../../azure-monitor/app/ip-addresses.md).

### <a name="firewall-exception"></a>Eccezione del firewall

Consentire al server Web di inviare la telemetria agli endpoint. 

### <a name="gateway-redirect"></a>Reindirizzamento di gateway

Instradare il traffico dal server a un gateway nella rete Intranet, sovrascrivendo gli endpoint nella configurazione.
Se queste proprietà "Endpoint" non sono presenti nel file config, queste classi useranno i valori predefiniti illustrati di seguito nell'esempio ApplicationInsights.config. 

Il gateway deve indirizzare il traffico all'indirizzo di base dell'endpoint. Nella configurazione sostituire i valori predefiniti con `http://<your.gateway.address>/<relative path>`.


#### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Esempio Applicationinsights.config con endpoint predefiniti:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

_ApplicationIdProvider è disponibile a partire da v2.6.0_

### <a name="proxy-passthrough"></a>Proxy pass-through

Proxy pass-through può essere ottenuta configurando un livello di computer o a livello di applicazione proxy.
Per altre informazioni vedere l'articolo di dotnet su [DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Web. config di esempio:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>È possibile eseguire test Web di disponibilità in un server Intranet?

I [test Web](monitor-web-app-availability.md) vengono eseguiti in punti di presenza distribuiti in tutto il globo. Sono disponibili due soluzioni:

* Porta di firewall: consentire al server di ricevere richieste dall'[elenco esteso e modificabile degli agenti di test Web](ip-addresses.md).
* Scrivere un codice personalizzato per inviare richieste periodiche al server dall'interno della rete Intranet. A tale scopo è anche possibile eseguire test Web di Visual Studio. Il tester può inviare i risultati ad Application Insights tramite l'API TrackAvailability().

## <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Quanto tempo occorre per raccogliere i dati di telemetria?

Quasi tutti i dati di Application Insights hanno una latenza inferiore a 5 minuti. Alcuni dati, in genere i file di log di maggiori dimensioni, possono richiedere tempi più lunghi. Per altre informazioni, vedere il [Contratto di Servizio per Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).

## <a name="more-answers"></a>Altre risposte
* [Forum di Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: data-retention-privacy.md
[platforms]: platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
