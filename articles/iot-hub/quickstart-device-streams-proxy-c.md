---
title: Guida introduttiva per C ai flussi dispositivo dell'hub IoT di Azure per SSH/RDP (anteprima) | Microsoft Docs
description: In questa guida introduttiva verrà eseguita un'applicazione C di esempio che funge da proxy per consentire scenari SSH/RDP su flussi dispositivo dell'hub IoT.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 1468268e407eeac6196c8e8e4db0fc5a52ca09c7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59501570"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-c-proxy-application-preview"></a>Avvio rapido: SSH/RDP su flussi dispositivo dell'hub IoT con un'applicazione proxy C (anteprima)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

L'hub IoT di Microsoft Azure attualmente supporta i flussi dispositivo come [funzionalità di anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I [flussi dispositivo dell'hub IoT](./iot-hub-device-streams-overview.md) consentono alle applicazioni del servizio e del dispositivo di comunicare in modo sicuro e di facile integrazione con i firewall. Vedere [questa pagina](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) per una panoramica della configurazione.

Questo documento descrive la configurazione per il tunneling del traffico SSH (con la porta 22) tramite i flussi dispositivo. La configurazione per il traffico RDP è simile e richiede una semplice modifica della configurazione. Poiché i flussi dispositivo sono indipendenti dalle applicazioni e dai protocolli, la presente guida introduttiva può essere modificata (cambiando le porte di comunicazione) per adattarsi ad altri tipi di traffico delle applicazioni.

## <a name="how-it-works"></a>Funzionamento

La figura seguente illustra la configurazione del modo in cui i programmi proxy locali del dispositivo e del servizio consentiranno la connettività end-to-end tra i processi del client SSH e del daemon SSH. Durante l'anteprima pubblica, l'SDK C supporta solo i flussi dispositivo sul lato dispositivo. Di conseguenza, questa guida introduttiva illustra solo le istruzioni per eseguire l'applicazione proxy locale del dispositivo. È consigliabile eseguire un'applicazione proxy locale del servizio associata disponibile nella [guida introduttiva per C#](./quickstart-device-streams-proxy-csharp.md) o nella [guida introduttiva per Node.js](./quickstart-device-streams-proxy-nodejs.md).

![Testo alternativo](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg "Configurazione di un proxy locale")

1. Il proxy locale del servizio si connette all'hub IoT e avvia un flusso dispositivo nel dispositivo di destinazione.

2. Il proxy locale del dispositivo completa l'handshake di avvio del flusso e stabilisce un tunnel di streaming end-to-end tramite l'endpoint di streaming dell'hub IoT sul lato servizio.

3. Il proxy locale del dispositivo si connette al daemon SSH (SSHD) in ascolto sulla porta 22 del dispositivo (configurabile come descritto [di seguito](#run-the device-local-proxy-application)).

4. Il proxy locale del servizio resta in attesa delle nuove connessioni SSH da parte dell'utente in ascolto su una porta designata che in questo caso è la porta 2222 (anche questa configurabile come descritto di [seguito](#run-the-device-local-proxy-application)). Quando l'utente si connette tramite il client SSH, il tunnel consente di trasferire il traffico dell'applicazione SSH tra i programmi client e server SSH.

> [!NOTE]
> Il traffico SSH che viene inviato tramite un flusso dispositivo sarà forzato mediante il tunnel attraverso l'endpoint di streaming dell'hub IoT anziché essere inviato direttamente tra il servizio e il dispositivo, ottenendo in tal modo [questi vantaggi](./iot-hub-device-streams-overview.md#benefits). Inoltre, la figura illustra il daemon SSH in esecuzione nello stesso dispositivo (o computer) del proxy locale del dispositivo. Se in questa guida introduttiva si fornisce l'indirizzo IP del daemon SSH, sarà possibile eseguire il proxy locale del dispositivo e il daemon anche in computer diversi.

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

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa sezione si registrerà un dispositivo simulato usando Azure Cloud Shell con l'[estensione IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest).

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

## <a name="ssh-to-a-device-via-device-streams"></a>Connessione SSH a un dispositivo tramite i flussi dispositivo

### <a name="run-the-device-local-proxy-application"></a>Eseguire l'applicazione proxy locale del dispositivo

1. Modificare il file di origine `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample/iothub_client_c2d_streaming_proxy_sample.c` e specificare la stringa di connessione del dispositivo, l'IP/nome host del dispositivo di destinazione e la porta SSH 22:

   ```C
   /* Paste in the your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

2. Compilare l'esempio:

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

3. Eseguire il programma compilato nel dispositivo:

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

Come descritto [in precedenza](#how-it-works), per stabilire un flusso end-to-end per effettuare il tunneling del traffico SSH, è necessario un proxy locale a ogni estremità, ovvero nel servizio e nel dispositivo. Durante l'anteprima pubblica, l'SDK C dell'hub IoT supporta solo i flussi dispositivo sul lato dispositivo. Per compilare ed eseguire il proxy locale del servizio, seguire la procedura disponibile nell'[argomento di avvio rapido per C# ](./quickstart-device-streams-proxy-csharp.md) o nell'[argomento di avvio rapido per Node. js](./quickstart-device-streams-proxy-nodejs.md).

### <a name="establish-an-ssh-session"></a>Stabilire una sessione SSH

Dopo che sono in esecuzione i proxy locali sia del dispositivo che del servizio, usare il programma client SSH e connettersi al proxy locale del servizio sulla porta 2222 (invece di usare direttamente il daemon SSH).

```cmd/sh
ssh <username>@localhost -p 2222
```

A questo punto, verrà visualizzato il prompt di accesso SSH per immettere le credenziali.

Output della console nel proxy locale del dispositivo che si connette al daemon SSH con `IP_address:22`: ![Testo alternativo](./media/quickstart-device-streams-proxy-c/device-console-output.PNG "Output del proxy locale del dispositivo")

Output della console del programma client SSH (il client SSH comunica con il daemon SSH tramite la connessione alla porta 22, su cui è in ascolto il proxy locale del servizio): ![Testo alternativo](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png "Output del client SSH")

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato configurato un hub IoT, è stato registrato un dispositivo, è stato distribuito un programma proxy locale del dispositivo e del servizio per stabilire un flusso dispositivo tramite l'hub IoT e sono stati usati i proxy per effettuare il tunneling del traffico SSH.

Consultare i collegamenti seguenti per altre informazioni sui flussi dispositivo:

> [!div class="nextstepaction"]
> [Panoramica dei flussi dispositivo](./iot-hub-device-streams-overview.md)
