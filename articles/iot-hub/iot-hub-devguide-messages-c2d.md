---
title: Informazioni sulla messaggistica da cloud a dispositivo dell'hub IoT di Azure | Microsoft Docs
description: 'Guida per gli sviluppatori: come usare la messaggistica da cloud a dispositivo con l''hub IoT. Include informazioni sul ciclo di vita del messaggio e sulle opzioni di configurazione.'
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 763bc597bdfc40395511cdd9d797e5c7aaad0fdf
ms.openlocfilehash: 706c9650a8deef941f9b39956021456053369e5e
ms.contentlocale: it-it
ms.lasthandoff: 09/06/2017

---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>Inviare messaggi da cloud a dispositivo dall'hub IoT

Per inviare notifiche unidirezionali all'app del dispositivo dal back-end della soluzione, inviare messaggi da cloud a dispositivo dall'hub IoT al dispositivo. Per una descrizione delle altre opzioni da cloud a dispositivo supportate dall'hub IoT, vedere [Indicazioni sulle comunicazioni da cloud a dispositivo][lnk-c2d-guidance].

È possibile inviare messaggi da cloud a dispositivo tramite un endpoint per il servizio (**/messages/servicebound**). Il dispositivo li riceve tramite un endpoint specifico per il dispositivo (**/devices/{deviceId}/messages/devicebound**).

Ogni messaggio da cloud a dispositivo è destinato a un singolo dispositivo, tramite la proprietà **to** impostata su **/devices/{deviceId}/messages/devicebound**.

Ogni coda di dispositivo può contenere un massimo di 50 messaggi da cloud a dispositivo. Se si prova a inviare più messaggi allo stesso dispositivo, si verificherà un errore.

## <a name="the-cloud-to-device-message-lifecycle"></a>Ciclo di vita dei messaggi da cloud a dispositivo

Per garantire il recapito di almeno un messaggio, l'hub IoT salva in modo permanente i messaggi da cloud a dispositivo nelle code dei singoli dispositivi. I dispositivi devono confermare esplicitamente il *completamento* per consentire all'hub IoT di rimuoverli dalla coda. Questo approccio garantisce la resilienza rispetto a errori di connettività e del dispositivo.

Il diagramma seguente illustra il grafico sullo stato del ciclo di vita per un messaggio da cloud a dispositivo nell'hub IoT.

![Ciclo di vita dei messaggi da cloud a dispositivo][img-lifecycle]

Quando il servizio hub IoT invia un messaggio a un dispositivo, lo stato del messaggio viene impostato su **Enqueued** (Accodato). Quando un dispositivo accetta di *ricevere* un messaggio, l'hub IoT *blocca* il messaggio, impostandone lo stato su **Invisibile**, per consentire ad altri thread nel dispositivo di iniziare a ricevere altri messaggi. Quando un thread del dispositivo termina l'elaborazione di un messaggio, invia una notifica all'hub IoT *completando* il messaggio. L'hub IoT quindi imposta lo stato **Completato**.

Un dispositivo può anche scegliere di:

* *Rifiutare* il messaggio. In questo caso, l'hub IoT ne imposta lo stato su **Non recapitabile**. I dispositivi che si connettono tramite il protocollo MQTT non possono rifiutare i messaggi da cloud a dispositivo.
* *Abbandonare* il messaggio. In questo caso, l'hub IoT inserisce di nuovo il messaggio nella coda con lo stato impostato su **Accodato**.

Un thread potrebbe non riuscire a elaborare un messaggio senza inviare una notifica all'hub IoT. In questo caso i messaggi passano automaticamente dallo stato **Invisibile** allo stato **Accodato** dopo un *timeout di visibilità o di blocco*. Il valore predefinito di questo timeout è un minuto.

Un messaggio può passare dallo stato **Accodato** allo stato **Invisibile** e viceversa per il numero massimo di volte specificato nella proprietà **maxdeliverycount** dell'hub IoT. Dopo il numero di transizioni specificato, l'hub IoT imposta lo stato del messaggio su **Non recapitabile**. Analogamente, l'hub IoT imposta lo stato di un messaggio su **Non recapitabile** dopo la relativa scadenza. Vedere la sezione [Scadenza del messaggio (durata)][lnk-ttl].

L'esercitazione [Come inviare i messaggi da cloud a dispositivo con l'hub IoT][lnk-c2d-tutorial] illustra come inviare messaggi da cloud a dispositivo dal cloud e riceverli su un dispositivo.

Generalmente, un dispositivo completa i messaggi da cloud a dispositivo quando la perdita del messaggio non influisce sulla logica dell'applicazione. Ad esempio, quando il dispositivo ha salvato in modo permanente il contenuto del messaggio in locale o ha eseguito correttamente un'operazione. Il messaggio potrebbe anche includere informazioni temporanee la cui perdita non influirebbe sulla funzionalità dell'applicazione. In alcuni casi, per le attività con esecuzione prolungata è possibile:

* Completare il messaggio da cloud a dispositivo dopo aver salvato in modo permanente la descrizione dell'attività nell'archivio locale.
* Inviare al back-end della soluzione una notifica con uno o più messaggi da dispositivo a cloud in diverse fasi di avanzamento dell'attività.

## <a name="message-expiration-time-to-live"></a>Scadenza del messaggio (durata)

Ogni messaggio da cloud a dispositivo ha una scadenza. Tale scadenza viene impostata da uno degli elementi seguenti:

