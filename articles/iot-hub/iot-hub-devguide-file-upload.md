---
title: Informazioni sul caricamento di file dell'hub IoT di Azure | Microsoft Docs
description: "Guida per gli sviluppatori: usare la funzionalità di caricamento di file dell'hub IoT per gestire il caricamento di file da un dispositivo a un contenitore BLOB di archiviazione di Azure."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a0427925-3e40-4fcd-96c1-2a31d1ddc14b
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 7bf1ba333f36dcfa8959320566bcb771f37cfe22
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="upload-files-with-iot-hub"></a>Caricare file con l'hub IoT

Come descritto nell'articolo [IoT Hub endpoints][lnk-endpoints] (Endpoint dell'hub IoT), un dispositivo può avviare il caricamento di un file inviando una notifica tramite un endpoint per il dispositivo (**/devices/{deviceId}/files**). Quando un dispositivo notifica all'hub IoT il completamento di un caricamento, l'hub IoT invia un messaggio di notifica per il caricamento del file tramite l'endpoint per il servizio (**/messages/servicebound/filenotifications**).

I messaggi non vengono negoziati tramite l'hub IoT, che funge invece da strumento di recapito per un account di archiviazione di Azure associato. Un dispositivo richiede dall'hub IoT un token di archiviazione specifico del file che intende caricare. Il dispositivo usa l'URI di firma di accesso condiviso per caricare il file nella risorsa di archiviazione e al termine del caricamento invia una notifica di completamento all'hub IoT. L'hub IoT verifica che il completamento del file sia stato completato e quindi aggiunge un messaggio di notifica di caricamento file all'endpoint per il servizio per le notifiche relative ai file.

Prima di caricare un file in un hub IoT da un dispositivo, è necessario configurare l'hub [associando un account di archiviazione di Azure][lnk-associate-storage] all'hub.

Il dispositivo può quindi [inizializzare un'operazione di caricamento][lnk-initialize] e quindi [Inviare una notifica all'hub IoT][lnk-notify] al termine del caricamento. Facoltativamente, quando un dispositivo comunica all'hub IoT che il caricamento è completo, il servizio può generare un [messaggio di notifica][lnk-service-notification].

### <a name="when-to-use"></a>Quando usare le autorizzazioni

Usare il caricamento di file per inviare file multimediali e batch di telemetria di grandi dimensioni caricati da dispositivi con connessione intermittente o compressi per risparmiare la larghezza di banda.

Vedere [Device-to-cloud communication guidance][lnk-d2c-guidance] (Indicazioni sulla comunicazione da dispositivo a cloud) in caso di dubbi tra l'uso delle proprietà indicate, dei messaggi da dispositivo a cloud o del caricamento di file.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associare un account di archiviazione di Azure all'hub IoT

Per usare la funzionalità di caricamento file, è prima necessario collegare un account di archiviazione di Azure all'hub IoT. Questa attività può essere completata nel [portale di Azure][lnk-management-portal] oppure a livello di codice con le [API REST del provider di risorse dell'hub IoT][lnk-resource-provider-apis]. Dopo l'associazione di un account di archiviazione di Azure all'hub IoT, quando un dispositivo avvia una richiesta di caricamento file il servizio restituisce al dispositivo un URI di firma di accesso condiviso.

> [!NOTE]
> Gli [Azure IoT SDKs][lnk-sdks] (SDK di IoT di Azure) gestiscono automaticamente il recupero dell'URI di firma di accesso condiviso, il caricamento del file e la notifica all'hub IoT del completamento del caricamento.


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
    "hostName": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Obsoleto: inizializzare un caricamento di file con un'operazione GET

> [!NOTE]
> In questa sezione viene descritta la funzionalità obsoleta per indicazioni su come ricevere un URI di firma di accesso condiviso dall'hub IoT. Usare il metodo POST descritto in precedenza.

