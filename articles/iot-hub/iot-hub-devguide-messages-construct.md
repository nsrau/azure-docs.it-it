---
title: Informazioni sul formato della messaggistica di hub IoT di Azure | Microsoft Docs
description: "Guida per sviluppatori: descrive il formato e il contenuto previsto dei messaggi dell'IoT Hub."
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 08eb7171249c42348877afedc80c6c6338265422
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861732"
---
# <a name="create-and-read-iot-hub-messages"></a>Creare e leggere messaggi dell'hub IoT

Per supportare l'interoperabilità senza problemi tra i protocolli, l'hub IoT definisce un formato di messaggio comune per tutti i protocolli del dispositivo. Tale formato viene usato sia per i messaggi di [routing da dispositivo a cloud](iot-hub-devguide-messages-d2c.md) che per i messaggi [da cloud a dispositivo](iot-hub-devguide-messages-c2d.md). 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

L'hub IoT implementa la messaggistica da dispositivo a cloud usando un modello di messaggistica di flusso. I messaggi da dispositivo a cloud dell'hub IoT sono più simili a *eventi* di [Hub eventi](/azure/event-hubs/) che non a *messaggi* del [bus di servizio](/azure/service-bus-messaging/), poiché è presente un volume elevato di eventi che passano attraverso il servizio e sono leggibili tramite più lettori.

Un messaggio dell'hub IoT è costituito da:

* Un set di predeterminato di *proprietà di sistema* come indicato di seguito.

* Un set di *proprietà dell'applicazione*. Dizionario di proprietà stringa che l'applicazione può definire e a cui può accedere senza dover deserializzare il corpo del messaggio. Queste proprietà non vengono mai modificate dall'hub IoT.

* Corpo binario opaco.

Quando si inviano messaggi da dispositivo a cloud tramite il protocollo HTTPS o messaggi da cloud a dispositivo, i valori e i nomi di proprietà possono contenere solo caratteri ASCII alfanumerici e i caratteri ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

La messaggistica da dispositivo a cloud con hub IoT ha le caratteristiche seguenti:

* I messaggi da dispositivo a cloud sono durevoli e vengono mantenuti nell'endpoint **messages/events** predefinito in un hub IoT per un massimo di sette giorni.

* I messaggi da dispositivo a cloud possono avere dimensioni massime pari a 256 KB e possono essere raggruppati in batch per ottimizzare gli invii. I batch possono avere dimensioni massime pari a 256 KB.

* L'hub IoT non consente il partizionamento arbitrario. I messaggi da dispositivo a cloud vengono partizionati in base al valore **deviceId**di origine.

* Come illustrato in [Controllare l'accesso all'hub IoT](iot-hub-devguide-security.md), l'hub IoT consente il controllo di accesso e l'autenticazione per singoli dispositivi.

Per altre informazioni sulla codifica e la decodifica dei messaggi inviati tramite protocolli diversi, vedere [Azure IoT SDK](iot-hub-devguide-sdks.md).

La tabella seguente riporta il set delle proprietà di sistema nei messaggi dell'hub IoT.