* La proprietà **ExpiryTimeUtc** nel servizio.
* Lo hub IoT che usa il valore *TTL* predefinito specificato come proprietà dello hub IoT.

Vedere [Opzioni di configurazione da cloud a dispositivo][lnk-c2d-configuration].

Un modo comune per usare la scadenza del messaggio a proprio vantaggio ed evitare l'invio di messaggi a dispositivi disconnessi consiste nell'impostare valori di durata brevi. Questo approccio permette di ottenere lo stesso risultato che si ottiene mantenendo connesso il dispositivo, ma è più efficiente. Quando si richiede l'acknowledgement dei messaggi, lo hub IoT informa che i dispositivi:

* sono abilitati a ricevere messaggi.
* sono offline, oppure l'operazione non è riuscita.

## <a name="message-feedback"></a>Commenti sui messaggi

Durante l'invio di messaggi da cloud a dispositivo, il servizio può richiedere il recapito di commenti specifici per ogni messaggio in merito allo stato finale del messaggio.

| Proprietà Ack | Comportamento |
| ------------ | -------- |
| **positive** | Se il messaggio da cloud a dispositivo ha raggiunto lo stato **Completato**, l'hub IoT genera un messaggio con commenti. |
| **negative** | Se il messaggio da cloud a dispositivo ha raggiunto lo stato **Deadlettered** (Recapitato), l'hub IoT genera un messaggio con commenti. |
| **full**     | L'hub IoT genera un messaggio di commenti in entrambi i casi. |

Se la proprietà **Ack** è impostata su **full** e non si riceve un messaggio con commenti, significa che il messaggio è scaduto. Il servizio non può sapere cosa è successo al messaggio originale. In pratica, un servizio deve garantire che sia possibile elaborare i commenti prima della scadenza. Il periodo di scadenza massimo è di due giorni ed è quindi disponibile un tempo sufficiente per ripristinare il servizio in caso di errore.

Come illustrato nella sezione [Endpoint][lnk-endpoints], l'hub IoT recapita commenti sotto forma di messaggi tramite un endpoint per servizio (**/messages/servicebound/feedback**). La semantica di ricezione per i commenti è uguale a quella dei messaggi da cloud a dispositivo e ha lo stesso [ciclo di vita dei messaggi][lnk-lifecycle]. Quando è possibile, i commenti sui messaggio vengono riuniti in batch in un unico messaggio con il formato seguente:

| Proprietà     | Descrizione |
| ------------ | ----------- |
| EnqueuedTime | Timestamp che indica quando è stato creato il messaggio. |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

Il corpo è una matrice serializzata con JSON dei record, ognuno con le proprietà seguenti:

| Proprietà           | Descrizione |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Timestamp che indica quando è stato creato il risultato del messaggio. Ad esempio, il dispositivo ha completato l'operazione o il messaggio è scaduto. |
| OriginalMessageId  | **MessageId** del messaggio da cloud a dispositivo correlato a queste informazioni sui commenti. |
| StatusCode         | Stringa obbligatoria. Usato nei messaggi con commenti generati dall'hub IoT. <br/> 'Operazione riuscita' <br/> 'Scaduto' <br/> 'Numero di distribuzioni superato' <br/> 'Rifiutato' <br/> 'Eliminato definitivamente' |
| Descrizione        | Valori stringa per **StatusCode**. |
| deviceId           | **DeviceId** del messaggio da cloud a dispositivo correlato a questi commenti e suggerimenti. |
| DeviceGenerationId | **DeviceGenerationId** del dispositivo di destinazione del messaggio da cloud a dispositivo correlato a questi commenti e suggerimenti. |

Il servizio deve specificare un valore **MessageId** per il messaggio da cloud a dispositivo per poter correlare i commenti al messaggio originale.

L'esempio seguente illustra il corpo di un messaggio con commenti.

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
| defaultTtlAsIso8601       | Durata (TTL) predefinita per messaggi da cloud a dispositivo. | Intervallo ISO_8601 fino a 2D (almeno 1 minuto). Predefinito: 1 ora. |
| maxDeliveryCount          | Numero massimo di recapiti per code da cloud a dispositivo per i singoli dispositivi. | Da 1 a 100. Predefinito: 10. |
| feedback.ttlAsIso8601     | Conservazione per messaggi con commenti diretti al servizio. | Intervallo ISO_8601 fino a 2D (almeno 1 minuto). Predefinito: 1 ora. |
| feedback.maxDeliveryCount |Numero massimo di recapiti per la coda di commenti. | Da 1 a 100. Predefinito: 100. |

Per altre informazioni su come impostare queste opzioni di configurazione, vedere [Creare hub IoT][lnk-portal].

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sugli SDK che è possibile usare per ricevere i messaggi da cloud a dispositivo, vedere [Azure IoT SDK][lnk-sdks].

Per provare a ricevere i messaggi da cloud a dispositivo, vedere l'esercitazione [Invio da cloud a dispositivo][lnk-c2d-tutorial].

[img-lifecycle]: ./media/iot-hub-devguide-messages-c2d/lifecycle.png

[lnk-portal]: iot-hub-create-through-portal.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-ttl]: #message-expiration-time-to-live
[lnk-c2d-configuration]: #cloud-to-device-configuration-options
[lnk-lifecycle]: #message-lifecycle
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

