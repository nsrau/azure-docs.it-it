---
title: Query sul routing dei messaggi di hub IoT di Azure | Microsoft Docs
description: Guida per sviluppatori - Sintassi di query per il routing dei messaggi di hub IoT di Azure.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 94d3599fe919cf648be7115be68002d2aa458ee3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60400644"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Sintassi query per il routing dei messaggi di hub IoT

Il routing dei messaggi consente agli utenti di instradare diversi tipi di dati, ovvero i messaggi di telemetria da dispositivo, gli eventi del ciclo di vita del dispositivo ed eventi di modifica del dispositivo gemello su diversi endpoint. È inoltre possibile applicare query complesse per ai dati prima del routing in modo da ricevere i dati rilevanti. Questo articolo descrive il linguaggio di query per il routing dei messaggi dell'hub IoT e indica alcuni modelli di query comuni.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Il routing dei messaggi consente di eseguire query sulle proprietà dei messaggi e sul corpo del messaggio, nonché sui tag e sulle proprietà del dispositivo gemello. Se il corpo del messaggio non è JSON, il routing dei messaggi può comunque instradare il messaggio, ma le query non possono essere applicate al corpo del messaggio.  Le query vengono definite come espressioni booleane in cui un valore booleano true rende la query riuscita e che instrada tutti i dati in ingresso, mentre il valore booleano false rende una query errata e nessun dato viene instradato. Se l'espressione dà come valore null o non definito, viene considerata come false e verrà generato un errore nel log di diagnostica in caso di esito negativo. La sintassi di query deve essere corretta per la route per essere salvata e valutata.  

## <a name="message-routing-query-based-on-message-properties"></a>Routing messaggi di query basato sulle proprietà del messaggio 

L'hub IoT definisce un [formato comune](iot-hub-devguide-messages-construct.md) per tutta la messaggistica da dispositivo a cloud per l'interoperabilità tra i protocolli. Il messaggio hub IoT presuppone la seguente rappresentazione JSON del messaggio. Le proprietà di sistema vengono aggiunte per tutti gli utenti e identificano il contenuto del messaggio. Gli utenti possono aggiungere al messaggio le proprietà dell'applicazione in modo selettivo. È consigliabile usare nomi delle proprietà univoci, in quanto l'hub IoT di messaggistica da dispositivo a cloud non fa distinzione tra maiuscole e minuscole. Ad esempio, se si hanno più proprietà con lo stesso nome, l'hub IoT invierà solo una delle proprietà.  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```

### <a name="system-properties"></a>Proprietà di sistema

Le proprietà di sistema identificano contenuto e origine dei messaggi. 

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| contentType | string | L'utente specifica il tipo di contenuto del messaggio. Per consentire query sul corpo del messaggio, questo valore deve essere impostato su application/JSON. |
| contentEncoding | string | L'utente specifica il tipo di codifica del messaggio. Valori consentiti sono UTF-8, UTF-16, UTF-32 Se il contentType è impostato su application/JSON. |
| iothub-connection-device-id | string | Questo valore viene impostato dall'hub IoT e identifica l'ID del dispositivo. Per la query, usare `$connectionDeviceId`. |
| iothub-enqueuedtime | string | Questo valore viene impostato dall'hub IoT e rappresenta l'ora effettiva di inserimento in coda del messaggio in UTC. Per la query, usare `enqueuedTime`. |

Come descritto in [messaggi dell'hub IoT](iot-hub-devguide-messages-construct.md), sono disponibili le proprietà di sistema aggiuntive in un messaggio. Oltre a **contentType**, **contentEncoding** e **enqueuedTime**, è possibile sottoporre a query anche **connectionDeviceId** e **connectionModuleId**.

### <a name="application-properties"></a>Proprietà dell'applicazione

Le proprietà dell'applicazione sono stringhe definite dall'utente che è possibile aggiungere al messaggio. Questi campi sono facoltativi.  

### <a name="query-expressions"></a>Espressioni di query

Una query sulle proprietà di sistema del messaggio richiede come prefisso il simbolo `$`. Viene eseguito l'accesso alle query sulle proprietà dell'applicazione con il relativo nome e non devono essere precedute da `$`simbolo. Se un nome di proprietà dell'applicazione inizia con `$`, allora l'hub IoT lo cercherà tra le proprietà del sistema e se non viene trovato, lo cercherà nelle proprietà dell'applicazione. Ad esempio: 

Per eseguire una query su una proprietà di sistema contentEncoding 

```sql
$contentEncoding = 'UTF-8'
```

Per eseguire una query una proprietà di applicazione processingPath:

```sql
processingPath = 'hot'
```

Per combinare queste query, è possibile usare funzioni ed espressioni booleane:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Fare riferimento alla sezione [Espressioni e condizioni](iot-hub-devguide-query-language.md#expressions-and-conditions) per l'elenco completo delle funzioni e degli operatori supportati

## <a name="message-routing-query-based-on-message-body"></a>Query di routing dei messaggi in base al corpo del messaggio 

Per abilitare l'esecuzione di query nel corpo del messaggio, il messaggio deve essere in formato JSON codificato in UTF-8, UTF-16 o UTF-32. Il `contentType` deve essere impostato su `application/JSON` e `contentEncoding` su una delle codifiche UTF supportate nelle proprietà di sistema. Se queste proprietà non vengono specificate, l'hub IoT non valuterà l'espressione di query nel corpo del messaggio. 

L'esempio seguente mostra come creare un messaggio con un corpo JSON correttamente formattato e codificato: 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

### <a name="query-expressions"></a>Espressioni di query

Una query nel corpo del messaggio deve essere preceduta da `$body`. Nell'espressione di query è possibile usare un riferimento al corpo, un riferimento a una matrice del corpo o riferimenti multipli al corpo. L'espressione di query può anche combinare un riferimento al corpo con le proprietà di sistema del messaggio e con il riferimento alle proprietà di applicazione del messaggio. Ad esempio, di seguito sono riportate tutte le espressioni di query valide: 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```

```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```

```sql
length($body.Weather.Location.State) = 2 
```

```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

## <a name="message-routing-query-based-on-device-twin"></a>Query di routing dei messaggi basata sul dispositivo gemello 

Il routing dei messaggi consente di eseguire query su tag e proprietà del [Dispositivo gemello](iot-hub-devguide-device-twins.md), che sono oggetti JSON. Si noti che l'esecuzione di query nel modulo gemello non è supportata. Di seguito è riportato un esempio di tag e proprietà del Dispositivo gemello.

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>Espressioni di query

Una query nel corpo del messaggio deve essere preceduta da `$twin`. L'espressione di query può anche combinare un tag gemello o un riferimento alla proprietà con un riferimento al corpo o un riferimento alle proprietà di sistema e applicazione del messaggio. È consigliabile usare nomi univoci per tag e proprietà, poiché la query non fa distinzione tra maiuscole e minuscole. Evitare anche di usare `twin`, `$twin`, `body` o `$body` come nomi di proprietà. Ad esempio, di seguito sono riportate tutte le espressioni di query valide: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su [routing dei messaggi](iot-hub-devguide-messages-d2c.md).
* Provare l'[esercitazione di routing dei messaggi](tutorial-routing.md).