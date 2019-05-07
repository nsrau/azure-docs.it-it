---
title: Configurare i dispositivi per i proxy di rete - Azure IoT Edge | Microsoft Docs
description: Come configurare il runtime Azure IoT Edge e tutti i moduli IoT Edge con connessione Internet per comunicare tramite un server proxy.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 883f6022f3d0f609de2d8f33b0285d8c40b7bee9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142132"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Configurare un dispositivo IoT Edge per comunicare tramite un server proxy

I dispositivi IoT Edge inviano le richieste HTTPS per comunicare con l'hub IoT. Se il dispositivo è connesso a una rete che usa un server proxy, è necessario configurare il runtime IoT Edge per comunicare tramite il server. I server proxy possono influire anche sui singoli moduli IoT Edge se vengono effettuate richieste HTTP o HTTPS che non vengono indirizzate tramite l'hub di IoT Edge. 

Per configurare un dispositivo IoT Edge da usare con un server proxy, seguire questi passaggi di base: 

1. Installare il runtime IoT Edge nel dispositivo. 
2. Configurare il daemon Docker e il daemon IoT Edge nel dispositivo per usare il server proxy.
3. Configurare le proprietà di edgeAgent nel file config.yaml nel dispositivo.
4. Impostare le variabili di ambiente per il runtime IoT Edge e altri moduli IoT Edge nel manifesto della distribuzione.

## <a name="know-your-proxy-url"></a>Conoscere l'URL proxy

Per configurare il daemon Docker e il daemon IoT Edge nel dispositivo è necessario conoscere l'URL proxy.

Gli URL proxy hanno il formato seguente: **protocollo**://**host_proxy**:**porta_proxy**.

* Il **protocollo** è HTTP o HTTPS. Il daemon Docker può utilizzare entrambi i protocolli in base alle impostazioni del registro contenitori, mentre il daemon e i contenitori di runtime di IoT Edge devono usare sempre HTTPS.

* L'**host_proxy** è un indirizzo per il server proxy. Se il server proxy richiede l'autenticazione, è possibile fornire le credenziali come parte dell'host del proxy con il formato seguente: **utente**:**password**\@**host_proxy** .

* La **porta_proxy** è la porta di rete a cui il proxy risponde al traffico di rete.

## <a name="install-the-runtime"></a>Installare il runtime

