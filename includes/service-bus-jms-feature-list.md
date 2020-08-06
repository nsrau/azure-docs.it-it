---
title: includere file
description: includere file
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 6/9/2020
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 9030080d0b8c8e032cb2992a62275efcdb04aabc
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87798138"
---
La tabella seguente elenca le funzionalità di Java Message Service (JMS) attualmente supportate dal bus di servizio di Azure. Mostra anche le funzionalità non supportate.


| Feature | API |Stato |
|---|---|---|
| Code   | <ul> <li> JMSContext. createQueue (String QueueName) </li> </ul>| **Supportato** |
| Argomenti   | <ul> <li> JMSContext. createTopic (stringa topicName) </li> </ul>| **Supportato** |
| Code temporanee |<ul> <li> JMSContext.createTemporaryQueue() </li> </ul>| **Supportato** |
| Argomenti temporanei |<ul> <li> JMSContext.createTemporaryTopic() </li> </ul>| **Supportato** |
| Produttore del messaggio/<br/> JMSProducer |<ul> <li> JMSContext.createProducer() </li> </ul>| **Supportato** |
| Browser di Accodamento |<ul> <li> JMSContext. createBrowser (coda coda) </li> <li> JMSContext. createBrowser (coda coda, stringa messageSelector) </li> </ul> | **Supportato** |
| Consumer di messaggi/ <br/> JMSConsumer | <ul> <li> JMSContext. createConsumer (destinazione destinazione) </li> <li> JMSContext. createConsumer (destinazione destinazione, stringa messageSelector) </li> <li> JMSContext. createConsumer (destinazione destinazione, stringa messageSelector, valore booleano NoLocal)</li> </ul>  <br/> NoLocal non è attualmente supportato | **Supportato** |
| Sottoscrizioni durevoli condivise | <ul> <li> JMSContext. createSharedDurableConsumer (argomento argomento, nome stringa) </li> <li> JMSContext. createSharedDurableConsumer (argomento argomento, nome stringa, stringa messageSelector) </li> </ul>| **Supportato**|
| Sottoscrizioni durevoli non condivise | <ul> <li> JMSContext. createDurableConsumer (argomento argomento, nome stringa) </li> <li> createDurableConsumer (argomento argomento, nome stringa, stringa messageSelector, booleano NoLocal) </li> </ul> <br/> NoLocal non è attualmente supportato e deve essere impostato su false | **Supportato** |
| Sottoscrizioni non durevoli condivise |<ul> <li> JMSContext. createSharedConsumer (argomento argomento, stringa sharedSubscriptionName) </li> <li> JMSContext. createSharedConsumer (argomento argomento, stringa sharedSubscriptionName, stringa messageSelector) </li> </ul> | **Supportato** |
| Sottoscrizioni non durevoli non condivise |<ul> <li> JMSContext. createConsumer (destinazione destinazione) </li> <li> JMSContext. createConsumer (destinazione destinazione, stringa messageSelector) </li> <li> JMSContext. createConsumer (destinazione destinazione, stringa messageSelector, valore booleano NoLocal) </li> </ul> <br/> NoLocal non è attualmente supportato e deve essere impostato su false | **Supportato** |
| Selettori di messaggi | dipende dal consumer creato | **Supportato** |
| Ritardo recapito (messaggi pianificati) | <ul> <li> JMSProducer. setDeliveryDelay (Long deliveryDelay) </li> </ul>|**Supportato**|
| Messaggio creato |<ul> <li> JMSContext. createMessage () </li> <li> JMSContext.createBytesMessage() </li> <li> JMSContext.createMapMessage() </li> <li> JMSContext. createObjectMessage (oggetto serializzabile) </li> <li> JMSContext.createStreamMessage() </li> <li> JMSContext.createTextMessage() </li> <li> JMSContext. createTextMessage (testo stringa) </li> </ul>| **Supportato** |
| Transazioni distribuite || Non supportate |
