---
title: 'Guida introduttiva: Inviare e ricevere eventi tramite Python - Hub eventi di Azure'
description: 'Guida introduttiva: Questa procedura dettagliata illustra come creare ed eseguire script Python che inviano o ricevono eventi da Hub eventi di Azure.'
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 9b6c3fb03f696f4142721284a14001eb51153a77
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720558"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python"></a>Guida introduttiva: Inviare e ricevere eventi con Hub eventi tramite Python

Hub eventi di Azure è una piattaforma di streaming per Big Data e un servizio di inserimento eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi è in grado di elaborare e archiviare eventi, dati o dati di telemetria provenienti da software e dispositivi distribuiti. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per altre informazioni su Hub eventi, vedere [Hub eventi di Azure](event-hubs-about.md) e [Funzionalità e terminologia di Hub eventi di Azure](event-hubs-features.md).

Questo Avvio rapido illustra come creare applicazioni Python che inviano e ricevono eventi da un hub eventi. 

> [!NOTE]
> Anziché seguire l'Avvio rapido, è possibile scaricare le [app di esempio](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) da GitHub ed eseguirle. Sostituire le stringhe `EventHubConnectionString` e `EventHubName` con i valori dell'hub eventi in uso. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva è necessario soddisfare i prerequisiti seguenti:

- Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Uno spazio dei nomi e un hub eventi di Hub eventi attivo, creato seguendo le istruzioni disponibili in [Avvio rapido: Creare un hub eventi con il portale di Azure](event-hubs-create.md). Prendere nota dei nomi dello spazio dei nomi e dell'hub eventi per usarli più avanti in questa procedura dettagliata. 
- Il nome della chiave di accesso condiviso e il valore della chiave primaria per lo spazio dei nomi di Hub eventi. Ottenere il nome e il valore della chiave di accesso seguendo le istruzioni in [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Il nome predefinito della chiave di accesso è **RootManageSharedAccessKey**. Copiare il nome della chiave e il valore della chiave primaria per usarli più avanti in questa procedura dettagliata. 
- Python 3.4 o versione successiva con `pip` installato e aggiornato.
- Il pacchetto Python per Hub eventi. Per installare il pacchetto, eseguire questo comando in un prompt dei comandi il cui percorso contenga Python: 
  
  ```cmd
  pip install azure-eventhub
  ```
  
  > [!NOTE]
  > Il codice in questo Avvio rapido usa la versione stabile corrente 1.3.1 di Event Hubs SDK. Per codice di esempio che usa la versione di anteprima dell'SDK, vedere [https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples).

## <a name="send-events"></a>Inviare eventi

Per creare un'applicazione Python che invia eventi a un hub eventi:

1. Aprire l'editor di Python preferito, ad esempio[Visual Studio Code](https://code.visualstudio.com/)
2. Creare un nuovo file denominato *send.py*. Questo script invia 100 eventi all'hub eventi.
3. Incollare il codice seguente in *send.py*, sostituendo \<namespace>,\<eventhub>, \<AccessKeyName> e \<primary key value> di Hub eventi con i propri valori: 
   
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

Per eseguire lo script, dalla directory in cui è stato salvato il file *sender.py* eseguire questo comando:

```cmd
start python send.py
```

Congratulazioni! Sono stati inviati messaggi a un hub eventi.

## <a name="receive-events"></a>Ricevere eventi

Per creare un'applicazione Python che riceve eventi da un hub eventi:

1. Nell'editor di Python creare un file denominato *recv.py*.
2. Incollare il codice seguente in *recv.py*, sostituendo \<namespace>,\<eventhub>, \<AccessKeyName> e \<primary key value> di Hub eventi con i propri valori: 
   
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

Per eseguire lo script, dalla directory in cui è stato salvato *recv.py*eseguire questo comando:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Hub eventi, vedere gli articoli seguenti:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funzionalità e terminologia di Hub eventi di Azure](event-hubs-features.md)
- [Domande frequenti su Hub eventi](event-hubs-faq.md)

