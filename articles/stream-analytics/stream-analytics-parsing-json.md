---
title: Analisi di dati JSON e Avro in Analisi di flusso di Azure
description: Questo articolo descrive come operare su tipi di dati complessi come matrici e dati in formato JSON e CSV.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: daf5b97e4ac586f89e5964ee16ee73c86f59b01d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329349"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analizzare dati JSON e Avro in Analisi di flusso di Azure

Azure Stream Analitica supportano l'elaborazione eventi nei formati di dati CSV, JSON e Avro. I dati JSON e Avro possono essere strutturati e contengono alcuni tipi complessi come matrici e oggetti annidati (record). 




## <a name="record-data-types"></a>Tipi di dati record
I tipi di dati record vengono usati per rappresentare le matrici JSON e Avro quando vengono usati formati corrispondenti nei flussi di dati di input. Questi esempi illustrano un sensore di esempio che legge gli eventi di input in formato JSON. Di seguito è riportato l'esempio di un singolo evento:

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```


### <a name="access-nested-fields-in-known-schema"></a>Campi di accesso annidato in uno schema noto
Usare la notazione del punto (.) per accedere facilmente ai campi annidati direttamente dalla query. Ad esempio, questa query Seleziona le coordinate di latitudine e longitudine nella proprietà relativa alla posizione nei dati JSON precedenti. La notazione del punto può essere utilizzata per spostarsi più livelli come illustrato di seguito.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>Seleziona tutte le proprietà
È possibile selezionare tutte le proprietà di un record annidato con il carattere jolly asterisco (*). Si consideri l'esempio seguente:

```SQL
SELECT input.Location.*
FROM input
```

Il risultato è:

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Campi di accesso annidato quando il nome della proprietà è una variabile
Usare la [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) funzionare se il nome della proprietà è una variabile. 

Si supponga, ad esempio, che un flusso di dati di esempio deve essere unito in join con soglie che lo contiene dei dati di riferimento per ogni sensore del dispositivo. Di seguito è riportato un frammento di questo tipo di dati di riferimento.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 75
}
```

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
    -- the where statement selects the property value coming from the reference data
```

### <a name="convert-record-fields-into-separate-events"></a>Convertire i campi del record in due eventi distinti
Per convertire i campi di record in eventi separati, usare l'operatore [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) insieme alla funzione [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics). Ad esempio, se l'esempio precedente ha diversi record per SensorReading, la query seguente è stato possibile utilizzabile per li estrae in diversi eventi:

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>Tipi di dati matrice

I tipi di dati matrice sono una raccolta ordinata di valori. Di seguito sono descritte alcune operazioni tipiche sui valori di matrice. Questi esempi presuppongono che gli eventi di input abbiano una proprietà denominata "arrayField" che rappresenta un tipo di dati matrice.

Questi esempi usano le funzioni [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics) e l'operatore [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics).

### <a name="working-with-a-specific-array-element"></a>Utilizzo di un elemento specifico della matrice
Selezionare un elemento della matrice in corrispondenza dell'indice specificato (selezione del primo elemento della matrice):

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>Selezionare la lunghezza della matrice

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>Convertire gli elementi della matrice in eventi separati
Selezionare tutti gli elementi della matrice come singoli eventi. L'operatore [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics), usato insieme alla funzione integrata [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), estrae tutti gli elementi della matrice come singoli eventi:

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>Vedere anche
[Data Types in Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) (Tipi di dati in Analisi di flusso di Azure)
