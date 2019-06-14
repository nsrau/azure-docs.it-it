---
title: Usare Python per inviare e ricevere messaggi - Hub eventi di Azure | Microsoft Docs
description: Informazioni su come inviare eventi, ricevere eventi e acquisire lo streaming di eventi con Hub eventi utilizzando Python.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: 88fdaec9e19c082a6fe981dc4d9a0e015335f1e2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60202974"
---
# <a name="how-to-use-azure-event-hubs-from-a-python-application"></a>Come usare Hub eventi di Azure da un'applicazione Python
Hub eventi di Azure è una piattaforma di Big Data streaming e un servizio di inserimento di eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per altre informazioni, vedere [Introduzione a Hub eventi](event-hubs-what-is-event-hubs.md). 

Questo articolo fornisce collegamenti ad articoli che illustrano come eseguire le operazioni seguenti da un'applicazione scritta in **Python**:

- [Inviare eventi da Hub eventi](#send-events-to-event-hubs)
- [Ricevere eventi da Hub eventi](#receive-events-from-event-hubs)
- Leggere i dati degli eventi acquisiti da una risorsa di archiviazione di Azure. 

## <a name="prerequisites"></a>Prerequisiti
- Creare un hub eventi seguendo uno dei seguenti avvii rapidi: [portale di Azure](event-hubs-create.md), [interfaccia della riga di comando di Azure](event-hubs-quickstart-cli.md), [Azure PowerShell](event-hubs-quickstart-powershell.md), [modello di Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md). 
- Python 3.4 o versione successiva installato nel computer.

## <a name="install-python-package"></a>Installa pacchetto Python

Per installare i pacchetti di Python per Hub eventi aprire un prompt dei comandi con un percorso contenente Python, quindi eseguire questo comando: 

```bash
pip install azure-eventhub
```

## <a name="send-events-to-event-hubs"></a>Inviare eventi a Hub eventi
Il codice seguente spiega come inviare eventi a un hub eventi da un'applicazione Python: 

1. Creare variabili per conservare l'URL dell'hub eventi, il nome della chiave e il relativo valore. 

    ```python
    # Import classes from Event Hubs python package
    from azure.eventhub import EventHubClient, Receiver, Offset
    
    # Address can be in either of these formats:
    # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
    # "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
    # For example:
    ADDRESS = "amqps://<MyEventHubNamspaceName>.servicebus.windows.net/<MyEventHubName>"
    
    # SAS policy and key are not required if they are encoded in the URL
    USER = "<Name of the access key. Default name: RootManageSharedAccessKey>"
    KEY = "<The access key>"
    ```

2. Creare un client di Hub eventi, aggiungere un mittente, eseguire il client, inviare l'evento con il mittente e arrestare il client al termine. 

    ```python
    # Create an Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    
    # Add a sender to the client
    sender = client.add_sender(partition="0")
    
    # Run the Event Hub client
    client.run()
    
    # Send event to the event hub
    sender.send(EventData("<MyEventData>"))
    
    # Stop the Event Hubs client
    client.stop()
    
    ```

In [questo articolo](event-hubs-python-get-started-send.md) è disponibile un’esercitazione completa su come inviare eventi a un hub eventi di un’applicazione scritta con Python.

## <a name="receive-events-from-event-hubs"></a>Ricevere eventi da Hub eventi
Il codice seguente spiega come ricevere eventi da un hub eventi di un’applicazione Python: 

```python

# Create an Event Hubs client
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)

# Add a receiver to the client
receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)

# Run the Event Hubs client
client.run()

# Receive event data from the event hub
for event_data in receiver.receive(timeout=100):
    last_offset = event_data.offset
    last_sn = event_data.sequence_number
    print("Received: {}, {}".format(last_offset, last_sn))

# Stop the Event Hubs client
client.stop()
```

In [questo articolo](event-hubs-python-get-started-receive.md) è disponibile un’esercitazione completa su come ricevere eventi da un hub eventi di un’applicazione scritta con Python.

## <a name="read-capture-event-data-from-azure-storage"></a>Leggere i dati degli eventi acquisiti da una risorsa di archiviazione Azure
Il codice seguente spiega come leggere i dati degli eventi acquisiti archiviati nelle **risorse di archiviazione BLOB di Azure** di un'applicazione Python: Abilitare **Acquisisci** per l’hub eventi seguendo le istruzioni riportate in: [Abilitare Acquisizione di Hub eventi usando il portale di Azure](event-hubs-capture-enable-through-portal.md). Quindi, inviare alcuni eventi all'hub eventi prima di verificare il codice. 

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
        #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
        if blob.properties.content_length > 508:
            print('Downloaded a non empty blob: ' + blob.name)
            cleanName = string.replace(blob.name, '/', '_')
            block_blob_service.get_blob_to_path(container, blob.name, cleanName)
            processBlob(cleanName)
            os.remove(cleanName)
        block_blob_service.delete_blob(container, blob.name)
startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')

```

In [questo articolo](event-hubs-capture-python.md) è disponibile un’esercitazione completa su come leggere i dati di Hub eventi acquisiti in una risorsa di archiviazione BLOB di Azure di un’applicazione scritta con Python.

## <a name="github-samples"></a>Esempi in GitHub
È possibile trovare altri esempi di Python nel repository Git [azure-event-hubs-python](https://github.com/Azure/azure-event-hubs-python/).

## <a name="next-steps"></a>Passaggi successivi
Leggere gli articoli nella sezione Concetti a partire da [Panoramica sulle funzionalità di Hub eventi](event-hubs-features.md).
