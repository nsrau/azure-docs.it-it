<properties linkid="develop-python-queue-service" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Python) | Microsoft Azure" metaKeywords="Azure Queue Service messaging Python" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Python." metaCanonical="" services="storage" documentationCenter="Python" title="How to Use the Queue Storage Service from Python" authors="" solutions="" manager="" editor="" />

Come utilizzare il servizio di archiviazione di accodamento di Python
================================

In questa guida viene illustrato come eseguire scenari comuni del servizio di archiviazione di accodamento di Azure. Gli esempi sono scritti utilizzando l'API Python. Gli scenari presentati includono l'**inserimento**, la **visualizzazione**, il **recupero** e l'**eliminazione** dei messaggi in coda, oltre alle procedure di **creazione ed eliminazione di code**. Per ulteriori informazioni sulle code, fare riferimento alla sezione [Passaggi successivi](#next-steps).

Sommario
-----

[Informazioni sull'archiviazione di accodamento](#what-is)
 [Concetti](#concepts)
 [Creazione di un account di archiviazione di Azure](#create-account)
 [Procedura: Creare una coda](#create-queue)
 [Procedura: Inserire un messaggio in una coda](#insert-message)
 [Procedura: Visualizzare il messaggio successivo](#peek-message)
 [Procedura: Rimuovere il messaggio successivo dalla coda](#get-message)
 [Procedura: Cambiare il contenuto di un messaggio in coda](#change-contents)
 [Procedura: Opzioni aggiuntive per rimuovere i messaggi dalla coda](#advanced-get)
 [Procedura: Recuperare la lunghezza della coda](#get-queue-length)
 [Procedura: Eliminare una coda](#delete-queue)
 [Passaggi successivi](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

Creazione di un account di archiviazione di Azure
---------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

**Nota:** se è necessario installare Python o le librerie client, vedere la [guida all'installazione di Python](../python-how-to-install/).

Procedura: Creare una coda
--------------

L'oggetto **QueueService** consente di utilizzare le code. Il codice seguente consente di creare un oggetto **QueueService**. Aggiungere il codice seguente vicino all'inizio del file Python da cui si desidera accedere all'archiviazione di Azure a livello di codice:

    from azure.storage import *

Il codice seguente consente di creare un oggetto **QueueService** utilizzando il nome dell'account di archiviazione e la chiave dell'account. Sostituire 'myaccount' e 'mykey' con l'account e la chiave reali.

    queue_service = QueueService(account_name='myaccount', account_key='mykey')

    queue_service.create_queue('taskqueue')

Procedura: Inserire un messaggio in una coda
--------------------

Per inserire un messaggio in una coda, utilizzare il metodo **put\_message** per creare un nuovo messaggio e aggiungerlo alla coda.

    queue_service.put_message('taskqueue', 'Hello World')

Procedura: Visualizzare il messaggio successivo
--------------------

È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo **peek\_messages**. Per impostazione predefinita, **peek\_messages** visualizza un singolo messaggio.

    messages = queue_service.peek_messages('taskqueue')
    for message in messages:
        print(message.message_text)

Procedura: Rimuovere il messaggio successivo dalla coda
------------------------

Il codice consente di rimuovere un messaggio da una coda in due passaggi. Per impostazione predefinita, chiamando **get\_messages** si ottiene il messaggio successivo in una coda. Un messaggio restituito da **get\_messages** diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche **delete\_message**. Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio abbia esito negativo a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice chiama **delete\_message** immediatamente dopo l'elaborazione del messaggio.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        print(message.message_text)
        queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

Procedura: Cambiare il contenuto di un messaggio in coda
------------------------

È possibile cambiare il contenuto di un messaggio inserito nella coda. Se il messaggio rappresenta un'attività di lavoro, è possibile utilizzare questa funzionalità per aggiornarne lo stato. La coda seguente utilizza il metodo **update\_message** per aggiornare un messaggio.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        queue_service.update_message('taskqueue', message.message_id, 'Hello World Again', message.pop_receipt, 0)

Procedura: Opzioni aggiuntive per rimuovere i messaggi dalla coda
----------------------------

È possibile personalizzare il recupero di messaggi da una coda in due modi. Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio. Nell'esempio di codice seguente viene utilizzato il metodo **get\_messages** per recuperare 16 messaggi con una sola chiamata. Quindi, ogni messaggio viene elaborato con un ciclo for. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

    messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
    for message in messages:
        print(message.message_text)
        queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

Procedura: Recuperare la lunghezza della coda
------------------

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo **get\_queue\_metadata** chiede al servizio di accodamento di restituire i metadati relativi alla coda. Per trovare il conteggio, è consigliabile utilizzare **x-ms-approximate-messages-count** come indice nel dizionario restituito. Il risultato è solo approssimativo, poiché è possibile aggiungere o rimuovere messaggi dopo la risposta del servizio di accodamento.

    queue_metadata = queue_service.get_queue_metadata('taskqueue')
    count = queue_metadata['x-ms-approximate-messages-count']

Procedura: Eliminare una coda
--------------

Per eliminare una coda e tutti i messaggi che contiene chiamare il metodo **delete\_queue**.

    queue_service.delete_queue('taskqueue')

Passaggi successivi
--------

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione di accodamento, visitare i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

-   Riferimento in MSDN: [Archiviazione e accesso ai dati in Azure](http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx)
-   [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)

