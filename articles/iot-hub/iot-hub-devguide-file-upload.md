---
title: Informazioni sul caricamento di file dell'hub IoT di Azure | Microsoft Docs
description: "Guida per gli sviluppatori: usare la funzionalità di caricamento di file dell'hub IoT per gestire il caricamento di file da un dispositivo a un contenitore BLOB di archiviazione di Azure."
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: 217d348eacab30b90e06fe805d9cdb0cf32349ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60950381"
---
# <a name="upload-files-with-iot-hub"></a>Caricare file con l'hub IoT

Come descritto nell'articolo [IoT Hub endpoints](iot-hub-devguide-endpoints.md) (Endpoint dell'hub IoT), un dispositivo può avviare il caricamento di file inviando una notifica tramite un endpoint per il dispositivo (**/devices/{deviceId}/files**). Quando un dispositivo notifica all'hub IoT il completamento di un caricamento, l'hub IoT invia un messaggio di notifica per il caricamento del file tramite l'endpoint per il servizio (**/messages/servicebound/filenotifications**).

I messaggi non vengono negoziati tramite l'hub IoT, che funge invece da strumento di recapito per un account di archiviazione di Azure associato. Un dispositivo richiede dall'hub IoT un token di archiviazione specifico del file che intende caricare. Il dispositivo usa l'URI di firma di accesso condiviso per caricare il file nella risorsa di archiviazione e al termine del caricamento invia una notifica di completamento all'hub IoT. L'hub IoT verifica che il completamento del file sia stato completato e quindi aggiunge un messaggio di notifica di caricamento file all'endpoint per il servizio per le notifiche relative ai file.

Prima di caricare un file in un hub IoT da un dispositivo, è necessario configurare l'hub [associando un account di archiviazione di Azure](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub) all'hub.

Il dispositivo può quindi [inizializzare un'operazione di caricamento](iot-hub-devguide-file-upload.md#initialize-a-file-upload) e quindi [Inviare una notifica all'hub IoT](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) al termine del caricamento. Facoltativamente, quando un dispositivo comunica all'hub IoT che il caricamento è completo, il servizio può generare un [messaggio di notifica](iot-hub-devguide-file-upload.md#file-upload-notifications).

### <a name="when-to-use"></a>Quando usare le autorizzazioni

Usare il caricamento di file per inviare file multimediali e batch di telemetria di grandi dimensioni caricati da dispositivi con connessione intermittente o compressi per risparmiare la larghezza di banda.

Vedere [Device-to-cloud communication guidance](iot-hub-devguide-d2c-guidance.md) (Indicazioni sulla comunicazione da dispositivo a cloud) in caso di dubbi tra l'uso delle proprietà indicate, dei messaggi da dispositivo a cloud o del caricamento di file.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associare un account di archiviazione di Azure all'hub IoT

Per usare la funzionalità di caricamento file, è prima necessario collegare un account di archiviazione di Azure all'hub IoT. Questa attività può essere completata nel portale di Azure oppure a livello di programmazione con le [API REST del provider di risorse dell'hub IoT](/rest/api/iothub/iothubresource). Dopo avere associato un account di archiviazione di Azure all'hub IoT, quando un dispositivo avvia una richiesta di caricamento file, il servizio restituisce al dispositivo un URI di firma di accesso condiviso.

Le guide procedurali per [Caricare i file dal dispositivo nel cloud con un hub IoT](iot-hub-csharp-csharp-file-upload.md) includono una procedura completa del processo di caricamento dei file. Queste guide procedurali illustrano come usare il portale di Azure per associare un account di archiviazione a un hub IoT.

> [!NOTE]
> Gli [SDK di IoT di Azure](iot-hub-devguide-sdks.md) gestiscono automaticamente il recupero dell'URI di firma di accesso condiviso, il caricamento del file e la notifica all'hub IoT del completamento del caricamento.

## <a name="initialize-a-file-upload"></a>Inizializzazione del caricamento di un file
L'hub IoT dispone di un endpoint dedicato in modo specifico ai dispositivi che consente di richiedere un URI di firma di accesso condiviso con cui l'archiviazione possa caricare un file. Per avviare il processo di caricamento del file, il dispositivo invia una richiesta POST a `{iot hub}.azure-devices.net/devices/{deviceId}/files` con il corpo JSON seguente:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

L'hub IoT restituisce i dati seguenti, usati dal dispositivo per caricare il file:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Obsoleto: inizializzare un caricamento di file con un'operazione GET

> [!NOTE]
> In questa sezione viene descritta la funzionalità obsoleta per indicazioni su come ricevere un URI di firma di accesso condiviso dall'hub IoT. Usare il metodo POST descritto in precedenza.

Per supportare il caricamento di file, l'hub IoT usa due endpoint REST: uno per ottenere l'URI di firma di accesso condiviso per l'archiviazione e uno per la notifica all'hub IoT del completamento del caricamento. Il dispositivo avvia il processo di caricamento del file inviando una richiesta GET all'hub IoT in `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. L'hub IoT restituisce:

* Un URI SAS specifico per il file da caricare.

