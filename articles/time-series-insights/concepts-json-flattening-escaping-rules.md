---
title: Regole di escape e Flat JSON-Azure Time Series Insights | Microsoft Docs
description: Informazioni sulla gestione delle matrici e sull'escape di JSON in Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/04/2020
ms.custom: seodec18
ms.openlocfilehash: 45eeebcc092513a0344acaff52c31c2cebfb377c
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049853"
---
# <a name="ingestion-rules"></a>Regole di inserimento
### <a name="json-flattening-escaping-and-array-handling"></a>Flat JSON, escape e gestione delle matrici

L'ambiente di Azure Time Series Insights creerà in modo dinamico le colonne degli archivi a caldo e a freddo, seguendo un particolare set di convenzioni di denominazione. Quando si inserisce un evento, viene applicato un set di regole al payload JSON e ai nomi di proprietà. Questi includono l'escape di determinati caratteri speciali e l'appiattimento di oggetti JSON annidati. È importante conoscere queste regole per comprendere in che modo la forma di JSON influirà sul modo in cui gli eventi vengono archiviati e sottoposti a query. Per l'elenco completo delle regole, vedere la tabella seguente. Gli esempi A & B illustrano inoltre come sia possibile eseguire in batch più serie temporali in una matrice in modo efficiente.

> [!IMPORTANT]
>
> * Esaminare le regole riportate di seguito prima di selezionare una proprietà relativa all' [ID della serie temporale](time-series-insights-update-how-to-id.md) e/o il [timestamp](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)delle origini eventi. Se l'ID o il timestamp di servizi Terminal si trova all'interno di un oggetto annidato o contiene uno o più dei caratteri speciali seguenti, è importante assicurarsi che il nome di proprietà fornito corrisponda al nome della colonna *dopo* l'applicazione delle regole di inserimento. Vedere l'esempio [B](concepts-json-flattening-escaping-rules.md#example-b) riportato di seguito.

