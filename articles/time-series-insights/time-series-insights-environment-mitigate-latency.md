---
title: Come monitorare e ridurre la limitazione in Azure Time Series Insights | Microsoft Docs
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
ms.date: 11/27/2017
ms.openlocfilehash: ac59359eb6af268f311534d90e1529fc5e41094f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Monitorare e ridurre la limitazione per evitare la latenza in Azure Time Series Insights
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

   ![Metriche](media/environment-mitigate-latency/add-metrics.png)

2. Fare clic su **Aggiungi avviso per la metrica**.  

    ![Aggiungi avviso per la metrica](media/environment-mitigate-latency/add-metric-alert.png)

A questo punto è possibile configurare avvisi usando le metriche seguenti:

|Metrica  |DESCRIZIONE  |
|---------|---------|
|**Ingress Received Bytes** (Byte ricevuti in ingresso)     | Numero di byte non elaborati letti dalle origini eventi. Include in genere il nome e il valore delle proprietà.  |  
|**Ingress Received Invalid Messages** (Messaggi non validi ricevuti in ingresso)     | Numero dei messaggi non validi letti da tutte le origini eventi di Hub eventi di Azure o Hub IoT di Azure.      |
|**Ingress Received Messages** (Messaggi ricevuti in ingresso)   | Numero dei messaggi letti da tutte le origini eventi di hub eventi o hub IoT.        |
|**Ingress Stored Bytes** (Byte archiviati in ingresso)     | Dimensioni totali degli eventi archiviati e disponibili per le query. Le dimensioni sono calcolate solo sul valore delle proprietà.        |
|**Ingress Stored Events** (Eventi archiviati in ingresso)     |   Numero di eventi bidimensionali archiviati e disponibili per le query.      |
|**Ingress Received Message Time Lag** (Tempo di ritardo messaggi ricevuti in ingresso)    |  Differenza tra l'ora in cui il messaggio viene accodato nell'origine eventi e l'ora di elaborazione in ingresso.      |
|**Ingress Received Message Count Lag** (Differenza numero messaggi ricevuti in ingresso)    |  Differenza tra il numero di sequenza dell'ultimo messaggio accodato nella partizione di origine eventi e il numero di sequenza del messaggio elaborato in ingresso.      |


![Latenza](media/environment-mitigate-latency/latency.png)

In presenza di limitazioni, verrà visualizzato un valore per *Ingress Received Message Time Lag* (Tempo di ritardo messaggi ricevuti in ingresso) che indica il numero di minuti di ritardo di Time Series Insights rispetto all'ora di arrivo effettiva del messaggio all'origine eventi, escluso il tempo di indicizzazione di circa 30-60 secondi.  Anche per *Ingress Received Message Count Lag* (Differenza numero messaggi ricevuti in ingresso) deve essere disponibile un valore, che consente di determinare di quanti messaggi si è in ritardo.  Il modo più semplice per mettersi in pari consiste nell'aumentare la capacità dell'ambiente fino a dimensioni che consentono di recuperare la differenza.  

Ad esempio, se è disponibile un ambiente S1 a singola unità e si riscontra un ritardo di cinque milioni di messaggi, è possibile aumentare le dimensioni dell'ambiente a sei unità per circa un giorno per mettersi in pari.  È possibile aumentare ulteriormente per recuperare più velocemente.  Si tratta di una situazione comune durante il provisioning iniziale di un ambiente, in particolare quando lo si connette a un'origine eventi che già include eventi oppure quando si esegue un caricamento in blocco di molti dati cronologici.

Un'altra tecnica consiste nell'impostare un avviso **Ingress Stored Events** (Eventi archiviati in ingresso) per un valore maggiore o uguale a una soglia leggermente inferiore alla capacità totale dell'ambiente per un periodo di 2 ore.  Questo avviso consentirà di comprendere se si raggiunge costantemente la capacità e la probabilità di latenza è quindi elevata.  

Ad esempio, se è stato effettuato il provisioning di tre unità S1, con capacità in ingresso di 2100 eventi al minuto, è possibile impostare un avviso **Ingress Stored Events** (Eventi archiviati in ingresso) per un valore maggiore o uguale a 1900 eventi per 2 ore. Se si supera costantemente questa soglia attivando così l'avviso, è probabile che il provisioning sia insufficiente.  

Se si sospetta che sia stata applicata la limitazione, inoltre, è possibile confrontare **Ingress Received Messages** (Messaggi ricevuti in ingresso) con i messaggi in uscita dell'origine eventi.  Se l'ingresso nell'hub eventi è superiore a **Ingress Received Messages** (Messaggi ricevuti in ingresso), è probabile che a Time Series Insights venga applicata la limitazione.

## <a name="improving-performance"></a>Miglioramento delle prestazioni 
Per ridurre la limitazione o i casi di latenza, la correzione migliore consiste nell'aumentare la capacità dell'ambiente. 

È possibile evitare la latenza o la limitazione configurando l'ambiente in modo appropriato per la quantità di dati che si vuole analizzare. Per altre informazioni su come aggiungere capacità all'ambiente, vedere l'articolo su come [ridimensionare l'ambiente](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Passaggi successivi
- Per altre procedure di risoluzione dei problemi, vedere [Diagnosticare e risolvere i problemi nell'ambiente Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).
- Per ulteriore assistenza, avviare una conversazione nel [forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) o in [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). È anche possibile contattare il [supporto di Azure](https://azure.microsoft.com/support/options/) per opzioni di supporto assistito.
