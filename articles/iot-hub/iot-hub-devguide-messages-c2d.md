---
title: Informazioni sulla messaggistica da cloud a dispositivo dell'hub IoT di Azure | Microsoft Docs
description: Questa guida per gli sviluppatori illustra come usare la messaggistica da cloud a dispositivo con l'hub Internet delle cose. Sono incluse informazioni sul ciclo di vita dei messaggi e sulle opzioni di configurazione.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: d4a51a44b48e94669e92a9d525c1b0966df53c18
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68964139"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Inviare messaggi da cloud a dispositivo da un hub Internet delle cose

Per inviare notifiche unidirezionali a un'app del dispositivo dal back-end della soluzione, inviare messaggi da cloud a dispositivo dall'hub Internet al dispositivo. Per una descrizione delle altre opzioni da cloud a dispositivo supportate dall'hub Azure, vedere indicazioni sulle [comunicazioni da cloud a dispositivo](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Inviare messaggi da cloud a dispositivo tramite un endpoint per il servizio, */messages/devicebound*. Un dispositivo riceve quindi i messaggi tramite un endpoint specifico del dispositivo, */Devices/{DeviceID}/messages/devicebound*.

Per impostare come destinazione ogni messaggio da cloud a dispositivo in un singolo dispositivo, l'hub Internet delle cose imposta la proprietà **su** */Devices/{DeviceID}/messages/devicebound*.

Ogni coda di dispositivi include al massimo 50 messaggi da cloud a dispositivo. Per tentare di inviare più messaggi allo stesso dispositivo, viene restituito un errore.

## <a name="the-cloud-to-device-message-life-cycle"></a>Ciclo di vita dei messaggi da cloud a dispositivo

Per garantire il recapito di messaggi at-least-once, l'hub Internet delle cose rende permanente i messaggi da cloud a dispositivo nelle code per ogni dispositivo. Per rimuovere i messaggi dalla coda dall'hub Internet, i dispositivi devono confermare esplicitamente il *completamento*. Questo approccio garantisce la resilienza rispetto a errori di connettività e del dispositivo.

Il grafico di stato del ciclo di vita viene visualizzato nel diagramma seguente:

![Ciclo di vita dei messaggi da cloud a dispositivo](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Quando il servizio hub Internet delle cose Invia un messaggio a un dispositivo, il servizio imposta lo statodel messaggio su accodato. Quando un dispositivo vuole *ricevere* un messaggio, l'hub Internet delle cose *blocca* il messaggio impostando lo stato su *invisibile*. Questo stato consente agli altri thread sul dispositivo di iniziare a ricevere altri messaggi. Al termine dell'elaborazione di un messaggio da parte di un thread del dispositivo, viene inviata una notifica all'hub delle cose completando il messaggio. L'hub Internet delle cose imposta quindi lo stato su *completato*.

Un dispositivo può anche:

* *Rifiutare* il messaggio, che fa sì che l'hub Internet sia impostato sullo stato non recapitabile. I dispositivi che si connettono tramite il protocollo di trasporto di telemetria di Accodamento messaggi (MQTT) non possono rifiutare i messaggi da cloud a dispositivo.

* *Abbandonare* il messaggio, che fa in modo che l'hub di Internet delle cose riporti il messaggio nella coda, con lo stato impostato su *accodato*. I dispositivi che si connettono tramite il protocollo MQTT non possono abbandonare i messaggi da cloud a dispositivo.

Un thread potrebbe non riuscire a elaborare un messaggio senza notificare l'hub. In questo caso, i messaggi passano automaticamente dallo stato *invisibile* allo stato *accodato* dopo un timeout di *visibilità* (o il timeout del *blocco* ). Il valore di questo timeout è di un minuto e non può essere modificato.

La proprietà **Max Delivery count** nell'hub Internet determina il numero massimo di volte in cui un messaggio può passare tra gli stati accodati e quelli invisibili. Dopo tale numero di transizioni, l'hub Internet viene impostato sullo stato del messaggio nonrecapitabile. Analogamente, l'hub di Internet delle cose imposta lo stato di un messaggio su messaggi non recapitabili dopo la data di scadenza. Per ulteriori informazioni, vedere [durata (TTL](#message-expiration-time-to-live)).

L'articolo [come inviare messaggi da cloud a dispositivo con l'hub](iot-hub-csharp-csharp-c2d.md) di questo articolo illustra come inviare messaggi da cloud a dispositivo dal cloud e riceverli in un dispositivo.

