---
title: Usare gli argomenti e le sottoscrizioni del bus di servizio di Azure con il pacchetto azure-service-bus Python versione 7.0.0
description: Questo articolo illustra come usare Python per inviare messaggi a un argomento e ricevere messaggi da una sottoscrizione.
documentationcenter: python
author: spelluru
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: devx-track-python
ms.openlocfilehash: 4035eaabb727d0db07553804b6fe94c60ddea64c
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95804814"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-python"></a>Inviare messaggi a un argomento del bus di servizio di Azure e ricevere messaggi dalle sottoscrizioni dell'argomento (Python)
Questo articolo illustra come usare Python per inviare messaggi a un argomento del bus di servizio e ricevere messaggi da una sottoscrizione dell'argomento. 

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure. È possibile attivare i [vantaggi della sottoscrizione Visual Studio o MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oppure registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Seguire la procedura descritta in [Avvio rapido: Usare il portale di Azure per creare un argomento del bus di servizio e le sottoscrizioni dell'argomento](service-bus-quickstart-topics-subscriptions-portal.md). Prendere nota dei valori di stringa di connessione, nome dell'argomento e nome della sottoscrizione. Per questa guida di avvio rapido verrà usata una sola sottoscrizione. 
- Python 2.7 o versione successiva, con il pacchetto [Azure Python SDK][pacchetto Azure Python] installato. Per altre informazioni, vedere la [Guida all'installazione di Python](/azure/developer/python/azure-sdk-install).

## <a name="send-messages-to-a-topic"></a>Inviare messaggi a un argomento

1. Aggiungere l'istruzione import seguente. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Aggiungere le costanti seguenti. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    TOPIC_NAME = "<TOPIC NAME>"
    SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"
    ```
    
    > [!IMPORTANT]
    > - Sostituire `<NAMESPACE CONNECTION STRING>` con la stringa di connessione per lo spazio dei nomi.
    > - Sostituire `<TOPIC NAME>` con il nome dell'argomento.
    > - Sostituire `<SUBSCRIPTION NAME>` con il nome della sottoscrizione dell'argomento. 
3. Aggiungere un metodo per inviare un singolo messaggio.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the topic
        sender.send_messages(message)
        print("Sent a single message")
    ```

    Il mittente è un oggetto che funge da client per l'argomento creato. Verrà creato in seguito e verrà inviato come argomento a questa funzione. 
4. Aggiungere un metodo per inviare un elenco di messaggi.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the topic
        sender.send_messages(messages)
        print("Sent a list of 5 messages")
    ```
5. Aggiungere un metodo per inviare un batch di messaggi.

    ```python
    def send_batch_message(sender):
        # create a batch of messages
        batch_message = sender.create_message_batch()
        for _ in range(10):
            try:
                # add a message to the batch
                batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
            except ValueError:
                # ServiceBusMessageBatch object reaches max_size.
                # New ServiceBusMessageBatch object can be created here to send more data.
                break
        # send the batch of messages to the topic
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Creare un client del bus di servizio e quindi un oggetto mittente dell'argomento per l'invio di messaggi.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Topic Sender object to send messages to the topic
        sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
        with sender:
            # send one message        
            send_single_message(sender)
            # send a list of messages
            send_a_list_of_messages(sender)
            # send a batch of messages
            send_batch_message(sender)
    
    print("Done sending messages")
    print("-----------------------")
    ```
 
## <a name="receive-messages-from-a-subscription"></a>Ricevere messaggi da una sottoscrizione
Aggiungere il codice seguente dopo l'istruzione print. Questo codice riceve continuamente nuovi messaggi finché non ne riceve nessuno per 5 (`max_wait_time`) secondi. 

```python
with servicebus_client:
    # get the Subscription Receiver object for the subscription    
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the subscription
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Codice completo

```python
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
TOPIC_NAME = "<TOPIC NAME>"
SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"

def send_single_message(sender):
    message = ServiceBusMessage("Single Message")
    sender.send_messages(message)
    print("Sent a single message")

def send_a_list_of_messages(sender):
    messages = [ServiceBusMessage("Message in list") for _ in range(5)]
    sender.send_messages(messages)
    print("Sent a list of 5 messages")

def send_batch_message(sender):
    batch_message = sender.create_message_batch()
    for _ in range(10):
        try:
            batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
        except ValueError:
            # ServiceBusMessageBatch object reaches max_size.
            # New ServiceBusMessageBatch object can be created here to send more data.
            break
    sender.send_messages(batch_message)
    print("Sent a batch of 10 messages")

servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)

with servicebus_client:
    sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            receiver.complete_message(msg)
```

## <a name="run-the-app"></a>Eseguire l'app
Quando si esegue l'applicazione, viene generato l'output seguente: 

```console
Sent a single message
Sent a list of 5 messages
Sent a batch of 10 messages
Done sending messages
-----------------------
Received: Single Message
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
```

Nel portale di Azure passare allo spazio dei nomi del bus di servizio. Nella pagina **Panoramica** verificare che il numero di messaggi **in ingresso** e **in uscita** sia 16. Se non viene visualizzato questo numero, aggiornare la pagina e aspettare alcuni minuti. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Numero di messaggi in ingresso e in uscita":::

Selezionare l'argomento nel riquadro in basso per visualizzare la pagina **Argomento del bus di servizio** per l'argomento. In questa pagina verranno visualizzati tre messaggi in ingresso e tre messaggi in uscita nel grafico **Messaggi**. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Messaggi in ingresso e in uscita":::

Se in questa pagina si seleziona una sottoscrizione si passa alla pagina **Sottoscrizione del bus di servizio**. In questa pagina è possibile visualizzare il numero di messaggi attivi, il numero di messaggi non recapitabili e altro ancora. In questo esempio tutti i messaggi sono stati ricevuti, quindi il numero di messaggi attivi è zero. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Numero di messaggi attivi":::

Se si imposta come commento la coda di ricezione, si vedrà che il numero di messaggi attivi è 16. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count-2.png" alt-text="Numero di messaggi attivi - senza ricezione":::

## <a name="next-steps"></a>Passaggi successivi
Vedere la documentazione e gli esempi seguenti: 

- [Libreria client del bus di servizio di Azure per Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Esempi](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - La cartella **sync_samples** include esempi che mostrano come interagire con il bus di servizio in modo sincrono. In questa guida di avvio rapido è stato usato questo metodo. 
    - La cartella **async_samples** include esempi che mostrano come interagire con il bus di servizio in modo asincrono. 
- [Documentazione di riferimento di azure-servicebus](https://docs.microsoft.com/python/api/azure-servicebus/azure.servicebus?view=azure-python-preview&preserve-view=true)
