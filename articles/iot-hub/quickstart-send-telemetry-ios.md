---
title: Guida introduttiva sull'invio di dati di telemetria all'hub IoT di Azure | Microsoft Docs
description: In questa guida introduttiva si esegue un'applicazione iOS di esempio per inviare dati di telemetria simulati a un hub IoT e leggere i dati di telemetria dall'hub IoT per l'elaborazione nel cloud.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/20/2018
ms.author: kgremban
ms.openlocfilehash: dbc1cc4a72d0346c92d506358c39a66a4d780b32
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38309746"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>Guida introduttiva: inviare dati di telemetria da un dispositivo a un hub IoT (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

L'hub IoT è un servizio di Azure che consente di acquisire volumi elevati di dati di telemetria dai dispositivi IoT nel cloud per l'archiviazione o l'elaborazione. In questo articolo si inviano dati di telemetria da un'applicazione di dispositivo simulato all'hub IoT. Sarà quindi possibile visualizzare i dati da un'applicazione back-end. 

In questo articolo si usa un'applicazione Swift già pronta per inviare i dati di telemetria e un'utilità da riga di comando per leggere i dati di telemetria dall'hub IoT. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

- L'esempio di codice scaricato da [esempi di Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip). 
- La versione più recente di [XCode](https://developer.apple.com/xcode/), che esegue la versione più recente di iOS SDK. Questa guida introduttiva è stata testata con XCode 9.3 e iOS 11.3.
- La versione più recente di [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).
- L'utilità da riga di comando iothub-explorer, che legge i dati di telemetria dall'hub IoT. Per installarla, installare prima [Node.js](https://nodejs.org) versione 4.x.x o successiva e quindi eseguire il comando seguente: 

   ```sh
   sudo npm install -g iothub-explorer
   ```

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]


## <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa l'interfaccia della riga di comando di Azure per registrare un dispositivo simulato.

1. Aggiungere l'estensione della riga di comando dell'hub IoT CLI e creare l'identità del dispositivo. Sostituire `{YourIoTHubName}` con il nome dell'hub IoT:

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myiOSdevice
   ```

    Se si sceglie un nome diverso per il dispositivo, aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

1. Eseguire il comando seguente per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato:

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id myiOSdevice --output table
   ```

   Annotare la stringa di connessione del dispositivo, che avrà questo aspetto: `Hostname=...=`. Il valore verrà usato più avanti in questo articolo.

1. È inoltre necessaria una _stringa di connessione del servizio_ per consentire alle applicazioni back-end di connettersi all'hub IoT e recuperare i messaggi da dispositivo a cloud. Il comando seguente recupera la stringa di connessione del servizio per l'hub IoT:

   ```azurecli-interactive
   az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
   ```

   Annotare la stringa di connessione del servizio, che avrà questo aspetto: `Hostname=...=`. Il valore verrà usato più avanti in questo articolo.

## <a name="send-simulated-telemetry"></a>Inviare dati di telemetria simulati

L'applicazione di esempio viene eseguita in un dispositivo iOS, che si connette a un endpoint specifico del dispositivo nell'hub IoT e invia dati di telemetria simulati di temperatura e umidità. 

### <a name="install-cocoapods"></a>Installare CocoaPods

CocoaPods gestisce le dipendenze per i progetti iOS che usano librerie di terze parti.

In una finestra del terminale passare alla cartella Azure-IoT-Samples-iOS scaricata nei prerequisiti. Passare quindi alla cartella del progetto di esempio:

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

L'app di esempio eseguita nell'emulatore XCode mostra i dati relativi ai messaggi inviati dal dispositivo. I dati possono inoltre essere visualizzati dall'hub IoT man mano che vengono ricevuti. L'utilità da riga di comando `iothub-explorer` si connette all'endpoint **Eventi** sul lato servizio dell'hub IoT. 

Aprire una nuova finestra del terminale. Eseguire il comando seguente, sostituendo {your hub service connection string} con la stringa di connessione del servizio recuperata all'inizio di questo articolo:

```sh
iothub-explorer monitor-events myiOSdevice --login "{your hub service connection string}"
```

La schermata seguente mostra il tipo di dati di telemetria visualizzati nella finestra del terminale:

![Visualizzare i dati di telemetria](media/quickstart-send-telemetry-ios/view-telemetry.png)

Se si verifica un errore quando si esegue il comando iothub-explorer, accertarsi di usare la *stringa di connessione del servizio* per l'hub IoT e non la *stringa di connessione del dispositivo* per il dispositivo IoT. Entrambe le stringhe di connessione iniziano con **Hostname={iothubname}**, ma la stringa di connessione del servizio contiene la proprietà **SharedAccessKeyName**, mentre la stringa di connessione del dispositivo contiene **DeviceID**. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è configurato un hub IoT e registrato un dispositivo, si sono inviati dati di telemetria simulati all'hub da un dispositivo iOS e si sono letti i dati di telemetria dall'hub. 

Per informazioni su come controllare il dispositivo simulato da un'applicazione back-end, continuare nella Guida introduttiva successiva.

> [!div class="nextstepaction"]
> [Guida introduttiva: Controllare un dispositivo connesso a un hub IoT](quickstart-control-device-node.md)

<!-- Links -->
[lnk-process-d2c-tutorial]: tutorial-routing.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
