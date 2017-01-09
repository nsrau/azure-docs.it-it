---
title: 'Guida per sviluppatori: linguaggio di query dell&quot;hub IoT | Documentazione Microsoft'
description: 'Guida per sviluppatori dell&quot;hub IoT di Azure: descrizione del linguaggio di query simile a SQL usato per recuperare informazioni su dispositivi gemelli e processi dall&quot;hub IoT'
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
ms.date: 09/30/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 627de0ca1647e98e08165521e7d3a519e1950296
ms.openlocfilehash: 8007c6864368868d9cb489236d958eeada8789bd


---
# <a name="reference---iot-hub-query-language-for-device-twins-and-jobs"></a>Informazioni di riferimento: linguaggio di query dell'hub IoT per dispositivi gemelli e processi
## <a name="overview"></a>Panoramica
L'hub IoT fornisce un linguaggio simile a SQL avanzato per recuperare informazioni su [dispositivi gemelli][lnk-twins] e [processi][lnk-jobs]. Questo articolo contiene:

* Un'introduzione alle principali funzionalità del linguaggio di query dell'hub IoT
* La descrizione dettagliata del linguaggio

## <a name="getting-started-with-device-twin-queries"></a>Introduzione alle query dei dispositivi gemelli
I [dispositivi gemelli][lnk-twins] possono contenere oggetti JSON arbitrari come tag e proprietà. L'hub IoT consente di effettuare una query dei dispositivi gemelli come singolo documento JSON contenente tutte le informazioni sui dispositivi gemelli.
Si supponga, ad esempio, che i dispositivi gemelli dell'hub IoT abbiano la struttura seguente:

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

L'hub IoT espone i dispositivi gemelli come raccolta di documenti denominata **devices**.
Quindi la query seguente recupera l'intero set di dispositivi:

        SELECT * FROM devices

