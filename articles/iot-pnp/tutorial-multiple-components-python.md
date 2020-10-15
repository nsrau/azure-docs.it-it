---
title: Connettere il codice Python di un dispositivo Plug and Play IoT di esempio con componenti a un hub IoT | Microsoft Docs
description: Compilare ed eseguire il codice Python di un dispositivo Plug and Play IoT di esempio che usa più componenti e si connette a un hub IoT. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1b329206dcc41d8f7fcb3874da037f5b57142722
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613663"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-multiple-component-device-application-to-iot-hub-python"></a>Esercitazione: Connettere un'applicazione di un dispositivo Plug and Play IoT di esempio con più componenti a un hub IoT (Python)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Questa esercitazione illustra come creare un'applicazione di un dispositivo Plug and Play IoT di esempio con componenti, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate all'hub. L'applicazione di esempio è scritta per Python ed è inclusa in Azure IoT SDK per dispositivi per Python. Un integratore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza doverne visualizzare il codice.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Per completare questa esercitazione, è necessario Python 3.7 nel computer di sviluppo. È possibile scaricare la versione più recente consigliata per più piattaforme da [python.org](https://www.python.org/). È possibile controllare la versione di Python con il comando seguente:  

```cmd/sh
python --version
```

È possibile scaricare la versione più recente consigliata per più piattaforme da [python.org](https://www.python.org/).

## <a name="download-the-code"></a>Scaricare il codice

Il pacchetto **azure-iot-device** viene pubblicato come PIP.

Nell'ambiente python locale installare il pacchetto come indicato di seguito:

```cmd/sh
pip install azure-iot-device
```

Se è stata completato l'argomento [Avvio rapido: Connettere un'applicazione di un dispositivo Plug and Play IoT di esempio in esecuzione in Windows a un hub IoT (Python)](quickstart-connect-device-python.md), il repository è già stato clonato.

Clonare il repository IoT di Python SDK:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>Esaminare il codice

Questo esempio implementa un dispositivo termoregolatore Plug and Play IoT. Il modello implementato da questo esempio usa [più componenti](concepts-components.md). Il [file di modello DTDL (Digital Twins Definition Language) per il dispositivo termoregolatore](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definisce i dati di telemetria, le proprietà e i comandi implementati.

La cartella *azure-iot-sdk-python\azure-iot-device\samples\pnp* contiene il codice di esempio per il dispositivo Plug and Play IoT. I file per l'esempio di dispositivo termoregolatore sono:

- temp_controller_with_thermostats.py
- pnp_helper.py

Il dispositivo termoregolatore include più componenti e un componente predefinito ed è basato sul modello DTDL.

Aprire il file *temp_controller_with_thermostats.py* in un editor a scelta. Il codice di questo file:

1. Importa `pnp_helper.py` per ottenere l'accesso ai metodi helper.

1. Definisce due identificatori DTMI (Digital Twin Model Identifier) che rappresentano in modo univoco due interfacce diverse, definite nel modello DTDL. I componenti di un dispositivo termoregolatore reale dovranno implementare queste due interfacce. Queste due interfacce sono già pubblicate in un repository centrale. Questi DTMI devono essere noti all'utente e variano in base allo scenario di implementazione del dispositivo. Per l'esempio corrente, queste due interfacce rappresentano:

    - Un termostato
    - Informazioni sul dispositivo sviluppate da Azure.

1. Definisce il DTMI `model_id` per il dispositivo da implementare. Il DTMI è definito dall'utente e deve corrispondere al DTMI nel file del modello DTDL.

1. Definisce i nomi assegnati ai componenti nel file DTDL. Nel DTDL sono inclusi due termostati e un componente di informazioni sul dispositivo. Nel componente predefinito viene anche definita una costante denominata `serial_number`. Non è possibile cambiare `serial_number` per un dispositivo.

1. Definisce le implementazioni del gestore comandi, ossia l'operazione eseguita dal dispositivo quando riceve richieste di comandi.

1. Definisce le funzioni per la creazione della risposta a un comando, ossia come risponde il dispositivo alle richieste dei comandi. Creare le funzioni di risposta ai comandi se un comando deve inviare una risposta personalizzata all'hub IoT. Se non viene specificata una funzione di risposta per un comando, viene inviata una risposta generica. In questo esempio solo il comando **getMaxMinReport** include una risposta personalizzata.

1. Definisce una funzione per l'invio di dati di telemetria da questo dispositivo. Sia i termostati che il componente predefinito inviano dati di telemetria. Questa funzione accetta un parametro facoltativo per il nome del componente che consente di identificare il componente che ha inviato i dati di telemetria.

1. Definisce un listener per le richieste dei comandi.

1. Definisce un listener per gli aggiornamenti delle proprietà desiderati.

1. Include una funzione `main` che:

    - Usa l'SDK del dispositivo per creare un client del dispositivo e connettersi all'hub IoT. Il dispositivo invia `model_id` in modo che possa essere identificato dall'hub IoT come dispositivo Plug and Play IoT.

    - Usa la funzione `create_reported_properties` nel file helper per creare le proprietà. Passare il nome del componente e le proprietà come coppie chiave-valore a questa funzione.

    - Aggiorna le proprietà leggibili per i relativi componenti chiamando `patch_twin_reported_properties`.

    - Avvia l'ascolto di richieste dei comandi tramite la funzione `execute_command_listener`. La funzione configura un listener per le richieste dei comandi provenienti dal servizio. Quando si configura il listener, si specificano valori i parametri `method_name`, `user_command_handler` e `create_user_response_handler` (facoltativo).
        - `method_name` definisce la richiesta del comando. In questo esempio il modello definisce i comandi **reboot** e **getMaxMinReport**.
        - La funzione `user_command_handler` definisce l'operazione che il dispositivo deve eseguire quando riceve un comando.
        - La funzione `create_user_response_handler` crea una risposta da inviare all'hub IoT quando un comando viene eseguito correttamente. È possibile visualizzare questa risposta nel portale. Se questa funzione non viene specificata, al servizio viene inviata una risposta generica.

    - Usa `execute_property_listener` per restare in ascolto di aggiornamenti delle proprietà.

    - Avvia l'invio di dati di telemetria tramite `send_telemetry`. Il codice di esempio usa un ciclo per chiamare tre funzioni di invio di dati di telemetria. Ciascuna di esse viene chiamata ogni otto secondi

    - Disabilita tutti i listener e le attività ed esce dal ciclo quando si preme **Q** o **q**.

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Per altre informazioni sulla configurazione di esempio, vedere il file [Leggimi dell'esempio](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

Usare il comando seguente per eseguire l'esempio:

```cmd/sh
python temp_controller_with_thermostats.py
```

Il dispositivo di esempio invia messaggi di telemetria a intervalli di pochi secondi all'hub IoT.

Viene visualizzato l'output seguente, che indica che il dispositivo invia dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà.

![Messaggi di conferma del dispositivo](media/tutorial-multiple-components-python/multiple-component.png)

Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come connettere un dispositivo Plug and Play IoT con componenti a un hub IoT. Per altre informazioni sui modelli di dispositivo Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Guida per sviluppatori alla modellazione di Plug and Play IoT](concepts-developer-guide-device-csharp.md)
