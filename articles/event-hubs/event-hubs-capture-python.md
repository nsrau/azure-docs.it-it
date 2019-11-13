---
title: "Guida introduttiva: Leggere i dati acquisiti dall'app Python - Hub eventi di Azure"
description: 'Guida introduttiva: Script che usano Azure Python SDK per illustrare la funzionalità Acquisizione di Hub eventi.'
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
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: ade4aa79b2de005bfecd7a5882f06cb491ea4e6d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717851"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python"></a>Guida introduttiva: Procedura dettagliata sull'acquisizione di Hub eventi: Python

L'acquisizione è una funzionalità di Hub eventi di Azure. È possibile usare la funzionalità Acquisizione per inviare automaticamente i dati di streaming dell'hub eventi a un determinato account di archiviazione BLOB di Azure. Questa funzionalità semplifica l'esecuzione dell'elaborazione batch su dati di streaming in tempo reale. In questo articolo viene descritto come usare l'acquisizione di Hub eventi con Python. Per altre informazioni su Acquisizione di Hub eventi, vedere [Acquisire eventi tramite Hub eventi di Azure][Overview of Event Hubs Capture].

Questa procedura dettagliata usa [Azure Python SDK](https://azure.microsoft.com/develop/python/) per illustrare la funzionalità Acquisizione. Il programma *sender.py* invia una simulazione di telemetria ambientale a Hub eventi in formato JSON. L'hub eventi usa la funzionalità Acquisizione per scrivere i dati in batch nell'archiviazione BLOB. L'app *capturereader.py* legge questi BLOB, crea un file di accodamento per ogni dispositivo e scrive i dati in file con estensione *csv* in ogni dispositivo.

Questa procedura dettagliata è costituita dai passaggi seguenti: 

> [!div class="checklist"]
> * Creare un account di archiviazione e un contenitore BLOB di Azure nel portale di Azure.
> * Abilitare Acquisizione di Hub eventi e indirizzarla al proprio account di archiviazione.
> * Inviare i dati all'hub eventi tramite uno script Python.
> * Leggere ed elaborare i file ottenuti da Acquisizione di Hub eventi tramite un altro script Python.

## <a name="prerequisites"></a>Prerequisiti

- Python 3.4 o versione successiva con `pip` installato e aggiornato.
  
- Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
  
- Uno spazio dei nomi Hub eventi e un hub eventi attivi, creati seguendo le istruzioni disponibili in [Avvio rapido: Creare un hub eventi con il portale di Azure](event-hubs-create.md). Prendere nota dei nomi dello spazio dei nomi e dell'hub eventi da usare più avanti in questa procedura dettagliata. 
  
  > [!NOTE]
  > Se si ha già un contenitore di archiviazione da usare, è possibile abilitare la funzionalità Acquisizione e selezionare il contenitore di archiviazione durante la creazione dell'hub eventi. 
  > 
  
- Il nome della chiave di accesso condiviso e il valore della chiave primaria di Hub eventi. Trovare o creare questi valori in **Criteri di accesso condiviso** nella pagina Hub eventi. Il nome della chiave di accesso predefinita è **RootManageSharedAccessKey**. Copiare il nome della chiave di accesso e il valore della chiave primaria da usare più avanti in questa procedura dettagliata. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Creare un account di archiviazione e un contenitore BLOB di Azure

Creare un account di archiviazione e un contenitore da usare per l'acquisizione. 

1. Accedere al [portale di Azure][Azure portal].
2. Nel riquadro di spostamento a sinistra selezionare **Account di archiviazione** e nella schermata **Account di archiviazione** selezionare **Aggiungi**.
3. Nella schermata di creazione dell'account di archiviazione selezionare una sottoscrizione e un gruppo di risorse. Assegnare un nome all'account di archiviazione. Lasciare le impostazioni predefinite per le altre selezioni. Selezionare **Rivedi e crea**, esaminare le impostazioni e quindi selezionare **Crea**. 
   
   ![Crea account di archiviazione][1]
   
4. Al termine della distribuzione, selezionare **Vai alla risorsa** e nella schermata **Panoramica** dell'account di archiviazione selezionare **Contenitori**.
5. Nella schermata **Contenitori** selezionare **+ Contenitore**. 
6. Nella schermata **Nuovo contenitore** assegnare un nome al contenitore e quindi selezionare **OK**. Prendere nota del nome del contenitore da usare più avanti nella procedura dettagliata. 
7. Nel riquadro di spostamento di sinistra della schermata **Contenitori** selezionare **Chiavi di accesso**. Copiare il valore di **Nome account di archiviazione** e il valore del campo **Chiave** in **Key1**, da usare più avanti nella procedura dettagliata.
 
## <a name="enable-event-hubs-capture"></a>Abilitare Acquisizione di Hub eventi

1. Nel portale di Azure passare all'hub eventi selezionando il relativo spazio dei nomi Hub eventi da **Tutte le risorse**, quindi **Hub eventi** nel riquadro di spostamento di sinistra e selezionando infine l'hub eventi. 
2. Nella schermata **Panoramica** dell'hub eventi selezionare **Acquisisci eventi**.
3. Nella schermata **Acquisizione** selezionare **Attivata**. Quindi, in **Contenitore di archiviazione di Azure** fare clic su **Selezionare il contenitore**. 
4. Nella schermata **Contenitori** selezionare il contenitore di archiviazione che si vuole usare e quindi **Seleziona**. 
5. Nella schermata **Acquisizione** selezionare **Salva le modifiche**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Creare uno script Python per inviare eventi all'hub eventi
Questo script invia 200 eventi all'hub eventi. Gli eventi sono semplici letture ambientali inviate in JSON.

1. Aprire l'editor preferito di Python, ad esempio [Visual Studio Code][Visual Studio Code].
2. Creare un nuovo file denominato *sender.py*. 
3. Incollare il codice seguente in *sender.py*. Sostituire \<namespace>, \<AccessKeyName>, \<primary key value> e \<eventhub> con i propri valori per Hub eventi.
   
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

Questo script legge i file acquisiti e crea un file per ognuno dei dispositivi in modo da scrivere i dati solo per quel dispositivo.

1. Nell'editor di Python creare un nuovo file denominato *capturereader.py*. 
2. Incollare il codice seguente in *capturereader.py*. Sostituire \<storageaccount>, \<storage account access key> e \<storagecontainer> con i valori salvati.
   
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

1. Aprire un prompt dei comandi con un percorso contenente Python ed eseguire questi comandi per installare i pacchetti dei prerequisiti di Python:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Se si ha una versione precedente di `azure-storage` o `azure`, potrebbe essere necessario usare l'opzione `--upgrade`.
   
   Potrebbe anche essere necessario eseguire il comando riportato di seguito. L'esecuzione di questo comando non è necessaria nella maggior parte dei sistemi. 
   
   ```cmd
   pip install cryptography
   ```
   
2. Dalla directory in cui sono stati salvati *sender.py* e *capturereader.py* eseguire questo comando:
   
   ```cmd
   start python sender.py
   ```
   
   Il comando avvia un nuovo processo di Python per eseguire il mittente.
   
3. Al termine dell'esecuzione dell'acquisizione, eseguire questo comando:
   
   ```cmd
   python capturereader.py
   ```

   Il processore di acquisizione scarica tutti i BLOB non vuoti dal contenitore dell'account di archiviazione e scrive i risultati come file con estensione *csv* nella directory locale. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Hub eventi, vedere: 

* [Panoramica dell'acquisizione di Hub eventi][Overview of Event Hubs Capture]
* [Applicazioni di esempio che usano Hub eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Panoramica di Hub eventi][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
