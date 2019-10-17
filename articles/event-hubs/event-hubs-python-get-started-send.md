---
title: Inviare e ricevere eventi con Python-Hub eventi di Azure
description: Questa procedura dettagliata Mostra come creare ed eseguire script Python che inviano eventi a o ricevono eventi da Hub eventi di Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/11/2019
ms.author: shvija
ms.openlocfilehash: 330a7f5dc325c707b5be7ce9f9b3242a1d4c9547
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428892"
---
# <a name="send-and-receive-events-with-event-hubs-using-python"></a>Inviare e ricevere eventi con hub eventi usando Python

Hub eventi di Azure è una piattaforma di streaming per Big Data e un servizio di inserimento eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o telemetria da software e dispositivi distribuiti. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per altre informazioni sugli hub eventi, vedere [Hub](event-hubs-about.md) eventi di Azure e [funzionalità e terminologia in hub eventi di Azure](event-hubs-features.md).

Questa Guida introduttiva illustra come creare applicazioni Python che inviano eventi a e ricevono eventi da un hub eventi. 

> [!NOTE]
> Invece di usare la Guida introduttiva, è possibile scaricare ed eseguire le [app di esempio](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) da GitHub. Sostituire le stringhe `EventHubConnectionString` e `EventHubName` con i valori dell'hub eventi. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva è necessario soddisfare i prerequisiti seguenti:

- Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Uno spazio dei nomi di hub eventi attivo e un hub eventi, creato seguendo le istruzioni disponibili in [Guida introduttiva: creare un hub eventi usando portale di Azure](event-hubs-create.md). Prendere nota dei nomi degli spazi dei nomi e degli hub eventi da usare più avanti in questa procedura dettagliata. 
- Il nome della chiave di accesso condiviso e il valore della chiave primaria per lo spazio dei nomi di hub eventi. Ottenere il nome e il valore della chiave di accesso seguendo le istruzioni [riportate in ottenere la stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Il nome predefinito della chiave di accesso è **RootManageSharedAccessKey**. Copiare il nome della chiave e il valore della chiave primaria da usare più avanti in questa procedura dettagliata. 
- Python 3,4 o versione successiva, con `pip` installato e aggiornato.
- Il pacchetto python per hub eventi. Per installare il pacchetto, eseguire questo comando in un prompt dei comandi con Python nel percorso: 
  
  ```cmd
  pip install azure-eventhub
  ```
  
  > [!NOTE]
  > Il codice in questa Guida introduttiva usa la versione stabile corrente 1.3.1 dell'SDK di hub eventi. Per il codice di esempio che usa la versione di anteprima dell'SDK, vedere [https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples).

## <a name="send-events"></a>Inviare eventi

Per creare un'applicazione Python che invii gli eventi a un hub eventi:

1. Aprire l'editor Python preferito, ad esempio [Visual Studio Code](https://code.visualstudio.com/)
2. Creare un nuovo file denominato *Send.py*. Questo script invia 100 eventi all'hub eventi.
3. Incollare il codice seguente in *Send.py*, sostituendo gli hub eventi \<namespace >, \<eventhub >, \<AccessKeyName > e il valore della chiave 4primary @no__t con i valori: 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   try:
       if not ADDRESS:
           raise ValueError("No EventHubs URL supplied.")
   
       # Create Event Hubs client
       client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
       sender = client.add_sender(partition="0")
       client.run()
       try:
           start_time = time.time()
           for i in range(100):
               print("Sending message: {}".format(i))
               message = "Message {}".format(i)
               sender.send(EventData(message))
       except:
           raise
       finally:
           end_time = time.time()
           client.stop()
           run_time = end_time - start_time
           logger.info("Runtime: {} seconds".format(run_time))
   
   except KeyboardInterrupt:
       pass
   ```
   
4. Salvare il file. 

Per eseguire lo script, dalla directory in cui è stato salvato *Send.py*, eseguire questo comando:

```cmd
start python send.py
```

Congratulazioni. Sono stati inviati messaggi a un hub eventi.

## <a name="receive-events"></a>Ricevere eventi

Per creare un'applicazione Python che riceve eventi da un hub eventi:

1. Nell'editor di Python creare un file denominato *Recv.py*.
2. Incollare il codice seguente in *Recv.py*, sostituendo gli hub eventi \<namespace >, \<eventhub >, \<AccessKeyName > e il valore della chiave 4primary @no__t con i valori: 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
           total += 1
   
       end_time = time.time()
       client.stop()
       run_time = end_time - start_time
       print("Received {} messages in {} seconds".format(total, run_time))
   
   except KeyboardInterrupt:
       pass
   finally:
       client.stop()
   ```
   
4. Salvare il file.

Per eseguire lo script, dalla directory in cui è stato salvato *Recv.py*, eseguire questo comando:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Hub eventi, vedere gli articoli seguenti:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funzionalità e terminologia di Hub eventi di Azure](event-hubs-features.md)
- [Domande frequenti su Hub eventi](event-hubs-faq.md)

