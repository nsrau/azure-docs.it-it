---
title: Come usare l'archiviazione code da Python | Microsoft Docs
description: Informazioni su come usare il servizio di accodamento di Azure da Python per creare ed eliminare code e per inserire, visualizzare ed eliminare messaggi.
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: cc0d2da2-379a-4b58-a234-8852b4e3d99d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: c7976c01436b1c30880bfd4c57cb97f72a4f48b0
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-queue-storage-from-python"></a>Come usare l'archiviazione di accodamento da Python
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica
In questa guida viene illustrato come eseguire scenari comuni del servizio di archiviazione di accodamento di Azure. Gli esempi sono scritti in Python e usano [Microsoft Azure Storage SDK per Python]. Gli scenari illustrati includono **inserimento**, **visualizzazione**, **recupero** ed **eliminazione** dei messaggi in coda, oltre alla **creazione ed eliminazione di code**. Per altre informazioni sulle code, fare riferimento alla sezione [Passaggi successivi].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Scaricare e installare Azure Storage SDK per Python

Azure Storage SDK per Python richiede Python 2.7, 3.3, 3.4, 3.5 o 3.6 ed è disponibile in quattro diversi pacchetti: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` e `azure-storage-queue`. In questa esercitazione verrà usato il pacchetto `azure-storage-queue`.
 
### <a name="install-via-pypi"></a>Eseguire l'installazione tramite PyPi

Per eseguire l'installazione tramite Python Package Index (PyPI), digitare:

```bash
pip install azure-storage-queue
```


> [!NOTE]
> Se si esegue l'aggiornamento da Azure Storage SDK per Python 0.36 o versione precedente, sarà innanzitutto necessario eseguire la disinstallazione tramite `pip uninstall azure-storage` dal momento che Azure Storage SDK per Python non viene più rilasciato in un singolo pacchetto.
> 
> 

Per metodi di installazione alternativi, vedere la pagina di [Azure Storage SDK per Python su Github](https://github.com/Azure/azure-storage-python/).

## <a name="how-to-create-a-queue"></a>Procedura: creare una coda
L'oggetto **QueueService** consente di utilizzare le code. Il codice seguente consente di creare un oggetto **QueueService** . Aggiungere il codice seguente vicino all'inizio del file Python da cui si desidera accedere all'archiviazione di Azure a livello di codice:

```python
from azure.storage.queue import QueueService
```

Il codice seguente consente di creare un oggetto **QueueService** usando il nome dell'account di archiviazione e la chiave dell'account. Sostituire 'myaccount' e 'mykey' con l'account e la chiave da usare.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Procedura: inserire un messaggio in una coda
Per inserire un messaggio in una coda, usare il metodo **put\_message** per creare un nuovo messaggio e aggiungerlo alla coda.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Procedura: visualizzare il messaggio successivo
È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo **peek\_messages**. Per impostazione predefinita, **peek\_messages** visualizza un singolo messaggio.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Procedura: Rimuovere messaggi dalla coda
Il codice consente di rimuovere un messaggio da una coda in due passaggi. Per impostazione predefinita, chiamando **get\_messages**, si ottiene il messaggio successivo in una coda. Un messaggio restituito da **get\_messages** diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche **delete\_message**. Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio abbia esito negativo a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice chiama **delete\_message** immediatamente dopo l'elaborazione del messaggio.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

È possibile personalizzare il recupero di messaggi da una coda in due modi.
Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio. Nell'esempio di codice seguente viene usato il metodo **get\_messages** per recuperare 16 messaggi con una sola chiamata. Quindi, ogni messaggio viene elaborato con un ciclo for. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

```python
messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)        
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procedura: cambiare il contenuto di un messaggio accodato
È possibile cambiare il contenuto di un messaggio inserito nella coda. Se il messaggio rappresenta un'attività di lavoro, è possibile utilizzare questa funzionalità per aggiornarne lo stato. La coda seguente usa il metodo **update\_message** per aggiornare un messaggio. Il timeout di visibilità è impostato su 0. Ciò significa che il messaggio viene visualizzato immediatamente e il contenuto viene aggiornato.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Procedura: recuperare la lunghezza delle code
È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo **get\_queue\_metadata** chiede al servizio di accodamento di restituire i metadati relativi alla coda e **approximate_message_count**. Il risultato è solo approssimativo, poiché è possibile aggiungere o rimuovere messaggi dopo la risposta del servizio di accodamento.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Procedura: eliminare una coda
Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo **delete\_queue**.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo avere appreso le nozioni di base dell'archiviazione code, visitare i collegamenti seguenti per altre informazioni.

* [Centro per sviluppatori di Python](/develop/python/)
* [API REST dei servizi di archiviazione di Azure](http://msdn.microsoft.com/library/azure/dd179355)
* [Blog del team di Archiviazione di Azure]
* [Microsoft Azure Storage SDK per Python]

[Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK per Python]: https://github.com/Azure/azure-storage-python