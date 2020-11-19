---
title: Comunicare con un'app del dispositivo in Node.js tramite i flussi del dispositivo dell'hub IoT di Azure
description: In questo argomento di avvio rapido verrà eseguita un'applicazione sul lato servizio in Node.js che comunica con un dispositivo IoT tramite un flusso dispositivo.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 678955970f3eeb87a10c43cd43effc3464db7794
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832008"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Guida introduttiva: Comunicare con un'applicazione del dispositivo in Node.js tramite i flussi dispositivo dell'hub IoT (anteprima)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

In questa guida di avvio rapido si esegue un'applicazione sul lato servizio e si configura la comunicazione tra un dispositivo e il servizio usando flussi del dispositivo. I flussi del dispositivo dell'hub IoT di Azure consentono alle applicazioni di servizio e del dispositivo di comunicare in modo sicuro e adatto al firewall. Durante l'anteprima pubblica, l'SDK Node.js supporta solo flussi del dispositivo sul lato servizio. Di conseguenza, questa guida introduttiva illustra solo le istruzioni per eseguire l'applicazione sul lato servizio.

## <a name="prerequisites"></a>Prerequisiti

* Completamento di [Comunicare con app del dispositivo in C tramite i flussi del dispositivo dell'hub IoT](./quickstart-device-streams-echo-c.md) o [Comunicare con app del dispositivo in C# tramite i flussi del dispositivo dell'hub IoT](./quickstart-device-streams-echo-csharp.md).

* Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10+](https://nodejs.org).

    È possibile verificare la versione corrente di Node.js installata nel computer di sviluppo tramite il comando seguente:

    ```cmd/sh
    node --version
    ```

* [Un progetto Node.js di esempio](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

L'hub IoT di Microsoft Azure supporta attualmente i flussi dispositivo come [funzionalità in anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> L'anteprima dei flussi dispositivo è attualmente supportata solo per gli hub IoT creati nelle aree seguenti:
>
> * Stati Uniti centrali
> * Stati Uniti centrali EUAP
> * Europa settentrionale
> * Asia sud-orientale

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Se è stata completata la precedente [Guida introduttiva: Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md), ignorare questo passaggio.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

Se è stata completata la precedente [Guida introduttiva: Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md), ignorare questo passaggio.

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire questo comando in Azure Cloud Shell per creare l'identità del dispositivo.

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

   **MyDevice**: nome del dispositivo da registrare. È consigliabile usare **MyDevice**, come illustrato. Se si sceglie un altro nome per il dispositivo, è necessario usare tale nome anche nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. È necessaria anche una *stringa di connessione del servizio* per consentire all'applicazione back-end di connettersi all'hub IoT dell'utente e recuperare i messaggi. Il comando seguente recupera la stringa di connessione del servizio per l'hub IoT:

    **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Prendere nota della stringa di connessione del servizio restituita per usarla in seguito in questo argomento di avvio rapido. Sarà simile a quanto indicato nell'esempio seguente:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Stabilire la comunicazione tra un dispositivo e un servizio tramite i flussi dispositivo

In questa sezione verranno eseguite sia l'applicazione sul lato dispositivo sia l'applicazione sul lato servizio con le relative comunicazioni.

### <a name="run-the-device-side-application"></a>Eseguire l'applicazione sul lato dispositivo

Come indicato in precedenza, l'SDK Node.js dell'hub IoT supporta solo i flussi dispositivo sul lato servizio. Per l'applicazione sul lato dispositivo usare uno dei programmi del dispositivo associati disponibili in questi argomenti di avvio rapido:

* [Comunicare con le applicazioni del dispositivo in C tramite i flussi dispositivo dell'hub IoT](./quickstart-device-streams-echo-c.md)

* [Comunicare con le applicazioni del dispositivo in C# tramite i flussi dispositivo dell'hub IoT](./quickstart-device-streams-echo-csharp.md)

Prima di procedere al passaggio successivo verificare che l'applicazione sul lato dispositivo sia in esecuzione.

### <a name="run-the-service-side-application"></a>Eseguire l'applicazione sul lato servizio

L'applicazione Node.js sul lato servizio di questa guida introduttiva presenta le funzionalità seguenti:

* Crea un flusso dispositivo in un dispositivo IoT.
* Legge l'input dalla riga di comando e lo invia all'applicazione del dispositivo che lo invia di nuovo all'applicazione sul lato servizio.

Il codice illustrerà il processo di avvio di un flusso dispositivo, nonché come usarlo per inviare e ricevere dati.

Supponendo che l'applicazione sul lato dispositivo sia in esecuzione, attenersi alla procedura seguente in una finestra terminale locale per eseguire l'applicazione sul lato servizio in Node.js:

* Fornire le credenziali del servizio e l'ID dispositivo come variabili di ambiente.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Modificare il segnaposto ServiceConnectionString in modo che corrisponda alla stringa di connessione del servizio e **MyDevice** in modo che corrisponda all'ID dispositivo se si è assegnato un nome diverso.

* Passare a `Quickstarts/device-streams-service` nella cartella del progetto decompressa ed eseguire l'esempio usando il nodo.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

Alla fine dell'ultimo passaggio, il programma sul lato servizio avvierà un flusso nel dispositivo e dopo aver stabilito la comunicazione invierà un buffer di stringa al servizio tramite il flusso. In questo esempio il programma sul lato servizio legge semplicemente i dati `stdin` nel terminale e li invia al dispositivo che quindi li invierà di nuovo al servizio. Questo esempio illustra la comunicazione bidirezionale riuscita tra le due applicazioni.

![Output della console sul lato del servizio](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

È quindi possibile terminare il programma premendo di nuovo INVIO.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato configurato un hub IoT, è stato registrato un dispositivo, è stato stabilito un flusso dispositivo tra le applicazioni sul lato dispositivo e sul lato servizio ed è stato usato il flusso per inviare dati tra le applicazioni.

Consultare i collegamenti seguenti per altre informazioni sui flussi dispositivo:

> [!div class="nextstepaction"]
> [Panoramica dei flussi dispositivo](./iot-hub-device-streams-overview.md) 