Per supportare il caricamento di file, l'hub IoT usa due endpoint REST: uno per ottenere l'URI di firma di accesso condiviso per l'archiviazione e uno per la notifica all'hub IoT del completamento del caricamento. Il dispositivo avvia il processo di caricamento file inviato una richiesta GET all'hub IoT in `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. L'hub IoT restituisce:

* Un URI SAS specifico per il file da caricare.
* Un ID di correlazione da utilizzare dopo il completamento del caricamento.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Notifica all'hub IoT del completamento del caricamento di un file

Il dispositivo è responsabile del caricamento del file nella risorsa di archiviazione con Azure Storage SDK. Al termine del caricamento, il dispositivo invia una richiesta POST a `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` con il corpo JSON seguente:

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

Facoltativamente, quando un dispositivo notifica all'hub IoT il completamento di un caricamento, l'hub IoT genera un messaggio di notifica contenente il nome e la posizione di archiviazione del file.

Come illustrato nella sezione [Endpoint][lnk-endpoints], , l'hub IoT recapita le notifiche di caricamento file sotto forma di messaggi tramite un endpoint per servizio (**/messages/servicebound/fileuploadnotifications**). La semantica di ricezione per le notifiche di caricamento file è uguale a quella dei messaggi da cloud a dispositivo e ha lo stesso [ciclo di vita dei messaggi][lnk-lifecycle]. Ogni messaggio recuperato dall'endpoint delle notifiche di caricamento file è un record JSON con le proprietà seguenti.

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

Ogni hub IoT espone le opzioni di configurazione seguenti per le notifiche di caricamento file.

| Proprietà | DESCRIZIONE | Intervallo e valore predefinito |
| --- | --- | --- |
| **enableFileUploadNotifications** |Controlla se verranno scritte notifiche di caricamento file nell'endpoint per le notifiche relative ai file. |Valore booleano. Predefinito: True. |
| **fileNotifications.ttlAsIso8601** |Durata (TTL) predefinita per le notifiche di caricamento file. |Intervallo ISO_8601 fino a 48 ore (minimo 1 minuto). Predefinito: 1 ora. |
| **fileNotifications.lockDuration** |Durata del blocco per la coda delle notifiche di caricamento file. |Da 5 a 300 secondi (minimo 5 secondi). Predefinito: 60 secondi. |
| **fileNotifications.maxDeliveryCount** |Numero massimo di recapiti per la coda delle notifiche di caricamento file. |Da 1 a 100. Predefinito: 100. |

## <a name="additional-reference-material"></a>Materiale di riferimento

Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint dell'hub IoT][lnk-endpoints] illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione.
* [Quote e limitazioni][lnk-quotas] descrive le quote e i comportamenti di limitazione applicabili al servizio hub IoT.
* [Azure IoT SDK per dispositivi e servizi][lnk-sdks] elenca gli SDK nei diversi linguaggi che è possibile usare quando si sviluppano app per dispositivi e servizi che interagiscono con l'hub IoT.
* [Linguaggio di query dell'hub IoT][lnk-query] descrive il linguaggio di query che è possibile usare per recuperare informazioni dall'hub IoT sui dispositivi gemelli e sui processi.
* [Supporto di MQTT nell'hub IoT][lnk-devguide-mqtt] offre altre informazioni sul supporto dell'hub IoT per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come caricare i file dai dispositivi con l'hub IoT. Altri argomenti di interesse reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Gestire le identità dei dispositivi nell'hub IoT][lnk-devguide-identities]
* [Controllare l'accesso all'hub IoT][lnk-devguide-security]
* [Use device twins to synchronize state and configurations][lnk-devguide-device-twins] (Usare dispositivi gemelli per sincronizzare lo stato e le configurazioni)
* [Richiamare un metodo diretto in un dispositivo][lnk-devguide-directmethods]
* [Pianificare processi in più dispositivi][lnk-devguide-jobs]

Per provare alcuni dei concetti descritti in questo articolo, vedere l'esercitazione sull'hub IoT seguente:

* [Come caricare file dai dispositivi al cloud con l'hub IoT][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
