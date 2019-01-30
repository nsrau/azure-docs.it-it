---
title: Guida introduttiva per Node.js ai flussi dispositivo dell'hub IoT di Azure per SSH/RDP (anteprima) | Microsoft Docs
description: In questa guida introduttiva verrà eseguita un'applicazione Node.js di esempio che funge da proxy per consentire scenari SSH/RDP su flussi dispositivo dell'hub IoT.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 012fdfa4faf10cacaf85819517f358c1af1ab39d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830708"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-nodejs-proxy-application-preview"></a>Guida introduttiva: SSH/RDP su flussi dispositivo dell'hub IoT con un'applicazione proxy Node.js (anteprima)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

I [flussi dispositivo dell'hub IoT](./iot-hub-device-streams-overview.md) consentono alle applicazioni del servizio e del dispositivo di comunicare in modo sicuro e di facile integrazione con i firewall. Questa guida introduttiva descrive l'esecuzione di un'applicazione proxy Node.js in esecuzione sul lato servizio per consentire l'invio del traffico RDP e SSH al dispositivo tramite un flusso dispositivo. Vedere [questa pagina](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) per una panoramica della configurazione. Durante l'anteprima pubblica, l'SDK Node.js supporta solo i flussi dispositivo sul lato servizio. Di conseguenza, questa guida introduttiva illustra solo le istruzioni per eseguire il proxy sul lato servizio. È consigliabile eseguire un proxy sul dispositivo associato disponibile nella [guida introduttiva per C](./quickstart-device-streams-proxy-c.md) o nella [guida introduttiva per C#](./quickstart-device-streams-proxy-csharp.md).

Per prima cosa viene descritta la configurazione per SSH (con la porta `22`). Verrà quindi illustrato come modificare la configurazione per RDP (che usa la porta 3389). Poiché i flussi dispositivo sono indipendenti dalle applicazioni e dai protocolli, lo stesso esempio può essere modificato (cambiando in genere le porte di comunicazione) per adattarsi ad altri tipi di traffico delle applicazioni.

Il codice illustra l'avvio e l'uso di un flusso dispositivo e può essere riconfigurato anche per altri tipi di traffico delle applicazioni (oltre a RDP e SSH).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a name="prerequisites"></a>Prerequisiti

Per eseguire l'applicazione sul lato servizio in questa guida introduttiva è necessario Node.js versione 4.x.x o versione successiva nel computer di sviluppo.

È possibile scaricare Node.js per più piattaforme da [nodejs.org](https://nodejs.org).

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
    az iot hub show-connection-string --policy-name service --hub-name YourIoTHubName
    ```

    Prendere nota del valore restituito che sarà simile a quello seguente:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`


## <a name="ssh-to-a-device-via-device-streams"></a>Connessione SSH a un dispositivo tramite i flussi dispositivo

### <a name="run-the-device-side-proxy"></a>Eseguire il proxy sul lato dispositivo

Come indicato in precedenza, l'SDK Node.js dell'hub IoT supporta solo i flussi dispositivo sul lato servizio. Per l'applicazione sul lato dispositivo usare i programmi proxy del dispositivo associati disponibili nella [guida introduttiva per C](./quickstart-device-streams-proxy-c.md) o nella [guida introduttiva per C#](./quickstart-device-streams-proxy-csharp.md). Prima di procedere al passaggio successivo verificare che il proxy sul lato dispositivo sia in esecuzione.


### <a name="run-the-service-side-proxy"></a>Eseguire il proxy sul lato servizio

Supponendo che il proxy sul lato dispositivo sia in esecuzione, attenersi alla procedura seguente per eseguire il proxy sul lato servizio scritto in Node.js:

- Fornire le credenziali del servizio, l'ID dispositivo di destinazione in cui viene eseguito il daemon SSH e il numero di porta per il proxy in esecuzione nel dispositivo come variabili di ambiente.
```
  # In Linux
  export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
  export STREAMING_TARGET_DEVICE="MyDevice"
  export PROXY_PORT=2222

  # In Windows
  SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
  SET STREAMING_TARGET_DEVICE=MyDevice
  SET PROXY_PORT=2222
```
Modificare `MyDevice` con l'ID dispositivo scelto per il dispositivo.

- Passare a `Quickstarts/device-streams-service` nella cartella del progetto decompressa ed eseguire il proxy locale del servizio.
```
  cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

  # Install the preview service SDK, and other dependencies
  npm install azure-iothub@streams-preview
  npm install

  # Run the service-local proxy application
  node proxy.js
```

### <a name="ssh-to-your-device-via-device-streams"></a>Connessione SSH a un dispositivo tramite i flussi dispositivo
In Linux eseguire SSH usando `ssh $USER@localhost -p 2222` in un terminale. In Windows usare il client SSH preferito, ad esempio PuTTY.

Output della console sul lato servizio dopo aver stabilito una sessione SSH (il proxy locale del servizio è in ascolto sulla porta 2222): ![Testo alternativo](./media/quickstart-device-streams-proxy-nodejs/service-console-output.PNG "Output terminale SSH")


Output della console del programma client SSH (il client SSH comunica con il daemon SSH tramite la connessione alla porta <code>22</code> dove è in ascolto il proxy locale del servizio): ![Testo alternativo](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.PNG "Output del client SSH")


### <a name="rdp-to-your-device-via-device-streams"></a>Connessione RDP a un dispositivo tramite i flussi dispositivo

Usare ora il programma client RDP e connettersi al proxy del servizio sulla porta 2222 (una porta arbitraria disponibile scelta in precedenza).

> [!NOTE]
> Assicurarsi che il proxy del dispositivo sia configurato correttamente per RDP e che sia configurato con la porta RDP 3389.

![Testo alternativo](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.PNG "Il client RDP si connette al proxy locale del servizio.")


## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato configurato un hub IoT, è stato registrato un dispositivo ed è stato distribuito un programma proxy del servizio per abilitare RDP e SSH in un dispositivo IoT. Il traffico RDP e SSH viene forzato mediante il tunnel attraverso un flusso dispositivo tramite l'hub IoT. In questo modo si elimina la necessità della connettività diretta al dispositivo.

Consultare i collegamenti seguenti per altre informazioni sui flussi dispositivo:

> [!div class="nextstepaction"]
> [Panoramica dei flussi dispositivo](./iot-hub-device-streams-overview.md)
