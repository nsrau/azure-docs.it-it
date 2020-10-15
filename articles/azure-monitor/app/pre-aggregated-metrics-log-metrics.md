---
title: Metriche basate su log e metriche preaggregate in Azure Application Insights | Microsoft Docs
description: Perché e quando usare metriche basate su log o metriche preaggregate in Azure Application Insights
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f7bfa15b12618715bf0d911e4b4927a1fa327107
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91539130"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Metriche basate su log e metriche preaggregate in Azure Application Insights

Questo articolo illustra la differenza tra le metriche "tradizionali" Application Insights basate sui log e le metriche pre-aggregate. Entrambi i tipi di metriche sono disponibili per gli utenti di Application Insights e ognuna offre vantaggi unici per il monitoraggio dell'integrità dell'applicazione, la diagnostica e l'analisi. Gli sviluppatori che si occupano della strumentazione di applicazioni possono scegliere il tipo di metrica più adatto per ogni scenario specifico, a seconda delle dimensioni dell'applicazione, del volume previsto di dati di telemetria e dei requisiti aziendali per la precisione delle metriche e la generazione di avvisi.

## <a name="log-based-metrics"></a>Metriche basate su log

In passato, il modello di dati di telemetria per il monitoraggio delle applicazioni in Application Insights era basato esclusivamente su un numero ridotto di tipi di eventi predefiniti, ad esempio richieste, eccezioni, chiamate di dipendenza, visualizzazioni di pagina e così via. Gli sviluppatori possono usare l'SDK per emettere manualmente questi eventi (scrivendo codice che richiama in modo esplicito l'SDK) oppure possono basarsi sulla raccolta automatica di eventi dalla strumentazione automatica. In entrambi i casi, il back-end di Application Insights archivia tutti gli eventi raccolti sotto forma di log e i pannelli di Application Insights nel portale di Azure fungono da strumento di analisi e diagnostica per la visualizzazione dei dati basati su eventi dai log.

L'uso di log per mantenere un set completo di eventi può offrire eccezionali vantaggi in termini di analisi e diagnostica. Ad esempio, è possibile ottenere un conteggio esatto delle richieste a un URL specifico con il numero di utenti distinti che hanno effettuato queste chiamate. In alternativa, è possibile ottenere tracce di diagnostica dettagliate, incluse le eccezioni e le chiamate di dipendenza per qualsiasi sessione utente. La disponibilità di questo tipo di informazioni può migliorare significativamente la visibilità riguardo all'integrità e all'utilizzo dell'applicazione, riducendo il tempo necessario per diagnosticare problemi relativi a un'app.

Allo stesso tempo, la raccolta di un set completo di eventi può risultare poco pratica (o addirittura impossibile) per le applicazioni che generano un volume elevato di dati di telemetria. Per i casi in cui il volume di eventi è troppo elevato, Application Insights implementa diverse tecniche di riduzione dei volumi di dati di telemetria, ad esempio il [campionamento](./sampling.md) e l'[applicazione di filtri](./api-filtering-sampling.md), che riducono il numero di eventi raccolti e archiviati. Sfortunatamente, riducendo il numero di eventi archiviati si riduce anche l'accuratezza delle metriche che, in background, devono eseguire aggregazioni in fase di query degli eventi archiviati nei log.

> [!NOTE]
> In Application Insights le metriche basate sull'aggregazione in fase di query di eventi e misurazioni archiviati in log sono denominate metriche basate su log. Queste metriche in genere hanno molte dimensioni delle proprietà degli eventi e questo aspetto le rende più appropriate per l'analisi, ma l'accuratezza di queste metriche è influenzata negativamente dal campionamento e dai filtri.

## <a name="pre-aggregated-metrics"></a>Metriche preaggregate

Oltre alle metriche basate su log, alla fine del 2018, il team Application Insights ha fornito un'anteprima pubblica delle metriche archiviate in un repository specializzato ottimizzato per le serie temporali. Le nuove metriche non vengono più mantenute come singoli eventi con un numero elevato di proprietà. Al contrario, vengono archiviate come serie temporali preaggregate e solo con dimensioni di chiave. Questa caratteristica le rende migliori in fase di query: il recupero dei dati avviene molto più rapidamente e richiede meno potenza di calcolo. Di conseguenza, sono possibili nuovi scenari, come la [generazione di avvisi praticamente in tempo reale in base alle dimensioni delle metriche](../platform/alerts-metric-near-real-time.md), [dashboard](./overview-dashboard.md) più reattivi e così via.

> [!IMPORTANT]
> Le metriche basate su log e quelle preaggregate coesistono in Azure Application Insights. Per distinguere i due elementi, nella Application Insights UX le metriche pre-aggregate sono ora denominate "metriche standard (anteprima)", mentre le metriche tradizionali dagli eventi sono state rinominate "metriche basate su log".

