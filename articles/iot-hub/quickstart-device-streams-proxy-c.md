---
title: Avvio rapido per C ai flussi del dispositivo dell'hub IoT di Azure per SSH e RDP (anteprima) | Microsoft Docs
description: In questo argomento di avvio rapido verrà eseguita un'applicazione C di esempio che funge da proxy per consentire scenari SSH e RDP su flussi del dispositivo dell'hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: b958711c498f0826f2a48d92d4892eb43ec8d18a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446087"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Guida introduttiva: Abilitare SSH e RDP su un flusso del dispositivo dell'hub IoT con un'applicazione proxy C (anteprima)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

L'hub IoT di Azure supporta attualmente i flussi del dispositivo come [funzionalità in anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I [flussi dispositivo dell'hub IoT](./iot-hub-device-streams-overview.md) consentono alle applicazioni del servizio e del dispositivo di comunicare in modo sicuro e di facile integrazione con i firewall. Per una panoramica della configurazione, vedere la pagina dell'[esempio di proxy locale](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Questo argomento di avvio rapido descrive la configurazione per il tunneling del traffico SSH (Secure Shell) (porta 22) tramite i flussi del dispositivo. La configurazione per il traffico RDP (Remote Desktop Protocol) è simile e richiede una semplice modifica della configurazione. Poiché i flussi del dispositivo sono indipendenti da applicazioni e protocolli, lo stesso esempio può essere modificato e quindi adattato ad altri tipi di traffico delle applicazioni.

## <a name="how-it-works"></a>Funzionamento

La figura seguente illustra il modo in cui i programmi proxy locali del dispositivo e del servizio consentono la connettività end-to-end tra i processi del client SSH e del daemon SSH. Durante l'anteprima pubblica, l'SDK C supporta solo i flussi del dispositivo sul lato dispositivo. Di conseguenza, questo argomento di avvio rapido include le istruzioni per eseguire solo l'applicazione proxy locale del dispositivo. Eseguire una delle guide di avvio rapido lato servizio seguenti:

* [SSH/RDP su flussi dispositivo dell'hub IoT con proxy C#](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP su flussi dispositivo dell'hub IoT con proxy Node.js](./quickstart-device-streams-proxy-nodejs.md)