* Un ID di correlazione da utilizzare dopo il completamento del caricamento.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Notifica all'hub IoT del completamento del caricamento di un file

Il dispositivo carica i file nella risorsa di archiviazione usando gli Azure Storage SDK. Al termine del caricamento, il dispositivo invia una richiesta POST a `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` con il corpo JSON seguente:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Il valore di `isSuccess` è un valore booleano che indica se il file è stato caricato correttamente. Il codice di stato per `statusCode` è lo stato per il caricamento del file nell'archiviazione e `statusDescription` corrisponde a `statusCode`.

## <a name="reference-topics"></a>Argomenti di riferimento:

Negli argomenti di riferimento seguenti vengono offerte altre informazioni sul caricamento dei file da un dispositivo.

## <a name="file-upload-notifications"></a>Notifiche di caricamento file

Facoltativamente, quando un dispositivo comunica all'hub IoT che il caricamento è completato, l'hub IoT genera un messaggio di notifica. Questo messaggio contiene il nome e il percorso di archiviazione del file.

Come illustrato nella sezione [Endpoint](iot-hub-devguide-endpoints.md), l'hub IoT recapita le notifiche di caricamento file sotto forma di messaggi tramite un endpoint per servizio (**/messages/servicebound/fileuploadnotifications**). La semantica di ricezione per le notifiche di caricamento file è uguale a quella dei messaggi da cloud a dispositivo e ha lo stesso [ciclo di vita dei messaggi](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle). Ogni messaggio recuperato dall'endpoint delle notifiche di caricamento file è un record JSON con le proprietà seguenti.

| Proprietà | DESCRIZIONE |
| --- | --- |
| EnqueuedTimeUtc |Timestamp che indica quando è stata creata la notifica. |
| deviceId |**DeviceId** del dispositivo che ha caricato il file. |
| BlobUri |URI del file caricato. |
| BlobName |Nome del file caricato. |
| LastUpdatedTime |Timestamp che indica quando è stato eseguito l'ultimo aggiornamento del file. |
| BlobSizeInBytes |Dimensioni del file caricato. |

**Esempio**. Questo esempio illustra il corpo di un messaggio di notifica di caricamento di un messaggio.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Opzioni di configurazione per le notifiche di caricamento file

Ogni hub IoT espone le opzioni di configurazione seguenti per le notifiche di caricamento file:

| Proprietà | DESCRIZIONE | Intervallo e valore predefinito |
| --- | --- | --- |
| **enableFileUploadNotifications** |Controlla se verranno scritte notifiche di caricamento file nell'endpoint per le notifiche relative ai file. |Valore booleano. Predefinito: vero. |
| **fileNotifications.ttlAsIso8601** |Durata (TTL) predefinita per le notifiche di caricamento file. |Intervallo ISO_8601 fino a 48 ore (minimo 1 minuto). Predefinito: 1 ora |
| **fileNotifications.lockDuration** |Durata del blocco per la coda delle notifiche di caricamento file. |Da 5 a 300 secondi (minimo 5 secondi). Predefinito: 60 secondi. |
| **fileNotifications.maxDeliveryCount** |Numero massimo di recapiti per la coda delle notifiche di caricamento file. |Da 1 a 100. Predefinito: 100. |

## <a name="additional-reference-material"></a>Materiale di riferimento

Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint dell'hub IoT](iot-hub-devguide-endpoints.md) illustra i diversi endpoint dell'hub IoT per le operazioni della fase di esecuzione e di gestione.

* [Quote e limitazioni](iot-hub-devguide-quotas-throttling.md) descrive le quote e i comportamenti di limitazione applicabili al servizio hub IoT.

* [Azure IoT SDK per dispositivi e servizi](iot-hub-devguide-sdks.md) elenca gli SDK nei diversi linguaggi che è possibile usare quando si sviluppano app per dispositivi e servizi che interagiscono con l'hub IoT.

* [Linguaggio di query dell'hub IoT](iot-hub-devguide-query-language.md) descrive il linguaggio di query che è possibile usare per recuperare informazioni dall'hub IoT sui dispositivi gemelli e sui processi.

* [Supporto di MQTT nell'hub IoT](iot-hub-mqtt-support.md) offre altre informazioni sul supporto dell'hub IoT per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi

Si è ora appreso come caricare i file dai dispositivi usando l'hub IoT. Altri argomenti di interesse reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Gestire le identità dei dispositivi nell'hub IoT](iot-hub-devguide-identity-registry.md)

* [Controllare l'accesso all'hub IoT](iot-hub-devguide-security.md)

* [Use device twins to synchronize state and configurations](iot-hub-devguide-device-twins.md) (Usare dispositivi gemelli per sincronizzare lo stato e le configurazioni)

* [Richiamare un metodo diretto in un dispositivo](iot-hub-devguide-direct-methods.md)

* [Pianificare processi in più dispositivi](iot-hub-devguide-jobs.md)

Per provare alcuni dei concetti descritti in questo articolo, vedere l'esercitazione sull'hub IoT seguente:

* [Come caricare file dai dispositivi al cloud con l'hub IoT](iot-hub-csharp-csharp-file-upload.md)