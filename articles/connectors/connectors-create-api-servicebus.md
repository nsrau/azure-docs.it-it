<properties
pageTitle="Informazioni su come usare il connettore del bus di servizio di Azure nelle app per la logica | Microsoft Azure"
description="Creare app per la logica con Servizio app di Azure. Connettersi al bus di servizio di Azure per inviare e ricevere messaggi. È possibile eseguire varie azioni, ad esempio inviare alla coda, inviare all'argomento, ricevere dalla coda, ricevere dalla sottoscrizione, e così via."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/02/2016"
ms.author="deonhe"/>

# Introduzione al connettore del bus di servizio di Azure

Connettersi al bus di servizio di Azure per inviare e ricevere messaggi. È possibile eseguire varie azioni, ad esempio inviare alla coda, inviare all'argomento, ricevere dalla coda, ricevere dalla sottoscrizione, e così via.

Per usare [qualsiasi connettore](./apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Connettersi al bus di servizio di Azure

Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una [connessione](./connectors-overview.md) fornisce la connettività tra un'app per la logica e un altro servizio.

### Creare una connessione al bus di servizio di Azure

>[AZURE.INCLUDE [Passaggi per creare una connessione al bus di servizio di Azure](../../includes/connectors-create-api-servicebus.md)]

## Usare un trigger del bus di servizio di Azure

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.INCLUDE [Passaggi per creare un trigger del bus di servizio](../../includes/connectors-create-api-servicebus-trigger.md)]

## Usare un'azione del bus di servizio di Azure

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

[AZURE.INCLUDE [Passaggi per creare un'azione del bus di servizio](../../includes/connectors-create-api-servicebus-action.md)]

## Dettagli tecnici

Ecco i dettagli sui trigger, le azioni e le risposte che la connessione supporta:

## Trigger del bus di servizio di Azure

Il bus di servizio di Azure supporta i trigger seguenti:

|Trigger | Descrizione|
|--- | ---|
|[When a message is received in a queue](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue) (Quando un messaggio viene ricevuto in una coda)|Questa operazione attiva un flusso quando viene ricevuto un messaggio in una coda.|
|[When a message is received in a topic subscription](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription) (Quando un messaggio viene ricevuto nella sottoscrizione di un argomento)|Questa operazione attiva un flusso quando viene ricevuto un messaggio nella sottoscrizione di un argomento.|


## Azioni del bus di servizio di Azure

Il bus di servizio Azure supporta le azioni seguenti:


|Azione|Descrizione|
|--- | ---|
|[Send message](connectors-create-api-servicebus.md#send-message)|Questa operazione Invia un messaggio a una coda o argomento.|
### Informazioni dettagliate sulle azioni

Ecco i dettagli per le azioni e i trigger per questo connettore con le relative risposte:



### Send message
Questa operazione Invia un messaggio a una coda o argomento.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|ContentData*|Content|Contenuto del messaggio|
|ContentType|Content Type|Tipo del contenuto del messaggio|
|Proprietà|Proprietà|Coppie chiave-valore per ogni proprietà negoziata|
|entityName*|Nome coda/argomento|Nome della coda o dell'argomento|

Sono disponibili anche questi parametri avanzati:

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|MessageId|ID del messaggio|Questo è un valore definito dall'utente che il bus di servizio può usare per identificare i messaggi duplicati, se abilitato.|
|To|To|Inviare all'indirizzo|
|ReplyTo|Rispondi a|Indirizzo della coda a cui rispondere|
|ReplyToSessionId|ID sessione risposta|Identificatore della sessione a cui rispondere|
|Etichetta|Etichetta|Etichetta specifica dell'applicazione|
|ScheduledEnqueueTimeUtc|ScheduledEnqueueTimeUtc|Data e ora, in UTC, in cui il messaggio sarà aggiunto alla coda|
|SessionId|ID sessione|Identificatore della sessione|
|CorrelationId|ID correlazione|Identificatore della correlazione|
|TimeToLive|Durata (TTL)|Si tratta della durata di validità del messaggio espressa in tick. La durata inizia da quando il messaggio viene inviato al bus di servizio.|



* indica che la proprietà è obbligatoria




### When a message is received in a queue (Quando un messaggio viene ricevuto in una coda)
Questa operazione attiva un flusso quando viene ricevuto un messaggio in una coda.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|queueName*|Nome della coda|Nome della coda|


* indica che la proprietà è obbligatoria

#### Dettagli dell'output

ServiceBusMessage: questo oggetto presenta il contenuto e le proprietà di un messaggio del bus di servizio.


| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|ContentData|string|Contenuto del messaggio|
|ContentType|string|Tipo del contenuto del messaggio|
|Proprietà|object|Coppie chiave-valore per ogni proprietà negoziata|
|MessageId|string|Questo è un valore definito dall'utente che il bus di servizio può usare per identificare i messaggi duplicati, se abilitato.|
|To|string|Inviare all'indirizzo|
|ReplyTo|string|Indirizzo della coda a cui rispondere|
|ReplyToSessionId|string|Identificatore della sessione a cui rispondere|
|Etichetta|string|Etichetta specifica dell'applicazione|
|ScheduledEnqueueTimeUtc|string|Data e ora, in UTC, in cui il messaggio sarà aggiunto alla coda|
|SessionId|string|Identificatore della sessione|
|CorrelationId|string|Identificatore della correlazione|
|TimeToLive|string|Si tratta della durata di validità del messaggio espressa in tick. La durata inizia da quando il messaggio viene inviato al bus di servizio.|




### When a message is received in a topic subscription (Quando un messaggio viene ricevuto nella sottoscrizione di un argomento)
Questa operazione attiva un flusso quando viene ricevuto un messaggio nella sottoscrizione di un argomento.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|topicName*|Nome argomento|Nome dell'argomento|
|subscriptionName*|Nome sottoscrizione argomento|Nome della sottoscrizione dell'argomento|


* indica che la proprietà è obbligatoria

#### Dettagli dell'output

ServiceBusMessage: questo oggetto presenta il contenuto e le proprietà di un messaggio del bus di servizio.


| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|ContentData|string|Contenuto del messaggio|
|ContentType|string|Tipo del contenuto del messaggio|
|Proprietà|object|Coppie chiave-valore per ogni proprietà negoziata|
|MessageId|string|Questo è un valore definito dall'utente che il bus di servizio può usare per identificare i messaggi duplicati, se abilitato.|
|To|string|Inviare all'indirizzo|
|ReplyTo|string|Indirizzo della coda a cui rispondere|
|ReplyToSessionId|string|Identificatore della sessione a cui rispondere|
|Etichetta|string|Etichetta specifica dell'applicazione|
|ScheduledEnqueueTimeUtc|string|Data e ora, in UTC, in cui il messaggio sarà aggiunto alla coda|
|SessionId|string|Identificatore della sessione|
|CorrelationId|string|Identificatore della correlazione|
|TimeToLive|string|Si tratta della durata di validità del messaggio espressa in tick. La durata inizia da quando il messaggio viene inviato al bus di servizio.|



## Risposte HTTP

Le azioni e i trigger riportati sopra possono restituire uno o più dei seguenti codici di stato HTTP:

|Nome|Descrizione|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto.|
|default|Operazione non riuscita.|

## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!----HONumber=AcomDC_0803_2016-->
