---
title: "Procedura dettagliata sull’acquisizione di Hub eventi di Azure | Documentazione Microsoft"
description: "Un esempio che usa l'SDK di Azure Python per illustrare l'uso della funzionalità di acquisizione dell'Hub eventi."
services: event-hubs
documentationcenter: 
author: djrosanova
manager: timlt
editor: 
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 5fb691ec53fed20e5df4f581da10b964c07e09b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-capture-walkthrough-python"></a>Procedura dettagliata sull'acquisizione di Hub eventi: Python

Acquisisci è una nuova funzionalità di Hub eventi che consente di distribuire automaticamente i dati di streaming dell'hub eventi a un account di archiviazione BLOB di Azure di propria scelta. Questa capacità rende più semplice eseguire l'elaborazione di batch su dati di streaming in tempo reale. In questo articolo viene descritto come usare l'acquisizione di Hub eventi con Python. Per altre informazioni sull'acquisizione di Hub eventi, vedere l' [articolo con la panoramica](event-hubs-capture-overview.md).

Questo esempio usa [Azure Python SDK](https://azure.microsoft.com/develop/python/) per illustrare la funzionalità di acquisizione. Il programma sender.py invia una simulazione di telemetria ambientale a Hub eventi in formato JSON. L'hub eventi è configurato per usare la funzione Acquisisci per scrivere i dati nell'archiviazione BLOB in batch. L'app capturereader.py legge quindi questi BLOB, crea un file aggiuntivo per dispositivo, quindi scrive i dati in file con estensione csv.

## <a name="what-will-be-accomplished"></a>Contenuto dell'esercitazione

1. Creazione di un account di archiviazione BLOB di Azure e di un contenitore BLOB all'interno nel portale di Azure.
2. Creazione di uno spazio dei nomi di Hub eventi nel portale di Azure.
3. Creazione di un hub eventi con la funzione Acquisisci abilitata nel portale di Azure.
4. Invio dei dati all'hub eventi con uno script Python.
5. Lettura dei file dall'acquisizione ed elaborazione con un altro script Python.

## <a name="prerequisites"></a>Prerequisiti

- Python 2.7.x
- Una sottoscrizione di Azure.
- Uno [spazio dei nomi di Hub eventi attivo e hub eventi.](event-hubs-create.md)

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-storage-account"></a>Creare un account di Archiviazione di Azure
1. Accedere al [portale di Azure][Azure portal].
2. Nel riquadro di spostamento sinistro del portale fare clic su **Nuovo**, quindi su **Archiviazione** e quindi su **Account di archiviazione**.
3. Completare i campi nel pannello dell'account di archiviazione e quindi fare clic su **Crea**.
   
   ![][1]
4. Dopo aver visualizzato il messaggio **Deployments Succeeded** (Le distribuzioni sono riuscite), fare clic sul nome del nuovo account di archiviazione e nel pannello **Informazioni di base** fare clic su **BLOB**. Quando si apre il pannello **Servizio BLOB**, fare clic su **+ Contenitore** in alto. Assegnare al contenitore il nome **acquisizione**, quindi chiudere il pannello **Servizio BLOB**.
5. Fare clic su **Chiavi di accesso** nel pannello sinistro e copiare il nome dell'account di archiviazione e il valore **key1**. Salvare questi valori nel Blocco note o in un'altra posizione temporanea.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Creazione di uno script Python per inviare gli eventi all'hub eventi
1. Aprire l'editor preferito di Python, ad esempio[Visual Studio Code][Visual Studio Code].
2. Creare uno script chiamato **sender.py**. Questo script invia 200 eventi all'hub eventi. Si tratta di semplici letture ambientali inviate in JSON.
3. Incollare il seguente codice in sender.py:
   
  ```python
  import uuid
  import datetime
  import random
  import json
  from azure.servicebus import ServiceBusService
   
  sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
  devices = []
  for x in range(0, 10):
      devices.append(str(uuid.uuid4()))
   
  for y in range(0,20):
      for dev in devices:
          reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
          s = json.dumps(reading)
          sbs.send_event('INSERT YOUR EVENT HUB NAME', s)
      print y
  ```
4. Aggiornare il codice precedente in modo che usi il nome dello spazio dei nomi, il valore chiave e il nome dell'hub eventi ottenuti al momento della creazione dello spazio dei nomi dell'hub eventi.

## <a name="create-a-python-script-to-read-your-capture-files"></a>Creare uno script Python per leggere i file dell'acquisizione

1. Compilare il pannello e fare clic su **Crea**.
2. Creare uno script chiamato **capturereader.py**. Questo script legge i file di acquisizione e crea un file per dispositivo in modo da scrivere i dati solo per quel dispositivo.
3. Incollare il seguente codice in capturereader.py:
   
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
          if not dict.has_key(parsed_json['id']):
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
      print 'Processor started using path: ' + os.getcwd()
      block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
      generator = block_blob_service.list_blobs(container)
      for blob in generator:
          if blob.properties.content_length != 0:
              print('Downloaded a non empty blob: ' + blob.name)
              cleanName = string.replace(blob.name, '/', '_')
              block_blob_service.get_blob_to_path(container, blob.name, cleanName)
              processBlob(cleanName)
              os.remove(cleanName)
          block_blob_service.delete_blob(container, blob.name)
  startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
  ```
4. Assicurarsi di incollare i valori appropriati per il nome e la chiave dell'account di archiviazione e nella chiamata a `startProcessing`.

## <a name="run-the-scripts"></a>Esecuzione degli script
1. Aprire un prompt dei comandi con un percorso contenente Python, quindi eseguire questi comandi per installare i pacchetti dei prerequisiti di Python:
   
  ```
  pip install azure-storage
  pip install azure-servicebus
  pip install avro
  ```
   
  Se si dispone di una versione precedente di Azure o dell'archiviazione di Azure, potrebbe essere necessario usare l'opzione **aggiornamento**
   
  Potrebbe inoltre essere necessario eseguire il comando seguente (non necessario nella maggior parte dei sistemi):
   
  ```
  pip install cryptography
  ```
2. Modificare la directory nel punto in cui sono stati salvati sender.py e capturereader.py ed eseguire questo comando:
   
  ```
  start python sender.py
  ```
   
  Questo comando avvia un nuovo processo di Python per eseguire il mittente.
3. A questo punto attendere alcuni minuti per l'esecuzione dell'acquisizione. Digitare quindi il comando seguente nella finestra di comando originale:
   
   ```
   python capturereader.py
   ```

   Questo processore dell'acquisizione usa la directory locale per scaricare tutti i BLOB dal contenitore o dall'account di archiviazione. Vengono elaborati i BLOB non vuoti e i risultati vengono scritti come file con estensione csv nella directory locale.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica dell'acquisizione di Hub eventi][Overview of Event Hubs Capture]
* [Applicazioni di esempio che usano Hub eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Panoramica di Hub eventi][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
