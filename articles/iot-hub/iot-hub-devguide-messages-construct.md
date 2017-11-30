---
title: Informazioni sul formato della messaggistica di hub IoT di Azure | Microsoft Docs
description: 'Guida per sviluppatori: descrive il formato e il contenuto previsto dei messaggi hub IoT.'
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: dobett
ms.openlocfilehash: 1a20b9c6981125895ecd6952135ec4f365bb8d45
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="create-and-read-iot-hub-messages"></a>Creare e leggere messaggi dell'hub IoT

Per supportare l'interoperabilità senza problemi tra i protocolli, l'hub IoT definisce un formato di messaggio comune per tutti i protocolli del dispositivo. Il formato del messaggio viene usato per i messaggi [da dispositivo a cloud][lnk-d2c] e [da cloud a dispositivo][lnk-c2d]. Un [messaggio dell'hub IoT][lnk-messaging] è costituito da:

* Un set di *proprietà del sistema*. Proprietà impostate o interpretate dall'hub IoT. Il set è predeterminato.
* Un set di *proprietà dell'applicazione*. Dizionario di proprietà stringa che l'applicazione può definire e a cui può accedere senza dover deserializzare il corpo del messaggio. Queste proprietà non vengono mai modificate dall'hub IoT.
* Corpo binario opaco.

I valori e i nomi di proprietà possono contenere solo caratteri ASCII alfanumerici e ``{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` quando:

* Si inviano messaggi da dispositivo a cloud usando il protocollo HTTPS.
* Invio di messaggi da cloud a dispositivo.

Per altre informazioni sulla codifica e la decodifica dei messaggi inviati tramite diversi protocolli, vedere [Azure IoT SDK][lnk-sdks].

La tabella seguente riporta il set delle proprietà di sistema nei messaggi dell'hub IoT.

| Proprietà | Descrizione |
| --- | --- |
| MessageId |Un identificatore configurabile dall'utente per il messaggio, usato per i modelli di richiesta-risposta. Formato: stringa con distinzione tra maiuscole e minuscole (con lunghezza massima di 128 caratteri) di caratteri alfanumerici ASCII a 7 bit + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Numero di sequenza |Numero, univoco per ogni dispositivo-coda, assegnato dall'hub IoT a ogni messaggio da cloud a dispositivo. |
| To |Destinazione specificata nei messaggi [da cloud a dispositivo][lnk-c2d]. |
| ExpiryTimeUtc |Data e ora della scadenza del messaggio. |
| EnqueuedTime |Date e ora del messaggio [da cloud a dispositivo][lnk-c2d] ricevute dall'hub IoT. |
| CorrelationId |Proprietà stringa in un messaggio di risposta che contiene in genere il valore MessageId della richiesta nei modelli richiesta-risposta. |
| UserId |ID usato per specificare l'origine dei messaggi. Quando i messaggi vengono generati dall'hub IoT, viene impostata su `{iot hub name}`. |
| Ack |Generatore di messaggi con commenti. Questa proprietà viene usata nei messaggi da cloud a dispositivo per richiedere all'hub IoT di generare messaggi con commenti come risultato dell'utilizzo del messaggio da parte del dispositivo. I valori possibili sono i seguenti: **none** (predefinito): non viene generato alcun messaggio con commenti, **positive**: si riceve un messaggio con commenti se il messaggio è stato completato, **negative**: si riceve un messaggio con commenti se il messaggio è scaduto o se è stato raggiunto il numero massimo di recapiti senza il completamento da parte del dispositivo, **full**: sia per esito positivo che negativo. Per altre informazioni, vedere [Commenti sui messaggi][lnk-feedback]. |
| ConnectionDeviceId |ID impostato dall'hub IoT sui messaggi da dispositivo a cloud. Contiene il valore **deviceId** del dispositivo che ha inviato il messaggio. |
| ConnectionDeviceGenerationId |ID impostato dall'hub IoT sui messaggi da dispositivo a cloud. Contiene il valore **generationId** (come indicato in [Proprietà delle identità dei dispositivi][lnk-device-properties]) del dispositivo che ha inviato il messaggio. |
| ConnectionAuthMethod |Metodo di autenticazione impostato dall'hub IoT sui messaggi da dispositivo a cloud. Questa proprietà contiene informazioni sul metodo di autenticazione usato per autenticare il dispositivo che invia il messaggio. Per altre informazioni, vedere la sezione sull'[anti-spoofing da dispositivo a cloud][lnk-antispoofing]. |

## <a name="message-size"></a>Dimensioni dei messaggi

L'hub IoT misura le dimensioni dei messaggi in modo indipendente dal protocollo, prendendo in considerazione solo il payload effettivo. Le dimensioni in byte vengono calcolate come somma degli elementi seguenti:

* Dimensioni del corpo in byte.
* Dimensioni in byte di tutti i valori delle proprietà di sistema dei messaggi.
* Dimensioni in byte di tutti i nomi e i valori delle proprietà dell'utente.

I nomi e i valori delle proprietà sono limitati ai caratteri ASCII, in modo che la lunghezza delle stringhe sia uguale alle dimensioni in byte.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla dimensione massima dei messaggi nell'hub IoT, vedere [Quote e limitazioni dell'hub IoT][lnk-quotas].

Per informazioni su come creare e leggere i messaggi dell'hub IoT in diversi linguaggi di programmazione, vedere le esercitazioni di [Introduzione][lnk-get-started].

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties
