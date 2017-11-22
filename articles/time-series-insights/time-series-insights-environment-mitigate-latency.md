---
title: Come monitorare le prestazioni, ridurre la limitazione ed evitare la latenza in Azure Time Series Insights | Microsoft Docs
description: Questo articolo illustra come monitorare, diagnosticare e attenuare i problemi di prestazioni che causano latenza e limitazione in Azure Time Series Insights.
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/15/2017
ms.openlocfilehash: 5af5589922ded802703b9ba8f3b0aa8c5524bbad
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="improve-performance-reduce-latency-and-mitigate-throttling-in-azure-time-series-insights"></a>Migliorare le prestazioni, ridurre la latenza e attenuare la limitazione in Azure Time Series Insights
Quando la quantità di dati in ingresso è superiore alla configurazione dell'ambiente, in Azure Time Series Insights si può verificare una latenza o una limitazione.

È possibile evitare la latenza o la limitazione configurando l'ambiente in modo appropriato per la quantità di dati che si vuole analizzare.

È molto probabile riscontrare latenza o limitazione nei casi seguenti:

- Aggiunta di un'origine eventi contenente dati meno recenti che potrebbero superare la velocità in ingresso assegnata, imponendo a Time Series Insights di recuperare.
- Aggiunta di più origini eventi a un ambiente con conseguente picco a causa degli eventi aggiuntivi, che potrebbero superare la capacità dell'ambiente.
- Push di grandi quantità di dati cronologici a un'origine eventi, che potrebbe determinare un ritardo imponendo a Time Series Insights di recuperare.
- Aggiunta di dati di riferimento alla telemetria, che determina un aumento delle dimensioni degli eventi.  Dal punto di vista della limitazione, un pacchetto di dati in ingresso di dimensioni pari a 32 KB viene considerato come 32 eventi, ognuno di 1 KB di dimensioni. Le dimensioni massime degli eventi consentite sono 32 KB e i pacchetti di dati di dimensioni superiori a 32 KB vengono troncati.


## <a name="monitor-latency-and-throttling-with-alerts"></a>Monitorare la latenza e la limitazione con avvisi

Gli avvisi consentono di diagnosticare e attenuare i problemi di latenza causati dall'ambiente. 

1. Nel portale di Azure fare clic su **Metriche**. 

   ![Metrica](media/environment-mitigate-latency/add-metrics.png)

2. Fare clic su **Aggiungi avviso per la metrica**.  

    ![Aggiungi avviso per la metrica](media/environment-mitigate-latency/add-metric-alert.png)

A questo punto è possibile configurare avvisi usando le metriche seguenti:

|Metrica  |Descrizione  |
|---------|---------|
|**Ingress Received Bytes** (Byte ricevuti in ingresso)     | Numero di byte non elaborati letti dalle origini eventi. Include in genere il nome e il valore delle proprietà.  |  
|**Ingress Received Invalid Messages** (Messaggi non validi ricevuti in ingresso)     | Numero dei messaggi non validi letti da tutte le origini eventi di Hub eventi di Azure o Hub IoT di Azure.      |
|**Ingress Received Messages** (Messaggi ricevuti in ingresso)   | Numero dei messaggi letti da tutte le origini eventi di hub eventi o hub IoT.        |
|**Ingress Stored Bytes** (Byte archiviati in ingresso)     | Dimensioni totali degli eventi archiviati e disponibili per le query. Le dimensioni sono calcolate solo sul valore delle proprietà.        |
|**Ingress Stored Events** (Eventi archiviati in ingresso)     |   Numero di eventi bidimensionali archiviati e disponibili per le query.      |

![Latency](media/environment-mitigate-latency/latency.png)

Una tecnica consiste nell'impostare un avviso **Ingress Stored Events** (Eventi archiviati in ingresso) per un valore maggiore o uguale a una soglia leggermente inferiore alla capacità totale dell'ambiente per un periodo di 2 ore.  Questo avviso consentirà di comprendere se si raggiunge costantemente la capacità e la probabilità di latenza è quindi elevata.  

Ad esempio, se è stato effettuato il provisioning di tre unità S1, con capacità in ingresso di 2100 eventi al minuto, è possibile impostare un avviso **Ingress Stored Events** (Eventi archiviati in ingresso) per un valore maggiore o uguale a 1900 eventi per 2 ore. Se si supera costantemente questa soglia attivando così l'avviso, è probabile che il provisioning sia insufficiente.  

Se si sospetta che sia stata applicata la limitazione, inoltre, è possibile confrontare **Ingress Received Messages** (Messaggi ricevuti in ingresso) con i messaggi in uscita dell'origine eventi.  Se l'ingresso nell'hub eventi è superiore a **Ingress Received Messages** (Messaggi ricevuti in ingresso), è probabile che a Time Series Insights venga applicata la limitazione.

## <a name="improving-performance"></a>Miglioramento delle prestazioni 
Per ridurre la limitazione o i casi di latenza, la correzione migliore consiste nell'aumentare la capacità dell'ambiente. 

È possibile evitare la latenza o la limitazione configurando l'ambiente in modo appropriato per la quantità di dati che si vuole analizzare. Per altre informazioni su come aggiungere capacità all'ambiente, vedere l'articolo su come [ridimensionare l'ambiente](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Passaggi successivi
- Per altre procedure di risoluzione dei problemi, vedere [Diagnosticare e risolvere i problemi nell'ambiente Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).
- Per ulteriore assistenza, avviare una conversazione nel [forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) o in [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). È anche possibile contattare il [supporto di Azure](https://azure.microsoft.com/support/options/) per opzioni di supporto assistito.
