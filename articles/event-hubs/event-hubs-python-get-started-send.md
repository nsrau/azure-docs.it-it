---
title: Inviare eventi ad Hub eventi di Azure tramite Python | Microsoft Docs
description: Guida introduttiva all'invio di eventi ad Hub eventi tramite Python
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/26/2018
ms.author: sethm
ms.openlocfilehash: 762e21cfc7d16b614eb637c569f8bfc5b6115db1
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703407"
---
# <a name="send-events-to-event-hubs-using-python"></a>Inviare eventi ad Hub eventi tramite Python

Hub eventi di Azure è un sistema di gestione degli eventi a scalabilità elevata, in grado di gestire milioni di eventi al secondo, che consente alle applicazioni di elaborare e analizzare le elevate quantità di dati generati dai dispositivi connessi e da altri sistemi. Dopo aver raccolto gli eventi in un hub eventi, è possibile riceverli e gestirli usando dei gestori in-process o inoltrandoli ad altri sistemi di analisi.

Per altre informazioni su Hub eventi, vedere [Panoramica di Hub eventi][Event Hubs overview].

Questa esercitazione descrive come inviare eventi a un hub eventi da un'applicazione scritta in Python. Per ricevere gli eventi, vedere [l'articolo corrispondente Ricezione](event-hubs-python-get-started-receive.md).

Il codice riportato nell'esercitazione proviene da [questi esempi di GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples), che è possibile esaminare per visualizzare la versione completa dell'applicazione in funzione, incluse le istruzioni di importazione e le dichiarazioni di variabili. Altri esempi sono disponibili nella stessa cartella GitHub.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

- Python 3.4 o versione successiva.
- Un hub eventi e uno spazio dei nomi di Hub eventi esistenti. È possibile creare queste entità seguendo le istruzioni di [questo articolo](event-hubs-create.md). 

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]


## <a name="install-python-package"></a>Installa pacchetto Python

Per installare i pacchetti di Python per Hub eventi aprire un prompt dei comandi con un percorso contenente Python, quindi eseguire questo comando: 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-send-events"></a>Creazione di uno script Python per inviare gli eventi

Successivamente, creare un'applicazione Python che invia eventi a un hub eventi:

1. Aprire l'editor preferito di Python, ad esempio[Visual Studio Code][Visual Studio Code].
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

## <a name="send-events"></a>Inviare eventi

Per eseguire lo script aprire un prompt dei comandi con un percorso contenente Python, quindi eseguire questo comando:

```bash
start python send.py
```
 
## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati inviati gli eventi a un hub eventi tramite Python, per ricevere gli eventi, vedere [l'articolo corrispondente Ricezione](event-hubs-python-get-started-receive.md).

Per altre informazioni su Hub eventi, visitare le pagine seguenti:

* [Panoramica di Hub eventi][Event Hubs overview]
* [Creare un hub eventi](event-hubs-create.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
