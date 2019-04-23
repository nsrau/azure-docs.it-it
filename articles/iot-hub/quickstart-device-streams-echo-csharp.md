---
title: Guida introduttiva per C# ai flussi dispositivo dell'hub IoT di Azure (anteprima) | Microsoft Docs
description: In questa guida introduttiva verranno eseguite due applicazioni C# di esempio che comunicano tramite un flusso dispositivo stabilito tramite l'hub IoT.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 2853bd5539a40e3b38927f619756fe37a4cec984
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006870"
---
# <a name="quickstart-communicate-to-device-applications-in-c-via-iot-hub-device-streams-preview"></a>Guida introduttiva: Comunicare con le applicazioni del dispositivo in C# tramite i flussi dispositivo dell'hub IoT (anteprima)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

L'hub IoT di Microsoft Azure supporta attualmente i flussi dispositivo come [funzionalità in anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I [flussi dispositivo dell'hub IoT](./iot-hub-device-streams-overview.md) consentono alle applicazioni del servizio e del dispositivo di comunicare in modo sicuro e di facile integrazione con i firewall. Questa guida introduttiva prevede due programmi C# che sfruttano i flussi dispositivo per inviare dati.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

L'anteprima dei flussi dispositivo è attualmente supportata solo per gli hub IoT creati nelle aree seguenti:

  - **Stati Uniti centrali**
  - **Stati Uniti centrali EUAP**

Le due applicazioni di esempio eseguite in questa guida introduttiva sono scritte in C#. È necessario .NET Core SDK 2.1.0 o versione successiva nel computer di sviluppo.

È possibile scaricare .NET Core SDK per più piattaforme da [.NET](https://www.microsoft.com/net/download/all).

È possibile verificare la versione corrente di C# installata nel computer di sviluppo tramite il comando seguente:

```
dotnet --version
```

Eseguire il comando seguente per aggiungere l'estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure all'istanza di Cloud Shell. L'estensione IoT aggiunge i comandi specifici di hub IoT, IoT Edge e servizio Device Provisioning in hub IoT all'interfaccia della riga di comando di Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Scaricare il progetto C# di esempio da https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip ed estrarre l'archivio ZIP. Servirà sia sul lato del dispositivo che sul lato del servizio.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire il comando seguente in Azure Cloud Shell per creare l'identità del dispositivo.

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

   **MyDevice**: nome specificato per il dispositivo registrato. Usare MyDevice come illustrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usare tale nome nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Eseguire il comando seguente in Azure Cloud Shell per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato.

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Annotare la stringa di connessione del dispositivo che sarà simile a quella dell'esempio seguente:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida introduttiva.

3. È necessario anche la _stringa di connessione del servizio_ dell'hub IoT per consentire all'applicazione sul lato servizio di connettersi all'hub IoT e stabilire un flusso dispositivo. Il comando seguente recupera questo valore per l'hub IoT:

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Prendere nota del valore restituito che sarà simile a quello seguente:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Stabilire la comunicazione tra un dispositivo e un servizio tramite i flussi dispositivo

### <a name="run-the-service-side-application"></a>Eseguire l'applicazione sul lato servizio

Passare a `iot-hub/Quickstarts/device-streams-echo/service` nella cartella del progetto decompressa. Sarà necessario specificare le informazioni seguenti:

| Nome parametro | Valore del parametro |
|----------------|-----------------|
| `ServiceConnectionString` | Specificare la stringa di connessione del servizio dell'hub IoT. |
| `DeviceId` | Specificare l'ID del dispositivo creato in precedenza, ad esempio MyDevice. |

Compilare ed eseguire il codice come segue:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> Se l'applicazione sul lato del dispositivo non risponde entro il tempo previsto, si verifica un timeout.

### <a name="run-the-device-side-application"></a>Eseguire l'applicazione sul lato dispositivo

Passare alla directory `iot-hub/Quickstarts/device-streams-echo/device` nella cartella del progetto decompressa. Sarà necessario specificare le informazioni seguenti:

| Nome parametro | Valore del parametro |
|----------------|-----------------|
| `DeviceConnectionString` | Specificare la stringa di connessione del dispositivo dell'hub IoT. |

Compilare ed eseguire il codice come segue:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

Alla fine dell'ultimo passaggio, il programma sul lato servizio avvierà un flusso nel dispositivo e dopo aver stabilito la comunicazione invierà un buffer di stringa al servizio tramite il flusso. In questo esempio, il programma sul lato servizio restituisce semplicemente gli stessi dati al dispositivo, dimostrando la riuscita della comunicazione bidirezionale tra le due applicazioni. Vedere la figura seguente.

Output della console sul lato dispositivo: ![testo alternativo](./media/quickstart-device-streams-echo-csharp/device-console-output.png "Output della console sul lato dispositivo")

Output della console sul lato servizio: ![testo alternativo](./media/quickstart-device-streams-echo-csharp/service-console-output.png "Output della console sul lato servizio")

Il traffico che viene inviato tramite il flusso sarà forzato mediante il tunnel attraverso l'hub IoT anziché essere inviato direttamente, ottenendo in tal modo [questi vantaggi](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato configurato un hub IoT, è stato registrato un dispositivo, è stato stabilito un flusso dispositivo tra le applicazioni C# sul lato dispositivo e sul lato servizio ed è stato usato il flusso per inviare dati tra le applicazioni.

Consultare i collegamenti seguenti per altre informazioni sui flussi dispositivo:

> [!div class="nextstepaction"]
> [Panoramica dei flussi dispositivo](./iot-hub-device-streams-overview.md)
