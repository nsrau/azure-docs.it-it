---
title: Risoluzione dei problemi di Azure IoT Edge | Microsoft Docs
description: Risolvere problemi comuni e acquisire competenze di risoluzione dei problemi per Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: eb185a83ea154025e94c01c7b142a8d16fce91ab
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258348"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemi comuni e soluzioni per Azure IoT Edge

Se si verificano problemi durante l'esecuzione di Azure IoT Edge nel proprio ambiente, usare questo articolo come guida per la risoluzione. 

## <a name="standard-diagnostic-steps"></a>Procedura di diagnostica standard 

Quando si verifica un problema, è possibile ottenere informazioni sullo stato del dispositivo IoT Edge esaminando i registri del contenitore e i messaggi in transito da e verso il dispositivo. Usare i comandi e gli strumenti illustrati in questa sezione per raccogliere informazioni. 

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Controllare lo stato di IoT Edge Security Manager e i relativi log:

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
   # Displays logs from today, newest at the bottom.
 
   Get-WinEvent -ea SilentlyContinue `
   -FilterHashtable @{ProviderName= "iotedged";
     LogName = "application"; StartTime = [datetime]::Today} |
   select TimeCreated, Message |
   sort-object @{Expression="TimeCreated";Descending=$false}
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Se IoT Edge Security Manager non è in esecuzione, verificare il file di configurazione yaml

> [!WARNING]
> I file con estensione yalm non possono contenere tabulazioni per i rientri. In alternativa usare due spazi.

In Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

In Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Controllare i log dei contenitori per eventuali problemi

Non appena il daemon di sicurezza di IoT Edge è in esecuzione, esaminare i log dei contenitori per rilevare eventuali problemi. Iniziare con i contenitori distribuiti, quindi esaminare i contenitori che costituiscono il runtime di IoT Edge: agente di Edge e hub di Edge. I log dell'agente di Edge forniscono generalmente informazioni sul ciclo di vita di ogni contenitore. I log dell'hub di Edge forniscono informazioni su messaggistica e routing. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-edge-hub"></a>Visualizzare i messaggi che transitano nell'hub di Edge

Visualizzare i messaggi che transitano nell'hub di Edge e raccogliere informazioni dettagliate sugli aggiornamenti delle proprietà dei dispositivi con log dettagliati dei contenitori di runtime edgeAgent ed edgeHub. Per attivare i log dettagliati su questi contenitori, impostare `RuntimeLogLevel` nel file di configurazione yaml. Per aprire il file:

In Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

In Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Per impostazione predefinita, l'elemento `agent` sarà simile al seguente:

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

> [!WARNING]
> I file con estensione yalm non possono contenere tabulazioni per i rientri. In alternativa usare due spazi.

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

Salvare il file e riavviare il gestore sicurezza IoT Edge.

È anche possibile verificare i messaggi inviati tra l'hub IoT e i dispositivi IoT Edge. Visualizzare questi messaggi tramite l'estensione [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) per Visual Studio Code. Per altre informazioni, vedere [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/) (Strumento utile quando si sviluppa con Azure IoT).

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

## <a name="edge-agent-stops-after-about-a-minute"></a>L'agente Edge si arresta dopo circa un minuto

L'agente Edge viene avviato ed eseguito correttamente per circa un minuto, quindi si arresta. I log indicano che l'agente di Edge sta provando a connettersi all'hub IoT tramite AMQP e quindi tramite AMQP su WebSocket dopo circa 30 secondi. Se l'operazione non riesce, l'agente si chiude. 

Log di esempio dell'agente di Edge:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Causa radice
Una configurazione nella rete host impedisce all'agente Edge di raggiungere la rete. L'agente prova prima a connettersi tramite AMQP (porta 5671). Se l'operazione non riesce, prova con WebSocket (porta 443).

Il runtime di IoT Edge configura una rete per ognuno dei moduli usati per la comunicazione. In Linux questa rete è una rete di bridge. In Windows si usa la rete NAT. Questo problema è più comune nei dispositivi Windows con contenitori Windows che usano la rete NAT. 

### <a name="resolution"></a>Risoluzione
Verificare che sia presente un instradamento a Internet per gli indirizzi IP assegnati a questa rete di bridge/NAT. Talvolta una configurazione VPN nell'host esegue l'override della rete di IoT Edge. 

## <a name="edge-hub-fails-to-start"></a>L'hub di Edge non si avvia

L'hub di Edge non si avvia e stampa il messaggio seguente nei log: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Causa radice
Un altro processo nel computer host è associato alla porta 443. L'hub di Edge esegue il mapping delle porte 5671 e 443 per l'uso negli scenari di gateway. Questo mapping delle porte non riesce se un altro processo è già associato a questa porta. 

### <a name="resolution"></a>Risoluzione
Individuare e arrestare il processo che usa la porta 443. Questo processo è in genere un server Web.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>L'agente di Edge non può accedere all'immagine di un modulo (403)
Un contenitore non viene eseguito correttamente e i log dell'agente di Edge indicano un errore 403. 

### <a name="root-cause"></a>Causa radice
L'agente di Edge non è autorizzato ad accedere all'immagine del modulo. 

### <a name="resolution"></a>Risoluzione
Verificare che le credenziali del registro siano specificate correttamente nel manifesto di distribuzione

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Se il nome host non è valido, l'esecuzione del daemon di sicurezza di IoT Edge ha esito negativo.

Il comando `sudo journalctl -u iotedge` ha esito negativo e viene stampato il messaggio seguente: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Causa radice
Il runtime di IoT Edge può supportare solo nomi host contenenti meno di 64 caratteri. In genere non è un problema per i computer fisici, ma può esserlo quando si configura il runtime in una macchina virtuale. I nomi host generati automaticamente per le macchine virtuali Windows ospitate in Azure, in particolare, tendono a essere lunghi. 

### <a name="resolution"></a>Risoluzione
Quando viene visualizzato questo errore, è possibile risolvere il problema configurando il nome DNS della macchina virtuale e quindi impostando il nome DNS come nome host nel comando di configurazione.

1. Nel portale di Azure passare alla pagina di panoramica della macchina virtuale. 
2. Selezionare **Configura** sotto Nome DNS. Se la macchina virtuale dispone già di un nome DNS configurato, non è necessario configurarne uno nuovo. 

   ![Configurare il nome DNS](./media/troubleshoot/configure-dns.png)

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

### <a name="root-cause"></a>Causa radice
L'hub Edge, incluso nel runtime di Edge, è ottimizzato in termini di prestazioni per impostazione predefinita e tenta di allocare blocchi di memoria di grandi dimensioni. Questa situazione non è ideale per i dispositivi Edge con limiti e può causare problemi di stabilità.

### <a name="resolution"></a>Risoluzione
Per l'hub Edge impostare una variabile di ambiente **OptimizeForPerformance** su **false**. A questo scopo è possibile procedere in due modi:

Nell'interfaccia utente: 

Nel portale in *Dettagli dispositivo*->*Imposta moduli*->*Configura impostazioni avanzate per il runtime di IoT Edge* creare una variabile di ambiente denominata *OptimizeForPerformance* impostata su *false* per l'*Hub Edge*.

![optimizeforperformance][img-optimize-for-perf]

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

### <a name="root-cause"></a>Causa radice
Il comando di PowerShell `Get-WinEvent` si basa su una voce del Registro di sistema che deve essere presente per trovare i log da uno specifico `ProviderName`.

### <a name="resolution"></a>Risoluzione
Impostare una voce del Registro di sistema per il daemon di IoT Edge. Creare un file **iotedge.reg** con il contenuto seguente e importarlo nel Registro di sistema di Windows facendovi doppio clic o usando il comando `reg import iotedge.reg`:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```


## <a name="next-steps"></a>Passaggi successivi
Se si ritiene di aver rilevato un bug nella piattaforma di IoT Edge, [inviare il problema](https://github.com/Azure/iotedge/issues) in modo da poter migliorare l'esperienza. 

<!-- Images -->
[img-optimize-for-perf]: ./media/troubleshoot/OptimizeForPerformanceFalse.png
