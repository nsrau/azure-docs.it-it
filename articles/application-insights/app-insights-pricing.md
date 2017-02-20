---
title: Gestire volumi di dati e prezzi per Application Insights | Microsoft Docs
description: Gestire volumi di dati di telemetria e monitorare i costi in Application Insights.
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 01/13/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 9b26cfed4102bc09512f5ef1270aa0275a7f8f69
ms.openlocfilehash: 58e1f5862cd147d7664248cf393f77a5e9af10d3


---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Gestire volumi di dati e prezzi in Application Insights


I prezzi per [Azure Application Insights][start] dipendono dal volume di dati per ogni applicazione. Un basso uso durante lo sviluppo o per un'app di piccole dimensioni è probabilmente gratis, dal momento che è previsto l'uso di 1 GB di dati di telemetria per un mese.

Ogni risorsa di Application Insights viene addebitata come servizio separato e contribuisce alla fatturazione per la sottoscrizione di Azure.

Esistono due piani tariffari. Il piano predefinito è denominato Basic. È possibile scegliere il piano Enterprise, che non prevede una tariffa giornaliera ma consente alcune funzionalità aggiuntive, ad esempio l'[esportazione continua](app-insights-export-telemetry.md).

Per domande sulla determinazione dei prezzi per Application Insights, puoi pubblicare una domanda nel nostro [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights). 

## <a name="the-pricing-plans"></a>Piani tariffari

Vedere la [pagina sui prezzi di Application Insights][pricing] per i prezzi correnti nella valuta locale.

### <a name="basic-plan"></a>Piano Basic

Il piano Basic è la scelta predefinita quando viene creata una nuova risorsa di Application Insight ed è sufficiente per la maggior parte dei clienti.

* Nel piano Basic vengono applicati addebiti in base al volume dei dati: il numero di byte dei dati di telemetria ricevuti da Application Insights. Il volume di dati viene misurato come le dimensioni del pacchetto di dati JSON non compresso inviato dall'applicazione e ricevuto da Application Insights.
* Il primo GB per ciascuna applicazione è gratuito. Pertanto se si usa lo strumento per prova o per sviluppo, probabilmente non si incorrerà in addebiti.
* Nel piano Basic, con un addebito extra per GB è disponibile l'[Esportazione continua](app-insights-export-telemetry.md), gratuita fino ai primi giorni di marzo 2017.

### <a name="enterprise-plan"></a>Piano Enterprise

* Nel piano Enterprise, l'app può usare tutte le funzionalità di Application Insights. Il piano Enterprise include connettori [Esportazione continua](app-insights-export-telemetry.md) e [Log Analytics](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) senza costi aggiuntivi.
* Si paga per ogni nodo che invii dati di telemetria per le app nel piano Enterprise. 
 * Un *nodo* è una macchina server fisica o virtuale oppure un'istanza del ruolo PaaS (piattaforma distribuita come servizio) che ospita l'app.
 * Computer di sviluppo, browser client e dispositivi mobili non sono conteggiati come nodi.
 * Se l'app dispone di diversi componenti che inviano dati di telemetria, ad esempio un servizio Web e un lavoro back-end, questi vengono conteggiati separatamente.
