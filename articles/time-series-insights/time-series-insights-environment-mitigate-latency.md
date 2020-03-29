---
title: Come monitorare e ridurre la limitazione delle richieste - Azure Time Series Insights Documenti Microsoft
description: Informazioni su come monitorare, diagnosticare e ridurre i problemi di prestazioni che causano latenza e limitazione in Azure Time Series Insights.Learn how to monitor, diagnose, and mitigate performance issues that cause latency and throttling in Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 245a0b18187ff1c1b226e94b03374f2c071e51c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314828"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Monitorare e ridurre la limitazione per evitare la latenza in Azure Time Series Insights

Quando la quantità di dati in ingresso è superiore alla configurazione dell'ambiente, in Azure Time Series Insights si può verificare una latenza o una limitazione.

È possibile evitare la latenza o la limitazione configurando l'ambiente in modo appropriato per la quantità di dati che si vuole analizzare.

È molto probabile riscontrare latenza o limitazione nei casi seguenti:

- Aggiunta di un'origine eventi contenente dati meno recenti che potrebbero superare la velocità in ingresso assegnata, imponendo a Time Series Insights di recuperare.
- Aggiunta di più origini eventi a un ambiente con conseguente picco a causa degli eventi aggiuntivi, che potrebbero superare la capacità dell'ambiente.
- Push di grandi quantità di dati cronologici a un'origine eventi, che potrebbe determinare un ritardo imponendo a Time Series Insights di recuperare.
- Aggiunta di dati di riferimento alla telemetria, che determina un aumento delle dimensioni degli eventi. Dal punto di vista della limitazione, un pacchetto di dati in ingresso di dimensioni pari a 32 KB viene considerato come 32 eventi, ognuno di 1 KB di dimensioni. Le dimensioni massime degli eventi consentite sono 32 KB e i pacchetti di dati di dimensioni superiori a 32 KB vengono troncati.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Scopri di più sul comportamento in ingresso dei dati di Time Series Insights e su come pianificarli.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Monitorare la latenza e la limitazione con avvisi

Gli avvisi consentono di diagnosticare e ridurre i problemi di latenza che si verificano nell'ambiente.

1. Nel portale di Azure selezionare l'ambiente Time Series Insights desiderato. Selezionare **quindi Avvisi**.

   [![Aggiungere un avviso all'ambiente Time Series Insights](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. Selezionare **+ Nuova regola di avviso**. Verrà quindi visualizzato il pannello **Crea regola.** Selezionare **Aggiungi** in **CONDIZIONE**.

   [![Aggiungi riquadro degli avvisi](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. Configurare quindi le condizioni esatte per la logica del segnale.

   [![Configurare la logica dei segnali](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   Da qui, è possibile configurare gli avvisi utilizzando alcune delle seguenti condizioni:

   |Metrica  |Descrizione  |
   |---------|---------|
   |**Ingress Received Bytes** (Byte ricevuti in ingresso)     | Numero di byte non elaborati letti dalle origini eventi. Include in genere il nome e il valore delle proprietà.  |  
   |**Ingress Received Invalid Messages** (Messaggi non validi ricevuti in ingresso)     | Numero dei messaggi non validi letti da tutte le origini eventi di Hub eventi di Azure o Hub IoT di Azure.      |
   |**Ingress Received Messages** (Messaggi ricevuti in ingresso)   | Numero dei messaggi letti da tutte le origini eventi di hub eventi o hub IoT.        |
   |**Ingress Stored Bytes** (Byte archiviati in ingresso)     | Dimensioni totali degli eventi archiviati e disponibili per le query. Le dimensioni sono calcolate solo sul valore delle proprietà.        |
   |**Eventi archiviati in ingressoIngress Stored Events**    |   Numero di eventi bidimensionali archiviati e disponibili per le query.      |
   |**Ingress Received Message Time Lag (Tempo di ritardo messaggi ricevuti in ingresso) **    |  Differenza in secondi tra l'ora in cui il messaggio viene accodato nell'origine eventi e l'ora di elaborazione in ingresso.      |
   |**Ingress Received Message Count Lag (Ritardo numero di messaggi ricevuti in ingresso) **    |  Differenza tra il numero di sequenza dell'ultimo messaggio accodato nella partizione di origine eventi e il numero di sequenza del messaggio elaborato in ingresso.      |

   Selezionare **Fatto**.

1. Dopo aver configurato la logica di segnale desiderata, esaminare visivamente la regola di avviso scelta.

   [![Visualizzazione della latenza e creazione di grafici](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Gestione della limitazione e dell'ingresso

* Se si sta limitando, verrà registrato un valore per il tempo di scadenza dei messaggi in *ingresso* che informa il numero di secondi dopo l'ambiente TIme Series Insights dal tempo effettivo in cui il messaggio raggiunge l'origine dell'evento (escluso il tempo di indicizzazione di appx. 30-60 secondi.  

  Anche per *Ingress Received Message Count Lag* (Differenza numero messaggi ricevuti in ingresso) deve essere disponibile un valore, che consente di determinare di quanti messaggi si è in ritardo.  Il modo più semplice per mettersi in pari consiste nell'aumentare la capacità dell'ambiente fino a dimensioni che consentono di recuperare la differenza.  

  Ad esempio, se l'ambiente S1 sta dimostrando un ritardo di 5.000.000 messaggi, è possibile aumentare le dimensioni dell'ambiente a sei unità per circa un giorno per essere coinvolti.  È possibile aumentare ulteriormente per recuperare più velocemente. Il periodo di recupero è una situazione comune durante il provisioning iniziale di un ambiente, in particolare quando lo si connette a un'origine eventi che già include eventi oppure quando si esegue un caricamento in blocco di molti dati cronologici.

* Un'altra tecnica consiste nell'impostare un avviso **Ingress Stored Events** (Eventi archiviati in ingresso) per un valore maggiore o uguale a una soglia leggermente inferiore alla capacità totale dell'ambiente per un periodo di 2 ore.  Questo avviso consentirà di comprendere se si raggiunge costantemente la capacità e la probabilità di latenza è quindi elevata. 

  Ad esempio, se è stato effettuato il provisioning di tre unità S1, con capacità in ingresso di 2100 eventi al minuto, è possibile impostare un avviso **Ingress Stored Events** (Eventi archiviati in ingresso) per un valore maggiore o uguale a 1900 eventi per 2 ore. Se si supera costantemente questa soglia attivando così l'avviso, è probabile che il provisioning sia insufficiente.  

* Se si sospetta di essere limitati, è possibile confrontare i **messaggi ricevuti** in ingresso con i messaggi in uscita dell'origine evento.  Se l'ingresso nell'hub eventi è superiore a **Ingress Received Messages** (Messaggi ricevuti in ingresso), è probabile che a Time Series Insights venga applicata la limitazione.

## <a name="improving-performance"></a>Miglioramento delle prestazioni

Per ridurre la limitazione o i casi di latenza, la correzione migliore consiste nell'aumentare la capacità dell'ambiente.

È possibile evitare la latenza o la limitazione configurando l'ambiente in modo appropriato per la quantità di dati che si vuole analizzare. Per ulteriori informazioni su come aggiungere capacità all'ambiente, vedere [Ridimensionare l'ambiente](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Diagnosticare e risolvere i problemi nell'ambiente Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).

- Informazioni su [come ridimensionare l'ambiente Time Series Insights.](time-series-insights-how-to-scale-your-environment.md)