![Configurazione di un proxy locale](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. Il proxy locale del servizio si connette all'hub IoT e avvia un flusso del dispositivo nel dispositivo di destinazione.

2. Il proxy locale del dispositivo completa l'handshake di avvio del flusso e stabilisce un tunnel di streaming end-to-end tramite l'endpoint di streaming dell'hub IoT sul lato servizio.

3. Il proxy locale del dispositivo si connette al daemon SSH in ascolto sulla porta 22 nel dispositivo. Questa impostazione è configurabile, come descritto nella sezione "Eseguire l'applicazione proxy locale del dispositivo".

4. Il proxy locale del servizio attende nuove connessioni SSH da parte dell'utente restando in ascolto su una porta designata, che in questo caso è la porta 2222. Questa impostazione è configurabile, come descritto nella sezione "Eseguire l'applicazione proxy locale del dispositivo". Quando l'utente si connette tramite il client SSH, il tunnel consente di trasferire il traffico dell'applicazione SSH tra i programmi client e server SSH.

> [!NOTE]
> Il traffico SSH che viene inviato tramite un flusso del dispositivo sarà sottoposto a tunneling attraverso l'endpoint di streaming dell'hub IoT invece di essere inviato direttamente tra il servizio e il dispositivo. Per altre informazioni, vedere la sezione sui [vantaggi dell'uso di flussi del dispositivo dell'hub IoT](iot-hub-device-streams-overview.md#benefits). Inoltre, la figura illustra il daemon SSH in esecuzione nello stesso dispositivo (o computer) del proxy locale del dispositivo. Se in questo argomento di avvio rapido si fornisce l'indirizzo IP del daemon SSH, sarà possibile eseguire il proxy locale del dispositivo e il daemon anche in computer diversi.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* L'anteprima dei flussi del dispositivo è attualmente supportata solo per gli hub IoT creati nelle aree seguenti:

  * Stati Uniti centrali
  * Stati Uniti centrali EUAP

* Installare [Visual Studio 2019](https://www.visualstudio.com/vs/) con il carico di lavoro [Sviluppo di applicazioni desktop con C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) abilitato.
* Installare la versione più recente di [Git](https://git-scm.com/download/).

* Eseguire questo comando per aggiungere l'estensione Azure IoT per l'interfaccia della riga di comando di Azure all'istanza di Cloud Shell. L'estensione IoT aggiunge i comandi specifici di hub IoT, IoT Edge e servizio Device Provisioning in hub IoT all'interfaccia della riga di comando di Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

Per questo argomento di avvio rapido si userà [Azure IoT SDK per dispositivi per C](iot-hub-device-sdk-c-intro.md). Si preparerà un ambiente di sviluppo usato per clonare e creare [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) da GitHub. L'SDK in GitHub include il codice di esempio usato in questo argomento.

1. Scaricare il [sistema di compilazione CMake](https://cmake.org/download/).

    È importante che i prerequisiti di Visual Studio (Visual Studio e il carico di lavoro *Sviluppo di applicazioni desktop con C++* ) siano installati nel computer *prima* di avviare l'installazione di CMake. Dopo aver soddisfatto i prerequisiti e verificato il download, è possibile installare il sistema di compilazione CMake.

1. Aprire un prompt dei comandi o la shell Git Bash. Eseguire il comando seguente per clonare il repository GitHub [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Il completamento di questa operazione richiederà alcuni minuti.

1. Creare una sottodirectory *cmake* nella directory radice del repository Git, come illustrato nel comando seguente, e passare a tale cartella.

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Eseguire i comandi seguenti dalla directory *cmake* per creare una versione dell'SDK specifica per la piattaforma client di sviluppo.

   * Linux:

      ```bash
      cmake ..
      make -j
      ```

   * In Windows eseguire questi comandi al prompt dei comandi per gli sviluppatori per Visual Studio 2015 o 2017. Verrà generata una soluzione di Visual Studio per il dispositivo simulato nella directory *cmake*.

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

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa sezione si usa Azure Cloud Shell con l'[estensione IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) per registrare un dispositivo simulato.

1. Per creare l'identità del dispositivo, eseguire il comando seguente in Cloud Shell:

   > [!NOTE]
   > * Sostituire il segnaposto *YourIoTHubName* con il nome scelto per l'hub IoT.
   > * Usare *MyDevice* come illustrato. Si tratta del nome specificato per il dispositivo registrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usarlo nell'intero articolo e aggiornarlo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Eseguire i comandi seguenti in Cloud Shell per ottenere la *stringa di connessione* per il dispositivo appena registrato:

   > [!NOTE]
   > Sostituire il segnaposto *YourIoTHubName* con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Prendere nota della stringa di connessione del dispositivo per usarla in seguito nella guida. Sarà simile a quanto indicato nell'esempio seguente:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>Connessione SSH a un dispositivo tramite i flussi dispositivo

In questa sezione si stabilisce un flusso end-to-end per il tunneling del traffico SSH.

### <a name="run-the-device-local-proxy-application"></a>Eseguire l'applicazione proxy locale del dispositivo

1. Modificare il file di origine *iothub_client_c2d_streaming_sample.c* nella cartella *iothub_client/samples/iothub_client_c2d_streaming_sample* e specificare la stringa di connessione del dispositivo, l'indirizzo IP/nome host del dispositivo di destinazione e la porta SSH 22:

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Compilare l'esempio:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Eseguire il programma compilato nel dispositivo:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>Eseguire l'applicazione proxy locale del servizio

Come descritto nella sezione "Funzionamento", per stabilire un flusso end-to-end per il tunneling del traffico SSH, è necessario un proxy locale a ogni estremità, ovvero sui lati servizio e dispositivo. Durante l'anteprima pubblica, l'SDK C dell'hub IoT supporta solo i flussi del dispositivo sul lato dispositivo. Per compilare ed eseguire il proxy locale del servizio, seguire le istruzioni disponibili in uno degli argomenti di avvio rapido seguenti:

   * [SSH/RDP su flussi dispositivo dell'hub IoT con applicazioni proxy C#](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP su flussi del dispositivo dell'hub IoT con applicazioni proxy Node.js](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Stabilire una sessione SSH

Dopo che sono in esecuzione i proxy locali sia del dispositivo che del servizio, usare il programma client SSH e connettersi al proxy locale del servizio sulla porta 2222 (invece di usare direttamente il daemon SSH).

```cmd/sh
ssh <username>@localhost -p 2222
```

A questo punto, la finestra di accesso SSH richiede di immettere le credenziali.

L'immagine seguente mostra l'output della console nel proxy locale del dispositivo che si connette al daemon SSH all'indirizzo `IP_address:22`:

![Output del proxy locale del servizio](./media/quickstart-device-streams-proxy-c/device-console-output.png)

L'immagine seguente mostra l'output della console del programma client SSH. Il client SSH comunica con il daemon SSH tramite connessione alla porta 22, su cui è in ascolto il proxy locale del servizio:

![Output del client SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato configurato un hub IoT, è stato registrato un dispositivo, sono stati distribuiti i programmi proxy locali del dispositivo e del servizio per stabilire un flusso del dispositivo tramite l'hub IoT, quindi tali proxy sono stati usati per effettuare il tunneling del traffico SSH.

Per altre informazioni sui flussi del dispositivo, vedere:

> [!div class="nextstepaction"]
> [Panoramica dei flussi dispositivo](./iot-hub-device-streams-overview.md)
