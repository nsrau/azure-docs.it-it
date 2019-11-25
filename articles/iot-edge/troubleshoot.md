---
title: Risoluzione dei problemi - Azure IoT Edge | Microsoft Docs
description: Usare questo articolo per acquisire competenze diagnostiche standard per Azure IoT Edge, come il recupero dello stato e dei log dei componenti e la risoluzione di problemi comuni
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cc05d820a409500e66e2525654954e4dddf48e4b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457257"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemi comuni e soluzioni per Azure IoT Edge

Se si verificano problemi durante l'esecuzione di Azure IoT Edge nel proprio ambiente, usare questo articolo come guida per la risoluzione.

## <a name="run-the-iotedge-check-command"></a>Run the iotedge 'check' command

Your first step when troubleshooting IoT Edge should be to use the `check` command, which performs a collection of configuration and connectivity tests for common issues. The `check` command is available in [release 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) and later.

You can run the `check` command as follows, or include the `--help` flag to see a complete list of options:

* In Linux:

  ```bash
  sudo iotedge check
  ```

* In Windows:

  ```powershell
  iotedge check
  ```

The types of checks run by the tool can be classified as:

* Configuration checks: Examines details that could prevent Edge devices from connecting to the cloud, including issues with *config.yaml* and the container engine.
* Connection checks: Verifies the IoT Edge runtime can access ports on the host device and all the IoT Edge components can connect to the IoT Hub.
* Production readiness checks: Looks for recommended production best practices, such as the state of device certificate authority (CA) certificates and module log file configuration.

