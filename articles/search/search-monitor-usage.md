---
title: "Monitorare l’utilizzo e le statistiche in Ricerca di Azure | Microsoft Docs"
description: Tenere traccia delle dimensioni di indice e consumo delle risorse per la Ricerca di Azure, un servizio di ricerca ospitato sul cloud in Microsoft Azure.
services: search
documentationcenter: 
author: bernitorres
manager: jlembicz
editor: 
tags: azure-portal
ms.assetid: 122948de-d29a-426e-88b4-58cbcee4bc23
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 05/01/2017
ms.author: betorres
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 16cb5a1e16a59200f0e731622398efcf24c3f777
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="monitoring-an-azure-search-service"></a>Monitoraggio di un servizio di Ricerca di Azure

Ricerca di Azure offre varie risorse per tenere traccia dell'utilizzo e delle prestazioni dei servizi di ricerca. Consente di accedere a metriche, log, statistiche di indice e funzionalità estese di monitoraggio in Power BI. L'articolo descrive come abilitare le diverse strategie di monitoraggio e come interpretare i dati risultanti.

## <a name="azure-search-metrics"></a>Metriche di Ricerca di Azure
Le metriche offrono una visibilità quasi in tempo reale nel servizio di ricerca e sono disponibili per ogni servizio, senza alcuna impostazione aggiuntiva. Consentono di tenere traccia delle prestazioni del servizio fino a 30 giorni.

Ricerca di Azure raccoglie i dati per tre metriche diverse:

* Latenza ricerca: tempo necessario al servizio di ricerca per elaborare le query di ricerca, aggregate per minuto.
* Query di ricerca al secondo: numero di query di ricerca ricevute al secondo, aggregate per minuto.
* Percentuale query di ricerca limitate: percentuale di query di ricerca che sono state limitate, aggregate per minuto.

