---
title: Guida introduttiva per Node.js ai flussi dispositivo dell'hub IoT di Azure (anteprima) | Microsoft Docs
description: In questa guida introduttiva verrà eseguita un'applicazione sul lato servizio in Node.js che comunica con un dispositivo IoT tramite un flusso dispositivo.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 1e7efe28918cafb3fa9547c144be3360768d549c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58079896"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Guida introduttiva: Comunicare con un'applicazione del dispositivo in Node.js tramite i flussi dispositivo dell'hub IoT (anteprima)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

L'hub IoT di Microsoft Azure attualmente supporta i flussi dispositivo come [funzionalità di anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I [flussi dispositivo dell'hub IoT](./iot-hub-device-streams-overview.md) consentono alle applicazioni del servizio e del dispositivo di comunicare in modo sicuro e di facile integrazione con i firewall. Durante l'anteprima pubblica, l'SDK Node.js supporta solo i flussi dispositivo sul lato servizio. Di conseguenza, questa guida introduttiva illustra solo le istruzioni per eseguire l'applicazione sul lato servizio. È consigliabile eseguire un'applicazione sul dispositivo associata disponibile nella [guida introduttiva per C](./quickstart-device-streams-echo-c.md) o nella [guida introduttiva per C#](./quickstart-device-streams-echo-csharp.md).

L'applicazione Node.js sul lato servizio di questa guida introduttiva presenta le funzionalità seguenti:

* Crea un flusso dispositivo in un dispositivo IoT.

* Legge l'input dalla riga di comando e lo invia all'applicazione del dispositivo che lo invia di nuovo all'applicazione sul lato servizio.

Il codice illustra il processo di avvio di un flusso dispositivo, nonché come usarlo per inviare e ricevere dati.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a name="prerequisites"></a>Prerequisiti

L'anteprima dei flussi dispositivo attualmente è supportata solo per gli hub IoT creati nelle aree seguenti:

  - **Stati Uniti centrali**
  - **Stati Uniti centrali EUAP**

Per eseguire l'applicazione sul lato servizio in questa guida introduttiva è necessario Node.js versione 4.x.x o versione successiva nel computer di sviluppo.

È possibile scaricare Node.js per più piattaforme da [Nodejs.org](https://nodejs.org).

È possibile verificare la versione corrente di Node.js installata nel computer di sviluppo tramite il comando seguente:

```
node --version
```

Se non è già stato fatto, scaricare il progetto Node.js di esempio da https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip ed estrarre l'archivio ZIP.


## <a name="create-an-iot-hub"></a>Creare un hub IoT

Se è stata completata la precedente [Guida introduttiva: Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md), è possibile ignorare questo passaggio.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]


## <a name="register-a-device"></a>Registrare un dispositivo

Se è stata completata la precedente [Guida introduttiva: Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md), è possibile ignorare questo passaggio.

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire i comandi seguenti in Azure Cloud Shell per aggiungere l'estensione dell'interfaccia della riga di comando dell'hub IoT e per creare l'identità del dispositivo. 

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

   **MyDevice**: nome specificato per il dispositivo registrato. Usare MyDevice come illustrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usare tale nome nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. È necessaria anche una _stringa di connessione del servizio_ per consentire all'applicazione back-end di connettersi all'hub IoT dell'utente e recuperare i messaggi. Il comando seguente recupera la stringa di connessione del servizio per l'hub IoT:

    **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Prendere nota del valore restituito che sarà simile a quello seguente:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`


## <a name="communicate-between-device-and-service-via-device-streams"></a>Stabilire la comunicazione tra un dispositivo e un servizio tramite i flussi dispositivo

### <a name="run-the-device-side-application"></a>Eseguire l'applicazione sul lato dispositivo

Come indicato in precedenza, l'SDK Node.js dell'hub IoT supporta solo i flussi dispositivo sul lato servizio. Per l'applicazione sul lato dispositivo usare i programmi del dispositivo associati disponibili nella [guida introduttiva per C](./quickstart-device-streams-echo-c.md) o nella [guida introduttiva per C#](./quickstart-device-streams-echo-csharp.md). Prima di procedere al passaggio successivo verificare che l'applicazione sul lato dispositivo sia in esecuzione.


### <a name="run-the-service-side-application"></a>Eseguire l'applicazione sul lato servizio

Supponendo che l'applicazione sul lato dispositivo sia in esecuzione, attenersi alla procedura seguente per eseguire l'applicazione sul lato servizio in Node.js:

- Fornire le credenziali del servizio e l'ID dispositivo come variabili di ambiente.
  ```
  # In Linux
  export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
  export STREAMING_TARGET_DEVICE="MyDevice"

  # In Windows
  SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
  SET STREAMING_TARGET_DEVICE=MyDevice
  ```
  Modificare `MyDevice` con l'ID dispositivo scelto per il dispositivo.

- Passare a `Quickstarts/device-streams-service` nella cartella del progetto decompressa ed eseguire l'esempio usando il nodo.
  ```
  cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
  
  # Install the preview service SDK, and other dependencies
  npm install azure-iothub@streams-preview
  npm install

  node echo.js
  ```

Alla fine dell'ultimo passaggio, il programma sul lato servizio avvierà un flusso nel dispositivo e dopo aver stabilito la comunicazione invierà un buffer di stringa al servizio tramite il flusso. In questo esempio, il programma sul lato servizio legge semplicemente i dati da stdin nel terminale e li invia al dispositivo che quindi li invierà di nuovo al servizio. Questo esempio illustra la comunicazione bidirezionale riuscita tra le due applicazioni.

Output della console sul lato servizio: ![testo alternativo](./media/quickstart-device-streams-echo-nodejs/service-console-output.PNG "Output della console sul lato servizio")


È quindi possibile terminare il programma premendo di nuovo INVIO.


## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]


## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato configurato un hub IoT, è stato registrato un dispositivo, è stato stabilito un flusso dispositivo tra le applicazioni sul lato dispositivo e sul lato servizio ed è stato usato il flusso per inviare dati tra le applicazioni.

Consultare i collegamenti seguenti per altre informazioni sui flussi dispositivo:

> [!div class="nextstepaction"]
> [Panoramica dei flussi dispositivo](./iot-hub-device-streams-overview.md)
