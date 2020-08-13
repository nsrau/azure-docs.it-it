---
title: 'Guida introduttiva: Libreria di Archiviazione code di Azure v12 - Python'
description: In questo argomento di avvio rapido si apprenderà come usare la libreria di Archiviazione code di Azure v12 per Python per creare una coda e aggiungervi messaggi, come leggere ed eliminare i messaggi dalla coda e come eliminare una coda.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/10/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 12c2652b4dcef46c5affde2c3fb9ef9288176eb9
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852260"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Guida introduttiva: Libreria client di Archiviazione code di Azure v12 per Python

Iniziare a usare la libreria client di Archiviazione code di Azure versione 12 per Python. Archiviazione code di Azure è un servizio che consente di archiviare un numero elevato di messaggi per recuperali ed elaborarli successivamente. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

Usare la libreria client di Archiviazione code di Azure v12 per Python per:

* Creare una coda
* Aggiungere messaggi a una coda
* Visualizzare in anteprima i messaggi in una coda
* Aggiornare un messaggio in una coda
* Ricevere messaggi da una coda
* Eliminare messaggi da una coda
* Eliminare una coda

Risorse aggiuntive:

* [Documentazione di riferimento delle API](https://docs.microsoft.com/python/api/azure-storage-queue/index)
* [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)
* [Pacchetto (Python Package Index)](https://pypi.org/project/azure-storage-queue/)
* [Esempi](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* Account di archiviazione di Azure: [creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Python](https://www.python.org/downloads/) per il sistema operativo in uso: versione 2.7, 3.5 o superiore

## <a name="setting-up"></a>Configurazione

Questa sezione illustra come preparare un progetto da usare con la libreria client di Archiviazione code di Azure v12 per Python.

### <a name="create-the-project"></a>Creare il progetto

Creare un'applicazione Python con il nome *queues-quickstart-v12*.

1. In una finestra della console, ad esempio cmd, PowerShell o Bash, creare una nuova directory per il progetto.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Passare alla directory *queues-quickstart-v12* appena creata.

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Installare il pacchetto

Installare la libreria client di Archiviazione code di Azure per il pacchetto Python usando il comando `pip install`.

```console
pip install azure-storage-queue
```

Questo comando installa il pacchetto della libreria client di Archiviazione code di Azure per Python e tutte le librerie da cui dipende. In questo caso, si tratta solo della libreria principale di Azure per Python.

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

1. Aprire un nuovo file di testo nell'editor del codice
1. Aggiungere le istruzioni `import`
1. Creare la struttura per il programma, inclusa la gestione delle eccezioni di base

    Ecco il codice:

    ```python
    import os, uuid
    from azure.storage.queue import QueueServiceClient, QueueClient, QueueMessage

    try:
        print("Azure Queue storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)

    ```

1. Salvare il nuovo file con il nome *queues-quickstart-v12.py* nella directory *queues-quickstart-v12*.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modello a oggetti

Archiviazione code di Azure è un servizio per l'archiviazione di un numero elevato di messaggi. Un messaggio in coda avere dimensioni fino a 64 KB. Una coda può contenere milioni di messaggi, fino al limite di capacità totale dell'account di archiviazione. Le code vengono in genere usate per creare un backlog di lavoro da elaborare in modo asincrono. Archiviazione code offre tre tipi di risorse:

* L'account di archiviazione
* Una coda nell'account di archiviazione
* Messaggi all'interno della coda

Il diagramma seguente mostra la relazione tra queste risorse.

![Diagramma dell'architettura di Archiviazione code](./media/storage-queues-introduction/queue1.png)

Per interagire con queste risorse, usare le classi Python seguenti:

* [QueueServiceClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): la classe `QueueServiceClient` consente di gestire tutte le code nell'account di archiviazione.
* [QueueClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient): la classe `QueueClient` consente di gestire e modificare una singola coda e i relativi messaggi.
* [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage): la classe `QueueMessage` rappresenta i singoli oggetti restituiti quando si chiama [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) su una coda.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice di esempio illustrano come eseguire le azioni seguenti con la libreria client di Archiviazione code di Azure per Python:

* [Ottenere la stringa di connessione](#get-the-connection-string)
* [Creare una coda](#create-a-queue)
* [Aggiungere messaggi a una coda](#add-messages-to-a-queue)
* [Visualizzare in anteprima i messaggi in una coda](#peek-at-messages-in-a-queue)
* [Aggiornare un messaggio in una coda](#update-a-message-in-a-queue)
* [Ricevere messaggi da una coda](#receive-messages-from-a-queue)
* [Eliminare messaggi da una coda](#delete-messages-from-a-queue)
* [Eliminare una coda](#delete-a-queue)

### <a name="get-the-connection-string"></a>Ottenere la stringa di connessione

Il codice seguente recupera la stringa di connessione per l'account di archiviazione. La stringa di connessione è archiviata nella variabile di ambiente creata nella sezione [Configurare la stringa di connessione di archiviazione](#configure-your-storage-connection-string).

Aggiungere questo codice all'interno del blocco `try`:

```python
    # Retrieve the connection string for use with the application. The storage
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_STORAGE_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-queue"></a>Creare una coda

Decidere un nome per la nuova coda. Il codice seguente aggiunge un valore UUID al nome della coda per garantirne l'univocità.

> [!IMPORTANT]
> I nomi di coda possono contenere solo lettere minuscole, numeri e segni meno e devono iniziare con una lettera o un numero. Ogni trattino deve essere preceduto e seguito da un carattere diverso da un trattino. Il nome deve inoltre avere una lunghezza compresa fra 3 e 63 caratteri. Per altre informazioni sull'assegnazione di nomi alle code, vedere [Denominazione di code e metadati](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata).

Creare un'istanza della classe [QueueClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient). Chiamare quindi il metodo [create_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) per creare la coda nell'account di archiviazione.

Aggiungere questo codice alla fine del blocco `try`:

```python
    # Create a unique name for the queue
    queue_name = "quickstartqueues-" + str(uuid.uuid4())

    print("Creating queue: " + queue_name)

    # Instantiate a QueueClient which will be
    # used to create and manipulate the queue
    queue_client = QueueClient.from_connection_string(connect_str, queue_name)

    # Create the queue
    queue_client.create_queue()
```

### <a name="add-messages-to-a-queue"></a>Aggiungere messaggi a una coda

Il frammento di codice seguente aggiunge messaggi alla coda chiamando il metodo [send_message](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-). Salva anche il [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage) restituito dalla terza chiamata a `send_message`. Il `saved_message` viene usato per aggiornare il contenuto del messaggio in un secondo momento nel programma.

Aggiungere questo codice alla fine del blocco `try`:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Visualizzare in anteprima i messaggi in una coda

Per visualizzare in anteprima i messaggi nella coda, chiamare il metodo [peek_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-). Il metodo `peek_messages` recupera uno o più messaggi dall'inizio della coda, senza modificare la visibilità del messaggio.

Aggiungere questo codice alla fine del blocco `try`:

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Aggiornare un messaggio in una coda

Per aggiornare il contenuto di un messaggio, chiamare il metodo [update_message](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-). Il metodo `update_message` può modificare il timeout di visibilità e il contenuto di un messaggio. Il contenuto del messaggio deve essere una stringa con codifica UTF-8 di dimensioni non superiori a 64 KB. Insieme al nuovo contenuto, passare i valori del messaggio salvati in precedenza nel codice. I valori del `saved_message` identificano il messaggio da aggiornare.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Ricevere messaggi da una coda

Scaricare i messaggi aggiunti precedentemente chiamando il metodo [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-).

Aggiungere questo codice alla fine del blocco `try`:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Eliminare messaggi da una coda

Eliminare i messaggi dalla coda dopo che sono stati ricevuti ed elaborati. In questo caso, l'elaborazione consiste semplicemente nella visualizzazione del messaggio nella console.

L'app viene sospesa per l'input dell'utente chiamando `input` prima dell'elaborazione ed eliminazione dei messaggi. Verificare nel [portale di Azure](https://portal.azure.com) che le risorse siano state create correttamente, prima di eliminarle. Gli eventuali messaggi che non vengono eliminati in modo esplicito diventeranno nuovamente visibili nella coda per poter essere elaborati di nuovo.

Aggiungere questo codice alla fine del blocco `try`:

```python
    print("\nPress Enter key to 'process' messages and delete them from the queue...")
    input()

    for msg_batch in messages.by_page():
            for msg in msg_batch:
                # "Process" the message
                print(msg.content)
                # Let the service know we're finished with
                # the message and it can be safely deleted.
                queue_client.delete_message(msg)
```

### <a name="delete-a-queue"></a>Eliminare una coda

Il codice seguente pulisce le risorse create dall'app eliminando la coda tramite il metodo [delete_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-).

Aggiungere questo codice alla fine del blocco `try` e salvare il file:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>Eseguire il codice

Questa app crea e aggiunge tre messaggi a una coda di Azure. Il codice elenca i messaggi nella coda, quindi li recupera e li elimina, prima di eliminare definitivamente la coda.

Nella finestra della console passare alla directory contenente il file *queues-quickstart-v12.py*, quindi usare il comando `python` seguente per eseguire l'app.

```console
python queues-quickstart-v12.py
```

L'output dell'app è simile all'esempio seguente:

```output
Azure Queue storage v12 - Python quickstart sample
Creating queue: quickstartqueues-cac365be-7ce6-4065-bd65-3756ea052cb8

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

First message
Second message
Third message has been updated

Press Enter key to delete the queue...

Deleting queue...
Done
```

Quando l'app viene sospesa prima della ricezione dei messaggi, controllare l'account di archiviazione nel [portale di Azure](https://portal.azure.com). Verificare che i messaggi siano nella coda.

Premere **INVIO** per ricevere ed eliminare i messaggi. Quando richiesto, premere di nuovo **INVIO** per eliminare la coda e terminare la demo.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare una coda e aggiungervi messaggi usando codice Python. Si è quindi appreso come visualizzare in anteprima, recuperare ed eliminare i messaggi. Infine, si è appreso come eliminare una coda di messaggi.

Per esercitazioni, esempi, guide di avvio rapido e altra documentazione, vedere:

> [!div class="nextstepaction"]
> [Azure per sviluppatori Python](https://docs.microsoft.com/azure/python/)

* Per altre informazioni, vedere le [librerie di Archiviazione di Azure per Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage).
* Per altre app di esempio su Archiviazione code di Azure, continuare con gli [esempi della libreria client di Archiviazione code di Azure v12 per Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples).
