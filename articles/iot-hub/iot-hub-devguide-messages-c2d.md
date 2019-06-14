---
title: Informazioni sulla messaggistica da cloud a dispositivo dell'hub IoT di Azure | Microsoft Docs
description: Questa Guida per gli sviluppatori viene illustrato come usare la messaggistica da cloud a dispositivo con l'hub IoT. Sono incluse informazioni sulle opzioni di configurazione e del ciclo di vita di messaggio.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 0fc1b65a4ba1c8a3d76b48206d6a4703035e05bc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055316"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Inviare messaggi da cloud a dispositivo da un hub IoT

Per inviare notifiche unidirezionali all'app dispositivo dal back-end della soluzione, inviare messaggi da cloud a dispositivo dall'hub IoT al dispositivo. Per una descrizione delle altre opzioni da cloud a dispositivo supportate dall'IoT Hub di Azure, vedere [indicazioni sulle comunicazioni da Cloud a dispositivo](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Si inviano messaggi da cloud a dispositivo tramite un endpoint per il servizio, */Messages/servicebound*. Un dispositivo riceve quindi i messaggi attraverso un endpoint specifico del dispositivo */Devices/{DeviceID}/Files /Messages/servicebound*.

Per indirizzare ogni messaggio da cloud a dispositivo a un singolo dispositivo, l'hub IoT imposta il **al** proprietà */Devices/{DeviceID}/Files /Messages/servicebound*.

Ogni coda di dispositivo contiene al massimo 50 messaggi da cloud a dispositivo. Per provare a inviare più messaggi per gli stessi risultati di dispositivo in un errore.

## <a name="the-cloud-to-device-message-life-cycle"></a>Il ciclo di vita del messaggio da cloud a dispositivo

Per garantire il recapito dei messaggi at-least-once, l'hub IoT rende persistenti i messaggi da cloud a dispositivo nelle code per dispositivo. Per l'hub IoT rimuovere i messaggi dalla coda, i dispositivi devono confermare esplicitamente *completamento*. Questo approccio garantisce la resilienza rispetto a errori di connettività e del dispositivo.

Il grafico sullo stato del ciclo di vita viene visualizzato nel diagramma seguente:

