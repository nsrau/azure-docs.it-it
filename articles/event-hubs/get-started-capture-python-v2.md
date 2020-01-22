---
title: Leggere i dati acquisiti da Hub eventi di Azure dall'app Python | Microsoft Docs
description: Questo articolo illustra come scrivere codice Python per acquisire i dati inviati a un hub eventi e leggere i dati degli eventi acquisiti da un'archiviazione di Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: spelluru
ms.openlocfilehash: 43223f7cb9ed254340c99d235d494d1e93583c7f
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293539"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-using-python"></a>Acquisire i dati di hub eventi in archiviazione di Azure e leggerli con Python 
È possibile usare configurare un hub eventi in modo che i dati inviati a un hub eventi vengano acquisiti in una risorsa di archiviazione di Azure o in Azure Data Lake Storage. Questo articolo illustra come usare scrivere codice Python per inviare eventi a un hub eventi e leggere i dati acquisiti da un archivio BLOB di Azure. Per altre informazioni su questa funzionalità, vedere [Panoramica della funzionalità di acquisizione di hub eventi](event-hubs-capture-overview.md).

Questo esempio usa [Azure Python SDK](https://azure.microsoft.com/develop/python/) per illustrare la funzionalità di acquisizione. Il programma sender.py invia una simulazione di telemetria ambientale a Hub eventi in formato JSON. L'hub eventi è configurato per l'uso della funzione Acquisisci per la scrittura di questi dati nell'archivio BLOB in batch. L'app capturereader.py legge questi BLOB e crea un file di accodamento per dispositivo. L'app quindi scrive i dati all'interno di file con estensione csv.

> [!IMPORTANT]
> Questa Guida introduttiva usa la versione 5 dell'SDK Python di hub eventi di Azure. Per una guida introduttiva che usa la versione 1 precedente di Python SDK, vedere [questo articolo](event-hubs-capture-python.md). Se si usa la versione 1 dell'SDK, è consigliabile eseguire la migrazione del codice alla versione più recente. Per informazioni dettagliate, vedere la [Guida alla migrazione](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).

Questa guida introduttiva spiega come: 

> [!div class="checklist"]
> * Creare un account di archiviazione e un contenitore BLOB di Azure nel portale di Azure.
> * Creare uno spazio dei nomi di hub eventi usando il portale di Azure.
> * Creare un hub eventi con la funzionalità di acquisizione abilitata e connetterla all'account di archiviazione.
> * Inviare i dati all'hub eventi tramite uno script Python.
> * Leggere ed elaborare i file ottenuti da Acquisizione di Hub eventi tramite un altro script Python.

## <a name="prerequisites"></a>Prerequisiti

- Python 2,7 e 3,5 o versioni successive, con `pip` installato e aggiornato.
- Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- [Creare uno spazio dei nomi di hub eventi e un hub eventi nello spazio dei nomi](event-hubs-create.md). Annotare il nome dello spazio dei nomi di hub eventi, il nome dell'hub eventi e la chiave di accesso primaria per lo spazio dei nomi. Ottenere la chiave di accesso seguendo le istruzioni dell'articolo: [ottenere la stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Il nome della chiave predefinita è: **RootManageSharedAccessKey**. Per l'esercitazione non è necessaria la stringa di connessione. È necessaria solo la chiave primaria. 
- Per creare un **account di archiviazione di Azure** e un **contenitore BLOB**, seguire questa procedura:
    1. [Creare un account di archiviazione di Azure](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal).
    2. [Creare un contenitore BLOB nella risorsa di archiviazione](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). 
    3. [Ottenere la stringa di connessione all'account di archiviazione](../storage/common/storage-configure-connection-string.md#view-and-copy-a-connection-string).

        Annotare la **stringa di connessione** e il **nome del contenitore**. Che sarà possibile usare in un secondo momento nel codice. 
- Abilitare la funzionalità di **acquisizione** per l'hub eventi seguendo le istruzioni riportate in [abilitare l'acquisizione di hub eventi usando il portale di Azure](event-hubs-capture-enable-through-portal.md). Selezionare l'account di archiviazione e il contenitore BLOB creato nel passaggio precedente. È anche possibile abilitare la funzionalità quando si crea un hub eventi. 

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Creazione di uno script Python per inviare gli eventi all'hub eventi
In questa sezione viene creato uno script Python che invia eventi 200 (10 dispositivi * 20 eventi) a un hub eventi. Questi eventi sono letture ambientali di esempio inviate in formato JSON. 

1. Aprire l'editor preferito di Python, ad esempio [Visual Studio Code][Visual Studio Code].
2. Creare uno script chiamato **sender.py**. 
3. Incollare il codice seguente in sender.py. Per informazioni dettagliate, vedere i commenti del codice. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # this scripts simulates production of events for 10 devices
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # create a producer client to produce/publish events to the event hub
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # for each device, produce 20 events 
        event_data_batch = producer.create_batch() # create a batch. you will add events to the batch later. 
        for dev in devices:
            # create a dummy reading
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # convert reading into a JSON string
            event_data_batch.add(EventData(s)) # add event data to the batch
        producer.send_batch(event_data_batch) # send the batch of events to the event hub
    
    # close the producer    
    producer.close()
    ```
4. Sostituire i valori seguenti negli script:
    1. Sostituire `EVENT HUBS NAMESPACE CONNECTION STRING` con la stringa di connessione per lo spazio dei nomi di hub eventi.
    2. Sostituire `EVENT HUB NAME` con il nome dell'hub eventi. 
5. Eseguire lo script per inviare eventi all'hub eventi. 
6. Nella portale di Azure è possibile verificare che l'hub eventi abbia ricevuto i messaggi. Passare alla visualizzazione **messaggi** nella sezione **metrica** . Aggiornare la pagina per aggiornare il grafico. Potrebbero essere necessari alcuni secondi per indicare che i messaggi sono stati ricevuti. 

    [![verificare che l'hub eventi abbia ricevuto i messaggi](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Creare uno script Python per leggere i file dell'acquisizione
In questo esempio, i dati acquisiti vengono archiviati nell'archivio BLOB di Azure. Lo script in questa sezione legge i file di dati di acquisizione dall'archiviazione di Azure e genera file CSV per facilitare l'apertura e la visualizzazione del contenuto. Nella directory di lavoro corrente dell'applicazione vengono visualizzati 10 file. Questi file conterranno le letture ambientali per i 10 dispositivi. 

1. Nell'editor di Python creare uno script denominato **capturereader.py**. Questo script legge i file di acquisizione e crea un file per dispositivo in modo da scrivere i dati solo per quel dispositivo.
2. Incollare il codice seguente in capturereader.py. Per informazioni dettagliate, vedere i commenti del codice. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # create a blob container client
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # list all the blobs in the container
        for blob in blob_list:
            #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # create a blob client for the blob
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # construct a file name based on the blob name
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # open the file to write. create if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # write blob contents into the file
                processBlob2(cleanName) # convert the file into a CSV file
                os.remove(cleanName) # remove the original downloaded file
                # delete the blob from the container after it's read
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
4. Sostituire `<AZURE STORAGE CONNECTION STRING>` con la stringa di connessione per l'account di archiviazione di Azure. Il nome del contenitore creato in questa esercitazione è: **Capture**. Se è stato usato un nome diverso per il contenitore, sostituire `capture` con il nome del contenitore nell'account di archiviazione. 

## <a name="run-the-scripts"></a>Esecuzione degli script
1. Aprire un prompt dei comandi con un percorso contenente Python, quindi eseguire questi comandi per installare i pacchetti dei prerequisiti di Python:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Modificare la directory nel punto in cui sono stati salvati sender.py e capturereader.py ed eseguire questo comando:
   
   ```
   python sender.py
   ```
   
   Questo comando avvia un nuovo processo di Python per eseguire il mittente.
3. Attendere qualche minuto il completamento dell'esecuzione dell'acquisizione. Digitare quindi il comando seguente nella finestra di comando originale:
   
   ```
   python capturereader.py
   ```

   Questo processore dell'acquisizione usa la directory locale per scaricare tutti i BLOB dal contenitore o dall'account di archiviazione. Elabora i BLOB non vuoti e scrive i risultati come file con estensione csv nella directory locale.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli esempi di Python in GitHub [qui](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
