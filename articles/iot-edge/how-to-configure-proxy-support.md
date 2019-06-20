---
title: Configurare i dispositivi per i proxy di rete - Azure IoT Edge | Microsoft Docs
description: Come configurare il runtime Azure IoT Edge e tutti i moduli IoT Edge con connessione Internet per comunicare tramite un server proxy.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 34a94a1b9c38070f5c9de22d9a9e4f24183a876c
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151012"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Configurare un dispositivo IoT Edge per comunicare tramite un server proxy

I dispositivi IoT Edge inviano le richieste HTTPS per comunicare con l'hub IoT. Se il dispositivo è connesso a una rete che usa un server proxy, è necessario configurare il runtime IoT Edge per comunicare tramite il server. I server proxy possono influire anche sui singoli moduli IoT Edge se vengono effettuate richieste HTTP o HTTPS che non vengono indirizzate tramite l'hub di IoT Edge. 

Questo articolo illustra i quattro passaggi seguenti per configurare e gestire un dispositivo IoT Edge dietro un server proxy: 

1. **Installare il runtime di IoT Edge nel dispositivo.**

   Gli script di installazione di IoT Edge pull pacchetti e i file da internet, in modo che il dispositivo deve comunicare tramite il server proxy per rendere tali richieste. Per informazioni dettagliate, vedere la [installare il runtime tramite un proxy](#install-the-runtime-through-a-proxy) sezione di questo articolo. Per i dispositivi Windows, lo script di installazione fornisce anche un [installazione Offline](how-to-install-iot-edge-windows.md#offline-installation) opzione. 

   Questo passaggio è un processo unico eseguito nel dispositivo IoT Edge quando si è prima configurarlo. Sono inoltre necessarie le stesse connessioni quando si aggiorna il runtime di IoT Edge. 

2. **Configurare il daemon Docker e il daemon di IoT Edge sul dispositivo.**

   IoT Edge usa due daemon sul dispositivo, che devono effettuare richieste web tramite il server proxy. Il daemon di IoT Edge è responsabile per le comunicazioni con l'IoT Hub. Il daemon Moby è responsabile della gestione dei contenitori, pertanto, comunica con i registri contenitori. Per informazioni dettagliate, vedere la [configurare il daemon](#configure-the-daemons) sezione di questo articolo. 

   Questo passaggio è un processo unico eseguito nel dispositivo IoT Edge quando si è prima configurarlo.

3. **Configurare le proprietà dell'agente di IoT Edge nel file config. yaml nel dispositivo.**

   Il daemon di IoT Edge viene avviato il modulo di edgeAgent inizialmente, ma il modulo di edgeAgent non sarà responsabile per il recupero del manifesto di distribuzione dall'IoT Hub e l'avvio di tutti gli altri moduli. Per l'agente di IoT Edge stabilire la connessione iniziale all'IoT Hub, configurare manualmente le variabili di ambiente modulo edgeAgent nel dispositivo stesso. Dopo la connessione iniziale, è possibile configurare il modulo di edgeAgent in modalità remota. Per informazioni dettagliate, vedere la [configurare l'agente di IoT Edge](#configure-the-iot-edge-agent) sezione di questo articolo.

   Questo passaggio è un processo unico eseguito nel dispositivo IoT Edge quando si è prima configurarlo.

4. **Per tutte le distribuzioni future modulo, impostare le variabili di ambiente per tutti i moduli che comunicano tramite il proxy.**

   Una volta nel dispositivo IoT Edge viene configurato e connesso all'IoT Hub tramite il server proxy, è necessario mantenere la connessione in tutte le distribuzioni future modulo. Per informazioni dettagliate, vedere la [configurare i manifesti della distribuzione](#configure-deployment-manifests) sezione di questo articolo. 

   Questo passaggio è un processo continuo eseguito in modalità remota in modo che ogni nuovo aggiornamento distribuzione o modulo gestisce la capacità del dispositivo di comunicare attraverso il server proxy. 

## <a name="know-your-proxy-url"></a>Conoscere l'URL proxy

Prima di iniziare i passaggi descritti in questo articolo, è necessario conoscere l'URL del proxy.

Gli URL proxy hanno il formato seguente: **protocollo**://**host_proxy**:**porta_proxy**.

* Il **protocollo** è HTTP o HTTPS. Il daemon Docker può utilizzare qualsiasi protocollo, a seconda delle impostazioni del Registro di sistema contenitore, ma i contenitori di daemon e runtime di IoT Edge devono sempre usare HTTP per connettersi al proxy.

* L'**host_proxy** è un indirizzo per il server proxy. Se il server proxy richiede l'autenticazione, è possibile fornire le credenziali come parte dell'host del proxy con il formato seguente: **utente**:**password**\@**host_proxy** .

* La **porta_proxy** è la porta di rete a cui il proxy risponde al traffico di rete.

## <a name="install-the-runtime-through-a-proxy"></a>Installare il runtime tramite un proxy

Se il dispositivo IoT Edge viene eseguito in Windows o Linux, è necessario accedere i pacchetti di installazione tramite il server proxy. A seconda del sistema operativo, seguire i passaggi per installare il runtime di IoT Edge tramite un server proxy. 

### <a name="linux"></a>Linux

Se si installa il runtime IoT Edge in un dispositivo Linux, configurare la gestione di pacchetti per il passaggio attraverso il server proxy per accedere al pacchetto di installazione. Ad esempio, [Configurare apt-get per usare un proxy http](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Dopo aver configurato la gestione pacchetti, seguire le istruzioni in [Installare il runtime di Azure IoT Edge in Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) oppure [Installare il runtime di Azure IoT Edge in Linux (x64)](how-to-install-iot-edge-linux.md) come di consueto.

### <a name="windows"></a>Windows

Se si installa il runtime di IoT Edge in un dispositivo Windows, è necessario passare attraverso il server proxy due volte. La prima connessione scaricato il file di script del programma di installazione, e la seconda connessione durante l'installazione per scaricare i componenti necessari. È possibile configurare le informazioni sul proxy nelle impostazioni di Windows, o includere le informazioni sul proxy direttamente nei comandi di PowerShell. 

I passaggi seguenti illustrano un esempio di un'installazione di windows usando il `-proxy` argomento:

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

Per altre informazioni sui parametri del proxy, vedere [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Per altre informazioni sulle opzioni di installazione di Windows, inclusa l'installazione offline, vedere [runtime di installare Azure IoT Edge su Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Configurare i daemon

IoT Edge si basa su due daemon in esecuzione nel dispositivo IoT Edge. Il daemon Moby effettua le richieste web per le immagini del contenitore pull da registri contenitori. Il daemon IoT Edge esegue richieste Web per comunicare con l'hub IoT.

Il Moby sia i daemon di IoT Edge devono essere configurati per utilizzare il server proxy per la funzionalità di dispositivo in corso. Questo passaggio viene eseguita nel dispositivo IoT Edge durante l'installazione iniziale del dispositivo. 

### <a name="moby-daemon"></a>Daemon MOBY

Poiché Moby è basato su Docker, vedere la documentazione di Docker per configurare il daemon Moby con variabili di ambiente. La maggior parte dei registri contenitori (compresi DockerHub e registri contenitori Azure) supporta le richieste HTTPS, per cui il parametro da impostare è **HTTPS_PROXY**. Se si esegue il pull di immagini da un registro che non supporta il protocollo TLS (Transport Layer Security), sarà necessario impostare il parametro **HTTP_PROXY**. 

Scegliere l'articolo che si applicano al sistema operativo dispositivo IoT Edge: 

* [Configurare il daemon Docker in Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * Il daemon Moby nei dispositivi Linux mantiene il nome Docker.
* [Configurare il daemon Docker in Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * Il daemon Moby nei dispositivi Windows è chiamato iotedge moby. I nomi sono diversi perché è possibile eseguire Docker Desktop sia Moby in parallelo in un dispositivo Windows. 

### <a name="iot-edge-daemon"></a>Daemon IoT Edge

Il daemon di IoT Edge è configurato in modo analogo al daemon Moby. Usare la procedura seguente per impostare una variabile di ambiente per il servizio, in base al sistema operativo usato. 

Il daemon di IoT Edge Usa sempre HTTPS per inviare richieste all'IoT Hub.

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

Questo passaggio viene eseguita una sola volta nel dispositivo IoT Edge durante l'installazione iniziale del dispositivo. 

1. Aprire il file config.yaml nel dispositivo IoT Edge. Nei sistemi Linux questo file si trova in **/etc/iotedge/config.yaml**. Nei sistemi Windows questo file si trova in **C:\ProgramData\iotedge\config.yaml**. Il file di configurazione è protetto, pertanto sono necessari i privilegi amministrativi per accedervi. Nei sistemi Linux, usare il `sudo` comando prima di aprire il file nell'editor di testo preferito. In Windows, aprire un editor di testo come blocco note come amministratore e quindi aprire il file. 

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

Dopo aver configurato il dispositivo IoT Edge per l'uso con il server proxy, è necessario continuare a dichiarare le variabili di ambiente nei manifesti della distribuzione futuri. È possibile modificare i manifesti di distribuzione utilizzando il procedura guidata del portale Azure o modificando una distribuzione di file JSON del manifesto. 

Configurare sempre i due moduli di runtime, edgeAgent ed edgeHub, per comunicare attraverso il server proxy affinché possano mantenere una connessione con l'hub IoT. Se si rimuovono le informazioni sul proxy dal modulo di edgeAgent, l'unico modo per ristabilire la connessione è modificando il file config. yaml sul dispositivo, come descritto nella sezione precedente. 

Altri moduli di IoT Edge che si connettono a internet devono essere configurati per comunicare tramite il server proxy, troppo. Tuttavia, i moduli che indirizzano i messaggi tramite edgeHub o che comunicano solo con altri moduli nel dispositivo non necessitano dei dettagli del server proxy. 

Questo passaggio è in corso in tutto il ciclo di vita del dispositivo IoT Edge. 

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

