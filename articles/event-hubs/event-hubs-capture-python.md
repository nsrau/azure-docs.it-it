---
title: Leggere i dati acquisiti da Hub eventi di Azure in un'app Python (ultima versione)
description: Questo articolo illustra come scrivere codice Python per acquisire i dati inviati a un hub eventi e leggere i dati degli eventi acquisiti da un account di archiviazione di Azure.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: cb7165565516136a8425c4c77748c2e13715edb7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88927868"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub-version-5"></a>Acquisire i dati di Hub eventi in archiviazione di Azure e leggerli con Python (azure-eventhub versione 5)

È possibile configurare un hub eventi in modo che i dati che riceve vengano acquisiti in un account di archiviazione di Azure o in Azure Data Lake Storage Gen 1 o Gen 2. Questo articolo illustra come scrivere codice Python per inviare eventi a un hub eventi e leggere i dati acquisiti da **Archiviazione BLOB di Azure**. Per altre informazioni su questa funzionalità, vedere la [Panoramica sulle funzionalità Acquisizione di hub eventi](event-hubs-capture-overview.md).

In questo argomento di avvio rapido si usa [Azure Python SDK](https://azure.microsoft.com/develop/python/) per illustrare la funzionalità Acquisizione. L'app *sender.py* invia dati di telemetria ambientale simulati a hub eventi in formato JSON. L'hub eventi è configurato per l'uso della funzione Acquisisci per la scrittura di questi dati nell'archivio BLOB in batch. L'app *capturereader.py* legge questi BLOB e crea un file di accodamento per ogni dispositivo. L'app quindi scrive i dati all'interno di file CSV.

Questa guida introduttiva spiega come: 

> [!div class="checklist"]
> * Creare un account di archiviazione e un contenitore BLOB di Azure nel portale di Azure.
> * Creazione di uno spazio dei nomi di Hub eventi tramite il portale di Azure.
> * Creare un hub eventi con la funzionalità Acquisizione abilitata e connetterlo all'account di archiviazione.
> * Inviare i dati all'hub eventi tramite uno script Python.
> * Leggere ed elaborare i file ottenuti da Acquisizione di Hub eventi tramite un altro script Python.

## <a name="prerequisites"></a>Prerequisiti

- Python 2.7 e 3.5 o versione successiva con PIP installato e aggiornato.  
- Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.  
- Uno spazio dei nomi di Hub eventi e un hub eventi attivi.
[Creare uno spazio dei nomi di Hub eventi e un hub eventi al suo interno](event-hubs-create.md). Prendere nota del nome dello spazio dei nomi di Hub eventi, del nome dell'hub eventi e della chiave di accesso primaria per lo spazio dei nomi. Per ottenere la chiave di accesso, vedere [Ottenere una stringa di connessione di Hub eventi](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Il nome della chiave predefinita è *RootManageSharedAccessKey*. Per questo argomento di avvio rapido, è necessaria solo la chiave primaria. La stringa di connessione non è necessaria.  
- Un account di archiviazione di Azure, un contenitore BLOB al suo interno e una stringa di connessione all'account di archiviazione. Se questi elementi non sono disponibili, procedere come segue:  
    1. [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal)  
    1. [Creare un contenitore BLOB nell'account di archiviazione](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [Ottenere la stringa di connessione all'account di archiviazione](../storage/common/storage-configure-connection-string.md)

    Assicurarsi di prendere nota della stringa di connessione e del nome del contenitore per un uso successivo in questo argomento.  
- Abilitare la funzionalità Acquisizione per l'hub eventi. A tale scopo, seguire le istruzioni riportate in [Abilitare Acquisizione di Hub eventi usando il portale di Azure](event-hubs-capture-enable-through-portal.md). Selezionare l'account di archiviazione nel contenitore BLOB creato nel passaggio precedente. È anche possibile abilitare la funzionalità quando si crea un hub eventi.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Creazione di uno script Python per inviare gli eventi all'hub eventi
In questa sezione viene creato uno script Python che invia eventi 200 (10 dispositivi * 20 eventi) a un hub eventi. Questi eventi sono esempi di letture ambientali inviate in formato JSON. 

1. Aprire l'editor preferito di Python, ad esempio [Visual Studio Code][Visual Studio Code].
2. Creare uno script chiamato *sender.py*. 
3. Incollare il codice seguente in *sender.py*. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # This script simulates the production of events for 10 devices.
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # Create a producer client to produce and publish events to the event hub.
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # For each device, produce 20 events. 
        event_data_batch = producer.create_batch() # Create a batch. You will add events to the batch later. 
        for dev in devices:
            # Create a dummy reading.
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # Convert the reading into a JSON string.
            event_data_batch.add(EventData(s)) # Add event data to the batch.
        producer.send_batch(event_data_batch) # Send the batch of events to the event hub.
    
    # Close the producer.    
    producer.close()
    ```
4. Sostituire i valori seguenti negli script:  
    * Sostituire `EVENT HUBS NAMESPACE CONNECTION STRING` con la stringa di connessione per lo spazio dei nomi di Hub eventi.  
    * Sostituire `EVENT HUB NAME` con il nome dell'hub eventi.  
5. Eseguire lo script per inviare gli eventi all'hub eventi.  
6. Nel portale di Azure è possibile verificare se l'hub eventi ha ricevuto i messaggi. Passare alla visualizzazione **Messaggi** nella sezione **Metriche**. Aggiornare la pagina per aggiornare il grafico. La pagina di conferma che i messaggi sono stati ricevuti potrebbe comparire dopo alcuni secondi. 

    [![Verificare se l'hub eventi ha ricevuto i messaggi](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Creare uno script Python per leggere i file dell'acquisizione
In questo esempio i dati acquisiti vengono archiviati in archiviazione BLOB di Azure. Lo script di questa sezione legge i file di dati acquisiti dall'account di archiviazione di Azure e genera i file CSV che è possibile aprire e visualizzare con facilità. Nella directory di lavoro corrente dell'applicazione vengono visualizzati 10 file. Questi file conterranno le letture ambientali per i 10 dispositivi. 

1. Nell'editor di Python creare un nuovo file denominato *capturereader.py*. Questo script legge i file acquisiti e crea un file per ogni dispositivo, in modo da scrivere i dati solo per tale dispositivo.
2. Incollare il codice seguente in *capturereader.py*. 
   
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
        # Create a blob container client.
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # List all the blobs in the container.
        for blob in blob_list:
            # Content_length == 508 is an empty file, so process only content_length > 508 (skip empty files).        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # Create a blob client for the blob.
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # Construct a file name based on the blob name.
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # Open the file to write. Create it if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # Write blob contents into the file.
                processBlob2(cleanName) # Convert the file into a CSV file.
                os.remove(cleanName) # Remove the original downloaded file.
                # Delete the blob from the container after it's read.
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
3. Sostituire `AZURE STORAGE CONNECTION STRING` con la stringa di connessione per l'account di archiviazione di Azure. Il nome del contenitore creato in questo argomento di avvio rapido è *capture*. Se è stato usato un nome diverso per il contenitore, sostituire *capture* con il nome del contenitore nell'account di archiviazione. 

## <a name="run-the-scripts"></a>Esecuzione degli script
1. Aprire un prompt dei comandi con un percorso contenente Python, quindi eseguire questi comandi per installare i pacchetti dei prerequisiti di Python:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Passare alla directory in cui sono stati salvati i file *sender.py* e *capturereader.py* ed eseguire questo comando:
   
   ```
   python sender.py
   ```
   
   Questo comando avvia un nuovo processo di Python per eseguire il mittente.
3. Attendere alcuni minuti che venga eseguita l'acquisizione e quindi immettere il comando seguente nella finestra di comando originale:
   
   ```
   python capturereader.py
   ```

   Questo processore di acquisizione usa la directory locale per scaricare tutti i BLOB dall'account di archiviazione e dal contenitore. Elabora quelli che non sono vuoti e scrive i risultati come file CSV nella directory locale.

## <a name="next-steps"></a>Passaggi successivi
Esaminare gli [esempi di Python in GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: ./event-hubs-about.md
