---
title: Gestire l'utilizzo e i costi per Azure Application Insights | Microsoft Docs
description: Gestire volumi di dati di telemetria e monitorare i costi in Application Insights.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: DaleKoetke
ms.author: dalek
ms.date: 5/7/2020
ms.reviewer: mbullwin
ms.openlocfilehash: b695205c08f9039fbf91eaeddb7622b784d81d12
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400588"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Gestire l'utilizzo e i costi per Application Insights

> [!NOTE]
> Questo articolo descrive come comprendere e controllare i costi per Application Insights.  Un articolo correlato, [Monitoraggio dell'utilizzo e dei costi stimati](../platform/usage-estimated-costs.md), descrive come visualizzare l'utilizzo e i costi stimati relativi a più funzionalità di monitoraggio di Azure per diversi modelli di prezzi.

Application Insights è progettato per garantire tutto ciò che serve per monitorare disponibilità, prestazioni e utilizzo delle applicazioni Web, indipendentemente dal fatto che siano ospitate in Azure o in locale. Application Insights supporta i linguaggi e i framework più diffusi, ad esempio .NET, Java e Node.js, e si integra con processi e strumenti DevOps come Azure DevOps, Jira e PagerDuty. È importante capire cosa determina i costi di monitoraggio delle applicazioni. In questo articolo vengono esaminati i costi di monitoraggio delle applicazioni e il modo in cui è possibile monitorarli e controllarli in modo proattivo.

Per domande sulla determinazione dei prezzi per Application Insights, è possibile pubblicare una domanda nella [pagina delle domande e risposte di Microsoft](/answers/topics/azure-monitor.html).

## <a name="pricing-model"></a>Modello di prezzi

L'impostazione dei prezzi per [Azure Application Insights][start] è un modello **con pagamento in base al consumo** basato sul volume di dati inserito e, facoltativamente, per la conservazione dei dati più a lungo termine. Ogni risorsa di Application Insights viene addebitata come servizio separato e contribuisce alla fatturazione per la sottoscrizione di Azure. Il volume de dati viene misurato in base alle dimensioni del pacchetto di dati JSON non compresso inviato dall'applicazione ad Application Insights. Non è previsto alcun addebito relativo al volume di dati per l'utilizzo di [Live Metrics Stream](./live-stream.md).

È prevista una tariffa aggiuntiva per i [test Web in più passi](./availability-multistep.md). I test Web in più passi sono test Web che eseguono una sequenza di azioni. Non è prevista una tariffa separata per i *test del ping* di una singola pagina. I dati di telemetria dei test del ping e dei test in più passi vengono addebitai allo stesso costo di altri dati di telemetria provenienti dall'app.

L'opzione Application Insights per [abilitare gli avvisi sulle dimensioni delle metriche personalizzate](./pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) può anche generare costi aggiuntivi, in quanto può comportare la creazione di metriche di preaggregazione aggiuntive. Sono disponibili [altre informazioni](./pre-aggregated-metrics-log-metrics.md) sulle metriche preaggregate e basate su log in Application Insights e sui [prezzi](https://azure.microsoft.com/pricing/details/monitor/) per le metriche personalizzate di Monitoraggio di Azure.

### <a name="workspace-based-application-insights"></a>Risorse di Application Insights basate sull'area di lavoro

Per le risorse Application Insights che inviano i dati a un'area di lavoro Log Analytics, denominate [risorse Application Insights basate sull'area di lavoro](create-workspace-resource.md), la fatturazione per l'inserimento e la conservazione dei dati viene effettuata dall'area di lavoro in cui si trovano i dati di Application Insights. In questo modo i clienti possono sfruttare tutte le opzioni del [modello di determinazione dei prezzi](../platform/manage-cost-storage.md#pricing-model) di Log Analytics, che include le prenotazioni della capacità oltre al pagamento in base al consumo. Log Analytics include inoltre più opzioni per la conservazione dei dati, inclusa la [conservazione per tipo di dati](../platform/manage-cost-storage.md#retention-by-data-type). I tipi di dati Application Insights nell'area di lavoro ricevono 90 giorni di conservazione senza addebiti. L'utilizzo dei test Web e l'abilitazione degli avvisi sulle dimensioni della metriche personalizzate vengono comunque segnalati tramite Application Insights. Sono disponibili informazioni su come tenere traccia dei costi di inserimento e conservazione dei dati in Log Analytics mediante [Utilizzo e costi stimati](../platform/manage-cost-storage.md#understand-your-usage-and-estimate-costs), [Gestione dei costi e fatturazione di Azure](../platform/manage-cost-storage.md#viewing-log-analytics-usage-on-your-azure-bill) e [query Log Analytics](#data-volume-for-workspace-based-application-insights-resources). 

## <a name="estimating-the-costs-to-manage-your-application"></a>Stima dei costi per la gestione dell'applicazione

Se non si usa ancora Application Insights, è possibile usare il [calcolatore dei prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/calculator/?service=monitor) per stimare il costo dell'uso di Log Analytics. Per iniziare, immettere "Monitoraggio di Azure" nella casella di ricerca e fare clic sul riquadro Monitoraggio di Azure che viene visualizzato. Scorrere la pagina verso il basso fino a Monitoraggio di Azure e selezionare Log Analytics dall'elenco a discesa Tipo.  Qui è possibile immettere il numero di GB di dati che si prevede di raccogliere al mese, quindi la domanda è la quantità di dati che Application Insights raccoglierà monitorando l'applicazione.

Esistono due approcci per gestire questa situazione: l'uso del monitoraggio predefinito e del campionamento adattivo, disponibile in ASP.NET SDK, o la stima dell'inserimento dati in base a ciò che altri clienti simili hanno sperimentato.

### <a name="data-collection-when-using-sampling"></a>Raccolta di dati in caso di uso del campionamento

Con il [campionamento adattivo](sampling.md#adaptive-sampling) di ASP.NET SDK, il volume di dati viene regolato automaticamente in modo da rimanere entro una frequenza massima di traffico specificata per il monitoraggio predefinito di Application Insights. Se l'applicazione produce una quantità ridotta di dati di telemetria, ad esempio durante il debug o a causa di un utilizzo ridotto, gli elementi non verranno eliminati dal processore di campionamento, purché il volume sia inferiore al livello di eventi configurati al secondo. Per un'applicazione con volume elevato, con la soglia predefinita di cinque eventi al secondo, il campionamento adattivo limiterà il numero di eventi giornalieri a 432.000. Una dimensione di evento media tipica di 1 KB corrisponde a 13,4 GB di dati di telemetria per ogni mese di 31 giorni per nodo che ospita l'applicazione (poiché il campionamento viene eseguito localmente in ogni nodo). 

Per gli SDK che non supportano il campionamento adattivo, è possibile usare il [campionamento per inserimento](./sampling.md#ingestion-sampling), che esegue il campionamento quando i dati vengono ricevuti da Application Insights in base a una percentuale di dati da conservare, oppure il [campionamento a frequenza fissa per ASP.NET, ASP.NET Core e siti Web Java](sampling.md#fixed-rate-sampling) per ridurre il traffico inviato dal server Web e dai Web browser

### <a name="learn-from-what-similar-customers-collect"></a>Dati raccolti da clienti analoghi

Nel calcolatore dei prezzi di Monitoraggio di Azure per Application Insights, se si abilita la funzionalità di stima del volume di dati in base all'attività dell'applicazione, è possibile fornire input sull'applicazione (richieste al mese e visualizzazioni di pagina al mese, nel caso in cui vengano raccolti i dati di telemetria sul lato client) e quindi il calcolatore consente di visualizzare il livello mediano e il 90° percentile dei dati raccolti da applicazioni simili. Queste applicazioni coprono l'intero spettro di configurazione di Application Insights (ad esempio, alcune hanno il [campionamento](./sampling.md) predefinito, altre sono sprovviste di campionamento e così via), quindi è comunque possibile ridurre il volume di dati inseriti al di sotto del livello mediano usando il campionamento. Si tratta tuttavia di un punto di partenza per capire cosa accade altri clienti simili.

## <a name="understand-your-usage-and-estimate-costs"></a>Comprendere l'utilizzo e stimare i costi

Application Insights offre informazioni per comprendere con facilità i possibili costi associati in base a modelli di uso recente. Per iniziare, nel portale di Azure, per la risorsa di Application Insights passare alla pagina **Utilizzo e costi stimati**:

![Scegliere i prezzi](./media/pricing/pricing-001.png)

R. Esaminare il volume di dati per il mese. Sono inclusi tutti i dati ricevuti e conservati (dopo un [campionamento](./sampling.md)) dal server e dalle app client, oltre che dai test di disponibilità.  
B. Viene effettuato un addebito separato per i [test Web](./availability-multistep.md)in più passaggi. Non sono inclusi i test di disponibilità semplici, il cui costo viene addebitato con il volume di dati.  
C. Visualizzare le tendenze del volume dei dati relative all'ultimo mese.  
D. Abilitare il [campionamento](./sampling.md) per l'inserimento dei dati.
E. Impostare il limite giornaliero di utilizzo per volume dati.  

Si noti che tutti i prezzi visualizzati negli screenshot in questo articolo sono solo a scopo esemplificativo. Per i prezzi correnti nella valuta e nell'area locali, consultare [Prezzi di Application Insights][pricing].

Per analizzare l'utilizzo di Application Insights a un livello più dettagliato, aprire la pagina **Metriche**, aggiungere la metrica denominata "Volume del punto dati" e quindi selezionare l'opzione *Applica suddivisione* per suddividere i dati in base al tipo di elemento di telemetria.

Gli addebiti di Application Insights vengono aggiunti alla fatturazione di Azure. È possibile visualizzare i dettagli della fattura di Azure nella sezione **Gestione dei costi e fatturazione** del portale di Azure oppure nel [portale di fatturazione di Azure](https://account.windowsazure.com/Subscriptions).  Per informazioni dettagliate sull'uso per Application Insights, [vedere di seguito](#viewing-application-insights-usage-on-your-azure-bill). 

![Nel menu a sinistra selezionare Fatturazione](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Uso delle metriche dei volumi di dati
<a id="understanding-ingested-data-volume"></a>

Per altre informazioni sui volumi di dati, selezionare **Metriche** per la risorsa Application Insights e aggiungere un nuovo grafico. Per la metrica del grafico, in **Metriche basate su log** selezionare **Volume dei punti dati**. Fare clic su **Applica suddivisione** e selezionare Raggruppa per tipo **`Telemetryitem`** .

![Usare le metriche per esaminare il volume di dati](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Query per comprendere i dettagli dei volumi di dati

Esistono due approcci per analizzare i volumi di dati per Application Insights. Il primo usa le informazioni aggregate nella tabella `systemEvents` e il secondo la proprietà `_BilledSize`, disponibile in ogni evento inserito. `systemEvents` non include informazioni sulle dimensioni dei dati per [risorse Application Insights basate sull'area di lavoro](#data-volume-for-workspace-based-application-insights-resources).

#### <a name="using-aggregated-data-volume-information"></a>Uso di informazioni sul volume di dati aggregati

Ad esempio, è possibile usare la tabella `systemEvents` per visualizzare il volume di dati inserito nelle ultime 24 ore con la query:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

In alternativa, per visualizzare un grafico del volume di dati (in byte) per tipo di dati per gli ultimi 30 giorni, è possibile usare:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Si noti che questa query può essere usata in un [avviso log di Azure](../platform/alerts-unified-log.md) per impostare avvisi sui volumi di dati.  

Per altre informazioni sulle modifiche apportate ai dati di telemetria, è possibile ottenere il conteggio degli eventi in base al tipo usando la query:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Uso delle informazioni sulle dimensioni dei dati per evento

Per altre informazioni sull'origine dei volumi di dati, è possibile usare la proprietà `_BilledSize` presente in ogni evento inserito.

Per esaminare, ad esempio, le operazioni che generano il volume di dati più recente negli ultimi 30 giorni, è possibile sommare `_BilledSize` per tutti gli eventi di dipendenza:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

#### <a name="data-volume-for-workspace-based-application-insights-resources"></a>Volume di dati per risorse di Application Insights basate sull'area di lavoro

Per esaminare le tendenze dei volumi di dati per tutte le [risorse Application Insights basate sull'area di lavoro](create-workspace-resource.md) in un'area di lavoro per l'ultima settimana, passare all'area di lavoro Log Analytics ed eseguire la query:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d) and TimeGenerated < startofday(now())
| summarize sum(_BilledSize) by _ResourceId, bin(TimeGenerated, 1d)
| render areachart
```

Per eseguire una query sulle tendenze dei volumi di dati per tipo per una specifica risorsa Application Insights basata sull'area di lavoro, nell'area di lavoro Log Analytics usare:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d) and TimeGenerated < startofday(now())
| where _ResourceId contains "<myAppInsightsResourceName>"
| summarize sum(_BilledSize) by Type, bin(TimeGenerated, 1d)
| render areachart
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Visualizzazione dell'utilizzo di Application Insights nella fattura di Azure

Azure fornisce una grande quantità di funzionalità utili nell'hub [Gestione dei costi e fatturazione](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) di Azure. Ad esempio, la funzionalità di analisi dei costi consente di visualizzare le spese per le risorse di Azure. L'aggiunta di un filtro per tipo di risorsa (a microsoft.insights/Ccmponents per Application Insights) consente di tenere traccia della spesa. Quindi, per "Raggruppa per", selezionare "Categoria del contatore" o "Contatore".  Per le risorse Application Insights sui piani tariffari correnti, la maggior parte dell'utilizzo viene visualizzata come Log Analytics per la categoria Contatore, poiché esiste un singolo back-end di log per tutti i componenti di Monitoraggio di Azure. 

Una maggiore comprensione dell'utilizzo può essere ottenuta [scaricando l'utilizzo dal portale di Azure](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal).
Nel foglio di calcolo scaricato è possibile visualizzare l'utilizzo per ogni risorsa di Azure al giorno. In questo foglio di calcolo di Excel, l'utilizzo delle risorse di Application Insights è disponibile filtrando prima di tutto la colonna "Categoria del contatore" per visualizzare "Application Insights" e "Log Analytics", quindi aggiungendo un filtro alla colonna "ID istanza" che risulta "contiene microsoft.insights/components".  La maggior parte dell'utilizzo di Application Insights viene segnalata nei contatori con la categoria Contatore di Log Analytics, dal momento che è presente un singolo back-end per tutti i componenti di Monitoraggio di Azure.  Solo le risorse Application Insights sui piani tariffari legacy e i test Web in più passaggi vengono riportate con una categoria Contatore di Application Insights.  L'utilizzo viene visualizzato nella colonna "Quantità utilizzata" e l'unità per ogni voce viene visualizzata nella colonna "Unità di misura".  Maggiori dettagli sono disponibili nelle [informazioni sulla fattura di Microsoft Azure](../../cost-management-billing/understand/review-individual-bill.md).

## <a name="managing-your-data-volume"></a>Gestione del volume di dati

Il volume dei dati inviati può essere gestito con le tecniche seguenti:

* **Campionamento**: È possibile usare il campionamento per ridurre la quantità di dati di telemetria inviati dal server e dalle app client, con una distorsione minima delle metriche. Si tratta dello strumento principale da usare per ottimizzare la quantità di dati inviati. Altre informazioni sulle [funzionalità di campionamento](./sampling.md).

* **Limitare le chiamate Ajax**: è possibile [limitare il numero di chiamate Ajax che possono essere segnalate](./javascript.md#configuration) in ogni visualizzazione pagina o disattivare la creazione di report Ajax.

* **Disabilitare i moduli non necessari**: Disattivare i moduli di raccolta non necessari [modificando il file ApplicationInsights.config](./configuration-with-applicationinsights-config.md). Ad esempio, è possibile che i contatori delle prestazioni o dati sulle dipendenze siano non essenziali.

* **Preaggregare metriche**: Se sono state inserite chiamate a TrackMetric nell'app, è possibile ridurre il traffico usando l'overload che accetta il calcolo della media e la deviazione standard di un batch di misurazioni. In alternativa è possibile usare un [pacchetto di pre-aggregazione](https://www.myget.org/gallery/applicationinsights-sdk-labs).
 
* **Limite giornaliero**: Quando si crea una risorsa di Application Insights nel portale di Azure, il limite di utilizzo giornaliero è impostato su 100 GB al giorno. Il valore predefinito quando si crea una risorsa di Application Insights in Visual Studio è basso, solo 32,3 MB al giorno. Il valore predefinito del limite di utilizzo giornaliero è impostato per semplificare le operazioni di test. È opportuno che l'utente aumenti il limite di utilizzo giornaliero prima di distribuire l'app nell'ambiente di produzione. 

    Il limite di utilizzo massimo è 1.000 GB al giorno, a meno che non si richieda un valore massimo più alto per un'applicazione con traffico elevato.
    
    Vengono inviati messaggi di posta elettronica di avviso relativi al limite giornaliero agli account membri di questi ruoli per la risorsa Application Insights: "ServiceAdmin", "AccountAdmin", "CoAdmin", "Owner".

    Prestare attenzione quando si imposta il limite di utilizzo giornaliero. Lo scopo dovrebbe essere quello di *non raggiungere mai il limite di utilizzo giornaliero*. Se si raggiunge il limite di utilizzo giornaliero, i dati della restante parte della giornata andranno persi e non sarà possibile monitorare l'applicazione. Per modificare il limite di utilizzo giornaliero, usare l'opzione **Limite di utilizzo volume giornaliero**. È possibile accedere a questa opzione nel riquadro **Usage and estimated cost** (Uso e costi stimati), descritto in dettaglio più avanti in questo articolo.
    
    La restrizione è stata rimossa per alcuni tipi di sottoscrizione con un credito che non poteva essere usato per Application Insights. In precedenza, se alla sottoscrizione era associato un limite di spesa, nella finestra di dialogo relativa al limite di utilizzo giornaliero erano presenti istruzioni per rimuoverlo e abilitare l'aumento del limite di utilizzo giornaliero oltre 32,3 MB al giorno.
    
* **Limitazione**: Limita la velocità dei dati a 32.000 eventi al secondo, in base a una media calcolata a intervalli di 1 minuto per chiave di strumentazione. Il volume dei dati inviati dall'app viene valutato ogni minuto. Se il valore supera la velocità media al secondo calcolata nel minuto, il server rifiuta alcune richieste. L'SDK memorizza nel buffer i dati e quindi tenta di rinviarli. Segnala un sovraccarico per alcuni minuti. Se l'app continua a inviare dati che eccedono il valore della limitazione, alcuni di questi verranno eliminati. Gli SDK di ASP.NET, Java e JavaScript ritentano l'invio dei dati, mentre altri SDK potrebbero semplicemente eliminare i dati che superano la limitazione. In caso di avvenuta limitazione, verrà visualizzata una notifica che avviserà l'utente di che ciò si è verificato.

## <a name="manage-your-maximum-daily-data-volume"></a>Gestire il volume di dati giornaliero massimo

È possibile usare il limite di utilizzo giornaliero di volume per limitare i dati raccolti. Tuttavia se si raggiunge questo limite, tutti i dati di telemetria inviati dall'applicazione per la restante parte del giorno andranno persi. *Non è consigliabile* raggiungere il limite di utilizzo giornaliero dell'applicazione. Dopo averlo raggiunto non sarà possibile monitorare l'integrità e le prestazioni dell'applicazione.

Anziché usare il limite di volume giornaliero, usare il [campionamento](./sampling.md) per impostare il volume di dati al livello desiderato. Usare quindi il limite di utilizzo giornaliero solo se "strettamente necessario" nel caso in cui l'applicazione inizi a inviare volumi più elevati di dati di telemetria in modo imprevisto.

### <a name="identify-what-daily-data-limit-to-define"></a>Identificare la soglia dei dati giornaliera da definire

Vedere Utilizzo e costi stimati di Application Insights per informazioni sulla tendenza nell'inserimento dati e sul limite di volume giornaliero da definire. Fare attenzione nella scelta del limite, in quanto non sarà possibile monitorare le risorse una volta raggiunto il limite.

### <a name="set-the-daily-cap"></a>Impostare il limite giornaliero

Per modificare il limite di utilizzo giornaliero, nella sezione **Configura** della risorsa Application Insights, dalla pagina **Utilizzo e costi stimati**, selezionare **Limite di utilizzo giornaliero**.

![Regolazione del limite del volume dei dati di telemetria giornaliero](./media/pricing/pricing-003.png)

Per [modificare il limite di utilizzo giornaliero tramite Azure Resource Manager](./powershell.md), la proprietà da modificare è `dailyQuota`.  Tramite Azure Resource Manager è anche possibile impostare `dailyQuotaResetTime` e la proprietà `warningThreshold` del limite di utilizzo giornaliero.

### <a name="create-alerts-for-the-daily-cap"></a>Crea avvisi per il limite di utilizzo giornaliero

Il limite di utilizzo giornaliero di Application Insights crea un evento nel log attività di Azure quando i volumi di dati inseriti raggiungono il livello di avviso o il limite di utilizzo giornaliero.  È possibile [creare un avviso in base a questi eventi del log attività](../platform/alerts-activity-log.md#create-with-the-azure-portal). Le indicazioni per questi eventi sono:

* È stata raggiunta la soglia di avviso del limite giornaliero per il componente Application Insights

* È stato raggiunto il limite giornaliero per il componente Application Insights

## <a name="sampling"></a>campionamento
il [campionamento](./sampling.md) è un metodo per ridurre la velocità con cui i dati di telemetria vengono inviati all'app, pur mantenendo la possibilità di trovare eventi correlati durante le ricerche diagnostiche. e il conteggio corretto degli eventi.

Il campionamento consente di ridurre in modo efficace i costi e di non superare la quota mensile. L'algoritmo di campionamento conserva gli elementi correlati ai dati di telemetria, in modo che, quando si usa la ricerca, ad esempio, è possibile trovare la richiesta correlata a una particolare eccezione. L'algoritmo mantiene anche i conteggi corretti e consente di visualizzare in Esplora metriche i valori corretti della frequenza delle richieste, della frequenza delle eccezioni e di altri contatori.

Sono disponibili diversi tipi di campionamento.

* Il [campionamento adattivo](./sampling.md) è il valore predefinito per l'SDK di ASP.NET. Il campionamento adattivo si adatta automaticamente al volume dei dati di telemetria inviati dall'app. Viene eseguito automaticamente nell'SDK dell'app Web riducendo il traffico dei dati di telemetria sulla rete. 
* *campionamento per inserimento* è un'alternativa che opera nel punto in cui i dati di telemetria provenienti dall'app raggiungono il servizio Application Insights. Il campionamento per inserimento non ha alcun effetto sul volume dei dati di telemetria inviati dall'app, ma riduce il volume mantenuto dal servizio. È possibile usarlo per ridurre la quota che i dati di telemetria usano dal browser e da altri SDK.

Per impostare il campionamento per inserimento, visitare il riquadro **Prezzi**:

![Nel riquadro Quota and pricing (Quota e tariffe) fare clic sul riquadro Campioni e quindi selezionare una frazione di campionamento](./media/pricing/pricing-004.png)

> [!WARNING]
> Il riquadro **Campionamento dei dati** controlla solo il valore del campionamento per inserimento. Non riflette la frequenza di campionamento applicata dall'SDK di Application Insights nell'app. Se i dati di telemetria in ingresso sono già stati campionati nell'SDK, il campionamento per inserimento non verrà applicato.
>

Per individuare la frequenza di campionamento effettiva indipendentemente dal punto in cui è stata applicata, usare una [query di Analytics](../log-query/log-query-overview.md). La query appare come la seguente:

```kusto
requests | where timestamp > ago(1d)
| summarize 100/avg(itemCount) by bin(timestamp, 1h)
| render areachart
```

In ogni record conservato, `itemCount` indica il numero di record originali che rappresenta, uguale a 1 + il numero di record precedenti scartati.

## <a name="change-the-data-retention-period"></a>Cambiare il periodo di conservazione dei dati

Il valore predefinito di conservazione dei dati per le risorse Application Insights è 90 giorni. È possibile selezionare periodi di conservazione dei dati diversi per ogni risorsa Application Insights. Il set completo di periodi di conservazione disponibili è 30, 60, 90, 120, 180, 270, 365, 550 o 730 giorni. Sono disponibili [altre informazioni](https://azure.microsoft.com/pricing/details/monitor/) sui prezzi per la conservazione dei dati più a lungo termine. 

Per modificare il periodo di conservazione, dalla risorsa Application Insights passare alla pagina **Utilizzo e costi stimati** e selezionare l'opzione **Conservazione dei dati**:

![Screenshot che mostra dove modificare il periodo di conservazione dei dati.](./media/pricing/pricing-005.png)

Quando il periodo di conservazione viene ridotto, è previsto un periodo di tolleranza di diversi giorni prima che i dati meno recenti vengano rimossi.

Il periodo di conservazione può anche essere [impostato a livello di codice mediante PowerShell](powershell.md#set-the-data-retention) usando il parametro `retentionInDays`. Inoltre, se si imposta la conservazione dei dati su 30 giorni, è possibile attivare una rimozione immediata dei dati meno recenti usando il parametro `immediatePurgeDataOn30Days`, che può essere utile per scenari correlati alla conformità. Questa funzionalità di rimozione viene esposta solo tramite Azure Resource Manager e deve essere usata con estrema cautela. Il tempo di ripristino giornaliero per il limite del volume di dati può essere configurato usando Azure Resource Manager per impostare il parametro `dailyQuotaResetTime`.

## <a name="data-transfer-charges-using-application-insights"></a>Addebiti per il trasferimento dei dati tramite Application Insights

L'invio di dati a Application Insights potrebbe comportare addebiti per la larghezza di banda dei dati. Come descritto nella [pagina dei prezzi per la larghezza di banda di Azure](https://azure.microsoft.com/pricing/details/bandwidth/), il trasferimento dei dati tra i servizi di Azure in due aree viene addebitato come trasferimento dati in uscita alla tariffa normale. Il trasferimento di dati in entrata è gratuito. Tuttavia, questo addebito è minimo (% ridotta) rispetto ai costi per l'inserimento dei dati di Application Insights. Di conseguenza, il controllo dei costi per Log Analytics deve concentrarsi sul volume di dati inserito e [qui](#managing-your-data-volume) sono disponibili indicazioni utili per la sua comprensione.

## <a name="limits-summary"></a>Riepilogo dei limiti

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Disabilitare i messaggi di posta elettronica relativi al limite di utilizzo giornaliero

Per disabilitare i messaggi di posta elettronica relativi al limite di utilizzo giornaliero, nella sezione **Configura** della risorsa di Application Insights, dal riquadro **Utilizzo e costi stimati**, selezionare **Limite di utilizzo giornaliero**. Sono disponibili impostazioni per inviare messaggi di posta elettronica quando si raggiunge il limite e anche quando si raggiunge un limite di avviso modificabile. Se si vogliono disabilitare tutti i messaggi di posta elettronica relativi al volume del limite giornaliero, deselezionare entrambe le caselle.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Piano tariffario Enterprise (Per Node) legacy

Per chi ha adottato da tempo Azure Application Insights, sono ancora disponibili due piani tariffari: Basic ed Enterprise. Il piano tariffario Basic è identico a quello descritto sopra ed è il piano predefinito. Include tutte le funzionalità del piano Enterprise, senza alcun costo aggiuntivo. Nel piano Basic la fatturazione viene effettuata principalmente in base al volume di dati inseriti.

> [!NOTE]
> Questi piani tariffari legacy sono stati rinominati. Il piano tariffario Enterprise è ora denominato **Per Node** e il piano tariffario Basic **Per GB**. Questi nuovi nomi vengono usati di seguito e nel portale di Azure.  

Il piano Per Node (in precedenza Enterprise) prevede un addebito per nodo e una determinata quantità di dati giornaliera per ogni nodo. Nel piano tariffario Per Node vengono addebitati i dati inseriti in aggiunta alla quantità inclusa. Se si usa Operations Management Suite, è consigliabile scegliere il piano Per Node.

Per i prezzi correnti nella valuta e nell'area locali, vedere i [prezzi di Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> Nel mese di aprile 2018 è stato [introdotto](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) un nuovo modello di determinazione dei prezzi per Monitoraggio di Azure. Questo modello adotta un semplice modello di "pagamento in base al consumo" per tutto il portfolio dei servizi di monitoraggio. Altre informazioni sul [nuovo modello di determinazione dei prezzi](../platform/usage-estimated-costs.md), su come [valutare l'impatto del passaggio a questo modello](../platform/usage-estimated-costs.md#understanding-your-azure-monitor-costs) in base ai modelli di uso e su [come acconsentire esplicitamente al nuovo modello](../platform/usage-estimated-costs.md#azure-monitor-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Piano Per Node e diritti della sottoscrizione di Operations Management Suite

Come [annunciato di recente](/archive/blogs/msoms/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription), i clienti che acquistano Operations Management Suite E1 ed E2 possono ottenere Application Insights Per Node come componente aggiuntivo senza sostenere altri costi. Nello specifico, ogni unità di Operations Management Suite E1 ed E2 include il diritto a un nodo del piano Per Node di Application Insights. Ogni nodo di Application Insights include fino a 200 MB al giorno per l'inserimento di dati, che non comprendono l'inserimento di dati di Log Analytics, con un periodo di conservazione di 90 giorni senza costi aggiuntivi. Questo piano viene descritto in modo più dettagliato più avanti nell'articolo.

Poiché tale piano è applicabile solo ai clienti con una sottoscrizione Operations Management Suite, per i clienti che non la possiedono non viene visualizzata alcuna opzione per la selezione di questo piano.

> [!NOTE]
> Per assicurarsi di ottenere questo diritto, le risorse di Application Insights devono essere disponibili nel piano tariffario Per Node. Questo diritto viene applicato solo ai nodi. Le risorse Application Insights nel piano Per GB non ne traggono alcun vantaggio. Questo diritto non è visibile nei costi stimati indicati nel riquadro **Usage and estimated cost** (Uso e costi stimati). In aggiunta se si sposta una sottoscrizione al nuovo modello di determinazione dei prezzi di Monitoraggio di Azure ad aprile 2018, il piano Per GB sarà l'unico piano disponibile. Spostare una sottoscrizione al nuovo modello di determinazione dei prezzi di Monitoraggio di Azure non è consigliabile se si dispone di una sottoscrizione Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Funzionamento del livello Per Node

* Si paga per ogni nodo che invia dati di telemetria per le app nel piano Per Node.
  * Un *nodo* è un computer server fisico o virtuale oppure un'istanza del ruolo piattaforma distribuita come servizio che ospita l'app.
  * Computer di sviluppo, browser client e dispositivi mobili non sono conteggiati come nodi.
  * Se l'app ha diversi componenti che inviano dati di telemetria, ad esempio un servizio Web e un ruolo di lavoro back-end, i componenti vengono conteggiati separatamente.
  * I dati [Live Metrics Stream](./live-stream.md) non vengono conteggiati ai fini della determinazione del prezzo. In una sottoscrizione gli addebiti si applicano per nodo, non per app. Se si hanno cinque nodi che inviano dati di telemetria per 12 app, l'addebito sarà per cinque nodi.
* Sebbene gli addebiti siano fatturati mensilmente, quelli effettivi hanno luogo solo nelle ore durante le quali un nodo invia dati di telemetria da un'app. La tariffa oraria è la tariffa mensile fatturata divisa per 744 (il numero di ore in un mese di 31 giorni).
* Viene fornita un'allocazione di volume di dati di 200 MB al giorno per ogni nodo rilevato (con granularità oraria). L'allocazione di dati non usata non viene trasferita al giorno successivo.
  * Scegliendo il piano tariffario Per Node, ogni sottoscrizione ottiene una quantità di dati giornaliera inclusa in base al numero di nodi che inviano dati di telemetria alle risorse di Application Insights in quella sottoscrizione. Se si hanno quindi cinque nodi che inviano dati tutto il giorno, sarà applicata una quantità inclusa di 1 GB a tutte le risorse di Application Insights in quella sottoscrizione. Non è importante se determinati nodi inviano più dati di altri, perché i dati inclusi vengono condivisi tra tutti i nodi. Se un determinato giorno le risorse di Application Insights ricevono più dati di quelli inclusi nell'allocazione giornaliera per la sottoscrizione, si applicheranno gli addebiti di dati in eccedenza per GB. 
  * La quantità di dati giornaliera viene calcolata come numero di ore del giorno (fuso orario UTC) in cui ogni nodo invia dati di telemetria, diviso per 24 e moltiplicato per 200 MB. Se quindi si hanno quattro nodi che inviano dati di telemetria per 15 ore su 24 durante un giorno, i dati inclusi per tale giorno saranno ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Al prezzo di USD 2,30 per GB di eccedenza di dati, l'addebito sarà di USD 1,15 supponendo che i nodi quel giorno inviino 1 GB di dati.
  * La quantità giornaliera di dati inclusa nel piano Per Node non viene condivisa con le applicazioni per le quali si sceglie il piano Per GB. La quantità non usata non viene trasferita da un giorno all'altro.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Esempi di come determinare il conteggio di nodi specifico

| Scenario                               | Conteggio giornaliero totale dei nodi |
|:---------------------------------------|:----------------:|
| 1 applicazione che usa 3 istanze del Servizio app di Azure e 1 server virtuale | 4 |
| 3 applicazioni in esecuzione su 2 macchine virtuali. Le risorse di Application Insights per queste applicazioni si trovano nella stessa sottoscrizione e nel piano Per Node | 2 | 
| 4 applicazioni le cui risorse di Application Insights si trovano nella stessa sottoscrizione. Ogni applicazione esegue 2 istanze durante 16 ore non di punta e 4 istanze durante 8 ore di punta | 13.33 |
| Servizi cloud con ruolo di lavoro 1 e 1 ruolo Web, ognuno dei quali esegue 2 istanze | 4 | 
| Un cluster di Azure Service Fabric a 5 nodi che esegue 50 microservizi. Ogni microservizio esegue 3 istanze | 5|

* Il conteggio preciso dei nodi dipende dall'SDK di Application Insights usato dall'applicazione. 
  * Nell'SDK versioni 2.2 e successive sia [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) che [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) di Application Insights segnalano ogni host applicazioni come nodo. Ne sono esempi il nome computer del server fisico e degli host di VM o il nome dell'istanza per i servizi cloud.  L'unica eccezione è data da un'applicazione che usa solo [.NET Core](https://dotnet.github.io/) e Core SDK di Application Insights. In tal caso, viene segnalato solo un nodo per tutti gli host perché il nome host non è disponibile.
  * Per le versioni precedenti dell'SDK, [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) ha lo stesso comportamento delle nuove versioni dell'SDK, ma [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) segnala solamente un nodo, a prescindere dal numero degli host applicazioni.
  * Se l'applicazione usa l'SDK per impostare **roleInstance** su un valore personalizzato, per impostazione predefinita viene usato quello stesso valore per determinare il conteggio dei nodi.
  * Se si usa una nuova versione dell'SDK con un'app eseguita da computer client o dispositivi mobili, il conteggio dei nodi potrebbe restituire un numero elevato (a causa del numero elevato di computer client o dispositivi mobili).

## <a name="automation"></a>Automazione

È possibile scrivere uno script per impostare il piano tariffario tramite Azure Resource Manager. [Informazioni](powershell.md#price).

## <a name="next-steps"></a>Passaggi successivi

* [campionamento](./sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ./app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/

