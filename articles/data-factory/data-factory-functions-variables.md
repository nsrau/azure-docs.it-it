---
title: Funzioni e variabili di sistema di Data Factory | Microsoft Docs
description: Fornisce un elenco delle funzioni e delle variabili di sistema di Azure Data Factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
services: data-factory
ms.assetid: b6b3c2ae-b0e8-4e28-90d8-daf20421660d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: shlo
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 72a966bdc271f86b9568d3310d2e22d83b447594
ms.contentlocale: it-it
ms.lasthandoff: 06/21/2017

---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory - Funzioni e variabili di sistema
In questo articolo vengono fornite informazioni sulle funzioni e le variabili supportate da Azure Data Factory.

## <a name="data-factory-system-variables"></a>Variabili di sistema di Data Factory
| Nome variabile | Descrizione | Ambito dell'oggetto | Ambito JSON e casi d'uso |
| --- | --- | --- | --- |
| WindowStart |Inizio dell'intervallo di tempo relativo alla finestra di esecuzione dell'attività |attività |<ol><li>Definizione delle query di selezione dei dati. Vedere gli articoli connettore a cui fa riferimento l'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) .</li> |
| WindowEnd |Fine dell'intervallo di tempo relativo alla finestra di esecuzione dell'attività |attività |uguale a WindowStart. |
| SliceStart |Inizio dell'intervallo di tempo relativo alla sezione di dati in fase di produzione |attività<br/>attività |<ol><li>Definizione di nomi di file e percorsi di cartelle dinamici durante l'uso dell'[archivio BLOB di Azure](data-factory-azure-blob-connector.md) e dei [set di dati del file system](data-factory-onprem-file-system-connector.md).</li><li>Definizione delle dipendenze di input con le funzioni della data factory nella raccolta di input dell'attività.</li></ol> |
| SliceEnd |Fine dell'intervallo di tempo relativo alla sezione di dati corrente. |attività<br/>dataset |uguale a SliceStart. |

> [!NOTE]
> Attualmente Data Factory richiede che la pianificazione specificata nell'attività corrisponda esattamente alla pianificazione specificata nella sezione relativa alla disponibilità del set di dati di output. Il mapping delle variabili WindowStart, WindowEnd, SliceStart e SliceEnd viene quindi sempre eseguito allo stesso periodo di tempo e a un'unica sezione di output.
> 

### <a name="example-for-using-a-system-variable"></a>Esempio di uso di una variabile di sistema
Nell'esempio seguente l'anno, il mese, il giorno e l'ora di **SliceStart** vengono estratti in variabili separate usate dalle proprietà **folderPath** e **fileName**.

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Funzioni di Data Factory
È possibile combinare le funzioni della data factory con le variabili di sistema per gli scopi seguenti:

