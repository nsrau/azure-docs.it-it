---
title: File di inclusione
description: File di inclusione
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147746"
---
### <a name="code-walkthrough"></a>Procedura dettagliata per il codice

Questa sezione descrive alcune delle parti principali del codice di esempio e illustra come queste interagiscono con l'acceleratore di soluzioni Monitoraggio remoto.

Il frammento di codice seguente illustra come vengono definite le proprietà segnalate che descrivono le funzionalità del dispositivo. Queste proprietà includono:

- La posizione del dispositivo per consentire all'acceleratore di soluzioni di aggiungere il dispositivo alla mappa.
- La versione corrente del firmware.
- L'elenco dei metodi supportati dal dispositivo.
- Lo schema dei messaggi di telemetria inviati dal dispositivo.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

L'esempio include una funzione **serializeToJson** che serializza questa struttura dei dati usando la libreria Parson.

L'esempio include diverse funzioni di callback che stampano informazioni nella console quando il client interagisce con l'acceleratore di soluzioni:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

Il frammento di codice seguente illustra la funzione **device_method_callback**. Questa funzione determina l'azione da intraprendere quando viene ricevuta una chiamata al metodo dall'acceleratore di soluzioni. La funzione riceve un riferimento alla struttura dei dati **Chiller** nel parametro **userContextCallback**. Il valore di **userContextCallback** è impostato quando la funzione di callback è configurata nella funzione **main**:

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Quando l'acceleratore di soluzioni chiama il metodo di aggiornamento del firmware, l'esempio deserializza il payload JSON e avvia un thread in background per completare il processo di aggiornamento. Il frammento di codice seguente illustra l'elemento **do_firmware_update** che viene eseguito sul thread:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

Il frammento di codice seguente illustra come il client invia un messaggio di telemetria all'acceleratore di soluzioni. Le proprietà del messaggio includono lo schema del messaggio per consentire all'acceleratore di soluzioni di visualizzare i dati di telemetria nel dashboard:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

La funzione **main** nell'esempio:

- Inizializza e arresta il sottosistema SDK.
- Inizializza la struttura dei dati **Chiller**.
- Invia le proprietà segnalate all'acceleratore di soluzioni.
- Configura la funzione di callback del metodo del dispositivo.
- Invia i valori dei dati di telemetria simulati all'acceleratore di soluzioni.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
