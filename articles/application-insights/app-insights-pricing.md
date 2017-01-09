---
title: "Gestire volumi di dati e funzionalità per Application Insights | Microsoft Docs"
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
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 3fdb050d1d6746313b2dffb089d56b71908335d2
ms.openlocfilehash: 585e493b2642b3ba798977528f7564674a44d738


---
# <a name="manage-features-and-data-volume-in-application-insights"></a>Gestire volumi di dati e funzionalità in Application Insights


I prezzi per [Azure Application Insights][start] dipendono dal volume di dati per ogni applicazione. Un basso uso durante lo sviluppo o per un'app di piccole dimensioni è probabilmente gratis, dal momento che è previsto l'uso gratuito dei dati di telemetria per un mese.

Ogni risorsa di Application Insights viene addebitata come servizio separato e contribuisce alla fatturazione per la sottoscrizione di Azure.

Esistono due piani tariffari. Il piano predefinito è denominato Basic. È possibile scegliere il piano Enterprise, che non prevede una tariffa giornaliera ma consente alcune funzionalità aggiuntive, ad esempio l'[esportazione continua](app-insights-export-telemetry.md).

[Vedere la pagina relativa ai prezzi][pricing].

## <a name="review-pricing-plan-for-your-application-insights-resource"></a>Esaminare il piano tariffario per la propria risorsa di Application Insights
Aprire il pannello Funzionalità e prezzi nella risorsa di Application Insights per l'applicazione.

![Scegliere Prezzi.](./media/app-insights-pricing/01-pricing.png)

1. Esaminare il volume di dati per il mese. Sono inclusi tutti i dati ricevuti e conservati (dopo qualsiasi [campionamento](app-insights-sampling.md)) da server e app client e dai test di disponibilità.
2. I costi per i [test Web in più passaggi](app-insights-monitor-web-app-availability.md#multi-step-web-tests) vengono addebitati separatamente. Non sono inclusi i test di disponibilità semplici, il cui costo viene addebitato con il volume di dati.
3. Abilitare le funzionalità aggiuntive fornite dal piano Enterprise. Questo piano non prevede dati gratis.
4. Fare clic sulle opzioni di gestione dei dati per impostare un limite giornaliero o il campionamento per inserimento.

Gli addebiti di Application Insights vengono aggiunti alla fatturazione di Azure. È possibile visualizzare i dettagli della fattura Azure nella sezione Fatturazione del portale di Azure oppure nel [portale di fatturazione di Azure](https://account.windowsazure.com/Subscriptions). 

![Nel menu laterale scegliere Fatturazione.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Velocità dati
Esistono tre modi per limitare il volume di invio dei dati:

* Impostazione di un limite giornaliero. Per impostazione predefinita è 100 GB al giorno. Quando l'app raggiunge il limite, viene inviata un'e-mail e viene impedito l'uso di dati fino alla fine della giornata. Per modificarlo, accedere al pannello Data Volume Management (Gestione volume dati).
* [Campionamento](app-insights-sampling.md). Questo meccanismo può ridurre la quantità di dati di telemetria inviati da server e app client, con una distorsione minima delle metriche.
* Limitazione della quantità di dati al minuto. Per il piano tariffario Basic, il limite è di 200 punti dati al secondo in base a una media calcolata su 5 minuti; per piani Enterprise è di 500 punti dati al secondo in base a una media calcolata su 1 minuto. 

Per la limitazione, esistono tre bucket che vengono conteggiati separatamente:

* [Chiamate TrackTrace](app-insights-api-custom-events-metrics.md#track-trace) e [log acquisiti](app-insights-asp-net-trace-logs.md).
* [Eccezioni](app-insights-api-custom-events-metrics.md#track-exception), limitate a 50 punti al secondo.
* Tutti gli altri dati di telemetria (visualizzazioni pagina, sessioni, richieste, dipendenze, metrica, eventi personalizzati, risultati dei test Web).

*Cosa accade se l'app supera la velocità al secondo.*

* Il volume dei dati inviati dall'app viene valutato ogni minuto. Se il valore supera la velocità media al secondo calcolata nel minuto, il server rifiuta alcune richieste. L'SDK esegue il buffering dei dati e quindi tenta di inviare di nuovo, distribuendo una sovratensione nell'arco di diversi minuti. Se l'app continua a inviare dati che eccedono la limitazione, alcuni di questi verranno eliminati. (Gli SDK di ASP.NET, Java e JavaScript ritentano l'invio, mentre altri SDK potrebbero semplicemente eliminare i dati sottoposti a limitazione).

In caso di avvenuta limitazione, verrà visualizzata una notifica che avviserà che ciò si è verificato.

*Come è possibile sapere quanti punti dati vengono inviati dall'app?*

* Aprire il pannello Prezzi per visualizzare il grafico Volume dati.
* Altrimenti, in Esplora metriche, aggiungere un nuovo grafico e selezionare **Volume punti dati** come metrica. Attivare il raggruppamento in base a **Tipo di dati**.

## <a name="to-reduce-your-data-rate"></a>Per ridurre la velocità dei dati
Ecco alcune operazioni da eseguire per ridurre il volume di dati:

* Ridurre il limite di volume giornaliero. Per impostazione predefinita è 100 GB al giorno.
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


## <a name="limits-summary"></a>Riepilogo dei limiti
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Campionamento](app-insights-sampling.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/




<!--HONumber=Nov16_HO3-->


