---
title: Informazioni sulla messaggistica da cloud a dispositivo dell'hub IoT di Azure | Microsoft Docs
description: Questa guida per gli sviluppatori illustra come usare la messaggistica da cloud a dispositivo con l'hub IoT.This developer guide discusses how to use cloud-to-device messaging with your IoT hub. Include informazioni sul ciclo di vita dei messaggi e sulle opzioni di configurazione.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt
ms.openlocfilehash: 307ab47c1f7498f71e61108a616d35ef1d4f61c9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730005"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Inviare messaggi da cloud a dispositivo da un hub IoTSend cloud-to-device messages from an IoT hub

Per inviare notifiche unidirezionali a un'app per dispositivi dal back-end della soluzione, invia messaggi da cloud a dispositivo dall'hub IoT al dispositivo. Per una descrizione di altre opzioni da cloud a dispositivo supportate dall'hub IoT di Azure, vedere Linee guida per le comunicazioni da [cloud a dispositivo.](iot-hub-devguide-c2d-guidance.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I messaggi da cloud a dispositivo vengono inviati tramite un endpoint pubblico servizio, */messages/devicebound*. Un dispositivo riceve quindi i messaggi tramite un endpoint specifico del dispositivo, */devices/*

Per indirizzare ogni messaggio da cloud a dispositivo in un singolo dispositivo, l'hub IoT imposta la proprietà **to** su */devices/*

Ogni coda di dispositivo contiene al massimo 50 messaggi da cloud a dispositivo. Per tentare di inviare più messaggi allo stesso dispositivo, viene generato un errore.

## <a name="the-cloud-to-device-message-life-cycle"></a>Ciclo di vita dei messaggi da cloud a dispositivo

Per garantire il recapito dei messaggi almeno una volta, l'hub IoT mantiene i messaggi da cloud a dispositivo nelle code per dispositivo. Affinché l'hub IoT rimuova i messaggi dalla coda, i dispositivi devono riconoscere in modo esplicito *il completamento*. Questo approccio garantisce la resilienza rispetto a errori di connettività e del dispositivo.

Il grafico dello stato del ciclo di vita viene visualizzato nel diagramma seguente:The life-cycle state graph is displayed in the following diagram:

