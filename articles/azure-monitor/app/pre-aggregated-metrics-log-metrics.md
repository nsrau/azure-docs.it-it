---
title: Metriche basate su log e metriche preaggregate in Azure Application Insights | Microsoft Docs
description: Perché e quando usare metriche basate su log o metriche preaggregate in Azure Application Insights
services: application-insights
keywords: ''
author: vgorbenko
ms.author: vitalyg
ms.reviewer: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 96d4c2c8d6c2dd7722b7377dd7a0ffd42acd3126
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64572509"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Metriche basate su log e metriche preaggregate in Azure Application Insights

Questo articolo spiega la differenza tra le metriche "tradizionali" di Application Insights basate su log e le metriche preaggregate, attualmente disponibili in anteprima pubblica. Entrambi i tipi di metriche sono disponibili per gli utenti di Application Insights e ognuna offre vantaggi unici per il monitoraggio dell'integrità dell'applicazione, la diagnostica e l'analisi. Gli sviluppatori che si occupano della strumentazione di applicazioni possono scegliere il tipo di metrica più adatto per ogni scenario specifico, a seconda delle dimensioni dell'applicazione, del volume previsto di dati di telemetria e dei requisiti aziendali per la precisione delle metriche e la generazione di avvisi.

## <a name="log-based-metrics"></a>Metriche basate su log

Fino a poco tempo fa, il modello di dati di telemetria per il monitoraggio delle applicazioni in Application Insights era unicamente basato su un numero ridotto di tipi predefiniti di eventi, tra cui richieste, eccezioni, chiamate di dipendenza, visualizzazioni pagina e così via. Gli sviluppatori possono usare l'SDK per generare questi eventi manualmente (scrivendo codice che richiama in modo esplicito l'SDK) o possono affidarsi alla raccolta automatica di eventi tramite la strumentazione automatica. In entrambi i casi, il back-end di Application Insights archivia tutti gli eventi raccolti sotto forma di log e i pannelli di Application Insights nel portale di Azure fungono da strumento di analisi e diagnostica per la visualizzazione dei dati basati su eventi dai log.

L'uso di log per mantenere un set completo di eventi può offrire eccezionali vantaggi in termini di analisi e diagnostica. Ad esempio, è possibile ottenere un conteggio esatto delle richieste a un URL specifico con il numero di utenti distinti che hanno effettuato queste chiamate. In alternativa, è possibile ottenere tracce di diagnostica dettagliate, incluse le eccezioni e le chiamate di dipendenza per qualsiasi sessione utente. La disponibilità di questo tipo di informazioni può migliorare significativamente la visibilità riguardo all'integrità e all'utilizzo dell'applicazione, riducendo il tempo necessario per diagnosticare problemi relativi a un'app. 

Allo stesso tempo, la raccolta di un set completo di eventi può essere svantaggiosa (o persino impossibile) per le applicazioni che generano una grande quantità di dati di telemetria. Per i casi in cui il volume di eventi è troppo elevato, Application Insights implementa diverse tecniche di riduzione dei volumi di dati di telemetria, ad esempio il [campionamento](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) e l'[applicazione di filtri](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling), che riducono il numero di eventi raccolti e archiviati. Sfortunatamente, riducendo il numero di eventi archiviati si riduce anche l'accuratezza delle metriche che, in background, devono eseguire aggregazioni in fase di query degli eventi archiviati nei log.

> [!NOTE]
> In Application Insights le metriche basate sull'aggregazione in fase di query di eventi e misurazioni archiviati in log sono denominate metriche basate su log. Queste metriche in genere hanno molte dimensioni delle proprietà degli eventi e questo aspetto le rende più appropriate per l'analisi, ma l'accuratezza di queste metriche è influenzata negativamente dal campionamento e dai filtri.

## <a name="pre-aggregated-metrics"></a>Metriche preaggregate

Oltre alle metriche basate su log, nell'autunno del 2018 il team di Application Insights ha reso disponibile un'anteprima pubblica di un tipo di metriche che sono archiviate in un repository specializzato ottimizzato per le serie temporali. Le nuove metriche non vengono più mantenute come singoli eventi con un numero elevato di proprietà. Al contrario, vengono archiviate come serie temporali preaggregate e solo con dimensioni di chiave. Questa caratteristica le rende migliori in fase di query: il recupero dei dati avviene molto più rapidamente e richiede meno potenza di calcolo. Di conseguenza, sono possibili nuovi scenari, come la [generazione di avvisi praticamente in tempo reale in base alle dimensioni delle metriche](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts), [dashboard](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards) più reattivi e così via.

> [!IMPORTANT]
> Le metriche basate su log e quelle preaggregate coesistono in Azure Application Insights. Per distinguere tra i due tipi, nell'esperienza utente di Application Insights le metriche preaggregate sono ora denominate "metriche standard (anteprima)", mentre le metriche tradizionali derivate da eventi sono state rinominate "metriche basate su log".

Gli SDK più recenti ([Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK o versioni successive per .NET) preaggregano le metriche durante la raccolta prima dell'avvio di tecniche di riduzione dei volumi di dati di telemetria. Questo significa che l'accuratezza delle nuove metriche non è influenzata dal campionamento e dai filtri quando si usa la versione più recente di Application Insights SDK.

Per gli SDK che non implementano la preaggregazione, ovvero le versioni meno recenti di Application Insights SDK o per la strumentazione del browser, il back-end di Application Insights popola ancora le nuove metriche aggregando gli eventi ricevuti dall'endpoint di raccolta degli eventi di Application Insights. Questo significa che anche se non è possibile trarre vantaggio dal volume ridotto dei dati trasmessi in rete, è comunque possibile usare le metriche preaggregate e ottenere prestazioni migliori e supporto per la generazione di avvisi dimensionali praticamente in tempo reale con gli SDK che non preaggregano le metriche durante la raccolta.

Vale la pena sottolineare che l'endpoint di raccolta preaggrega gli eventi prima del campionamento per inserimento e di conseguenza il [campionamento per inserimento](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) non ha mai impatto sull'accuratezza delle metriche preaggregate, indipendentemente dalla versione dell'SDK usata con l'applicazione.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Uso della preaggregazione con metriche personalizzate di Application Insights

È possibile usare la preaggregazione con metriche personalizzate. I due vantaggi principali sono la possibilità di configurare e generare un avviso per una dimensione di una metrica personalizzata e la riduzione del volume di dati inviati dall'SDK all'endpoint di raccolta di Application Insights.

Esistono diversi [modi per l'invio di metriche personalizzate da Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Se la versione dell'SDK offre i metodi [GetMetric e TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric), questo è il modo migliore per inviare metriche personalizzate, perché in questo caso la preaggregazione avviene all'interno dell'SDK, riducendo non solo il volume di dati archiviati in Azure, ma anche il volume di dati trasmessi dall'SDK ad Application Insights. In caso contrario, usare il metodo [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric), che preaggrega gli eventi di metrica durante l'inserimento dati.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Dimensioni delle metriche personalizzate e preaggregazione

Tutte le metriche inviate tramite chiamate API [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) o [GetMetric e TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) vengono automaticamente archiviate sia in archivi di log sia in archivi di metriche. Tuttavia, mentre la versione basata su log della metrica personalizzata mantiene sempre tutte le dimensioni, la versione preaggregata della metrica viene archiviata senza dimensioni per impostazione predefinita. È possibile attivare la raccolta di dimensioni di metriche personalizzate nella scheda [Utilizzo e costi stimati](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) selezionando "Enable alerting on custom metric dimensions" (Abilita avvisi per dimensioni delle metriche personalizzate): 

![Utilizzo e costi stimati](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Perché la raccolta di dimensioni di metriche personalizzate è disattivata per impostazione predefinita?

La raccolta di dimensioni di metriche personalizzate è disattivata per impostazione predefinita perché in futuro l'archiviazione di metriche personalizzate con dimensioni verrà fatturata separatamente da Application Insights, mentre l'archiviazione di metriche personalizzate non dimensionali resterà gratuita (fino a una quota). Per informazioni sulle modifiche che stanno per essere apportate al modello tariffario, vedere la [pagina ufficiale dei prezzi](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Creazione di grafici ed esplorazione di metriche basate su log e preaggregate standard

Uso [Esplora metriche di monitoraggio di Azure](../platform/metrics-getting-started.md) per tracciare i grafici dalla metrica preaggregata e basato su log, nonché creare dashboard con i grafici. Dopo aver selezionato la risorsa di Application Insights desiderata, usare il selettore dello spazio dei nomi per passare tra metriche standard (anteprima) e metriche basate su log oppure selezionare uno spazio dei nomi delle metriche personalizzate:

![Spazio dei nomi delle metriche](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="next-steps"></a>Passaggi successivi

* [Generazione di avvisi praticamente in tempo reale](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric e TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)