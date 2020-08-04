---
title: Interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione IoT di Azure (Python) | Microsoft Docs
description: Usare Python per connettersi e interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione Azure IoT.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e3f00bb601cce17721c5247941588be1c2de788d
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352731"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-python"></a>Avvio rapido: Interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

Plug and Play IoT (anteprima) semplifica l'Internet delle cose consentendo di interagire con il modello di un dispositivo senza conoscere l'implementazione del dispositivo sottostante. Questo argomento di avvio rapido mostra come usare Python per connettersi a un dispositivo Plug and Play IoT connesso alla soluzione e controllarlo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questo argomento di avvio rapido, è necessario Python 3.7 nel computer di sviluppo. È possibile scaricare la versione più recente consigliata per più piattaforme da [python.org](https://www.python.org/). È possibile controllare la versione di Python con il comando seguente:  

```cmd/sh
python --version
```

Installare il [pacchetto dell'SDK del servizio Python di anteprima](https://pypi.org/project/azure-iot-hub/2.2.1rc0/) eseguendo questo comando:

```cmd/sh
pip3 install azure-iot-hub==2.2.1rc0
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Eseguire il comando seguente per ottenere la _stringa di connessione dell'hub IoT_ per l'hub. Prendere nota della stringa di connessione, poiché verrà usata più avanti in questo argomento di avvio rapido:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Eseguire questo comando per ottenere la _stringa di connessione del dispositivo_ aggiunto all'hub. Prendere nota della stringa di connessione, poiché verrà usata più avanti in questo argomento di avvio rapido:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

## <a name="run-the-sample-device"></a>Eseguire il dispositivo di esempio

In questo argomento di avvio rapido viene usato come dispositivo Plug and Play IoT un dispositivo termostato di esempio scritto in Python. Per eseguire il dispositivo di esempio:

1. Aprire una finestra terminale in una cartella di propria scelta. Eseguire questo comando per clonare il repository GitHub [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) in questo percorso:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Questa finestra terminale viene ora usata come terminale del **dispositivo**. Passare alla cartella del repository clonato e quindi alla cartella */azure-iot-sdk-python/azure-iot-device/samples/pnp*.

1. Configurare la _stringa di connessione del dispositivo_:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Eseguire il dispositivo termostato di esempio con il comando seguente:

    ```cmd/sh
    python pnp_thermostat.py
    ```

1. Verranno visualizzati messaggi che indicano che il dispositivo ha inviato informazioni e segnalato il proprio stato online. Questi messaggi indicano che il dispositivo ha iniziato a inviare dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà. Non chiudere questo terminale perché sarà necessario per verificare il funzionamento del servizio di esempio.

## <a name="run-the-sample-solution"></a>Eseguire la soluzione di esempio

In questo argomento di avvio rapido si userà una soluzione IoT di esempio in Python per interagire con il dispositivo di esempio appena configurato.

1. Aprire un'altra finestra terminale da usare come terminale del **servizio**. L'SDK del servizio è disponibile in anteprima, quindi è necessario clonare gli esempi da un [ramo di anteprima di Python SDK](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python -b pnp-preview-refresh
    ```

1. Passare alla cartella del ramo del repository clonato e quindi alla cartella */azure-iot-sdk-python/azure-iot-hub/samples*.

1. Configurare le variabili di ambiente per l'ID dispositivo e la _stringa di connessione dell'hub IoT_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

1. Nella cartella samples sono disponibili quattro file di esempio con un prefisso `pnp`. Questi esempi illustrano come usare ogni API per interagire con i dispositivi Plug and Play IoT:

### <a name="get-digital-twin"></a>Ottenere il gemello digitale

Per eseguire l'esempio, usare il comando seguente nel terminale del **servizio**:

```cmd/sh
python pnp_get_digital_twin_sample.py
```

L'output mostra il gemello digitale del dispositivo e stampa l'ID modello:

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

Il frammento di codice seguente mostra il codice di esempio di *pnp_get_digital_twin_sample.py*:

```python
    # Get digital twin and retrieve the modelId from it
    digital_twin = iothub_digital_twin_manager.get_digital_twin(device_id)
    if digital_twin:
        print(digital_twin)
        print("Model Id: " + digital_twin["$metadata"]["$model"])
    else:
        print("No digital_twin found")
```

### <a name="update-a-digital-twin"></a>Aggiornare un gemello digitale

Questo esempio illustra come usare una *patch* per aggiornare le proprietà tramite il gemello digitale del dispositivo. Il frammento di codice seguente di *pnp_update_digital_twin_sample.py* mostra come costruire la patch:

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Per eseguire l'esempio, usare il comando seguente nel terminale del **servizio**:

```cmd/sh
python pnp_update_digital_twin_sample.py
```

È possibile verificare l'applicazione dell'aggiornamento nel terminale del **dispositivo** che mostra l'output seguente:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
previous values
42
```

Il terminale del **servizio** conferma che la patch è stata completata correttamente:

```cmd/sh
Patch has been successfully applied
```

### <a name="invoke-a-command"></a>Richiamare un comando

Per richiamare un comando, eseguire l'esempio *pnp_invoke_command_sample.py*. Questo esempio mostra come richiamare un comando in un semplice dispositivo termostato. Prima di eseguire questo esempio, impostare le variabili di ambiente `IOTHUB_COMMAND_NAME` e `IOTHUB_COMMAND_PAYLOAD` nel terminale del **servizio**:

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

Nel terminale del **servizio** usare questo comando per eseguire l'esempio:
  
```cmd/sh
python pnp_invoke_command_sample.py
```

Il terminale del **servizio** mostra un messaggio di conferma dal dispositivo:

```cmd/sh
{"tempReport": {"avgTemp": 34.5, "endTime": "13/07/2020 16:03:38", "maxTemp": 49, "minTemp": 11, "startTime": "13/07/2020 16:02:18"}}
```

Nel terminale del **dispositivo** è possibile osservare che il dispositivo riceve il comando:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
Sent message
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a una soluzione IoT. Per altre informazioni sui modelli di dispositivo Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Guida per gli sviluppatori alla modellazione di Plug and Play IoT (anteprima)](concepts-developer-guide.md)