![Ciclo di vita di messaggi da cloud a dispositivo](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Quando il servizio hub IoT invia un messaggio a un dispositivo, il servizio imposta lo stato del messaggio *accodati*. Quando un dispositivo desidera *ricevere* un messaggio, l'hub IoT *blocchi* messaggio impostando lo stato su *invisibile*. Questo stato consentire ad altri thread sul dispositivo per iniziare a ricevere altri messaggi. Quando un thread del dispositivo completa l'elaborazione di un messaggio, notifica all'hub IoT da *completamento* il messaggio. L'hub IoT quindi imposta lo stato su *Completed*.

Un dispositivo può anche:

* *Rifiutare* il messaggio, che fa sì che l'hub IoT impostare l'opzione di *Dead lettered* dello stato. I dispositivi che si connettono tramite il protocollo di messaggi di Accodamento MQTT Telemetry Transport () non possono rifiutare i messaggi da cloud a dispositivo.

* *Abbandonare* il messaggio, che fa sì che l'hub IoT inserisce il messaggio nella coda, con lo stato impostato su *accodati*. I dispositivi che si connettono tramite il protocollo MQTT non è possibile abbandonare i messaggi da cloud a dispositivo.

Un thread potrebbe non riuscire a elaborare un messaggio senza inviare notifica all'hub IoT. In questo caso, i messaggi passano automaticamente dal *invisibile* allo stato di *accodati* dopo una *visibilità* timeout (o *blocco* timeout). Il valore predefinito di questo valore di timeout è un minuto.

Il **maxdeliverycount** proprietà dell'hub IoT consente di determinare il numero massimo di volte in cui un messaggio può passare tra le *accodati* e *invisibile* stati. Dopo il numero di transizioni, l'hub IoT imposta lo stato del messaggio *Dead lettered*. Analogamente, l'hub IoT imposta lo stato di un messaggio al *Dead lettered* dopo la scadenza. Per altre informazioni, vedere [durata (TTL)](#message-expiration-time-to-live).

Il [come inviare messaggi da cloud a dispositivo con l'IoT Hub](iot-hub-csharp-csharp-c2d.md) articolo illustra come inviare messaggi da cloud a dispositivo dal cloud e riceverli in un dispositivo.

Quando la perdita del messaggio non influisce sulla logica dell'applicazione, un dispositivo completa in genere un messaggio da cloud a dispositivo. Un esempio potrebbe essere quando il dispositivo ha reso persistente in locale il contenuto del messaggio o eseguiti correttamente un'operazione. Il messaggio potrebbe anche includere informazioni temporanee la cui perdita non influire negativamente sulla funzionalità dell'applicazione. In alcuni casi, per le attività con esecuzione prolungata è possibile:

* Completare il messaggio da cloud a dispositivo dopo che il dispositivo ha reso persistente la descrizione dell'attività nell'archivio locale.

* Inviare al back-end della soluzione una notifica con uno o più messaggi da dispositivo a cloud in diverse fasi di avanzamento dell'attività.

## <a name="message-expiration-time-to-live"></a>Scadenza del messaggio (durata)

Ogni messaggio da cloud a dispositivo ha una scadenza. Questa durata viene impostata da uno dei seguenti:

* Il **ExpiryTimeUtc** proprietà nel servizio
* L'hub IoT, usando il valore predefinito *durata (TTL)* che viene specificato come proprietà dell'hub IoT

Vedere [Opzioni di configurazione da cloud a dispositivo](#cloud-to-device-configuration-options).

Un modo comune per sfruttare i vantaggi di una scadenza del messaggio ed evitare l'invio di messaggi a dispositivi disconnessi consiste nell'impostare brevi *durata (TTL)* valori. Questo approccio consente di ottenere lo stesso risultato di mantenere lo stato di connessione del dispositivo, ma è più efficiente. Quando si richiede dei riconoscimenti dei messaggi, invia una notifica che i dispositivi dell'hub IoT:

* sono abilitati a ricevere messaggi.
* sono offline, oppure l'operazione non è riuscita.

## <a name="message-feedback"></a>Commenti sui messaggi

Quando si invia un messaggio da cloud a dispositivo, il servizio può richiedere il recapito di commenti e suggerimenti per ogni messaggio sullo stato finale del messaggio in questione. A questo scopo, impostare il **iothub-ack** proprietà dell'applicazione nel messaggio da cloud a dispositivo che viene inviato a uno dei quattro valori seguenti:

| Valore della proprietà ACK | Comportamento |
| ------------ | -------- |
| Nessuno     | L'hub IoT non genera un messaggio con commenti (comportamento predefinito). |
| positivo | Se il messaggio da cloud a dispositivo raggiunge il *Completed* lo stato, l'hub IoT genera un messaggio con commenti. |
| Negativo | Se il messaggio da cloud a dispositivo raggiunge il *Dead lettered* lo stato, l'hub IoT genera un messaggio con commenti. |
| completo     | L'hub IoT genera un messaggio con commenti in entrambi i casi. |

Se il **Ack** valore è *completo*e non si riceve un messaggio con commenti, significa che il messaggio di commenti e suggerimenti è scaduto. Il servizio non può sapere cosa è successo al messaggio originale. In pratica, un servizio deve garantire che sia possibile elaborare i commenti prima della scadenza. L'ora di scadenza massimo è due giorni e il tempo sufficiente per ripristinare il servizio in esecuzione anche in questo caso se si verifica un errore.

Come illustrato nella [endpoint](iot-hub-devguide-endpoints.md), l'hub IoT recapita commenti e suggerimenti tramite un endpoint per il servizio */messages/servicebound/feedback*, sotto forma di messaggi. La semantica di ricezione per i commenti è uguale a quella dei messaggi da cloud a dispositivo. Quando è possibile, i commenti sui messaggio vengono riuniti in batch in un unico messaggio con il formato seguente:

| Proprietà     | Descrizione |
| ------------ | ----------- |
| EnqueuedTime | Timestamp che indica quando è stato ricevuto il messaggio con commenti dall'hub |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

Il corpo è una matrice serializzata con JSON dei record, ognuno con le proprietà seguenti:

| Proprietà           | Descrizione |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Timestamp che indica quando il risultato del messaggio si è verificata (ad esempio, l'hub ha ricevuto il messaggio di commenti e suggerimenti o il messaggio originale è scaduto) |
| OriginalMessageId  | Il *MessageId* del messaggio da cloud a dispositivo a cui si riferiscano queste informazioni sui commenti |
| StatusCode         | Stringa obbligatoria, usata nei messaggi con commenti generati dall'hub IoT: <br/> *Success* <br/> *Expired* <br/> *DeliveryCountExceeded* <br/> *Rifiutato* <br/> *Eliminati* |
| Descrizione        | Valori stringa per *StatusCode* |
| deviceId           | Il *DeviceId* del dispositivo di destinazione del messaggio da cloud a dispositivo a cui si riferisce commenti e suggerimenti |
| DeviceGenerationId | Il *DeviceGenerationId* del dispositivo di destinazione del messaggio da cloud a dispositivo a cui si riferisce commenti e suggerimenti |

Per il messaggio da cloud a dispositivo correlare i commenti con il messaggio originale, il servizio deve specificare una *MessageId*.

Il corpo di un messaggio di commenti e suggerimenti è illustrato nel codice seguente:

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>Opzioni di configurazione da cloud a dispositivo

Ogni hub IoT espone le opzioni di configurazione seguenti per la messaggistica da cloud a dispositivo:

| Proprietà                  | Descrizione | Intervallo e valore predefinito |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Durata TTL predefinita per i messaggi da cloud a dispositivo | Intervallo ISO_8601 fino a 2 giorni (minimo 1 minuto); valore predefinito: 1 ora |
| maxDeliveryCount          | Numero massimo di recapiti per code da cloud a dispositivo per dispositivo | da 1 a 100. valore predefinito: 10 |
| feedback.ttlAsIso8601     | Conservazione per messaggi con commenti diretti al servizio | Intervallo ISO_8601 fino a 2 giorni (minimo 1 minuto); valore predefinito: 1 ora |
| feedback.maxDeliveryCount | Numero massimo di recapiti per la coda di commenti e suggerimenti | da 1 a 100. valore predefinito: 100 |

Per altre informazioni su come impostare queste opzioni di configurazione, vedere [Creare hub IoT](iot-hub-create-through-portal.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sugli SDK che è possibile usare per ricevere messaggi da cloud a dispositivo, vedere [Azure IoT SDKs](iot-hub-devguide-sdks.md).

Per provare a ricevere i messaggi da cloud a dispositivo, vedere l'esercitazione [Invio da cloud a dispositivo](iot-hub-csharp-csharp-c2d.md).
