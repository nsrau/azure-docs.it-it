---
title: Come eseguire il debug di funzioni definite dall'utente in Gemelli digitali di Azure | Microsoft Docs
description: Linee guida su come eseguire il debug di funzioni definite dall'utente in Gemelli digitali di Azure.
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: stefanmsft
ms.custom: seodec18
ms.openlocfilehash: e373e7c3ca83a0200cd1b6b945c5e4cb43b77a51
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974863"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Come eseguire il debug di funzioni definite dall'utente in Gemelli digitali di Azure

Questo articolo fornisce un riepilogo su come eseguire la diagnosi delle funzioni definite dall'utente e quindi identifica alcuni degli scenari più comuni che si possono riscontrare quando si usano tali funzioni.

>[!TIP]
> Vedere [Come configurare il monitoraggio in Gemelli digitali di Azure](./how-to-configure-monitoring.md) per altre informazioni su come configurare gli strumenti di debug in Gemelli digitali di Azure mediante log attività, log di diagnostica e Monitoraggio di Azure.

## <a name="debug-issues"></a>Eseguire il debug dei problemi

Imparare a eseguire la diagnosi degli eventuali problemi che si verificano all'interno dell'istanza di Gemelli digitali di Azure aiuterà l'utente a identificare in modo efficace il problema, la causa di tale problema e una possibile soluzione.

### <a name="enable-log-analytics-for-your-instance"></a>Abilitare Log Analytics per l'istanza in uso

I log e le metriche per l'istanza di Gemelli digitali di Azure in uso sono esposti tramite Monitoraggio di Azure. Nella documentazione seguente si presuppone che sia stata creata un'area di lavoro di [Azure Log Analytics](../azure-monitor/log-query/log-query-overview.md) tramite il [portale di Azure](../azure-monitor/learn/quick-create-workspace.md), l'[interfaccia della riga di comando di Azure](../azure-monitor/learn/quick-create-workspace-cli.md) o [PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

> [!NOTE]
> Quando si inviano eventi ad Azure Log Analytics per la prima volta, può verificarsi un ritardo di 5 minuti.

Per configurare il monitoraggio e la registrazione delle risorse di Gemelli digitali di Azure, vedere [Come configurare il monitoraggio in Gemelli digitali di Azure](./how-to-configure-monitoring.md).

Leggere l'articolo [Raccogliere e usare i dati dei log dalle risorse di Azure](../azure-monitor/platform/diagnostic-logs-overview.md) per una panoramica completa delle impostazioni dei log di diagnostica per l'istanza di Gemelli digitali di Azure tramite il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell.

>[!IMPORTANT]
> Assicurarsi di selezionare tutte le categorie di log, le metriche e l'area di lavoro di Azure Log Analytics.

### <a name="trace-sensor-telemetry"></a>Tracciare i dati di telemetria del sensore

Assicurarsi che le impostazioni di diagnostica siano abilitate nell'istanza di Gemelli digitali di Azure, che tutte le categorie di log siano selezionate e che i log vengano inviati ad Azure Log Analytics.

Per associare un messaggio di telemetria del sensore ai rispettivi log, è possibile specificare un ID di correlazione per i dati dell'evento inviati. A tale scopo, impostare la proprietà `x-ms-client-request-id` su un GUID.

Dopo l'invio dei dati di telemetria, aprire Azure Log Analytics per eseguire una query dei log usando l'ID di correlazione impostato:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Valore della query | Sostituire con |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | ID di correlazione specificato per i dati dell'evento |

Se la funzione definita dall'utente viene registrata, questi log verranno visualizzati nell'istanza di Azure Log Analytics con la categoria `UserDefinedFunction`. Per recuperarli, immettere la seguente condizione di query in Azure Log Analytics:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Per altre informazioni sulle operazioni di query avanzate, vedere [Introduzione alle query in Log Analytics](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Identificare i problemi più comuni

La diagnosi e l'identificazione dei problemi più comuni sono importanti per la risoluzione dei problemi della soluzione. Di seguito viene fornito un riepilogo di alcuni problemi comuni riscontrati durante lo sviluppo di funzioni definite dall'utente.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="ensure-a-role-assignment-was-created"></a>Verificare che sia stata creata un'assegnazione di ruolo

Se non è stata creata un'assegnazione di ruolo all'interno dell'API Gestione, la funzione definita dall'utente non potrà accedere per eseguire le azioni, ad esempio l'invio delle notifiche, il recupero dei metadati e l'impostazione dei valori calcolati all'interno della topologia.

Controllare se esiste un'assegnazione di ruolo per la funzione definita dall'utente tramite l'API Gestione:

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Valore del parametro | Sostituire con |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | ID della funzione definita dall'utente per la quale recuperare le assegnazioni di ruolo|

Se non esiste alcuna assegnazione di ruolo, vedere [Come creare funzioni definite dall'utente in Gemelli digitali di Azure](./how-to-user-defined-functions.md).

### <a name="check-if-the-matcher-will-work-for-a-sensors-telemetry"></a>Controllare se il matcher funzionerà per i dati di telemetria di un sensore

Con la chiamata seguente all'API Gestione delle istanze di Gemelli digitali di Azure, sarà possibile determinare se un dato matcher è valido per il sensore specificato.

```plaintext
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

### <a name="check-what-a-sensor-will-trigger"></a>Controllare i dati che attiverà un sensore

Con la chiamata seguente all'API Gestione delle istanze di Gemelli digitali di Azure, sarà possibile determinare gli identificatori delle funzioni definite dall'utente che verranno attivate dai dati di telemetria in ingresso del sensore specificato:

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parametro | Sostituire con |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | ID del sensore che invierà i dati di telemetria |

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

Quando non si ricevono notifiche dall'interno della funzione definita dall'utente attivata, assicurarsi che il parametro del tipo di oggetto della topologia corrisponda al tipo dell'identificatore in uso.

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

1. **Limitazione delle richieste**: se la funzione definita dall'utente supera i limiti della velocità di esecuzione descritti nell'articolo [Limiti del servizio](./concepts-service-limits.md), il numero di richiesta verrà limitato. La limitazione delle richieste implica che non verranno eseguite altre operazioni fino alla scadenza dei limiti.

1. **Impossibile trovare dati**: se la funzione definita dall'utente prova ad accedere a metadati che non esistono, l'operazione avrà esito negativo.

1. **Non autorizzato**: se per la funzione definita dall'utente non è impostata un'assegnazione di ruolo o se tale funzione non ha autorizzazioni sufficienti per accedere a determinati metadati della topologia, l'operazione avrà esito negativo.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come abilitare il [monitoraggio e i log](../azure-monitor/platform/activity-logs-overview.md) in Gemelli digitali di Azure
