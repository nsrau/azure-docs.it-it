---
title: Inviare o ricevere eventi con Python-Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce una procedura dettagliata per la creazione di un'applicazione Python che invia eventi a hub eventi di Azure.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/08/2020
ms.author: spelluru
ms.openlocfilehash: 11548ba180a2dd6541240431d670812448c502b8
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981577"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Inviare o ricevere eventi da Hub eventi tramite Python

Hub eventi di Azure è una piattaforma di Big Data streaming e un servizio di inserimento di eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

Questa esercitazione descrive come creare applicazioni Python per inviare o ricevere eventi da un hub eventi.

> [!IMPORTANT]
> Questa Guida introduttiva usa la versione 5 dell'SDK Python di hub eventi di Azure. Per una guida introduttiva che usa la versione 1 precedente di Python SDK, vedere [questo articolo](event-hubs-python-get-started-send.md). Se si usa la versione 1 dell'SDK, è consigliabile eseguire la migrazione del codice alla versione più recente. Per informazioni dettagliate, vedere la [Guida alla migrazione](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).


## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

- Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Uno spazio dei nomi di hub eventi attivo e un hub eventi, creato seguendo le istruzioni disponibili in [Guida introduttiva: creare un hub eventi usando portale di Azure](event-hubs-create.md). Prendere nota dei nomi dello spazio dei nomi e dell'hub eventi per usarli più avanti in questa procedura dettagliata.
- Il nome della chiave di accesso condiviso e il valore della chiave primaria per lo spazio dei nomi di Hub eventi. Ottenere il nome e il valore della chiave di accesso seguendo le istruzioni in [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Il nome della chiave di accesso predefinita è **RootManageSharedAccessKey**. Copiare il nome della chiave e il valore della chiave primaria per usarli più avanti in questa procedura dettagliata.
- Python 2,7 e 3,5 o versioni successive, con `pip` installato e aggiornato.
- Il pacchetto Python per Hub eventi. Per installare il pacchetto, eseguire questo comando in un prompt dei comandi il cui percorso contenga Python:

    ```cmd
    pip install azure-eventhub
    ```

    Installare questo pacchetto per ricevere gli eventi usando un archivio BLOB di Azure come archivio di checkpoint.

    ```cmd
    pip install azure-eventhub-checkpointstoreblobaio
    ```

## <a name="send-events"></a>Inviare eventi
In questa sezione viene creato uno script Python per inviare gli eventi all'hub eventi creato in precedenza.

1. Aprire l'editor di Python preferito, ad esempio[Visual Studio Code](https://code.visualstudio.com/)
2. Creare uno script denominato **send.py**. Questo script Invia un batch di eventi all'hub eventi creato in precedenza.
3. Incollare il codice seguente in send.py. Per informazioni dettagliate, vedere i commenti del codice.

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # create a producer client to send messages to the event hub
        # specify connection string to your event hubs namespace and
            # the event hub name
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # create a batch
            event_data_batch = await producer.create_batch()

            # add events to the batch
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # send the batch of events to the event hub
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > Per il codice sorgente completo con commenti molto utili, vedere [questo file in GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)

## <a name="receive-events"></a>Ricevere eventi
Questa Guida introduttiva usa un archivio BLOB di Azure come archivio di checkpoint. L'archivio checkpoint viene usato per salvare i checkpoint (ultima posizione di lettura).  

### <a name="create-an-azure-storage-and-a-blob-container"></a>Creare un'archiviazione di Azure e un contenitore BLOB
Seguire questa procedura per creare un account di archiviazione di Azure in un contenitore BLOB.

1. [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Creare un contenitore BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Ottenere la stringa di connessione all'account di archiviazione](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Annotare la stringa di connessione e il nome del contenitore. Che vengono usati nel codice di ricezione.


### <a name="create-python-script-to-receive-events"></a>Creare uno script Python per la ricezione di eventi

In questa sezione viene creato uno script Python per la ricezione di eventi dall'hub eventi:

1. Aprire l'editor di Python preferito, ad esempio[Visual Studio Code](https://code.visualstudio.com/)
2. Creare uno script denominato **recv.py**.
3. Incollare il codice seguente in recv.py. Per informazioni dettagliate, vedere i commenti del codice.

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # print the event data
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time
        await partition_context.update_checkpoint(event)

    async def main():
        # create an Azure blob checkpoint store to store the checkpoints
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # create a consumer client for the event hub
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # call the receive method
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # run the main method
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Per il codice sorgente completo con commenti molto utili, vedere [questo file in GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py)


### <a name="run-the-receiver-app"></a>Eseguire l'app Receiver

Per eseguire lo script aprire un prompt dei comandi con un percorso contenente Python, quindi eseguire questo comando:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Eseguire l'app mittente

Per eseguire lo script aprire un prompt dei comandi con un percorso contenente Python, quindi eseguire questo comando:

```bash
python send.py
```

Verranno visualizzati i messaggi inviati all'hub eventi nella finestra del ricevitore.


## <a name="next-steps"></a>Passaggi successivi
In questa Guida introduttiva sono stati inviati e ricevuti eventi in modo asincrono. Per informazioni su come inviare e ricevere eventi in modo sincrono, vedere esempi in [questa posizione](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

[Qui](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)è possibile trovare tutti gli esempi (Sync e Async) in GitHub.
