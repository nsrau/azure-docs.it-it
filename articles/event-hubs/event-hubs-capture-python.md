---
title: Lettura dei dati acquisiti dall'app Python - Hub eventi di Azure | Microsoft Docs
description: Script che usano Azure Python SDK per illustrare la funzionalità di acquisizione di hub eventi.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 10/10/2019
ms.author: shvija
ms.openlocfilehash: 354964e1b66b55dcccd9b5674f011f8c5a38a1c5
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428936"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Procedura dettagliata sull'acquisizione di Hub eventi: Python

L'acquisizione è una funzionalità di Hub eventi di Azure. È possibile usare Acquisisci per recapitare automaticamente i dati in streaming nell'hub eventi a un account di archiviazione BLOB di Azure di propria scelta. Questa funzionalità consente di semplificare l'elaborazione batch sui dati in streaming in tempo reale. In questo articolo viene descritto come usare l'acquisizione di Hub eventi con Python. Per altre informazioni sull'acquisizione di hub eventi, vedere [acquisizione di eventi tramite hub eventi di Azure][Overview of Event Hubs Capture].

Questa procedura dettagliata USA [Azure Python SDK](https://azure.microsoft.com/develop/python/) per illustrare la funzionalità di acquisizione. Il programma *sender.py* invia dati di telemetria ambientali simulati a hub eventi in formato JSON. L'hub eventi usa la funzionalità di acquisizione per scrivere i dati nell'archivio BLOB in batch. L'app *capturereader.py* legge questi BLOB, crea un file di Accodamento per ogni dispositivo e scrive i dati in file con *estensione CSV* in ogni dispositivo.

Questa procedura dettagliata è costituita dai passaggi seguenti: 

> [!div class="checklist"]
> * Creare un account e un contenitore di archiviazione BLOB di Azure nell'portale di Azure.
> * Abilitare l'acquisizione di hub eventi e indirizzarla all'account di archiviazione.
> * Inviare i dati all'hub eventi usando uno script Python.
> * Leggere ed elaborare i file dall'acquisizione di hub eventi usando un altro script Python.

## <a name="prerequisites"></a>Prerequisiti

- Python 3,4 o versione successiva, con `pip` installato e aggiornato.
  
- Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
  
- Uno spazio dei nomi di hub eventi attivo e un hub eventi, creato seguendo le istruzioni disponibili in [Guida introduttiva: creare un hub eventi usando portale di Azure](event-hubs-create.md). Prendere nota dei nomi degli spazi dei nomi e degli hub eventi da usare più avanti in questa procedura dettagliata. 
  
  > [!NOTE]
  > Se è già presente un contenitore di archiviazione da usare, è possibile abilitare l'acquisizione e selezionare il contenitore di archiviazione quando si crea l'hub eventi. 
  > 
  
- Il nome della chiave di accesso condiviso di hub eventi e il valore della chiave primaria. Trovare o creare questi valori in **criteri di accesso condiviso** nella pagina Hub eventi. Il nome predefinito della chiave di accesso è **RootManageSharedAccessKey**. Copiare il nome della chiave di accesso e il valore della chiave primaria da usare più avanti in questa procedura dettagliata. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Creare un account e un contenitore di archiviazione BLOB di Azure

Creare un account di archiviazione e un contenitore da usare per l'acquisizione. 

1. Accedere al [portale di Azure][Azure portal].
2. Nel percorso di spostamento a sinistra selezionare **account di archiviazione**e nella schermata account di **archiviazione** Selezionare **Aggiungi**.
3. Nella schermata di creazione dell'account di archiviazione selezionare una sottoscrizione e un gruppo di risorse e assegnare un nome all'account di archiviazione. Per impostazione predefinita, è possibile lasciare le altre selezioni. Selezionare **Verifica e crea**, rivedere le impostazioni e quindi selezionare **Crea**. 
   
   ![Crea account di archiviazione][1]
   
4. Al termine della distribuzione, selezionare **Vai alla risorsa**e nella schermata di **Panoramica** dell'account di archiviazione selezionare **contenitori**.
5. Nella schermata **contenitori** selezionare **+ contenitore**. 
6. Nella schermata **nuovo contenitore** assegnare un nome al contenitore e quindi fare clic su **OK**. Prendere nota del nome del contenitore da usare più avanti nella procedura dettagliata. 
7. Nella finestra di spostamento a sinistra della schermata **contenitori** selezionare **chiavi di accesso**. Copiare il **nome dell'account di archiviazione**e il valore della **chiave** in **Key1**, da usare più avanti nella procedura dettagliata.
 
## <a name="enable-event-hubs-capture"></a>Abilitare l'acquisizione di hub eventi

1. Nella portale di Azure passare all'hub eventi selezionando il relativo spazio dei nomi di hub eventi da **tutte le risorse**, selezionando **Hub eventi** nel pannello di navigazione a sinistra e quindi selezionando l'hub eventi. 
2. Nella schermata **Panoramica** di hub eventi selezionare **Acquisisci eventi**.
3. Nella schermata **Acquisisci** selezionare **on**. Quindi, in **contenitore di archiviazione di Azure**selezionare **Seleziona contenitore**. 
4. Nella schermata **contenitori** selezionare il contenitore di archiviazione che si vuole usare e quindi selezionare **Seleziona**. 
5. Nella schermata **Acquisisci** selezionare **Salva modifiche**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Creare uno script Python per inviare eventi all'hub eventi
Questo script invia 200 eventi all'hub eventi. Gli eventi sono semplici letture ambientali inviate in JSON.

1. Aprire l'editor preferito di Python, ad esempio [Visual Studio Code][Visual Studio Code].
2. Creare un nuovo file denominato *sender.py*. 
3. Incollare il codice seguente in *sender.py*. Sostituire i valori personalizzati per gli hub eventi \<namespace >, \<AccessKeyName >, \<primary chiave value > e \<eventhub >.
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='<namespace>', shared_access_key_name='<AccessKeyName>', shared_access_key_value='<primary key value>')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('<eventhub>', s)
       print(y)
   ```
4. Salvare il file.

## <a name="create-a-python-script-to-read-capture-files"></a>Creare uno script Python per leggere i file di acquisizione

Questo script legge i file acquisiti e crea un file per ogni dispositivo per scrivere i dati solo per quel dispositivo.

1. Nell'editor di Python creare un nuovo file denominato *capturereader.py*. 
2. Incollare il codice seguente in *capturereader.py*. Sostituire i valori salvati per il > \<storageaccount, la chiave di accesso dell'account \<storage > e @no__t >-2storagecontainer.
   
   ```python
   import os
   import string
   import json
   import avro.schema
   from avro.datafile import DataFileReader, DataFileWriter
   from avro.io import DatumReader, DatumWriter
   from azure.storage.blob import BlockBlobService
   
   def processBlob(filename):
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
           deviceFile = open(device + '.csv', "a")
           for r in dict[device]:
               deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
   
   def startProcessing(accountName, key, container):
       print('Processor started using path: ' + os.getcwd())
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = str.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('<storageaccount>', '<storage account access key>', '<storagecontainer>')
   ```

## <a name="run-the-python-scripts"></a>Eseguire gli script Python

1. Aprire un prompt dei comandi con Python nel percorso ed eseguire questi comandi per installare i pacchetti di prerequisiti per Python:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Se si dispone di una versione precedente di `azure-storage` o `azure`, potrebbe essere necessario usare l'opzione `--upgrade`.
   
   Potrebbe anche essere necessario eseguire il comando seguente. L'esecuzione di questo comando non è necessaria nella maggior parte dei sistemi. 
   
   ```cmd
   pip install cryptography
   ```
   
2. Eseguire questo comando dalla directory in cui sono stati salvati *sender.py* e *capturereader.py*:
   
   ```cmd
   start python sender.py
   ```
   
   Il comando avvia un nuovo processo Python per eseguire il mittente.
   
3. Al termine dell'esecuzione dell'acquisizione, eseguire questo comando:
   
   ```cmd
   python capturereader.py
   ```

   Il processore di acquisizione Scarica tutti i BLOB non vuoti dal contenitore dell'account di archiviazione e scrive i risultati come file con *estensione CSV* nella directory locale. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Hub eventi, vedere: 

* [Panoramica dell'acquisizione di hub eventi][Overview of Event Hubs Capture]
* [Applicazioni di esempio che usano Hub eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Panoramica di Hub eventi][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