Gli SDK più recenti ([Application Insights 2,7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK o versioni successive per .NET) metriche di pre-aggregazione durante la raccolta. Questo vale per le  [metriche standard inviate per impostazione predefinita](../platform/metrics-supported.md#microsoftinsightscomponents) , pertanto l'accuratezza non è influenzata dal campionamento o dal filtro. Si applica anche alle metriche personalizzate inviate tramite [Getmetric](./api-custom-events-metrics.md#getmetric) , con conseguente minore inserimento di dati e costi ridotti.

Per gli SDK che non implementano la pre-aggregazione (ovvero le versioni precedenti di Application Insights SDK o per la strumentazione del browser), il Application Insights back-end compila ancora le nuove metriche aggregando gli eventi ricevuti dall'endpoint della raccolta di eventi Application Insights. Ciò significa che anche se non è possibile trarre vantaggio dal volume ridotto dei dati trasmessi in rete, è comunque possibile usare le metriche pre-aggregate e provare a migliorare le prestazioni e il supporto degli avvisi dimensionali quasi in tempo reale con SDK che non preaggregano le metriche durante la raccolta.

Vale la pena sottolineare che l'endpoint di raccolta preaggrega gli eventi prima del campionamento per inserimento e di conseguenza il [campionamento per inserimento](./sampling.md) non ha mai impatto sull'accuratezza delle metriche preaggregate, indipendentemente dalla versione dell'SDK usata con l'applicazione.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Uso della preaggregazione con metriche personalizzate di Application Insights

È possibile usare la preaggregazione con metriche personalizzate. I due vantaggi principali sono la possibilità di configurare e generare un avviso per una dimensione di una metrica personalizzata e la riduzione del volume di dati inviati dall'SDK all'endpoint di raccolta di Application Insights.

Esistono diversi [modi per l'invio di metriche personalizzate da Application Insights SDK](./api-custom-events-metrics.md). Se la versione dell'SDK offre i metodi [GetMetric e TrackValue](./api-custom-events-metrics.md#getmetric), questo è il modo migliore per inviare metriche personalizzate, perché in questo caso la preaggregazione avviene all'interno dell'SDK, riducendo non solo il volume di dati archiviati in Azure, ma anche il volume di dati trasmessi dall'SDK ad Application Insights. In caso contrario, usare il metodo [trackMetric](./api-custom-events-metrics.md#trackmetric), che preaggrega gli eventi di metrica durante l'inserimento dati.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Dimensioni delle metriche personalizzate e preaggregazione

Tutte le metriche inviate tramite chiamate API [trackMetric](./api-custom-events-metrics.md#trackmetric) o [GetMetric e TrackValue](./api-custom-events-metrics.md#getmetric) vengono automaticamente archiviate sia in archivi di log sia in archivi di metriche. Tuttavia, mentre la versione basata su log della metrica personalizzata mantiene sempre tutte le dimensioni, la versione preaggregata della metrica viene archiviata senza dimensioni per impostazione predefinita. È possibile attivare la raccolta di dimensioni delle metriche personalizzate nella scheda [utilizzo e costo stimato](./pricing.md) selezionando "Abilita avvisi sulle dimensioni della metrica personalizzata": 

![Utilizzo e costi stimati](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Perché la raccolta di dimensioni di metriche personalizzate è disattivata per impostazione predefinita?

La raccolta di dimensioni di metriche personalizzate è disattivata per impostazione predefinita perché in futuro l'archiviazione di metriche personalizzate con dimensioni verrà fatturata separatamente da Application Insights, mentre l'archiviazione di metriche personalizzate non dimensionali resterà gratuita (fino a una quota). Per informazioni sulle modifiche che stanno per essere apportate al modello tariffario, vedere la [pagina ufficiale dei prezzi](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Creazione di grafici ed esplorazione di metriche basate su log e preaggregate standard

Usare [monitoraggio di Azure Esplora metriche](../platform/metrics-getting-started.md) per tracciare i grafici dalle metriche pre-aggregate e basate su log e per creare dashboard con grafici. Dopo aver selezionato la risorsa di Application Insights desiderata, usare il selettore dello spazio dei nomi per passare tra metriche standard (anteprima) e metriche basate su log oppure selezionare uno spazio dei nomi delle metriche personalizzate:

![Spazio dei nomi delle metriche](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Modelli di prezzi per le metriche di Application Insights

L'inserimento di metriche in Application Insights, in base al log o pre-aggregato, genererà costi in base alle dimensioni dei dati inseriti, come descritto [qui](./pricing.md#pricing-model). Le metriche personalizzate, incluse tutte le relative dimensioni, vengono sempre archiviate nel Application Insights log-Store. Inoltre, per impostazione predefinita, una versione pre-aggregata delle metriche personalizzate (senza dimensioni) viene trasmessa all'archivio di metriche.

Selezionando l'opzione [Abilita avvisi sulle dimensioni della metrica personalizzata](#custom-metrics-dimensions-and-pre-aggregation) per archiviare tutte le dimensioni delle metriche pre-aggregate nell'archivio delle metriche, è possibile generare costi **aggiuntivi** in base ai [prezzi delle metriche personalizzate](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="next-steps"></a>Passaggi successivi

* [Generazione di avvisi praticamente in tempo reale](../platform/alerts-metric-near-real-time.md)
* [GetMetric e TrackValue](./api-custom-events-metrics.md#getmetric)
