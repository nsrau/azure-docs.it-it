---
title: Gestire l'utilizzo e i costi per Azure Application Insights | Microsoft Docs
description: Gestire volumi di dati di telemetria e monitorare i costi in Application Insights.
services: application-insights
documentationcenter: ''
author: DaleKoetke
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 10/03/2019
ms.author: dalek
ms.openlocfilehash: f9d92f03b1f55ad9d1f1e272886095ae48033266
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750388"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Gestire l'utilizzo e i costi per Application Insights

> [!NOTE]
> Questo articolo descrive come comprendere e controllare i costi per Application Insights.  Un articolo correlato, [monitoraggio dell'utilizzo e dei costi stimati](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) descrive come visualizzare l'utilizzo e i costi stimati tra più funzionalità di monitoraggio di Azure per i diversi modelli tariffari.

Application Insights è progettato per ottenere tutto il necessario per monitorare la disponibilità, le prestazioni e l'utilizzo delle applicazioni Web, indipendentemente dal fatto che siano ospitate in Azure o in locale. Application Insights supporta i linguaggi e i Framework più diffusi, ad esempio .NET, Java e node. js, e si integra con i processi e gli strumenti DevOps come Azure DevOps, JIRA e PagerDuty. È importante comprendere cosa determina i costi di monitoraggio delle applicazioni. In questo articolo vengono esaminati i costi di monitoraggio delle applicazioni e il modo in cui è possibile monitorarli e controllarli in modo proattivo.

Per domande sulla determinazione dei prezzi per Application Insights, è possibile pubblicare una domanda nel [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc).

## <a name="pricing-model"></a>Modello tariffario

Il prezzo per [applicazione Azure Insights][start] è un modello con **pagamento in** base al consumo basato sul volume di dati inserito e, facoltativamente, per la conservazione dei dati più lunga. Ogni risorsa di Application Insights viene addebitata come servizio separato e contribuisce alla fatturazione per la sottoscrizione di Azure. Il volume de dati viene misurato in base alle dimensioni del pacchetto di dati JSON non compresso inviato dall'applicazione ad Application Insights. Non è previsto alcun addebito per il volume di dati per l'utilizzo del [Live Metrics Stream](../../azure-monitor/app/live-stream.md).

È prevista una tariffa aggiuntiva per i [test Web in più passi](../../azure-monitor/app/availability-multistep.md). I test Web in più passi sono test Web che eseguono una sequenza di azioni. Non è prevista una tariffa separata per i *test del ping* di una singola pagina. I dati di telemetria dei test del ping e dei test in più passi vengono addebitai allo stesso costo di altri dati di telemetria provenienti dall'app.

## <a name="estimating-the-costs-to-manage-your-application"></a>Stima dei costi per la gestione dell'applicazione 

Se non si usa ancora Application Insights, è possibile usare il [calcolatore dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/calculator/?service=monitor) per stimare il costo dell'uso di Application Insights. Per iniziare, immettere "monitoraggio di Azure" nella casella di ricerca e fare clic sul riquadro Monitoraggio di Azure risultante. Scorrere la pagina verso il basso fino a monitoraggio di Azure e selezionare Application Insights dall'elenco a discesa tipo.  Qui è possibile immettere il numero di GB di dati che si prevede di raccogliere al mese, in modo che si metta in discussione la quantità di dati che Application Insights raccogliere il monitoraggio dell'applicazione. 

Esistono due approcci per risolvere questo problema: usare il monitoraggio predefinito e il campionamento adattivo, disponibile in ASP.NET SDK, o stimare il probabile inserimento di dati in base agli altri clienti simili che hanno visto. 

### <a name="data-collection-when-using-sampling"></a>Raccolta dati quando si usa il campionamento

Con il [campionamento adattivo](https://docs.microsoft.com/azure/azure-monitor/app/sampling#adaptive-sampling-in-your-aspnetaspnet-core-web-applications)di ASP.NET SDK, il volume di dati viene regolato automaticamente per rimanere entro una frequenza massima specificata di traffico per il monitoraggio predefinito Application Insights. Se l'applicazione produce una quantità ridotta di dati di telemetria, ad esempio durante il debug o a causa di un utilizzo ridotto, gli elementi non verranno eliminati dal processore di campionamento, purché il volume sia inferiore al livello di eventi configurati al secondo. Per un'applicazione con volume elevato, con la soglia predefinita di 5 eventi al secondo, il campionamento adattivo limiterà il numero di eventi giornalieri a 432.000. Se si usa una dimensione di evento Media tipica di 1 KB, corrisponde a 13,4 GB di dati di telemetria per ogni mese di 31 giorni per nodo che ospita l'applicazione (poiché il campionamento viene eseguito localmente in ogni nodo). 

Per gli SDK che non supportano il campionamento adattivo, è possibile usare il [campionamento](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling) per inserimento, che include esempi quando i dati vengono ricevuti da Application Insights in base a una percentuale di dati da conservare o [campionamento a frequenza fissa per ASP.NET, ASP.NET Core e Java siti](https://docs.microsoft.com/azure/azure-monitor/app/sampling#fixed-rate-sampling-for-aspnet-aspnet-core-java-websites-and-python-applications) Web per ridurre il traffico inviato dal server Web e dai Web browser

### <a name="learn-from-what-similar-customers-collect"></a>Impara da ciò che i clienti simili raccolgono

Nel calcolatore dei prezzi di monitoraggio di Azure per Application Insights, se si Abilita la funzionalità "stima del volume di dati in base all'attività dell'applicazione", è possibile fornire input sull'applicazione (richieste al mese e visualizzazioni di pagina al mese, nel caso in cui venga raccogliere dati di telemetria sul lato client) e quindi il calcolatore indica la quantità mediana e 90 ° percentile di dati raccolti da applicazioni simili. Naturalmente, queste applicazioni si estendono nell'intervallo di Application Insights configurazione (ad esempio, il [campionamento](../../azure-monitor/app/sampling.md)predefinito, alcune non hanno campionamento e così via), quindi è ancora possibile ridurre il volume di dati inseriti al di sotto del livello mediano usando il campionamento. Tuttavia, si tratta di un punto di partenza per comprendere quali altri clienti simili vengono visualizzati. 

## <a name="understand-your-usage-and-estimate-costs"></a>Comprendere i costi di utilizzo e stima

Application Insights offre informazioni per comprendere con facilità i possibili costi associati in base a modelli di uso recente. Per iniziare, nel portale di Azure, per la risorsa di Application Insights passare alla pagina **Utilizzo e costi stimati**: 

![Scegliere i prezzi](./media/pricing/pricing-001.png)

R. Esaminare il volume di dati per il mese. Sono inclusi tutti i dati ricevuti e conservati (dopo un [campionamento](../../azure-monitor/app/sampling.md)) dal server e dalle app client, oltre che dai test di disponibilità.  
B. I costi per i [test Web in più passaggi](../../azure-monitor/app/availability-multistep.md) vengono addebitati separatamente. Non sono inclusi i test di disponibilità semplici, il cui costo viene addebitato con il volume di dati.  
C. Visualizzare le tendenze del volume dei dati relative all'ultimo mese.  
D. Abilitare il [campionamento](../../azure-monitor/app/sampling.md) per l'inserimento dei dati.   
E. Impostare il limite giornaliero di utilizzo per volume dati.  

Si noti che tutti i prezzi visualizzati nelle schermate in questo articolo sono solo a scopo esemplificativo. Per i prezzi correnti nella valuta e nell'area geografica, vedere [Application Insights prezzi][pricing].

Per analizzare l'utilizzo di Application Insights a un livello più dettagliato, aprire la pagina **Metriche**, aggiungere la metrica denominata "Volume del punto dati" e quindi selezionare l'opzione *Applica suddivisione* per suddividere i dati in base al tipo di elemento di telemetria. 

Gli addebiti di Application Insights vengono aggiunti alla fatturazione di Azure. È possibile visualizzare i dettagli della fattura di Azure nella sezione **Fatturazione** del portale di Azure oppure nel [portale di fatturazione di Azure](https://account.windowsazure.com/Subscriptions). 

![Nel menu a sinistra selezionare Fatturazione](./media/pricing/02-billing.png)

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Visualizzazione dell'utilizzo Application Insights nella fattura di Azure 

Azure fornisce una grande quantità di funzionalità utili in [Gestione costi di Azure](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) e nell'hub di fatturazione. Ad esempio, la funzionalità di analisi dei costi consente di visualizzare le spese per le risorse di Azure. L'aggiunta di un filtro per tipo di risorsa (a Microsoft. Insights/Components per Application Insights) consentirà di tenere traccia della spesa.

Una maggiore comprensione dell'utilizzo può essere ottenuta [scaricando l'utilizzo dal portale di Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Nel foglio di calcolo scaricato è possibile visualizzare l'utilizzo per ogni risorsa di Azure al giorno. In questo foglio di calcolo di Excel, l'utilizzo delle risorse del Application Insights è disponibile per primo, applicando un filtro alla colonna "categoria contatore" per visualizzare "Application Insights" e "Log Analytics", quindi aggiungendo un filtro alla colonna "ID istanza" che contiene Microsoft. Insights/Components ".  La maggior parte delle Application Insights utilizzo viene segnalata in contatori con la categoria di contatori di Log Analytics, dal momento che è disponibile un unico back-end per tutti i componenti di monitoraggio di Azure.  Solo Application Insights risorse sui piani tariffari legacy e i test Web in più passaggi vengono segnalati con una categoria di contatori di Application Insights.  L'utilizzo viene visualizzato nella colonna "quantità utilizzata" e l'unità per ogni voce viene visualizzata nella colonna "unità di misura".  Sono disponibili altre informazioni che consentono di [comprendere la fattura Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="managing-your-data-volume"></a>Gestione del volume di dati 

Per conoscere la quantità di dati inviati dall'app, è possibile:

* Aprire il riquadro **Usage and estimated cost** (Uso e costi stimati) per visualizzare il grafico dei volumi di dati giornalieri. 
* In Esplora metriche aggiungere un nuovo grafico. Per la metrica del grafico, selezionare **Volume dei punti dati**. Attivare **Raggruppamento** e quindi raggruppare in base al **Tipo di dati**.
* Utilizzare il tipo di dati `systemEvents`. Ad esempio, per visualizzare il volume di dati inserito nell'ultimo giorno, la query è:

```kusto
systemEvents 
| where timestamp >= ago(1d)
| where type == "Billing" 
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Questa query può essere usata in un [Avviso del log di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) per impostare avvisi sui volumi di dati. 

Il volume dei dati inviati può essere gestito con le tecniche seguenti:

* **Campionamento:** è possibile usare il campionamento per ridurre la quantità di dati di telemetria inviati dal server e dalle app client, con una distorsione minima delle metriche. Si tratta dello strumento principale da usare per ottimizzare la quantità di dati inviati. Altre informazioni sulle [funzionalità di campionamento](../../azure-monitor/app/sampling.md).

* **Limitare le chiamate AJAX**: è possibile [limitare il numero di chiamate AJAX che possono essere segnalate](../../azure-monitor/app/javascript.md#configuration) in ogni visualizzazione pagina o disattivare la creazione di report AJAX.

* **Disabilitare i moduli non necessari**: [modificare ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) per disattivare i moduli di raccolta che non sono necessari. Ad esempio, è possibile che i contatori delle prestazioni o dati sulle dipendenze siano non essenziali.

* **Metriche pre-aggregate**: se si inseriscono chiamate a TrackMetric nell'app, è possibile ridurre il traffico usando l'overload che accetta il calcolo della deviazione media e standard di un batch di misurazioni. In alternativa è possibile usare un [pacchetto di pre-aggregazione](https://www.myget.org/gallery/applicationinsights-sdk-labs).
 
* **Limite di utilizzo giornaliero:** quando si crea una risorsa di Application Insights nel portale di Azure, il limite di utilizzo giornaliero è impostato su 100 GB al giorno. Il valore predefinito quando si crea una risorsa di Application Insights in Visual Studio è basso, solo 32,3 MB al giorno. Il valore predefinito del limite di utilizzo giornaliero è impostato per semplificare le operazioni di test. È opportuno che l'utente aumenti il limite di utilizzo giornaliero prima di distribuire l'app nell'ambiente di produzione. 

    Il limite di utilizzo massimo è 1.000 GB al giorno, a meno che non si richieda un valore massimo più alto per un'applicazione con traffico elevato. 
    
    I messaggi di avviso relativi al limite giornaliero vengono inviati all'account che sono membri di questi ruoli per la risorsa Application Insights: "ServiceAdmin", "amministratore", "coadmin", "Owner".

    Prestare attenzione quando si imposta il limite di utilizzo giornaliero. Lo scopo dovrebbe essere quello di *non raggiungere mai il limite di utilizzo giornaliero*. Se si raggiunge il limite di utilizzo giornaliero, i dati della restante parte della giornata andranno persi e non sarà possibile monitorare l'applicazione. Per modificare il limite di utilizzo giornaliero, usare l'opzione **Limite di utilizzo volume giornaliero**. È possibile accedere a questa opzione nel riquadro **Usage and estimated cost** (Uso e costi stimati), descritto in dettaglio più avanti in questo articolo.
    
    La restrizione è stata rimossa per alcuni tipi di sottoscrizione con un credito che non poteva essere usato per Application Insights. In precedenza, se alla sottoscrizione era associato un limite di spesa, nella finestra di dialogo relativa al limite di utilizzo giornaliero erano presenti istruzioni per rimuoverlo e abilitare l'aumento del limite di utilizzo giornaliero oltre 32,3 MB al giorno.
    
* **Limitazione:** limita la velocità dei dati a 32.000 eventi al secondo, in base a una media calcolata a intervalli di 1 minuto per chiave di strumentazione. Il volume dei dati inviati dall'app viene valutato ogni minuto. Se il valore supera la velocità media al secondo calcolata nel minuto, il server rifiuta alcune richieste. L'SDK memorizza nel buffer i dati e quindi tenta di rinviarli. Segnala un sovraccarico per alcuni minuti. Se l'app continua a inviare dati che eccedono il valore della limitazione, alcuni di questi verranno eliminati. Gli SDK ASP.NET, Java e JavaScript tentano di inviare nuovamente i dati in questo modo. altri SDK potrebbero semplicemente eliminare i dati limitati. Se si verifica una limitazione, un avviso di notifica indica che si è verificato l'errore.

## <a name="manage-your-maximum-daily-data-volume"></a>Gestisci il volume di dati massimo giornaliero

È possibile usare il limite di utilizzo giornaliero di volume per limitare i dati raccolti. Tuttavia se si raggiunge questo limite, tutti i dati di telemetria inviati dall'applicazione per la restante parte del giorno andranno persi. *Non è consigliabile* raggiungere il limite di utilizzo giornaliero dell'applicazione. Dopo averlo raggiunto non sarà possibile monitorare l'integrità e le prestazioni dell'applicazione.

Anziché usare il limite di volume giornaliero, usare il [campionamento](../../azure-monitor/app/sampling.md) per impostare il volume di dati al livello desiderato. Usare quindi il limite di utilizzo giornaliero solo se "strettamente necessario" nel caso in cui l'applicazione inizi a inviare volumi più elevati di dati di telemetria in modo imprevisto.

### <a name="identify-what-daily-data-limit-to-define"></a>Identificare la soglia dei dati giornaliera da definire

Esaminare Application Insights utilizzo e i costi stimati per comprendere la tendenza di inserimento dei dati e il limite di volume giornaliero da definire. Fare attenzione nella scelta del limite, in quanto non sarà possibile monitorare le risorse una volta raggiunto il limite. 

### <a name="set-the-daily-cap"></a>Imposta il limite giornaliero

Per modificare il limite giornaliero, nella sezione **Configura** della risorsa di Application Insights, nella pagina **utilizzo e costi stimati** Selezionare **limite giornaliero**.

![Regolazione del limite del volume dei dati di telemetria giornaliero](./media/pricing/pricing-003.png)

Per [modificare il limite giornaliero tramite Azure Resource Manager](../../azure-monitor/app/powershell.md), la proprietà da modificare è il `dailyQuota`.  Tramite Azure Resource Manager è anche possibile impostare i `dailyQuotaResetTime` e il limite giornaliero `warningThreshold`. 

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

La conservazione predefinita per Application Insights risorse è di 90 giorni. È possibile selezionare periodi di conservazione diversi per ogni risorsa di Application Insights. Il set completo di periodi di conservazione disponibili è 30, 60, 90, 120, 180, 270, 365, 550 o 730 giorni. 

Per modificare il periodo di conservazione, dalla risorsa Application Insights passare alla pagina **utilizzo e costi stimati** e selezionare l'opzione **conservazione dati** :

![Regolazione del limite del volume dei dati di telemetria giornaliero](./media/pricing/pricing-005.png)

Il periodo di conservazione può anche essere [impostato a livello usando PowerShell](powershell.md#set-the-data-retention) usando il parametro `retentionInDays`. Inoltre, se si imposta la conservazione dei dati su 30 giorni, è possibile attivare un'eliminazione immediata dei dati meno recenti utilizzando il parametro `immediatePurgeDataOn30Days`, che può essere utile per gli scenari correlati alla conformità. Questa funzionalità di ripulitura viene esposta solo tramite Azure Resource Manager e deve essere usata con estrema attenzione. 

Quando la fatturazione ha inizio per un periodo di conservazione più lungo entro il 2019 dicembre, i dati conservati per più di 90 giorni verranno fatturati con la stessa tariffa attualmente addebitata per la conservazione dei dati di Azure Log Analytics. Per altre informazioni, vedere la [pagina dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/). Rimanere sempre aggiornati sullo stato di conservazione [delle variabili votando questo suggerimento](https://feedback.azure.com/forums/357324-azure-monitor-application-insights/suggestions/17454031). 

## <a name="data-transfer-charges-using-application-insights"></a>Addebiti per il trasferimento dei dati tramite Application Insights

L'invio di dati a Application Insights potrebbe influire sulla larghezza di banda dei dati. Come descritto nella pagina dei prezzi per la [larghezza di banda di Azure](https://azure.microsoft.com/pricing/details/bandwidth/), il trasferimento dei dati tra i servizi di Azure si trova in due aree addebitate come trasferimento di dati in uscita alla tariffa normale. Il trasferimento dei dati in ingresso è gratuito. Tuttavia, questo importo è molto ridotto (pochi%) rispetto ai costi per l'inserimento dei dati di Application Insights log. Di conseguenza, il controllo dei costi per Log Analytics deve concentrarsi sul volume di dati inserito e sono disponibili indicazioni utili per comprendere [questo comportamento.](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume)   

## <a name="limits-summary"></a>Riepilogo dei limiti

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Disabilitare i messaggi di posta elettronica relativi al limite di utilizzo giornaliero

Per disabilitare i messaggi di posta elettronica relativi al limite di utilizzo giornaliero, nella sezione **Configura** della risorsa di Application Insights, dal riquadro **Utilizzo e costi stimati**, selezionare **Limite di utilizzo giornaliero**. Sono disponibili impostazioni per inviare messaggi di posta elettronica quando si raggiunge il limite e anche quando si raggiunge un limite di avviso modificabile. Se si desidera disabilitare tutti i messaggi di posta elettronica relativi ai contratti multilicenza giornalieri deselezionare entrambe le caselle.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Piano tariffario Enterprise (per nodo) legacy

Per i primi utenti di applicazione Azure Insights, sono ancora disponibili due piani tariffari: Basic ed Enterprise. Il piano tariffario Basic è identico a quello descritto in precedenza ed è il livello predefinito. Include tutte le funzionalità del livello Enterprise senza costi aggiuntivi. Il livello Basic fattura principalmente il volume dei dati inseriti. 

> [!NOTE]
> Questi piani tariffari legacy sono stati rinominati. Il piano tariffario Enterprise è ora chiamato **per nodo** e il piano tariffario Basic è ora chiamato **per GB**. Questi nuovi nomi vengono usati sotto e nell'portale di Azure.  

Il livello per nodo (in precedenza Enterprise) prevede un addebito per ogni nodo e ogni nodo riceve una concessione giornaliera di dati. Nel piano tariffario per nodo viene addebitato il costo per i dati inseriti oltre la franchigia inclusa. Se si usa Operations Management Suite, è consigliabile scegliere il livello per nodo. 

Per i prezzi correnti nella valuta e nell'area locali, vedere i [prezzi di Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> Nel mese di aprile 2018 è stato [introdotto](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) un nuovo modello di determinazione dei prezzi per Monitoraggio di Azure. Questo modello adotta un semplice modello di "pagamento in base al consumo" per tutto il portfolio dei servizi di monitoraggio. Altre informazioni sul [nuovo modello di determinazione dei prezzi](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), su come [valutare l'impatto del passaggio a questo modello](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) in base ai modelli di uso e su [come acconsentire esplicitamente al nuovo modello](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Diritti di sottoscrizione per livello per nodo e Operations Management Suite

I clienti che acquistano Operations Management Suite E1 ed E2 possono ottenere Application Insights per nodo come componente aggiuntivo senza costi aggiuntivi, come [annunciato in precedenza](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). In particolare, ogni unità di Operations Management Suite E1 ed E2 include il diritto a un nodo del livello Application Insights per nodo. Ogni nodo di Application Insights include fino a 200 MB al giorno per l'inserimento di dati, che non comprendono l'inserimento di dati di Log Analytics, con un periodo di conservazione di 90 giorni senza costi aggiuntivi. Il livello viene descritto in dettaglio più avanti in questo articolo. 

Poiché questo livello è applicabile solo ai clienti con una sottoscrizione di Operations Management Suite, i clienti che non hanno una sottoscrizione di Operations Management Suite non vedranno un'opzione per selezionare questo livello.

> [!NOTE]
> Per assicurarsi di ottenere questo diritto, le risorse di Application Insights devono essere nel piano tariffario per nodo. Questo diritto viene applicato solo ai nodi. Application Insights risorse nel livello per GB non sono in grado di realizzare alcun vantaggio. Questo diritto non è visibile nei costi stimati indicati nel riquadro **Usage and estimated cost** (Uso e costi stimati). Se, inoltre, si sposta una sottoscrizione al nuovo modello di determinazione dei prezzi di monitoraggio di Azure in aprile 2018, il livello per GB è l'unico disponibile. Spostare una sottoscrizione al nuovo modello di determinazione dei prezzi di Monitoraggio di Azure non è consigliabile se si dispone di una sottoscrizione Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Funzionamento del livello per nodo

* Si paga per ogni nodo che invia dati di telemetria per le app nel livello per nodo.
  * Un *nodo* è un computer server fisico o virtuale oppure un'istanza del ruolo piattaforma distribuita come servizio che ospita l'app.
  * Computer di sviluppo, browser client e dispositivi mobili non sono conteggiati come nodi.
  * Se l'app ha diversi componenti che inviano dati di telemetria, ad esempio un servizio Web e un ruolo di lavoro back-end, i componenti vengono conteggiati separatamente.
  * I dati [Live Metrics Stream](../../azure-monitor/app/live-stream.md) non vengono conteggiati ai fini della determinazione del prezzo. In una sottoscrizione gli addebiti si applicano per nodo, non per app. Se si hanno cinque nodi che inviano dati di telemetria per 12 app, l'addebito sarà per cinque nodi.
* Sebbene gli addebiti siano fatturati mensilmente, quelli effettivi hanno luogo solo nelle ore durante le quali un nodo invia dati di telemetria da un'app. La tariffa oraria è la tariffa mensile fatturata divisa per 744 (il numero di ore in un mese di 31 giorni).
* Viene fornita un'allocazione di volume di dati di 200 MB al giorno per ogni nodo rilevato (con granularità oraria). L'allocazione di dati non usata non viene trasferita al giorno successivo.
  * Se si sceglie il piano tariffario per nodo, ogni sottoscrizione ottiene un limite giornaliero di dati in base al numero di nodi che inviano dati di telemetria alle risorse Application Insights in tale sottoscrizione. Se si hanno quindi cinque nodi che inviano dati tutto il giorno, sarà applicata una quantità inclusa di 1 GB a tutte le risorse di Application Insights in quella sottoscrizione. Non è importante se determinati nodi inviano più dati di altri, perché i dati inclusi vengono condivisi tra tutti i nodi. Se in un determinato giorno, le risorse di Application Insights ricevono un numero di dati superiore a quello incluso nell'allocazione giornaliera dei dati per questa sottoscrizione, vengono applicati gli addebiti per i dati in eccedenza per GB. 
  * La quantità di dati giornaliera viene calcolata come numero di ore del giorno (fuso orario UTC) in cui ogni nodo invia dati di telemetria, diviso per 24 e moltiplicato per 200 MB. Se quindi si hanno quattro nodi che inviano dati di telemetria per 15 ore su 24 durante un giorno, i dati inclusi per tale giorno saranno ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Al prezzo di USD 2,30 per GB di eccedenza di dati, l'addebito sarà di USD 1,15 supponendo che i nodi quel giorno inviino 1 GB di dati.
  * La franchigia giornaliera del livello per nodo non è condivisa con le applicazioni per cui è stato scelto il livello per GB. La quantità non usata non viene trasferita da un giorno all'altro. 

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Esempi di come determinare il conteggio di nodi specifico

| Scenario                               | Conteggio giornaliero totale dei nodi |
|:---------------------------------------|:----------------:|
| 1 applicazione che usa 3 istanze del Servizio app di Azure e 1 server virtuale | 4 |
| 3 applicazioni in esecuzione su 2 macchine virtuali; le risorse Application Insights per queste applicazioni si trovano nella stessa sottoscrizione e nel livello per nodo | 2 | 
| 4 applicazioni le cui risorse di Application Insights si trovano nella stessa sottoscrizione. Ogni applicazione esegue 2 istanze durante 16 ore non di punta e 4 istanze durante 8 ore di punta | 13.33 | 
| Servizi cloud con ruolo di lavoro 1 e 1 ruolo Web, ognuno dei quali esegue 2 istanze | 4 | 
| Un cluster di Azure Service Fabric a 5 nodi che esegue 50 microservizi. Ogni microservizio esegue 3 istanze | 5|

* Il conteggio preciso dei nodi dipende dall'SDK di Application Insights usato dall'applicazione. 
  * Nell'SDK versioni 2.2 e successive sia [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) che [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) di Application Insights segnalano ogni host applicazioni come nodo. Ne sono esempi il nome computer del server fisico e degli host di VM o il nome dell'istanza per i servizi cloud.  L'unica eccezione è data da un'applicazione che usa solo [.NET Core](https://dotnet.github.io/) e Core SDK di Application Insights. In tal caso, viene segnalato solo un nodo per tutti gli host perché il nome host non è disponibile. 
  * Per le versioni precedenti dell'SDK, [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) ha lo stesso comportamento delle nuove versioni dell'SDK, ma [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) segnala solamente un nodo, a prescindere dal numero degli host applicazioni. 
  * Se l'applicazione usa l'SDK per impostare **roleInstance** su un valore personalizzato, per impostazione predefinita viene usato quello stesso valore per determinare il conteggio dei nodi. 
  * Se si usa una nuova versione dell'SDK con un'app eseguita da computer client o dispositivi mobili, il conteggio dei nodi potrebbe restituire un numero molto elevato (a causa del numero elevato di computer client o dispositivi mobili). 

## <a name="automation"></a>Automazione

È possibile scrivere uno script per impostare il piano tariffario usando Gestione risorse di Azure. [Informazioni](powershell.md#price).


## <a name="next-steps"></a>Passaggi successivi

* [Campionamento](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/