> [!NOTE]
> Gli [SDK dell'hub IoT][lnk-hub-sdks] supportano il paging di risultati di grandi dimensioni.
>
>

L'hub IoT consente di recuperare i dispositivi gemelli applicando filtri con condizioni arbitrarie. Ad esempio,

        SELECT * FROM devices
        WHERE tags.location.region = 'US'

recupera i dispositivi gemelli con il tag **location.region** impostato su **US**.
Sono supportati anche gli operatori booleani e confronti aritmetici, ad esempio

        SELECT * FROM devices
        WHERE tags.location.region = 'US'
            AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60

recupera tutti i dispositivi gemelli situati negli Stati Uniti configurati per l'invio di dati di telemetria meno di ogni minuto. Per praticità, è anche possibile usare costanti di matrice in combinazione con gli operatori **IN** e **NIN** (not in). Ad esempio,

        SELECT * FROM devices
        WHERE property.reported.connectivity IN ['wired', 'wifi']

recupera tutti i dispositivi gemelli che hanno segnalato una connettività WiFi o cablata. È spesso necessario identificare tutti i dispositivi gemelli che contengono una proprietà specifica. L'hub IoT supporta la funzione `is_defined()` per questo scopo. Ad esempio,

        SELECT * FROM devices
        WHERE is_defined(property.reported.connectivity)

recupera tutti i dispositivi gemelli che definiscono la proprietà segnalata `connectivity`. Per informazioni di riferimento complete sulle funzionalità di filtro, vedere la sezione [Clausola WHERE][lnk-query-where].

Sono supportati anche il raggruppamento e le aggregazioni. Ad esempio,

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

restituisce il numero di dispositivi in ogni stato di configurazione della telemetria.

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

L'esempio precedente illustra una situazione in cui tre dispositivi hanno segnalato che la configurazione è riuscita, due stanno ancora applicando la configurazione e uno ha segnalato un errore.

### <a name="c-example"></a>Esempio in C#
La funzionalità di query viene esposta dall'[SDK del servizio C#][lnk-hub-sdks] nella classe **RegistryManager**.
Ecco un esempio di una query semplice:

        var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
        while (query.HasMoreResults)
        {
            var page = await query.GetNextAsTwinAsync();
            foreach (var twin in page)
            {
                // do work on twin object
            }
        }

Si noti come venga creata un'istanza dell'oggetto **query** con dimensioni della pagina (fino a 1000) e quindi si possano recuperare più pagine chiamando più volte i metodi **GetNextAsTwinAsync**.
È importante notare che l'oggetto query espone più **next\***, a seconda dell'opzione di deserializzazione richiesta dalla query, ad esempio se devono essere usati oggetti twin o job oppure il normale codice JSON quando si ricorre alle proiezioni.

### <a name="node-example"></a>Esempio in Node
La funzionalità di query viene esposta dall'[SDK del servizio Node][lnk-hub-sdks] nell'oggetto **Registry**.
Ecco un esempio di una query semplice:

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

Si noti come venga creata un'istanza dell'oggetto **query** con dimensioni della pagina (fino a 1000) e quindi si possano recuperare più pagine chiamando più volte i metodi **nextAsTwin**.
È importante notare che l'oggetto query espone più **next\***, a seconda dell'opzione di deserializzazione richiesta dalla query, ad esempio se devono essere usati oggetti twin o job oppure il normale codice JSON quando si ricorre alle proiezioni.

### <a name="limitations"></a>Limitazioni
I confronti sono attualmente supportati solo tra tipi primitivi (non oggetti), ad esempio `... WHERE properties.desired.config = properties.reported.config` è supportato solo se tali proprietà hanno valori primitivi.

## <a name="getting-started-with-jobs-queries"></a>Introduzione alle query dei processi
I [processi][lnk-jobs] consentono di eseguire operazioni su set di dispositivi. Ogni dispositivo gemello contiene le informazioni dei processi di cui fa parte in una raccolta denominata **jobs**.
La struttura logica è la seguente.

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

Attualmente è possibile effettuare una query di questa raccolta come **devices.jobs** nel linguaggio di query dell'hub IoT.

> [!IMPORTANT]
> Attualmente la proprietà dei processi non viene mai restituita quando si eseguono query sui dispositivi gemelli, ad esempio query che contengono 'FROM devices'. È possibile accedervi direttamente solo con le query che usano `FROM devices.jobs`.
>
>

Ad esempio, per ottenere tutti i processi (passati e pianificati) che influiscono su un singolo dispositivo, è possibile usare la query seguente:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'

Si noti come questa query indichi lo stato specifico del dispositivo (e verosimilmente la risposta del metodo diretto) di ogni processo restituito.
È anche possibile applicare un filtro con condizioni booleane arbitrarie a tutte le proprietà degli oggetti della raccolta **devices.jobs**.
Ad esempio, la query seguente:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'
            AND devices.jobs.jobType = 'scheduleTwinUpdate'
            AND devices.jobs.status = 'completed'
            AND devices.jobs.createdTimeUtc > '2016-09-01'

recupera tutti i processi di aggiornamento del dispositivo gemello completati per il dispositivo **myDeviceId** e creati dopo settembre 2016.

È anche possibile recuperare i risultati per ogni dispositivo di un singolo processo.

        SELECT * FROM devices.jobs
        WHERE devices.jobs.jobId = 'myJobId'

### <a name="limitations"></a>Limitazioni
Attualmente le query su **devices.jobs** non supportano:

* Proiezioni, quindi è possibile solo `SELECT *`
* Condizioni che fanno riferimento al dispositivo gemello oltre alle proprietà del processo, come illustrato sopra
* Esecuzione di aggregazioni, ad esempio count, avg, group by.

## <a name="basics-of-an-iot-hub-query"></a>Nozioni di base di una query dell'hub IoT
Ogni query dell'hub IoT è costituita da una clausola SELECT e da una clausola FROM e dalle clausole facoltative WHERE e GROUP BY. Ogni query viene eseguita su una raccolta di documenti JSON, ad esempio dispositivi gemelli. La clausola FROM indica la raccolta di documenti in cui eseguire l'iterazione (**devices** o **devices.jobs**). Viene quindi applicato il filtro nella clausola WHERE. Nel caso delle aggregazioni, i risultati di questo passaggio vengono raggruppati come specificato nella clausola GROUP BY e, per ogni gruppo, viene generata una riga come specificato nella clausola SELECT.

        SELECT <select_list>
        FROM <from_specification>
        [WHERE <filter_condition>]
        [GROUP BY <group_specification>]

## <a name="from-clause"></a>Clausola FROM
La clausola **FROM <from_specification>** può avere solo due valori: **FROM devices**, per effettuare una query dei dispositivi gemelli, o **FROM devices.jobs**, per effettuare una query dei dettagli per ogni dispositivo.

## <a name="where-clause"></a>Clausola WHERE
La clausola **WHERE <filter_condition>** è facoltativa e specifica la condizione (o le condizioni) che i documenti JSON della raccolta FROM devono soddisfare per essere inclusi come parte del risultato. Per essere incluso nel risultato, qualsiasi documento JSON deve restituire "true" per le condizioni specificate.

Le condizioni consentite vengono descritte nella sezione [Espressioni e condizioni][lnk-query-expressions].

## <a name="select-clause"></a>Clausola SELECT
La clausola SELECT (**SELECT <select_list>**) è obbligatoria e specifica quali valori saranno recuperati dalla query. Specifica i valori JSON da usare per generare nuovi oggetti JSON. Per ogni elemento del subset filtrato (e facoltativamente raggruppato) della raccolta FROM, la fase di proiezione genera un nuovo oggetto JSON, costruito con i valori specificati nella clausola SELECT.

Questa è la sintassi della clausola SELECT:

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

dove **attribute_name** si riferisce alle proprietà del documento JSON nella raccolta FROM. Alcuni esempi di clausola SELECT sono disponibili nella sezione [Introduzione alle query dei dispositivi gemelli][lnk-query-getstarted].

Attualmente le clausole di selezione diverse da **SELECT \*** sono supportate solo nelle query aggregate sui dispositivi gemelli.

## <a name="group-by-clause"></a>Clausola GROUP BY
La clausola **GROUP BY <group_specification>** è un passaggio facoltativo che può essere eseguito dopo il filtro specificato nella clausola WHERE e prima della proiezione specificata in SELECT. Raggruppa i documenti in base al valore di un attributo. Questi gruppi vengono usati per generare valori aggregati come specificato nella clausola SELECT.

Ecco un esempio di query che usa GROUP BY:

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

La sintassi formale di GROUP BY è:

        GROUP BY <group_by_element>
        <group_by_element> :==
            attribute_name
            | < group_by_element > '.' attribute_name

dove **attribute_name** si riferisce alle proprietà del documento JSON nella raccolta FROM.

Attualmente la clausola GROUP BY è supportata solo quando si effettua una query sui dispositivi gemelli.

## <a name="expressions-and-conditions"></a>Espressioni e condizioni
In generale, un'*espressione*:

* Restituisce un'istanza di un tipo JSON, ad esempio Boolean, number, string, array o object
* Viene definita modificando i dati provenienti dalle costanti e dal documento JSON dei dispositivi, che usano funzioni e operatori predefiniti.

Le *condizioni* sono espressioni che restituiscono un valore booleano, quindi una costante diversa dal valore booleano **true** viene considerata **false** (inclusi **null**, **undefined**, qualsiasi istanza di oggetto o matrice, qualsiasi stringa e ovviamente il valore booleano **false**).

La sintassi delle espressioni è:

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

dove:

| Simbolo | Definizione |
| --- | --- |
| attribute_name |Proprietà del documento JSON nella raccolta FROM. |
| binary_operator |Operatore binario come indicato nella sezione Operatori. |
| function_name| L'unico valore supportato è `is_defined()` |
| decimal_literal |Float espresso in una notazione decimale. |
| hexadecimal_literal |Numero espresso dalla stringa "0x" seguita da una stringa costituita da cifre esadecimali. |
| string_literal |I valori letterali stringa sono stringhe Unicode rappresentate da una sequenza di zero o più caratteri Unicode o sequenze di escape. I valori letterali stringa sono racchiusi tra virgolette singole (apostrofo: ') o virgolette doppie (virgolette inglesi: "). Caratteri di escape consentiti: `\'`, `\"`, `\\`, `\uXXXX` per i caratteri Unicode definiti da 4 cifre esadecimali. |

### <a name="operators"></a>Operatori
Sono supportati gli operatori seguenti:

| Famiglia | Operatori |
| --- | --- |
| Aritmetico |+,-,*,/,% |
| Logico |AND, OR, NOT |
| Confronto |=, !=, <, >, <=, >=, <> |

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come eseguire query nelle app usando gli [SDK dell'hub IoT][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#getting-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md



<!--HONumber=Nov16_HO5-->


