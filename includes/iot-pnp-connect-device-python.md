---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 607c83bd97f8e371896a5a8ac0c9aa05ab34fa2c
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511658"
---
Questa guida di avvio rapido illustra come creare un'applicazione di dispositivo Plug and Play IoT di esempio, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare i dati di telemetria inviati. L'applicazione di esempio è scritta per Python ed è inclusa in Azure IoT Hub Device SDK per Python. Un generatore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza doverne visualizzare il codice.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Per completare questo argomento di avvio rapido, è necessario Python 3.7 nel computer di sviluppo. È possibile scaricare la versione più recente consigliata per più piattaforme da [python.org](https://www.python.org/). È possibile controllare la versione di Python con il comando seguente:  

```cmd/sh
python --version
```

Nell'ambiente python locale installare il pacchetto come indicato di seguito:

```cmd/sh
pip install azure-iot-device
```

Clonare il repository IoT di Python SDK ed estrarre **master**:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>Eseguire il dispositivo di esempio

La cartella *azure-iot-sdk-python\azure-iot-device\samples\pnp* contiene il codice di esempio per il dispositivo Plug and Play IoT. Questa guida di avvio rapido usa il file *simple_thermostat.py*. Questo codice di esempio implementa un dispositivo compatibile con Plug and Play IoT e usa la libreria client dei dispositivi Python di Azure IoT.

Aprire il file **simple_thermostat.py** in un editor di testo. Si noti che:

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

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Per altre informazioni sulla configurazione di esempio, vedere il file [Leggimi dell'esempio](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

Dopo avere esaminato il codice, è possibile usare il comando seguente per eseguire l'esempio:

```cmd/sh
python simple_thermostat.py
```

Viene visualizzato l'output seguente, che indica che il dispositivo invia dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà:

```cmd/sh
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