![Ciclo di vita dei messaggi da cloud a dispositivo](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Quando il servizio hub IoT invia un messaggio a un dispositivo, il servizio imposta lo stato del messaggio su *Enqueued*. Quando un dispositivo desidera *ricevere* un messaggio, l'hub IoT *blocca* il messaggio impostando lo stato *su Invisibile*. Questo stato consente ad altri thread del dispositivo di iniziare a ricevere altri messaggi. Quando un thread del dispositivo completa l'elaborazione di un messaggio, invia una notifica all'hub IoT *completando* il messaggio. L'hub IoT imposta quindi lo stato *su Completato*.

Un dispositivo può anche:

* *Rifiutare* il messaggio, che fa sì che l'hub IoT lo imposti allo stato *Lettere morte.* I dispositivi che si connettono tramite il protocollo MQTT (Message Queuing Telemetry Transport) non possono rifiutare i messaggi da cloud a dispositivo.

* *Abbandonare* il messaggio, che fa sì che l'hub IoT reinseri il messaggio nella coda, con lo stato impostato su *Accodato*. I dispositivi che si connettono tramite il protocollo MQTT non possono abbandonare i messaggi da cloud a dispositivo.

Un thread potrebbe non riuscire a elaborare un messaggio senza notificare l'hub IoT.A thread could fail to process a message without notifying the IoT hub. In questo caso, i messaggi passano automaticamente dallo stato *Invisibile* allo stato *In coda* dopo un timeout di *visibilità* (o timeout di *blocco).* Il valore di questo timeout è di un minuto e non può essere modificato.

La proprietà numero massimo di **recapiti** nell'hub IoT determina il numero massimo di volte in cui un messaggio può eseguire la transizione tra gli stati *Accodato* e *Invisibile.* Dopo tale numero di transizioni, l'hub IoT imposta lo stato del messaggio su *Dead lettered*. Analogamente, l'hub IoT imposta lo stato di un messaggio su *Dead lettered* dopo l'ora di scadenza. Per ulteriori informazioni, consultate [Tempo di vita.](#message-expiration-time-to-live)

L'articolo [Come inviare messaggi da cloud a dispositivo con l'hub IoT](iot-hub-csharp-csharp-c2d.md) illustra come inviare messaggi da cloud a dispositivo dal cloud e riceverli su un dispositivo.

Un dispositivo completa in genere un messaggio da cloud a dispositivo quando la perdita del messaggio non influisce sulla logica dell'applicazione. Un esempio potrebbe essere quando il dispositivo ha reso persistente il contenuto del messaggio localmente o ha eseguito correttamente un'operazione. Il messaggio potrebbe anche contenere informazioni temporanee, la cui perdita non avrebbe alcun impatto sulla funzionalità dell'applicazione. In alcuni casi, per le attività con esecuzione prolungata è possibile:

* Completare il messaggio da cloud a dispositivo dopo che il dispositivo ha salvato in modo permanente la descrizione dell'attività nell'archiviazione locale.

* Inviare al back-end della soluzione una notifica con uno o più messaggi da dispositivo a cloud in diverse fasi di avanzamento dell'attività.

## <a name="message-expiration-time-to-live"></a>Scadenza del messaggio (durata)

Ogni messaggio da cloud a dispositivo ha una scadenza. Questo tempo è impostato da uno dei seguenti:

* La proprietà **ExpiryTimeUtc** nel servizio
* L'hub IoT, usando l'ora di *vita* predefinita specificata come proprietà dell'hub IoT

Vedere [Opzioni di configurazione da cloud a dispositivo](#cloud-to-device-configuration-options).

Un modo comune per sfruttare la scadenza di un messaggio ed evitare di inviare messaggi ai dispositivi disconnessi consiste nell'impostare valori di tempo brevi per la *vita.* Questo approccio consente di ottenere lo stesso risultato ottenuto mantenendo lo stato di connessione del dispositivo, ma è più efficiente. Quando richiedi i riconoscimenti dei messaggi, l'hub IoT notifica quali dispositivi sono:

* sono abilitati a ricevere messaggi.
* sono offline, oppure l'operazione non è riuscita.

## <a name="message-feedback"></a>Commenti sui messaggi

Quando si invia un messaggio da cloud a dispositivo, il servizio può richiedere il recapito di feedback per messaggio sullo stato finale del messaggio. A tale scopo, impostare la proprietà **dell'applicazione iothub-ack** nel messaggio cloud-to-device che viene inviato a uno dei seguenti quattro valori:

| Valore della proprietà Ack | Comportamento |
| ------------ | -------- |
| none     | L'hub IoT non genera un messaggio di feedback (comportamento predefinito). |
| positivo | Se il messaggio da cloud a dispositivo raggiunge lo stato *Completato,* l'hub IoT genera un messaggio di feedback. |
| negativo | Se il messaggio da cloud a dispositivo raggiunge lo stato *Letteramorta,* l'hub IoT genera un messaggio di feedback. |
| completi     | L'hub IoT genera un messaggio di feedback in entrambi i casi. |

Se il valore **Ack** è *pieno*e non si riceve un messaggio di feedback, significa che il messaggio di feedback è scaduto. Il servizio non può sapere cosa è successo al messaggio originale. In pratica, un servizio deve garantire che sia possibile elaborare i commenti prima della scadenza. L'ora di scadenza massima è di due giorni, che lascia il tempo di eseguire nuovamente il servizio se si verifica un errore.

Come spiegato in [Endpoints](iot-hub-devguide-endpoints.md), l'hub IoT invia commenti e suggerimenti tramite un endpoint rivolto al servizio, */messages/servicebound/feedback*, come messaggi. La semantica di ricezione per i commenti è uguale a quella dei messaggi da cloud a dispositivo. Quando è possibile, i commenti sui messaggio vengono riuniti in batch in un unico messaggio con il formato seguente:

| Proprietà     | Descrizione |
| ------------ | ----------- |
| EnqueuedTime | Timestamp che indica quando il messaggio di feedback è stato ricevuto dall'hub |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

Il corpo è una matrice serializzata con JSON dei record, ognuno con le proprietà seguenti:

| Proprietà           | Descrizione |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Indicatore di data e ora che indica quando si è verificato il risultato del messaggio (ad esempio, l'hub ha ricevuto il messaggio di feedback o il messaggio originale è scaduto) |
| OriginalMessageId  | *MessageId* del messaggio da cloud a dispositivo a cui si riferiscono queste informazioni di feedback |
| StatusCode         | Una stringa obbligatoria, usata nei messaggi di feedback generati dall'hub IoT: <br/> *Successo* <br/> *Scaduto* <br/> *DeliveryCountExceeded (DeliveryCountExceeded)* <br/> *Rifiutato* <br/> *Eliminati* |
| Descrizione        | Valori stringa per *StatusCodeString* values for StatusCode |
| deviceId           | *DeviceId* del dispositivo di destinazione del messaggio da cloud a dispositivo a cui si riferisce questo feedback |
| DeviceGenerationId | *DeviceGenerationId* del dispositivo di destinazione del messaggio da cloud a dispositivo a cui si riferisce questo feedback |

Affinché il messaggio cloud-to-device correli il feedback con il messaggio originale, il servizio deve specificare un *MessageId*.

Il corpo di un messaggio di feedback viene visualizzato nel codice seguente:The body of a feedback message is shown in the following code:

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

**Invio di commenti e suggerimenti per i dispositivi eliminati**

Quando un dispositivo viene eliminato, viene eliminato anche qualsiasi feedback in sospeso. Il feedback del dispositivo viene inviato in batch. Se un dispositivo viene eliminato nella finestra stretta (spesso meno di 1 secondo) tra quando il dispositivo conferma la ricezione del messaggio e quando viene preparato il batch di feedback successivo, il feedback non si verificherà.

È possibile risolvere questo comportamento attendendo un periodo di tempo per il feedback in sospeso per arrivare prima di eliminare il dispositivo. Il feedback dei messaggi correlati deve essere considerato perso dopo l'eliminazione di un dispositivo.

## <a name="cloud-to-device-configuration-options"></a>Opzioni di configurazione da cloud a dispositivo

Ogni hub IoT espone le opzioni di configurazione seguenti per la messaggistica da cloud a dispositivo:

| Proprietà                  | Descrizione | Intervallo e valore predefinito |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | TTL predefinito per i messaggi da cloud a dispositivo | ISO_8601 intervallo fino a 2 giorni (minimo 1 minuto); valore predefinito: 1 ora |
| maxDeliveryCount          | Numero massimo di recapiti per le code da cloud a dispositivoMaximum delivery count for cloud-to-device queues | da 1 a 100; valore predefinito: 10 |
| feedback.ttlAsIso8601     | Conservazione per i messaggi di feedback associati al servizio | ISO_8601 intervallo fino a 2 giorni (minimo 1 minuto); valore predefinito: 1 ora |
| feedback.maxDeliveryCount | Numero massimo di recapiti per la coda di feedbackMaximum delivery count for the feedback queue | da 1 a 100; valore predefinito: 10 |
| feedback.lockDurationAsIso8601 | Numero massimo di recapiti per la coda di feedbackMaximum delivery count for the feedback queue | ISO_8601 intervallo da 5 a 300 secondi (minimo 5 secondi); valore predefinito: 60 secondi. |

È possibile impostare le opzioni di configurazione in uno dei seguenti modi:

* **Portale di Azure:** in **Impostazioni** nell'hub IoT selezionare **Endpoint predefiniti** ed espandere **Messaggistica da cloud a dispositivo.** L'impostazione delle proprietà **feedback.maxDeliveryCount** e **feedback.lockDurationAsIso8601** non è attualmente supportata nel portale di Azure.

    ![Impostare le opzioni di configurazione per la messaggistica da cloud a dispositivo nel portaleSet configuration options for cloud-to-device messaging in the portal](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Interfaccia della riga di comando**di Azure: usare il comando az iot hub update:Azure CLI : Use the [az iot hub update](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-update) command:

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sugli SDK che è possibile usare per ricevere messaggi da cloud a dispositivo, vedere [SDK di Azure IoT.](iot-hub-devguide-sdks.md)

Per provare a ricevere i messaggi da cloud a dispositivo, vedere l'esercitazione [Invio da cloud a dispositivo](iot-hub-csharp-csharp-c2d.md).
