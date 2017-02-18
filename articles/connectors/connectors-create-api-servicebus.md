---
title: Informazioni su come usare il connettore del bus di servizio di Azure nelle app per la logica | Documentazione Microsoft
description: "Creare app per la logica con Servizio app di Azure. Connettersi al bus di servizio di Azure per inviare e ricevere messaggi. È possibile eseguire varie azioni, ad esempio inviare alla coda, inviare all&quot;argomento, ricevere dalla coda e ricevere dalla sottoscrizione."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/02/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 1e23402cbc63aeb262bfb471745589cc0bbd734f


---
# <a name="get-started-with-the-azure-service-bus-connector"></a>Introduzione al connettore del bus di servizio di Azure
Connettersi al bus di servizio di Azure per inviare e ricevere messaggi. È possibile eseguire varie azioni, ad esempio inviare alla coda, inviare all'argomento, ricevere dalla coda e ricevere dalla sottoscrizione.

Per usare [qualsiasi connettore](apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-service-bus"></a>Connettersi al bus di servizio
Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una connessione al servizio. Una [connessione](connectors-overview.md) fornisce la connettività tra un'app per la logica e un altro servizio.  

> [!INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]
> 
> 

## <a name="use-a-service-bus-trigger"></a>Usare un trigger di bus di servizio
Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]
> 
> 

## <a name="use-a-service-bus-action"></a>Usare un'azione del bus di servizio
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

[!INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]

## <a name="technical-details"></a>Dettagli tecnici
Ecco i dettagli relativi a trigger, azioni e risposte supportati dalla connessione.

### <a name="service-bus-triggers"></a>Trigger del bus di servizio
Il bus di servizio supporta i trigger seguenti:  

| Trigger | Descrizione |
| --- | --- |
| [Quando un messaggio viene ricevuto in una coda](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue) |Questa operazione attiva un flusso quando viene ricevuto un messaggio in una coda. |
| [Quando un messaggio viene ricevuto in una sottoscrizione dell'argomento](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription) |Questa operazione attiva un flusso quando viene ricevuto un messaggio nella sottoscrizione di un argomento. |

### <a name="service-bus-actions"></a>Azioni del bus di servizio
Il bus di servizio supporta le azioni seguenti:

| Azione | Descrizione |
| --- | --- |
| [Invia messaggio](connectors-create-api-servicebus.md#send-message) |Questa operazione Invia un messaggio a una coda o argomento. |

### <a name="action-and-trigger-details"></a>Dettagli relativi ad azioni e trigger
Ecco i dettagli relativi ad azioni e trigger per questo connettore con le relative risposte.

#### <a name="send-message"></a>Send message
| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| ContentData* |Content |Contenuto del messaggio. |
| ContentType |Content Type |Tipo di contenuto del messaggio. |
| Proprietà |Proprietà |Coppie chiave-valore per ogni proprietà negoziata. |
| entityName* |Nome coda/argomento |Nome della coda o dell'argomento. |

Sono disponibili anche questi parametri avanzati:

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| MessageId |ID del messaggio |Valore definito dall'utente che il bus di servizio può usare per identificare i messaggi duplicati, se abilitato. |
| To |To |Indirizzo di destinazione. |
| ReplyTo |Rispondi a |Indirizzo della coda a cui rispondere. |
| ReplyToSessionId |ID sessione risposta |Identificatore della sessione a cui rispondere. |
| Etichetta |Etichetta |Etichetta specifica dell'applicazione. |
| ScheduledEnqueueTimeUtc |ScheduledEnqueueTimeUtc |Data e ora, in UTC, in cui il messaggio sarà aggiunto alla coda. |
| SessionId |ID sessione |Identificatore della sessione. |
| CorrelationId |ID correlazione |Identificatore della correlazione. |
| TimeToLive |Durata (TTL) |Durata di validità del messaggio espressa in tick. La durata inizia dal momento in cui il messaggio viene inviato al bus di servizio. |

* indica che la proprietà è obbligatoria.

#### <a name="when-a-message-is-received-in-a-queue"></a>When a message is received in a queue (Quando un messaggio viene ricevuto in una coda)
| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| queueName* |Nome della coda |Nome della coda. |

* indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
ServiceBusMessage: questo oggetto presenta il contenuto e le proprietà di un messaggio del bus di servizio.

| Nome proprietà | Tipo di dati | Descrizione |
| --- | --- | --- |
| ContentData |stringa |Contenuto del messaggio. |
| ContentType |stringa |Tipo di contenuto del messaggio. |
| Proprietà |object |Coppie chiave-valore per ogni proprietà negoziata. |
| MessageId |stringa |Valore definito dall'utente che il bus di servizio può usare per identificare i messaggi duplicati, se abilitato. |
| To |string |Indirizzo di destinazione. |
| ReplyTo |string |Indirizzo della coda a cui rispondere. |
| ReplyToSessionId |string |Identificatore della sessione a cui rispondere. |
| Etichetta |stringa |Etichetta specifica dell'applicazione. |
| ScheduledEnqueueTimeUtc |stringa |Data e ora, in UTC, in cui il messaggio sarà aggiunto alla coda. |
| SessionId |stringa |Identificatore della sessione. |
| CorrelationId |stringa |Identificatore della correlazione. |
| TimeToLive |stringa |Durata di validità del messaggio espressa in tick. La durata inizia dal momento in cui il messaggio viene inviato al bus di servizio. |

#### <a name="when-a-message-is-received-in-a-topic-subscription"></a>When a message is received in a topic subscription (Quando un messaggio viene ricevuto nella sottoscrizione di un argomento)
| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| topicName* |Nome argomento |Nome dell'argomento. |
| subscriptionName* |Nome sottoscrizione argomento |Nome della sottoscrizione dell'argomento. |

* indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
ServiceBusMessage: questo oggetto presenta il contenuto e le proprietà di un messaggio del bus di servizio.

| Nome proprietà | Tipo di dati | Descrizione |
| --- | --- | --- |
| ContentData |stringa |Contenuto del messaggio. |
| ContentType |stringa |Tipo di contenuto del messaggio. |
| Proprietà |object |Coppie chiave-valore per ogni proprietà negoziata. |
| MessageId |stringa |Valore definito dall'utente che il bus di servizio può usare per identificare i messaggi duplicati, se abilitato. |
| To |string |Indirizzo di destinazione. |
| ReplyTo |string |Indirizzo della coda a cui rispondere. |
| ReplyToSessionId |string |Identificatore della sessione a cui rispondere. |
| Etichetta |stringa |Etichetta specifica dell'applicazione. |
| ScheduledEnqueueTimeUtc |stringa |Data e ora, in UTC, in cui il messaggio sarà aggiunto alla coda. |
| SessionId |stringa |Identificatore della sessione. |
| CorrelationId |stringa |Identificatore della correlazione. |
| TimeToLive |stringa |Durata di validità del messaggio espressa in tick. La durata inizia dal momento in cui il messaggio viene inviato al bus di servizio. |

### <a name="http-responses"></a>Risposte HTTP
Le azioni e i trigger riportati sopra possono restituire uno o più dei codici di stato HTTP seguenti:

| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Richiesta non valida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto. |
| default |Operazione non riuscita. |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).




<!--HONumber=Jan17_HO3-->


