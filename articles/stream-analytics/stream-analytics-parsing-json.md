---
title: Analisi di dati JSON e Avro in Analisi di flusso di Azure
description: Questo articolo descrive come operare su tipi di dati complessi come matrici e dati in formato JSON e CSV.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: 73905483850a47a9d036bef1b9e1ee60d3484555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484588"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analizzare dati JSON e Avro in Analisi di flusso di Azure

Analisi di flusso di Azure supporta l'elaborazione di eventi in formati di dati CSV, JSON e Avro.Azure Stream Analytics support processing events in CSV, JSON, and Avro data formats. I dati JSON e Avro possono essere strutturati e contenere alcuni tipi complessi, ad esempio oggetti annidati (record) e matrici. 

>[!NOTE]
>I file AVRO creati da Event Hub Capture utilizzano un formato specifico che richiede l'utilizzo della funzionalità *di deserializzatore personalizzato.* Per ulteriori informazioni, vedere [Leggere l'input in qualsiasi formato utilizzando deserializzatori personalizzati .NET.](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples)



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

### <a name="access-nested-fields-in-known-schema"></a>Accedere ai campi nidificati nello schema notoAccess nested fields in known schema
Utilizzare la notazione punto (.) per accedere facilmente ai campi nidificati direttamente dalla query. Ad esempio, questa query seleziona le coordinate Latitudine e Longitudine nella proprietà Location nei dati JSON precedenti. La notazione del punto può essere utilizzata per navigare su più livelli come illustrato di seguito.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

Il risultato è:

|DeviceID|Lat|long|Temperatura|Versione|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Seleziona tutte le proprietà
È possibile selezionare tutte le proprietà di un record annidato con il carattere jolly asterisco (*). Prendere in considerazione gli esempi seguenti:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

Il risultato è:

|DeviceID|Lat|long|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Accedere ai campi nidificati quando il nome della proprietà è una variabileAccess nested fields when property name is a variable

Utilizzare la funzione [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) se il nome della proprietà è una variabile. Ciò consente la creazione di query dinamiche senza nomi di proprietà hardcoded.

Si supponga, ad esempio, che il flusso di dati di esempio debba **essere unito a dati** di riferimento contenenti soglie per ogni sensore del dispositivo. Di seguito è riportato un frammento di tali dati di riferimento.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

L'obiettivo consiste nel unire il set di dati di esempio dall'inizio dell'articolo ai dati di riferimento e nell'output di un evento per ogni misura del sensore al di sopra della soglia. Ciò significa che il nostro singolo evento di cui sopra può generare più eventi di uscita se più sensori sono al di sopra delle rispettive soglie, grazie al join. Per ottenere risultati simili senza un join, vedere la sezione seguente.

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**GetRecordPropertyValue** seleziona la proprietà in *SensorReadings*, il cui nome corrisponde al nome della proprietà proveniente dai dati di riferimento. Quindi viene estratto il valore associato da *SensorReadings.*

Il risultato è:

|DeviceID|SensorName (Nome Sensore)|AlertMessage (Messaggio di avviso)|
|-|-|-|
|12345|Umidità|Avviso : Sensore al di sopra della soglia|

### <a name="convert-record-fields-into-separate-events"></a>Convertire i campi record in eventi separati

Per convertire i campi di record in eventi separati, usare l'operatore [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) insieme alla funzione [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics).

Con i dati di esempio originali, è possibile usare la query seguente per estrarre le proprietà in eventi diversi.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Il risultato è:

|DeviceID|SensorName (Nome Sensore)|AlertMessage (Messaggio di avviso)|
|-|-|-|
|12345|Temperatura|80|
|12345|Umidità|70|
|12345|SensorEPersonalizzato01|5|
|12345|SensorEPersonalizzato02|99|
|12345|SensorMetadata (Metadati del sensore|[object Object]|

Utilizzando [WITH,](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics)è quindi possibile indirizzare tali eventi a destinazioni diverse:

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

### <a name="parse-json-record-in-sql-reference-data"></a>Analizzare il record JSON nei dati di riferimento SQLParse JSON record in SQL reference data
Quando si usa il database SQL di Azure come dati di riferimento nel processo, è possibile avere una colonna con dati in formato JSON. Di seguito è illustrato un esempio.

|DeviceID|Dati|
|-|-|
|12345|"chiave" : "valore1"|
|54321|"chiave" : "valore2"|

È possibile analizzare il record JSON nella colonna *Dati* scrivendo una semplice funzione JavaScript definita dall'utente.

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

È quindi possibile creare un passaggio nella query di Analisi di flusso come illustrato di seguito per accedere ai campi dei record JSON.

 ```SQL
 WITH parseJson as
 (
 SELECT DeviceID, udf.parseJson(sqlRefInput.Data) as metadata,
 FROM sqlRefInput
 )
 
 SELECT metadata.key
 INTO output
 FROM streamInput
 JOIN parseJson 
 ON streamInput.DeviceID = parseJson.DeviceID
```

## <a name="array-data-types"></a>Tipi di dati matrice

I tipi di dati matrice sono una raccolta ordinata di valori. Di seguito sono descritte alcune operazioni tipiche sui valori di matrice. Questi esempi usano le funzioni [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics) e l'operatore [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics).

Ecco un esempio di un singolo evento. Entrambi `CustomSensor03` `SensorMetadata` e sono di tipo **matrice**:

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>Utilizzo di un elemento di matrice specificoWorking with a specific array element

Selezionare un elemento della matrice in corrispondenza dell'indice specificato (selezione del primo elemento della matrice):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

Il risultato è:

|primoelemento|
|-|
|12|

### <a name="select-array-length"></a>Selezionare la lunghezza della serie

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

Il risultato è:

|arrayLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Convertire gli elementi della matrice in eventi separati

Selezionare tutti gli elementi della matrice come singoli eventi. L'operatore [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics), usato insieme alla funzione integrata [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), estrae tutti gli elementi della matrice come singoli eventi:

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

Il risultato è:

|deviceId|Arrayindex|ArrayValue|
|-|-|-|
|12345|0|12|
|12345|1|-5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
Il risultato è:

|deviceId|chiave smKey|smValue (valori di smValue)|
|-|-|-|
|12345|Produttore|ABC|
|12345|Versione|1.2.45|

Se i campi estratti devono essere visualizzati nelle colonne, è possibile eseguire il pivot del set di dati utilizzando la sintassi [WITH](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics) oltre all'operazione [JOIN.](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) Tale join richiederà una condizione limite di tempo che impedisca la duplicazione:That join will require a [time boundary](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) condition that prevents duplication:

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

Il risultato è:

|deviceId|Lat|long|versione/om|smProduttore|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>Vedere anche
[Data Types in Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) (Tipi di dati in Analisi di flusso di Azure)
