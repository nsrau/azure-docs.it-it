---
title: Eventi forma-Azure Time Series Insights | Microsoft Docs
description: Informazioni sulle procedure consigliate e su come eseguire il data shaping degli eventi per l'esecuzione di query in Azure Time Insights Preview.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77650924"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Definire la struttura degli eventi con Anteprima di Time Series Insights

Questo articolo descrive le procedure consigliate per modellare i payload JSON per l'inserimento in Azure Time Series Insights e per ottimizzare l'efficienza delle query in anteprima.

## <a name="best-practices"></a>Procedure consigliate

È consigliabile valutare attentamente il modo in cui si inviano gli eventi all'ambiente di Time Series Insights anteprima. 

Le procedure consigliate generali includono:

* Inviare dati tramite rete nel modo più efficiente possibile.
* Archiviare i dati per poterli aggregare in modo più adeguato allo scenario.

Per ottenere prestazioni ottimali per le query, attenersi alle regole seguenti:

* Non inviare proprietà non necessarie. Time Series Insights le fatture di anteprima per utilizzo. È consigliabile archiviare ed elaborare solo i dati di cui si esegue la query.
* Usare i campi di istanza per i dati statici. Questa procedura consente di evitare l'invio di dati statici in rete. I campi di istanza, un componente del modello Time Series, funzionano come i dati di riferimento nel servizio Time Series Insights disponibile a livello generale. Per ulteriori informazioni sui campi di istanza, vedere [modello Time Series](./time-series-insights-update-tsm.md).
* Condividere le proprietà delle dimensioni tra due o più eventi. Questa procedura consente di inviare i dati in rete in modo più efficiente.
* Non usare un annidamento troppo profondo delle matrici. Time Series Insights anteprima supporta fino a due livelli di matrici annidate contenenti oggetti. Anteprima di Time Series Insights rende flat le matrici nei messaggi in più eventi con coppie di valori delle proprietà.
* Se sono disponibili solo alcune misure per tutti gli eventi o per la maggior parte degli eventi, è consigliabile inviare tali misure come proprietà separate nello stesso oggetto. Inviarli separatamente riduce il numero di eventi e potrebbe migliorare le prestazioni delle query perché è necessario elaborare un numero inferiore di eventi.

## <a name="column-flattening"></a>Flating delle colonne

Durante l'inserimento, i payload che contengono oggetti annidati verranno resi bidimensionali, in modo che il nome della colonna sia un singolo valore con un delimitatore.

* Ad esempio, il seguente JSON annidato:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   Diventa: `data_flow` se bidimensionale.

> [!IMPORTANT]
> * Azure Time Series Insights anteprima usa caratteri di sottolineatura (`_`) per la delineatura della colonna.
> * Si noti la differenza rispetto alla disponibilità generale che usa`.`invece i punti ().

Di seguito sono illustrati gli scenari più complessi.

#### <a name="example-1"></a>Esempio 1:

Nello scenario seguente sono presenti due o più dispositivi che inviano le misurazioni (segnali) *: frequenza del flusso*, *pressione dell'olio del motore*, *temperatura*e *umidità*.

Viene inviato un singolo messaggio dell'hub di Azure, in cui l'array esterno contiene una sezione condivisa dei valori delle dimensioni comuni (si notino le due voci del dispositivo contenute nel messaggio).

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

**Takeaway**

* Il codice JSON di esempio include una matrice esterna che usa i dati dell' [istanza della serie temporale](./time-series-insights-update-tsm.md#time-series-model-instances) per aumentare l'efficienza del messaggio. Anche se le istanze della serie temporale non cambiano probabilmente i metadati del dispositivo, fornisce spesso proprietà utili per l'analisi dei dati.

* Il codice JSON combina due o più messaggi (uno da ogni dispositivo) in un singolo payload che salva la larghezza di banda nel tempo.

* I singoli punti dati della serie per ogni dispositivo vengono combinati in un singolo attributo della **serie** riducendo la necessità di trasmettere continuamente gli aggiornamenti per ogni dispositivo.

> [!TIP]
> Per ridurre il numero di messaggi necessari per inviare i dati e rendere più efficiente la telemetria, è consigliabile suddividere in batch i valori delle dimensioni comuni e i metadati delle istanze della serie temporale in un singolo payload JSON.

#### <a name="time-series-instance"></a>Istanza della serie temporale 

Si esaminerà ora come usare l'istanza della [serie temporale](./time-series-insights-update-tsm.md#time-series-model-instances) per modellare il JSON in modo ottimale. 

> [!NOTE]
> Gli [ID di serie temporali](./time-series-insights-update-how-to-id.md) seguenti sono *deviceIds*.

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

| deviceId  | Tipo | L1 | L2 | timestamp | Frequenza series_Flow ft3/s | Pressione del petrolio series_Engine |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | Battery System | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   Battery System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    Battery System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  La tabella precedente rappresenta la visualizzazione query in [Esplora anteprime della serie temporale](./time-series-insights-update-explorer.md).

**Takeaway**

* Nell'esempio precedente, le proprietà statiche vengono archiviate in Time Series Insights anteprima per ottimizzare i dati inviati in rete.
* Time Series Insights i dati di anteprima vengono uniti in fase di query tramite l'ID della serie temporale definito nell'istanza.
* Vengono utilizzati due livelli di annidamento. Questo numero è il maggior numero di Time Series Insights supporta l'anteprima. È essenziale evitare matrici annidate profondamente.
* Poiché le misure sono poche, vengono inviate come proprietà separate nello stesso oggetto. Nell'esempio **Series_Flow rate PSI**, **series_Engine Pressure Oil**e **series_Flow rate ft3/s** sono colonne univoche.

>[!IMPORTANT]
> I campi di istanza non vengono archiviati con dati di telemetria. Sono archiviati con metadati nel modello Time Series.

#### <a name="example-2"></a>Esempio 2:

Si consideri il codice JSON seguente:

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

Nell'esempio precedente, la `data["flow"]` proprietà bidimensionale presenta un conflitto di denominazione con la `data_flow` proprietà.

In questo caso, il valore della proprietà *più recente* sovrascrive quello precedente. 

> [!TIP]
> Per ulteriore assistenza, contattare il team di Time Series Insights.

> [!WARNING] 
> * Nei casi in cui le proprietà duplicate sono presenti nello stesso payload dell'evento (singolare) a causa dell'appiattimento o di un altro meccanismo, viene archiviato il valore più recente della proprietà >, sovrascrivendo i valori precedenti.
> * Non è possibile eseguire l'override di una serie di eventi combinati.

## <a name="next-steps"></a>Passaggi successivi

* Per applicare queste linee guida, leggere [Azure Time Series Insights sintassi di query di anteprima](./time-series-insights-query-data-csharp.md). Verranno fornite altre informazioni sulla sintassi di query per l' [API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview) di Time Series Insights Preview per l'accesso ai dati.

* Combinare le procedure consigliate JSON con [il modello Time Series](./time-series-insights-update-how-to-tsm.md).