Se si installa il runtime IoT Edge in un dispositivo Linux, configurare la gestione di pacchetti per il passaggio attraverso il server proxy per accedere al pacchetto di installazione. Ad esempio, [Configurare apt-get per usare un proxy http](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Dopo aver configurato la gestione pacchetti, seguire le istruzioni in [Installare il runtime di Azure IoT Edge in Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) oppure [Installare il runtime di Azure IoT Edge in Linux (x64)](how-to-install-iot-edge-linux.md) come di consueto.

Se si installa il runtime di IoT Edge in un dispositivo Windows, è necessario passare attraverso il server proxy due volte. La prima connessione consiste nello scaricare il file di script del programma di installazione e la seconda è durante l'installazione per scaricare i componenti necessari. È possibile configurare le informazioni sul proxy nelle impostazioni di Windows, o includere le informazioni sul proxy direttamente nei comandi di PowerShell. I passaggi seguenti illustrano un esempio di un'installazione di windows usando il `-proxy` argomento:

1. Il comando Invoke-WebRequest richiede informazioni sul proxy per accedere allo script del programma di installazione. Il comando Distribuisci IoTEdge deve quindi le informazioni sul proxy per scaricare i file di installazione. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Il comando Initialize-IoTEdge senza dover passare attraverso il server proxy, in modo che il secondo passaggio richiede solo le informazioni sul proxy per Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge

If you have complicated credentials for the proxy server that can't be included in the URL, use the `-ProxyCredential` parameter within `-InvokeWebRequestParameters`. For example,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Per altre informazioni sui parametri del proxy, vedere [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Per altre informazioni sulle opzioni di installazione di Windows, vedere [runtime di installare Azure IoT Edge su Windows](how-to-install-iot-edge-windows.md).

Dopo aver installato il runtime di IoT Edge, usare la sezione seguente per configurarlo con le informazioni sul proxy. 

## <a name="configure-the-daemons"></a>Configurare i daemon

Il daemon Moby e IoT Edge in esecuzione nel dispositivo IoT Edge devono essere configurati per usare il server proxy. Il daemon Moby effettua le richieste web per le immagini del contenitore pull da registri contenitori. Il daemon IoT Edge esegue richieste Web per comunicare con l'hub IoT.

### <a name="moby-daemon"></a>Daemon MOBY

Poiché Moby è basato su Docker, vedere la documentazione di Docker per configurare il daemon Moby con variabili di ambiente. La maggior parte dei registri contenitori (compresi DockerHub e registri contenitori Azure) supporta le richieste HTTPS, per cui il parametro da impostare è **HTTPS_PROXY**. Se si esegue il pull di immagini da un registro che non supporta il protocollo TLS (Transport Layer Security), sarà necessario impostare il parametro **HTTP_PROXY**. 

Scegliere l'articolo che si applicano al sistema operativo dispositivo IoT Edge: 

* [Configurare il daemon Docker in Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * Il daemon Moby nei dispositivi Linux mantiene il nome Docker.
* [Configurare il daemon Docker in Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * Il daemon Moby nei dispositivi Windows è chiamato iotedge moby. I nomi sono diversi perché è possibile eseguire Docker Desktop sia Moby in parallelo in un dispositivo Windows. 

### <a name="iot-edge-daemon"></a>Daemon IoT Edge

Il daemon di IoT Edge è configurato in modo analogo al daemon Moby. Tutte le richieste inviate da IoT Edge all'hub IoT usano HTTPS. Usare la procedura seguente per impostare una variabile di ambiente per il servizio, in base al sistema operativo usato. 

#### <a name="linux"></a>Linux

Aprire un editor nel terminale per configurare il daemon IoT Edge. 

```bash
sudo systemctl edit iotedge
```

Immettere il testo seguente, sostituendo l'**\<URL del proxy>** con la porta e l'indirizzo del server proxy. Quindi, salvare e chiudere. 

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

Aprire una finestra di PowerShell come amministratore ed eseguire il comando seguente per modificare il registro con la nuova variabile di ambiente. Sostituire l'**\<UEL del proxy>** con la porta e l'indirizzo del server proxy. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Riavviare IoT Edge per rendere effettive le modifiche apportate.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Configurare l'agente IoT Edge

L'agente IoT Edge è il primo modulo che viene avviato in un dispositivo IoT Edge. Viene avviato per la prima volta in base alle informazioni presenti nel file config.yaml di IoT Edge. L'agente IoT Edge si connette quindi all'hub IoT per recuperare i manifesti della distribuzione, che dichiarano quali altri moduli devono essere distribuiti nel dispositivo.

Aprire il file config.yaml nel dispositivo IoT Edge. Nei sistemi Linux questo file si trova in **/etc/iotedge/config.yaml**. Nei sistemi Windows questo file si trova in **C:\ProgramData\iotedge\config.yaml**. Il file di configurazione è protetto, pertanto sono necessari i privilegi amministrativi per accedervi. Nei sistemi Linux sarà quindi necessario usare il comando `sudo` prima di aprire il file nell'editor di testo preferito. In Windows sarà necessario aprire un editor di testo come Blocco note da eseguire come amministratore e quindi aprire il file. 

Nel file config.yaml trovare la sezione **Edge Agent module spec**. La definizione dell'agente IoT Edge include un parametro **env** in cui è possibile aggiungere le variabili di ambiente. 

<!--
![edgeAgent definition](./media/how-to-configure-proxy-support/edgeagent-unedited.png)
-->

Rimuovere le parentesi graffe che sono un segnaposto per il parametro env e aggiungere la nuova variabile in una nuova riga. Tenere presente che i rientri in YAML sono rappresentati da due spazi. 

```yaml
https_proxy: "<proxy URL>"
```

Per impostazione predefinita, il runtime IoT Edge usa AMQP per comunicare con l'hub IoT. Alcuni server proxy bloccano le porte AMQP. In tal caso, sarà anche necessario configurare edgeAgent per usare AMQP su WebSocket. Aggiungere una seconda variabile di ambiente.

```yaml
UpstreamProtocol: "AmqpWs"
```

![definizione di edgeAgent con variabili di ambiente](./media/how-to-configure-proxy-support/edgeagent-edited.png)

Salvare le modifiche apportate a config.yaml e chiudere l'editor. Riavviare IoT Edge per rendere effettive le modifiche apportate. 

* Linux: 

   ```bash
   sudo systemctl restart iotedge
   ```

* Windows:

   ```powershell
   Restart-Service iotedge
   ```

## <a name="configure-deployment-manifests"></a>Configurare i manifesti della distribuzione  

Dopo aver configurato il dispositivo IoT Edge per l'uso con il server proxy, è necessario continuare a dichiarare le variabili di ambiente nei manifesti della distribuzione futuri. Configurare sempre i due moduli di runtime, edgeAgent ed edgeHub, per comunicare attraverso il server proxy affinché possano mantenere una connessione con l'hub IoT. Per il server proxy devono essere configurati altri moduli IoT Edge con connessione Internet. Tuttavia, i moduli che indirizzano i messaggi tramite edgeHub o che comunicano solo con altri moduli nel dispositivo non necessitano dei dettagli del server proxy. 

È possibile creare i manifesti della distribuzione usando il portale di Azure o manualmente modificando un file JSON. 

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
            "value": "https://proxy.example.com:3128"
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

