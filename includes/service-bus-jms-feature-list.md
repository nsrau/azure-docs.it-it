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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798138"
---
La tabella seguente elenca le funzionalità di Java Message Service (JMS) attualmente supportate dal bus di servizio di Azure. Mostra anche le funzionalità non supportate.


| Funzionalità | API |Stato |
|---|---|---|
| Code   | <ul> <li> JMSContext. createQueue (String QueueName) </li> </ul>| **Supporto** |
| Argomenti   | <ul> <li> JMSContext. createTopic (stringa topicName) </li> </ul>| **Supporto** |
| Code temporanee |<ul> <li> JMSContext.createTemporaryQueue() </li> </ul>| **Supporto** |
| Argomenti temporanei |<ul> <li> JMSContext.createTemporaryTopic() </li> </ul>| **Supporto** |
| Produttore del messaggio/<br/> JMSProducer |<ul> <li> JMSContext.createProducer() </li> </ul>| **Supporto** |
| Browser di Accodamento |<ul> <li> JMSContext. createBrowser (coda coda) </li> <li> JMSContext. createBrowser (coda coda, stringa messageSelector) </li> </ul> | **Supporto** |
| Consumer di messaggi/ <br/> JMSConsumer | <ul> <li> JMSContext. createConsumer (destinazione destinazione) </li> <li> JMSContext. createConsumer (destinazione destinazione, stringa messageSelector) </li> <li> JMSContext. createConsumer (destinazione destinazione, stringa messageSelector, valore booleano NoLocal)</li> </ul>  <br/> NoLocal non è attualmente supportato | **Supporto** |
| Sottoscrizioni durevoli condivise | <ul> <li> JMSContext. createSharedDurableConsumer (argomento argomento, nome stringa) </li> <li> JMSContext. createSharedDurableConsumer (argomento argomento, nome stringa, stringa messageSelector) </li> </ul>| **Supporto**|
| Sottoscrizioni durevoli non condivise | <ul> <li> JMSContext. createDurableConsumer (argomento argomento, nome stringa) </li> <li> createDurableConsumer (argomento argomento, nome stringa, stringa messageSelector, booleano NoLocal) </li> </ul> <br/> NoLocal non è attualmente supportato e deve essere impostato su false | **Supporto** |
| Sottoscrizioni non durevoli condivise |<ul> <li> JMSContext. createSharedConsumer (argomento argomento, stringa sharedSubscriptionName) </li> <li> JMSContext. createSharedConsumer (argomento argomento, stringa sharedSubscriptionName, stringa messageSelector) </li> </ul> | **Supporto** |
| Sottoscrizioni non durevoli non condivise |<ul> <li> JMSContext. createConsumer (destinazione destinazione) </li> <li> JMSContext. createConsumer (destinazione destinazione, stringa messageSelector) </li> <li> JMSContext. createConsumer (destinazione destinazione, stringa messageSelector, valore booleano NoLocal) </li> </ul> <br/> NoLocal non è attualmente supportato e deve essere impostato su false | **Supporto** |
| Selettori di messaggi | dipende dal consumer creato | **Supporto** |
| Ritardo recapito (messaggi pianificati) | <ul> <li> JMSProducer. setDeliveryDelay (Long deliveryDelay) </li> </ul>|**Supporto**|
| Messaggio creato |<ul> <li> JMSContext. createMessage () </li> <li> JMSContext.createBytesMessage() </li> <li> JMSContext.createMapMessage() </li> <li> JMSContext. createObjectMessage (oggetto serializzabile) </li> <li> JMSContext.createStreamMessage() </li> <li> JMSContext.createTextMessage() </li> <li> JMSContext. createTextMessage (testo stringa) </li> </ul>| **Supporto** |
| Transazioni distribuite || Non supportato |
