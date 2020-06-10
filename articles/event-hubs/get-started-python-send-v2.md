---
title: Inviare o ricevere eventi da Hub eventi di Azure tramite Python (ultima versione)
description: Questo articolo illustra come creare un'applicazione Python che invia/riceve eventi a/da Hub eventi di Azure usando il pacchetto più recente azure-eventhub versione 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.custom: tracking-python
ms.openlocfilehash: 7550ba3883503c5991cd14e80354b187116a3b51
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560284"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Inviare o ricevere eventi da Hub eventi tramite Python (azure-eventhub versione 5)
Questa guida di avvio rapido illustra come inviare e ricevere eventi da un hub eventi con il pacchetto Python **azure-eventhub versione 5**.

> [!IMPORTANT]
> In questa guida di avvio rapido si usa il pacchetto azure-eventhub versione 5. Per una guida di avvio rapido che usa la versione 1 del pacchetto azure-eventhub, vedere [Inviare e ricevere eventi con azure-eventhub versione 1](event-hubs-python-get-started-send.md). 

## <a name="prerequisites"></a>Prerequisiti
Se non si ha familiarità con Hub eventi di Azure, vedere [Panoramica di Hub eventi](event-hubs-about.md) prima di procedere con questa guida di avvio rapido. 

Per completare questa guida introduttiva è necessario soddisfare i prerequisiti seguenti:

- **Sottoscrizione di Microsoft Azure**. Per usare i servizi di Azure, tra cui Hub eventi di Azure, è necessaria una sottoscrizione.  Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/free/) oppure usare i vantaggi della sottoscrizione di MSDN per la [creazione di un account](https://azure.microsoft.com).
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
- **Creare uno spazio dei nomi di Hub eventi e un hub eventi**. Il primo passaggio consiste nell'usare il [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo Hub eventi e ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md). Ottenere quindi la **stringa di connessione allo spazio dei nomi di Hub eventi** seguendo le istruzioni disponibili nell'articolo [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). La stringa di connessione sarà necessaria più avanti in questa guida di avvio rapido.

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

> [!NOTE]
> Se il codice viene eseguito in Hub di Azure Stack, questa piattaforma potrebbe supportare una versione diversa dell'SDK di archiviazione BLOB rispetto a quelle disponibili in Azure. Se ad esempio l'esecuzione avviene nell'[hub di Azure Stack versione 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), la versione più recente disponibile per il servizio di archiviazione è 2017-11-09. In questo caso, oltre ai passaggi descritti in questa sezione, sarà anche necessario aggiungere codice destinato alla versione 2017-11-09 dell'API del servizio di archiviazione. Per informazioni su come definire come destinazione una versione specifica dell'API di archiviazione, vedere gli esempi [sincroni](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) e [asincroni](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py) in GitHub. Per altre informazioni sulle versioni del servizio di archiviazione di Azure supportate in Hub di Azure Stack, vedere [Archiviazione nell'hub di Azure Stack: differenze e considerazioni](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Creare un account di archiviazione di Azure e un contenitore BLOB
Per creare un account di archiviazione di Azure e un contenitore BLOB al suo interno, eseguire queste operazioni:

1. [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Creare un contenitore BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Ottenere la stringa di connessione all'account di archiviazione](../storage/common/storage-configure-connection-string.md)

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
