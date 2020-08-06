---
title: Messaggistica del bus di servizio di Azure-entità del servizio messaggi Java (anteprima)
description: Questo articolo fornisce una panoramica delle entità di messaggistica del bus di servizio di Azure accessibili tramite l'API del servizio messaggi Java.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: 1a7fe3d6355146ccf0fce50266a6f3b8da5231b3
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87801799"
---
# <a name="java-message-service-jms-20-entities-preview"></a>Entità Java Message Service (JMS) 2,0 (anteprima)

Le applicazioni client che si connettono al bus di servizio di Azure Premium e usano la [libreria JMS del bus di servizio di Azure](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) possono sfruttare le entità seguenti.

## <a name="queues"></a>Code

Le code in JMS sono semanticamente confrontabili con le [code del bus di servizio](service-bus-queues-topics-subscriptions.md#queues)tradizionali.

Per creare una coda, usare i metodi seguenti nella `JMSContext` classe-

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Argomenti

Gli argomenti di JMS sono semanticamente confrontabili con gli argomenti tradizionali del [bus di servizio](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions).

Per creare un argomento, usare i metodi seguenti nella `JMSContext` classe-

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Code temporanee

Quando un'applicazione client richiede un'entità temporanea esistente per la durata dell'applicazione, può utilizzare le code temporanee. Questi vengono usati nel modello [Request/Reply](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) .

Per creare una coda temporanea, utilizzare i metodi seguenti nella `JMSContext` classe-

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Argomenti temporanei

Analogamente alle code temporanee, sono disponibili argomenti temporanei per abilitare la pubblicazione/sottoscrizione tramite un'entità temporanea esistente per la durata dell'applicazione.

Per creare un argomento temporaneo, usare i metodi seguenti nella `JMSContext` classe-

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Sottoscrizioni di Java Message Service (JMS)

Sebbene siano semanticamente simili alle [sottoscrizioni](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (ovvero esistono in un argomento e abilitano la semantica di pubblicazione/sottoscrizione), la specifica di Java Message Service introduce i concetti relativi agli attributi **condivisi**, non **condivisi**, **durevoli** e **non durevoli** per una determinata sottoscrizione.

> [!NOTE]
> Le sottoscrizioni seguenti sono disponibili nel livello Premium del bus di servizio di Azure per l'anteprima per le applicazioni client che si connettono al bus di servizio di Azure tramite la [libreria JMS del bus di servizio](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)
>
> Per l'anteprima pubblica, queste sottoscrizioni non possono essere create usando il portale di Azure.
>

### <a name="shared-durable-subscriptions"></a>Sottoscrizioni durevoli condivise

Una sottoscrizione durevole condivisa viene utilizzata quando tutti i messaggi pubblicati in un argomento devono essere ricevuti ed elaborati da un'applicazione, indipendentemente dal fatto che l'applicazione utilizzi attivamente dalla sottoscrizione.

Poiché si tratta di una sottoscrizione condivisa, qualsiasi applicazione autenticata per la ricezione dal bus di servizio può ricevere dalla sottoscrizione.

Per creare una sottoscrizione durevole condivisa, usare i metodi seguenti nella `JMSContext` classe-

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

La sottoscrizione durevole condivisa continua a esistere a meno che non venga eliminata usando il `unsubscribe` metodo nella `JMSContext` classe.

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>Sottoscrizioni durevoli non condivise

Analogamente a una sottoscrizione durevole condivisa, viene usata una sottoscrizione durevole non condivisa quando tutti i messaggi pubblicati in un argomento devono essere ricevuti ed elaborati da un'applicazione, indipendentemente dal fatto che l'applicazione stia sempre utilizzando attivamente la sottoscrizione.

Tuttavia, poiché si tratta di una sottoscrizione non condivisa, solo l'applicazione che ha creato la sottoscrizione può ricevere da tale sottoscrizione.

Per creare una sottoscrizione durevole non condivisa, usare i metodi seguenti della `JMSContext` classe: 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> La `noLocal` funzionalità non è attualmente supportata e viene ignorata.
>

La sottoscrizione durevole non condivisa continua a esistere a meno che non venga eliminata utilizzando il `unsubscribe` metodo nella `JMSContext` classe.

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>Sottoscrizioni non durevoli condivise

Una sottoscrizione non durevole condivisa viene usata quando più applicazioni client devono ricevere ed elaborare messaggi da una singola sottoscrizione, solo fino a quando non utilizzano attivamente/riceventi.

Poiché la sottoscrizione non è durevole, non è persistente. I messaggi non vengono ricevuti da questa sottoscrizione quando non vi sono utenti attivi.

Per creare una sottoscrizione non durevole condivisa, creare un `JmsConsumer` come illustrato nei metodi seguenti della `JMSContext` classe:

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

La sottoscrizione non durevole condivisa continua a esistere fino a quando non vi sono utenti attivi che ricevono.

### <a name="unshared-non-durable-subscriptions"></a>Sottoscrizioni non durevoli non condivise

Una sottoscrizione non durevole non condivisa viene usata quando l'applicazione client deve ricevere ed elaborare un messaggio da una sottoscrizione, solo finché non lo utilizza attivamente. In questa sottoscrizione possono esistere solo un consumer, ovvero il client che ha creato la sottoscrizione.

Poiché la sottoscrizione non è durevole, non è persistente. I messaggi non vengono ricevuti da questa sottoscrizione quando non è presente alcun consumer attivo.

Per creare una sottoscrizione non durevole non condivisa, creare un `JMSConsumer` come illustrato nei metodi seguenti della `JMSContext` classe:

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> La `noLocal` funzionalità non è attualmente supportata e viene ignorata.
>

La sottoscrizione non durevole non condivisa continua a esistere fino a quando non viene ricevuta da un utente attivo.

### <a name="message-selectors"></a>Selettori di messaggi

Analogamente ai **filtri e alle azioni** esistenti per le normali sottoscrizioni del bus di servizio, esistono **selettori di messaggi** per le sottoscrizioni JMS.

I selettori di messaggi possono essere configurati in ognuna delle sottoscrizioni JMS ed esistere come condizione di filtro nelle proprietà dell'intestazione del messaggio. Vengono recapitati solo i messaggi con proprietà di intestazione corrispondenti all'espressione del selettore dei messaggi. Un valore null o una stringa vuota indica che non è presente alcun selettore di messaggi per la sottoscrizione o il consumer JMS.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi e informazioni sull'uso della messaggistica del bus di servizio, vedere gli argomenti avanzati seguenti:

* [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
* [Usare l'API del servizio messaggi Java 2,0 con il bus di servizio Premium di Azure (anteprima)](how-to-use-java-message-service-20.md)



