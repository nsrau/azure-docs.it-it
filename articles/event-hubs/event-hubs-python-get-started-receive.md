---
title: Ricevere eventi tramite Python - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce una procedura dettagliata per la creazione di un'applicazione Python in grado di ricevere eventi da Hub eventi di Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: bc1cf07c5a74bc4d7182eea5281e75525fd04247
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103185"
---
# <a name="receive-events-from-event-hubs-using-python"></a>Ricevere eventi da Hub eventi tramite Python

Hub eventi di Azure è un sistema di gestione degli eventi a scalabilità elevata, in grado di gestire milioni di eventi al secondo, che consente alle applicazioni di elaborare e analizzare le elevate quantità di dati generati dai dispositivi connessi e da altri sistemi. Dopo aver raccolto gli eventi in un hub eventi, è possibile riceverli e gestirli usando dei gestori in-process o inoltrandoli ad altri sistemi di analisi.

Per altre informazioni su Hub eventi, vedere [Panoramica di Hub eventi][Event Hubs overview].

Questa esercitazione descrive come ricevere eventi da un hub eventi da un'applicazione scritta in Python. Per inviare eventi vedere [l'articolo corrispondente relativo all'invio](event-hubs-python-get-started-send.md).

Il codice riportato nell'esercitazione proviene da [questi esempi di GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples), che è possibile esaminare per visualizzare la versione completa dell'applicazione in funzione, incluse le istruzioni di importazione e le dichiarazioni di variabili. Altri esempi sono disponibili nella stessa cartella GitHub.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

- Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Python 3.4 o versione successiva.
- Un hub eventi e uno spazio dei nomi di Hub eventi esistenti. È possibile creare queste entità seguendo le istruzioni di [questo articolo](event-hubs-create.md). 

## <a name="install-python-package"></a>Installa pacchetto Python

Per installare i pacchetti di Python per Hub eventi aprire un prompt dei comandi con un percorso contenente Python, quindi eseguire questo comando: 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-receive-events"></a>Creazione di uno script Python per ricevere gli eventi

Successivamente, creare un'applicazione Python che riceve eventi da un hub eventi:

1. Aprire l'editor preferito di Python, ad esempio[Visual Studio Code][Visual Studio Code].
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

## <a name="receive-events"></a>Ricevere eventi

Per eseguire lo script aprire un prompt dei comandi con un percorso contenente Python, quindi eseguire questo comando:

```bash
start python recv.py
```
 
## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva è stata creata un'applicazione Python che ha ricevuto messaggi da un hub eventi. Per informazioni su come inviare eventi a un hub eventi tramite Python, vedere [Inviare eventi da hub eventi di Azure usando Python](event-hubs-python-get-started-send.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
