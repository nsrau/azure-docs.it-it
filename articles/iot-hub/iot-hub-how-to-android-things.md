---
title: Sviluppare per la piattaforma Android Things tramite Azure IoT SDK | Microsoft Docs
description: Guida per gli sviluppatori - Informazioni sullo sviluppo in Android Things tramite gli SDK per l'hub IoT di Azure.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: yizhon
ms.openlocfilehash: 8e36cee9857c00fcb618a8491595432fb0fd60fd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64571269"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Sviluppare per la piattaforma Android Things tramite Azure IoT SDK

Gli [SDK per l'hub IoT di Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) offrono il supporto di primo livello per piattaforme note come Windows, Linux, OSX, MBED e piattaforme per dispositivi mobili come Android e iOS.  Come parte dell'impegno volto a offrire una più ampia scelta e maggiore flessibilità nelle distribuzioni di IoT, Java SDK supporta anche la piattaforma [Android Things](https://developer.android.com/things/).  Gli sviluppatori possono sfruttare i vantaggi del sistema operativo Android Things sul lato dispositivo e usare l'[hub IoT di Azure](about-iot-hub.md) come hub di messaggi centrale che garantisce la scalabilità per milioni di dispositivi connessi simultaneamente.

Questa esercitazione illustra i passaggi per compilare un'applicazione lato dispositivo in Android Things con Azure IoT Java SDK.

## <a name="prerequisites"></a>Prerequisiti

* Hardware supportato da Android Things con il sistema operativo Android Things in esecuzione.  È possibile fare riferimento alla [documentazione di Android Things](https://developer.android.com/things/get-started/kits#flash-at) per informazioni su come eseguire il flashing del sistema operativo Android Things.  Assicurarsi che il dispositivo Android Things sia connesso a Internet con periferiche di base quali la tastiera, lo schermo e il mouse collegati.  Questa esercitazione usa Raspberry Pi 3.

* Ultima versione di [Android Studio](https://developer.android.com/studio/)

* Ultima versione di [Git](https://git-scm.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire i comandi seguenti in Azure Cloud Shell per aggiungere l'estensione dell'interfaccia della riga di comando dell'hub IoT e per creare l'identità del dispositivo.

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

   **MyAndroidThingsDevice**: nome specificato per il dispositivo registrato. Usare MyAndroidThingsDevice come illustrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usare tale nome nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Eseguire i comandi seguenti in Azure Cloud Shell per ottenere il *stringa di connessione del dispositivo* per il dispositivo appena registrato. Sostituire `YourIoTHubName` seguito con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Annotare la stringa di connessione del dispositivo, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida introduttiva.

## <a name="building-an-android-things-application"></a>Compilazione di un'applicazione Android Things

1. Il primo passaggio per la compilazione di un'applicazione Android Things consiste nel connettersi ai dispositivi Android Things. Connettere il dispositivo Android Things a uno schermo e a Internet. Android Things mette a disposizione la [documentazione](https://developer.android.com/things/get-started/kits) con informazioni su come connettersi al Wi-Fi. Dopo la connessione a Internet, prendere nota dell'indirizzo IP elencato in Reti.

2. Usare lo strumento [adb](https://developer.android.com/studio/command-line/adb) per connettere il dispositivo Android Things all'indirizzo IP annotato in precedenza. Verificare la connessione usando il comando seguente dal terminale. Verranno visualizzati i dispositivi elencati come "connesso".

   ```
   adb devices
   ```

3. Scaricare l'esempio per Android/Android Things da questo [repository](https://github.com/Azure-Samples/azure-iot-samples-java) o usare Git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. In Android Studio aprire il progetto Android in "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample".

5. Aprire il file gradle.properties e sostituire "Device_connection_string" con la stringa di connessione del dispositivo annotata in precedenza.
 
6. Fare clic su Run - Debug (Esegui - Esegui debug) e selezionare il dispositivo per distribuire il codice nei dispositivi Android Things.

7. Se l'applicazione viene avviata correttamente, è possibile osservare un'applicazione in esecuzione nel dispositivo Android Things. Questa applicazione di esempio invia letture di temperature generate in modo casuale.

## <a name="read-the-telemetry-from-your-hub"></a>Leggere i dati di telemetria dell'hub

I dati possono essere visualizzati dall'hub IoT man mano che vengono ricevuti. L'estensione dell'interfaccia della riga di comando dell'hub IoT può connettersi all'endpoint **Eventi** sul lato servizio dell'hub IoT. L'estensione riceve i messaggi da dispositivo a cloud inviati dal dispositivo simulato. In genere, un'applicazione di back-end di hub IoT viene eseguita nel cloud per ricevere ed elaborare i messaggi da dispositivo a cloud.

Eseguire i comandi seguenti in Azure Cloud Shell, sostituendo `YourIoTHubName` con il nome dell'hub IoT:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su [come gestire le funzionalità di connettività e messaggistica affidabile](iot-hub-reliability-features-in-sdks.md) con gli SDK per l'hub IoT.
* Informazioni su come [sviluppare per piattaforme per dispositivi mobili](iot-hub-how-to-develop-for-mobile-devices.md) come iOS e Android.
* [Supporto della piattaforma di Azure IoT SDK](iot-hub-device-sdk-platform-support.md)
