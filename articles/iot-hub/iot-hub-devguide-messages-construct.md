---
title: Informazioni sul formato della messaggistica di hub IoT di Azure | Microsoft Docs
description: 'Guida per sviluppatori: descrive il formato e il contenuto previsto dei messaggi hub IoT.'
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: edea20343c2a261902c082dbc5c96b78db6b470d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973220"
---
# <a name="create-and-read-iot-hub-messages"></a>Creare e leggere messaggi dell'hub IoT

Per supportare l'interoperabilità senza problemi tra i protocolli, l'hub IoT definisce un formato di messaggio comune per tutti i protocolli del dispositivo. Tale formato viene usato sia per i messaggi di [routing da dispositivo a cloud](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) che per i messaggi [da cloud a dispositivo](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-c2d). 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

L'hub IoT implementa la messaggistica da dispositivo a cloud usando un modello di messaggistica di flusso. I messaggi da dispositivo a cloud dell'hub IoT sono più simili a *eventi* di [Hub eventi](https://docs.microsoft.com/azure/event-hubs/) che non a *messaggi* del [bus di servizio](https://docs.microsoft.com/azure/service-bus-messaging/), poiché è presente un volume elevato di eventi che passano attraverso il servizio e sono leggibili tramite più lettori.

Un messaggio dell'hub IoT è costituito da:
* Un set di predeterminato di *proprietà di sistema* come indicato di seguito.
* Un set di *proprietà dell'applicazione*. Dizionario di proprietà stringa che l'applicazione può definire e a cui può accedere senza dover deserializzare il corpo del messaggio. Queste proprietà non vengono mai modificate dall'hub IoT.
* Corpo binario opaco.

Quando si inviano messaggi da dispositivo a cloud tramite il protocollo HTTPS o messaggi da cloud a dispositivo, i valori e i nomi di proprietà possono contenere solo caratteri ASCII alfanumerici e i caratteri ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}```.

La messaggistica da dispositivo a cloud con hub IoT ha le caratteristiche seguenti:

* I messaggi da dispositivo a cloud sono durevoli e vengono mantenuti nell'endpoint **messages/events** predefinito in un hub IoT per un massimo di sette giorni.
* I messaggi da dispositivo a cloud possono avere dimensioni massime pari a 256 KB e possono essere raggruppati in batch per ottimizzare gli invii. I batch possono avere dimensioni massime pari a 256 KB.
* L'hub IoT non consente il partizionamento arbitrario. I messaggi da dispositivo a cloud vengono partizionati in base al valore **deviceId**di origine.
* Come illustrato nella sezione [Controllare l'accesso all'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security), l'hub IoT consente il controllo di accesso e l'autenticazione per singoli dispositivi.

Per altre informazioni sulla codifica e la decodifica dei messaggi inviati tramite protocolli diversi, vedere [Azure IoT SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

La tabella seguente riporta il set delle proprietà di sistema nei messaggi dell'hub IoT.

| Proprietà | DESCRIZIONE | È possibile impostare l'utente? |
| --- | --- | --- |
| MessageId |Un identificatore configurabile dall'utente per il messaggio, usato per i modelli di richiesta-risposta. Formato: stringa con distinzione tra maiuscole e minuscole (con lunghezza massima di 128 caratteri) di caratteri alfanumerici ASCII a 7 bit + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | Yes |
| Numero di sequenza |Numero, univoco per ogni dispositivo-coda, assegnato dall'hub IoT a ogni messaggio da cloud a dispositivo. | No per i messaggi C2D, sì altrimenti. |
| A |Destinazione specificata nei messaggi [da cloud a dispositivo][lnk-c2d]. | No per i messaggi C2D, sì altrimenti. |
| ExpiryTimeUtc |Data e ora della scadenza del messaggio. | Yes |
| EnqueuedTime |Data e ora della ricezione del messaggio [da cloud a dispositivo][lnk-c2d] da parte dall'hub IoT. | No per i messaggi C2D, sì altrimenti. |
| CorrelationId |Proprietà stringa in un messaggio di risposta che contiene in genere il valore MessageId della richiesta nei modelli richiesta-risposta. | Yes |
| UserId |ID usato per specificare l'origine dei messaggi. Quando i messaggi vengono generati dall'hub IoT, viene impostata su `{iot hub name}`. | No  |
| Ack |Generatore di messaggi con commenti. Questa proprietà viene usata nei messaggi da cloud a dispositivo per richiedere all'hub IoT di generare messaggi con commenti come risultato dell'utilizzo del messaggio da parte del dispositivo. I valori possibili sono i seguenti: **none** (predefinito): non viene generato alcun messaggio con commenti, **positive**: si riceve un messaggio con commenti se il messaggio è stato completato, **negative**: si riceve un messaggio con commenti se il messaggio è scaduto o se è stato raggiunto il numero massimo di recapiti senza il completamento da parte del dispositivo, **full**: sia per esito positivo che negativo. Per altre informazioni, vedere [Commenti sui messaggi][lnk-feedback]. | Yes |
| ConnectionDeviceId |ID impostato dall'hub IoT sui messaggi da dispositivo a cloud. Contiene il valore **deviceId** del dispositivo che ha inviato il messaggio. | No per i messaggi D2C, sì altrimenti. |
| ConnectionDeviceGenerationId |ID impostato dall'hub IoT sui messaggi da dispositivo a cloud. Contiene il valore **generationId** del dispositivo che ha inviato il messaggio, come indicato in [Proprietà delle identità dei dispositivi][lnk-device-properties]. | No per i messaggi D2C, sì altrimenti. |
| ConnectionAuthMethod |Metodo di autenticazione impostato dall'hub IoT sui messaggi da dispositivo a cloud. Questa proprietà contiene informazioni sul metodo di autenticazione usato per autenticare il dispositivo che invia il messaggio. Per altre informazioni, vedere la sezione [Anti-spoofing da dispositivo a cloud][lnk-antispoofing]. | No per i messaggi D2C, sì altrimenti. |
| CreationTimeUtc | Data e ora di creazione del messaggio in un dispositivo. Nel dispositivo questo valore deve essere impostato in modo esplicito. | Yes |

## <a name="message-size"></a>Dimensioni dei messaggi

L'hub IoT misura le dimensioni dei messaggi in modo indipendente dal protocollo, prendendo in considerazione solo il payload effettivo. Le dimensioni in byte vengono calcolate come somma degli elementi seguenti:

* Dimensioni del corpo in byte.
* Dimensioni in byte di tutti i valori delle proprietà di sistema dei messaggi.
* Dimensioni in byte di tutti i nomi e i valori delle proprietà dell'utente.

I nomi e i valori delle proprietà sono limitati ai caratteri ASCII, in modo che la lunghezza delle stringhe sia uguale alle dimensioni in byte.

## <a name="anti-spoofing-properties"></a>Proprietà anti-spoofing

Per evitare lo spoofing di dispositivi nei messaggi da dispositivo a cloud, l'hub IoT contrassegna tutti i messaggi con le proprietà seguenti:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Le prime due contengono le proprietà **deviceId** e **generationId** del dispositivo di origine, come indicato in [Proprietà delle identità dei dispositivi](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#device-identity-properties).

La proprietà **ConnectionAuthMethod** contiene un oggetto serializzato JSON con le proprietà seguenti:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla dimensione massima dei messaggi nell'hub IoT, vedere [Quote e limitazioni dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-quotas-throttling).
* Per informazioni su come creare e leggere i messaggi dell'hub IoT in diversi linguaggi di programmazione, vedere le [guide introduttive](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-node).