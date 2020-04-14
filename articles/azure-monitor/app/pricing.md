---
title: Gestire l'utilizzo e i costi per Azure Application Insights | Microsoft Docs
description: Gestire volumi di dati di telemetria e monitorare i costi in Application Insights.
ms.topic: conceptual
author: DaleKoetke
ms.author: dalek
ms.date: 11/27/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9ecd0ffd76650efff3a4c9f877522cba6f28d080
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271115"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Gestire l'utilizzo e i costi per Application Insights

> [!NOTE]
> In questo articolo viene descritto come comprendere e controllare i costi per Application Insights.  Un articolo correlato, [Monitoring usage and estimated costs](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) descrive come visualizzare l'utilizzo e i costi stimati tra più funzionalità di monitoraggio di Azure per modelli di determinazione dei prezzi diversi.

Application Insights è progettato per ottenere tutto il necessario per monitorare la disponibilità, le prestazioni e l'utilizzo delle applicazioni Web, indipendentemente dal fatto che siano ospitate in Azure o in locale. Application Insights supporta linguaggi e framework più diffusi, ad esempio .NET, Java e Node.js, e si integra con processi e strumenti DevOps come Azure DevOps, Jira e PagerDuty. È importante capire cosa determina i costi del monitoraggio delle applicazioni. In questo articolo viene esaminato il livello di guida dei costi di monitoraggio delle applicazioni e il modo in cui è possibile monitorarli e controllarli in modo proattivo.

Per domande sulla determinazione dei prezzi per Application Insights, è possibile pubblicare una domanda nel [forum](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc).

## <a name="pricing-model"></a>Modello di prezzi

I prezzi per [Azure Application Insights][start] sono un modello con pagamento in base al **consumo** di dati ingerita e, facoltativamente, per una conservazione dei dati più lunga. Ogni risorsa di Application Insights viene addebitata come servizio separato e contribuisce alla fatturazione per la sottoscrizione di Azure. Il volume de dati viene misurato in base alle dimensioni del pacchetto di dati JSON non compresso inviato dall'applicazione ad Application Insights. Non è previsto alcun costo per il volume di dati per l'utilizzo di [Live Metrics Stream](../../azure-monitor/app/live-stream.md).

È prevista una tariffa aggiuntiva per i [test Web in più passi](../../azure-monitor/app/availability-multistep.md). I test Web in più passi sono test Web che eseguono una sequenza di azioni. Non è prevista una tariffa separata per i *test del ping* di una singola pagina. I dati di telemetria dei test del ping e dei test in più passi vengono addebitai allo stesso costo di altri dati di telemetria provenienti dall'app.

L'opzione Application Insights per [abilitare gli avvisi sulle dimensioni metriche personalizzate](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation) può anche generare costi aggiuntivi perché ciò può comportare la creazione di metriche di pre-aggregazione aggiuntive. [Altre informazioni] sulle metriche basate su log e preaggregate in Application Insights e sui prezzi per le metriche personalizzate di Monitoraggio di Azure.[Learn more] about log-based and pre-aggregated metrics in Application Insights and about [pricing](https://azure.microsoft.com/pricing/details/monitor/) for Azure Monitor custom metrics.

## <a name="estimating-the-costs-to-manage-your-application"></a>Stima dei costi per la gestione dell'applicazione

