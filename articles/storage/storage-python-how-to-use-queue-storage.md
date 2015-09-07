<properties 
	pageTitle="Come usare l'archiviazione di accodamento da Python | Microsoft Azure"
	description="Informazioni su come usare il servizio di accodamento di Azure da Python per creare ed eliminare code e per inserire, visualizzare ed eliminare messaggi."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor=""/>

<tags 
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="emgerner"/>

# Come usare l'archiviazione di accodamento da Python

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Panoramica

In questa guida viene illustrato come eseguire scenari comuni del servizio di archiviazione di accodamento di Azure. Gli esempi sono scritti in Python e utilizzano il [pacchetto di archiviazione Python di Azure][]. Gli scenari presentati includono l'**inserimento**, la **visualizzazione**, il **recupero** e l'**eliminazione** dei messaggi in coda, oltre alle procedure di **creazione ed eliminazione di code**. Per altre informazioni sulle code, fare riferimento alla sezione [Passaggi successivi][].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


> [AZURE.NOTE]Se è necessario installare Python o il [pacchetto Python di Azure][], vedere la [guida all'installazione di Python](../python-how-to-install.md).

## Procedura: creare una coda

L'oggetto **QueueService** consente di utilizzare le code. Il codice seguente consente di creare un oggetto **QueueService**. Aggiungere il codice seguente vicino all'inizio del file Python da cui si desidera accedere all'archiviazione di Azure a livello di codice:

	from azure.storage.queue import QueueService

Il codice seguente consente di creare un oggetto **QueueService** usando il nome dell'account di archiviazione e la chiave dell'account. Sostituire 'myaccount' e 'mykey' con l'account e la chiave reali.

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## Procedura: inserire un messaggio in una coda

Per inserire un messaggio in una coda, utilizzare il metodo **put\_message** per creare un nuovo messaggio e aggiungerlo alla coda.

	queue_service.put_message('taskqueue', 'Hello World')


## Procedura: visualizzare il messaggio successivo

È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo **peek\_messages**. Per impostazione predefinita, **peek\_messages** visualizza un singolo messaggio.

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.message_text)


## Procedura: rimuovere il messaggio successivo dalla coda

Il codice consente di rimuovere un messaggio da una coda in due passaggi. Per impostazione predefinita, chiamando **get\_messages** si ottiene il messaggio successivo in una coda. Un messaggio restituito da **get\_messages** diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche **delete\_message**. Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio abbia esito negativo a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice chiama **delete\_message** immediatamente dopo l'elaborazione del messaggio.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)


## Procedura: cambiare il contenuto di un messaggio accodato

È possibile cambiare il contenuto di un messaggio inserito nella coda. Se il messaggio rappresenta un'attività di lavoro, è possibile utilizzare questa funzionalità per aggiornarne lo stato. La coda seguente utilizza il metodo **update\_message** per aggiornare un messaggio.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.message_id, 'Hello World Again', message.pop_receipt, 0)

## Procedura: opzioni aggiuntive per rimuovere i messaggi dalla coda

È possibile personalizzare il recupero di messaggi da una coda in due modi. Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio. Nell'esempio di codice seguente viene utilizzato il metodo **get\_messages** per recuperare 16 messaggi con una sola chiamata. Quindi, ogni messaggio viene elaborato con un ciclo for. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

	messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

## Procedura: recuperare la lunghezza delle code

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo **get\_queue\_metadata** chiede al servizio di accodamento di restituire i metadati relativi alla coda. Per trovare il conteggio, è consigliabile utilizzare **x-ms-approximate-messages-count** come indice nel dizionario restituito. Il risultato è solo approssimativo, poiché è possibile aggiungere o rimuovere messaggi dopo la risposta del servizio di accodamento.

	queue_metadata = queue_service.get_queue_metadata('taskqueue')
	count = queue_metadata['x-ms-approximate-messages-count']

## Procedura: eliminare una coda

Per eliminare una coda e tutti i messaggi che contiene chiamare il metodo **delete\_queue**.

	queue_service.delete_queue('taskqueue')

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione di accodamento, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

-   Vedere la documentazione MSDN: [Archiviazione e accesso ai dati in Azure][]
-   [Blog del team di Archiviazione di Azure][]

[Archiviazione e accesso ai dati in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[pacchetto Python di Azure]: https://pypi.python.org/pypi/azure
[pacchetto di archiviazione Python di Azure]: https://pypi.python.org/pypi/azure-storage
 

<!---HONumber=August15_HO9-->