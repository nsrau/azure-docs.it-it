---
title: Eventi Shape - Approfondimenti sulle serie temporali di Azure Documenti Microsoft
description: Informazioni sulle procedure consigliate e su come modellare gli eventi per l'esecuzione di query in Azure Time Insights Preview.Learn about best practices and how to shape events for querying in Azure Time Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 99a2f32c3f76d7fec475c9b299f7208b4db29cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650924"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Definire la struttura degli eventi con Anteprima di Time Series Insights

Questo articolo definisce le procedure consigliate per modellare i payload JSON per l'inserimento in Azure Time Series Insights e per ottimizzare l'efficienza delle query di anteprima.

## <a name="best-practices"></a>Procedure consigliate

È consigliabile considerare attentamente come inviare gli eventi all'ambiente Time Series Insights Preview. 

Le procedure consigliate generali includono:

* Inviare dati tramite rete nel modo più efficiente possibile.
* Archiviare i dati per poterli aggregare in modo più adeguato allo scenario.

Per ottenere prestazioni di query ottimali, attenersi alle regole empiriche seguenti:For the best query performance, adhere to the following rules of thumb:

* Non inviare proprietà non necessarie. Time Series Insights Antedata delle fatture in base all'utilizzo. È consigliabile archiviare ed elaborare solo i dati su cui verrà eseguito una query.
* Usare i campi di istanza per i dati statici. Questa procedura consente di evitare l'invio di dati statici in rete. I campi di istanza, un componente del modello Time Series, funzionano come i dati di riferimento nel servizio Time Series Insights che è generalmente disponibile. Per ulteriori informazioni sui campi di istanza, vedere [Time Series Model](./time-series-insights-update-tsm.md).
* Condividere le proprietà delle dimensioni tra due o più eventi. Questa procedura consente di inviare i dati in rete in modo più efficiente.
* Non usare un annidamento troppo profondo delle matrici. Time Series Insights Preview supporta fino a due livelli di matrici nidificate che contengono oggetti. Anteprima di Time Series Insights rende flat le matrici nei messaggi in più eventi con coppie di valori delle proprietà.
* Se sono disponibili solo alcune misure per tutti gli eventi o per la maggior parte degli eventi, è consigliabile inviare tali misure come proprietà separate nello stesso oggetto. L'invio di essi separatamente riduce il numero di eventi e potrebbe migliorare le prestazioni delle query perché è necessario elaborare un numero inferiore di eventi.

## <a name="column-flattening"></a>Appiattimento delle colonne

Durante l'inserimento, i payload che contengono oggetti nidificati verranno appiattiti in modo che il nome della colonna sia un singolo valore con un delimitatore.

* Ad esempio, il seguente JSON annidato:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   Diventa: `data_flow` quando appiattito.

> [!IMPORTANT]
> * Azure Time Series Insights Preview`_`usa caratteri di sottolineatura ( ) per la delineazione delle colonne.
> * Si noti la differenza rispetto`.`alla disponibilità generale che utilizza invece i periodi ( ).

Scenari più complessi sono illustrati di seguito.

#### <a name="example-1"></a>Esempio 1:

Lo scenario seguente dispone di due (o più) dispositivi che inviano le misure (segnali): *Flow Rate*, *Engine Oil Pressure*, *Temperature*e *Humidity*.

Viene inviato un singolo messaggio dell'hub IoT di Azure in cui l'array esterno contiene una sezione condivisa di valori di dimensione comuni (notare le due voci del dispositivo contenute nel messaggio).

```JSON
[
  {
    "deviceId":"FXXX",
    "timestamp":"2018-01-17T01:17:00Z",
    "series":[
      {
        "Flow Rate ft3/s":1.0172575712203979,
        "Engine Oil Pressure psi ":34.7
      },
      {
        "Flow Rate ft3/s":2.445906400680542,
        "Engine Oil Pressure psi ":49.2
      }
    ]
  },
  {
    "deviceId":"FYYY",
    "timestamp":"2018-01-17T01:18:00Z",
    "series":[
      {
        "Flow Rate ft3/s":0.58015072345733643,
        "Engine Oil Pressure psi ":22.2
      }
    ]
  }
]
```

**Take away:**

