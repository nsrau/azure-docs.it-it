---
title: Analisi di dati JSON e Avro in Analisi di flusso di Azure
description: Questo articolo descrive come operare su tipi di dati complessi come matrici e dati in formato JSON e CSV.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: ac06521df38bdc91ca717d888c73cd541576014d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905460"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analizzare dati JSON e Avro in Analisi di flusso di Azure

Analisi di flusso di Azure supporta l'elaborazione di eventi nei formati di dati CSV, JSON e avro. Sia i dati JSON che i dati avro possono essere strutturati e contenere alcuni tipi complessi, ad esempio gli oggetti annidati (record) e le matrici. 

>[!NOTE]
>I file AVRO creati dall'acquisizione di hub eventi usano un formato specifico che richiede l'uso della funzionalità di *deserializzazione personalizzata* . Per altre informazioni, vedere [leggere input in qualsiasi formato usando i deserializzatori personalizzati di .NET](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples).



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

### <a name="access-nested-fields-in-known-schema"></a>Accedere ai campi annidati nello schema noto
Usare la notazione del punto (.) per accedere facilmente ai campi nidificati direttamente dalla query. Questa query, ad esempio, consente di selezionare le coordinate di latitudine e longitudine nella proprietà location nei dati JSON precedenti. La notazione del punto può essere usata per spostarsi a più livelli, come illustrato di seguito.

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

|DeviceID|Lat|Lungo|Temperatura|Versione|
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

|DeviceID|Lat|Lungo|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Accedere ai campi nidificati quando il nome della proprietà è una variabile

Utilizzare la funzione [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) se il nome della proprietà è una variabile. Ciò consente di creare query dinamiche senza nomi di proprietà hardcoded.

Si supponga, ad esempio, che il flusso **di dati di esempio debba essere unito con i dati di riferimento contenenti le** soglie per ogni sensore di dispositivo. Di seguito è riportato un frammento di tali dati di riferimento.

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

L'obiettivo è unire il set di dati di esempio dall'inizio dell'articolo ai dati di riferimento e restituire un evento per ogni misura del sensore al di sopra della soglia. Questo significa che il singolo evento precedente può generare più eventi di output se più sensori superano le rispettive soglie, grazie al join. Per ottenere risultati simili senza un join, vedere la sezione seguente.

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

**GetRecordPropertyValue** seleziona la proprietà in *SensorReadings*, il cui nome corrisponde al nome della proprietà proveniente dai dati di riferimento. Viene quindi estratto il valore associato da *SensorReadings* .

Il risultato è:

|DeviceID|Sensorename|AlertMessage|
|-|-|-|
|12345|Umidità|Avviso: sensore sopra la soglia|

### <a name="convert-record-fields-into-separate-events"></a>Converti i campi dei record in eventi distinti

Per convertire i campi di record in eventi separati, usare l'operatore [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) insieme alla funzione [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics).

Con i dati di esempio originali, è possibile utilizzare la query seguente per estrarre le proprietà in eventi diversi.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Il risultato è:

|DeviceID|Sensorename|AlertMessage|
|-|-|-|
|12345|Temperatura|80|
|12345|Umidità|70|
|12345|CustomSensor01|5|
|12345|CustomSensor02|99|
|12345|SensorMetadata|[oggetto oggetto]|

Utilizzando [con](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics), è possibile indirizzare tali eventi a destinazioni diverse:

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

## <a name="array-data-types"></a>Tipi di dati matrice

I tipi di dati matrice sono una raccolta ordinata di valori. Di seguito sono descritte alcune operazioni tipiche sui valori di matrice. Questi esempi usano le funzioni [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics) e l'operatore [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics).

Di seguito è riportato un esempio di un singolo evento. Sia `CustomSensor03` che `SensorMetadata` sono di tipo **matrice**:

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

### <a name="working-with-a-specific-array-element"></a>Utilizzo di un elemento di matrice specifico

Selezionare un elemento della matrice in corrispondenza dell'indice specificato (selezione del primo elemento della matrice):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

Il risultato è:

|FirstElement|
|-|
|12|

### <a name="select-array-length"></a>Selezionare la lunghezza della matrice

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

Il risultato è:

|arrayLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Converte gli elementi di matrice in eventi distinti

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

|deviceId|ArrayIndex|ArrayValue|
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

|deviceId|smKey|smValue|
|-|-|-|
|12345|Produttore|ABC|
|12345|Versione|1.2.45|

Se i campi estratti devono essere visualizzati in colonne, è possibile eseguire il pivot del set di dati usando la sintassi [with](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics) oltre all'operazione di [join](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) . Il join richiede una condizione di [limite temporale](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) che impedisce la duplicazione:

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

|deviceId|Lat|Lungo|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>Vedere anche
[Data Types in Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) (Tipi di dati in Analisi di flusso di Azure)
