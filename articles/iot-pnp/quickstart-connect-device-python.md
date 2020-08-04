---
title: Connettere il codice del dispositivo Python di esempio Plug and Play IoT (anteprima) all'hub IoT di Azure | Microsoft Docs
description: Usare Python per creare ed eseguire il codice del dispositivo di esempio Plug and Play IoT (anteprima) che si connette a un hub IoT. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 84ef7ff18c294097da20640c1de237b41900cb40
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352632"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-python"></a>Avvio rapido: Connettere un'applicazione dispositivo Plug and Play IoT (anteprima) di esempio all'hub IoT (Python)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Questa guida di avvio rapido illustra come creare un'applicazione di dispositivo Plug and Play IoT di esempio, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare i dati di telemetria inviati. L'applicazione di esempio è scritta per Python ed è inclusa in Azure IoT Hub Device SDK per Python. Un generatore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza doverne visualizzare il codice.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida di avvio rapido, è necessario Python 3.7 nel computer di sviluppo. È possibile scaricare la versione più recente consigliata per più piattaforme da [python.org](https://www.python.org/). È possibile controllare la versione di Python con il comando seguente:  

```cmd/sh
python --version
```

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Per interagire con il dispositivo di esempio nella seconda parte di questa guida di avvio rapido, usare lo strumento**Azure IoT Explorer**. [Scaricare e installare la versione di Azure IoT Explorer più recente](./howto-use-iot-explorer.md) per il sistema operativo in uso.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Eseguire questo comando per ottenere la _stringa di connessione dell'hub IoT_ per l'hub. Prendere nota della stringa di connessione, poiché verrà usata più avanti in questa guida di avvio rapido:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> È anche possibile usare lo strumento Azure IoT Explorer per trovare la stringa di connessione dell'hub IoT.

Eseguire questo comando per ottenere la _stringa di connessione del dispositivo_ aggiunto all'hub. Prendere nota della stringa di connessione, poiché verrà usata più avanti in questa guida di avvio rapido:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="set-up-your-environment"></a>Configurare l'ambiente

Il pacchetto viene pubblicato come PIP per l'aggiornamento dell'anteprima pubblica. La versione del pacchetto deve essere la più recente o `2.1.4`

Nell'ambiente python locale installare il file come indicato di seguito:

```cmd/sh
pip install azure-iot-device
```

Clonare il repository IoT di Python SDK ed estrarre **master**:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>Eseguire il dispositivo di esempio

La cartella *azure-iot-sdk-python\azure-iot-device\samples\pnp* contiene il codice di esempio per il dispositivo Plug and Play IoT. L'avvio rapido usa il file *pnp_thermostat.py*. Questo codice di esempio implementa un dispositivo compatibile con Plug and Play IoT e usa la libreria client dei dispositivi Python di Azure IoT.

Creare una variabile di ambiente definita **IOTHUB_DEVICE_CONNECTION_STRING** per archiviare la stringa di connessione del dispositivo annotata in precedenza.

Aprire il file **pnp_thermostat.py** in un editor di testo. Si noti che:

1. Definisce un singolo identificatore di modello di dispositivo gemello che rappresenta in modo univoco il [termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json). Un identificatore di modello di dispositivo gemello deve essere noto all'utente e dipende dallo scenario di implementazione del dispositivo. Per l'esempio corrente il modello rappresenta un termostato con dati di telemetria, proprietà e comandi associati al monitoraggio della temperatura.

1. Include funzioni per definire implementazioni del gestore di comandi. È necessario scrivere questi gestori per definire il modo in cui il dispositivo risponde alle richieste dei comandi.

1. Include una funzione per definire una risposta ai comandi. È necessario creare funzioni di risposta ai comandi per restituire una risposta all'hub IoT.

1. Definisce una funzione listener di tastiera di input che consente di uscire dall'applicazione.

1. Include una funzione **main**. La funzione **main**:

    1. Usa l'SDK del dispositivo per creare un client del dispositivo e connettersi all'hub IoT.

    1. Aggiorna le proprietà. Il modello usato, **Termostato**, definisce `targetTemperature` e `maxTempSinceLastReboot` come le due proprietà per il termostato, quindi verranno usati tali valori. Le proprietà vengono aggiornate tramite il metodo `patch_twin_reported_properties` definito in `device_client`.

    1. Avvia l'ascolto di richieste di comandi tramite la funzione **execute_command_listener**. La funzione configura un "listener" per ascoltare i comandi provenienti dal servizio. Quando si configura il listener, si specificano valori per `method_name`, `user_command_handler` e `create_user_response_handler`. 
        - La funzione `user_command_handler` definisce l'operazione che il dispositivo deve eseguire quando riceve un comando. Se ad esempio la sveglia si attiva, l'effetto della ricezione di questo comando è che ci si sveglia. Quando un comando viene richiamato, si ottiene un "effetto" analogo.
        - La funzione `create_user_response_handler` crea una risposta da inviare all'hub IoT quando un comando viene eseguito correttamente. Se ad esempio la sveglia si attiva e si preme il tasto per posporla, questa azione corrisponde al feedback al servizio. Questa azione è analoga alla risposta fornita al servizio. È possibile visualizzare questa risposta nel portale.

    1. Avviare l'invio dei dati di telemetria. Il valore **pnp_send_telemetry** viene definito nel file pnp_methods.py. Il codice di esempio usa un ciclo per chiamare questa funzione ogni otto secondi.

    1. Disabilita tutti i listener e le attività ed esce dal ciclo quando si preme **Q** o **q**.

Dopo avere esaminato il codice, è possibile usare il comando seguente per eseguire l'esempio:

```cmd/sh
python pnp_thermostat.py
```

Viene visualizzato l'output seguente, che indica che il dispositivo invia dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà:

```cmd/sh
Connecting using Connection String HostName=<your hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<your device shared access key>
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a un hub IoT. Per altre informazioni su come creare una soluzione che interagisce con i dispositivi Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione](quickstart-service-python.md)