| Proprietà | DESCRIZIONE | È possibile impostare l'utente? |
| --- | --- | --- |
| message-id |Un identificatore configurabile dall'utente per il messaggio, usato per i modelli di richiesta-risposta. Formato: Stringa con distinzione tra maiuscole e minuscole (con lunghezza massima di 128 caratteri) di caratteri alfanumerici ASCII a 7 bit + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | Sì |
| sequence-number |Numero, univoco per ogni dispositivo-coda, assegnato dall'hub IoT a ogni messaggio da cloud a dispositivo. | No per i messaggi C2D, sì altrimenti. |
| to |Destinazione specificata nei messaggi [da cloud a dispositivo](iot-hub-devguide-c2d-guidance.md) . | No per i messaggi C2D, sì altrimenti. |
| absolute-expiry-time |Data e ora della scadenza del messaggio. | Sì |
| iothub-enqueuedtime |Date e ora di ricezione del messaggio [da cloud a dispositivo](iot-hub-devguide-c2d-guidance.md) da parte dell'hub IoT. | No per i messaggi C2D, sì altrimenti. |
| correlation-id |Proprietà stringa in un messaggio di risposta che contiene in genere il valore MessageId della richiesta nei modelli richiesta-risposta. | Sì |
| user-id |ID usato per specificare l'origine dei messaggi. Quando i messaggi vengono generati dall'hub IoT, viene impostata su `{iot hub name}`. | No  |
| iothub-ack |Generatore di messaggi con commenti. Questa proprietà viene usata nei messaggi da cloud a dispositivo per richiedere all'hub IoT di generare messaggi con commenti come risultato dell'utilizzo del messaggio da parte del dispositivo. I valori possibili sono i seguenti: **none** (predefinito): non viene generato alcun messaggio con commenti, **positive**: si riceve un messaggio con commenti se il messaggio è stato completato, **negative**: si riceve un messaggio con commenti se il messaggio è scaduto o se è stato raggiunto il numero massimo di recapiti senza il completamento da parte del dispositivo, **full**: sia per esito positivo che negativo. <!-- robinsh For more information, see [Message feedback][lnk-feedback].--> | Sì |
| iothub-connection-device-id |ID impostato dall'hub IoT sui messaggi da dispositivo a cloud. Contiene il valore **deviceId** del dispositivo che ha inviato il messaggio. | No per i messaggi D2C, sì altrimenti. |
| iothub-connection-auth-generation-id |ID impostato dall'hub IoT sui messaggi da dispositivo a cloud. Contiene il valore **generationId** (come indicato in [Proprietà delle identità dei dispositivi](iot-hub-devguide-identity-registry.md#device-identity-properties)) del dispositivo che ha inviato il messaggio. | No per i messaggi D2C, sì altrimenti. |
| iothub-connection-auth-method |Metodo di autenticazione impostato dall'hub IoT sui messaggi da dispositivo a cloud. Questa proprietà contiene informazioni sul metodo di autenticazione usato per autenticare il dispositivo che invia il messaggio. <!-- ROBINSH For more information, see [Device to cloud anti-spoofing][lnk-antispoofing].--> | No per i messaggi D2C, sì altrimenti. |
| iothub-creation-time-utc | Data e ora di creazione del messaggio in un dispositivo. Nel dispositivo questo valore deve essere impostato in modo esplicito. | Sì |

## <a name="message-size"></a>Dimensioni dei messaggi

L'hub IoT misura le dimensioni dei messaggi in modo indipendente dal protocollo, prendendo in considerazione solo il payload effettivo. Le dimensioni in byte vengono calcolate come somma degli elementi seguenti:

* Dimensioni del corpo in byte.
* Dimensioni in byte di tutti i valori delle proprietà di sistema dei messaggi.
* Dimensioni in byte di tutti i nomi e i valori delle proprietà dell'utente.

I nomi e i valori delle proprietà sono limitati ai caratteri ASCII, in modo che la lunghezza delle stringhe sia uguale alle dimensioni in byte.

## <a name="anti-spoofing-properties"></a>Proprietà anti-spoofing

Per evitare lo spoofing di dispositivi nei messaggi da dispositivo a cloud, l'hub IoT contrassegna tutti i messaggi con le proprietà seguenti:

* **iothub-connection-device-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-method**

Le prime due contengono le proprietà **deviceId** e **generationId** del dispositivo di origine, come indicato in [Proprietà delle identità dei dispositivi](iot-hub-devguide-identity-registry.md#device-identity-properties).

La proprietà **iothub-connection-auth-method** contiene un oggetto serializzato JSON con le proprietà seguenti:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla dimensione massima dei messaggi nell'hub IoT, vedere [Quote e limitazioni dell'hub IoT](iot-hub-devguide-quotas-throttling.md).

* Per informazioni su come creare e leggere i messaggi dell'hub IoT in diversi linguaggi di programmazione, vedere le [guide introduttive](quickstart-send-telemetry-node.md).
