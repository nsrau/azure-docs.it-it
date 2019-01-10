---
title: Azure Application Insights | Microsoft Docs
description: ''
services: application-insights
documentationcenter: ''
author: eternovsky
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.author: Evgeny.Ternovsky
ms.openlocfilehash: d034bf130440fdb5b783db41161ab5a21a306478
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103113"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Correlazione dei dati di Application Insights con origini dati personalizzate

Application Insights raccoglie diversi tipi di dati: eccezioni, tracce, visualizzazioni pagina e altro ancora. Anche se ciò è spesso sufficiente per analizzare le prestazioni, l'affidabilità e l'utilizzo di un'applicazione, esistono casi in cui è utile correlare i dati archiviati in Application Insights ad altri set di dati completamente personalizzati.

Alcune situazioni in cui è preferibile disporre di dati personalizzati includono:

- Tabelle di arricchimento dei dati o ricerca: integrare, ad esempio, un nome del server con il proprietario del server e il percorso lab in cui è disponibile 
- Correlazione con origini dati non di Application Insights: ad esempio, correlare i dati relativi a un acquisto in un archivio Web con informazioni provenienti dal servizio evasione degli acquisti per determinare l'accuratezza dei tempi di spedizione stimati 
- Dati completamente personalizzati: molti clienti Microsoft apprezzano il linguaggio di query e le prestazioni della piattaforma dati di Log Analytics che esegue il backup di Application Insights e desiderano usarli per eseguire query su dati che non sono correlati ad Application Insights. Ad esempio, per tenere traccia delle prestazioni di un pannello solare nel quadro di un'installazione domestica intelligente, come delineato [qui]( https://blogs.catapultsystems.com/cfuller/archive/2017/10/04/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Procedura di correlazione di dati personalizzati con dati di Application Insights 

Poiché Application Insights è supportato dalla potente piattaforma di dati di Log Analytics, è possibile usare tutte le potenzialità di Log Analytics per inserire i dati. Quindi, verranno scritte query che usano l'operatore "join" per mettere in correlazione i dati personalizzati con i dati disponibili in Log Analytics. 

## <a name="ingesting-data"></a>Inserimento di dati

In questa sezione viene esaminata anche la procedura per ottenere i dati in Log Analytics.

Se non se ne possiede già una, effettuare il provisioning di una nuova area di lavoro di Log Analytics seguendo [queste istruzioni]( https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm) fino al passaggio "creare un'area di lavoro" incluso.

Iniziare a inviare dati in Log Analytics. Sono disponibili diverse opzioni:

- Per un meccanismo sincrono, è possibile chiamare direttamente l'[API di raccolta dati](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) o usare il connettore di App per la logica. È sufficiente cercare "Azure Log Analytics" e selezionare l'opzione "Invia dati":

 ![Screenshot di scelta e azione](./media/custom-data-correlation/01-logic-app-connector.png)  

- Per un'opzione asincrona, usare l'API di raccolta dati per creare una pipeline di elaborazione. Per informazioni dettagliate, vedere [questo articolo](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api).

## <a name="correlating-data"></a>Correlazione dei dati

Application Insights si basa sulla piattaforma dati di Log Analytics. Di conseguenza verranno usati [join tra risorse](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) per correlare i dati inseriti in Log Analytics con i dati di Application Insights.

Ad esempio, è possibile inserire l'inventario e i percorsi lab in una tabella denominata "LabLocations_CL" in un'area di lavoro di Log Analytics denominata "myLA". Quindi, per esaminare le richieste registrate nell'app di Application Insights denominata "myAI" e correlare i nomi di computer che hanno servito le richieste ai percorsi di tali computer archiviati nella tabella personalizzata menzionata in precedenza, è possibile eseguire la query seguente dal contesto di Application Insights o di Log Analytics:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Passaggi successivi

- Consultare il riferimento all'[API di raccolta dati](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api).
- Altre informazioni sul [join tra risorse](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).
