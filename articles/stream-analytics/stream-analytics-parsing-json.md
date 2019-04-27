---
title: Analisi di dati JSON e Avro in Analisi di flusso di Azure
description: Questo articolo descrive come operare su tipi di dati complessi come matrici e dati in formato JSON e CSV.
services: stream-analytics
ms.service: stream-analytics
author: lingliw
ms.author: v-lingwu
manager: digimobile
ms.topic: conceptual
origin.date: 08/03/2018
ms.date: 11/26/2018
ms.openlocfilehash: 3d4c1bfa8b376ec50efc7b3cddc8d22a40e70892
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478862"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analizzare dati JSON e Avro in Analisi di flusso di Azure

Analisi di flusso di Azure supporta l'elaborazione di eventi nei formati di dati CSV, JSON e Avro. Entrambi i dati JSON e Avro possono includere tipi complessi come le matrici e gli oggetti annidati (record). 

## <a name="array-data-types"></a>Tipi di dati matrice  
I tipi di dati matrice sono una raccolta ordinata di valori. Di seguito sono descritte alcune operazioni tipiche sui valori di matrice. Questi esempi presuppongono che gli eventi di input abbiano una proprietà denominata "arrayField" che rappresenta un tipo di dati matrice.

Questi esempi usano le funzioni [GetArrayElement](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelement-azure-stream-analytics), [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics), [GetArrayLength](https://msdn.microsoft.com/azure/stream-analytics/reference/getarraylength-azure-stream-analytics) e l'operatore [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics).

## <a name="examples"></a>Esempi  
 Selezionare un elemento della matrice in corrispondenza dell'indice specificato (selezione del primo elemento della matrice):  

```SQL 
SELECT   
    GetArrayElement(arrayField, 0) AS firstElement  
FROM input  
```  

 Selezionare la lunghezza della matrice:  

```SQL  
SELECT   
    GetArrayLength(arrayField) AS arrayLength  
FROM input  
```  

Selezionare tutti gli elementi della matrice come singoli eventi. L'operatore [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics), usato insieme alla funzione integrata [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics), estrae tutti gli elementi della matrice come singoli eventi:  

```SQL  
SELECT   
    arrayElement.ArrayIndex,  
    arrayElement.ArrayValue  
FROM input as event  
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement  
```  

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
        "CustomSensor02" : 99  
    }  
}  
```  

## <a name="examples"></a>Esempi  
Usare la notazione con il punto (.) per accedere ai campi annidati. Ad esempio, questa query seleziona le coordinate Lat e Long nella proprietà Location nei dati JSON precedenti: 

```SQL  
SELECT  
    DeviceID,  
    Location.Lat,  
    Location.Long  
FROM input  
```  

Usare la funzione [GetRecordPropertyValue](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordpropertyvalue-azure-stream-analytics) se il nome della proprietà è sconosciuto. Si supponga, ad esempio, che un flusso di dati di esempio debba essere unito a dati di riferimento contenenti soglie per ogni sensore di dispositivo:  

```json  
{  
    "DeviceId" : "12345",  
    "SensorName" :  "Temperature",
    "Value" : 75
}  
```  

```SQL  
SELECT  
    input.DeviceID,  
    thresholds.SensorName  
FROM input  
JOIN thresholds  
ON  
    input.DeviceId = thresholds.DeviceId  
WHERE  
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value  
```  

Per convertire i campi di record in eventi separati, usare l'operatore [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) insieme alla funzione [GetRecordProperties](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordproperties-azure-stream-analytics). Ad esempio, per convertire un flusso di esempio in un flusso di eventi con letture di singoli sensori, è possibile usare questa query:  

```SQL  
SELECT   
    event.DeviceID,  
    sensorReading.PropertyName,  
    sensorReading.PropertyValue  
FROM input as event  
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading  
```  

È possibile selezionare tutte le proprietà di un record annidato con il carattere jolly asterisco (*). Si consideri l'esempio seguente:  

```SQL  
SELECT input.SensorReadings.*  
FROM input  
```  

Il risultato è:  

```json  
{  
    "Temperature" : 80,  
    "Humidity" : 70,  
    "CustomSensor01" : 5,  
    "CustomSensor022" : 99  
}  
```  

## <a name="see-also"></a>Vedere anche  
 [Data Types in Azure Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/data-types-azure-stream-analytics) (Tipi di dati in Analisi di flusso di Azure)
 
<!-- Update_Description: new articles on stream analytics parsing json -->
<!--ms.date: 09/17/2018-->