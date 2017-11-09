---
title: Informazioni sul linguaggio di query dell'hub IoT di Azure | Documentazione Microsoft
description: 'Guida per sviluppatori: descrizione del linguaggio di query dell''hub IoT simile a SQL usato per recuperare informazioni su dispositivi gemelli e processi dall''hub IoT.'
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 851a9ed3-b69e-422e-8a5d-1d79f91ddf15
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: elioda
ms.openlocfilehash: fd047b8618f6e6814e0656ac2ab19e30016016fa
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2017
---
# <a name="iot-hub-query-language-for-device-twins-jobs-and-message-routing"></a>Linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing di messaggi

L'hub IoT offre un linguaggio simile a SQL avanzato per recuperare informazioni su [dispositivi gemelli][lnk-twins], [processi][lnk-jobs] e [routing di messaggi][lnk-devguide-messaging-routes]. Questo articolo contiene:

* Un'introduzione alle principali funzionalità del linguaggio di query dell'hub IoT
* La descrizione dettagliata del linguaggio

## <a name="device-twin-queries"></a>Query del dispositivo gemello
I [dispositivi gemelli][lnk-twins] possono contenere oggetti JSON arbitrari come tag e proprietà. L'hub IoT consente di effettuare una query dei dispositivi gemelli come singolo documento JSON contenente tutte le informazioni sui dispositivi gemelli.
Si supponga, ad esempio, che i dispositivi gemelli dell'hub IoT abbiano la struttura seguente:

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
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

L'hub IoT espone i dispositivi gemelli come raccolta di documenti denominata **devices**.
Quindi la query seguente recupera l'intero set di dispositivi:

```sql
SELECT * FROM devices
```

