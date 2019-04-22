---
title: Inviare o ricevere eventi tramite Python - hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce una procedura dettagliata per la creazione di un'applicazione Python che invia eventi all'hub eventi di Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 6a8f0ddcfe6de904219059c6e761ead4c004732d
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681710"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Inviare eventi a o ricevere eventi da hub eventi con Python

Hub eventi di Azure è una piattaforma di Big Data streaming e un servizio di inserimento di eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

Questa esercitazione descrive come creare applicazioni Python per inviare eventi a o ricevere eventi da un hub eventi. 

> [!NOTE]
> È possibile scaricare questa guida introduttiva come esempio da [GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples), sostituire le stringhe `EventHubConnectionString` e `EventHubName` con i valori dell'hub eventi in uso ed eseguirla. In alternativa, è possibile seguire la procedura illustrata in questa esercitazione per creare una soluzione propria.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

- Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Python 3.4 o versione successiva.
- Usare la [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo hub eventi e ottenere le credenziali di gestione dell'applicazione necessarie per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md). Quindi, ottenere il valore della chiave di accesso per l'hub eventi seguendo le istruzioni disponibili nell'articolo: [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Si userà la chiave di accesso nel codice scritto più avanti in questa esercitazione. Il nome della chiave predefinita è: **RootManageSharedAccessKey**.

## <a name="install-python-package"></a>Installa pacchetto Python

Per installare i pacchetti di Python per Hub eventi aprire un prompt dei comandi con un percorso contenente Python, quindi eseguire questo comando: 

```bash
pip install azure-eventhub
```

## <a name="send-events"></a>Inviare eventi

### <a name="create-a-python-script-to-send-events"></a>Creazione di uno script Python per inviare gli eventi

Successivamente, creare un'applicazione Python che invia eventi a un hub eventi:

1. Aprire l'editor preferito di Python, ad esempio [Visual Studio Code](https://code.visualstudio.com/)
2. Creare uno script denominato **send.py**. Questo script invia 100 eventi all'hub eventi.
3. Incollare il codice seguente in send.py, sostituendo i valori INDIRIZZO, UTENTE e CHIAVE con i valori ottenuti dal portale di Azure nella sezione precedente: 

```python
import sys
import logging
import datetime
import time
import os

from azure.eventhub import EventHubClient, Sender, EventData

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"

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
            sender.send(EventData(str(i)))
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

### <a name="run-application-to-send-events"></a>Eseguire l'applicazione per inviare gli eventi

Per eseguire lo script aprire un prompt dei comandi con un percorso contenente Python, quindi eseguire questo comando:

```bash
start python send.py
```

Congratulazioni! Sono stati inviati messaggi a un hub eventi.

## <a name="receive-events"></a>Ricevere eventi

### <a name="create-a-python-script-to-receive-events"></a>Creazione di uno script Python per ricevere gli eventi

Successivamente, creare un'applicazione Python che riceve eventi da un hub eventi:

1. Aprire l'editor preferito di Python, ad esempio [Visual Studio Code](https://code.visualstudio.com/)
2. Creare uno script denominato **recv.py**.
3. Incollare il codice seguente in recv.py, sostituendo i valori INDIRIZZO, UTENTE e CHIAVE con i valori ottenuti dal portale di Azure nella sezione precedente: 

```python
import os
import sys
import logging
import time
from azure.eventhub import EventHubClient, Receiver, Offset

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"
CONSUMER_GROUP = "$default"
OFFSET = Offset("-1")
PARTITION = "0"

total = 0
last_sn = -1
last_offset = "-1"
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
try:
    receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
    client.run()
    start_time = time.time()
    for event_data in receiver.receive(timeout=100):
        last_offset = event_data.offset
        last_sn = event_data.sequence_number
        print("Received: {}, {}".format(last_offset, last_sn))
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

### <a name="receive-events"></a>Ricevere eventi

Per eseguire lo script aprire un prompt dei comandi con un percorso contenente Python, quindi eseguire questo comando:

```bash
start python recv.py
```
 
## <a name="next-steps"></a>Passaggi successivi
Leggere gli articoli seguenti:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Le funzionalità e la terminologia nell'hub eventi di Azure](event-hubs-features.md)
- [Domande frequenti su Hub eventi](event-hubs-faq.md)

