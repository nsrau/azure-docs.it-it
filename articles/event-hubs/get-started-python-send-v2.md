---
title: Inviare o ricevere eventi da Hub eventi di Azure tramite Python (ultima versione)
description: Questo articolo illustra come creare un'applicazione Python che invia/riceve eventi a/da Hub eventi di Azure usando il pacchetto più recente azure-eventhub versione 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: d977ae9ea8b78664ac1d3a318f58553da696c089
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906350"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Inviare o ricevere eventi da Hub eventi tramite Python (azure-eventhub versione 5)

Hub eventi di Azure è una piattaforma di streaming per Big Data e un servizio di inserimento eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Gli hub eventi sono in grado di elaborare e archiviare eventi, dati o dati di telemetria prodotti da software distribuito e dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per altre informazioni, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

Questo argomento di avvio rapido descrive come creare applicazioni Python per inviare o ricevere eventi da un hub eventi.

> [!IMPORTANT]
> In questo argomento di avvio rapido si usa le versione 5 di Python SDK per Hub eventi di Azure. Per un argomento di avvio rapido che usa la versione 1 di Python SDK, vedere [questo articolo](event-hubs-python-get-started-send.md). 

## <a name="prerequisites"></a>Prerequisites

Per completare questa guida introduttiva è necessario soddisfare i prerequisiti seguenti:

- Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Uno spazio dei nomi di Hub eventi e un hub eventi attivi. Per crearli, seguire le istruzioni riportate in [Avvio rapido: Creare un hub eventi con il portale di Azure](event-hubs-create.md). Prendere nota dei nomi dello spazio dei nomi e dell'hub eventi per usarli più avanti in questo argomento di avvio rapido.
- Il nome della chiave di accesso condiviso e il valore della chiave primaria per lo spazio dei nomi di Hub eventi. Ottenere il nome e il valore della chiave di accesso seguendo le istruzioni riportate in [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Il nome della chiave di accesso predefinita è *RootManageSharedAccessKey*. Prendere nota del nome e del valore della chiave primaria per usarli più avanti in questo argomento di avvio rapido.
- Python 2.7 o 3.5 o versione successiva, con PIP installato e aggiornato.
- Il pacchetto Python per Hub eventi. 

    Per installare il pacchetto, eseguire questo comando in un prompt dei comandi il cui percorso contenga Python:

    ```cmd
    pip install azure-eventhub
    ```

    Installare il pacchetto seguente per ricevere gli eventi usando archiviazione BLOB di Azure come archivio di checkpoint:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```

## <a name="send-events"></a>Inviare eventi
In questa sezione viene creato uno script Python per inviare eventi all'hub eventi creato in precedenza.

1. Aprire l'editor preferito di Python, ad esempio [Visual Studio Code](https://code.visualstudio.com/).
2. Creare uno script denominato *send.py*. Questo script invia un batch di eventi all'hub eventi creato in precedenza.
3. Incollare il codice seguente in *send.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
            # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > Per il codice sorgente completo, inclusi i commenti informativi aggiuntivi, passare alla [pagina send_async.py di GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="receive-events"></a>Ricevere eventi
In questo argomento di avvio rapido si usa archiviazione BLOB di Azure come archivio di checkpoint. L'archivio di checkpoint viene usato per rendere persistenti i checkpoint (ovvero le ultime posizioni di lettura).  

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Creare un account di archiviazione di Azure e un contenitore BLOB
Per creare un account di archiviazione di Azure e un contenitore BLOB al suo interno, eseguire queste operazioni:

1. [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Creare un contenitore BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Ottenere la stringa di connessione all'account di archiviazione](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Assicurarsi di prendere nota della stringa di connessione e del nome del contenitore per un uso successivo nel codice di ricezione.


### <a name="create-a-python-script-to-receive-events"></a>Creazione di uno script Python per ricevere gli eventi

In questa sezione viene creato uno script Python per ricevere eventi dall'hub eventi:

1. Aprire l'editor preferito di Python, ad esempio [Visual Studio Code](https://code.visualstudio.com/).
2. Creare uno script denominato *recv.py*.
3. Incollare il codice seguente in *recv.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method.
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Per il codice sorgente completo, inclusi i commenti informativi aggiuntivi, passare alla [pagina recv_with_checkpoint_store_async.py di GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


### <a name="run-the-receiver-app"></a>Eseguire l'app ricevente

Per eseguire lo script aprire un prompt dei comandi con un percorso contenente Python, quindi eseguire questo comando:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Eseguire l'app mittente

Per eseguire lo script aprire un prompt dei comandi con un percorso contenente Python, quindi eseguire questo comando:

```bash
python send.py
```

La finestra ricevente dovrebbe visualizzare i messaggi inviati all'hub eventi.


## <a name="next-steps"></a>Passaggi successivi
In questo argomento di avvio rapido sono stati inviati e ricevuti eventi in modo asincrono. Per informazioni su come inviare e ricevere eventi in modo sincrono, passare alla [pagina sync_samples di GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Per tutti gli esempi (sincroni e asincroni) di GitHub, passare alla [libreria client di Hub eventi di Azure per gli esempi di Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
