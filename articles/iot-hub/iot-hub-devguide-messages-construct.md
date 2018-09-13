---
title: Informazioni sul formato della messaggistica di hub IoT di Azure | Microsoft Docs
description: 'Guida per sviluppatori: descrive il formato e il contenuto previsto dei messaggi hub IoT.'
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 7c08848698f07d64bbbff429682c18525659f7bf
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286518"
---
# <a name="create-and-read-iot-hub-messages"></a>Creare e leggere messaggi dell'hub IoT

Per supportare l'interoperabilità senza problemi tra i protocolli, l'hub IoT definisce un formato di messaggio comune per tutti i protocolli del dispositivo. Il formato del messaggio viene usato per i messaggi [da dispositivo a cloud][lnk-d2c] e [da cloud a dispositivo][lnk-c2d]. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Un [messaggio dell'hub IoT][lnk-messaging] è costituito da:

* Un set di predeterminato di *proprietà di sistema* come indicato di seguito.
* Un set di *proprietà dell'applicazione*. Dizionario di proprietà stringa che l'applicazione può definire e a cui può accedere senza dover deserializzare il corpo del messaggio. Queste proprietà non vengono mai modificate dall'hub IoT.
* Corpo binario opaco.

I valori e i nomi di proprietà possono contenere solo caratteri ASCII alfanumerici e ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}``` quando:  

* Si inviano messaggi da dispositivo a cloud usando il protocollo HTTPS.
* Invio di messaggi da cloud a dispositivo.

Per altre informazioni sulla codifica e la decodifica dei messaggi inviati tramite diversi protocolli, vedere [Azure IoT SDK][lnk-sdks].

La tabella seguente riporta il set delle proprietà di sistema nei messaggi dell'hub IoT.

| Proprietà | DESCRIZIONE | È possibile impostare l'utente? |
| --- | --- | --- |
| MessageId |Un identificatore configurabile dall'utente per il messaggio, usato per i modelli di richiesta-risposta. Formato: stringa con distinzione tra maiuscole e minuscole (con lunghezza massima di 128 caratteri) di caratteri alfanumerici ASCII a 7 bit + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | Yes |
| Numero di sequenza |Numero, univoco per ogni dispositivo-coda, assegnato dall'hub IoT a ogni messaggio da cloud a dispositivo. | No per i messaggi C2D, sì altrimenti. |
| A |Destinazione specificata nei messaggi [da cloud a dispositivo][lnk-c2d]. | No per i messaggi C2D, sì altrimenti. |
| ExpiryTimeUtc |Data e ora della scadenza del messaggio. | Yes |
| EnqueuedTime |Date e ora del messaggio [da cloud a dispositivo][lnk-c2d] ricevute dall'hub IoT. | No per i messaggi C2D, sì altrimenti. |
| CorrelationId |Proprietà stringa in un messaggio di risposta che contiene in genere il valore MessageId della richiesta nei modelli richiesta-risposta. | Yes |
| UserId |ID usato per specificare l'origine dei messaggi. Quando i messaggi vengono generati dall'hub IoT, viene impostata su `{iot hub name}`. | No  |
| Ack |Generatore di messaggi con commenti. Questa proprietà viene usata nei messaggi da cloud a dispositivo per richiedere all'hub IoT di generare messaggi con commenti come risultato dell'utilizzo del messaggio da parte del dispositivo. I valori possibili sono i seguenti: **none** (predefinito): non viene generato alcun messaggio con commenti, **positive**: si riceve un messaggio con commenti se il messaggio è stato completato, **negative**: si riceve un messaggio con commenti se il messaggio è scaduto o se è stato raggiunto il numero massimo di recapiti senza il completamento da parte del dispositivo, **full**: sia per esito positivo che negativo. Per altre informazioni, vedere [Commenti sui messaggi][lnk-feedback]. | Yes |
| ConnectionDeviceId |ID impostato dall'hub IoT sui messaggi da dispositivo a cloud. Contiene il valore **deviceId** del dispositivo che ha inviato il messaggio. | No per i messaggi D2C, sì altrimenti. |
| ConnectionDeviceGenerationId |ID impostato dall'hub IoT sui messaggi da dispositivo a cloud. Contiene il valore **generationId** (come indicato in [Proprietà delle identità dei dispositivi][lnk-device-properties]) del dispositivo che ha inviato il messaggio. | No per i messaggi D2C, sì altrimenti. |
| ConnectionAuthMethod |Metodo di autenticazione impostato dall'hub IoT sui messaggi da dispositivo a cloud. Questa proprietà contiene informazioni sul metodo di autenticazione usato per autenticare il dispositivo che invia il messaggio. Per altre informazioni, vedere la sezione sull'[anti-spoofing da dispositivo a cloud][lnk-antispoofing]. | No per i messaggi D2C, sì altrimenti. |
| CreationTimeUtc | Data e ora di creazione del messaggio in un dispositivo. Nel dispositivo questo valore deve essere impostato in modo esplicito. | Yes |

## <a name="message-size"></a>Dimensioni dei messaggi

L'hub IoT misura le dimensioni dei messaggi in modo indipendente dal protocollo, prendendo in considerazione solo il payload effettivo. Le dimensioni in byte vengono calcolate come somma degli elementi seguenti:

* Dimensioni del corpo in byte.
* Dimensioni in byte di tutti i valori delle proprietà di sistema dei messaggi.
* Dimensioni in byte di tutti i nomi e i valori delle proprietà dell'utente.

I nomi e i valori delle proprietà sono limitati ai caratteri ASCII, in modo che la lunghezza delle stringhe sia uguale alle dimensioni in byte.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla dimensione massima dei messaggi nell'hub IoT, vedere [Quote e limitazioni dell'hub IoT][lnk-quotas].

Per informazioni su come creare e leggere i messaggi dell'hub IoT in diversi linguaggi di programmazione, vedere le [guide introduttive][lnk-get-started].

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: quickstart-send-telemetry-node.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties
