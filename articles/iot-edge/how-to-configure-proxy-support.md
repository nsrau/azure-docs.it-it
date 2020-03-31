---
title: Configurare i dispositivi per i proxy di rete - Azure IoT Edge | Microsoft Docs
description: Come configurare il runtime Azure IoT Edge e tutti i moduli IoT Edge con connessione Internet per comunicare tramite un server proxy.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0600568ace5384cfb13688d14d1cf79e473f3208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133208"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Configurare un dispositivo IoT Edge per comunicare tramite un server proxy

I dispositivi IoT Edge inviano le richieste HTTPS per comunicare con l'hub IoT. Se il dispositivo è connesso a una rete che usa un server proxy, è necessario configurare il runtime IoT Edge per comunicare tramite il server. I server proxy possono influire anche sui singoli moduli IoT Edge se vengono effettuate richieste HTTP o HTTPS che non vengono indirizzate tramite l'hub di IoT Edge.

In questo articolo vengono illustrati i quattro passaggi seguenti per configurare e quindi gestire un dispositivo IoT Edge dietro un server proxy:

1. **Installare il runtime IoT Edge nel dispositivo.**

   Gli script di installazione di IoT Edge estraggono pacchetti e file da Internet, pertanto il dispositivo deve comunicare tramite il server proxy per effettuare tali richieste. Per la procedura dettagliata, vedere la sezione [Installare il runtime tramite un proxy](#install-the-runtime-through-a-proxy) di questo articolo. Per i dispositivi Windows, lo script di installazione fornisce anche [un'opzione](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation) di installazione offline.

   Questo passaggio è un processo una tantera eseguito sul dispositivo IoT Edge quando lo configuri per la prima volta. Le stesse connessioni sono necessarie anche quando si aggiorna il runtime di IoT Edge.The same connections are also required when you update the IoT Edge runtime.

2. **Configurare il daemon Docker e il daemon IoT Edge sul dispositivo.**

   IoT Edge utilizza due daemons sul dispositivo, entrambi i quali devono effettuare richieste web tramite il server proxy. Il daemon IoT Edge è responsabile delle comunicazioni con l'hub IoT. Il daemon Moby è responsabile della gestione dei contenitori, pertanto comunica con i registri contenitori. Per la procedura dettagliata, vedere la sezione [Configurare i daemondi](#configure-the-daemons) di questo articolo.

   Questo passaggio è un processo una tantera eseguito sul dispositivo IoT Edge quando lo configuri per la prima volta.

3. **Configurare le proprietà dell'agente IoT Edge nel file config.yaml nel dispositivo.**

   Il daemon IoT Edge avvia inizialmente il modulo edgeAgent, ma poi il modulo edgeAgent è responsabile del recupero del manifesto di distribuzione dall'hub IoT e dell'avvio di tutti gli altri moduli. Affinché l'agente IoT Edge effettui la connessione iniziale all'hub IoT, configurare manualmente le variabili di ambiente del modulo edgeAgent nel dispositivo stesso. Dopo la connessione iniziale, è possibile configurare il modulo edgeAgent in remoto. Per la procedura dettagliata, vedere la sezione [Configurare l'agente IoT Edge](#configure-the-iot-edge-agent) di questo articolo.

   Questo passaggio è un processo una tantera eseguito sul dispositivo IoT Edge quando lo configuri per la prima volta.

4. **Per tutte le distribuzioni di moduli futuri, impostare le variabili di ambiente per qualsiasi modulo che comunica attraverso il proxy.**

   Dopo aver configurato e connesso il dispositivo IoT Edge all'hub IoT tramite il server proxy, è necessario mantenere la connessione in tutte le distribuzioni di moduli future. Per la procedura dettagliata, vedere la sezione [Configurare i manifesti](#configure-deployment-manifests) di distribuzione di questo articolo.

   Questo passaggio è un processo in corso eseguito in remoto in modo che ogni nuovo aggiornamento di distribuzione o modulo mantiene la capacità del dispositivo di comunicare tramite il server proxy.

## <a name="know-your-proxy-url"></a>Conoscere l'URL proxy

Prima di iniziare una delle istruzioni descritte in questo articolo, è necessario conoscere l'URL del proxy.

Gli URL proxy hanno il formato seguente: **protocollo**://**host_proxy**:**porta_proxy**.

* Il **protocollo** è HTTP o HTTPS. Il daemon Docker può usare entrambi i protocolli, a seconda delle impostazioni del Registro di sistema del contenitore, ma i contenitori IoT Edge e runtime devono sempre usare HTTP per connettersi al proxy.

* L'**host_proxy** è un indirizzo per il server proxy. Se il server proxy richiede l'autenticazione, è possibile fornire le credenziali come parte dell'host proxy nel seguente formato: **utente**:**password**\@**proxy_host**.

* La **porta_proxy** è la porta di rete a cui il proxy risponde al traffico di rete.

## <a name="install-the-runtime-through-a-proxy"></a>Installare il runtime tramite un proxyInstall the runtime through a proxy

Sia che il dispositivo IoT Edge venga eseguito su Windows o Linux, è necessario accedere ai pacchetti di installazione tramite il server proxy. A seconda del sistema operativo in uso, seguire la procedura per installare il runtime di IoT Edge tramite un server proxy.

### <a name="linux-devices"></a>Dispositivi Linux

Se si installa il runtime IoT Edge in un dispositivo Linux, configurare la gestione di pacchetti per il passaggio attraverso il server proxy per accedere al pacchetto di installazione. Ad esempio, [Configurare apt-get per usare un proxy http](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Dopo aver configurato il gestore di pacchetti, seguire le istruzioni in Installare il runtime di [Azure IoT Edge in Linux](how-to-install-iot-edge-linux.md) come di consueto.

### <a name="windows-devices"></a>Dispositivi Windows

Se si installa il runtime IoT Edge in un dispositivo Windows, è necessario passare attraverso il server proxy due volte. La prima connessione scarica il file di script del programma di installazione e la seconda durante l'installazione si trova durante l'installazione per scaricare i componenti necessari. È possibile configurare le informazioni sul proxy nelle impostazioni di Windows o includere le informazioni sul proxy direttamente nei comandi di PowerShell.You can configure proxy information in Windows settings, or include your proxy information directly in the PowerShell commands.

I passaggi seguenti illustrano un esempio `-proxy` di installazione di Windows usando l'argomento :

1. Il comando Invoke-WebRequest richiede informazioni proxy per accedere allo script del programma di installazione. Quindi il comando Deploy-IoTEdge richiede le informazioni sul proxy per scaricare i file di installazione.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Il comando Initialize-IoTEdge non deve passare attraverso il server proxy, pertanto il secondo passaggio richiede solo informazioni proxy per Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Se si hanno credenziali complesse per il server proxy che non è possibile includere nell'URL, usare il parametro `-ProxyCredential` all'interno di `-InvokeWebRequestParameters`. Ad esempio,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Per altre informazioni sui parametri del proxy, vedere [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Per altre informazioni sulle opzioni di installazione di Windows, inclusa l'installazione offline, vedere Installare il runtime di [Azure IoT Edge in Windows.](how-to-install-iot-edge-windows.md)

## <a name="configure-the-daemons"></a>Configurare i daemon

IoT Edge si basa su due daemon in esecuzione sul dispositivo IoT Edge. Il daemon Moby effettua richieste Web per estrarre le immagini del contenitore dai registri dei contenitori. Il daemon IoT Edge esegue richieste Web per comunicare con l'hub IoT.

Entrambi i daemonS Moby e IoT Edge devono essere configurati per utilizzare il server proxy per le funzionalità del dispositivo in corso. Questo passaggio viene eseguito sul dispositivo IoT Edge durante la configurazione iniziale del dispositivo.

### <a name="moby-daemon"></a>daemon Moby

Poiché Moby è basato su Docker, fare riferimento alla documentazione di Docker per configurare il daemon Moby con variabili di ambiente. La maggior parte dei registri contenitori (compresi DockerHub e registri contenitori Azure) supporta le richieste HTTPS, per cui il parametro da impostare è **HTTPS_PROXY**. Se si esegue il pull di immagini da un registro che non supporta il protocollo TLS (Transport Layer Security), sarà necessario impostare il parametro **HTTP_PROXY**.

Scegliere l'articolo che si applica al sistema operativo del dispositivo IoT Edge:

* [Configurare Docker daemon su Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) Il daemon Moby sui dispositivi Linux mantiene il nome Docker.
* [Configurare il daemon Docker in Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) Il daemon Moby sui dispositivi Windows è chiamato iotedge-moby. I nomi sono diversi perché è possibile eseguire sia Docker Desktop che Moby in parallelo su un dispositivo Windows.

### <a name="iot-edge-daemon"></a>Daemon IoT Edge

Il daemon IoT Edge è configurato in modo simile al daemon Moby. Usare la procedura seguente per impostare una variabile di ambiente per il servizio, in base al sistema operativo usato.

Il daemon IoT Edge usa sempre HTTPS per inviare richieste all'hub IoT.The IoT Edge daemon always uses HTTPS to send requests to IoT Hub.

#### <a name="linux"></a>Linux

Aprire un editor nel terminale per configurare il daemon IoT Edge.

```bash
sudo systemctl edit iotedge
```

Immettere il testo seguente, sostituendo ** \<l'URL proxy>** con l'indirizzo e la porta del server proxy. Quindi, salvare e chiudere.

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

#### <a name="windows"></a>WINDOWS

Aprire una finestra di PowerShell come amministratore ed eseguire il comando seguente per modificare il registro con la nuova variabile di ambiente. Sostituire ** \<il>url proxy** con l'indirizzo e la porta del server proxy.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Riavviare IoT Edge per rendere effettive le modifiche apportate.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Configurare l'agente IoT Edge

L'agente IoT Edge è il primo modulo che viene avviato in un dispositivo IoT Edge. Viene avviato per la prima volta in base alle informazioni presenti nel file config.yaml di IoT Edge. L'agente IoT Edge si connette quindi all'hub IoT per recuperare i manifesti della distribuzione, che dichiarano quali altri moduli devono essere distribuiti nel dispositivo.

Questo passaggio viene eseguito una volta sul dispositivo IoT Edge durante la configurazione iniziale del dispositivo.

1. Aprire il file config.yaml nel dispositivo IoT Edge. Nei sistemi Linux questo file si trova in **/etc/iotedge/config.yaml**. Nei sistemi Windows questo file si trova in **C:\ProgramData\iotedge\config.yaml**. Il file di configurazione è protetto, pertanto sono necessari i privilegi amministrativi per accedervi. Nei sistemi Linux, `sudo` utilizzare il comando prima di aprire il file nell'editor di testo preferito. In Windows, aprire un editor di testo come Blocco note come amministratore e quindi aprire il file.

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

Dopo aver configurato il dispositivo IoT Edge per funzionare con il server proxy, è necessario continuare a dichiarare la variabile di ambiente HTTPS_PROXY nei manifesti di distribuzione futuri. È possibile modificare i manifesti di distribuzione usando la procedura guidata del portale di Azure o modificando un file JSON del manifesto di distribuzione.

Configurare sempre i due moduli di runtime, edgeAgent ed edgeHub, per comunicare attraverso il server proxy affinché possano mantenere una connessione con l'hub IoT. Se si rimuovono le informazioni sul proxy dal modulo edgeAgent, l'unico modo per ristabilire la connessione consiste nel modificare il file config.yaml nel dispositivo, come descritto nella sezione precedente.

Oltre ai moduli edgeAgent e edgeHub, altri moduli potrebbero richiedere la configurazione del proxy. Si tratta di moduli che devono accedere alle risorse di Azure oltre all'hub IoT, ad esempio l'archiviazione BLOB, e devono avere la variabile HTTPS_PROXY specificata per il modulo nel file manifesto di distribuzione.

La procedura seguente è applicabile per tutta la durata del dispositivo IoT Edge.

### <a name="azure-portal"></a>Portale di Azure

Quando si usa la procedura guidata **Imposta moduli** per creare le distribuzioni per i dispositivi IoT Edge, ogni modulo avrà una sezione **Variabili di ambiente** che è possibile usare per configurare le connessioni al server proxy.

Per configurare i moduli dell'agente IoT Edge e dell'hub IoT Edge, selezionare **Impostazioni runtime** nel primo passaggio della procedura guidata.

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
