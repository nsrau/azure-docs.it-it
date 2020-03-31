---
title: Come usare archiviazione delle code di Azure v2.1 da Python - Archiviazione di AzureHow to use Azure Queue storage v2.1 from Python - Azure Storage
description: Informazioni su come usare il servizio di coda di Azure v2.1 da Python per creare ed eliminare code e inserire, ottenere ed eliminare messaggi.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-october2019
ms.openlocfilehash: ca0831fd7554058d21e315b67d6965579af1d38b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060908"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>Come usare l'archiviazione delle code di Azure v2.1 da PythonHow to use Azure Queue storage v2.1 from Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

Questo articolo illustra scenari comuni usando il servizio di archiviazione delle code di Azure.This article demonstrates common scenarios using the Azure Queue storage service. Gli scenari trattati includono l'inserimento, l'visualizzazione, il recupero e l'eliminazione dei messaggi della coda e la creazione e l'eliminazione di code.

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica

Gli esempi in questo articolo sono scritti in Python e usano [Microsoft Azure Storage SDK per Python.] Per altre informazioni sulle code, vedere la sezione [Passaggi successivi](#next-steps) .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Scaricare e installare Azure Storage SDK per PythonDownload and install Azure Storage SDK for Python

[Azure Storage SDK per Python](https://github.com/azure/azure-storage-python) richiede Python versione 2.7, 3.3 o successiva.
 
### <a name="install-via-pypi"></a>Eseguire l'installazione tramite PyPi

Per eseguire l'installazione tramite Python Package Index (PyPI), digitare:

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> Se si esegue l'aggiornamento da Azure Storage SDK per Python 0.36 o versione precedente, disinstallare l'SDK meno recente tramite `pip uninstall azure-storage` prima di installare il pacchetto più recente.

Per metodi di installazione alternativi, vedere la pagina di [Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Visualizzare l'applicazione di esempio

Per visualizzare ed eseguire un'applicazione di esempio che illustra come usare Python con le code di Azure, vedere Archiviazione di Azure: introduzione alle code di Azure in Python.To view and run a sample application that shows how to use Python with Azure Queues, see [Azure Storage: Getting Started with Azure Queues in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

Per eseguire questa applicazione di esempio, verificare di aver installato entrambi i pacchetti `azure-storage-queue` e `azure-storage-common`.

## <a name="create-a-queue"></a>Creare una coda

L'oggetto [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) consente di utilizzare le code. Il codice seguente `QueueService` crea un oggetto. Aggiungere il codice seguente vicino all'inizio del file Python da cui si desidera accedere all'archiviazione di Azure a livello di codice:

```python
from azure.storage.queue import QueueService
```

Il codice seguente crea un oggetto `QueueService` usando il nome e la chiave dell'account di archiviazione. Sostituisci *myaccount* e *mykey* con il nome e la chiave del tuo account.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Inserire un messaggio in una coda

Per inserire un messaggio in una coda, utilizzare il metodo [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) per creare un nuovo messaggio e aggiungerlo alla coda.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

I messaggi della coda di Azure vengono archiviati come testo. Se si desidera archiviare dati binari, impostare le funzioni di codifica e decodifica Base64 nell'oggetto servizio di coda prima di inserire un messaggio nella coda.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>Visualizzare il messaggio successivo

È possibile visualizzare il messaggio nella parte anteriore di una coda senza rimuoverlo dalla coda chiamando il [metodo peek_messages.](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) Per impostazione predefinita, `peek_messages` visualizza un singolo messaggio.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Rimuovere dalla coda i messaggi

Il codice consente di rimuovere un messaggio da una coda in due passaggi. Quando si chiama [get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-), si ottiene il messaggio successivo in una coda per impostazione predefinita. Un messaggio restituito da `get_messages` diventa invisibile a qualsiasi altro elemento di codice che legge i messaggi di questa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-). Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio abbia esito negativo a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice `delete_message` chiama subito dopo l'elaborazione del messaggio.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

È possibile personalizzare il recupero di messaggi da una coda in due modi. Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio. Nell'esempio di `get_messages` codice riportato di seguito viene utilizzato il metodo per ottenere 16 messaggi in una chiamata. Quindi, ogni messaggio viene elaborato con un ciclo for. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>Cambiare il contenuto di un messaggio in coda

È possibile cambiare il contenuto di un messaggio inserito nella coda. Se il messaggio rappresenta un'attività di lavoro, è possibile utilizzare questa funzionalità per aggiornarne lo stato. Il codice seguente usa il [metodo update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) per aggiornare un messaggio. Il timeout di visibilità è impostato su 0. Ciò significa che il messaggio viene visualizzato immediatamente e il contenuto viene aggiornato.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Recuperare la lunghezza della coda

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) chiede al servizio di coda `approximate_message_count`di restituire i metadati relativi alla coda e al metodo . Il risultato è solo approssimativo, poiché è possibile aggiungere o rimuovere messaggi dopo la risposta del servizio di accodamento.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Eliminare una coda

Per eliminare una coda e tutti i messaggi in essa contenuti, chiamare il [metodo delete_queue.](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-)

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base sull'archiviazione delle code, seguire questi collegamenti per ulteriori informazioni.

* [Informazioni di riferimento sull'API Python delle code di AzureAzure Queues Python API reference](/python/api/azure-storage-queue)
* [Centro per sviluppatori Python](https://azure.microsoft.com/develop/python/)
* [API REST di Servizi di archiviazione di AzureAzure Storage Services REST API](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK per Python]: https://github.com/Azure/azure-storage-python
