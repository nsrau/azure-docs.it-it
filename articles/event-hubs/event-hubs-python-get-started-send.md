---
title: Inviare o ricevere eventi da Hub eventi di Azure tramite Python (versione precedente)
description: Questa procedura dettagliata illustra come creare ed eseguire script Python che inviano o ricevono eventi da Hub eventi di Azure tramite il pacchetto precedente azure-eventhub versione 1.
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 22f6b2aba36e560e9bd335baa92925fe9846c670
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162600"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Guida introduttiva: Inviare e ricevere eventi con Hub eventi tramite Python (azure-eventhub versione 1)
Questa guida di avvio rapido illustra come inviare e ricevere eventi da un hub eventi con il pacchetto Python **azure-eventhub versione 1**. 

> [!WARNING]
> In questa guida di avvio rapido si usa il pacchetto azure-eventhub versione 1. Per una guida di avvio rapido che usa la **versione 5** del pacchetto, vedere [Inviare e ricevere eventi con azure-eventhub versione 5](get-started-python-send-v2.md). Per fare in modo che l'applicazione usi il nuovo pacchetto al posto di quello precedente, vedere la [guida alla migrazione dalla versione 1 alla versione 5 di azure-eventhub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).
 

## <a name="prerequisites"></a>Prerequisiti
Se non si ha familiarità con Hub eventi di Azure, vedere [Panoramica di Hub eventi](event-hubs-about.md) prima di procedere con questa guida di avvio rapido. 

Per completare questa guida introduttiva è necessario soddisfare i prerequisiti seguenti:

- **Sottoscrizione di Microsoft Azure**. Per usare i servizi di Azure, tra cui Hub eventi di Azure, è necessaria una sottoscrizione.  Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/free/) oppure usare i vantaggi della sottoscrizione di MSDN per la [creazione di un account](https://azure.microsoft.com).
- Python 3.4 o versione successiva con `pip` installato e aggiornato.
- Il pacchetto Python per Hub eventi. Per installare il pacchetto, eseguire questo comando in un prompt dei comandi il cui percorso contenga Python: 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Creare uno spazio dei nomi di Hub eventi e un hub eventi**. Il primo passaggio consiste nell'usare il [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo Hub eventi e ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md). Ottenere quindi il valore della chiave di accesso dell'hub eventi seguendo le istruzioni disponibili nell'articolo: [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). La chiave di accesso nel codice scritto verrà usata più avanti in questa guida di avvio rapido. Il nome della chiave predefinita è: **RootManageSharedAccessKey**. 


## <a name="send-events"></a>Inviare eventi

Per creare un'applicazione Python che invia eventi a un hub eventi:

> [!NOTE]
> Anziché seguire l'Avvio rapido, è possibile scaricare le [app di esempio](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) da GitHub ed eseguirle. Sostituire le stringhe `EventHubConnectionString` e `EventHubName` con i valori dell'hub eventi in uso.

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

