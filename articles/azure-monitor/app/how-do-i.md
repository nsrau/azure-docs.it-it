---
title: Cosa fare in Azure Application Insights | Microsoft Docs
description: Domande frequenti in Application Insights
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 134089f4df8f80147182835ca8746322c1de7e50
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319253"
---
# <a name="how-do-i--in-application-insights"></a>Cosa fare in Application Insights?
## <a name="get-an-email-when-"></a>Per ricevere un messaggio di posta elettronica quando...
### <a name="email-if-my-site-goes-down"></a>Inviare un messaggio di posta elettronica se il sito non è disponibile
Impostare un [test web di disponibilità](./monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>Inviare un messaggio di posta elettronica se il sito è sovraccarico
Impostare un [avviso](../platform/alerts-log.md) per il **tempo di risposta del server**. Una soglia compresa tra 1 e 2 secondi dovrebbe risolvere il problema.

![Screenshot che illustra come impostare un avviso sul tempo di risposta del server.](./media/how-do-i/030-server.png)

L'applicazione potrebbe inoltre dare segni di difficoltà tramite la restituzione di codici di errore. Impostare un avviso per le **richieste non riuscite**.

Se si desidera impostare un avviso per le **eccezioni del server**, è necessario effettuare [alcune impostazioni aggiuntive](./asp-net-exceptions.md) per visualizzare i dati.

### <a name="email-on-exceptions"></a>Inviare un messaggio di posta elettronica in caso di eccezioni
1. [Configurare il monitoraggio delle eccezioni](./asp-net-exceptions.md)
2. [Impostare un avviso](../platform/alerts-log.md) sulla metrica relativa al conteggio del numero di eccezioni

### <a name="email-on-an-event-in-my-app"></a>Inviare un messaggio di posta elettronica per un evento generato dall'app
Si supponga che si desidera ricevere un messaggio di posta elettronica quando si verifica un evento specifico. Application Insights non fornisce direttamente questa funzionalità, ma è possibile [inviare un avviso quando una metrica supera una soglia](../platform/alerts-log.md).

Gli avvisi possono essere impostati per [metriche personalizzate](./api-custom-events-metrics.md#trackmetric), anche se non per gli eventi personalizzati. Scrivere codice per potenziare una metrica quando si verifica l'evento:

```csharp
telemetry.TrackMetric("Alarm", 10);
```

oppure:

```csharp
var measurements = new Dictionary<string,double>();
measurements ["Alarm"] = 10;
telemetry.TrackEvent("status", null, measurements);
```

Poiché gli avvisi possono avere due stati, è necessario inviare un valore basso quando si presume che l'avviso sia terminato:

```csharp
telemetry.TrackMetric("Alarm", 0.5);
```

Creare un grafico in [Esplora metriche](../platform/metrics-charts.md) per visualizzare l'allarme:

![Screenshot che illustra come creare un grafico in Esplora metriche per visualizzare l'allarme.](./media/how-do-i/010-alarm.png)

Impostare un avviso da attivare quando la metrica supera un valore medio per un breve periodo:

![Screenshot che illustra come impostare un avviso da attivare quando la metrica supera il valore medio per un breve periodo di tempo.](./media/how-do-i/020-threshold.png)

Impostare il periodo medio sul valore minimo.

Verranno ricevuti messaggi di posta elettronica quando la metrica è maggiore o minore della soglia.

Alcune informazioni da considerare:

* Un avviso dispone di due stati: "Avviso" e "Integro". Lo stato viene valutato solo quando viene ricevuta una metrica.
* Un messaggio di posta elettronica viene inviato solo quando lo stato cambia. Questo è il motivo per cui è necessario inviare sia metriche con valori alti che metriche con valori bassi.
* Per valutare l'avviso, viene eseguita la media dei valori ricevuti nel periodo precedente. Ciò si verifica ogni volta che viene ricevuta una metrica. Per questo motivo è possibile che i messaggi di posta elettronica vengano inviati più frequentemente rispetto al periodo impostato.
* Poiché vengono inviati messaggi di posta elettronica sia per avvisi con stato "Avviso" e che per avvisi con stato "Integro", è possibile riconsiderare l'evento unico come una condizione con due stati. Ad esempio, anziché un evento "processo completato", creare una condizione "processo in corso", dove è possibile ricevere messaggi di posta elettronica all'inizio e alla fine di un processo.

### <a name="set-up-alerts-automatically"></a>Impostare automaticamente gli avvisi
[Usare PowerShell per creare nuovi avvisi](../platform/alerts-log.md)

## <a name="use-powershell-to-manage-application-insights"></a>Usare PowerShell per gestire Application Insights
* [Creare nuove risorse](./create-new-resource.md#creating-a-resource-automatically)
* [Creare nuovi avvisi](../platform/alerts-log.md)

## <a name="separate-telemetry-from-different-versions"></a>Separare la telemetria da diverse versioni

* Più ruoli in un'app: usare una singola risorsa di Application Insights e filtrare per [cloud_Rolename](./app-map.md).
* Separazione delle versioni di sviluppo, test e rilascio: usare risorse di Application Insights diverse. Acquisire le chiavi di strumentazione da web.config. [Altre informazioni](./separate-resources.md)
* Creazione di report sulle versioni della build: aggiungere una proprietà usando un inizializzatore di telemetria. [Altre informazioni](./separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Monitorare i server back-end e le app desktop
[Usare il modulo di Windows Server SDK](./windows-desktop.md).

## <a name="visualize-data"></a>Visualizzare i dati
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Dashboard con metriche da più app
* In [Esplora metriche](../platform/metrics-charts.md)personalizzare il grafico e salvarlo come preferito. Aggiungerlo al dashboard di Azure.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Dashboard con dati provenienti da altre fonti e Application Insights
* [Esportare dati di telemetria in Power BI](./export-power-bi.md).

Oppure

* Usare SharePoint come dashboard, dove i dati vengono visualizzati in Web part di SharePoint. [Usare l'esportazione continua e l'analisi dei flussi per eseguire l'esportazione in SQL](./code-sample-export-sql-stream-analytics.md).  Usare PowerView per esaminare il database e creare una Web part di SharePoint per PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Filtrare gli utenti anonimi o autenticati
Se gli utenti effettuano l'accesso, è possibile impostare l'[ID utente autenticato](./api-custom-events-metrics.md#authenticated-users). Questa operazione non viene eseguita automaticamente.

Sarà quindi possibile:

* Eseguire ricerche in base a ID utente specifici

![Screenshot che mostra le opzioni per la ricerca su ID utente specifici.](./media/how-do-i/110-search.png)

* Filtrare le metriche in base a utenti anonimi o autenticati

![Screenshot che mostra il filtro metrixs per gli utenti anonimi o autenticati.](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Modificare i nomi della proprietà o i valori
Creare un [filtro](./api-filtering-sampling.md#filtering). Consente di modificare o filtrare la telemetria prima che venga inviata dall'app ad Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Elencare utenti specifici e il relativo uso
Se si vuole [cercare utenti specifici](#search-specific-users), è possibile impostare l'[ID utente autenticato](./api-custom-events-metrics.md#authenticated-users).

Se si desidera un elenco di utenti con i dati quali, ad esempio, le pagine visualizzate o la frequenza di accesso, sono disponibili due opzioni:

* [Impostare l'ID utente autenticato](./api-custom-events-metrics.md#authenticated-users), [eseguire l'esportazione in un database](./code-sample-export-sql-stream-analytics.md) e usare gli strumenti appropriati per analizzare i dati utente.
* Se si ha solo un numero limitato di utenti, inviare gli eventi o le metriche personalizzate usando i dati di interesse come valore della metrica o nome dell'evento e impostando l'ID utente come proprietà. Per analizzare le visualizzazioni di pagina, sostituire la chiamata standard JavaScript trackPageView. Per analizzare la telemetria sul lato server, usare un inizializzatore di telemetria per aggiungere l'ID utente a tutta la telemetria del server. È quindi possibile filtrare e segmentare le metriche e le ricerche in base all'ID utente.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Ridurre il traffico dall'app ad Application Insights
* In [ApplicationInsights.config](./configuration-with-applicationinsights-config.md)disabilitare tutti i moduli non necessari, come gli agenti di raccolta del contatore delle prestazioni.
* Usare [Campionamento e filtri](./api-filtering-sampling.md) nell’SDK.
* Nelle pagine Web limitare il numero di chiamate Ajax segnalato per ogni visualizzazione della pagina. Nel frammento di script dopo `instrumentationKey:...` inserire: `,maxAjaxCallsPerView:3` (o un numero adatto).
* Se si usa [TrackMetric](./api-custom-events-metrics.md#trackmetric), calcolare l'aggregazione di batch di valori delle metriche prima di inviare il risultato. Un overload di TrackMetric() esegue questa operazione.

Altre informazioni su [prezzi e quote](./pricing.md).

## <a name="disable-telemetry"></a>Disabilitare telemetria
Per **avviare e arrestare in modo dinamico** la raccolta e la trasmissione di dati di telemetria dal server:

### <a name="aspnet-classic-applications"></a>Applicazioni ASP.NET classiche

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Altre applicazioni
Non è consigliabile usare il singleton `TelemetryConfiguration.Active` nella console o nelle applicazioni ASP.NET Core.
Se è stata creata l'istanza`TelemetryConfiguration`, impostare `DisableTelemetry` su `true`.

Per le applicazioni ASP.NET Core, è possibile accedere all'istanza `TelemetryConfiguration` usando l'[inserimento delle dipendenze di ASP.NET Core](/aspnet/core/fundamentals/dependency-injection/). Per informazioni dettagliate, vedere l'articolo [ApplicationInsights per le applicazioni ASP.NET Core](./asp-net-core.md).

## <a name="disable-selected-standard-collectors"></a>Disabilitare gli agenti di raccolta standard selezionati
È possibile disabilitare gli agenti di raccolta standard, ad esempio i contatori delle prestazioni, le richieste HTTP o le dipendenze.

* **Applicazioni ASP.NET**: eliminare o impostare come commento le righe pertinenti in [ApplicationInsights.config](./configuration-with-applicationinsights-config.md)
* **Applicazioni ASP.NET Core**: seguire le opzioni di configurazione per i moduli di telemetria in [ApplicationInsights ASP.NET Core](./asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>Visualizzare i contatori delle prestazioni di sistema
Tra le metriche che è possibile visualizzare in Esplora metriche è disponibile un set di contatori delle prestazioni di sistema. Esiste un pannello predefinito denominato **Server** in cui sono visualizzati alcuni set.

![Aprire la risorsa Application Insights e fare clic su Server.](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Se non vengono visualizzati dati dei contatori delle prestazioni
* **Server IIS** sul proprio computer o in una macchina virtuale. [Installare Status Monitor](./monitor-performance-live-website-now.md).
* **Sito Web di Azure** - i contatori delle prestazioni non sono ancora supportati. Esistono diverse metriche che è possibile ottenere come una parte standard del Pannello di controllo del sito Web di Azure.
* **Server Unix** - [Installare collectd](./java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Per visualizzare altri contatori delle prestazioni
* Innanzitutto [aggiungere un nuovo grafico](../platform/metrics-charts.md) e verificare che il contatore sia incluso nel set di base offerto.
* In caso contrario, [aggiungere il contatore al set raccolto dal modulo del contatore delle prestazioni](./performance-counters.md).

