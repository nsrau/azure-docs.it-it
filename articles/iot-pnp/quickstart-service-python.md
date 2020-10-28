---
title: Interagire con un dispositivo Plug and Play IoT connesso alla soluzione Azure IoT (Python) | Microsoft Docs
description: Usare Python per connettersi e interagire con un dispositivo Plug and Play IoT connesso alla soluzione Azure IoT.
author: elhorton
ms.author: elhorton
ms.date: 10/05/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ad5fa271e3abfaf0c7ee4884881262773a9ad485
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92741484"
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

1. Questa finestra terminale viene ora usata come terminale del **dispositivo** . Passare alla cartella del repository clonato e quindi alla cartella */azure-iot-sdk-python/azure-iot-device/samples/pnp* .

1. Eseguire il dispositivo termostato di esempio con il comando seguente:

    ```cmd/sh
    python simple_thermostat.py
    ```

1. Verranno visualizzati messaggi che indicano che il dispositivo ha inviato informazioni e segnalato il proprio stato online. Questi messaggi indicano che il dispositivo ha iniziato a inviare dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà. Non chiudere questo terminale perché sarà necessario per verificare il funzionamento del servizio di esempio.

## <a name="run-the-sample-solution"></a>Eseguire la soluzione di esempio

In questo argomento di avvio rapido si userà una soluzione IoT di esempio in Python per interagire con il dispositivo di esempio appena configurato.

1. Aprire un'altra finestra terminale da usare come terminale del **servizio** .

1. Passare alla cartella */azure-iot-sdk-python/azure-iot-hub/samples* del repository dell'SDK per Python clonato.

1. Aprire il file *registry_manager_pnp_sample.py* ed esaminare il codice. Questo esempio illustra come usare la classe **IoTHubRegistryManager** per interagire con il dispositivo Plug and Play IoT.

> [!NOTE]
> Questi esempi di servizio usano la classe **IoTHubRegistryManager** del **client del servizio hub IoT** . Per altre informazioni sulle API, tra cui l'API gemelli digitali, vedere la [guida per sviluppatori di servizi](concepts-developer-guide-service.md).

### <a name="get-the-device-twin"></a>Ottenere il dispositivo gemello

In [Configurare l'ambiente per le esercitazioni e le guide di avvio rapido di Plug and Play IoT](set-up-environment.md) sono state create due variabili di ambiente per configurare l'esempio da connettere all'hub IoT e al dispositivo:

* **IOTHUB_CONNECTION_STRING** : stringa di connessione dell'hub IoT annotata in precedenza.
* **IOTHUB_DEVICE_ID** : `"my-pnp-device"`.

Per eseguire l'esempio, usare il comando seguente nel terminale del **servizio** :

```cmd/sh
set IOTHUB_METHOD_NAME="getMaxMinReport"
set IOTHUB_METHOD_PAYLOAD="hello world"
python registry_manager_pnp_sample.py
```

> [!NOTE]
> Se si esegue questo esempio in Linux, usare `export` al posto di `set`.

L'output mostra il dispositivo gemello e stampa l'ID modello:

```cmd/sh
The Model ID for this device is:
dtmi:com:example:Thermostat;1
```

Il frammento di codice seguente mostra il codice di esempio di *registry_manager_pnp_sample.py* :

```python
    # Create IoTHubRegistryManager
    iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

    # Get device twin
    twin = iothub_registry_manager.get_twin(device_id)
    print("The device twin is: ")
    print("")
    print(twin)
    print("")

    # Print the device's model ID
    additional_props = twin.additional_properties
    if "modelId" in additional_props:
        print("The Model ID for this device is:")
        print(additional_props["modelId"])
        print("")
```

### <a name="update-a-device-twin"></a>Aggiornare un dispositivo gemello

Questo esempio illustra come aggiornare la proprietà `targetTemperature` scrivibile nel dispositivo:

```python
    # Update twin
    twin_patch = Twin()
    twin_patch.properties = TwinProperties(
        desired={"targetTemperature": 42}
    )  # this is relevant for the thermostat device sample
    updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
    print("The twin patch has been successfully applied")
    print("")
```

È possibile verificare l'applicazione dell'aggiornamento nel terminale del **dispositivo** che mostra l'output seguente:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
```

Il terminale del **servizio** conferma che la patch è stata completata correttamente:

```cmd/sh
The twin patch has been successfully applied
```

### <a name="invoke-a-command"></a>Richiamare un comando

L'esempio richiama quindi un comando:

Il terminale del **servizio** mostra un messaggio di conferma dal dispositivo:

```cmd/sh
The device method has been successfully invoked
```

Nel terminale del **dispositivo** è possibile osservare che il dispositivo riceve il comando:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a una soluzione IoT. Per altre informazioni sui modelli di dispositivo Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Guida per sviluppatori alla modellazione di Plug and Play IoT](concepts-developer-guide-device-csharp.md)
