---
title: "Monitorare l’utilizzo e le statistiche in Ricerca di Azure | Microsoft Docs"
description: Tenere traccia delle dimensioni di indice e consumo delle risorse per la Ricerca di Azure, un servizio di ricerca ospitato sul cloud in Microsoft Azure.
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 122948de-d29a-426e-88b4-58cbcee4bc23
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/29/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6e40acb01d6297aa66090859533fd48c108a9a88


---
# <a name="monitor-usage-and-query-metrics-in-an-azure-search-service"></a>Monitorare l’utilizzo e le metriche di query in Ricerca di Azure
Ricerca di Azure raccoglie le statistiche riguardanti l'esecuzione di query a livello di servizio, incluse le query al secondo, la latenza e la percentuale di query eliminate se il volume supera la capacità. Le metriche sono visibili nel portale tramite il pannello Monitoraggio.

   ![Screenshot dell'attività delle query al secondo][5]

Questo articolo descrive le metriche di tutti i servizi. Per informazioni approfondite sulle attività a livello di indice, abilitare l'analisi del traffico di ricerca e usare Power BI per visualizzare l'analisi. Per iniziare, visitare [Analisi del traffico di ricerca per Ricerca di Azure](search-traffic-analytics.md) .

## <a name="view-query-throughput-statistics"></a>Visualizzare le statistiche sulla velocità effettiva di query
Fare clic sul riquadro Monitoraggio nel dashboard del servizio per aprire il pannello Monitoraggio.

   ![Riquadro Monitoraggio][2]

Viene registrata l'attività di query a livello di servizio, relativamente all'esecuzione di query, alla latenza e alla limitazione. Le metriche vengono raccolte in modo continuo. Possono tuttavia passare alcuni minuti prima che il portale visualizzi le attività più recenti. 

Fare clic su uno dei riquadri in Metrica per visualizzare i dettagli, aggiungere gli avvisi, abilitare la diagnostica o modificare il grafico.

  ![Comandi del pannello Metrica][4]

### <a name="set-up-alerts"></a>Impostare gli avvisi
Dalla pagina di dettaglio relativa alle metriche, è possibile configurare gli avvisi per attivare una notifica di posta elettronica se l'esecuzione di query, la latenza o la limitazione non rispettano i corrispondenti criteri definiti.

### <a name="enable-diagnostics"></a>Abilitare la diagnostica
Quando la diagnostica viene attivata, è possibile configurare dove archiviare i dati di diagnostica, se si vogliono includere i registri operazioni e le metriche e per quanto tempo i dati saranno mantenuti.

### <a name="change-chart-type-and-data-collection-interval"></a>Modificare il tipo di grafico e l'intervallo di raccolta dei dati
Per ogni metrica, è possibile fare clic su **modifica** per modificare la visualizzazione da grafico a linee a grafico a barre o modificare l'asse x per coprire un intervallo di tempo diverso.

  ![Configurazione dell'intervallo di tempo][3]

## <a name="view-counts-and-resource-usage-in-the-portal"></a>Visualizzare i conteggi e l'uso delle risorse nel portale
Il controllo dell'aumento degli indici e delle dimensioni del documento consente di regolare in modo proattivo la capacità prima di raggiungere il limite massimo stabilito per il servizio. 

Per monitorare l'uso delle risorse, visualizzare i conteggi e le statistiche per il servizio nel [portale](https://portal.azure.com). È anche possibile ottenere informazioni a livello di codice se si sta compilando uno strumento di amministrazione del servizio personalizzato.

1. Accedere al [portale](https://portal.azure.com). 
2. Aprire il dashboard relativo al servizio Ricerca di Azure. Nella Home page sono presenti riquadri per il servizio oppure è possibile accedere al servizio tramite Sfoglia nell'indice. 

La sezione Utilizzo include un indicatore che indica la quantità di risorse disponibili attualmente in uso. Per informazioni sui limiti dei servizi per indici, documenti e archiviazione, vedere [Limiti dei servizi](search-limits-quotas-capacity.md).

  ![Riquadro Utilizzo][1]

> [!NOTE]
> Lo screenshot precedente si riferisce al servizio gratuito, che offre una sola replica e una partizione e può ospitare solo 3 indici, 10.000 documenti o 50 MB di dati, a seconda di quale di queste condizioni si verifichi per prima. I servizi creati con piano Basic o Standard hanno limiti più elevati. Per altre informazioni sulla scelta di un piano, vedere [Scegliere uno SKU o un piano](search-sku-tier.md).
> 
> 

### <a name="get-index-statistics-using-the-rest-api"></a>Ottenere le statistiche di indice utilizzando l'API REST
L'API REST di Ricerca di Azure e .NET SDK forniscono l'accesso a livello di codice alle metriche di servizio.  Se si utilizzano [indicizzatori](https://msdn.microsoft.com/library/azure/dn946891.aspx) per caricare un indice dal database SQL di Azure o da DocumentDB, è disponibile un'API aggiuntiva per ottenere i numeri necessari. 

* [Ottenere le statistiche di indice](https://msdn.microsoft.com/library/azure/dn798942.aspx)
* [Conteggio documenti](https://msdn.microsoft.com/library/azure/dn798924.aspx)
* [Ottenere lo stato dell'indicizzatore](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## <a name="next-steps"></a>Passaggi successivi
Rivedere [Limiti dei servizi in Ricerca di Azure](search-limits-quotas-capacity.md) per indicazioni su come bilanciare l'allocazione delle partizioni e delle repliche per un servizio esistente. 

Visitare [Amministrazione del servizio per Ricerca di Azure nel portale di Azure](search-manage.md) per altre informazioni sull'amministrazione del servizio o [Prestazioni e ottimizzazione](search-performance-optimization.md) per indicazioni sull'ottimizzazione.

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[2]: ./media/search-monitor-usage/AzSearch-Monitor-Tile.PNG
[3]: ./media/search-monitor-usage/AzSearch-Monitor-Intervals.PNG
[4]: ./media/search-monitor-usage/AzSearch-Monitor-AlertCmd.PNG
[5]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG








<!--HONumber=Nov16_HO3-->


