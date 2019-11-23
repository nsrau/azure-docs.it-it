---
title: How to debug UDFs - Azure Digital Twins | Microsoft Docs
description: Learn about recommended approaches to debug user-defined functions in Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/01/2019
ms.custom: seodec18
ms.openlocfilehash: a5f5729836e031b895fdb584efd971f2b8653353
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383387"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Come eseguire il debug di funzioni definite dall'utente in Gemelli digitali di Azure

This article summarizes how to diagnose and debug user-defined functions in Azure Digital Twins. Quindi identifica alcuni degli scenari più comuni riscontrati quando si esegue il debug di tali funzioni.

>[!TIP]
> Vedere [Come configurare il monitoraggio in Gemelli digitali di Azure](./how-to-configure-monitoring.md) per altre informazioni su come configurare gli strumenti di debug in Gemelli digitali di Azure mediante log attività, log di diagnostica e Monitoraggio di Azure.

## <a name="debug-issues"></a>Eseguire il debug dei problemi

Knowing how to diagnose issues within Azure Digital Twins allows you to effectively analyze issues, identify the causes of problems, and provide appropriate solutions for them.

A variety of logging, analytics, and diagnostic tools are provided to that end.

### <a name="enable-logging-for-your-instance"></a>Enable logging for your instance

Gemelli digitali di Azure supporta potenti funzionalità di registrazione, monitoraggio e analisi. Solutions developers can use Azure Monitor logs, diagnostic logs, activity logs, and other services to support the complex monitoring needs of an IoT app. Le opzioni di registrazione possono essere combinate per eseguire query o visualizzare i record in diversi servizi e per fornire una copertura di registrazione granulare per numerosi servizi.

* For logging configuration specific to Azure Digital Twins, read [How to configure monitoring and logging](./how-to-configure-monitoring.md).
* Consult the [Azure Monitor](../azure-monitor/overview.md) overview to learn about powerful log settings enabled through Azure Monitor.
* Review the article [Collect and consume log data from your Azure resources](../azure-monitor/platform/resource-logs-overview.md) for configuring diagnostic log settings in Azure Digital Twins through the Azure portal, Azure CLI, or PowerShell.

Once configured, you'll be able to select all log categories, metrics, and use powerful Azure Monitor log analytics workspaces to support your debugging efforts.

### <a name="trace-sensor-telemetry"></a>Tracciare i dati di telemetria del sensore

Per tracciare i dati di telemetria del sensore, verificare che siano abilitate le impostazioni di diagnostica per l'istanza di Gemelli digitali di Azure. Quindi assicurarsi che tutte le categorie di log desiderate siano selezionate. Lastly, confirm that the desired logs are being sent to Azure Monitor logs.

Per associare un messaggio di telemetria del sensore ai rispettivi log, è possibile specificare un ID di correlazione per i dati dell'evento inviati. A tale scopo, impostare la proprietà `x-ms-client-request-id` su un GUID.

After sending telemetry, open Azure Monitor log analytics to query for logs using the set Correlation ID:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Valore della query | Sostituire con |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | ID di correlazione specificato per i dati dell'evento |

To see all recent telemetry logs query:

```Kusto
AzureDiagnostics
| order by CorrelationId desc
```

If you enable logging for your user-defined function, those logs appear in your log analytics instance with the category `UserDefinedFunction`. To retrieve them, enter the following query condition in log analytics:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Per altre informazioni sulle operazioni di query avanzate, vedere [Introduzione alle query in Log Analytics](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Identificare i problemi più comuni

La diagnosi e l'identificazione dei problemi più comuni sono importanti per la risoluzione dei problemi della soluzione. Nelle sottosezioni seguenti viene fornito un riepilogo di alcuni problemi comuni riscontrati durante lo sviluppo di funzioni definite dall'utente.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Verificare che sia stata creata un'assegnazione di ruolo

Se non è stata creata un'assegnazione di ruolo all'interno dell'API Gestione, la funzione definita dall'utente non può accedere per eseguire alcuna azione, come ad esempio l'invio delle notifiche, il recupero di metadati e l'impostazione dei valori calcolati all'interno della topologia.

Controllare se esiste un'assegnazione di ruolo per la funzione definita dall'utente tramite l'API Gestione:

```URL
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Valore del parametro | Sostituire con |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | ID della funzione definita dall'utente per la quale recuperare le assegnazioni di ruolo|

Se non esiste alcuna assegnazione di ruolo, vedere [Come creare funzioni definite dall'utente in Gemelli digitali di Azure](./how-to-user-defined-functions.md).

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Controllare se il matcher funziona per i dati di telemetria di un sensore

Con la chiamata seguente all'API Gestione delle istanze di Gemelli digitali di Azure, è possibile determinare se un dato matcher è valido per il sensore specificato.

```URL
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parametro | Sostituire con |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | ID del matcher da valutare |
| *YOUR_SENSOR_IDENTIFIER* | ID del sensore da valutare |

Risposta:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Controllare ciò che viene attivato da un sensore

Con la chiamata seguente alle API Gestione di Gemelli digitali di Azure, è possibile individuare gli identificatori delle funzioni definite dall'utente attivate dai dati di telemetria in ingresso del sensore specificato:

```URL
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parametro | Sostituire con |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | L'ID del sensore a cui inviare i dati di telemetria |

Risposta:

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>Problema relativo alla ricezione delle notifiche

Quando non si ricevono notifiche dalla funzione definita dall'utente attivata, assicurarsi che il parametro del tipo di oggetto della topologia corrisponda al tipo di identificatore in uso.

Esempio **non corretto**:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Questo scenario si verifica perché l'identificatore usato fa riferimento a un sensore, mentre il tipo di oggetto della topologia specificato è `Space`.

Esempio **corretto**:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

Il modo più semplice per non incorrere in questo problema è usare il metodo `Notify` sull'oggetto metadati.

Esempio:

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>Eccezioni comuni di diagnostica

Se si abilitano le impostazioni di diagnostica, è possibile riscontrare queste eccezioni comuni:

1. **Limitazione delle richieste**: se la funzione definita dall'utente supera i limiti della velocità di esecuzione descritti nell'articolo [Limiti del servizio](./concepts-service-limits.md), il numero di richiesta verrà limitato. Non verranno eseguite correttamente altre operazioni fino alla scadenza della limitazione delle richieste.

1. **Impossibile trovare dati**: se la funzione definita dall'utente prova ad accedere a metadati non esistenti, l'operazione avrà esito negativo.

1. **Non autorizzato**: se per la funzione definita dall'utente non è impostata un'assegnazione di ruolo o se tale funzione non ha autorizzazioni sufficienti per accedere a determinati metadati della topologia, l'operazione avrà esito negativo.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come abilitare il [monitoraggio e i log](./how-to-configure-monitoring.md) in Gemelli digitali di Azure

- Read the [Overview of Azure Activity log](../azure-monitor/platform/activity-logs-overview.md) article for more Azure logging options.