* In una sottoscrizione, gli addebiti si applicano per nodo, non per app. Se si dispone di cinque nodi che inviano dati di telemetria per 12 app, l'addebito sarà per cinque nodi.
* Sebbene gli addebiti siano fatturati mensilmente, quelli effettivi hanno luogo solo nelle ore durante le quali un nodo invia dati di telemetria da un'app. La tariffa oraria è la tariffa mensile fatturata divisa per 744 (il numero di ore in un mese di 31 giorni).
* Viene fornita un'allocazione di volume di dati di 200 MB al giorno per ciascun nodo rilevato (con granularità oraria). L'allocazione di dati non usata non viene trasferita al giorno successivo.
 * Scegliendo l'opzione di prezzo Enterprise, ciascuna sottoscrizione ottiene una quantità di dati giornaliera inclusa in base al numero di nodi che inviano dati di telemetria alle risorse di Application Insights in quella sottoscrizione. Se si dispone quindi di 5 nodi che inviano dati tutto il giorno, sarà applicata una quantità inclusa di 1 GB a tutte le risorse di Application Insights in quella sottoscrizione. Non è importante se determinati nodi inviano più dati di altri, perché i dati inclusi vengono condivisi tra tutti i nodi. Se un determinato giorno le risorse di Application Insights ricevono più dati di quelli inclusi nell'allocazione giornaliera per la sottoscrizione, si applicheranno gli addebiti di dati in eccedenza per GB. 
 * La quantità di dati giornaliera viene calcolata come numero di ore del giorno (fuso orario UTC) in cui ogni nodo invia dati di telemetria, diviso per 24 e moltiplicato per 200 MB. Se si dispone di 4 nodi che inviano dati di telemetria per 15 ore su 24 durante un giorno, i dati inclusi per tale giorno saranno ((4 x 15) / 24) x 200 MB = 500 MB. Al prezzo di USD 2,30 per GB di eccedenza di dati, l'addebito sarà di 1,15 GB supponendo che i nodi quel giorno inviino 1 GB di dati.
 * La quantità giornaliera del piano Enterprise non viene condivisa con le applicazioni per cui è stata scelta l'opzione Basic. Inoltre, eventuali quantità non usate in un determinato giorno non vengono accumulate. 
* Di seguito sono riportati alcuni esempi di determinazione di un conteggio di nodi specifico:
| Scenario                               | Conteggio giornaliero totale dei nodi |
|:---------------------------------------|:----------------:|
| 1 applicazione usa 3 istanze del Servizio app di Azure e 1 server virtuale | 4 |
| 3 applicazioni in esecuzione su 2 macchine virtuali. Le risorse di Application Insights per queste applicazioni si trovano nella stessa sottoscrizione e nello stesso piano Enterprise | 2 | 
| 4 applicazioni le cui risorse di Application Insights si trovano nella stessa sottoscrizione. Ogni applicazione esegue 2 istanze durante 16 ore di scarso traffico e 4 istanze durante 8 ore di traffico intenso. | 13.33 | 
| Servizi cloud con ruolo di lavoro 1 e 1 ruolo Web, ognuno dei quali esegue 2 istanze | 4 | 
| Cluster Service Fabric a 5 nodi che esegue 50 microservizi. Ciascun microservizio esegue 3 istanze | 5|

