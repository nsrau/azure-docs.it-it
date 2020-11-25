---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 6b861baea93c2c57b8f66ebac928a7e4fd3adfa8
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95487808"
---
Plug and Play IoT semplifica l'Internet delle cose consentendo di interagire con il modello di un dispositivo senza conoscere l'implementazione del dispositivo sottostante. Questo argomento di avvio rapido mostra come usare Python per connettersi a un dispositivo Plug and Play IoT connesso alla soluzione e controllarlo.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

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

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

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

1. Aprire il file *registry_manager_pnp_sample.py* ed esaminare il codice. Questo esempio illustra come usare la classe **IoTHubRegistryManager** per interagire con il dispositivo Plug and Play IoT.

> [!NOTE]
> Questi esempi di servizio usano la classe **IoTHubRegistryManager** del **client del servizio hub IoT**. Per altre informazioni sulle API, tra cui l'API gemelli digitali, vedere la [guida per sviluppatori di servizi](../articles/iot-pnp/concepts-developer-guide-service.md).

### <a name="get-the-device-twin"></a>Ottenere il dispositivo gemello

In [Configurare l'ambiente per le esercitazioni e le guide di avvio rapido di Plug and Play IoT](../articles/iot-pnp/set-up-environment.md) sono state create due variabili di ambiente per configurare l'esempio da connettere all'hub IoT e al dispositivo:

* **IOTHUB_CONNECTION_STRING**: stringa di connessione dell'hub IoT annotata in precedenza.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

Per eseguire l'esempio, usare il comando seguente nel terminale del **servizio**:

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

Il frammento di codice seguente mostra il codice di esempio di *registry_manager_pnp_sample.py*:

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
