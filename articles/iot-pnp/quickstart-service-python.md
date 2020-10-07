---
title: Interagire con un dispositivo Plug and Play IoT connesso alla soluzione Azure IoT (Python) | Microsoft Docs
description: Usare Python per connettersi e interagire con un dispositivo Plug and Play IoT connesso alla soluzione Azure IoT.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: be5ff3e863752dfc187bd91257425af5e8de85c4
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574968"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-python"></a>Avvio rapido: Interagire con un dispositivo Plug and Play IoT connesso alla soluzione (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

Plug and Play IoT semplifica l'Internet delle cose consentendo di interagire con il modello di un dispositivo senza conoscere l'implementazione del dispositivo sottostante. Questo argomento di avvio rapido mostra come usare Python per connettersi a un dispositivo Plug and Play IoT connesso alla soluzione e controllarlo.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Per completare questo argomento di avvio rapido, è necessario Python 3.7 nel computer di sviluppo. È possibile scaricare la versione più recente consigliata per più piattaforme da [python.org](https://www.python.org/). È possibile controllare la versione di Python con il comando seguente:  

```cmd/sh
python --version
```

Il pacchetto **azure-iot-device** viene pubblicato come PIP.

Nell'ambiente python locale installare il pacchetto come indicato di seguito:

```cmd/sh
pip install azure-iot-device
```

Installare il pacchetto **azure-iot-hub** eseguendo questo comando:

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>Eseguire il dispositivo di esempio

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Per altre informazioni sulla configurazione di esempio, vedere il file [Leggimi dell'esempio](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

In questo argomento di avvio rapido viene usato come dispositivo Plug and Play IoT un dispositivo termostato di esempio scritto in Python. Per eseguire il dispositivo di esempio:

1. Aprire una finestra terminale in una cartella di propria scelta. Eseguire questo comando per clonare il repository GitHub [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) in questo percorso:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Questa finestra terminale viene ora usata come terminale del **dispositivo**. Passare alla cartella del repository clonato e quindi alla cartella */azure-iot-sdk-python/azure-iot-device/samples/pnp*.

1. Eseguire il dispositivo termostato di esempio con il comando seguente:

    ```cmd/sh
    python simple_thermostat.py
    ```

1. Verranno visualizzati messaggi che indicano che il dispositivo ha inviato informazioni e segnalato il proprio stato online. Questi messaggi indicano che il dispositivo ha iniziato a inviare dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà. Non chiudere questo terminale perché sarà necessario per verificare il funzionamento del servizio di esempio.

## <a name="run-the-sample-solution"></a>Eseguire la soluzione di esempio

In questo argomento di avvio rapido si userà una soluzione IoT di esempio in Python per interagire con il dispositivo di esempio appena configurato.

1. Aprire un'altra finestra terminale da usare come terminale del **servizio**. 

1. Passare alla cartella */azure-iot-sdk-python/azure-iot-hub/samples* del repository dell'SDK per Python clonato.

1. Nella cartella samples sono disponibili quattro file di esempio che illustrano le operazioni con la classe Digital Twin Manager: *get_digital_twin_sample.py, update_digitial_twin_sample.py, invoke_command_sample.py e invoke_component_command_sample-.py*.  Questi esempi illustrano come usare ogni API per interagire con i dispositivi Plug and Play IoT:

### <a name="get-digital-twin"></a>Ottenere il gemello digitale

In [Configurare l'ambiente per le esercitazioni e le guide di avvio rapido di Plug and Play IoT](set-up-environment.md) sono state create due variabili di ambiente per configurare l'esempio da connettere all'hub IoT e al dispositivo:

* **IOTHUB_CONNECTION_STRING**: stringa di connessione dell'hub IoT annotata in precedenza.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

Per eseguire l'esempio, usare il comando seguente nel terminale del **servizio**:

```cmd/sh
python get_digital_twin_sample.py
```

L'output mostra il gemello digitale del dispositivo e stampa l'ID modello:

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

Il frammento di codice seguente mostra il codice di esempio di *get_digital_twin_sample.py*:

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

Questo esempio illustra come usare una *patch* per aggiornare le proprietà tramite il gemello digitale del dispositivo. Il frammento di codice seguente di *update_digital_twin_sample.py* mostra come costruire la patch:

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Per eseguire l'esempio, usare il comando seguente nel terminale del **servizio**:

```cmd/sh
python update_digital_twin_sample.py
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

Per richiamare un comando, eseguire l'esempio *invoke_command_sample.py*. Questo esempio mostra come richiamare un comando in un semplice dispositivo termostato. Prima di eseguire questo esempio, impostare le variabili di ambiente `IOTHUB_COMMAND_NAME` e `IOTHUB_COMMAND_PAYLOAD` nel terminale del **servizio**:

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

Nel terminale del **servizio** usare questo comando per eseguire l'esempio:
  
```cmd/sh
python invoke_command_sample.py
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

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a una soluzione IoT. Per altre informazioni sui modelli di dispositivo Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Guida per sviluppatori alla modellazione di Plug and Play IoT](concepts-developer-guide-device-csharp.md)