| Regola | JSON di esempio |Nome colonna nello spazio di archiviazione |
|---|---|---|
| Il tipo di dati TSI viene aggiunto alla fine del nome della colonna come "_ \<dataType\> " | ```"type": "Accumulated Heat"``` | type_string |
| La [proprietà timestamp](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) dell'origine evento verrà salvata in TSI come "timestamp" nell'archivio e il valore archiviato in UTC. È possibile personalizzare la proprietà timestamp delle origini eventi in modo che soddisfi le esigenze della soluzione, ma il nome della colonna nell'archiviazione a caldo e a freddo è "timestamp". Altre proprietà JSON DateTime che non sono il timestamp dell'origine eventi verranno salvate con "_datetime" nel nome della colonna, come indicato nella regola precedente.  | ```"ts": "2020-03-19 14:40:38.318"``` | timestamp |
| Nomi delle proprietà JSON che includono i caratteri speciali. [\ è sono preceduti da un carattere di escape con [' and ']  |  ```"id.wasp": "6A3090FD337DE6B"``` | [' ID. Wasp '] _string |
| In [' and '] è presente un ulteriore escape di virgolette singole e barre rovesciate. Una virgoletta singola verrà scritta come \' e una barra rovesciata verrà scritta come\\\ | ```"Foo's Law Value": "17.139999389648"``` | [' Foo \' s Law value '] _double |
| Gli oggetti JSON annidati sono bidimensionali con un punto come separatore. È supportato l'annidamento di un massimo di 10 livelli. |  ```"series": {"value" : 316 }``` | serie. value_long |
| Le matrici di tipi primitivi vengono archiviate come tipo dinamico |  ```"values": [154, 149, 147]``` | values_dynamic |
| Le matrici contenenti oggetti hanno due comportamenti a seconda del contenuto dell'oggetto: se gli ID o le proprietà timestamp sono inclusi negli oggetti in una matrice, la matrice verrà annullata in modo tale che il payload JSON iniziale produca più eventi. In questo modo è possibile raggruppare più eventi in una struttura JSON. Tutte le proprietà di primo livello che sono peer della matrice verranno salvate con ogni oggetto non registrato. Se gli ID e il timestamp di Servizi terminal *non* si trovano all'interno della matrice, verranno salvati interi come tipo dinamico. | Vedere gli esempi [A](concepts-json-flattening-escaping-rules.md#example-a), [B](concepts-json-flattening-escaping-rules.md#example-b) e [C](concepts-json-flattening-escaping-rules.md#example-c) sotto
| Le matrici contenenti elementi misti non sono bidimensionali. |  ```"values": ["foo", {"bar" : 149}, 147]``` | values_dynamic |
| 512 caratteri è il limite del nome della proprietà JSON. Se il nome supera i 512 caratteri, viene troncato a 512 e viene aggiunto ' _<' codice hash ' >'. Si **noti** che questo vale anche per i nomi di proprietà concatenati dall'oggetto bidimensionale, che denota un percorso dell'oggetto annidato. |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` | Data. Items. DataPoints. Values. telemetria<... continuando a 512 caratteri>_912ec803b2ce49e4a541068d495ab570_double |

## <a name="understanding-the-dual-behavior-for-arrays"></a>Informazioni sul doppio comportamento per le matrici

Le matrici di oggetti verranno archiviate intere o suddivise in più eventi a seconda di come sono stati modellati i dati. In questo modo è possibile usare una matrice per eseguire il batch degli eventi ed evitare di ripetere le proprietà di telemetria definite a livello di oggetto radice. L'invio in batch può risultare vantaggioso perché comporta un minor numero di hub eventi o messaggi dell'hub. 

Tuttavia, in alcuni casi, le matrici che contengono oggetti sono significative solo nel contesto di altri valori. La creazione di più eventi renderebbe insignificanti i dati. Per assicurarsi che una matrice di oggetti venga archiviata come un tipo dinamico, seguire le indicazioni sulla modellazione dei dati di seguito e osservare l' [esempio C](concepts-json-flattening-escaping-rules.md#example-c)

### <a name="how-do-i-know-if-my-array-of-objects-will-produce-multiple-events"></a>Ricerca per categorie verificare se la matrice di oggetti produrrà più eventi?

Se uno o più degli ID di serie temporali sono annidati all'interno di oggetti in una matrice *o* se la proprietà timestamp dell'origine evento è annidata, il motore di inserimento lo suddividerà per creare più eventi. I nomi di proprietà specificati per gli ID e/o il timestamp di Servizi terminal devono rispettare le regole di Flat sopra indicate e indicheranno quindi la forma del file JSON. Vedere gli esempi riportati di seguito e consultare la guida per la [selezione di una proprietà ID Time Series.](time-series-insights-update-how-to-id.md)

### <a name="example-a"></a>Esempio A:
ID della serie temporale alla radice dell'oggetto e al timestamp annidato<br/>
**ID della serie temporale dell'ambiente:**`"id"`<br/>
**Timestamp origine evento:**`"values.time"`<br/>
**Payload JSON:**

```JSON
[
    {
        "id": "caaae533-1d6c-4f58-9b75-da102bcc2c8c",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 25.6073
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 43.9077
            }
        ]
    },
    {
        "id": "1ac87b74-0865-4a07-b512-56602a3a576f",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 0.337288
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 4.76562
            }
        ]
    }
]
```

**Risultato in un file parquet:**
<br/>
La configurazione e il payload precedenti produrranno tre colonne e quattro eventi

| timestamp  | id_string | valori. value_double 
| ---- | ---- | ---- | 
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` | 
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` | 
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` | 
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` | 

### <a name="example-b"></a>Esempio B:
ID della serie temporale composita con una proprietà nidificata<br/> 
**ID della serie temporale dell'ambiente:** `"plantId"` e`"telemetry.tagId"`<br/>
**Timestamp origine evento:**`"timestamp"`<br/>
**Payload JSON:**

```JSON
[
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:38:09Z",
        "telemetry": [
            {
                "tagId": "100231-A-A6",
                "tagValue": -31.149018
            },
            {
                "tagId": "100231-A-A1",
                "tagValue": 20.560796
            },
            {
                "tagId": "100231-A-A9",
                "tagValue": 177
            },
            {
                "tagId": "100231-A-A8",
                "tagValue": 420
            },
        ]
    },
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:42:14Z",
        "telemetry": [
            {
                "tagId": "103585-A-A7",
                "value": -30.9918
            },
            {
                "tagId": "103585-A-A4",
                "value": 19.960796
            }
        ]
    }
]
```

**Risultato in un file parquet:**
<br/>
La configurazione e il payload precedenti produrranno quattro colonne e sei eventi

| timestamp  | plantId_string | telemetria. tagId_string | telemetria. value_double 
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  -31,149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20,560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336972` | ``100231-A-A7`` | -30,9918 |  
| `2020-01-22T16:42:14Z` | `9336972` | ``100231-A-A4`` | 19,960796 | 

### <a name="example-c"></a>Esempio C:
L'ID e il timestamp della serie temporale si trovano nella radice dell'oggetto<br/> 
**ID della serie temporale dell'ambiente:**`"id"`<br/>
**Timestamp origine evento:**`"timestamp"`<br/>
**Payload JSON:**

```JSON
{
    "id": "800500054755",
    "timestamp": "2020-11-01T10:00:00.000Z",
    "datapoints": [{
            "value": 120
        },
        {
            "value": 124
        }
    ]
}
```

**Risultato in un file parquet:**
<br/>
La configurazione e il payload precedenti produrranno tre colonne e un evento

| timestamp  | id_string | datapoints_dynamic  
| ---- | ---- | ---- | 
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle limitazioni della [velocità effettiva](concepts-streaming-throughput-limitations.md) dell'ambiente