For a complete list of diagnostic checks, see [Built-in troubleshooting functionality](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="standard-diagnostic-steps"></a>Procedura di diagnostica standard

If you encounter an issue, you can learn more about the state of your IoT Edge device by reviewing the container logs and the messages that pass to and from the device. Usare i comandi e gli strumenti illustrati in questa sezione per raccogliere informazioni.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Check the status of the IoT Edge Security Manager and its logs

In Linux:
- Per visualizzare lo stato di IoT Edge Security Manager:

   ```bash
   sudo systemctl status iotedge
   ```

- Per visualizzare i log di IoT Edge Security Manager:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- Per visualizzare log più dettagliati di IoT Edge Security Manager:

   - Modificare le impostazioni del daemon iotedge:

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - Aggiornare le righe seguenti:
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - Riavviare il daemon di sicurezza di IoT Edge:
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

In Windows:
- Per visualizzare lo stato di IoT Edge Security Manager:

   ```powershell
   Get-Service iotedge
   ```

- Per visualizzare i log di IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Se IoT Edge Security Manager non è in esecuzione, verificare il file di configurazione yaml

> [!WARNING]
> YAML files cannot contain tabs as indentation. In alternativa usare due spazi.

In Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

In Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Controllare i log dei contenitori per eventuali problemi

Non appena il daemon di sicurezza di IoT Edge è in esecuzione, esaminare i log dei contenitori per rilevare eventuali problemi. Start with your deployed containers, then look at the containers that make up the IoT Edge runtime: edgeAgent and edgeHub. The IoT Edge agent logs typically provide info on the lifecycle of each container. The IoT Edge hub logs provide info on messaging and routing. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>View the messages going through the IoT Edge hub

You can view the messages going through the IoT Edge hub, and gather insights from verbose logs from the runtime containers. Per attivare i log dettagliati su questi contenitori, impostare `RuntimeLogLevel` nel file di configurazione yaml. Per aprire il file:

In Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

In Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Per impostazione predefinita, l'elemento `agent` avrà un aspetto simile all'esempio seguente:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Sostituire `env: {}` con:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > I file con estensione yalm non possono contenere tabulazioni per i rientri. In alternativa usare due spazi.

Salvare il file e riavviare il gestore sicurezza IoT Edge.

È anche possibile verificare i messaggi inviati tra l'hub IoT e i dispositivi IoT Edge. Visualizzare questi messaggi usando l'estensione del [Azure IoT Hub Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (in precedenza estensione Azure IoT Toolkit) per Visual Studio Code. Per altre informazioni, vedere [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/) (Strumento utile quando si sviluppa con Azure IoT).

### <a name="restart-containers"></a>Riavviare i contenitori
Dopo avere esaminato i log e i messaggi per raccogliere informazioni, è possibile provare a riavviare i contenitori:

```
iotedge restart <container name>
```

Riavviare i contenitori di runtime di IoT Edge:

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Riavviare IoT Edge Security Manager

Se il problema persiste, è possibile provare a riavviare IoT Edge Security Manager.

In Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

In Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge agent stops after about a minute

The edgeAgent module starts and runs successfully for about a minute, then stops. The logs indicate that the IoT Edge agent attempts to connect to IoT Hub over AMQP, and then attempts to connect using AMQP over WebSocket. When that fails, the IoT Edge agent exits. 

Example edgeAgent logs:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

**Root cause**

A networking configuration on the host network is preventing the IoT Edge agent from reaching the network. L'agente prova prima a connettersi tramite AMQP (porta 5671). Se la connessione non riesce, prova WebSocket (porta 443).

Il runtime di IoT Edge configura una rete per ognuno dei moduli usati per la comunicazione. In Linux questa rete è una rete di bridge. In Windows si usa la rete NAT. Questo problema è più comune nei dispositivi Windows con contenitori Windows che usano la rete NAT. 

**Risoluzione**

Verificare che sia presente un instradamento a Internet per gli indirizzi IP assegnati a questa rete di bridge/NAT. Talvolta una configurazione VPN nell'host esegue l'override della rete di IoT Edge. 

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge hub fails to start

The edgeHub module fails to start, and prints the following message to the logs: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

**Root cause**

Un altro processo nel computer host è associato alla porta 443. The IoT Edge hub maps ports 5671 and 443 for use in gateway scenarios. Questo mapping delle porte non riesce se un altro processo è già associato a questa porta. 

**Risoluzione**

Individuare e arrestare il processo che usa la porta 443. Questo processo è in genere un server Web.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge agent can't access a module's image (403)
A container fails to run, and the edgeAgent logs show a 403 error. 

**Root cause**

The Iot Edge agent doesn't have permissions to access a module's image. 

**Risoluzione**

Verificare che le credenziali del registro siano specificate correttamente nel manifesto di distribuzione

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Se il nome host non è valido, l'esecuzione del daemon di sicurezza di IoT Edge ha esito negativo.

Il comando `sudo journalctl -u iotedge` ha esito negativo e viene stampato il messaggio seguente: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Root cause**

Il runtime di IoT Edge può supportare solo nomi host contenenti meno di 64 caratteri. I computer fisici non hanno in genere nomi host lunghi, ma il problema è più comune in una macchina virtuale. I nomi host generati automaticamente per le macchine virtuali Windows ospitate in Azure, in particolare, tendono a essere lunghi. 

**Risoluzione**

Quando viene visualizzato questo errore, è possibile risolvere il problema configurando il nome DNS della macchina virtuale e quindi impostando il nome DNS come nome host nel comando di configurazione.

1. Nel portale di Azure passare alla pagina di panoramica della macchina virtuale. 
2. Selezionare **Configura** sotto Nome DNS. Se la macchina virtuale dispone già di un nome DNS configurato, non è necessario configurarne uno nuovo. 

   ![Configurare il nome DNS della macchina virtuale](./media/troubleshoot/configure-dns.png)

3. Specificare un valore per **Etichetta del nome DNS** e selezionare **Salva**.
4. Copiare il nuovo nome DNS, che deve essere nel formato  **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com**.
5. All'interno della macchina virtuale, usare il comando seguente per configurare il runtime di IoT Edge con il nome DNS:

   - In Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - In Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Problemi di stabilità nei dispositivi con risorse limitate 
È possibile che si verifichino problemi di stabilità nei dispositivi con limiti come Raspberry Pi, in particolare se usati come gateway. I problemi includono eccezioni di memoria insufficiente nel modulo dell'hub Edge, impossibilità di connessione dei dispositivi downstream o interruzione dell'invio dei messaggi dei dati di telemetria da parte del dispositivo dopo alcune ore.

**Root cause**

The IoT Edge hub, which is part of the IoT Edge runtime, is optimized for performance by default and attempts to allocate large chunks of memory. Questa ottimizzazione non è ideale per i dispositivi Edge con limiti e può causare problemi di stabilità.

**Risoluzione**

For the IoT Edge hub, set an environment variable **OptimizeForPerformance** to **false**. A questo scopo è possibile procedere in due modi:

Nell'interfaccia utente: 

In the portal, navigate to **Device Details** > **Set Modules** > **Configure advanced Edge Runtime settings**. Create an environment variable for the Edge Hub module called *OptimizeForPerformance* that is set to *false*.

![OptimizeForPerformance impostato su false](./media/troubleshoot/optimizeforperformance-false.png)

**OR**

Nel manifesto della distribuzione:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```
## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Non è possibile ottenere i log del daemon di IoT Edge su Windows
Se si verifica un'eccezione di tipo EventLogException quando si usa `Get-WinEvent` su Windows, verificare le voci del Registro di sistema.

**Root cause**

Il comando di PowerShell `Get-WinEvent` si basa su una voce del Registro di sistema che deve essere presente per trovare i log da uno specifico `ProviderName`.

**Risoluzione**

Impostare una voce del Registro di sistema per il daemon di IoT Edge. Creare un file **iotedge.reg** con il contenuto seguente e importarlo nel Registro di sistema di Windows facendovi doppio clic o usando il comando `reg import iotedge.reg`:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>Il modulo IoT Edge non riesce a inviare un messaggio a edgeHub con errore 404

Il modulo IoT Edge non riesce a inviare un messaggio a edgeHub con errore `Module not found` 404. Il daemon di IoT Edge stampa il messaggio seguente nei log: 

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 ) 
```

**Root cause**

Per motivi di sicurezza, il daemon di IoT Edge impone l'identificazione del processo a tutti i moduli che si connettono a edgeHub. Verifica che tutti i messaggi inviati da un modulo provengano dall'ID del processo principale del modulo. Se viene inviato un messaggio da un modulo da un ID di processo diverso rispetto a quello inizialmente stabilito, rifiuterà il messaggio con un messaggio di errore 404.

**Risoluzione**

As of version 1.0.7, all module processes are authorized to connect. If upgrading to 1.0.7 isn't possible, complete the following steps. For more information, see the [1.0.7 release changelog](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Verificare che il modulo IoT Edge personalizzato usi sempre lo stesso ID di processo per inviare messaggi a edgeHub. For instance, make sure to `ENTRYPOINT` instead of `CMD` command in your Docker file, since `CMD` will lead to one process ID for the module and another process ID for the bash command running the main program whereas `ENTRYPOINT` will lead to a single process ID.


## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Regole di configurazione di firewall e porte per la distribuzione di IoT Edge
Azure IoT Edge allows communication from an on-premises server to Azure cloud using supported IoT Hub protocols, see [choosing a communication protocol](../iot-hub/iot-hub-devguide-protocols.md). Per una maggiore sicurezza, i canali di comunicazione tra Azure IoT Edge e hub IoT di Azure sono sempre configurati per essere in uscita. Questa configurazione si basa sul [modello di comunicazione assistita dei servizi](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), che consente di ridurre la superficie di attacco esplorabile da un'entità dannosa. Le comunicazioni in ingresso sono necessarie solo per scenari specifici in cui l'hub IoT di Azure deve eseguire il push dei messaggi al dispositivo Azure IoT Edge. I messaggi da cloud a dispositivo sono protetti tramite canali sicuri TLS e possono essere protetti ulteriormente tramite certificati X.509 e moduli di dispositivo TPM. Il gestore sicurezza di Azure IoT Edge stabilisce come attivare la comunicazione, vedere [Gestore sicurezza di Azure IoT Edge](../iot-edge/iot-edge-security-manager.md).

Sebbene IoT Edge offra una configurazione avanzata per la protezione del runtime e dei moduli distribuiti di Azure IoT Edge, dipende comunque dal computer e dalla configurazione di rete sottostanti. Hence, it is imperative to ensure proper network and firewall rules are set up for secure edge to cloud communication. The following table can be used as a guideline when configuration firewall rules for the underlying servers where Azure IoT Edge runtime is hosted:

|Protocol|Porta|In ingresso|In uscita|Guida|
|--|--|--|--|--|
|MQTT|8883|BLOCCATO (impostazione predefinita)|BLOCCATO (impostazione predefinita)|<ul> <li>Configurare i dati in uscita in modo che siano Aperti quando si usa MQTT come protocollo di comunicazione.<li>1883 per MQTT non è supportato da IoT Edge. <li>Le connessioni in ingresso devono essere bloccate.</ul>|
|AMQP|5671|BLOCCATO (impostazione predefinita)|APERTO (impostazione predefinita)|<ul> <li>Protocollo di comunicazione predefinito per IoT Edge. <li> Deve essere configurato per essere Aperto se Azure IoT Edge non è configurato per altri protocolli supportati o AMQP è il protocollo di comunicazione desiderato.<li>5672 per AMQP non è supportato da IoT Edge.<li>Bloccare questa porta quando Azure IoT Edge usa un protocollo supportato dall'hub IoT diverso.<li>Le connessioni in ingresso devono essere bloccate.</ul></ul>|
|HTTPS|443|BLOCCATO (impostazione predefinita)|APERTO (impostazione predefinita)|<ul> <li>Configurare le connessioni in uscita come Aperto sulla porta 443 per il provisioning di IoT Edge. Questa configurazione è necessaria quando si usano script manuali o il servizio Device Provisioning di Azure IoT. <li>La connessione In ingresso deve essere Aperta solo per scenari specifici: <ul> <li>  Se si dispone di un gateway trasparente con dispositivi foglia che possono inviare richieste di metodo. In questo caso, non occorre che la porta 443 sia aperta a reti esterne per connettersi a IotHub o fornire servizi IoTHub tramite Azure IoT Edge. Pertanto, la regola in ingresso potrebbe essere limitata all'apertura solo in ingresso dalla rete interna. <li> Per scenari da client a dispositivo (C2D).</ul><li>80 per HTTP non è supportato da IoT Edge.<li>Se i protocolli non HTTP (ad esempio, AMQP o MQTT) non possono essere configurati nell'azienda. I messaggi possono essere inviati tramite WebSocket. In questo caso, la porta 443 verrà usata per la comunicazione WebSocket.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge Agent module continually reports 'empty config file' and no modules start on the device

The device has trouble starting modules defined in the deployment. Only the edgeAgent is running but continually reporting 'empty config file...'.

**Root cause**

By default, IoT Edge starts modules in their own isolated container network. The device may be having trouble with DNS name resolution within this private network.

**Risoluzione**


**Option 1: Set DNS server in container engine settings**

Specify the DNS server for your environment in the container engine settings which will apply to all container modules started by the engine. Create a file named `daemon.json` specifying the DNS server to use. ad esempio:

```
{
    "dns": ["1.1.1.1"]
}
```

The above example sets the DNS server to a publicly accessible DNS service. If the edge device cannot access this IP from its environment, replace it with DNS server address that is accessible.

Place `daemon.json` in the right location for your platform: 

| Piattaforma | Località |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows host with Windows containers | `C:\ProgramData\iotedge-moby\config` |

If the location already contains `daemon.json` file, add the **dns** key to it and save the file.

*Restart the container engine for the updates to take effect*

| Piattaforma | Comando |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Admin Powershell) | `Restart-Service iotedge-moby -Force` |

**Option 2: Set DNS server in IoT Edge deployment per module**

You can set DNS server for each module's *createOptions* in the IoT Edge deployment. ad esempio:

```
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Be sure to set this for the *edgeAgent* and *edgeHub* modules as well. 

## <a name="next-steps"></a>Passaggi successivi
Se si ritiene di aver rilevato un bug nella piattaforma di IoT Edge, [Inviare un problema](https://github.com/Azure/iotedge/issues) in modo da poter migliorare l'esperienza. 

In caso di altre domande, creare una [Richiesta di supporto](https://portal.azure.com/#create/Microsoft.Support) per assistenza. 