> [!NOTE]
> Gli [SDK dell'hub IoT][lnk-hub-sdks] supportano il paging di risultati di grandi dimensioni.

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

Per informazioni di riferimento complete sulle funzionalità di filtro, vedere la sezione [Clausola WHERE][lnk-query-where].

Sono supportati anche il raggruppamento e le aggregazioni. Ad esempio, per trovare il numero di dispositivi in ogni stato di configurazione di telemetria, usare la query seguente:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Questa query di raggruppamento restituisce un risultato simile all'esempio seguente. In questo caso i tre dispositivi segnalano che la configurazione è riuscita, due stanno ancora applicando la configurazione e uno ha segnalato un errore. 

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

### <a name="c-example"></a>Esempio in C#
La funzionalità di query viene esposta dall'[SDK del servizio C#][lnk-hub-sdks] nella classe **RegistryManager**.
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

Si noti come venga creata un'istanza dell'oggetto **query** con dimensioni della pagina (fino a 100) e quindi si possano recuperare più pagine chiamando più volte i metodi **GetNextAsTwinAsync**.
Si noti che l'oggetto query espone più **Next***, a seconda dell'opzione di deserializzazione richiesta dalla query, ad esempio se devono essere usati oggetti dispositivi gemelli, oggetti processo oppure il normale codice JSON quando si ricorre alle proiezioni.

### <a name="nodejs-example"></a>Esempio di Node. js
La funzionalità di query viene esposta da [SDK per i servizi IoT di Azure per Node.js][lnk-hub-sdks] nell'oggetto **Registro**.
Ecco un esempio di una query semplice:

```nodejs
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

Si noti come venga creata un'istanza dell'oggetto **query** con dimensioni della pagina (fino a 100) e quindi si possano recuperare più pagine chiamando più volte i metodi **nextAsTwin**.
Si noti che l'oggetto query espone più **next***, a seconda dell'opzione di deserializzazione richiesta dalla query, ad esempio se devono essere usati oggetti dispositivi gemelli, oggetti processo oppure il normale codice JSON quando si ricorre alle proiezioni.

### <a name="limitations"></a>Limitazioni
> [!IMPORTANT]
> I risultati della query possono avere qualche minuto di ritardo rispetto ai valori più recenti nei dispositivi gemelli. Se si eseguono query su singoli dispositivi gemelli in base all'D, è sempre preferibile usare l'API di recupero di dispositivi gemelli, che contiene i valori più recenti e ha soglie di limitazione più alte.

I confronti sono attualmente supportati solo tra tipi primitivi (non oggetti), ad esempio `... WHERE properties.desired.config = properties.reported.config` è supportato solo se tali proprietà hanno valori primitivi.

## <a name="get-started-with-jobs-queries"></a>Introduzione alle query dei processi
I [processi][lnk-jobs] consentono di eseguire operazioni su set di dispositivi. Ogni dispositivo gemello contiene le informazioni dei processi di cui fa parte in una raccolta denominata **jobs**.
La struttura logica è la seguente.

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
> Attualmente la proprietà dei processi non viene mai restituita quando si eseguono query sui dispositivi gemelli (ad esempio query che contengono 'FROM devices'). È possibile accedervi direttamente solo con le query che usano `FROM devices.jobs`.
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

## <a name="device-to-cloud-message-routes-query-expressions"></a>Espressioni di query per route di messaggi da dispositivo a cloud

Usando i [route da dispositivo a cloud][lnk-devguide-messaging-routes], è possibile configurare l'hub IoT per l'invio di messaggi da dispositivo a cloud a endpoint diversi in base alle espressioni valutate nei singoli messaggi.

La [condizione][lnk-query-expressions] route usa lo stesso linguaggio di query dell'hub IoT come condizione nelle query gemelle e di processo. Le condizioni di routing vengono valutate nel corpo e nelle intestazioni del messaggio. L'espressione di query del routing potrebbe riguardare solo le intestazioni dei messaggi, solo il corpo del messaggio oppure entrambi. Per il routing dei messaggi, l'hub IoT presuppone uno schema specifico per le intestazioni e il corpo del messaggio. Nelle sezioni seguenti vengono descritti i requisiti dell'hub IoT per un routing corretto.

### <a name="routing-on-message-headers"></a>Routing delle intestazioni dei messaggi

L'hub IoT presuppone la seguente rappresentazione JSON delle intestazioni dei messaggi per il routing dei messaggi:

```json
{
    "$messageId": "",
    "$enqueuedTime": "",
    "$to": "",
    "$expiryTimeUtc": "",
    "$correlationId": "",
    "$userId": "",
    "$ack": "",
    "$connectionDeviceId": "",
    "$connectionDeviceGenerationId": "",
    "$connectionAuthMethod": "",
    "$content-type": "",
    "$content-encoding": "",

    "userProperty1": "",
    "userProperty2": ""
}
```

Le proprietà di sistema del messaggio hanno come prefisso il simbolo `'$'`.
Alle proprietà utente si accede sempre con il relativo nome. Se un nome di proprietà utente coincide con una proprietà di sistema, ad esempio `$to`, la proprietà dell'utente verrà recuperata con l'espressione `$to`.
È sempre possibile accedere alle proprietà di sistema con le parentesi `{}`: ad esempio, l'espressione `{$to}` consente di accedere alla proprietà di sistema `to`. I nomi di proprietà tra parentesi recuperano sempre le corrispondenti proprietà di sistema.

Si ricordi che nei nomi delle proprietà viene fatta distinzione tra maiuscole e minuscole.

> [!NOTE]
> Tutte le proprietà dei messaggi sono stringhe. Le proprietà di sistema, come descritto nella [guida per gli sviluppatori][lnk-devguide-messaging-format], non sono attualmente disponibili per l'uso nelle query.
>

Ad esempio, se si utilizza una proprietà `messageType`, è possibile indirizzare tutti i dati di telemetria a un endpoint e tutti gli avvisi a un altro endpoint. È possibile scrivere l'espressione seguente per indirizzare i dati di telemetria:

```sql
messageType = 'telemetry'
```

L'espressione seguente per indirizzare i messaggi di avviso:

```sql
messageType = 'alert'
```

Sono supportate anche le espressioni e le funzione booleane. Questa funzionalità consente di distinguere i vari livelli di gravità, ad esempio:

```sql
messageType = 'alerts' AND as_number(severity) <= 2
```

Fare riferimento alla sezione [Espressioni e condizioni][lnk-query-expressions] per l'elenco completo delle funzioni e degli operatori supportati.

### <a name="routing-on-message-bodies"></a>Routing del corpo dei messaggi

L'hub IoT può eseguire il routing solo in base al contenuto del corpo del messaggio se il corpo del messaggio è formato correttamente con codifica JSON in UTF-8, UTF-16 o UTF-32. Impostare il tipo di contenuto del messaggio su `application/json` e la codifica del contenuto su una delle codifiche UTF supportate nelle intestazioni del messaggio. Se una delle intestazioni viene omessa, l'hub IoT non tenterà di valutare alcuna espressione di query che coinvolga il corpo rispetto al messaggio. Se il messaggio non è un messaggio JSON oppure se il messaggio non specifica il tipo di contenuto e la codifica del contenuto, è comunque possibile usare il routing dei messaggi per indirizzare il messaggio in base alle intestazioni.

È possibile usare `$body` nell'espressione di query per indirizzare il messaggio. Nell'espressione di query è possibile usare un riferimento semplice al corpo, un riferimento a una matrice del corpo o riferimenti multipli al corpo. L'espressione di query può anche combinare un riferimento al corpo con un riferimento all'intestazione del messaggio. Ad esempio, di seguito sono riportate tutte le espressioni di query valide:

```sql
$body.message.Weather.Location.State = 'WA'
$body.Weather.HistoricalData[0].Month = 'Feb'
$body.Weather.Temperature = 50 AND $body.message.Weather.IsEnabled
length($body.Weather.Location.State) = 2
$body.Weather.Temperature = 50 AND Status = 'Active'
```

## <a name="basics-of-an-iot-hub-query"></a>Nozioni di base di una query dell'hub IoT
Ogni query dell'hub IoT è costituita da una clausola SELECT e da una clausola FROM e dalle clausole facoltative WHERE e GROUP BY. Ogni query viene eseguita su una raccolta di documenti JSON, ad esempio dispositivi gemelli. La clausola FROM indica la raccolta di documenti in cui eseguire l'iterazione (**devices** o **devices.jobs**). Viene quindi applicato il filtro nella clausola WHERE. Nel caso delle aggregazioni, i risultati di questo passaggio vengono raggruppati come specificato nella clausola GROUP BY e, per ogni gruppo, viene generata una riga come specificato nella clausola SELECT.

```sql
SELECT <select_list>
FROM <from_specification>
[WHERE <filter_condition>]
[GROUP BY <group_specification>]
```

## <a name="from-clause"></a>Clausola FROM
La clausola **FROM <from_specification>** può avere solo due valori: **FROM devices**, per effettuare una query dei dispositivi gemelli, o **FROM devices.jobs**, per effettuare una query dei dettagli di ogni dispositivo.

## <a name="where-clause"></a>Clausola WHERE
La clausola **WHERE <filter_condition>** è facoltativa e specifica una o più condizioni che i documenti JSON della raccolta FROM devono soddisfare per essere inclusi come parte del risultato. Per essere incluso nel risultato, qualsiasi documento JSON deve restituire "true" per le condizioni specificate.

Le condizioni consentite vengono descritte nella sezione [Espressioni e condizioni][lnk-query-expressions].

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

**Attribute_name** si riferisce alle proprietà del documento JSON nella raccolta FROM. Alcuni esempi di clausola SELECT sono disponibili nella sezione [Introduzione alle query dei dispositivi gemelli][lnk-query-getstarted].

Attualmente le clausole di selezione diverse da **SELECT*** sono supportate solo nelle query aggregate in dispositivi gemelli.

## <a name="group-by-clause"></a>Clausola GROUP BY
La clausola **GROUP BY <group_specification>** è un passaggio facoltativo che può essere eseguito dopo aver applicato il filtro specificato nella clausola WHERE e prima della proiezione specificata in SELECT. Raggruppa i documenti in base al valore di un attributo. Questi gruppi vengono usati per generare valori aggregati come specificato nella clausola SELECT.

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

## <a name="expressions-and-conditions"></a>Espressioni e condizioni
In generale, un'*espressione*:

* Restituisce un'istanza di un tipo JSON, ad esempio un operatore booleano, un numero, una stringa, una matrice o un oggetto.
* Viene definita modificando i dati provenienti dalle costanti e dal documento JSON dei dispositivi, che usano funzioni e operatori predefiniti.

Le *condizioni* sono espressioni che restituiscono un valore booleano. Una costante diversa dal valore booleano **true** viene considerata **false** (inclusi **null**, **undefined**, qualsiasi istanza di oggetto o matrice, qualsiasi stringa e ovviamente il valore booleano **false**).

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
| IS_PRIMITIVE | Restituisce un valore booleano che indica se il tipo di espressione specificata è un primitivo (stringa, valore booleano, numerico o `null`). |
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
Informazioni su come eseguire query nelle app usando gli [SDK dell'hub IoT][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#get-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging-routes]: iot-hub-devguide-messages-read-custom.md
[lnk-devguide-messaging-format]: iot-hub-devguide-messages-construct.md
[lnk-devguide-messaging-routes]: ./iot-hub-devguide-messages-read-custom.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
