---
title: Azure Application Insights | Microsoft Docs
description: Correlare i dati da Application Insights ad altri set di dati, ad esempio l'arricchimento dei dati o le tabelle di ricerca, le origini dati non di Application Insights e i dati personalizzati.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 121e4699bd6a72f6865d3a6ffdef58c1b3806047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082762"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Correlazione dei dati di Application Insights con origini dati personalizzate

Application Insights raccoglie diversi tipi di dati: eccezioni, tracce, visualizzazioni pagina e altro ancora. Sebbene ciò sia spesso sufficiente per analizzare le prestazioni, l'affidabilità e l'utilizzo dell'applicazione, esistono casi in cui è utile correlare i dati archiviati in Application Insights ad altri set di dati completamente personalizzati.

Alcune situazioni in cui è preferibile disporre di dati personalizzati includono:

- Tabelle di arricchimento dei dati o ricerca: integrare, ad esempio, un nome del server con il proprietario del server e il percorso lab in cui è disponibile 
- Correlazione con origini dati non di Application Insights: ad esempio, correlare i dati relativi a un acquisto in un archivio Web con informazioni provenienti dal servizio evasione degli acquisti per determinare l'accuratezza dei tempi di spedizione stimati 
- Dati completamente personalizzati: molti clienti Microsoft apprezzano il linguaggio di query e le prestazioni della piattaforma di log di Monitoraggio di Azure che esegue il backup di Application Insights e vogliono usarli per eseguire query su dati non correlati ad Application Insights. Ad esempio, per tenere traccia delle prestazioni di un pannello solare nel quadro di un'installazione domestica intelligente, come delineato [qui](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Procedura di correlazione di dati personalizzati con dati di Application Insights 

Poiché Application Insights è supportato dalla potente piattaforma di log di Monitoraggio di Azure, è possibile usare tutte le potenzialità di Monitoraggio di Azure per inserire i dati. Quindi, scriveremo query utilizzando l'operatore "join" che correlerà questi dati personalizzati con i dati disponibili nei log di Monitoraggio di Azure.Then, we will write queries using the "join" operator that will correlate this custom data with the data available to us in Azure Monitor logs. 

## <a name="ingesting-data"></a>Inserimento di dati

In questa sezione viene esaminata anche la procedura per ottenere i dati nei log di Monitoraggio di Azure.

Se non ne hai già uno, esegui il provisioning di una nuova area di lavoro di Log Analytics seguendo [queste istruzioni](../learn/quick-collect-azurevm.md) e includendo il passaggio "Crea un'area di lavoro".

Per iniziare a inviare i dati di log in Monitoraggio di Azure. Sono disponibili diverse opzioni:

- Per un meccanismo sincrono, è possibile chiamare direttamente [l'API dell'agente](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) di raccolta dati o usare il connettore App per la logica: è sufficiente cercare "Azure Log Analytics" e selezionare l'opzione "Invia dati":

  ![Screenshot di scelta e azione](./media/custom-data-correlation/01-logic-app-connector.png)  

- Per un'opzione asincrona, usare l'API di raccolta dati per creare una pipeline di elaborazione. Vedere [questo articolo](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) per informazioni dettagliate.

## <a name="correlating-data"></a>Correlazione dei dati

Application Insights si basa sulla piattaforma di log di Monitoraggio di Azure. Di conseguenza verranno usati [join tra risorse](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) per correlare i dati inseriti in Monitoraggio di Azure con i dati di Application Insights.

Ad esempio, è possibile inserire l'inventario e le ubicazioni del lab in una tabella denominata "LabLocations_CL" in un'area di lavoro di Log Analytics denominata "myLA". Se quindi si desidera esaminare le richieste registrate nell'app Application Insights denominata "myAI" e correlare i nomi dei computer che hanno servito le richieste alle posizioni di queste macchine archiviate nella tabella personalizzata menzionata in precedenza, è possibile eseguire la query seguente da il contesto di Application Insights o Monitoraggio di Azure:either the Application Insights or Azure Monitor context:

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
