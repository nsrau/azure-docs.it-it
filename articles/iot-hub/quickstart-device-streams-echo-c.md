---
title: Comunicare con un'applicazione del dispositivo in C tramite i flussi dispositivo dell'hub IoT (anteprima) | Microsoft Docs
description: In questo argomento di avvio rapido verrà eseguita un'applicazione sul lato servizio in C che comunica con un dispositivo IoT tramite un flusso dispositivo.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: f5e6128c1ecceda181f92b2d81e9ac06effbfce2
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834071"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Guida introduttiva: Comunicare con un'applicazione del dispositivo in C tramite i flussi dispositivo dell'hub IoT (anteprima)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

L'hub IoT di Microsoft Azure supporta attualmente i flussi dispositivo come [funzionalità in anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I [flussi dispositivo dell'hub IoT](iot-hub-device-streams-overview.md) consentono alle applicazioni del servizio e del dispositivo di comunicare in modo sicuro e di facile integrazione con i firewall. Durante l'anteprima pubblica, l'SDK C supporta solo i flussi dispositivo sul lato dispositivo. Di conseguenza, questa guida introduttiva illustra solo le istruzioni per eseguire l'applicazione sul lato dispositivo. È consigliabile eseguire un'applicazione sul lato servizio associata, disponibile nelle guide di avvio rapido seguenti:
 
   * [Comunicazione con le applicazioni del dispositivo in C# tramite i flussi dispositivo dell'hub IoT](./quickstart-device-streams-echo-csharp.md)

   * [Comunicazione con le applicazioni del dispositivo in Node.js tramite i flussi dispositivo dell'hub IoT](./quickstart-device-streams-echo-nodejs.md)

L'applicazione C sul lato dispositivo in questa guida introduttiva presenta le funzionalità seguenti:

* Stabilire un flusso dispositivo in un dispositivo IoT.

* Ricevere i dati inviati dal lato servizio e inviarli di nuovo al lato dispositivo.

Il codice illustrerà il processo di avvio di un flusso dispositivo, nonché come usarlo per inviare e ricevere dati.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* L'anteprima dei flussi dispositivo è attualmente supportata solo per gli hub IoT creati nelle aree seguenti:

  * **Stati Uniti centrali**

  * **Stati Uniti centrali EUAP**

* Installare [Visual Studio 2017](https://www.visualstudio.com/vs/) con il carico di lavoro [Sviluppo di applicazioni desktop con C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) abilitato.

* Installare la versione più recente di [Git](https://git-scm.com/download/).

* Eseguire il comando seguente per aggiungere l'estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure all'istanza di Cloud Shell. L'estensione IoT aggiunge i comandi specifici di hub IoT, IoT Edge e servizio Device Provisioning in hub IoT all'interfaccia della riga di comando di Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

Per questa guida introduttiva si userà [Azure IoT SDK per dispositivi per C](iot-hub-device-sdk-c-intro.md). Si preparerà un ambiente di sviluppo usato per clonare e compilare [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) da GitHub. L'SDK in GitHub include il codice di esempio usato in questa guida introduttiva.

1. Scaricare il [sistema di compilazione CMake](https://cmake.org/download/).

    È importante che nel computer siano installati i prerequisiti di Visual Studio (Visual Studio e carico di lavoro 'Sviluppo di applicazioni desktop con C++') **prima** di avviare l'installazione di `CMake`. Quando i prerequisiti sono pronti e il download è stato verificato, installare il sistema di compilazione CMake.

2. Aprire un prompt dei comandi o la shell Git Bash. Eseguire il comando seguente per clonare il repository GitHub [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Il completamento di questa operazione richiederà alcuni minuti.

3. Creare una sottodirectory `cmake` nella directory radice del repository Git e passare a tale cartella.

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Eseguire questi comandi dalla directory `cmake` per compilare una versione dell'SDK specifica per la piattaforma client di sviluppo.

   * Linux:

      ```bash
      cmake ..
      make -j
      ```

   * In Windows eseguire questi comandi al prompt dei comandi per gli sviluppatori per Visual Studio 2015 o 2017. Verrà generata una soluzione di Visual Studio per il dispositivo simulato nella directory `cmake`.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa sezione si registrerà un dispositivo simulato usando Azure Cloud Shell con l'[estensione IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest).

1. Eseguire il comando seguente in Azure Cloud Shell per creare l'identità del dispositivo.

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

   **MyDevice**: nome specificato per il dispositivo registrato. Usare MyDevice come illustrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usare tale nome nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Eseguire il comando seguente in Azure Cloud Shell per ottenere la *stringa di connessione del dispositivo* per il dispositivo appena registrato.

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Annotare la stringa di connessione del dispositivo che sarà simile a quella dell'esempio seguente:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida introduttiva.

## <a name="communicate-between-device-and-service-via-device-streams"></a>Stabilire la comunicazione tra un dispositivo e un servizio tramite i flussi dispositivo

In questa sezione verranno eseguite sia l'applicazione sul lato dispositivo sia l'applicazione sul lato servizio con le relative comunicazioni.

### <a name="run-the-device-side-application"></a>Eseguire l'applicazione sul lato dispositivo

Per eseguire l'applicazione sul lato dispositivo, è necessario eseguire la procedura seguente:

1. Fornire le credenziali del dispositivo modificando il file di origine `iothub_client_c2d_streaming_sample.c` nella cartella `iothub_client/samples/iothub_client_c2d_streaming_sample` e specificando la stringa di connessione del dispositivo.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[device connection string]";
   ```

2. Compilare il codice come segue:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

3. Eseguire il programma compilato:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>Eseguire l'applicazione sul lato servizio

Come indicato in precedenza, l'SDK dell'hub IoT per C supporta solo i flussi dispositivo sul lato dispositivo. Per compilare ed eseguire l'applicazione sul lato servizio, seguire la procedura disponibile in una delle guide di avvio rapido seguenti:

* [Comunicare con un'applicazione del dispositivo in C# tramite i flussi dispositivo dell'hub IoT](./quickstart-device-streams-echo-csharp.md)

* [Comunicare con un'applicazione del dispositivo in Node.js tramite i flussi dispositivo dell'hub IoT](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato configurato un hub IoT, è stato registrato un dispositivo, è stato stabilito un flusso dispositivo tra un'applicazione C sul dispositivo e un'altra applicazione sul lato servizio ed è stato usato il flusso per inviare dati tra le applicazioni.

Consultare i collegamenti seguenti per altre informazioni sui flussi dispositivo:

> [!div class="nextstepaction"]
> [Panoramica dei flussi dispositivo](./iot-hub-device-streams-overview.md)