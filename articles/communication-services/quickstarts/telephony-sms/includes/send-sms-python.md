---
title: includere file
description: includere file
services: azure-communication-services
author: danieldoolabh
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 09/03/2020
ms.topic: include
ms.custom: include file
ms.author: dadoolab
ms.openlocfilehash: 9265caa3054cde6af311e655db2b2496524e8d24
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91757145"
---
Introduzione a Servizi di comunicazione di Azure, con l'invio di messaggi SMS tramite la libreria client SMS di Servizi di comunicazione per Python.

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- [Python](https://www.python.org/downloads/) 2.7, 3.5 o versione successiva
- Una risorsa attiva di Servizi di comunicazione e una stringa di connessione. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un numero di telefono abilitato per gli SMS. [Ottenere un numero di telefono](../get-phone-number.md).

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

- In una finestra del terminale o di comando eseguire il comando `python --version` per verificare se Python è installato.
- Per visualizzare i numeri di telefono associati alla risorsa di Servizi di comunicazione, accedere al [portale di Azure](https://portal.azure.com/), individuare la risorsa di Servizi di comunicazione e aprire la scheda dei **numeri di telefono** nel riquadro di spostamento sinistro.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Aprire la finestra del terminale o di comando, creare una nuova directory per l'app e passare a tale directory.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Usare un editor di testo per creare un file denominato **start-sms.py** nella directory radice del progetto e aggiungere la struttura per il programma, inclusa la gestione delle eccezioni di base. Tutto il codice sorgente per questa guida di avvio rapido verrà aggiunto a questo file nelle sezioni riportate di seguito.

```python
import os
from azure.communication.sms import PhoneNumber
from azure.communication.sms import SendSmsOptions
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>Installare il pacchetto

Rimanendo nella directory dell'applicazione, installare il pacchetto della libreria client SMS di Servizi di comunicazione di Azure per Python usando il comando `pip install`.

```console
pip install azure-communication-sms
```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client SMS di Servizi di comunicazione di Azure per Python.

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Questa classe è necessaria per tutte le funzionalità SMS. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per inviare messaggi SMS. |
| SendSmsOptions | Questa classe fornisce opzioni per la configurazione dei report di recapito. Se enable_delivery_report è impostato su True, verrà generato un evento quando il recapito ha esito positivo |

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza di **SmsClient** con la stringa di connessione. Il codice seguente recupera la stringa di connessione per la risorsa da una variabile di ambiente denominata `COMMUNICATION_SERVICES_CONNECTION_STRING`. Vedere come [gestire la stringa di connessione della risorsa](../../create-communication-resource.md#store-your-connection-string).

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.getenv('COMMUNICATION_SERVICES_CONNECTION_STRING')

# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(connection_string)
```

## <a name="send-an-sms-message"></a>Inviare SMS

Inviare un messaggio SMS chiamando il metodo Send. Aggiungere questo codice alla fine del blocco `try` in **send-sms.py**:

```python

# calling send() with sms values
sms_response = sms_client.send(
        from_phone_number=PhoneNumber("<leased-phone-number>"),
        to_phone_numbers=[PhoneNumber("<to-phone-number>")],
        message="Hello World via SMS",
        send_sms_options=SendSmsOptions(enable_delivery_report=True)) # optional property

```

È necessario sostituire `<leased-phone-number>` con un numero di telefono abilitato per gli SMS associato al servizio di comunicazione e `<to-phone-number>` con il numero di telefono a cui inviare un messaggio. 

Il parametro `send_sms_options` è facoltativo ed è possibile usarlo per configurare i report di recapito. È utile per gli scenari in cui si vogliono generare eventi quando vengono recapitati messaggi SMS. Per configurare i report di recapito per i messaggi SMS, vedere la guida di avvio rapido [Gestire gli eventi SMS](../handle-sms-events.md).

## <a name="run-the-code"></a>Eseguire il codice

Eseguire l'applicazione dalla directory dell'applicazione con il comando `python`.

```console
python send-sms.py
```
