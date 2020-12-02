---
title: Usare le code del bus di servizio di Azure con il pacchetto Python azure-servicebus versione 7.0.0
description: Questo articolo illustra come usare Python per inviare e ricevere messaggi dalle code del bus di servizio di Azure.
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 2b54b167413b0fcbe7022eab4bbbf34b37225be5
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95810592"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-python"></a>Inviare e ricevere messaggi dalle code del bus di servizio di Azure (Python)
Questo articolo illustra come usare Python per inviare e ricevere messaggi dalle code del bus di servizio di Azure. 

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure. È possibile attivare i [vantaggi della sottoscrizione Visual Studio o MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oppure registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se non si ha una coda da usare, seguire la procedura descritta nell'articolo [Usare il portale di Azure per creare una coda del bus di servizio](service-bus-quickstart-portal.md) per crearne una. Prendere nota della **stringa di connessione** per lo spazio dei nomi del bus di servizio e del nome della **coda** creata.
- Python 2.7 o versione successiva, con il pacchetto [Python Azure Service Bus](https://pypi.python.org/pypi/azure-servicebus) installato. Per altre informazioni, vedere la [Guida all'installazione di Python](/azure/developer/python/azure-sdk-install). 

## <a name="send-messages-to-a-queue"></a>Inviare messaggi a una coda

1. Aggiungere l'istruzione import seguente. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Aggiungere le costanti seguenti. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    QUEUE_NAME = "<QUEUE NAME>"
    ```

    > [!IMPORTANT]
    > - Sostituire `<NAMESPACE CONNECTION STRING>` con la stringa di connessione per lo spazio dei nomi del bus di servizio.
    > - Sostituire `<QUEUE NAME>` con il nome della coda. 
3. Aggiungere un metodo per inviare un singolo messaggio.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the queue
        sender.send_messages(message)
        print("Sent a single message")
    ```

    Il mittente è un oggetto che funge da client per la coda creata. Verrà creato in seguito e verrà inviato come argomento a questa funzione. 
4. Aggiungere un metodo per inviare un elenco di messaggi.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the queue
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
        # send the batch of messages to the queue
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Creare un client del bus di servizio e quindi un oggetto mittente della coda per l'invio di messaggi.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Queue Sender object to send messages to the queue
        sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
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
 
## <a name="receive-messages-from-a-queue"></a>Ricevere messaggi da una coda
Aggiungere il codice seguente dopo l'istruzione print. Questo codice riceve continuamente nuovi messaggi finché non ne riceve nessuno per 5 (`max_wait_time`) secondi. 

```python
with servicebus_client:
    # get the Queue Receiver object for the queue
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the queue
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Codice completo

```python
# import os
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
QUEUE_NAME = "<QUEUE NAME>"

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
    sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
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

Selezionare la coda in questa pagina **Panoramica** per passare alla pagina **Coda del bus di servizio**. In questa pagina è anche possibile visualizzare il numero di messaggi **in ingresso** e **in uscita**. Vengono visualizzate anche altre informazioni, come le **dimensioni correnti** della coda e il **numero di messaggi attivi**. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/queue-details.png" alt-text="Dettagli della coda":::


## <a name="next-steps"></a>Passaggi successivi
Vedere la documentazione e gli esempi seguenti: 

- [Libreria client del bus di servizio di Azure per Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Esempi](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - La cartella **sync_samples** include esempi che mostrano come interagire con il bus di servizio in modo sincrono. In questa guida di avvio rapido è stato usato questo metodo. 
    - La cartella **async_samples** include esempi che mostrano come interagire con il bus di servizio in modo asincrono. 
- [Documentazione di riferimento di azure-servicebus](https://docs.microsoft.com/python/api/azure-servicebus/azure.servicebus?view=azure-python-preview&preserve-view=true)

