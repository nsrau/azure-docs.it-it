---
title: Guida introduttiva sull'invio di dati di telemetria all'hub IoT di Azure | Microsoft Docs
description: In questa guida introduttiva si esegue un'applicazione iOS di esempio per inviare dati di telemetria simulati a un hub IoT e leggere i dati di telemetria dall'hub IoT per l'elaborazione nel cloud.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/03/2019
ms.openlocfilehash: a23518cd016a1711e47734df0f7179770aa92a87
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166978"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>Guida introduttiva: Inviare dati di telemetria da un dispositivo a un hub IoT (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

L'hub IoT è un servizio di Azure che consente di acquisire volumi elevati di dati di telemetria dai dispositivi IoT nel cloud per l'archiviazione o l'elaborazione. In questo articolo si inviano dati di telemetria da un'applicazione di dispositivo simulato all'hub IoT. Sarà quindi possibile visualizzare i dati da un'applicazione back-end.

In questo articolo si usa un'applicazione Swift già pronta per inviare i dati di telemetria e un'utilità da riga di comando per leggere i dati di telemetria dall'hub IoT.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- L'esempio di codice scaricato da [esempi di Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip).
- La versione più recente di [XCode](https://developer.apple.com/xcode/), che esegue la versione più recente di iOS SDK. Questa guida di avvio rapido è stata testata con XCode 10.2 e iOS 12.2.
- La versione più recente di [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).
- Eseguire questo comando per aggiungere l'estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure all'istanza di Cloud Shell. L'estensione IoT aggiunge i comandi specifici di hub IoT, IoT Edge e servizio Device Provisioning in hub IoT all'interfaccia della riga di comando di Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire il comando seguente in Azure Cloud Shell per creare l'identità del dispositivo.

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

   **myiOSdevice**: nome del dispositivo da registrare. È consigliabile usare **myiOSdevice**, come illustrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usare tale nome anche nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myiOSdevice
   ```

1. Eseguire il comando seguente in Azure Cloud Shell per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato:

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id myiOSdevice --output table
   ```

   Annotare la stringa di connessione del dispositivo, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=myiOSdevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida di avvio rapido.

## <a name="send-simulated-telemetry"></a>Inviare dati di telemetria simulati

L'applicazione di esempio viene eseguita in un dispositivo iOS, che si connette a un endpoint specifico del dispositivo nell'hub IoT e invia dati di telemetria simulati di temperatura e umidità. 

### <a name="install-cocoapods"></a>Installare CocoaPods

CocoaPods gestisce le dipendenze per i progetti iOS che usano librerie di terze parti.

In una finestra del terminale locale passare alla cartella Azure-IoT-Samples-iOS scaricata come prerequisito. Passare quindi alla cartella del progetto di esempio:

```sh
cd quickstart/sample-device
```

Assicurarsi che XCode sia chiuso, quindi eseguire il comando seguente per installare i CocoaPods dichiarati nel file **podfile**:

```sh
pod install
```

Oltre a installare i pod necessari per il progetto, il comando di installazione crea anche un file di area di lavoro XCode già configurato per usare i pod per le dipendenze. 

### <a name="run-the-sample-application"></a>Eseguire l'applicazione di esempio 

1. Aprire l'area di lavoro di esempio in XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Espandere il progetto **MQTT Client Sample** e quindi espandere la cartella con lo stesso nome.  
3. Aprire **ViewController.swift** per la modifica in XCode. 
4. Cercare la variabile **connectionString** e aggiornarne il valore con la stringa di connessione del dispositivo annotata in precedenza.
5. Salvare le modifiche. 
6. Eseguire il progetto nell'emulatore di dispositivo con il pulsante **Compila ed esegui** o la combinazione di tasti **Comando + R**. 

   ![Eseguire il progetto](media/quickstart-send-telemetry-ios/run-sample.png)

7. Quando l'emulatore si apre, selezionare **Start** nell'app di esempio.

Nella schermata seguente sono mostrati alcuni esempi di output dei dati di telemetria simulati inviati dall'applicazione all'hub IoT:

   ![Eseguire il dispositivo simulato](media/quickstart-send-telemetry-ios/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Leggere i dati di telemetria dell'hub

L'app di esempio eseguita nell'emulatore XCode mostra i dati relativi ai messaggi inviati dal dispositivo. I dati possono inoltre essere visualizzati dall'hub IoT man mano che vengono ricevuti. L'estensione dell'interfaccia della riga di comando dell'hub IoT può connettersi all'endpoint **Eventi** sul lato servizio dell'hub IoT. L'estensione riceve i messaggi da dispositivo a cloud inviati dal dispositivo simulato. In genere, un'applicazione di back-end di hub IoT viene eseguita nel cloud per ricevere ed elaborare i messaggi da dispositivo a cloud.

Eseguire i comandi seguenti in Azure Cloud Shell, sostituendo `YourIoTHubName` con il nome dell'hub IoT:

```azurecli-interactive
az iot hub monitor-events --device-id myiOSdevice --hub-name {YourIoTHubName}
```

Lo screenshot seguente mostra l'output mentre l'estensione riceve i dati di telemetria inviati dal dispositivo simulato all'hub:

La schermata seguente illustra il tipo di dati di telemetria visualizzati nella finestra del terminale locale:

![Visualizzare i dati di telemetria](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato configurato un hub IoT, è stato registrato un dispositivo, sono stati inviati dati di telemetria simulati all'hub da un dispositivo iOS e i dati di telemetria sono stati letti dall'hub. 

Per informazioni su come controllare il dispositivo simulato da un'applicazione back-end, continuare nella Guida introduttiva successiva.

> [!div class="nextstepaction"]
> [Guida introduttiva: controllare un dispositivo connesso a un hub IoT](quickstart-control-device-node.md)
