---
title: Informazioni sui processi hub IoT di Azure | Documentazione Microsoft
description: "Guida per sviluppatori - Pianificazione di processi da eseguire in più dispositivi connessi all&quot;hub di IoT. I processi possono aggiornare i tag e le proprietà desiderate e richiamare metodi diretti su più dispositivi."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: fe78458f-4f14-4358-ac83-4f7bd14ee8da
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: 8245c9d86d7a37bfb12c06b1cb2cbe9dae01d653
ms.openlocfilehash: c919105d2047e2a931433d2f30a7fa41192d7908


---
# <a name="schedule-jobs-on-multiple-devices"></a>Pianificare processi in più dispositivi
## <a name="overview"></a>Panoramica
Come descritto in articoli precedenti, l'hub IoT di Azure consente un numero di blocchi predefiniti ([tag e proprietà di dispositivi gemelli][lnk-twin-devguide] e [metodi diretti][lnk-dev-methods]).  In genere, le app back-end consentono agli amministratori dei dispositivi e agli operatori di aggiornare i dispositivi IoT in blocco e a un'ora pianificata e di interagirvi.  I processi incapsulano l'esecuzione di aggiornamenti dei dispositivi gemelli e i metodi diretti rispetto a un set di dispositivi a un'ora pianificata.  Ad esempio, un operatore userà un'app back-end avviata e terrà traccia di un processo per riavviare un set di dispositivi al terzo piano dell'edificio 43 in un orario opportuno per non interrompere le attività dell'edificio.

### <a name="when-to-use"></a>Quando usare le autorizzazioni
È consigliabile usare processi quando il back-end di una soluzione deve pianificare e tenere traccia dell'avanzamento di una qualsiasi delle attività seguenti in un set di dispositivi:

* Aggiornare le proprietà desiderate
* Aggiornare i tag
* Richiamare metodi diretti

## <a name="job-lifecycle"></a>Ciclo di vita dei processi
I processi vengono avviati dal back-end della soluzione e mantenuti dall'hub IoT.  È possibile avviare un processo tramite un URI del servizio (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`) ed eseguire query riguardo allo stato di avanzamento in un processo in esecuzione tramite un URI del servizio (`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`).  Dopo l'avvio del processo, l'esecuzione di query relative ai processi consente all'app back-end aggiornare lo stato dei processi in esecuzione.

> [!NOTE]
> Quando si avvia un processo, i valori e i nomi di proprietà possono contenere solo caratteri alfanumerici US-ASCII stampabili, ad eccezione dei seguenti: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

## <a name="reference-topics"></a>Argomenti di riferimento:
Gli argomenti di riferimento seguenti offrono altre informazioni sull'uso dei processi.

## <a name="jobs-to-execute-direct-methods"></a>Processi per eseguire metodi diretti
Di seguito sono riportati i dettagli di una richiesta HTTP 1.1 per eseguire un [metodo diretto][lnk-dev-methods] in un set di dispositivi tramite un processo:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-11-14

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            timeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }
    ```

## <a name="jobs-to-update-device-twin-properties"></a>Processi per aggiornare le proprietà dei dispositivi gemelli
Di seguito sono riportati i dettagli di una richiesta HTTP 1.1 per aggiornare le proprietà dei dispositivi gemelli tramite un processo:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-11-14
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }
    ```

## <a name="querying-for-progress-on-jobs"></a>Esecuzione di query sull'avanzamento dei processi
Di seguito sono riportati i dettagli di una richiesta HTTP 1.1 per [eseguire query sui processi][lnk-query]:

    ```
    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>
    ```

La risposta fornisce continuationToken.  

## <a name="jobs-properties"></a>Proprietà dei processi
Di seguito è riportato un elenco di proprietà e corrispondenti descrizioni che è possibile usare quando si eseguono query sui processi o sui relativi risultati.

| Proprietà | Description |
| --- | --- |
| **jobId** |ID fornito dall'applicazione per il processo. |
| **startTime** |Ora di inizio fornita dall'applicazione (ISO 8601) per il processo. |
| **endTime** |Data fornita dall'hub IoT (ISO 8601) per il completamento del processo. È valida solo quando il processo raggiunge lo stato di completamento. |
| **type** |Tipi di processi: |
| **scheduledUpdateTwin**: un processo usato per aggiornare un set di proprietà o tag desiderati. | |
| **scheduledDeviceMethod**: un processo usato per richiamare un metodo di dispositivo in un set di dispositivi gemelli. | |
| **Stato** |Stato corrente del processo. Valori possibili per lo stato: |
| **pending**: processo pianificato e in attesa del prelievo da parte del servizio del processo. | |
| **scheduled**: processo pianificato per un'ora futura. | |
| **running**: processo attualmente attivo. | |
| **cancelled**: processo annullato. | |
| **failed**: processo non riuscito. | |
| **completed**: processo completato. | |
| **deviceJobStatistics** |Statistiche sull'esecuzione del processo. |

Proprietà **deviceJobStatistics**.

| Proprietà | Description |
| --- | --- |
| **deviceJobStatistics.deviceCount** |Numero di dispositivi nel processo. |
| **deviceJobStatistics.failedCount** |Numero di dispositivi in cui il processo non è riuscito. |
| **deviceJobStatistics.succeededCount** |Numero di dispositivi in cui il processo è riuscito. |
| **deviceJobStatistics.runningCount** |Numero di dispositivi in cui il processo è in esecuzione. |
| **deviceJobStatistics.pendingCount** |Numero di dispositivi in cui il processo è in attesa. |

### <a name="additional-reference-material"></a>Materiale di riferimento
Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per sviluppatori dell'hub IoT:

* [Endpoint dell'hub IoT][lnk-endpoints] illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione.
* [Quote e limitazioni][lnk-quotas] descrive le quote applicabili al servizio Hub IoT e il comportamento di limitazione previsto quando si usa il servizio.
* [Azure IoT SDK per dispositivi e servizi][lnk-sdks] elenca gli SDK nei diversi linguaggi da usare quando si sviluppano app per dispositivi e servizi che interagiscono con l'hub IoT.
* [Linguaggio di query per dispositivi gemelli e processi][lnk-query] illustra il linguaggio di query dell'hub IoT che è possibile usare per recuperare informazioni dall'hub IoT sui dispositivi gemelli e sui processi.
* [Supporto di MQTT nell'hub IoT][lnk-devguide-mqtt] offre altre informazioni sul supporto dell'hub IoT per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi
Per provare alcuni dei concetti descritti in questo articolo, può essere utile l'esercitazione seguente sull'hub IoT:

* [Pianificare e trasmettere processi][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md



<!--HONumber=Dec16_HO1-->