* Il comportamento preciso del conteggio dei nodi dipende dall'SDK di ApplicationInsight usato dall'applicazione. 
  * Nelle versioni SDK 2.2 e successive, sia [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) che [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) di Application Insights possono riportare ciascun host applicazione come nodo, ad esempio il nome del computer per gli host server fisici e le macchine virtuali o il nome dell'istanza in caso di servizi cloud.  L'unica eccezione è data dalle applicazioni che usano solo [.NET Core](https://dotnet.github.io/) e Core SDK di Application Insights. In questo caso, verrà riportato un solo nodo per tutti gli host poiché il nome host non è disponibile. 
  * Per le versioni precedenti di SDK, [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) avrà lo stesso comportamento delle nuove versioni dell'SDK, tuttavia [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) riporterà solamente un nodo a prescindere dal numero effettivo degli host applicazione. 
  * Si noti che se l'applicazione usa SDK per impostare roleInstance su un valore personalizzato, per impostazione predefinita verrà usato quello stesso valore per determinare il conteggio dei nodi. 
  * Se si usa una nuova versione di SDK con un'app eseguita da computer client o dispositivi mobili, è possibile che il conteggio dei nodi restituisca un numero molto elevato (per via del numero elevato di computer client o dispositivi mobili). 

### <a name="multi-step-web-tests"></a>Test Web in più passaggi

È prevista una tariffa aggiuntiva per i [test Web in più passaggi](app-insights-monitor-web-app-availability.md#multi-step-web-tests). Quanto scritto si riferisce ai test Web che eseguono una sequenza di azioni. 

Non è prevista una tariffa separata per le "verifiche ping" di una singola pagina. I dati di telemetria delle verifiche ping e delle verifiche in più fasi incorrono in costi insieme agli altri dati di telemetria provenienti dall'app.

## <a name="review-pricing-plan-and-estimate-costs-for-your-application-insights-resource"></a>Esaminare il piano tariffario e i costi stimati per la propria risorsa di Application Insights
Aprire il pannello Funzionalità e prezzi nella risorsa di Application Insights per l'applicazione.

![Scegliere Prezzi.](./media/app-insights-pricing/01-pricing.png)

**a.** Esaminare il volume di dati per il mese. Sono inclusi tutti i dati ricevuti e conservati (dopo qualsiasi [campionamento](app-insights-sampling.md)) da server e app client e dai test di disponibilità.

**b.** I costi per i [test Web in più passaggi](app-insights-monitor-web-app-availability.md#multi-step-web-tests) vengono addebitati separatamente. Non sono inclusi i test di disponibilità semplici, il cui costo viene addebitato con il volume di dati.

**c.** Abilitare il piano Enterprise.

**d.** Fare clic sulle opzioni di gestione dei dati per visualizzare i volumi di dati dell'ultimo mese e per impostare un limite giornaliero o il campionamento per inserimento.

Gli addebiti di Application Insights vengono aggiunti alla fatturazione di Azure. È possibile visualizzare i dettagli della fattura Azure nella sezione Fatturazione del portale di Azure oppure nel [portale di fatturazione di Azure](https://account.windowsazure.com/Subscriptions). 

![Nel menu laterale scegliere Fatturazione.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Velocità dati
Esistono tre modi per limitare il volume di invio dei dati:

* **Limite giornaliero.** Per impostazione predefinita è 500 GB al giorno. Quando l'app raggiunge il limite, viene inviata un'e-mail e viene impedito l'uso di dati fino alla fine della giornata. È possibile modificarlo nel pannello Data Volume Management (Gestione volume dati).
* **[Campionamento](app-insights-sampling.md).** Questo meccanismo può ridurre la quantità di dati di telemetria inviati da server e app client, con una distorsione minima delle metriche.
* **La limitazione** limita la velocità dei dati a 16.000 eventi al secondo, calcolati con una media di 1 minuto. 


*Cosa accade se l'app supera la velocità di limitazione?*

* Il volume dei dati inviati dall'app viene valutato ogni minuto. Se il valore supera la velocità media al secondo calcolata nel minuto, il server rifiuta alcune richieste. L'SDK esegue il buffering dei dati e quindi tenta di inviare di nuovo, distribuendo una sovratensione nell'arco di diversi minuti. Se l'app continua a inviare dati che eccedono la limitazione, alcuni di questi verranno eliminati. (Gli SDK di ASP.NET, Java e JavaScript ritentano l'invio, mentre altri SDK potrebbero semplicemente eliminare i dati sottoposti a limitazione).

In caso di avvenuta limitazione, verrà visualizzata una notifica che avviserà che ciò si è verificato.

*Come è possibile sapere quanti punti dati vengono inviati dall'app?*

* Aprire il pannello Prezzi per visualizzare il grafico Volume dati.
* Altrimenti, in Esplora metriche, aggiungere un nuovo grafico e selezionare **Volume punti dati** come metrica. Attivare il raggruppamento in base a **Tipo di dati**.

## <a name="to-reduce-your-data-rate"></a>Per ridurre la velocità dei dati
Ecco alcune operazioni da eseguire per ridurre il volume di dati:

* Ridurre il limite di volume giornaliero. Il valore predefinito è 500 GB al giorno.
* Utilizzare [Campionamento](app-insights-sampling.md). Questa tecnologia riduce la frequenza dei dati senza deviare le metriche e senza compromettere la possibilità di spostarsi tra elementi correlati nella Ricerca. Nelle app server funziona automaticamente.
* [Limitare il numero di chiamate Ajax che possono essere segnalate](app-insights-javascript.md#detailed-configuration) in ogni visualizzazione pagina o disattivare la creazione di report Ajax.
* Disattivare i moduli di raccolta non necessari [modificando il file ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Ad esempio, è possibile che i contatori delle prestazioni o dati sulle dipendenze siano non essenziali.
* Suddividere i dati di telemetria per separare le chiavi di strumentazione. 
* Pre-aggregare metriche. Se sono state inserite chiamate a TrackMetric nell'applicazione, è possibile ridurre il traffico utilizzando l'overload che accetta il calcolo della media e la deviazione standard di un batch di misurazioni. In alternativa è possibile usare un [pacchetto di pre-aggregazione](https://www.myget.org/gallery/applicationinsights-sdk-labs). 

## <a name="sampling"></a>campionamento
[Sampling](app-insights-sampling.md) consente di ridurre la frequenza con cui i dati di telemetria vengono inviati all'app, pur mantenendo la possibilità di trovare gli eventi correlati durante le ricerche di diagnostica e il conteggio corretto degli eventi. 

Il campionamento consente di ridurre in modo efficace i costi e di non superare la quota mensile. L'algoritmo di campionamento conserva gli elementi correlati ai dati di telemetria, in modo che, quando si usa la ricerca, ad esempio, è possibile trovare la richiesta correlata a una particolare eccezione. L'algoritmo mantiene inoltre i conteggi corretti e consente di visualizzare in Esplora metriche i valori corretti della frequenza delle richieste, della frequenza delle eccezioni e di altri contatori.

Sono disponibili diversi tipi di campionamento.

* [Adaptive sampling](app-insights-sampling.md) è l'opzione predefinita per ASP.NET SDK e si adatta automaticamente al volume dei dati di telemetria inviati dall'app. Il campionamento viene eseguito automaticamente nell'SDK dell'app Web riducendo il traffico dei dati di telemetria sulla rete. 
* *campionamento per inserimento* è un'alternativa che opera nel punto in cui i dati di telemetria provenienti dall'app raggiungono il servizio Application Insights. Non ha alcun effetto sul volume dei dati di telemetria inviati dall'app, ma riduce tuttavia il volume mantenuto dal servizio. È possibile usarlo per ridurre la quota che i dati di telemetria usano da browser e altri SDK.

Per impostare il campionamento per inserimento, impostare il controllo nel pannello Prezzi:

![Dal pannello Quota + prezzi, fare clic sul riquadro degli esempi e selezionare una frazione di campionamento.](./media/app-insights-pricing/04.png)

> [!WARNING]
> Il valore visualizzato nel riquadro Campioni mantenuti indica solo il valore impostato per il campionamento per inserimento. Non visualizza la frequenza di campionamento che opera nell'SDK all'interno dell'app. 
> 
> Se i dati di telemetria in ingresso sono già stati campionati nell'SDK, il campionamento per inserimento non verrà applicato.
> 
> 

Per individuare la frequenza di campionamento effettiva indipendentemente dal punto in cui è stata applicata, usare una [query di Analisi](app-insights-analytics.md) simile alla seguente:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

In ogni record conservato, `itemCount` indica il numero di record originali che rappresenta, uguale a 1 + il numero di record precedenti scartati. 


## <a name="transition-from-the-old-pricing-tiers"></a>Transizione dai piani tariffari precedenti

Le applicazioni esistenti possono continuare a usare i piani tariffari precedenti fino a febbraio 2017. In tale data, la maggior parte delle applicazioni verrà spostata automaticamente al piano Basic. Le applicazioni che usano l'esportazione continua o il connettore per OMS Log Analytics verranno spostate al piano Enterprise.


## <a name="limits-summary"></a>Riepilogo dei limiti
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Campionamento](app-insights-sampling.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/




<!--HONumber=Feb17_HO1-->


