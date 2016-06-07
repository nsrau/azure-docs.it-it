<properties
	pageTitle="Come usare l'archiviazione di accodamento da Python | Microsoft Azure"
	description="Informazioni su come usare il servizio di accodamento di Azure da Python per creare ed eliminare code e per inserire, visualizzare ed eliminare messaggi."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="emgerner"/>

# Come usare l'archiviazione di accodamento da Python

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Panoramica

In questa guida viene illustrato come eseguire scenari comuni del servizio di archiviazione di accodamento di Azure. Gli esempi sono scritti in Python e utilizzano [Microsoft Azure Storage SDK per Python]. Gli scenari presentati includono l'**inserimento**, la **visualizzazione**, il **recupero** e l'**eliminazione** dei messaggi in coda, oltre alle procedure di **creazione ed eliminazione di code**. Per altre informazioni sulle code, fare riferimento alla sezione [Passaggi successivi].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Procedura: creare una coda

L'oggetto **QueueService** consente di utilizzare le code. Il codice seguente consente di creare un oggetto **QueueService**. Aggiungere il codice seguente vicino all'inizio del file Python da cui si desidera accedere all'archiviazione di Azure a livello di codice:

	from azure.storage.queue import QueueService

Il codice seguente consente di creare un oggetto **QueueService** usando il nome dell'account di archiviazione e la chiave dell'account. Sostituire 'myaccount' e 'mykey' con l'account e la chiave da usare.

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## Procedura: inserire un messaggio in una coda

Per inserire un messaggio in una coda, utilizzare il metodo **put\_message** per creare un nuovo messaggio e aggiungerlo alla coda.

	queue_service.put_message('taskqueue', u'Hello World')


## Procedura: visualizzare il messaggio successivo

È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo **peek\_messages**. Per impostazione predefinita, **peek\_messages** visualizza un singolo messaggio.

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.message_text)


## Procedura: Rimuovere messaggi dalla coda

Il codice consente di rimuovere un messaggio da una coda in due passaggi. Per impostazione predefinita, chiamando **get\_messages** si ottiene il messaggio successivo in una coda. Un messaggio restituito da **get\_messages** diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche **delete\_message**. Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio abbia esito negativo a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice chiama **delete\_message** immediatamente dopo l'elaborazione del messaggio.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.content)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

È possibile personalizzare il recupero di messaggi da una coda in due modi. Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio. Nell'esempio di codice seguente viene utilizzato il metodo **get\_messages** per recuperare 16 messaggi con una sola chiamata. Quindi, ogni messaggio viene elaborato con un ciclo for. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

	messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
	for message in messages:
		print(message.content)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)		


## Procedura: cambiare il contenuto di un messaggio accodato

È possibile cambiare il contenuto di un messaggio inserito nella coda. Se il messaggio rappresenta un'attività di lavoro, è possibile utilizzare questa funzionalità per aggiornarne lo stato. La coda seguente utilizza il metodo **update\_message** per aggiornare un messaggio. Il timeout di visibilità è impostato su 0. Ciò significa che il messaggio viene visualizzato immediatamente e il contenuto viene aggiornato.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.message_id, message.pop_receipt, 0, u'Hello World Again')

## Procedura: recuperare la lunghezza delle code

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo **get\_queue\_metadata** chiede al servizio di accodamento di restituire i metadati relativi alla coda e il valore di **approximate\_message\_count**. Il risultato è solo approssimativo, poiché è possibile aggiungere o rimuovere messaggi dopo la risposta del servizio di accodamento.

	metadata = queue_service.get_queue_metadata('taskqueue')
	count = metadata.approximate_message_count

## Procedura: eliminare una coda

Per eliminare una coda e tutti i messaggi che contiene chiamare il metodo **delete\_queue**.

	queue_service.delete_queue('taskqueue')

## Passaggi successivi

A questo punto, dopo avere appreso le nozioni di base dell'archiviazione code, visitare i collegamenti seguenti per altre informazioni.

- [Centro per sviluppatori di Python](/develop/python/)
- [API REST dei servizi di archiviazione di Azure](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog del team di Archiviazione di Azure]
- [Microsoft Azure Storage SDK per Python]

[Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK per Python]: https://github.com/Azure/azure-storage-python

<!---HONumber=AcomDC_0525_2016-->