Se non si usa ancora Application Insights, è possibile usare il [calcolatore](https://azure.microsoft.com/pricing/calculator/?service=monitor) dei prezzi di Monitoraggio di Azure per stimare il costo dell'uso di Application Insights.If you're not yet using Application Insights, you can use the Azure Monitor pricing calculator to estimate the cost of using Application Insights. Iniziare immettendo "Monitor di Azure" nella casella di ricerca e facendo clic sul riquadro di Monitoraggio di Azure risultante. Scorrere la pagina verso il basso fino a Monitoraggio di Azure e selezionare Application Insights dall'elenco a discesa Tipo.Scroll down the page to Azure Monitor, and select Application Insights from the Type dropdown.  Qui è possibile immettere il numero di GB di dati che si prevede di raccogliere al mese, quindi la domanda è la quantità di dati che Application Insights raccoglierà il monitoraggio dell'applicazione.

Esistono due approcci per risolvere questo problema: l'uso del monitoraggio predefinito e del campionamento adattivo, disponibile nell'SDK ASP.NET, o stimare l'inserimento di dati probabili in base a ciò che altri clienti simili hanno visto.

### <a name="data-collection-when-using-sampling"></a>Raccolta dei dati quando si usa il campionamentoData collection when using sampling

Con il [campionamento adattivo](sampling.md#adaptive-sampling)di ASP.NET SDK , il volume di dati viene regolato automaticamente per mantenere entro una velocità massima di traffico specificata per il monitoraggio predefinito di Application Insights. Se l'applicazione produce una quantità bassa di dati di telemetria, ad esempio durante il debug o a causa di un utilizzo ridotto, gli elementi non verranno eliminati dal processore di campionamento finché il volume è al di sotto degli eventi configurati al secondo livello. Per un'applicazione con volumi elevati, con la soglia predefinita di cinque eventi al secondo, il campionamento adattivo limiterà il numero di eventi giornalieri a 432.000. Utilizzando una dimensione media tipica dell'evento di 1 KB, ciò corrisponde a 13,4 GB di telemetria per 31 giorni per ogni nodo che ospita l'applicazione (poiché il campionamento viene eseguito locale per ogni nodo). 

Per gli SDK che non supportano il [campionamento](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling)adattivo, è possibile utilizzare il campionamento di inserimento, che viene eseguito quando i dati vengono ricevuti da Application Insights in base a una percentuale di dati da conservare o il campionamento a [frequenza fissa per ASP.NET, ASP.NET core e i siti Web Java](sampling.md#fixed-rate-sampling) per ridurre il traffico inviato dal server Web e dai browser Web

### <a name="learn-from-what-similar-customers-collect"></a>Scopri da cosa raccolgono clienti simili

Nel calcolatore dei prezzi di monitoraggio di Azure per Application Insights, se si abilita la funzionalità "Stima del volume di dati in base all'attività dell'applicazione", è possibile fornire input sull'applicazione (richieste al mese e visualizzazioni di pagina al mese, nel caso in cui si raccoglieranno dati di telemetria lato client), quindi il calcolatore indicherà la quantità mediana e il novantasimo percentile di dati raccolti da applicazioni simili. Queste applicazioni comprendono la gamma di configurazione di Application Insights (ad esempio alcune hanno il [campionamento](../../azure-monitor/app/sampling.md)predefinito, alcune non hanno campionamento e così via), quindi è comunque possibile controllare per ridurre il volume dei dati che si ingerisce molto al di sotto del livello mediano tramite campionamento. Ma questo è un punto di partenza per capire cosa stanno vedendo altri clienti simili.

## <a name="understand-your-usage-and-estimate-costs"></a>Comprendere l'utilizzo e stimare i costi

Application Insights offre informazioni per comprendere con facilità i possibili costi associati in base a modelli di uso recente. Per iniziare, nel portale di Azure, per la risorsa di Application Insights passare alla pagina **Utilizzo e costi stimati**:

![Scegliere i prezzi](./media/pricing/pricing-001.png)

R. Esaminare il volume di dati per il mese. Sono inclusi tutti i dati ricevuti e conservati (dopo un [campionamento](../../azure-monitor/app/sampling.md)) dal server e dalle app client, oltre che dai test di disponibilità.  
B. I costi per i [test Web in più passaggi](../../azure-monitor/app/availability-multistep.md) vengono addebitati separatamente. Non sono inclusi i test di disponibilità semplici, il cui costo viene addebitato con il volume di dati.  
C. Visualizzare le tendenze del volume dei dati relative all'ultimo mese.  
D. Abilitare il [campionamento](../../azure-monitor/app/sampling.md) per l'inserimento dei dati.
E. Impostare il limite giornaliero di utilizzo per volume dati.  

(Si noti che tutti i prezzi visualizzati nelle schermate in questo articolo sono solo a scopo esemplificativo. Per i prezzi correnti nella valuta e nell'area geografica, vedere Prezzi di [Application Insights.)][pricing]

Per analizzare l'utilizzo di Application Insights a un livello più dettagliato, aprire la pagina **Metriche**, aggiungere la metrica denominata "Volume del punto dati" e quindi selezionare l'opzione *Applica suddivisione* per suddividere i dati in base al tipo di elemento di telemetria.

Gli addebiti di Application Insights vengono aggiunti alla fatturazione di Azure. È possibile visualizzare i dettagli della fattura di Azure nella sezione **Fatturazione** del portale di Azure oppure nel [portale di fatturazione di Azure](https://account.windowsazure.com/Subscriptions).

![Nel menu a sinistra selezionare Fatturazione](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Utilizzo delle metriche del volume di dati
<a id="understanding-ingested-data-volume"></a>

Per altre informazioni sui volumi di dati, selezionare **Metriche** per la risorsa Application Insights, aggiungere un nuovo grafico. Per la metrica del grafico, in **Metriche basate su log**selezionare **Volume punto dati**. Fare clic su **Applica divisione**e selezionare raggruppa per ** `Telemetryitem` tipo**.

![Usare le metriche per esaminare il volume dei datiUse Metrics to look at data volume](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Query per comprendere i dettagli del volume di dati

Esistono due approcci per analizzare i volumi di dati per Application Insights.There are two approaches to investigating data volumes for Application Insights. Il primo utilizza informazioni `systemEvents` aggregate nella tabella e `_BilledSize` il secondo utilizza la proprietà , che è disponibile in ogni evento ingere.

#### <a name="using-aggregated-data-volume-information"></a>Utilizzo delle informazioni aggregate sul volume di dati

Ad esempio, è `systemEvents` possibile utilizzare la tabella per visualizzare il volume di dati ingerita nelle ultime 24 ore con la query:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

In alternativa, per visualizzare un grafico del volume di dati (in byte) per tipo di dati negli ultimi 30 giorni, è possibile utilizzare:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Si noti che questa query può essere usata in un avviso di log di [Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) per configurare gli avvisi sui volumi di dati.  

Per altre informazioni sulle modifiche ai dati di telemetria, è possibile ottenere il conteggio degli eventi per tipo usando la query:To learn more about your telemetry data changes, we can get the count of events by type using the query:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Utilizzo delle dimensioni dei dati per ogni evento

Per ulteriori informazioni sull'origine dei volumi di `_BilledSize` dati, è possibile usare la proprietà presente in ogni evento ingereto.

Ad esempio, per esaminare quali operazioni generano il maggior numero `_BilledSize` di volumi di dati negli ultimi 30 giorni, è possibile sommare tutti gli eventi di dipendenza:For example, to look at which operations generate the most data volume in the last 30 days, we can sum for all dependency events:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Visualizzazione dell'utilizzo di Application Insights nella fattura di AzureViewing Application Insights usage on your Azure bill

Azure offre una grande quantità di funzionalità utili nell'hub [Gestione costi di Azure e fatturazione.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Ad esempio, la funzionalità "Analisi dei costi" consente di visualizzare le spese per le risorse di Azure.For instance, the "Cost analysis" functionality enables you to view your spends for Azure resources. L'aggiunta di un filtro per tipo di risorsa (a microsoft.insights/components per Application Insights) consentirà di tenere traccia della spesa.

È possibile ottenere una maggiore comprensione dell'utilizzo [scaricando l'utilizzo dal portale](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)di Azure.
Nel foglio di calcolo scaricato è possibile visualizzare l'utilizzo per risorsa di Azure al giorno. In questo foglio di calcolo di Excel, l'utilizzo delle risorse di Application Insights è reperibile filtrando prima la colonna "Meter Category" per visualizzare "Application Insights" e "Log Analytics" e quindi aggiungendo un filtro nella colonna "Instance ID" che è "contains microsoft.insights/components".  La maggior parte dell'utilizzo di Application Insights viene segnalata nei contatori con la categoria di contatori di Log Analytics, poiché è presente un singolo back-end di log per tutti i componenti di Monitoraggio di Azure.Most Application Insights usage is reported on meters with the Meter Category of Log Analytics, since there is a single logs backend for all Azure Monitor components.  Solo le risorse di Application Insights nei piani tariffari legacy e nei test Web in più passaggi vengono segnalate con una categoria di contatori di Application Insights.Only Application Insights resources on legacy pricing tiers and multi-step web tests are reported with a Meter Category of Application Insights.  L'utilizzo viene visualizzato nella colonna "Quantità consumata" e l'unità per ogni voce viene visualizzata nella colonna "Unità di misura".  Sono disponibili ulteriori dettagli per comprendere la fattura di [Microsoft Azure.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)

## <a name="managing-your-data-volume"></a>Gestione del volume di dati

Il volume di dati inviati può essere gestito utilizzando le seguenti tecniche:

* **Campionamento:** è possibile usare il campionamento per ridurre la quantità di dati di telemetria inviati dal server e dalle app client, con una distorsione minima delle metriche. Si tratta dello strumento principale da usare per ottimizzare la quantità di dati inviati. Altre informazioni sulle [funzionalità di campionamento](../../azure-monitor/app/sampling.md).

* **Limite chiamate Ajax**: È possibile [limitare il numero di chiamate Ajax che possono essere segnalate](../../azure-monitor/app/javascript.md#configuration) in ogni visualizzazione di pagina o disattivare la creazione di report Ajax.

* **Disabilitare i moduli non necessari:** [modificare ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) per disattivare i moduli di raccolta non necessari. Ad esempio, è possibile che i contatori delle prestazioni o dati sulle dipendenze siano non essenziali.

* **Metriche preaggregate:** se inserisci chiamate a TrackMetric nella tua app, puoi ridurre il traffico usando il sovraccarico che accetta il calcolo della deviazione media e standard di un batch di misurazioni. In alternativa è possibile usare un [pacchetto di pre-aggregazione](https://www.myget.org/gallery/applicationinsights-sdk-labs).
 
* **Limite di utilizzo giornaliero:** quando si crea una risorsa di Application Insights nel portale di Azure, il limite di utilizzo giornaliero è impostato su 100 GB al giorno. Il valore predefinito quando si crea una risorsa di Application Insights in Visual Studio è basso, solo 32,3 MB al giorno. Il valore predefinito del limite di utilizzo giornaliero è impostato per semplificare le operazioni di test. È opportuno che l'utente aumenti il limite di utilizzo giornaliero prima di distribuire l'app nell'ambiente di produzione. 

    Il limite di utilizzo massimo è 1.000 GB al giorno, a meno che non si richieda un valore massimo più alto per un'applicazione con traffico elevato.
    
    I messaggi di posta elettronica di avviso relativi al limite giornaliero vengono inviati all'account che sono membri di questi ruoli per la risorsa Application Insights: "ServiceAdmin", "AccountAdmin", "CoAdmin", "Owner".

    Prestare attenzione quando si imposta il limite di utilizzo giornaliero. Lo scopo dovrebbe essere quello di *non raggiungere mai il limite di utilizzo giornaliero*. Se si raggiunge il limite di utilizzo giornaliero, i dati della restante parte della giornata andranno persi e non sarà possibile monitorare l'applicazione. Per modificare il limite di utilizzo giornaliero, usare l'opzione **Limite di utilizzo volume giornaliero**. È possibile accedere a questa opzione nel riquadro **Usage and estimated cost** (Uso e costi stimati), descritto in dettaglio più avanti in questo articolo.
    
    La restrizione è stata rimossa per alcuni tipi di sottoscrizione con un credito che non poteva essere usato per Application Insights. In precedenza, se alla sottoscrizione era associato un limite di spesa, nella finestra di dialogo relativa al limite di utilizzo giornaliero erano presenti istruzioni per rimuoverlo e abilitare l'aumento del limite di utilizzo giornaliero oltre 32,3 MB al giorno.
    
* **Limitazione:** limita la velocità dei dati a 32.000 eventi al secondo, in base a una media calcolata a intervalli di 1 minuto per chiave di strumentazione. Il volume dei dati inviati dall'app viene valutato ogni minuto. Se il valore supera la velocità media al secondo calcolata nel minuto, il server rifiuta alcune richieste. L'SDK memorizza nel buffer i dati e quindi tenta di rinviarli. Segnala un sovraccarico per alcuni minuti. Se l'app continua a inviare dati che eccedono il valore della limitazione, alcuni di questi verranno eliminati. (Le ASP.NET, Java e JavaScript SDK tentano di inviare nuovamente i dati in questo modo; altri SDK potrebbero semplicemente eliminare i dati limitati.) Se si verifica la limitazione, viene visualizzato un avviso di notifica che si è verificato.

## <a name="manage-your-maximum-daily-data-volume"></a>Gestisci il tuo volume massimo di dati giornaliero

È possibile usare il limite di utilizzo giornaliero di volume per limitare i dati raccolti. Tuttavia se si raggiunge questo limite, tutti i dati di telemetria inviati dall'applicazione per la restante parte del giorno andranno persi. *Non è consigliabile* raggiungere il limite di utilizzo giornaliero dell'applicazione. Dopo averlo raggiunto non sarà possibile monitorare l'integrità e le prestazioni dell'applicazione.

Anziché usare il limite di volume giornaliero, usare il [campionamento](../../azure-monitor/app/sampling.md) per impostare il volume di dati al livello desiderato. Usare quindi il limite di utilizzo giornaliero solo se "strettamente necessario" nel caso in cui l'applicazione inizi a inviare volumi più elevati di dati di telemetria in modo imprevisto.

### <a name="identify-what-daily-data-limit-to-define"></a>Identificare la soglia dei dati giornaliera da definire

Esaminare Application Insights Usage e i costi stimati per comprendere la tendenza all'inserimento dei dati e il limite di volume giornaliero da definire. Deve essere considerato con attenzione, poiché non sarà possibile monitorare le risorse dopo il raggiungimento del limite.

### <a name="set-the-daily-cap"></a>Impostare il limite giornaliero

Per modificare il limite giornaliero, nella sezione **Configura** della risorsa Application Insights selezionare **Limite giornaliero**nella pagina **Utilizzo e costi stimati.**

![Regolazione del limite del volume dei dati di telemetria giornaliero](./media/pricing/pricing-003.png)

Per [modificare il limite giornaliero tramite Azure Resource Manager,](../../azure-monitor/app/powershell.md)la proprietà da modificare è la `dailyQuota`proprietà .  Tramite Azure Resource Manager `dailyQuotaResetTime` è anche possibile `warningThreshold`impostare il e il limite giornaliero di .

### <a name="create-alerts-for-the-daily-cap"></a>Creare avvisi per il limite giornaliero

Il limite massimo giornaliero di Application Insights crea un evento nel log attività di Azure quando i volumi di dati ingeriti raggiunge il livello di avviso o il livello di limite giornaliero.  È possibile creare un avviso in base a [questi eventi del log attività.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log#create-with-the-azure-portal) I nomi dei segnali per questi eventi sono:

* Raggiunta la soglia di avviso del limite giornaliero del componente Application Insights

* Raggiunto il limite giornaliero del componente Application Insights

## <a name="sampling"></a>campionamento
Il [campionamento](../../azure-monitor/app/sampling.md) è un metodo che consente di ridurre la velocità con cui i dati di telemetria vengono inviati all'app, pur mantenendo la possibilità di trovare gli eventi correlati durante le ricerche di diagnostica e il conteggio corretto degli eventi.

Il campionamento consente di ridurre in modo efficace i costi e di non superare la quota mensile. L'algoritmo di campionamento conserva gli elementi correlati ai dati di telemetria, in modo che, quando si usa la ricerca, ad esempio, è possibile trovare la richiesta correlata a una particolare eccezione. L'algoritmo mantiene anche i conteggi corretti e consente di visualizzare in Esplora metriche i valori corretti della frequenza delle richieste, della frequenza delle eccezioni e di altri contatori.

Sono disponibili diversi tipi di campionamento.

* Il [campionamento adattivo](../../azure-monitor/app/sampling.md) è il valore predefinito per l'SDK di ASP.NET. Il campionamento adattivo si adatta automaticamente al volume dei dati di telemetria inviati dall'app. Viene eseguito automaticamente nell'SDK dell'app Web riducendo il traffico dei dati di telemetria sulla rete. 
* *campionamento per inserimento* è un'alternativa che opera nel punto in cui i dati di telemetria provenienti dall'app raggiungono il servizio Application Insights. Il campionamento per inserimento non ha alcun effetto sul volume dei dati di telemetria inviati dall'app, ma riduce il volume mantenuto dal servizio. È possibile usarlo per ridurre la quota che i dati di telemetria usano dal browser e da altri SDK.

Per impostare il campionamento per inserimento, visitare il riquadro **Prezzi**:

![Nel riquadro Quota and pricing (Quota e tariffe) fare clic sul riquadro Campioni e quindi selezionare una frazione di campionamento](./media/pricing/pricing-004.png)

> [!WARNING]
> Il riquadro **Campionamento dei dati** controlla solo il valore del campionamento per inserimento. Non riflette la frequenza di campionamento applicata dall'SDK di Application Insights nell'app. Se i dati di telemetria in ingresso sono già stati campionati nell'SDK, il campionamento per inserimento non verrà applicato.
>

Per individuare la frequenza di campionamento effettiva indipendentemente dal punto in cui è stata applicata, usare una [query di Analytics](analytics.md). La query appare come la seguente:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

In ogni record conservato, `itemCount` indica il numero di record originali che rappresenta, uguale a 1 + il numero di record precedenti scartati.

## <a name="change-the-data-retention-period"></a>Cambiare il periodo di conservazione dei dati

La conservazione predefinita per le risorse di Application Insights è 90 giorni. È possibile selezionare periodi di conservazione diversi per ogni risorsa di Application Insights.Different retention periods can be selected for each Application Insights resource. Il set completo di periodi di conservazione disponibili è 30, 60, 90, 120, 180, 270, 365, 550 o 730 giorni.

Per modificare la conservazione, dalla risorsa Application Insights passare alla pagina **Utilizzo e costi stimati** e selezionare l'opzione Conservazione dati:To change the **retention,** from your Application Insights resource, go to the Usage and Estimated Costs page and select the Data Retention option:

![Regolazione del limite del volume dei dati di telemetria giornaliero](./media/pricing/pricing-005.png)

La conservazione può anche essere impostata a `retentionInDays` livello di codice utilizzando [PowerShell](powershell.md#set-the-data-retention) utilizzando il parametro . Quando la conservazione viene abbassata, è presente un periodo di prova di diversi giorni prima che i dati meno recenti vengano rimossi. Se si imposta la conservazione dei dati su 30 giorni, `immediatePurgeDataOn30Days` è possibile attivare un'eliminazione immediata dei dati meno recenti utilizzando il parametro , che può essere utile per gli scenari correlati alla conformità. Questa funzionalità di eliminazione viene esposta solo tramite Azure Resource Manager e deve essere usata con estrema attenzione. Il tempo di reimpostazione giornaliero per il limite `dailyQuotaResetTime` del volume di dati può essere configurato usando Azure Resource Manager per impostare il parametro.

## <a name="data-transfer-charges-using-application-insights"></a>Costi di trasferimento dei dati tramite Application InsightsData

L'invio di dati ad Application Insights potrebbe comportare costi di larghezza di banda dei dati. Come descritto nella [pagina dei prezzi della larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/)di Azure , il trasferimento dei dati tra i servizi di Azure situati in due aree addebitate come trasferimento di dati in uscita alla velocità normale. Il trasferimento dei dati in ingresso è gratuito. Tuttavia, questa tassa è molto piccola (pochi %) rispetto ai costi per l'inserimento dei dati di log di Application Insights. Di conseguenza, il controllo dei costi per Log Analytics deve concentrarsi sul volume di dati ingeriti e abbiamo indicazioni per aiutarti a capire che [qui](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume).

## <a name="limits-summary"></a>Riepilogo dei limiti

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Disabilitare i messaggi di posta elettronica relativi al limite di utilizzo giornaliero

Per disabilitare i messaggi di posta elettronica relativi al limite di utilizzo giornaliero, nella sezione **Configura** della risorsa di Application Insights, dal riquadro **Utilizzo e costi stimati**, selezionare **Limite di utilizzo giornaliero**. Sono disponibili impostazioni per inviare messaggi di posta elettronica quando si raggiunge il limite e anche quando si raggiunge un limite di avviso modificabile. Se si desidera disabilitare tutte le e-mail relative al volume del limite giornaliero, deselezionare entrambe le caselle.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Piano tariffario Legacy Enterprise (Per Node)

Per i primi utenti di Azure Application Insights, sono ancora disponibili due possibili piani tariffari: Basic ed Enterprise. Il piano tariffario Di base è lo stesso descritto in precedenza ed è il livello predefinito. Include tutte le funzionalità di livello Enterprise, senza costi aggiuntivi. Il livello Basic fattura principalmente il volume di dati che viene ingerito.

> [!NOTE]
> Questi piani tariffari legacy sono stati rinominati. Il piano tariffario Enterprise è ora denominato **Per nodo** e il piano tariffario Di base è ora denominato **Per GB**. Questi nuovi nomi vengono usati di seguito e nel portale di Azure.These new names are used below and in the Azure portal.  

Il livello Per nodo (in precedenza Enterprise) ha un addebito per nodo e ogni nodo riceve una concessione giornaliera di dati. Nel piano tariffario Per nodo vengono addebitati i dati inseriti al di sopra della franchigia inclusa. Se si utilizza Operations Management Suite, è necessario scegliere il livello Per nodo.

Per i prezzi correnti nella valuta e nell'area locali, consultare [Prezzi di Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> Nel mese di aprile 2018 è stato [introdotto](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) un nuovo modello di determinazione dei prezzi per Monitoraggio di Azure. Questo modello adotta un semplice modello di "pagamento in base al consumo" per tutto il portfolio dei servizi di monitoraggio. Ulteriori informazioni sul nuovo modello di [determinazione dei prezzi,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs)su come [valutare l'impatto del passaggio a questo modello](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#understanding-your-azure-monitor-costs) in base ai modelli di utilizzo e su come [attivare il nuovo modello](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#azure-monitor-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Per ogni livello di nodo e diritti di sottoscrizione di Operations Management Suite

I clienti che acquistano Operations Management Suite E1 ed E2 possono ottenere Application Insights Per Node come componente aggiuntivo senza costi aggiuntivi, come [annunciato in precedenza.](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/) In particolare, ogni unità di Operations Management Suite E1 ed E2 include un diritto a un nodo del livello Application Insights Per Nodo. Ogni nodo di Application Insights include fino a 200 MB al giorno per l'inserimento di dati, che non comprendono l'inserimento di dati di Log Analytics, con un periodo di conservazione di 90 giorni senza costi aggiuntivi. Il livello è descritto in modo più dettagliato più avanti nell'articolo.

Poiché questo livello è applicabile solo ai clienti con una sottoscrizione di Operations Management Suite, i clienti che non dispongono di una sottoscrizione a Operations Management Suite non visualizzano un'opzione per selezionare questo livello.

> [!NOTE]
> Per garantire l'ottazione di questo diritto, le risorse di Application Insights devono trovarsi nel piano tariffario Per nodo. Questo diritto viene applicato solo ai nodi. Le risorse di Application Insights nel livello Per GB non traggono alcun vantaggio. Questo diritto non è visibile nei costi stimati indicati nel riquadro **Usage and estimated cost** (Uso e costi stimati). Inoltre, se si sposta una sottoscrizione al nuovo modello di determinazione dei prezzi di monitoraggio di Azure nell'aprile 2018, il livello Per GB è l'unico livello disponibile. Spostare una sottoscrizione al nuovo modello di determinazione dei prezzi di Monitoraggio di Azure non è consigliabile se si dispone di una sottoscrizione Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Funzionamento del livello Per nodo

* Si paga per ogni nodo che invia dati di telemetria per tutte le app nel livello Per nodo.
  * Un *nodo* è una macchina fisica o virtuale server o un'istanza del ruolo platform-as-a-service che ospita l'app.
  * Computer di sviluppo, browser client e dispositivi mobili non sono conteggiati come nodi.
  * Se l'app ha diversi componenti che inviano dati di telemetria, ad esempio un servizio Web e un ruolo di lavoro back-end, i componenti vengono conteggiati separatamente.
  * I dati [Live Metrics Stream](../../azure-monitor/app/live-stream.md) non vengono conteggiati ai fini della determinazione del prezzo. In una sottoscrizione gli addebiti si applicano per nodo, non per app. Se si hanno cinque nodi che inviano dati di telemetria per 12 app, l'addebito sarà per cinque nodi.
* Sebbene gli addebiti siano fatturati mensilmente, quelli effettivi hanno luogo solo nelle ore durante le quali un nodo invia dati di telemetria da un'app. La tariffa oraria è la tariffa mensile fatturata divisa per 744 (il numero di ore in un mese di 31 giorni).
* Viene fornita un'allocazione di volume di dati di 200 MB al giorno per ogni nodo rilevato (con granularità oraria). L'allocazione di dati non usata non viene trasferita al giorno successivo.
  * Se si sceglie il piano tariffario Per nodo, ogni sottoscrizione ottiene una richiesta giornaliera di dati in base al numero di nodi che inviano dati di telemetria alle risorse di Application Insights in tale sottoscrizione. Se si hanno quindi cinque nodi che inviano dati tutto il giorno, sarà applicata una quantità inclusa di 1 GB a tutte le risorse di Application Insights in quella sottoscrizione. Non è importante se determinati nodi inviano più dati di altri, perché i dati inclusi vengono condivisi tra tutti i nodi. Se in un determinato giorno le risorse di Application Insights ricevono più dati di quelli inclusi nell'allocazione giornaliera dei dati per questa sottoscrizione, si applicano gli addebiti per i dati di evasità per GB. 
  * La quantità di dati giornaliera viene calcolata come numero di ore del giorno (fuso orario UTC) in cui ogni nodo invia dati di telemetria, diviso per 24 e moltiplicato per 200 MB. Se quindi si hanno quattro nodi che inviano dati di telemetria per 15 ore su 24 durante un giorno, i dati inclusi per tale giorno saranno ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Al prezzo di USD 2,30 per GB di eccedenza di dati, l'addebito sarà di USD 1,15 supponendo che i nodi quel giorno inviino 1 GB di dati.
  * L'indennità giornaliera per livello per nodo non è condivisa con le applicazioni per le quali è stato scelto il livello Per GB. La quantità non usata non viene trasferita da un giorno all'altro.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Esempi di come determinare il conteggio di nodi specifico

| Scenario                               | Conteggio giornaliero totale dei nodi |
|:---------------------------------------|:----------------:|
| 1 applicazione che usa 3 istanze del Servizio app di Azure e 1 server virtuale | 4 |
| 3 applicazioni in esecuzione su 2 macchine virtuali; le risorse di Application Insights per queste applicazioni si trovano nella stessa sottoscrizione e nel livello Per nodo | 2 | 
| 4 applicazioni le cui risorse di Application Insights si trovano nella stessa sottoscrizione. Ogni applicazione esegue 2 istanze durante 16 ore non di punta e 4 istanze durante 8 ore di punta | 13.33 |
| Servizi cloud con ruolo di lavoro 1 e 1 ruolo Web, ognuno dei quali esegue 2 istanze | 4 | 
| Un cluster di Azure Service Fabric a 5 nodi che esegue 50 microservizi. Ogni microservizio esegue 3 istanze | 5|

* Il conteggio preciso dei nodi dipende dall'SDK di Application Insights usato dall'applicazione. 
  * Nell'SDK versioni 2.2 e successive sia [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) che [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) di Application Insights segnalano ogni host applicazioni come nodo. Ne sono esempi il nome computer del server fisico e degli host di VM o il nome dell'istanza per i servizi cloud.  L'unica eccezione è data da un'applicazione che usa solo [.NET Core](https://dotnet.github.io/) e Core SDK di Application Insights. In tal caso, viene segnalato solo un nodo per tutti gli host perché il nome host non è disponibile.
  * Per le versioni precedenti dell'SDK, [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) ha lo stesso comportamento delle nuove versioni dell'SDK, ma [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) segnala solamente un nodo, a prescindere dal numero degli host applicazioni.
  * Se l'applicazione usa l'SDK per impostare **roleInstance** su un valore personalizzato, per impostazione predefinita viene usato quello stesso valore per determinare il conteggio dei nodi.
  * Se usi una nuova versione SDK con un'app eseguita da computer client o dispositivi mobili, il numero di nodi potrebbe restituire un numero di grandi dimensioni (a causa del numero elevato di computer client o dispositivi mobili).

## <a name="automation"></a>Automazione

È possibile scrivere uno script per impostare il piano tariffario usando Gestione risorse di Azure.You can write a script to set the pricing tier by using Azure Resource Management. [Informazioni](powershell.md#price).

## <a name="next-steps"></a>Passaggi successivi

* [campionamento](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/