Un dispositivo in genere completa un messaggio da cloud a dispositivo quando la perdita del messaggio non influisce sulla logica dell'applicazione. Un esempio potrebbe essere quando il dispositivo ha salvato in modo permanente il contenuto del messaggio localmente oppure ha eseguito correttamente un'operazione. Il messaggio può inoltre contenere informazioni temporanee, la cui perdita non influirà sulla funzionalità dell'applicazione. In alcuni casi, per le attività con esecuzione prolungata è possibile:

* Completare il messaggio da cloud a dispositivo dopo che il dispositivo ha salvato in modo permanente la descrizione dell'attività nell'archivio locale.

* Inviare al back-end della soluzione una notifica con uno o più messaggi da dispositivo a cloud in diverse fasi di avanzamento dell'attività.

## <a name="message-expiration-time-to-live"></a>Scadenza del messaggio (durata)

Ogni messaggio da cloud a dispositivo ha una scadenza. Questa volta viene impostato in uno dei seguenti elementi:

* Proprietà **ExpiryTimeUtc** nel servizio
* Hub Internet delle cose, usando la *durata (TTL* ) predefinita specificata come proprietà dell'hub Internet.

Vedere [Opzioni di configurazione da cloud a dispositivo](#cloud-to-device-configuration-options).

Un modo comune per sfruttare la scadenza dei messaggi e per evitare l'invio di messaggi a dispositivi disconnessi consiste nell'impostare un breve *intervallo di tempo per* i valori dinamici. Questo approccio consente di ottenere lo stesso risultato della gestione dello stato di connessione del dispositivo, ma è più efficiente. Quando si richiedono i riconoscimenti dei messaggi, l'hub Internet informa i dispositivi:

* sono abilitati a ricevere messaggi.
* sono offline, oppure l'operazione non è riuscita.

## <a name="message-feedback"></a>Commenti sui messaggi

Quando si invia un messaggio da cloud a dispositivo, il servizio può richiedere il recapito dei commenti per messaggio sullo stato finale del messaggio. A tale scopo, impostare la proprietà dell'applicazione **iothub-ACK** nel messaggio da cloud a dispositivo inviato a uno dei quattro valori seguenti:

| Valore della proprietà ACK | Comportamento |
| ------------ | -------- |
| none     | L'hub Internet delle cose non genera un messaggio di feedback (comportamento predefinito). |
| positivo | Se il messaggio da cloud a dispositivo raggiunge lo stato *completato* , l'hub Internet genera un messaggio di feedback. |
| negativo | Se il messaggio da cloud a dispositivo raggiunge lo stato non recapitabile, l'hub Internet genera un messaggio di feedback. |
| completo     | In entrambi i casi l'hub Internet genera un messaggio di feedback. |

Se il valore **ACK** è *pieno*e non si riceve un messaggio di feedback, significa che il messaggio di feedback è scaduto. Il servizio non può sapere cosa è successo al messaggio originale. In pratica, un servizio deve garantire che sia possibile elaborare i commenti prima della scadenza. Il tempo di scadenza massimo è di due giorni, che lascia il tempo per riportare il servizio in caso di errore.

Come illustrato negli [endpoint](iot-hub-devguide-endpoints.md), l'hub Internet delle cose fornisce feedback tramite un endpoint per il servizio, */messages/servicebound/feedback*, come messaggi. La semantica di ricezione per i commenti è uguale a quella dei messaggi da cloud a dispositivo. Quando è possibile, i commenti sui messaggio vengono riuniti in batch in un unico messaggio con il formato seguente:

| Proprietà     | Descrizione |
| ------------ | ----------- |
| EnqueuedTime | Timestamp che indica quando il messaggio di feedback è stato ricevuto dall'hub |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

Il corpo è una matrice serializzata con JSON dei record, ognuno con le proprietà seguenti:

| Proprietà           | DESCRIZIONE |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Timestamp che indica quando si è verificato il risultato del messaggio (ad esempio, l'hub ha ricevuto il messaggio di feedback o il messaggio originale è scaduto) |
| OriginalMessageId  | *MessageID* del messaggio da cloud a dispositivo a cui si riferiscono le informazioni sul feedback |
| StatusCode         | Una stringa obbligatoria, usata nei messaggi di feedback generati dall'hub Internet delle cose: <br/> *Success* <br/> *Scaduto* <br/> *DeliveryCountExceeded* <br/> *Respinto* <br/> *Eliminati* |
| Descrizione        | Valori stringa per *statusCode* |
| DeviceId           | *DeviceID* del dispositivo di destinazione del messaggio da cloud a dispositivo a cui si riferisce questa parte del feedback |
| DeviceGenerationId | *DeviceGenerationId* del dispositivo di destinazione del messaggio da cloud a dispositivo a cui si riferisce questa parte del feedback |

Affinché il messaggio da cloud a dispositivo metta in correlazione il feedback con il messaggio originale, è necessario che il servizio specifichi un *MessageID*.

Il corpo di un messaggio di feedback è illustrato nel codice seguente:

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

**Feedback in sospeso per i dispositivi eliminati**

Quando un dispositivo viene eliminato, vengono eliminati anche eventuali commenti in sospeso. Il feedback del dispositivo viene inviato in batch. Se un dispositivo viene eliminato nella finestra Narrow (spesso inferiore a 1 secondo) tra il momento in cui il dispositivo conferma la ricezione del messaggio e quando viene preparato il successivo batch di feedback, il feedback non verrà eseguito.

È possibile risolvere questo comportamento attendendo un periodo di tempo per l'arrivo di feedback in sospeso prima di eliminare il dispositivo. Si presuppone che il feedback dei messaggi correlati venga perso dopo l'eliminazione di un dispositivo.

## <a name="cloud-to-device-configuration-options"></a>Opzioni di configurazione da cloud a dispositivo

Ogni hub IoT espone le opzioni di configurazione seguenti per la messaggistica da cloud a dispositivo:

| Proprietà                  | Descrizione | Intervallo e valore predefinito |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | TTL predefinito per i messaggi da cloud a dispositivo | Intervallo di ISO_8601 fino a 2 giorni (minimo 1 minuto); predefinita 1 ora |
| maxDeliveryCount          | Numero massimo di recapiti per le code per dispositivo da cloud a dispositivo | da 1 a 100; predefinita 10 |
| feedback.ttlAsIso8601     | Conservazione per i messaggi di feedback associati al servizio | Intervallo di ISO_8601 fino a 2 giorni (minimo 1 minuto); predefinita 1 ora |
| feedback.maxDeliveryCount | Numero massimo di recapiti per la coda di commenti | da 1 a 100; predefinita 100 |

Per altre informazioni su come impostare queste opzioni di configurazione, vedere [Creare hub IoT](iot-hub-create-through-portal.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sugli SDK che è possibile usare per ricevere i messaggi da cloud a dispositivo, vedere gli [SDK Azure](iot-hub-devguide-sdks.md).

Per provare a ricevere i messaggi da cloud a dispositivo, vedere l'esercitazione [Invio da cloud a dispositivo](iot-hub-csharp-csharp-c2d.md).
