---
title: Uso di stringhe nelle query di log di Monitoraggio di Azure | Microsoft Docs
description: Questo articolo fornisce un'esercitazione per l'uso di monitoraggio di Azure Log Analytics nel portale di Azure per eseguire query e analizzare i dati di log in monitoraggio di Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 82ac27e10a74dc99adb7615d604502e696aa9edb
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894316"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Uso di JSON e strutture dei dati nelle query di log di Monitoraggio di Azure

> [!NOTE]
> Prima di completare questa lezione, è necessario completare l'introduzione a [monitoraggio di azure log Analytics](get-started-portal.md) e [iniziare a usare le query di log di monitoraggio di Azure](get-started-queries.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Gli oggetti annidati sono oggetti che contengono altri oggetti in una matrice o una mappa di coppie chiave-valore. Questi oggetti sono rappresentati come stringhe JSON. Questo articolo descrive come usare JSON per recuperare i dati e analizzare gli oggetti annidati.

## <a name="working-with-json-strings"></a>Uso delle stringhe JSON
Usare `extractjson` per accedere a un elemento JSON specifico in un percorso noto. Questa funzione richiede un'espressione di percorso che usa le convenzioni seguenti.

- _$_ per fare riferimento alla cartella radice
- Usare la notazione tra parentesi quadre o con punto per fare riferimento agli indici e agli elementi, come illustrato negli esempi seguenti.


Usare le parentesi quadre per gli indici e i punti per separare gli elementi:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Si ottiene lo stesso risultato usando solo la notazione tra parentesi quadre:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Se c'è un solo elemento, è possibile usare solo la notazione con punto:

```Kusto
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Uso di oggetti

### <a name="parsejson"></a>parsejson
Per accedere a più elementi in una struttura json, è più facile accedervi come oggetto dinamico. Usare `parsejson` per eseguire il cast dei dati di testo in un oggetto dinamico. Una volta eseguita la conversione in un tipo dinamico, è possibile usare funzioni aggiuntive per analizzare i dati.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Usare `arraylength` per contare il numero di elementi in una matrice:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Usare `mvexpand` per suddividere le proprietà di un oggetto in righe separate.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Usare `buildschema` per ottenere lo schema che ammette tutti i valori di un oggetto:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

L'output è uno schema in formato JSON:
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
Questo output descrive i nomi dei campi dell'oggetto e i tipi di dati corrispondenti. 

Gli oggetti annidati possono avere schemi diversi, come nell'esempio seguente:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Schema di compilazione](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere altre lezioni per l'uso di query di log in Monitoraggio di Azure:

- [Operazioni con stringhe](string-operations.md)
- [Operazioni con data e ora](datetime-operations.md)
- [Funzioni di aggregazione](aggregations.md)
- [Aggregazioni avanzate](advanced-aggregations.md)
- [Scrittura di query avanzate](advanced-query-writing.md)
- [Join](joins.md)
- [Grafici](charts.md)