* Il JSON di esempio include una matrice esterna che usa i dati [dell'istanza di TimeSeries](./time-series-insights-update-tsm.md#time-series-model-instances) per aumentare l'efficienza del messaggio. Anche se non è probabile che i relativi metadati del dispositivo di istanze della serie temporale cambino, spesso fornisce proprietà utili per l'analisi dei dati.

* Il codice JSON combina due o più messaggi (uno per ogni dispositivo) in un unico payload risparmiando larghezza di banda nel tempo.

* I singoli punti dati della serie per ogni dispositivo vengono combinati in un unico attributo **di serie,** riducendo la necessità di trasmettere continuamente gli aggiornamenti per ogni dispositivo.

> [!TIP]
> Per ridurre il numero di messaggi necessari per inviare dati e rendere più efficiente la telemetria, prendere in considerazione l'invio in batch di valori di dimensione comuni e metadati dell'istanza Time Series in un singolo payload JSON.

#### <a name="time-series-instance"></a>Istanza della serie temporale 

Diamo un'occhiata più da vicino a come usare [l'istanza della serie temporale](./time-series-insights-update-tsm.md#time-series-model-instances) per modellare il tuo JSON in modo più ottimale. 

> [!NOTE]
> Gli [ID Time Series riportati di](./time-series-insights-update-how-to-id.md) seguito sono *deviceIds*.

```JSON
[
  {
    "timeSeriesId":[
      "FXXX"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"REVOLT SIMULATOR",
      "L2":"Battery System"
    }
  },
  {
    "timeSeriesId":[
      "FYYY"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"COMMON SIMULATOR",
      "L2":"Battery System"
    }
  }
]
```

Anteprima di Time Series Insights crea un join di una tabella (dopo la trasformazione in flat) durante la fase di query. La tabella include colonne aggiuntive, ad esempio **Type**.

| deviceId  | Type | L1 | L2 |  timestamp | series_Flow Tariffa ft3/s | series_Engine olio Pressione psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | Battery System | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   Battery System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    Battery System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  La tabella precedente rappresenta la visualizzazione query in [Visualizzazione anteprima serie temporali.](./time-series-insights-update-explorer.md)

**Take away:**

* Nell'esempio precedente, le proprietà statiche vengono archiviate in Time Series Insights Preview per ottimizzare i dati inviati in rete.
* Time Series Insights I dati di anteprima vengono uniti in fase di query tramite l'ID serie temporali definito nell'istanza.
* Vengono utilizzati due strati di nidificazione. Questo numero è il massimo supportato da Time Series Insights Preview. È essenziale evitare matrici annidate profondamente.
* Poiché le misure sono poche, vengono inviate come proprietà separate nello stesso oggetto. Nell'esempio, **series_Flow Tasso psi**, series_Engine Psi Pressione **dell'Olio e**tasso di series_Flow **ft3/s** sono colonne univoche.

>[!IMPORTANT]
> I campi di istanza non vengono archiviati con i dati di telemetria. Vengono archiviati con metadati nel modello Time Series.

#### <a name="example-2"></a>Esempio 2:

Si consideri il codice JSON seguente:Consider the following JSON:

```JSON
{
  "deviceId": "FXXX",
  "timestamp": "2019-01-18T01:17:00Z",
  "data": {
        "flow": 1.0172575712203979,
    },
  "data_flow" : 1.76435072345733643
}
```

Nell'esempio precedente, la `data["flow"]` proprietà appiattita `data_flow` presenterebbe una collisione di denominazione con la proprietà.

In questo caso, il valore della proprietà *più recente* sovrascriverebbe quello precedente. 

> [!TIP]
> Contatta il team di Time Series Insights per ulteriore assistenza!

> [!WARNING] 
> * Nei casi in cui le proprietà duplicate sono presenti nello stesso payload dell'evento (singolare) a causa della conversione o di un altro meccanismo, viene archiviata l'ultima > valore della proprietà, sovrascrivendo i valori precedenti.
> * La serie di eventi combinati non si sovrascriverà l'un l'altro.

## <a name="next-steps"></a>Passaggi successivi

* Per mettere in pratica queste linee guida, leggere [La sintassi della query di Azure Time Series Insights Preview](./time-series-insights-query-data-csharp.md). Ulteriori informazioni sulla sintassi delle query per [l'API REST di Time](https://docs.microsoft.com/rest/api/time-series-insights/preview) Series Insights Preview per l'accesso ai dati.

* Combinare le procedure consigliate JSON con il modello [Di serie temporale](./time-series-insights-update-how-to-tsm.md).