1. Definizione delle query di selezione dei dati (vedere gli articoli connettore a cui fa riferimento l'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) .
   
   La sintassi per chiamare una funzione della data factory è **$$<function>** per le query di selezione dei dati e altre proprietà dell'attività e del set di dati.  
2. Definizione delle dipendenze di input con le funzioni della data factory nella raccolta di input dell'attività.
   
    La sintassi $$ non è necessaria per definire le espressioni delle dipendenze di input.     

Nell'esempio seguente la proprietà **sqlReaderQuery** di un file JSON è assegnata a un valore restituito dalla funzione `Text.Format`. Questo esempio usa anche una variabile di sistema denominata **WindowStart**, che rappresenta l'ora di inizio della finestra di esecuzione dell'attività.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Vedere l'argomento [Stringhe di formato di data e ora personalizzato](https://msdn.microsoft.com/library/8kb3ddd4.aspx) che descrive diverse opzioni di formattazione che è possibile usare, ad esempio: aa e aaaa. 

### <a name="functions"></a>Funzioni
Le tabelle seguenti elencano tutte le funzioni di Data factory di Azure.

| Categoria | Funzione | Parametri | Descrizione |
| --- | --- | --- | --- |
| Time |AddHours(X,Y) |X: DateTime  <br/><br/>Y: int |Aggiunge Y ore all'ora X specificata. <br/><br/>Esempio: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Time |AddMinutes(X,Y) |X: DateTime  <br/><br/>Y: int |Aggiunge Y minuti a X.<br/><br/>Esempio: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Time |StartOfHour(X) |X: DateTime  |Ottiene l'ora di inizio per l'ora rappresentata dal componente ora di X. <br/><br/>Esempio: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Data |AddDays(X,Y) |X: DateTime <br/><br/>Y: int |Aggiunge Y giorni a X. <br/><br/>Esempio: 9/15/2013 12:00:00 PM + 2 giorni = 9/17/2013 12:00:00 PM.<br/><br/>È possibile anche sottrarre giorni specificando Y come un numero negativo.<br/><br/>Esempio: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Data |AddMonths(X,Y) |X: DateTime <br/><br/>Y: int |Aggiunge Y mesi a X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>È possibile anche sottrarre mesi specificando Y come un numero negativo.<br/><br/>Esempio: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Data |AddQuarters(X,Y) |X: DateTime  <br/><br/>Y: int |Aggiunge Y * 3 mesi a X.<br/><br/>Esempio: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Data |AddWeeks(X,Y) |X: DateTime <br/><br/>Y: int |Aggiunge Y * 7 giorni a X.<br/><br/>Esempio: 15/09/2013 12:00:00 + 1 settimana = 22/09/2013 12:00:00<br/><br/>È possibile anche sottrarre settimane specificando Y come un numero negativo.<br/><br/>Esempio: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Data |AddYears(X,Y) |X: DateTime <br/><br/>Y: int |Aggiunge Y anni a X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>È possibile anche sottrarre anni specificando Y come un numero negativo.<br/><br/>Esempio: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Data |Day(X) |X: DateTime  |Ottiene il componente giorno di X.<br/><br/>Esempio: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Data |DayOfWeek(X) |X: DateTime  |Ottiene il giorno del componente settimana di X.<br/><br/>Esempio: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Data |DayOfYear(X) |X: DateTime  |Ottiene il giorno dell'anno rappresentato dal componente anno di X.<br/><br/>Esempi:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Data |DaysInMonth(X) |X: DateTime  |Ottiene i giorni del mese rappresentati dal componente mese del parametro X.<br/><br/>Esempio: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Data |EndOfDay(X) |X: DateTime  |Ottiene la data e ora che rappresenta la fine del giorno (componente giorno) X.<br/><br/>Esempio: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Data |EndOfMonth(X) |X: DateTime  |Ottiene la fine del mese rappresentato dal componente mese del parametro X. <br/><br/>Esempio: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (data e ora che rappresentano la fine del mese di settembre) |
| Data |StartOfDay(X) |X: DateTime  |Ottiene l'inizio del giorno rappresentato dal componente giorno del parametro X.<br/><br/>Esempio: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |From(X) |X: String |Analizza la stringa X fino a una data/ora. |
| DateTime |Ticks(X) |X: DateTime |Ottiene la proprietà dei tick del parametro X. Un tick equivale a 100 nanosecondi. Il valore di questa proprietà rappresenta il numero di tick trascorsi dalla mezzanotte 12:00:00 del 1 gennaio 0001. |
| Text |Format(X) |X: variabile stringa |Formatta il testo (usare la combinazione `\\'` per il carattere di escape `'`).|

> [!IMPORTANT]
> Quando si usa una funzione all'interno di un'altra funzione, non è necessario usare il prefisso **$$** per la funzione interna. Ad esempio: $$Text.Format('PartitionKey eq \\'my_pkey_filter_value\\' and RowKey ge \\'{0: yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). In questo esempio il prefisso **$$** non viene usato per la funzione **Time.AddHours**. 

#### <a name="example"></a>Esempio
Nell'esempio seguente, i parametri di input e output per l'attività Hive vengono determinati usando la funzione `Text.Format` e la variabile di sistema SliceStart. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>Esempio 2

Nell'esempio seguente, il parametro DateTime per l'attività Stored Procedure viene determinato usando la funzione Text. Format e la variabile di SliceStart. 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Esempio 3
Per leggere i dati del giorno precedente anziché del giorno rappresentato da SliceStart, usare la funzione AddDays, come illustrato nell'esempio seguente: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Vedere l'argomento [Stringhe di formato di data e ora personalizzato](https://msdn.microsoft.com/library/8kb3ddd4.aspx) che descrive diverse opzioni di formattazione che è possibile usare, ad esempio: AA e aaaa. 


