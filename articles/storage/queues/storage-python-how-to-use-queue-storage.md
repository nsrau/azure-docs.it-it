---
title: Come usare l'archiviazione di Accodamento di Azure da Python-archiviazione di Azure
description: Informazioni su come usare il servizio di accodamento di Azure da Python per creare ed eliminare code e per inserire, visualizzare ed eliminare messaggi.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 09/17/2019
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 18333d3da0bb444ea236a4fbda4d6b72d7647053
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059048"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>Come usare l'archiviazione di Accodamento di Azure da Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica

In questa guida viene illustrato come eseguire scenari comuni del servizio di archiviazione di accodamento di Azure. Gli esempi sono scritti in Python e usano [Microsoft Azure Storage SDK per Python]. Gli scenari descritti includono inserimento, visualizzazione, recupero ed eliminazione dei messaggi in coda, nonché creazione ed eliminazione di code. Per ulteriori informazioni sulle code, vedere la sezione [passaggi successivi](#next-steps) .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Scaricare e installare Azure Storage SDK per Python

[Azure Storage SDK per Python](https://github.com/azure/azure-storage-python) richiede python versione 2,7, 3,3 o successiva.
 
### <a name="install-via-pypi"></a>Eseguire l'installazione tramite PyPi

Per eseguire l'installazione tramite Python Package Index (PyPI), digitare:

```bash
pip install azure-storage-queue
```

> [!NOTE]
> Se si esegue l'aggiornamento da Azure Storage SDK per Python 0.36 o versione precedente, disinstallare l'SDK meno recente tramite `pip uninstall azure-storage` prima di installare il pacchetto più recente.

Per metodi di installazione alternativi, vedere la pagina di [Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Visualizzare l'applicazione di esempio

Per visualizzare ed eseguire un'applicazione di esempio che mostra come usare Python con Code di Azure, vedere [Azure Storage: Getting Started with Azure Queues in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started) (Archiviazione di Azure: introduzione a Code di Azure in Python). 

Per eseguire questa applicazione di esempio, verificare di aver installato entrambi i pacchetti `azure-storage-queue` e `azure-storage-common`.

## <a name="create-a-queue"></a>Crea una coda

L'oggetto [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) consente di utilizzare le code. Il codice seguente crea un `QueueService` oggetto. Aggiungere il codice seguente vicino all'inizio del file Python da cui si desidera accedere all'archiviazione di Azure a livello di codice:

```python
from azure.storage.queue import QueueService
```

Il codice seguente crea un oggetto `QueueService` usando il nome e la chiave dell'account di archiviazione. Sostituire *account* e *myKey* con il nome e la chiave dell'account.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Inserire un messaggio in una coda

Per inserire un messaggio in una coda, usare il metodo [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) per creare un nuovo messaggio e aggiungerlo alla coda.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

I messaggi della coda di Azure vengono archiviati come testo. Se si desidera archiviare i dati binari, configurare le funzioni di codifica e decodifica Base64 nell'oggetto servizio di Accodamento prima di inserire un messaggio nella coda.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>Visualizzare il messaggio successivo

È possibile visualizzare il messaggio nella parte anteriore di una coda senza rimuoverlo dalla coda chiamando il metodo [peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) . Per impostazione predefinita `peek_messages` , Visualizza un singolo messaggio.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Rimuovere dalla coda i messaggi

Il codice consente di rimuovere un messaggio da una coda in due passaggi. Quando si chiama [get_Messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-), per impostazione predefinita si ottiene il messaggio successivo in una coda. Un messaggio restituito da `get_messages` diventa invisibile a qualsiasi altro elemento di codice che legge i messaggi di questa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-). Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio abbia esito negativo a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice chiama `delete_message` subito dopo l'elaborazione del messaggio.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

È possibile personalizzare il recupero di messaggi da una coda in due modi. Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio. Nell'esempio di codice seguente viene `get_messages` usato il metodo per ottenere 16 messaggi in una sola chiamata. Quindi, ogni messaggio viene elaborato con un ciclo for. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>Cambiare il contenuto di un messaggio in coda

È possibile cambiare il contenuto di un messaggio inserito nella coda. Se il messaggio rappresenta un'attività di lavoro, è possibile utilizzare questa funzionalità per aggiornarne lo stato. Il codice seguente usa il metodo [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) per aggiornare un messaggio. Il timeout di visibilità è impostato su 0. Ciò significa che il messaggio viene visualizzato immediatamente e il contenuto viene aggiornato.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Recuperare la lunghezza della coda

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) chiede al servizio di Accodamento di restituire i metadati relativi alla coda `approximate_message_count`e. Il risultato è solo approssimativo, poiché è possibile aggiungere o rimuovere messaggi dopo la risposta del servizio di accodamento.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Eliminare una coda

Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) .

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state apprese le nozioni di base dell'archiviazione code, seguire questi collegamenti per altre informazioni.

* [Informazioni di riferimento sull'API Python per le code di Azure](/python/api/azure-storage-queue)
* [Centro per sviluppatori Python](https://azure.microsoft.com/develop/python/)
* [API REST dei servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK per Python]: https://github.com/Azure/azure-storage-python
