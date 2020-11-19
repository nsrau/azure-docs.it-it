---
title: "Guida introduttiva: Inviare dati di telemetria dall'hub IoT di Azure con Java"
description: In questa guida introduttiva si eseguono due applicazioni Java di esempio per inviare dati di telemetria simulati a un hub IoT e leggere i dati di telemetria dall'hub IoT per l'elaborazione nel cloud.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- seo-java-august2019
- seo-java-september2019
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 05/26/2020
ms.openlocfilehash: 76326d0b68a3ff71bf95c09147d003e769e103db
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844643"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>Guida introduttiva: Inviare dati di telemetria a un hub IoT di Azure e leggerli con un'applicazione Java

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

In questa guida di avvio rapido si inviano dati di telemetria all'hub IoT di Azure e si leggono tali dati con un'applicazione Java. L'hub IoT è un servizio di Azure che consente di acquisire volumi elevati di dati di telemetria dai dispositivi IoT nel cloud per l'archiviazione o l'elaborazione. Questa guida di avvio rapido usa due applicazioni Java già scritte: una per l'invio dei dati di telemetria e una per la relativa lettura dall'hub. Prima di eseguire queste due applicazioni, creare un hub IoT e registrare un dispositivo con l'hub.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Java SE Development Kit 8. In [Supporto a lungo termine di Java per Azure e Azure Stack](/java/azure/jdk/?view=azure-java-stable) selezionare **Java 8** nella sezione **Supporto a lungo termine**.

    Per verificare la versione corrente di Java installata nel computer di sviluppo, usare il comando seguente:

    ```cmd/sh
    java -version
    ```

* [Apache Maven 3](https://maven.apache.org/download.cgi).

    Per verificare la versione corrente di Maven installata nel computer di sviluppo, usare il comando seguente:

    ```cmd/sh
    mvn --version
    ```

* [Un progetto Java di esempio](https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip).

* Porta 8883 aperta nel firewall. L'esempio di dispositivo di questo argomento di avvio rapido usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire questo comando in Azure Cloud Shell per creare l'identità del dispositivo.

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

   **MyJavaDevice**: nome del dispositivo da registrare. È consigliabile usare **MyJavaDevice**, come illustrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usare tale nome anche nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Eseguire il comando seguente in Azure Cloud Shell per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato:

    **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Annotare la stringa di connessione del dispositivo, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida di avvio rapido.

3. È necessario anche l'_endpoint compatibile con gli Hub eventi di Azure_, il _percorso compatibile con gli Hub eventi di Azure_, e la _chiave primaria di servizio_ dall'hub IoT dell'utente per consentire all'applicazione back-end di connettersi all'hub di IoT e recuperare i messaggi. I comandi seguenti recuperano questi valori per l'hub IoT:

     **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Prendere nota di questi tre valori, che verranno usati più avanti nella guida di avvio rapido.

## <a name="send-simulated-telemetry"></a>Inviare dati di telemetria simulati

L'applicazione del dispositivo simulato si connette a un endpoint specifico del dispositivo nell'hub IoT e invia dati di telemetria simulati di temperatura e umidità.

1. In una finestra del terminale locale passare alla cartella radice del progetto Java di esempio. Passare quindi alla cartella **iot-hub\Quickstarts\simulated-device**.

2. Aprire il file **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** in un editor di testo di propria scelta.

    Sostituire il valore della variabile `connString` con la stringa di connessione del dispositivo annotata in precedenza. Salvare quindi le modifiche nel file **SimulatedDevice.java**.

3. Nella finestra del terminale locale eseguire i comandi seguenti per installare le librerie necessarie e compilare l'applicazione del dispositivo simulato:

    ```cmd/sh
    mvn clean package
    ```

4. Nella finestra del terminale locale eseguire i comandi seguenti per eseguire l'applicazione del dispositivo simulato:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    La schermata seguente mostra l'output mentre l'applicazione del dispositivo simulato invia i dati di telemetria all'hub IoT:

    ![Output dei dati di telemetria inviati dal dispositivo all'hub IoT](media/quickstart-send-telemetry-java/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Leggere i dati di telemetria dell'hub

L'applicazione back-end si connette all'endpoint **Eventi** sul lato servizio dell'hub IoT. L'applicazione riceve i messaggi da dispositivo a cloud inviati dal dispositivo simulato. In genere, un'applicazione di back-end di hub IoT viene eseguita nel cloud per ricevere ed elaborare i messaggi da dispositivo a cloud.

1. In un'altra finestra del terminale locale passare alla cartella radice del progetto Java di esempio. Passare quindi alla cartella **iot-hub\Quickstarts\read-d2c-messages**.

2. Aprire il file **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** in un editor di testo di propria scelta. Aggiornare le variabili seguenti e salvare le modifiche nel file.

    | Variabile | valore |
    | -------- | ----------- |
    | `EVENT_HUBS_COMPATIBLE_ENDPOINT` | Sostituire il valore della variabile con l'endpoint compatibile con hub eventi annotato in precedenza. |
    | `EVENT_HUBS_COMPATIBLE_PATH`     | Sostituire il valore della variabile con il percorso compatibile con hub eventi annotato in precedenza. |
    | `IOT_HUB_SAS_KEY`                | Sostituire il valore della variabile con la chiave primaria di servizio annotata in precedenza. |

3. Nella finestra del terminale locale eseguire i comandi seguenti per installare le librerie necessarie e compilare l'applicazione back-end:

    ```cmd/sh
    mvn clean package
    ```

4. Nella finestra del terminale locale eseguire i comandi seguenti per eseguire l'applicazione back-end:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    La schermata seguente mostra l'output mentre l'applicazione back-end riceve i dati di telemetria inviati dal dispositivo simulato all'hub:

    ![Output mentre l'applicazione back-end riceve i dati di telemetria inviati all'hub IoT](media/quickstart-send-telemetry-java/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato configurato un hub IoT, è stato registrato un dispositivo, sono stati inviati dati di telemetria simulati all'hub usando un'applicazione Java e sono stati letti i dati di telemetria dall'hub usando una semplice applicazione back-end.

Per informazioni su come controllare il dispositivo simulato da un'applicazione back-end, continuare nella Guida introduttiva successiva.

> [!div class="nextstepaction"]
> [Avvio rapido: controllare un dispositivo connesso a un hub IoT](quickstart-control-device-java.md)
