---
title: Configurare i dispositivi per i proxy di rete - Azure IoT Edge | Microsoft Docs
description: Come configurare il runtime Azure IoT Edge e tutti i moduli IoT Edge con connessione Internet per comunicare tramite un server proxy.
author: kgremban
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9a4ed17ceddf01ec628d80dc3ba9f4d7ee305f3b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457161"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Configurare un dispositivo IoT Edge per comunicare tramite un server proxy

I dispositivi IoT Edge inviano le richieste HTTPS per comunicare con l'hub IoT. Se il dispositivo è connesso a una rete che usa un server proxy, è necessario configurare il runtime IoT Edge per comunicare tramite il server. I server proxy possono influire anche sui singoli moduli IoT Edge se vengono effettuate richieste HTTP o HTTPS che non vengono indirizzate tramite l'hub di IoT Edge. 

This article walks through the following four steps to configure and then manage an IoT Edge device behind a proxy server: 

1. **Install the IoT Edge runtime on your device.**

   The IoT Edge installation scripts pull packages and files from the internet, so your device needs to communicate through the proxy server to make those requests. For detailed steps, see the [Install the runtime through a proxy](#install-the-runtime-through-a-proxy) section of this article. For Windows devices, the installation script also provides an [Offline installation](how-to-install-iot-edge-windows.md#offline-installation) option. 

   This step is a one-time process performed on the IoT Edge device when you first set it up. The same connections are also required when you update the IoT Edge runtime. 

2. **Configure the Docker daemon and the IoT Edge daemon on your device.**

   IoT Edge uses two daemons on the device, both of which need to make web requests through the proxy server. The IoT Edge daemon is responsible for communications with IoT Hub. The Moby daemon is responsible for container management, so communicates with container registries. For detailed steps, see the [Configure the daemons](#configure-the-daemons) section of this article. 

   This step is a one-time process performed on the IoT Edge device when you first set it up.

3. **Configure the IoT Edge agent properties in the config.yaml file on your device.**

   The IoT Edge daemon starts the edgeAgent module initially, but then the edgeAgent module is responsible for retrieving the deployment manifest from IoT Hub and starting all the other modules. For the IoT Edge agent to make the initial connection to IoT Hub, configure the edgeAgent module environment variables manually on the device itself. After the initial connection, you can configure the edgeAgent module remotely. For detailed steps, see the [Configure the IoT Edge agent](#configure-the-iot-edge-agent) section of this article.

   This step is a one-time process performed on the IoT Edge device when you first set it up.

4. **For all future module deployments, set environment variables for any module communicating through the proxy.**

   Once your IoT Edge device is set up and connected to IoT Hub through the proxy server, you need to maintain the connection in all future module deployments. For detailed steps, see the [Configure deployment manifests](#configure-deployment-manifests) section of this article. 

   This step is an ongoing process performed remotely so that every new module or deployment update maintains the device's ability to communicate through the proxy server. 

## <a name="know-your-proxy-url"></a>Conoscere l'URL proxy

Before you begin any of the steps in this article, you need to know your proxy URL.

Gli URL proxy hanno il formato seguente: **protocollo**://**host_proxy**:**porta_proxy**.

* Il **protocollo** è HTTP o HTTPS. The Docker daemon can use either protocol, depending on your container registry settings, but the IoT Edge daemon and runtime containers should always use HTTP to connect to the proxy.

* L'**host_proxy** è un indirizzo per il server proxy. If your proxy server requires authentication, you can provide your credentials as part of the proxy host with the following format: **user**:**password**\@**proxy_host**.

* La **porta_proxy** è la porta di rete a cui il proxy risponde al traffico di rete.

## <a name="install-the-runtime-through-a-proxy"></a>Install the runtime through a proxy

Whether your IoT Edge device runs on Windows or Linux, you need to access the installation packages through the proxy server. Depending on your operating system, follow the steps to install the IoT Edge runtime through a proxy server. 

### <a name="linux"></a>Linux

Se si installa il runtime IoT Edge in un dispositivo Linux, configurare la gestione di pacchetti per il passaggio attraverso il server proxy per accedere al pacchetto di installazione. Ad esempio, [Configurare apt-get per usare un proxy http](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Once your package manager is configured, follow the instructions in [Install Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md) as usual.

### <a name="windows"></a>Windows

If you're installing the IoT Edge runtime on a Windows device, you need to go through the proxy server twice. The first connection downloads the installer script file, and the second connection is during the installation to download the necessary components. You can configure proxy information in Windows settings, or include your proxy information directly in the PowerShell commands. 

The following steps demonstrate an example of a windows installation using the `-proxy` argument:

1. The Invoke-WebRequest command needs proxy information to access the installer script. Then the Deploy-IoTEdge command needs the proxy information to download the installation files. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. The Initialize-IoTEdge command doesn't need to go through the proxy server, so the second step only requires proxy information for Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Se si hanno credenziali complesse per il server proxy che non è possibile includere nell'URL, usare il parametro `-ProxyCredential` all'interno di `-InvokeWebRequestParameters`. Ad esempio,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Per altre informazioni sui parametri del proxy, vedere [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). For more information about Windows installation options, including offline installation, see [Install Azure IoT Edge runtime on Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Configurare i daemon

IoT Edge relies on two daemons running on the IoT Edge device. The Moby daemon makes web requests to pull container images from container registries. Il daemon IoT Edge esegue richieste Web per comunicare con l'hub IoT.

Both the Moby and the IoT Edge daemons need to be configured to use the proxy server for ongoing device functionality. This step takes place on the IoT Edge device during initial device setup. 

### <a name="moby-daemon"></a>Moby daemon

Since Moby is built on Docker, refer to the Docker documentation to configure the Moby daemon with environment variables. La maggior parte dei registri contenitori (compresi DockerHub e registri contenitori Azure) supporta le richieste HTTPS, per cui il parametro da impostare è **HTTPS_PROXY**. Se si esegue il pull di immagini da un registro che non supporta il protocollo TLS (Transport Layer Security), sarà necessario impostare il parametro **HTTP_PROXY**. 

Choose the article that applies to your IoT Edge device operating system: 

* [Configure Docker daemon on Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * The Moby daemon on Linux devices keeps the name Docker.
* [Configure Docker daemon on Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * The Moby daemon on Windows devices is called iotedge-moby. The names are different because it's possible to run both Docker Desktop and Moby in parallel on a Windows device. 

### <a name="iot-edge-daemon"></a>Daemon IoT Edge

The IoT Edge daemon is configured in a similar manner to the Moby daemon. Usare la procedura seguente per impostare una variabile di ambiente per il servizio, in base al sistema operativo usato. 

The IoT Edge daemon always uses HTTPS to send requests to IoT Hub.

#### <a name="linux"></a>Linux

Aprire un editor nel terminale per configurare il daemon IoT Edge. 

```bash
sudo systemctl edit iotedge
```

Immettere il testo seguente, sostituendo l' **\<URL del proxy>** con la porta e l'indirizzo del server proxy. Quindi, salvare e chiudere. 

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Aggiornare Service Manager in modo da scegliere la nuova configurazione per IoT Edge.

```bash
sudo systemctl daemon-reload
```

Riavviare IoT Edge per rendere effettive le modifiche apportate.

```bash
sudo systemctl restart iotedge
```

Verificare che la variabile di ambiente sia stata creata e che la nuova configurazione sia stata caricata. 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Aprire una finestra di PowerShell come amministratore ed eseguire il comando seguente per modificare il registro con la nuova variabile di ambiente. Sostituire l' **\<UEL del proxy>** con la porta e l'indirizzo del server proxy. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Riavviare IoT Edge per rendere effettive le modifiche apportate.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Configurare l'agente IoT Edge

L'agente IoT Edge è il primo modulo che viene avviato in un dispositivo IoT Edge. Viene avviato per la prima volta in base alle informazioni presenti nel file config.yaml di IoT Edge. L'agente IoT Edge si connette quindi all'hub IoT per recuperare i manifesti della distribuzione, che dichiarano quali altri moduli devono essere distribuiti nel dispositivo.

This step takes place once on the IoT Edge device during initial device setup. 

1. Aprire il file config.yaml nel dispositivo IoT Edge. Nei sistemi Linux questo file si trova in **/etc/iotedge/config.yaml**. Nei sistemi Windows questo file si trova in **C:\ProgramData\iotedge\config.yaml**. Il file di configurazione è protetto, pertanto sono necessari i privilegi amministrativi per accedervi. On Linux systems, use the `sudo` command before opening the file in your preferred text editor. On Windows, open a text editor like Notepad as administrator and then open the file. 

2. Nel file config.yaml trovare la sezione **Edge Agent module spec**. La definizione dell'agente IoT Edge include un parametro **env** in cui è possibile aggiungere le variabili di ambiente. 

3. Rimuovere le parentesi graffe che sono un segnaposto per il parametro env e aggiungere la nuova variabile in una nuova riga. Tenere presente che i rientri in YAML sono rappresentati da due spazi. 

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. Per impostazione predefinita, il runtime IoT Edge usa AMQP per comunicare con l'hub IoT. Alcuni server proxy bloccano le porte AMQP. In tal caso, sarà anche necessario configurare edgeAgent per usare AMQP su WebSocket. Aggiungere una seconda variabile di ambiente.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![definizione di edgeAgent con variabili di ambiente](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Salvare le modifiche apportate a config.yaml e chiudere l'editor. Riavviare IoT Edge per rendere effettive le modifiche apportate. 

   * Linux: 

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Configurare i manifesti della distribuzione  

Dopo aver configurato il dispositivo IoT Edge per l'uso con il server proxy, è necessario continuare a dichiarare le variabili di ambiente nei manifesti della distribuzione futuri. You can edit deployment manifests either using the Azure portal wizard or by editing a deployment manifest JSON file. 

Configurare sempre i due moduli di runtime, edgeAgent ed edgeHub, per comunicare attraverso il server proxy affinché possano mantenere una connessione con l'hub IoT. If you remove the proxy information from the edgeAgent module, the only way to reestablish connection is by editing the config.yaml file on the device, as described in the previous section. 

Other IoT Edge modules that connect to the internet should be configured to communicate through the proxy server, too. Tuttavia, i moduli che indirizzano i messaggi tramite edgeHub o che comunicano solo con altri moduli nel dispositivo non necessitano dei dettagli del server proxy. 

This step is ongoing throughout the life of the IoT Edge device. 

### <a name="azure-portal"></a>Portale di Azure

Quando si usa la procedura guidata **Imposta moduli** per creare le distribuzioni per i dispositivi IoT Edge, ogni modulo avrà una sezione **Variabili di ambiente** che è possibile usare per configurare le connessioni al server proxy. 

Per configurare i moduli dell'agente IoT Edge e dell'hub IoT Edge, selezionare **Configura impostazioni avanzate per il runtime di Edge** nel primo passaggio della procedura guidata. 

![Configura impostazioni avanzate per il runtime di IoT Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Aggiungere la variabile di ambiente **https_proxy** alle definizioni dei moduli sia dell'agente IoT Edge sia dell'hub IoT Edge. Se nel file config.yaml è stata inclusa la variabile di ambiente **UpstreamProtocol** nel dispositivo IoT Edge, aggiungerla anche alla definizione del modulo dell'agente IoT Edge. 

![Impostare la variabile di ambiente https_proxy](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Tutti gli altri moduli che vengono aggiunti a un manifesto della distribuzione seguono lo stesso criterio. Nella pagina in cui si imposta il nome del modulo e l'immagine, è presente una sezione variabili di ambiente.

### <a name="json-deployment-manifest-files"></a>File JSON del manifesto della distribuzione

Se le distribuzioni per i dispositivi IoT Edge vengono create usando i modelli in Visual Studio Code o creando manualmente i file JSON, è possibile aggiungere le variabili di ambiente direttamente a ogni definizione del modulo. 

Usare il formato JSON seguente: 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Con le variabili di ambiente incluse la definizione del modulo sarà simile a quella illustrata nell'esempio di edgeHub seguente:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Se nel file config.yaml è stata inclusa la variabile di ambiente **UpstreamProtocol** nel dispositivo IoT Edge, aggiungerla anche alla definizione del modulo dell'agente IoT Edge. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui ruoli del [runtime IoT Edge](iot-edge-runtime.md).

Risolvere gli errori di installazione e configurazione con [Problemi comuni e soluzioni per Azure IoT Edge](troubleshoot.md)