![Screenshot dell'attività delle query al secondo][1]

### <a name="set-up-alerts"></a>Impostare gli avvisi
Dalla pagina di dettaglio relativa alle metriche è possibile configurare gli avvisi per attivare una notifica di posta elettronica o un'azione automatica quando una metrica non rispetta un limite definito.

Per altre informazioni sulle metriche, vedere la documentazione completa su Monitoraggio di Azure.  

## <a name="how-to-track-resource-usage"></a>Come tenere traccia dell'utilizzo delle risorse
Il controllo dell'aumento degli indici e delle dimensioni del documento consente di regolare in modo proattivo la capacità prima di raggiungere il limite massimo stabilito per il servizio. È possibile eseguire questa operazione nel portale o a livello di codice tramite l'API REST.

### <a name="using-the-portal"></a>Tramite il portale

Per monitorare l'uso delle risorse, visualizzare i conteggi e le statistiche per il servizio nel [portale](https://portal.azure.com).

1. Accedere al [portale](https://portal.azure.com).
2. Aprire il dashboard relativo al servizio Ricerca di Azure. Nella Home page sono presenti riquadri per il servizio oppure è possibile accedere al servizio tramite Sfoglia nell'indice.

La sezione Utilizzo include un indicatore che indica la quantità di risorse disponibili attualmente in uso. Per informazioni sui limiti dei servizi per indici, documenti e archiviazione, vedere [Limiti dei servizi](search-limits-quotas-capacity.md).

  ![Riquadro Utilizzo][2]

> [!NOTE]
> Lo screenshot precedente si riferisce al servizio gratuito, che offre una sola replica e una partizione e può ospitare solo 3 indici, 10.000 documenti o 50 MB di dati, a seconda di quale di queste condizioni si verifichi per prima. I servizi creati con piano Basic o Standard hanno limiti più elevati. Per altre informazioni sulla scelta di un piano, vedere [Scegliere uno SKU o un piano](search-sku-tier.md).
>
>

### <a name="using-the-rest-api"></a>Utilizzo dell'API REST
L'API REST di Ricerca di Azure e .NET SDK forniscono l'accesso a livello di codice alle metriche di servizio.  Se si usano [indicizzatori](https://msdn.microsoft.com/library/azure/dn946891.aspx) per caricare un indice dal database SQL di Azure o da Azure Cosmos DB, è disponibile un'API aggiuntiva per ottenere i numeri necessari.

* [Ottenere le statistiche di indice](/rest/api/searchservice/get-index-statistics)
* [Conteggio documenti](/rest/api/searchservice/count-documents)
* [Ottenere lo stato dell'indicizzatore](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>Come esportare i log e le metriche

È possibile esportare i log delle operazioni per il servizio e i dati non elaborati per le metriche descritte nella sezione precedente. I log operazioni mostrano il modo in cui il servizio viene usato e in che modo può essere usato da Power BI quando si copiano dati in un account di archiviazione. Ricerca di Azure include per questo scopo un pacchetto di contenuti Power BI per il monitoraggio.


### <a name="enabling-monitoring"></a>Abilitazione del monitoraggio
Aprire il servizio Ricerca di Azure nel [portale di Azure](http://portal.azure.com) sotto l'opzione Abilita monitoraggio.

Scegliere i dati da esportare: log, metriche o entrambi. È possibile copiare i dati in un account di archiviazione, inviarli a un hub eventi o esportarli in Log Analytics.

![Come abilitare il monitoraggio nel portale][3]

Per abilitare il monitoraggio tramite PowerShell o l'interfaccia della riga di comando di Azure, vedere la documentazione [qui](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

### <a name="logs-and-metrics-schemas"></a>Schemi di log e di metriche
I dati che vengono copiati in un account di archiviazione vengono formattati come JSON e inseriti in due contenitori:

* insights-logs-operationlogs: per i log relativi al traffico ricerca
* insights-metrics-pt1m: per le metriche

È presente un BLOB ogni ora per ciascun contenitore.

Percorso di esempio: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>Schema del log
I BLOB dei log contengono i log di traffico del servizio di ricerca.
Ogni BLOB ha un oggetto radice denominato **record** che contiene una matrice di oggetti di log.
Ogni BLOB contiene record su tutte le operazioni eseguite nell'arco della stessa ora.

| Nome | Tipo | Esempio | Note |
| --- | --- | --- | --- |
| time |datetime |"2015-12-07T00:00:43.6872559Z" |Timestamp dell'operazione |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |resourceId in uso |
| operationName |string |"Query.Search" |Nome dell'operazione |
| operationVersion |string |"2015-02-28" |api-version usata |
| category |string |"OperationLogs" |costante |
| resultType |string |"Esito positivo" |Valori possibili: esito positivo o negativo |
| resultSignature |int |200 |Codice risultato HTTP |
| durationMS |int |50 |Durata dell'operazione in millisecondi |
| properties |object |Vedere la tabella seguente |Oggetto contenente dati specifici dell'operazione |

**Schema delle proprietà**
| Nome | Tipo | Esempio | Note |
| --- | --- | --- | --- |
| Descrizione |string |"GET /indexes('content')/docs" |Endpoint dell'operazione |
| Query |string |"?search=AzureSearch&$count=true&api-version=2015-02-28" |Parametri della query |
| Documenti |int |42 |Numero di documenti elaborati |
| IndexName |string |"testindex" |Nome dell'indice associato all'operazione |

#### <a name="metrics-schema"></a>Schema delle metriche
| Nome | Tipo | Esempio | Note |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |ID risorsa in uso |
| metricName |string |"Latenza" |Nome della metrica |
| time |datetime |"2015-12-07T00:00:43.6872559Z" |Timestamp dell'operazione |
| average |int |64 |Valore medio degli esempi non elaborati nell'intervallo di tempo della metrica |
| minimum |int |37 |Valore minimo degli esempi non elaborati nell'intervallo di tempo della metrica |
| maximum |int |78 |Valore massimo degli esempi non elaborati nell'intervallo di tempo della metrica |
| total |int |258 |Valore totale degli esempi non elaborati nell'intervallo di tempo della metrica |
| count |int |4 |Numero degli esempi non elaborati usati per generare la metrica |
| timegrain |string |"PT1M" |Intervallo di tempo della metrica nel formato ISO 8601 |

Tutte le metriche vengono segnalate in intervalli di un minuto. Ogni metrica espone i valori minimi, massimi e medi al minuto.

Per la metrica SearchQueriesPerSecond, il valore minimo è quello più basso per le query di ricerca al secondo registrato durante questo minuto. Lo stesso vale anche per il valore massimo. Il valore medio è l'aggregato nel corso dell'intero minuto.
Considerare questo scenario nell'arco di un minuto: un secondo di carico elevato, che è il valore massimo per SearchQueriesPerSecond, seguito da 58 secondi di carico medio e infine da un secondo con una sola query, che è il valore minimo.

Per ThrottledSearchQueriesPercentage, i valori minimo, massimo, medio e totale corrispondono tutti allo stesso valore: la percentuale di query di ricerca che sono state limitate, dal numero totale di query di ricerca nell'arco di un minuto.

## <a name="analyzing-your-data-with-power-bi"></a>Analisi dei dati con Power BI

Si consiglia di usare [Power BI](https://powerbi.microsoft.com) per esplorare e visualizzare i dati. È possibile connettersi facilmente all'account di Archiviazione di Azure e avviare rapidamente l'analisi dei dati.

Ricerca di Azure include un [pacchetto di contenuti Power BI](https://app.powerbi.com/getdata/services/azure-search) che consente di monitorare ed esaminare il traffico delle ricerche attraverso tabelle e grafici predefiniti. Il pacchetto contiene un set di report Power BI che si connettono automaticamente ai dati e forniscono informazioni visive dettagliate sul servizio di ricerca. Per altre informazioni, vedere la [pagina della Guida del pacchetto di contenuti](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

![Dashboard di Power BI per Ricerca di Azure][4]

## <a name="next-steps"></a>Passaggi successivi
Rivedere [Limiti dei servizi in Ricerca di Azure](search-limits-quotas-capacity.md) per indicazioni su come bilanciare l'allocazione delle partizioni e delle repliche per un servizio esistente.

Visitare [Amministrazione del servizio per Ricerca di Azure nel portale di Azure](search-manage.md) per altre informazioni sull'amministrazione del servizio o [Prestazioni e ottimizzazione](search-performance-optimization.md) per indicazioni sull'ottimizzazione.

Altre informazioni sulla creazione di report utili Per informazioni dettagliate, vedere [Introduzione a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/).

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png

