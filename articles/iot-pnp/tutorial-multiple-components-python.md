---
title: Connettere il codice Python di un dispositivo Plug and Play IoT (anteprima) di esempio con componenti a un hub IoT | Microsoft Docs
description: Compilare ed eseguire il codice Python di un dispositivo Plug and Play IoT (anteprima) di esempio che usa più componenti e si connette a un hub IoT. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 0cde9caa2f2b68b1e75eac635a81865cc4b6b33c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351872"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-python"></a>Esercitazione: Connettere un'applicazione di un dispositivo Plug and Play IoT (anteprima) di esempio con più componenti all'hub IoT (Python)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Questa esercitazione illustra come creare un'applicazione di un dispositivo Plug and Play IoT di esempio con componenti e interfaccia radice, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate all'hub. L'applicazione di esempio è scritta per Python ed è inclusa in Azure IoT SDK per dispositivi per Python. Un integratore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza doverne visualizzare il codice.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario Python 3.7 nel computer di sviluppo. È possibile scaricare la versione più recente consigliata per più piattaforme da [python.org](https://www.python.org/). È possibile controllare la versione di Python con il comando seguente:  

```cmd/sh
python --version
```

È possibile scaricare la versione più recente consigliata per più piattaforme da [python.org](https://www.python.org/).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Per interagire con il dispositivo di esempio nella seconda parte di questa esercitazione, usare lo strumento**Azure IoT Explorer**. [Scaricare e installare la versione di Azure IoT Explorer più recente](./howto-use-iot-explorer.md) per il sistema operativo in uso.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Eseguire il comando seguente per ottenere la _stringa di connessione dell'hub IoT_ per l'hub. Prendere nota della stringa di connessione, poiché verrà usata più avanti in questa esercitazione:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> È anche possibile usare lo strumento Azure IoT Explorer per trovare la stringa di connessione dell'hub IoT.

Eseguire il comando seguente per ottenere la _stringa di connessione del dispositivo_ aggiunto all'hub. Prendere nota della stringa di connessione, poiché verrà usata più avanti in questa esercitazione:

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

Clonare il repository IoT di Python SDK ed estrarre **pnp-preview-refresh**:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>Esaminare il codice

Questo esempio implementa un dispositivo termoregolatore Plug and Play IoT. Il modello implementato da questo esempio usa [più componenti](concepts-components.md). Il [file di modello DTDL (Digital Twins Definition Language) per il dispositivo termoregolatore](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definisce i dati di telemetria, le proprietà e i comandi implementati.

La cartella *azure-iot-sdk-python\azure-iot-device\samples\pnp* contiene il codice di esempio per il dispositivo Plug and Play IoT. I file per l'esempio di dispositivo termoregolatore sono:

- pnp_temp_controller_with_thermostats.py
- pnp_helper_preview_refresh.py

Il dispositivo termoregolatore include più componenti e un'interfaccia radice ed è basato sul modello DTDL.

Aprire il file *pnp_temp_controller_with_thermostats.py* in un editor a scelta. Il codice di questo file:

1. Importa `pnp_helper_preview_refresh.py` per ottenere l'accesso ai metodi helper.

1. Definisce due identificatori DTMI (Digital Twin Model Identifier) che rappresentano in modo univoco due interfacce diverse, definite nel modello DTDL. I componenti di un dispositivo termoregolatore reale dovranno implementare queste due interfacce. Queste due interfacce sono già pubblicate in un repository centrale. Questi DTMI devono essere noti all'utente e variano in base allo scenario di implementazione del dispositivo. Per l'esempio corrente, queste due interfacce rappresentano:

  - Un termostato
  - Informazioni sul dispositivo sviluppate da Azure.

. Definisce il DTMI, `model_id`, per il dispositivo da implementare. Il DTMI è definito dall'utente e deve corrispondere al DTMI nel file del modello DTDL.

1. Definisce i nomi assegnati ai componenti nel file DTDL. Nel DTDL sono inclusi due termostati e un componente di informazioni sul dispositivo. Nell'interfaccia radice viene anche definita una costante denominata `serial_number`. Non è possibile cambiare `serial_number` per un dispositivo.

1. Definisce le implementazioni del gestore comandi, ossia l'operazione eseguita dal dispositivo quando riceve richieste di comandi.

1. Definisce le funzioni per la creazione della risposta a un comando, ossia come risponde il dispositivo alle richieste dei comandi. Creare le funzioni di risposta ai comandi se un comando deve inviare una risposta personalizzata all'hub IoT. Se non viene specificata una funzione di risposta per un comando, viene inviata una risposta generica. In questo esempio solo il comando **getMaxMinReport** include una risposta personalizzata.

1. Definisce una funzione per l'invio di dati di telemetria da questo dispositivo. Sia i termostati che l'interfaccia radice inviano dati di telemetria. Questa funzione accetta un parametro facoltativo per il nome del componente che consente di identificare il componente che ha inviato i dati di telemetria.

1. Definisce un listener per le richieste dei comandi.

1. Definisce un listener per gli aggiornamenti delle proprietà desiderati.

1. Include una funzione `main` che:

    1. Usa l'SDK del dispositivo per creare un client del dispositivo e connettersi all'hub IoT. Il dispositivo invia `model_id` in modo che possa essere identificato dall'hub IoT come dispositivo Plug and Play IoT.

    1. Usa la funzione `create_reported_properties` nel file helper per creare le proprietà. Passare il nome del componente e le proprietà come coppie chiave-valore a questa funzione.

    1. Aggiorna le proprietà leggibili per i relativi componenti chiamando `patch_twin_reported_properties`.

    1. Avvia l'ascolto di richieste dei comandi tramite la funzione `execute_command_listener`. La funzione configura un listener per le richieste dei comandi provenienti dal servizio. Quando si configura il listener, si specificano valori i parametri `method_name`, `user_command_handler` e `create_user_response_handler` (facoltativo).
        - `method_name` definisce la richiesta del comando. In questo esempio il modello definisce i comandi **reboot** e **getMaxMinReport**.
        - La funzione `user_command_handler` definisce l'operazione che il dispositivo deve eseguire quando riceve un comando.
        - La funzione `create_user_response_handler` crea una risposta da inviare all'hub IoT quando un comando viene eseguito correttamente. È possibile visualizzare questa risposta nel portale. Se questa funzione non viene specificata, al servizio viene inviata una risposta generica.

    1. Usa `execute_property_listener` per restare in ascolto di aggiornamenti delle proprietà.

    1. Avvia l'invio di dati di telemetria tramite `send_telemetry`. Il codice di esempio usa un ciclo per chiamare tre funzioni di invio di dati di telemetria. Ciascuna di esse viene chiamata ogni otto secondi

    1. Disabilita tutti i listener e le attività ed esce dal ciclo quando si preme **Q** o **q**.

Dopo aver visto il codice, creare una variabile di ambiente denominata **IOTHUB_DEVICE_CONNECTION_STRING** per archiviare la stringa di connessione del dispositivo annotata in precedenza. Usare il comando seguente per eseguire l'esempio:

```cmd/sh
python pnp_temp_controller_with_thermostats.py
```

Il dispositivo di esempio invia messaggi di telemetria a intervalli di pochi secondi all'hub IoT.

Viene visualizzato l'output seguente, che indica che il dispositivo invia dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà.

![Messaggi di conferma del dispositivo](media/tutorial-multiple-components-python/multiple-component.png)

Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come connettere un dispositivo Plug and Play IoT con componenti a un hub IoT. Per altre informazioni sui modelli di dispositivo Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Guida per gli sviluppatori alla modellazione di Plug and Play IoT (anteprima)](concepts-developer-guide.md)
