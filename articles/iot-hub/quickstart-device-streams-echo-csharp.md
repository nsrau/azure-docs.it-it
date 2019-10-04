---
title: Comunicare con un'applicazione del dispositivo in C# tramite i flussi dispositivo dell'hub IoT (anteprima) | Microsoft Docs
description: In questo argomento di avvio rapido verranno eseguite due applicazioni C# di esempio che comunicano tramite un flusso del dispositivo stabilito tramite l'hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: de581362371e28523c99f961dfdb5c2009901343
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446116"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Guida introduttiva: Comunicare con un'applicazione del dispositivo in C# tramite i flussi dispositivo dell'hub IoT (anteprima)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

L'hub IoT di Azure supporta attualmente i flussi del dispositivo come [funzionalità in anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I [flussi dispositivo dell'hub IoT](./iot-hub-device-streams-overview.md) consentono alle applicazioni del servizio e del dispositivo di comunicare in modo sicuro e di facile integrazione con i firewall. Questo argomento di avvio rapido prevede due programmi C# che sfruttano i flussi del dispositivo per inviare dati nelle due direzioni (eco).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* L'anteprima dei flussi del dispositivo è attualmente supportata solo per gli hub IoT creati nelle aree seguenti:
  * Stati Uniti centrali
  * Stati Uniti centrali EUAP

* Le due applicazioni di esempio eseguite in questo argomento sono scritte in C#. È necessario .NET Core SDK 2.1.0 o versione successiva nel computer di sviluppo.
  * Scaricare [.NET Core SDK per più piattaforme da .NET](https://www.microsoft.com/net/download/all).
  * Verificare la versione corrente di C# installata nel computer di sviluppo tramite il comando seguente:

   ```
   dotnet --version
   ```

* Aggiungere l'estensione Azure IoT per l'interfaccia della riga di comando di Azure all'istanza di Cloud Shell eseguendo il comando seguente. L'estensione IoT aggiunge i comandi specifici di hub IoT, IoT Edge e servizio Device Provisioning in hub IoT all'interfaccia della riga di comando di Azure.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* [Scaricare il progetto C# di esempio](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) ed estrarre l'archivio ZIP. È necessario averlo sia sul lato dispositivo che sul lato servizio.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa sezione si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Per creare l'identità del dispositivo, eseguire il comando seguente in Cloud Shell:

   > [!NOTE]
   > * Sostituire il segnaposto *YourIoTHubName* con il nome scelto per l'hub IoT.
   > * Usare *MyDevice* come illustrato. Si tratta del nome specificato per il dispositivo registrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usarlo nell'intero articolo e aggiornarlo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Per ottenere la *stringa di connessione* per il dispositivo appena registrato, eseguire il comando seguente in Cloud Shell:

   > [!NOTE]
   > Sostituire il segnaposto *YourIoTHubName* con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Prendere nota della stringa di connessione del dispositivo per usarla in seguito nella guida. Sarà simile a quanto indicato nell'esempio seguente:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. È necessario anche la *stringa di connessione del servizio* dell'hub IoT per consentire all'applicazione sul lato servizio di connettersi all'hub IoT e stabilire un flusso dispositivo. Il comando seguente recupera questo valore per l'hub IoT:

   > [!NOTE]
   > Sostituire il segnaposto *YourIoTHubName* con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Prendere nota del valore restituirlo per usarlo in seguito nella guida. Sarà simile a quanto indicato nell'esempio seguente:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Stabilire la comunicazione tra il dispositivo e il servizio tramite i flussi del dispositivo

In questa sezione verranno eseguite sia l'applicazione sul lato dispositivo sia l'applicazione sul lato servizio con le relative comunicazioni.

### <a name="run-the-service-side-application"></a>Eseguire l'applicazione sul lato servizio

Passare alla directory *iot-hub/Quickstarts/device-streams-echo/service* nella cartella del progetto decompresso. Tenere a portata di mano le informazioni seguenti:

| Nome parametro | Valore del parametro |
|----------------|-----------------|
| `ServiceConnectionString` | Specificare la stringa di connessione del servizio dell'hub IoT. |
| `DeviceId` | Specificare l'ID del dispositivo creato in precedenza, ad esempio *MyDevice*. |

Compilare ed eseguire il codice come segue:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> Se l'applicazione sul lato del dispositivo non risponde entro il tempo previsto, si verifica un timeout.

### <a name="run-the-device-side-application"></a>Eseguire l'applicazione sul lato dispositivo

Passare alla directory *iot-hub/Quickstarts/device-streams-echo/device* nella cartella del progetto decompresso. Tenere a portata di mano le informazioni seguenti:

| Nome parametro | Valore del parametro |
|----------------|-----------------|
| `DeviceConnectionString` | Specificare la stringa di connessione del dispositivo dell'hub IoT. |

Compilare ed eseguire il codice come segue:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

Alla fine dell'ultimo passaggio, l'applicazione sul lato servizio avvia un flusso verso il dispositivo. Una volta stabilito il flusso, l'applicazione lo usa per inviare un buffer di stringa al servizio. In questo esempio l'applicazione sul lato servizio restituisce semplicemente gli stessi dati al dispositivo, dimostrando la riuscita della comunicazione bidirezionale tra le due applicazioni.

Output della console sul lato dispositivo:

![Output della console sul lato dispositivo](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Output della console sul lato servizio:

![Output della console sul lato servizio](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Il traffico che viene inviato tramite il flusso sarà sottoposto a tunneling attraverso l'hub IoT invece di essere inviato direttamente. I vantaggi offerti sono descritti in [Vantaggi dei flussi dispositivo](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato configurato un hub IoT, è stato registrato un dispositivo ed è stato stabilito un flusso del dispositivo tra le applicazioni C# sui lati dispositivo e servizio, che è stato quindi usato per inviare e ricevere dati tra le due.

Per altre informazioni sui flussi del dispositivo, vedere:

> [!div class="nextstepaction"]
> [Panoramica dei flussi dispositivo](./iot-hub-device-streams-overview.md)
