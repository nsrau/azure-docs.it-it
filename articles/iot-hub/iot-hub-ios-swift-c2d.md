---
title: Messaggi da cloud a dispositivo con l'hub IoT di Azure (iOS) | Documentazione Microsoft
description: Come inviare messaggi da cloud a dispositivo a un dispositivo da un hub IoT di Azure usando gli SDK di Azure IoT per iOS.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: af1b331836cd025bbe15665aa03faee000e7c4f0
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404233"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Inviare messaggi da cloud a dispositivo con l'hub IoT (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. La Guida introduttiva inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-ios.md) Internet viene illustrato come creare un hub Internet, effettuare il provisioning di un'identità del dispositivo e codificare un'app per dispositivo simulato che invia messaggi da dispositivo a cloud.

Questa esercitazione illustra come:

* Dal back-end della soluzione inviare messaggi da cloud a dispositivo a un singolo dispositivo tramite l'hub IoT.

* Ricevere messaggi da cloud a dispositivo in un dispositivo.

* Dal back-end della soluzione, richiedere il riconoscimento del recapito (*feedback*) per i messaggi inviati a un dispositivo dall'hub Internet.

Altre informazioni sui messaggi da cloud a dispositivo sono disponibili nella [sezione di messaggistica della guida per sviluppatori dell'hub IoT](iot-hub-devguide-messaging.md).

Alla fine di questo articolo, vengono eseguiti due progetti Swift iOS:

* **sample-device**, la stessa app creata in [Send telemetry from a device to an IoT hub](quickstart-send-telemetry-ios.md) (Inviare dati di telemetria da un dispositivo a un hub IoT), che si connette all'hub IoT e riceve messaggi da cloud a dispositivo.

* **Sample-Service**, che invia un messaggio da cloud a dispositivo all'app per dispositivo simulato tramite l'hub Internet e quindi riceve la conferma di recapito.

> [!NOTE]
> L’hub IoT dispone del supporto SDK per molte piattaforme e linguaggi (inclusi C, Java e Javascript) tramite gli SDK del dispositivo IoT Azure. Per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure, vedere il [Centro per sviluppatori Azure IoT](https://www.azure.com/develop/iot).

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* Un hub IoT attivo in Azure.

* Esempio di codice di [esempi di Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip).

* La versione più recente di [XCode](https://developer.apple.com/xcode/), che esegue la versione più recente di iOS SDK. Questa guida introduttiva è stata testata con XCode 9.3 e iOS 11.3.

* La versione più recente di [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

## <a name="simulate-an-iot-device"></a>Simulare un dispositivo IoT

In questa sezione viene simulato un dispositivo iOS che esegue un'applicazione Swift per ricevere messaggi da cloud a dispositivo dall'hub IoT. 

Questo è il dispositivo di esempio creato nell'articolo [Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-ios.md). Se è già in esecuzione, è possibile ignorare questa sezione.

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

### <a name="run-the-sample-device-application"></a>Eseguire l'applicazione del dispositivo di esempio

1. Recuperare la stringa di connessione per il dispositivo. È possibile copiare questa stringa dal [portale di Azure](https://portal.azure.com) nel pannello dei dettagli del dispositivo o recuperarla con il comando dell'interfaccia della riga di comando seguente:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Aprire l'area di lavoro di esempio in XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Espandere il progetto **MQTT Client Sample** e quindi la cartella con lo stesso nome.  

3. Aprire **ViewController.swift** per la modifica in XCode. 

4. Cercare la variabile **connectionString** e aggiornare il valore con la stringa di connessione per il dispositivo copiata nel primo passaggio.

5. Salvare le modifiche. 

6. Eseguire il progetto nell'emulatore di dispositivo con il pulsante **Compila ed esegui** o la combinazione di tasti **Comando + R**.

   ![Eseguire il progetto](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub Internet

In questo articolo viene creato un servizio back-end per l'invio di messaggi da cloud a dispositivo tramite l'hub Internet delle cose creato in inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-ios.md). Per inviare messaggi da cloud a dispositivo, il servizio richiede l'autorizzazione **Connect del servizio** . Per impostazione predefinita, ogni hub tutto viene creato con un criterio di accesso condiviso denominato **Service** che concede l'autorizzazione.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>Simulare un dispositivo del servizio

In questa sezione viene simulato un secondo dispositivo iOS con un'app Swift che invia messaggi da cloud a dispositivo tramite l'hub IoT. Questa configurazione è utile per scenari IoT in cui è presente un iPhone o iPad che funge da controller per altri dispositivi iOS è connessi a un hub IoT.

### <a name="install-cocoapods"></a>Installare CocoaPods

CocoaPods gestisce le dipendenze per i progetti iOS che usano librerie di terze parti.

Passare alla cartella degli esempi iOS Azure IoT scaricata nei prerequisiti. Passare quindi al progetto del servizio di esempio:

```sh
cd quickstart/sample-service
```

Assicurarsi che XCode sia chiuso, quindi eseguire il comando seguente per installare i CocoaPods dichiarati nel file **podfile**:

```sh
pod install
```

Oltre a installare i pod necessari per il progetto, il comando di installazione crea anche un file di area di lavoro XCode già configurato per usare i pod per le dipendenze.

### <a name="run-the-sample-service-application"></a>Eseguire l'applicazione del servizio di esempio

1. Aprire l'area di lavoro di esempio in XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. Espandere il progetto **AzureIoTServiceSample** e quindi espandere la cartella con lo stesso nome.  

3. Aprire **ViewController.swift** per la modifica in XCode. 

4. Cercare la variabile **ConnectionString** e aggiornare il valore con la stringa di connessione del servizio copiata in precedenza in [ottenere la stringa di connessione dell'hub Internet](#get-the-iot-hub-connection-string).

5. Salvare le modifiche.

6. In Xcode, modificare le impostazioni dell'emulatore specificando un dispositivo iOS diverso da quello usato per eseguire il dispositivo IoT. XCode non consente di eseguire più emulatori dello stesso tipo.

   ![Modificare il dispositivo emulatore](media/iot-hub-ios-swift-c2d/change-device.png)

7. Eseguire il progetto nell'emulatore di dispositivo con il pulsante **Compila ed esegui** o la combinazione di tasti **Comando + R**.

   ![Eseguire il progetto](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio da cloud a dispositivo

A questo punto si è pronti per usare le due applicazioni per inviare e ricevere messaggi da cloud a dispositivo.

1. Nell'app **iOS App Sample** in esecuzione nel dispositivo IoT simulato, fare clic su **Start**. L'applicazione inizia a inviare messaggi da dispositivo a cloud, ma anche a restare in ascolto dei messaggi da cloud a dispositivo.

   ![Visualizzare l'app del dispositivo IoT di esempio](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. Nell'app **IoTHub Service Client Sample** in esecuzione nel dispositivo del servizio simulato, immettere l'ID per il dispositivo IoT a cui si vuole inviare un messaggio. 

3. Scrivere un messaggio come testo normale e quindi fare clic su **Send**.

    Non appena si fa clic su Send vengono eseguite varie azioni. L'esempio di servizio invia il messaggio all'hub IoT, accessibile per l'app grazie alla stringa di connessione del servizio fornita. L'hub IoT controlla l'ID del dispositivo, invia il messaggio al dispositivo di destinazione e invia una ricevuta di conferma al dispositivo di origine. L'app in esecuzione nel dispositivo IoT simulato verifica la presenza di messaggi dall'hub IoT e stampa il testo partendo dal più recente sullo schermo.

    L'output dovrebbe essere simile all'esempio seguente:

   ![Visualizzare i messaggi da cloud a dispositivo](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come inviare e ricevere messaggi da cloud a dispositivo.

Per visualizzare esempi di soluzioni end-to-end complete che usano l'hub IoT, vedere la documentazione degli [acceleratori di soluzioni di Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md).
