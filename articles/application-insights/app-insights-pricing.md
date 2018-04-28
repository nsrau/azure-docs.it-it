---
title: Gestire i prezzi e il volume di dati per Azure Application Insights | Microsoft Docs
description: Gestire volumi di dati di telemetria e monitorare i costi in Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 2c06c2220d3a3ed0a27b4f0febb4de95b2137ddc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Gestire volumi di dati e prezzi in Application Insights

I prezzi per [Azure Application Insights][start] dipendono dal volume di dati per ogni applicazione. Ogni risorsa di Application Insights viene addebitata come servizio separato e contribuisce alla fatturazione per la sottoscrizione di Azure.

Application Insights prevede due piani tariffari: Basic ed Enterprise. Il piano tariffario Basic è quello predefinito. Include tutte le funzionalità del piano Enterprise, senza alcun costo aggiuntivo. Nel piano Basic la fatturazione viene effettuata principalmente in base al volume di dati inseriti. 

Il piano Enterprise prevede un addebito per nodo e una determinata quantità di dati giornaliera per ogni nodo. Nel piano tariffario Enterprise vengono addebitati i dati inseriti in aggiunta alla quantità inclusa. Se si usa Operations Management Suite, è consigliabile scegliere il piano Enterprise. 

Per domande sulla determinazione dei prezzi per Application Insights, è possibile pubblicare una domanda nel [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-plans"></a>Piani tariffari

Per i prezzi correnti nella valuta e nell'area locali, consultare [Prezzi di Application Insights][pricing].

> [!NOTE]
> Nel mese di aprile 2018 è stato [introdotto](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) un nuovo modello di determinazione dei prezzi per Monitoraggio di Azure. Questo modello adotta un semplice modello di "pagamento in base al consumo" per tutto il portfolio dei servizi di monitoraggio. Altre informazioni sul [nuovo modello di determinazione dei prezzi](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), su come [valutare l'impatto del passaggio a questo modello](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) in base ai modelli di uso e su [come acconsentire esplicitamente al nuovo modello](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model).

### <a name="basic-plan"></a>Piano Basic

Il piano Basic è il piano tariffario predefinito quando viene creata una nuova risorsa di Application Insight. Il piano Basic è ottimale per tutti i clienti ad eccezione di quelli che dispongono di una sottoscrizione Operations Management Suite.

* Nel piano Basic vengono applicati addebiti in base al volume dei dati, il quale viene determinato dal numero di byte dei dati di telemetria ricevuti da Application Insights. 
    
    Il volume de dati viene misurato in base alle dimensioni del pacchetto di dati JSON non compresso inviato dall'applicazione ad Application Insights.

    Per i [dati tabulari importati in Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), il volume dei dati viene misurato in base alle dimensioni non compresse dei file inviati ad Application Insights.
* I dati [Live Metrics Stream](app-insights-live-stream.md) non vengono conteggiati ai fini della determinazione del prezzo.
* Dal mese di aprile 2018 il piano Basic include l'opzione di [esportazione continua](app-insights-export-telemetry.md) e il [connettore di Log Analytics di Azure](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) senza costi aggiuntivi.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Piano Enterprise e diritti della sottoscrizione di Operations Management Suite

Come [annunciato di recente](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/), i clienti che acquistano Operations Management Suite E1 ed E2 possono ottenere Application Insights Enterprise come componente aggiuntivo senza sostenere altri costi. Nello specifico, ogni unità di Operations Management Suite E1 ed E2 include il diritto a un nodo del piano Enterprise di Application Insights. Ogni nodo di Application Insights include fino a 200 MB al giorno per l'inserimento di dati, che non comprendono l'inserimento di dati di Log Analytics, con un periodo di conservazione di 90 giorni senza costi aggiuntivi. Questo piano viene descritto in modo più dettagliato più avanti nell'articolo. 

Poiché tale piano è applicabile solo ai clienti con una sottoscrizione Operations Management Suite, per i clienti che non la possiedono non viene visualizzata alcuna opzione per la selezione di questo piano.

> [!NOTE]
> Per assicurarsi di ottenere questo diritto, le risorse di Application Insights devono essere disponibili nel piano tariffario Enterprise. Questo diritto viene applicato solo ai nodi. Le risorse di Application Insights nel piano Basic non ne traggono alcun vantaggio. Questo diritto non è visibile nei costi stimati indicati nel riquadro **Usage and estimated cost** (Uso e costi stimati). In aggiunta se si sposta una sottoscrizione al nuovo modello di determinazione dei prezzi di Monitoraggio di Azure ad aprile 2018, il piano Basic sarà l'unico piano disponibile. Spostare una sottoscrizione al nuovo modello di determinazione dei prezzi di Monitoraggio di Azure non è consigliabile se si dispone di una sottoscrizione Operations Management Suite.

Per altre informazioni sul piano Enterprise, vedere [Dettagli del piano Enterprise](app-insights-pricing-enterprise-details.md).

### <a name="multi-step-web-tests"></a>Test Web in più passaggi

È prevista una tariffa aggiuntiva per i [test Web in più passi](app-insights-monitor-web-app-availability.md#multi-step-web-tests). I test Web in più passi sono test Web che eseguono una sequenza di azioni.

Non è prevista una tariffa separata per i *test del ping* di una singola pagina. I dati di telemetria dei test del ping e dei test in più passi vengono addebitai allo stesso costo di altri dati di telemetria provenienti dall'app.

## <a name="review-pricing-plans-and-estimate-costs"></a>Esaminare i piani tariffari e stimare i costi

Application Insights offre informazioni per comprendere con facilità i piani tariffari disponibili e i possibili costi associati in base a modelli di uso recente. Per iniziare, nel portale di Azure, aprire il riquadro **Usage and estimated cost** (Uso e costi stimati) nella risorsa di Application Insights:

![Scegliere i prezzi](./media/app-insights-pricing/pricing-001.png)

R. Esaminare il volume di dati per il mese. Sono inclusi tutti i dati ricevuti e conservati (dopo un [campionamento](app-insights-sampling.md)) dal server e dalle app client, oltre che dai test di disponibilità.  
B. I costi per i [test Web in più passaggi](app-insights-monitor-web-app-availability.md#multi-step-web-tests) vengono addebitati separatamente. Non sono inclusi i test di disponibilità semplici, il cui costo viene addebitato con il volume di dati.  
C. Visualizzare le tendenze del volume dei dati relative all'ultimo mese.  
D. Abilitare il [campionamento](app-insights-sampling.md) per l'inserimento dei dati.   
E. Impostare il limite giornaliero di utilizzo per volume dati.  

Gli addebiti di Application Insights vengono aggiunti alla fatturazione di Azure. È possibile visualizzare i dettagli della fattura di Azure nella sezione **Fatturazione** del portale di Azure oppure nel [portale di fatturazione di Azure](https://account.windowsazure.com/Subscriptions). 

![Nel menu a sinistra selezionare Fatturazione](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Velocità dati
Il volume dei dati inviati viene limitato in tre modi:

* **Campionamento:** è possibile usare il campionamento per ridurre la quantità di dati di telemetria inviati dal server e dalle app client, con una distorsione minima delle metriche. Si tratta dello strumento principale da usare per ottimizzare la quantità di dati inviati. Altre informazioni sulle [funzionalità di campionamento](app-insights-sampling.md). 
* **Limite di utilizzo giornaliero:** quando si crea una risorsa di Application Insights nel portale di Azure, il limite di utilizzo giornaliero è impostato su 100 GB al giorno. Il valore predefinito quando si crea una risorsa di Application Insights in Visual Studio è basso, solo 32,3 MB al giorno. Il valore predefinito del limite di utilizzo giornaliero è impostato per semplificare le operazioni di test. È opportuno che l'utente aumenti il limite di utilizzo giornaliero prima di distribuire l'app nell'ambiente di produzione. 

    Il limite di utilizzo massimo è 1.000 GB al giorno, a meno che non si richieda un valore massimo più alto per un'applicazione con traffico elevato. 

    Prestare attenzione quando si imposta il limite di utilizzo giornaliero. Lo scopo dovrebbe essere quello di *non raggiungere mai il limite di utilizzo giornaliero*. Se si raggiunge il limite di utilizzo giornaliero, i dati della restante parte della giornata andranno persi e non sarà possibile monitorare l'applicazione. Per modificare il limite di utilizzo giornaliero, usare l'opzione **Limite di utilizzo volume giornaliero**. È possibile accedere a questa opzione nel riquadro **Usage and estimated cost** (Uso e costi stimati), descritto in dettaglio più avanti in questo articolo.
    La restrizione è stata rimossa per alcuni tipi di sottoscrizione con un credito che non poteva essere usato per Application Insights. In precedenza, se alla sottoscrizione era associato un limite di spesa, nella finestra di dialogo relativa al limite di utilizzo giornaliero erano presenti istruzioni per rimuoverlo e abilitare l'aumento del limite di utilizzo giornaliero oltre 32,3 MB al giorno.
* **Limitazione:** limita la velocità dei dati a 32.000 eventi al secondo, in base a una media calcolata a intervalli di 1 minuto.

*Cosa accade se l'app supera la velocità di limitazione?*

* Il volume dei dati inviati dall'app viene valutato ogni minuto. Se il valore supera la velocità media al secondo calcolata nel minuto, il server rifiuta alcune richieste. L'SDK memorizza nel buffer i dati e quindi tenta di rinviarli. Segnala un sovraccarico per alcuni minuti. Se l'app continua a inviare dati che eccedono il valore della limitazione, alcuni di questi verranno eliminati. Gli SDK di ASP.NET, Java e JavaScript ritentano l'invio dei dati, mentre altri SDK potrebbero semplicemente eliminare i dati che superano la limitazione. In caso di avvenuta limitazione, verrà visualizzata una notifica che avviserà l'utente di che ciò si è verificato.

*Come è possibile conoscere la quantità di dati inviati dall'app?*

Per visualizzare la quantità di dati che l'app sta inviando, è possibile usare una delle opzioni seguenti:

* Aprire il riquadro **Usage and estimated cost** (Uso e costi stimati) per visualizzare il grafico dei volumi di dati giornalieri. 
* In Esplora metriche aggiungere un nuovo grafico. Per la metrica del grafico, selezionare **Volume dei punti dati**. Attivare **Raggruppamento** e quindi raggruppare in base al **Tipo di dati**.

## <a name="reduce-your-data-rate"></a>Ridurre la velocità dei dati
Ecco alcune operazioni da eseguire per ridurre il volume di dati:

* Utilizzare [Campionamento](app-insights-sampling.md). Questa tecnologia consente di ridurre la velocità dei dati senza rendere asimmetriche le metriche. È ancora possibile spostarsi tra gli elementi correlati nella ricerca. Nelle app server il campionamento funziona automaticamente.
* [Limitare il numero di chiamate Ajax che possono essere segnalate](app-insights-javascript.md#detailed-configuration) in ogni visualizzazione pagina o disattivare la creazione di report Ajax.
* Disattivare i moduli di raccolta non necessari [modificando il file ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Ad esempio, è possibile che i contatori delle prestazioni o dati sulle dipendenze siano non essenziali.
* Suddividere i dati di telemetria per separare le chiavi di strumentazione. 
* Pre-aggregare metriche. Se sono state inserite chiamate a TrackMetric nell'app, è possibile ridurre il traffico usando l'overload che accetta il calcolo della media e la deviazione standard di un batch di misurazioni. In alternativa è possibile usare un [pacchetto di pre-aggregazione](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Gestire il volume di dati giornaliero massimo

È possibile usare il limite di utilizzo giornaliero di volume per limitare i dati raccolti. Tuttavia se si raggiunge questo limite, tutti i dati di telemetria inviati dall'applicazione per la restante parte del giorno andranno persi. *Non è consigliabile* raggiungere il limite di utilizzo giornaliero dell'applicazione. Dopo averlo raggiunto non sarà possibile monitorare l'integrità e le prestazioni dell'applicazione.

Anziché usare il limite di volume giornaliero, usare il [campionamento](app-insights-sampling.md) per impostare il volume di dati al livello desiderato. Usare quindi il limite di utilizzo giornaliero solo se "strettamente necessario" nel caso in cui l'applicazione inizi a inviare volumi più elevati di dati di telemetria in modo imprevisto.

Per modificare il limite di utilizzo giornaliero, nella sezione **Configura** della risorsa di Application Insights, dal riquadro **Usage and estimated cost** (Uso e costi stimati), selezionare **Limite di utilizzo giornaliero**.

![Regolazione del limite del volume dei dati di telemetria giornaliero](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>campionamento
Il [campionamento](app-insights-sampling.md) è un metodo che consente di ridurre la velocità con cui i dati di telemetria vengono inviati all'app, pur mantenendo la possibilità di trovare gli eventi correlati durante le ricerche di diagnostica e il conteggio corretto degli eventi.

Il campionamento consente di ridurre in modo efficace i costi e di non superare la quota mensile. L'algoritmo di campionamento conserva gli elementi correlati ai dati di telemetria, in modo che, quando si usa la ricerca, ad esempio, è possibile trovare la richiesta correlata a una particolare eccezione. L'algoritmo mantiene anche i conteggi corretti e consente di visualizzare in Esplora metriche i valori corretti della frequenza delle richieste, della frequenza delle eccezioni e di altri contatori.

Sono disponibili diversi tipi di campionamento.

* Il [campionamento adattivo](app-insights-sampling.md) è il valore predefinito per l'SDK di ASP.NET. Il campionamento adattivo si adatta automaticamente al volume dei dati di telemetria inviati dall'app. Viene eseguito automaticamente nell'SDK dell'app Web riducendo il traffico dei dati di telemetria sulla rete. 
* *campionamento per inserimento* è un'alternativa che opera nel punto in cui i dati di telemetria provenienti dall'app raggiungono il servizio Application Insights. Il campionamento per inserimento non ha alcun effetto sul volume dei dati di telemetria inviati dall'app, ma riduce il volume mantenuto dal servizio. È possibile usarlo per ridurre la quota che i dati di telemetria usano dal browser e da altri SDK.

Per impostare il campionamento per inserimento, visitare il riquadro **Prezzi**:

![Nel riquadro Quota and pricing (Quota e tariffe) fare clic sul riquadro Campioni e quindi selezionare una frazione di campionamento](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> Il riquadro **Campionamento dei dati** controlla solo il valore del campionamento per inserimento. Non riflette la frequenza di campionamento applicata dall'SDK di Application Insights nell'app. Se i dati di telemetria in ingresso sono già stati campionati nell'SDK, il campionamento per inserimento non verrà applicato.
>

Per individuare la frequenza di campionamento effettiva indipendentemente dal punto in cui è stata applicata, usare una [query di Analytics](app-insights-analytics.md). La query appare come la seguente:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

In ogni record conservato, `itemCount` indica il numero di record originali che rappresenta, uguale a 1 + il numero di record precedenti scartati. 

## <a name="automation"></a>Automazione

È possibile scrivere uno script per impostare il piano tariffario tramite Azure Resource Manager. [Informazioni](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Riepilogo dei limiti

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Campionamento](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
