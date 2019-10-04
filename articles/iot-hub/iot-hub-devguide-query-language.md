---
title: Informazioni sul linguaggio di query dell'hub IoT di Azure | Documentazione Microsoft
description: "Guida per sviluppatori: descrizione del linguaggio di query dell'hub IoT simile a SQL usato per recuperare informazioni su dispositivi/moduli gemelli e processi dall'hub IoT."
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: robinsh
ms.openlocfilehash: 03d2ca0b7d6b53215c5293f84c8b22a2dc0d8297
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450068"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>Linguaggio di query dell'hub IoT per dispositivi e moduli gemelli, processi e routing di messaggi

L'IoT Hub fornisce un linguaggio simile a SQL avanzato per recuperare le informazioni relative a [sui dispositivi gemelli](iot-hub-devguide-device-twins.md), [moduli gemelli](iot-hub-devguide-module-twins.md), [processi](iot-hub-devguide-jobs.md), e [routingdeimessaggi](iot-hub-devguide-messages-d2c.md). Questo articolo contiene:

* Un'introduzione alle principali funzionalità del linguaggio di query dell'hub IoT
* La descrizione dettagliata del linguaggio Per informazioni sul linguaggio di query per il routing dei messaggi, vedere [Query nel routing dei messaggi](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Query su dispositivi e moduli gemelli

[Dispositivi gemelli](iot-hub-devguide-device-twins.md) e [moduli gemelli](iot-hub-devguide-module-twins.md) possono contenere oggetti JSON arbitrari come tag e proprietà. L'hub IoT consente di effettuare una query sui dispositivi e i moduli gemelli come singolo documento JSON contenente tutte le informazioni sui dispositivi e i moduli gemelli.

Si supponga, ad esempio, che i dispositivi gemelli dell'hub IoT abbiano la struttura seguente (i moduli gemelli sarebbero simili, avrebbero solo un moduleId aggiuntivo):

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

### <a name="device-twin-queries"></a>Query del dispositivo gemello

L'hub IoT espone i dispositivi gemelli come raccolta di documenti denominata **devices**. Ad esempio, la query seguente recupera l'intero set di dispositivi gemelli:

```sql
SELECT * FROM devices
```

> [!NOTE]
> Gli [SDK dell'hub IoT](iot-hub-devguide-sdks.md) supportano il paging di risultati di grandi dimensioni.

L'hub IoT consente di recuperare i dispositivi gemelli applicando filtri con condizioni arbitrarie. Ad esempio, per ricevere dispositivi gemelli in cui il tag **location.region** è impostato su**US** usare la query seguente:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Sono supportati anche operatori booleani e confronti aritmetici. Ad esempio, per recuperare dispositivi gemelli che si trovano negli Stati Uniti e configurati per inviare ogni minuto al massimo i dati di telemetria, usare la query seguente:

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Per praticità, è anche possibile usare costanti di matrice con gli operatori **IN** e **NIN** (non in). Ad esempio, per recuperare dispositivi gemelli che segnalano connettività Wi-Fi o cablata, usare la query seguente:

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

È spesso necessario identificare tutti i dispositivi gemelli che contengono una proprietà specifica. L'hub IoT supporta la funzione `is_defined()` per questo scopo. Ad esempio, per recuperare dispositivi gemelli che definiscono la proprietà `connectivity`, usare la query seguente:

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

Per informazioni di riferimento complete sulle funzionalità di filtro, vedere la sezione [Clausola WHERE](iot-hub-devguide-query-language.md#where-clause).

Sono supportati anche il raggruppamento e le aggregazioni. Ad esempio, per trovare il numero di dispositivi in ogni stato di configurazione di telemetria, usare la query seguente:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

Questa query di raggruppamento restituisce un risultato simile all'esempio seguente:

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

In questo esempio, tre dispositivi segnalano che la configurazione è riuscita, due stanno ancora applicando la configurazione e uno ha segnalato un errore.

Le query di proiezione consentono agli sviluppatori di restituire solo le proprietà che interessano. Ad esempio, per recuperare l'ora dell'ultima attività per tutti i dispositivi disconnessi, usare la query seguente:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Query sui moduli gemelli

Una query su moduli gemelli è simile all'esecuzione di query sui dispositivi gemelli, ma con una raccolta/spazio dei nomi diverso; anziché da **periferiche**, si esegue una query da **devices.modules**:

```sql
SELECT * FROM devices.modules
```

Il join tra i dispositivi e le raccolte devices.modules non è consentito. Per eseguire query sui moduli gemelli nei dispositivi, usare i tag. Questa query restituirà tutti i moduli gemelli presenti in tutti i dispositivi con lo stato di analisi:

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

Questa query restituirà tutti i moduli gemelli con lo stato di analisi, ma solo nel subset di dispositivi specificato:

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>Esempio in C#

La funzionalità di query viene esposta dall'[SDK del servizio C#](iot-hub-devguide-sdks.md) nella classe **RegistryManager**.

Ecco un esempio di una query semplice:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

L'istanza dell'oggetto **query** viene creata con dimensioni della pagina (fino a 100). Vengono quindi recuperate più pagine tramite più chiamate ai metodi **GetNextAsTwinAsync**.

L'oggetto query espone più valori **Next**, a seconda dell'opzione di deserializzazione richiesta dalla query, ad esempio, oggetti dispositivo gemello o processo oppure JSON semplice, in caso di uso di proiezioni.

### <a name="nodejs-example"></a>Esempio di Node. js

La funzionalità di query viene esposta da [SDK per i servizi IoT di Azure per Node.js](iot-hub-devguide-sdks.md) nell'oggetto **Registry**.

Ecco un esempio di una query semplice:

```javascript
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

L'istanza dell'oggetto **query** viene creata con dimensioni della pagina (fino a 100). Quindi vengono recuperate più pagine tramite più chiamate al metodo **nextAsTwin**.

L'oggetto query espone più valori **Next**, a seconda dell'opzione di deserializzazione richiesta dalla query, ad esempio, oggetti dispositivo gemello o processo oppure JSON semplice, in caso di uso di proiezioni.

### <a name="limitations"></a>Limitazioni

> [!IMPORTANT]
> I risultati della query possono avere qualche minuto di ritardo rispetto ai valori più recenti nei dispositivi gemelli. Se la query di dispositivi gemelli si basa sull'ID, usare l'API di recupero di dispositivi gemelli. Questa API contiene sempre i valori più recenti e ha limitazioni superiori.

I confronti sono attualmente supportati solo tra tipi primitivi (non oggetti), ad esempio `... WHERE properties.desired.config = properties.reported.config` è supportato solo se tali proprietà hanno valori primitivi.

## <a name="get-started-with-jobs-queries"></a>Introduzione alle query dei processi

I [processi](iot-hub-devguide-jobs.md) consentono di eseguire operazioni su set di dispositivi. Ogni dispositivo gemello contiene le informazioni dei processi di cui fa parte in una raccolta denominata **jobs**.

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

Attualmente è possibile effettuare una query di questa raccolta come **devices.jobs** nel linguaggio di query dell'hub IoT.

> [!IMPORTANT]
> Attualmente la proprietà jobs non viene mai restituita quando si eseguono query sui dispositivi gemelli, ovvero, query che contengono 'FROM devices'. La proprietà jobs è accessibile solo direttamente tramite query che usano `FROM devices.jobs`.
>
>

Ad esempio, per ottenere tutti i processi (passati e pianificati) che influiscono su un singolo dispositivo, è possibile usare la query seguente:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

Si noti come questa query indichi lo stato specifico del dispositivo (e verosimilmente la risposta del metodo diretto) di ogni processo restituito.

È anche possibile applicare un filtro con condizioni booleane arbitrarie a tutte le proprietà degli oggetti della raccolta **devices.jobs**.

Ad esempio, per recuperare tutti i processi di aggiornamento per dispositivi gemelli completi che sono stati creati dopo il mese di settembre 2016 per un dispositivo specifico, usare la query seguente:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

È anche possibile recuperare i risultati per ogni dispositivo di un singolo processo.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Limitazioni

Attualmente le query su **devices.jobs** non supportano:

* Proiezioni, quindi è possibile solo `SELECT *`.
* Condizioni che fanno riferimento al dispositivo gemello oltre alle proprietà del processo (vedere sezione precedente).
* Esecuzione di aggregazioni, ad esempio count, avg, group by.

## <a name="basics-of-an-iot-hub-query"></a>Nozioni di base di una query dell'hub IoT

Ogni query dell'hub IoT è costituita da una clausola SELECT e da una clausola FROM e dalle clausole facoltative WHERE e GROUP BY. Ogni query viene eseguita su una raccolta di documenti JSON, ad esempio dispositivi gemelli. La clausola FROM indica la raccolta di documenti per eseguire l'iterazione (**periferiche**, **devices.modules**, o **devices.jobs**). Viene quindi applicato il filtro nella clausola WHERE. Con le aggregazioni, i risultati di questo passaggio vengono raggruppati come specificato nella clausola GROUP BY. Per ogni gruppo, viene generata una riga come specificato nella clausola SELECT.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>Clausola FROM

Il **FROM < from_specification >** clausola può avere solo tre valori: **DAI dispositivi** alla query dei dispositivi gemelli, **da devices.modules** a moduli gemelli di query, o **da devices.jobs** a dettagli di ogni dispositivo di processo di query.

## <a name="where-clause"></a>Clausola WHERE

La clausola **WHERE <filter_condition>** è facoltativa e specifica una o più condizioni che i documenti JSON della raccolta FROM devono soddisfare per essere inclusi come parte del risultato. Per essere incluso nel risultato, qualsiasi documento JSON deve restituire "true" per le condizioni specificate.

Le condizioni consentite vengono descritte nella sezione [Espressioni e condizioni](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="select-clause"></a>Clausola SELECT

La clausola **SELECT <select_list>** è obbligatoria e specifica quali valori vengono recuperati dalla query. Specifica i valori JSON da usare per generare nuovi oggetti JSON.
Per ogni elemento del subset filtrato (e facoltativamente raggruppato) della raccolta FROM, la fase di proiezione genera un nuovo oggetto JSON, costruito con i valori specificati nella clausola SELECT.

Questa è la sintassi della clausola SELECT:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**Attribute_name** si riferisce alle proprietà del documento JSON nella raccolta FROM. Alcuni esempi di clausola SELECT sono disponibili nella sezione di introduzione alle query sui dispositivi gemelli.

Attualmente le clausole di selezione diverse da **SELECT*** sono supportate solo nelle query aggregate in dispositivi gemelli.

## <a name="group-by-clause"></a>Clausola GROUP BY

La clausola **GROUP BY <group_specification>** è un passaggio facoltativo che viene eseguito dopo l'applicazione del filtro specificato nella clausola WHERE e prima della proiezione specificata in SELECT. Raggruppa i documenti in base al valore di un attributo. Questi gruppi vengono usati per generare valori aggregati come specificato nella clausola SELECT.

Ecco un esempio di query che usa GROUP BY:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

La sintassi formale di GROUP BY è:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** si riferisce alle proprietà del documento JSON nella raccolta FROM.

Attualmente la clausola GROUP BY è supportata solo quando si effettua una query sui dispositivi gemelli.

> [!IMPORTANT]
> Il termine `group` viene attualmente considerato una parola chiave speciale nelle query. Se si utilizza `group` come nome di proprietà, è consigliabile racchiuderlo tra doppie parentesi quadre per evitare errori, ad esempio `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'`.
>

## <a name="expressions-and-conditions"></a>Espressioni e condizioni

In generale, un'*espressione*:

* Restituisce un'istanza di un tipo JSON, ad esempio un operatore booleano, un numero, una stringa, una matrice o un oggetto.
* Viene definita modificando i dati provenienti dalle costanti e dal documento JSON dei dispositivi, che usano funzioni e operatori predefiniti.

Le *condizioni* sono espressioni che restituiscono un valore booleano. Qualsiasi costante diversa dal valore booleano **true** viene considerata come **false**. Questa regola include **null**, **undefined**, qualsiasi istanza di oggetto o di matrice, qualsiasi stringa e il valore booleano **false**.

La sintassi delle espressioni è:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

Per informazioni sul significato di ogni simbolo nella sintassi delle espressioni, fare riferimento alla tabella seguente:

| Simbolo | Definizione |
| --- | --- |
| attribute_name | Proprietà del documento JSON nella raccolta **FROM**. |
| binary_operator | Operatore binario elencato nella sezione [Operatori](#operators). |
| function_name| Funzioni elencate nella sezione [Funzioni](#functions). |
| decimal_literal |Float espresso in una notazione decimale. |
| hexadecimal_literal |Numero espresso dalla stringa "0x" seguita da una stringa costituita da cifre esadecimali. |
| string_literal |I valori letterali stringa sono stringhe Unicode rappresentate da una sequenza di zero o più caratteri Unicode o sequenze di escape. I valori letterali stringa sono racchiusi tra virgolette singole o virgolette doppie. Caratteri di escape consentiti: `\'`, `\"`, `\\`, `\uXXXX` per i caratteri Unicode definiti da 4 cifre esadecimali. |

### <a name="operators"></a>Operatori

Sono supportati gli operatori seguenti:

| Famiglia | Operatori |
| --- | --- |
| Aritmetico |+, -, *, /, % |
| Logico |AND, OR, NOT |
| Confronto |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Funzioni

Quando si eseguono query gemelle e di processi l'unica funzione supportata è:

| Funzione | Descrizione |
| -------- | ----------- |
| IS_DEFINED(proprietà) | Restituisce un valore booleano che indica se alla proprietà è stata assegnato un valore (incluso `null`). |

Nelle condizioni di route, sono supportate le funzioni matematiche seguenti:

| Funzione | Descrizione |
| -------- | ----------- |
| ABS(x) | Restituisce il valore assoluto (positivo) dell'espressione numerica specificata. |
| EXP(x) | Restituisce il valore esponente dell'espressione numerica specificata (e^x). |
| POWER(x,y) | Restituisce il valore dell'espressione specificata alla potenza specificata (x^y).|
| SQUARE(x) | Restituisce il quadrato del valore numerico specificato. |
| CEILING(x) | Restituisce il più piccolo valore integer maggiore di o uguale all'espressione numerica specificata. |
| FLOOR(x) | Restituisce il valore integer più alto, minore di o uguale all'espressione numerica specificata. |
| SIGN(x) | Restituisce il segno positivo (+1), zero (0) o negativo (-1) dell'espressione numerica specificata.|
| SQRT(x) | Restituisce la radice quadrata del valore numerico specificato. |

Nelle condizioni di route, sono supportate le funzioni di trasmissione e controllo seguenti:

| Funzione | Descrizione |
| -------- | ----------- |
| AS_NUMBER | Converte la stringa di input in un numero. `noop` se l'input è un numero, `Undefined` se la stringa non rappresenta un numero.|
| IS_ARRAY | Restituisce un valore booleano che indica se il tipo di espressione specificata è una matrice. |
| IS_BOOL | Restituisce un valore booleano che indica se il tipo di espressione specificata è un valore booleano. |
| IS_DEFINED | Restituisce un valore booleano che indica se alla proprietà è stata assegnato un valore. |
| IS_NULL | Restituisce un valore booleano che indica se il tipo di espressione specificata è nulla. |
| IS_NUMBER | Restituisce un valore booleano che indica se il tipo di espressione specificata è un numero. |
| IS_OBJECT | Restituisce un valore booleano che indica se il tipo di espressione specificata è un oggetto JSON. |
| IS_PRIMITIVE | Restituisce un valore booleano che indica se il tipo dell'espressione specificata è primitivo (stringa, valore booleano, numerico o `null`). |
| IS_STRING | Restituisce un valore booleano che indica se il tipo di espressione specificata è una stringa. |

Nelle condizioni di route, sono supportate le funzioni di stringa seguenti:

| Funzione | Descrizione |
| -------- | ----------- |
| CONCAT(x, y, …) | Restituisce una stringa che rappresenta il risultato della concatenazione di due o più valori di stringa. |
| LENGTH(x) | Restituisce il numero di caratteri dell'espressione stringa specificata.|
| LOWER(x) | Restituisce un'espressione stringa dopo la conversione di dati in caratteri maiuscoli in caratteri minuscoli. |
| UPPER(x) | Restituisce un'espressione stringa dopo aver convertito i caratteri minuscoli in caratteri maiuscoli. |
| SUBSTRING (stringa, avvio [, lunghezza]) | Restituisce parte di un'espressione stringa a partire dalla posizione in base al carattere zero specificata e continua fino alla lunghezza specificata o alla fine della stringa. |
| INDEX_OF(stringa, frammento) | Restituisce la posizione iniziale della prima occorrenza della seconda stringa di espressione all'interno della prima espressione stringa specificata oppure -1 se la stringa non viene trovata.|
| STARTS_WITH(x, y) | Restituisce un valore booleano che indica se la prima espressione stringa inizia con il secondo. |
| ENDS_WITH(x, y) | Restituisce un valore booleano che indica se la prima espressione stringa termina con il secondo. |
| CONTAINS(x,y) | Restituisce un valore booleano che indica se la prima espressione stringa contiene il secondo. |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come eseguire query nelle app usando gli [SDK dell'hub IoT](iot-hub-devguide-sdks.md).