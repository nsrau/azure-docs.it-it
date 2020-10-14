---
title: Configurare i dispositivi per i proxy di rete - Azure IoT Edge | Microsoft Docs
description: Come configurare il runtime Azure IoT Edge e tutti i moduli IoT Edge con connessione Internet per comunicare tramite un server proxy.
author: kgremban
ms.author: kgremban
ms.date: 09/03/2020
ms.topic: how-to
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- contperfq1
ms.openlocfilehash: ae0c4c69cf500fb352cc889e068888084d1d8f8b
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045959"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Configurare un dispositivo IoT Edge per comunicare tramite un server proxy

I dispositivi IoT Edge inviano le richieste HTTPS per comunicare con l'hub IoT. Se il dispositivo è connesso a una rete che usa un server proxy, è necessario configurare il runtime IoT Edge per comunicare tramite il server. I server proxy possono influire anche sui singoli moduli IoT Edge se vengono effettuate richieste HTTP o HTTPS che non vengono indirizzate tramite l'hub di IoT Edge.

Questo articolo illustra i quattro passaggi seguenti per configurare e quindi gestire un dispositivo IoT Edge dietro a un server proxy:

1. [**Installare il runtime di IoT Edge nel dispositivo**](#install-the-runtime-through-a-proxy)

   Il IoT Edge script di installazione estrae i pacchetti e i file da Internet, in modo che il dispositivo debba comunicare attraverso il server proxy per effettuare tali richieste. Per i dispositivi Windows, lo script di installazione fornisce anche un'opzione di installazione offline.

   Questo passaggio è un processo unico per configurare il dispositivo IoT Edge al momento della prima configurazione. Quando si aggiorna il runtime di IoT Edge, sono necessarie anche le stesse connessioni.

2. [**Configurare il daemon Docker e il daemon IoT Edge nel dispositivo**](#configure-the-daemons)

   IoT Edge usa due daemon sul dispositivo, che devono eseguire entrambe le richieste Web tramite il server proxy. Il daemon IoT Edge è responsabile delle comunicazioni con l'hub Internet. Il daemon Moby è responsabile della gestione dei contenitori, quindi comunica con i registri contenitori.

   Questo passaggio è un processo unico per configurare il dispositivo IoT Edge al momento della prima configurazione.

3. [**Configurare le proprietà dell'agente IoT Edge nel file config. YAML del dispositivo**](#configure-the-iot-edge-agent)

   Il daemon di IoT Edge avvia inizialmente il modulo edgeAgent. Il modulo edgeAgent recupera quindi il manifesto di distribuzione dall'hub Internet e avvia tutti gli altri moduli. Per fare in modo che l'agente di IoT Edge effettui la connessione iniziale all'hub Internet, configurare manualmente le variabili di ambiente del modulo edgeAgent nel dispositivo stesso. Dopo la connessione iniziale, è possibile configurare il modulo edgeAgent in modalità remota.

   Questo passaggio è un processo unico per configurare il dispositivo IoT Edge al momento della prima configurazione.

4. [**Per tutte le distribuzioni di moduli future, impostare le variabili di ambiente per qualsiasi modulo che comunica tramite il proxy**](#configure-deployment-manifests)

   Quando il dispositivo di IoT Edge viene configurato e connesso all'hub Internet tramite il server proxy, è necessario mantenere la connessione in tutte le distribuzioni di moduli future.

   Questo passaggio è un processo continuo eseguito in modalità remota, in modo che ogni nuovo modulo o aggiornamento della distribuzione mantenga la capacità del dispositivo di comunicare tramite il server proxy.

## <a name="know-your-proxy-url"></a>Conoscere l'URL proxy

Prima di iniziare i passaggi descritti in questo articolo, è necessario conoscerne l'URL.

Gli URL proxy hanno il formato seguente: **protocollo**://**host_proxy**:**porta_proxy**.

* Il **protocollo** è HTTP o HTTPS. Il daemon Docker può usare uno dei due protocolli, a seconda delle impostazioni del registro contenitori, ma il daemon IoT Edge e i contenitori di runtime devono sempre usare HTTP per connettersi al proxy.

* L'**host_proxy** è un indirizzo per il server proxy. Se il server proxy richiede l'autenticazione, è possibile fornire le credenziali come parte dell'host proxy con il formato seguente: **User**:**password** \@ **proxy_host**.

* La **porta_proxy** è la porta di rete a cui il proxy risponde al traffico di rete.

## <a name="install-the-runtime-through-a-proxy"></a>Installare il runtime tramite un proxy

Se il dispositivo IoT Edge viene eseguito in Windows o Linux, è necessario accedere ai pacchetti di installazione tramite il server proxy. A seconda del sistema operativo, seguire i passaggi per installare il runtime di IoT Edge tramite un server proxy.

### <a name="linux-devices"></a>Dispositivi Linux

Se si installa il runtime IoT Edge in un dispositivo Linux, configurare la gestione di pacchetti per il passaggio attraverso il server proxy per accedere al pacchetto di installazione. Ad esempio, [Configurare apt-get per usare un proxy http](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Una volta configurata la gestione pacchetti, seguire le istruzioni in [Install Azure IOT Edge Runtime](how-to-install-iot-edge.md) come di consueto.

### <a name="windows-devices"></a>Dispositivi Windows

Se si sta installando il runtime di IoT Edge in un dispositivo Windows, è necessario eseguire due volte il server proxy. La prima connessione Scarica il file di script del programma di installazione e la seconda connessione durante l'installazione per scaricare i componenti necessari. È possibile configurare le informazioni sul proxy nelle impostazioni di Windows o includere le informazioni del proxy direttamente nei comandi di PowerShell.

Nei passaggi seguenti viene illustrato un esempio di installazione di Windows tramite l' `-proxy` argomento:

1. Il comando Invoke-WebRequest richiede informazioni sul proxy per accedere allo script del programma di installazione. Quindi il comando Deploy-IoTEdge richiede le informazioni sul proxy per scaricare i file di installazione.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Il comando Initialize-IoTEdge non deve passare attraverso il server proxy, quindi il secondo passaggio richiede solo le informazioni sul proxy per Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Se si hanno credenziali complesse per il server proxy che non è possibile includere nell'URL, usare il parametro `-ProxyCredential` all'interno di `-InvokeWebRequestParameters`. Ad esempio,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Per altre informazioni sui parametri del proxy, vedere [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest). Per ulteriori informazioni sui parametri di installazione di Windows, vedere [script di PowerShell per IOT Edge in Windows](reference-windows-scripts.md).

## <a name="configure-the-daemons"></a>Configurare i daemon

IoT Edge si basa su due daemon in esecuzione sul dispositivo IoT Edge. Il daemon Moby esegue richieste Web per estrarre le immagini del contenitore dai registri dei contenitori. Il daemon IoT Edge esegue richieste Web per comunicare con l'hub IoT.

È necessario configurare i daemon Moby e IoT Edge per usare il server proxy per la funzionalità del dispositivo in corso. Questo passaggio si verifica nel dispositivo IoT Edge durante la configurazione iniziale del dispositivo.

### <a name="moby-daemon"></a>Daemon Moby

Poiché Moby si basa su Docker, vedere la documentazione di Docker per configurare il daemon Moby con le variabili di ambiente. La maggior parte dei registri contenitori (compresi DockerHub e registri contenitori Azure) supporta le richieste HTTPS, per cui il parametro da impostare è **HTTPS_PROXY**. Se si esegue il pull di immagini da un registro che non supporta il protocollo TLS (Transport Layer Security), sarà necessario impostare il parametro **HTTP_PROXY**.

Scegliere l'articolo che si applica al sistema operativo del dispositivo IoT Edge:

* [Configurare il daemon Docker in Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) Il daemon Moby nei dispositivi Linux mantiene il nome docker.
* [Configurare il daemon Docker in Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) Il daemon Moby nei dispositivi Windows è denominato iotedge-Moby. I nomi sono diversi perché è possibile eseguire sia Docker desktop che Moby in parallelo in un dispositivo Windows.

### <a name="iot-edge-daemon"></a>Daemon IoT Edge

Il daemon di IoT Edge viene configurato in modo analogo al daemon Moby. Usare la procedura seguente per impostare una variabile di ambiente per il servizio, in base al sistema operativo usato.

Il daemon IoT Edge usa sempre HTTPS per inviare richieste all'hub.

#### <a name="linux"></a>Linux

Aprire un editor nel terminale per configurare il daemon IoT Edge.

```bash
sudo systemctl edit iotedge
```

Immettere il testo seguente, sostituendo **\<proxy URL>** con l'indirizzo e la porta del server proxy. Quindi, salvare e chiudere.

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

Aprire una finestra di PowerShell come amministratore ed eseguire il comando seguente per modificare il registro con la nuova variabile di ambiente. Sostituire **\<proxy url>** con l'indirizzo e la porta del server proxy.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Riavviare IoT Edge per rendere effettive le modifiche apportate.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Configurare l'agente IoT Edge

L'agente IoT Edge è il primo modulo che viene avviato in un dispositivo IoT Edge. Viene avviato per la prima volta in base alle informazioni presenti nel file config.yaml di IoT Edge. L'agente IoT Edge si connette quindi all'hub IoT per recuperare i manifesti della distribuzione, che dichiarano quali altri moduli devono essere distribuiti nel dispositivo.

Questo passaggio viene eseguito una volta nel dispositivo IoT Edge durante la configurazione iniziale del dispositivo.

1. Aprire il file config.yaml nel dispositivo IoT Edge. Nei sistemi Linux questo file si trova in **/etc/iotedge/config.yaml**. Nei sistemi Windows questo file si trova in **C:\ProgramData\iotedge\config.yaml**. Il file di configurazione è protetto, pertanto sono necessari i privilegi amministrativi per accedervi. Nei sistemi Linux, usare il `sudo` comando prima di aprire il file nell'editor di testo preferito. In Windows aprire un editor di testo come blocco note come amministratore e quindi aprire il file.

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

Una volta configurato il dispositivo IoT Edge per l'uso con il server proxy, è necessario continuare a dichiarare la variabile di ambiente HTTPS_PROXY nei manifesti di distribuzione futuri. È possibile modificare i manifesti della distribuzione utilizzando la procedura guidata portale di Azure o modificando un file JSON del manifesto di distribuzione.

Configurare sempre i due moduli di runtime, edgeAgent ed edgeHub, per comunicare attraverso il server proxy affinché possano mantenere una connessione con l'hub IoT. Se si rimuovono le informazioni sul proxy dal modulo edgeAgent, l'unico modo per ristabilire la connessione consiste nel modificare il file config. YAML sul dispositivo, come descritto nella sezione precedente.

Oltre ai moduli edgeAgent e edgeHub, altri moduli possono richiedere la configurazione del proxy. I moduli che devono accedere alle risorse di Azure oltre all'hub Internet, ad esempio l'archiviazione BLOB, devono avere la variabile HTTPS_PROXY specificata nel file manifesto della distribuzione.

La procedura seguente è applicabile per tutto il ciclo di vita del dispositivo IoT Edge.

### <a name="azure-portal"></a>Portale di Azure

Quando si usa la procedura guidata **imposta moduli** per creare distribuzioni per IOT Edge dispositivi, ogni modulo dispone di una sezione **variabili di ambiente** in cui è possibile configurare le connessioni al server proxy.

Per configurare l'agente IoT Edge e i moduli Hub IoT Edge, selezionare **impostazioni di runtime** nel primo passaggio della procedura guidata.

![Configura impostazioni avanzate per il runtime di IoT Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Aggiungere la variabile di ambiente **https_proxy** alle definizioni dei moduli sia dell'agente IoT Edge sia dell'hub IoT Edge. Se nel file config.yaml è stata inclusa la variabile di ambiente **UpstreamProtocol** nel dispositivo IoT Edge, aggiungerla anche alla definizione del modulo dell'agente IoT Edge.

![Impostare la variabile di ambiente https_proxy](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Tutti gli altri moduli che vengono aggiunti a un manifesto della distribuzione seguono lo stesso criterio.

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