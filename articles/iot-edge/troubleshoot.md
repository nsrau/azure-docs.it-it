---
title: Risoluzione dei problemi - Azure IoT Edge | Microsoft Docs
description: Usare questo articolo per apprendere le competenze diagnostiche standard per Azure IoT Edge, ad esempio il recupero dello stato dei componenti e dei log
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0e4ec7127df288ec1818df307da1ea9824141309
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87902457"
---
# <a name="troubleshoot-your-iot-edge-device"></a>Risolvere i problemi del dispositivo IoT Edge

Se si verificano problemi durante l'esecuzione di Azure IoT Edge nell'ambiente in uso, usare questo articolo come guida per la risoluzione dei problemi e la diagnostica.

## <a name="run-the-check-command"></a>Eseguire il comando ' check '

Il primo passaggio per la risoluzione dei problemi di IoT Edge deve essere quello di usare il `check` comando, che esegue una raccolta di test di configurazione e connettività per i problemi comuni. Il `check` comando è disponibile in [Release 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) e versioni successive.

>[!NOTE]
>Lo strumento per la risoluzione dei problemi non può eseguire controlli di connettività se il dispositivo IoT Edge è dietro a un server proxy.

È possibile eseguire il `check` comando come indicato di seguito o includere il `--help` flag per visualizzare un elenco completo delle opzioni:

In Linux:

```bash
sudo iotedge check
```

In Windows:

```powershell
iotedge check
```

Lo strumento di risoluzione dei problemi esegue molti controlli ordinati nelle tre categorie seguenti:

* I *controlli di configurazione* esaminano i dettagli che potrebbero impedire la connessione dei dispositivi IOT Edge al cloud, inclusi i problemi con *config. YAML* e il motore di gestione dei contenitori.
* I *controlli di connessione* verificano che il runtime di IOT Edge possa accedere alle porte sul dispositivo host e che tutti i componenti IOT Edge possano connettersi all'hub Internet. Questo set di controlli restituisce errori se il dispositivo IoT Edge si trova dietro un proxy.
* I *controlli di conformità* per la produzione cercano le procedure consigliate per la produzione, ad esempio lo stato dei certificati dell'autorità di certificazione (CA) del dispositivo e la configurazione del file di registro del modulo.

Per informazioni su ognuno dei controlli diagnostici eseguiti da questo strumento, incluse le operazioni da eseguire se viene visualizzato un errore o un avviso, vedere [IOT Edge risolvere i problemi relativi ai controlli](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-support-bundle-command"></a>Raccogliere le informazioni di debug con il comando ' support-bundle '

Quando è necessario raccogliere i log da un dispositivo IoT Edge, il modo più pratico consiste nell'usare il `support-bundle` comando. Per impostazione predefinita, questo comando raccoglie il modulo IoT Edge Security Manager e i log del motore del contenitore, l' `iotedge check` output JSON e altre informazioni di debug utili. Li comprime in un singolo file per semplificare la condivisione. Il `support-bundle` comando è disponibile in [Release 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) e versioni successive.

Eseguire il `support-bundle` comando con il `--since` flag per specificare per quanto tempo dal passato si desidera ottenere i log. Ad esempio `6h` , otterrà i log dalle ultime sei ore, `6d` dagli ultimi sei giorni, `6m` dagli ultimi sei minuti e così via. Includere il `--help` flag per visualizzare un elenco completo delle opzioni.

In Linux:

```bash
sudo iotedge support-bundle --since 6h
```

In Windows:

```powershell
iotedge support-bundle --since 6h
```

> [!WARNING]
> L'output del `support-bundle` comando può contenere nomi host, dispositivi e moduli, informazioni registrate dai moduli e così via. Tenere presente questo problema se si condivide l'output in un forum pubblico.

## <a name="check-your-iot-edge-version"></a>Controllare la versione di IoT Edge

Se si sta eseguendo una versione precedente di IoT Edge, l'aggiornamento può risolvere il problema. Lo `iotedge check` strumento verifica che il daemon di sicurezza IoT Edge sia la versione più recente, ma non controlla le versioni dei moduli dell'hub IOT Edge e dell'agente. Per verificare la versione dei moduli di runtime nel dispositivo, usare i comandi `iotedge logs edgeAgent` e `iotedge logs edgeHub` . Il numero di versione è dichiarato nei log all'avvio del modulo.

Per istruzioni su come aggiornare il dispositivo, vedere [aggiornare il daemon di sicurezza di IOT Edge e il runtime](how-to-update-iot-edge.md).

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Verificare lo stato di IoT Edge Security Manager e dei relativi log

Il [IOT Edge Security Manager](iot-edge-security-manager.md) è responsabile di operazioni quali l'inizializzazione del sistema IOT Edge all'avvio e il provisioning dei dispositivi. Se IoT Edge non viene avviato, è possibile che i log del gestore sicurezza forniscano informazioni utili.

In Linux:

* Visualizzare lo stato di IoT Edge Security Manager:

   ```bash
   sudo systemctl status iotedge
   ```

* Visualizzare i log di IoT Edge Security Manager:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Visualizzare i log più dettagliati di IoT Edge Security Manager:

  * Modificare le impostazioni del daemon di IoT Edge:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Aggiornare le righe seguenti:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Riavviare il daemon di sicurezza di IoT Edge:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

In Windows:

* Visualizzare lo stato di IoT Edge Security Manager:

   ```powershell
   Get-Service iotedge
   ```

* Visualizzare i log di IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* Visualizzare solo gli ultimi 5 minuti dei log di IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* Visualizzare i log più dettagliati di IoT Edge Security Manager:

  * Aggiungere una variabile di ambiente a livello di sistema:

      ```powershell
      [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
      ```

  * Riavviare il daemon di sicurezza di IoT Edge:

      ```powershell
      Restart-Service iotedge
      ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Se IoT Edge Security Manager non è in esecuzione, verificare il file di configurazione YAML

> [!WARNING]
> I file YAML non possono contenere tabulazioni come rientri. In alternativa usare due spazi. Gli elementi di primo livello non devono contenere spazi iniziali.

In Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

In Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
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

## <a name="check-container-logs-for-issues"></a>Controllare i log dei contenitori per eventuali problemi

Una volta che il daemon di sicurezza di IoT Edge è in esecuzione, esaminare i log dei contenitori per rilevare i problemi. Iniziare con i contenitori distribuiti, quindi esaminare i contenitori che costituiscono il runtime di IoT Edge: edgeAgent e edgeHub. I log dell'agente di IoT Edge in genere forniscono informazioni sul ciclo di vita di ogni contenitore. I log dell'hub IoT Edge forniscono informazioni sulla messaggistica e sul routing.

```cmd
iotedge logs <container name>
```

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Visualizzare i messaggi che passano attraverso l'hub IoT Edge

È possibile visualizzare i messaggi che passano attraverso l'hub IoT Edge e raccogliere informazioni dettagliate dai log dettagliati dai contenitori di Runtime. Per attivare i log dettagliati su questi contenitori, impostare `RuntimeLogLevel` nel file di configurazione yaml. Per aprire il file:

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
   > I file con estensione yalm non possono contenere tabulazioni per i rientri. In alternativa usare due spazi. Gli elementi di primo livello non possono contenere spazi vuoti iniziali.

Salvare il file e riavviare il gestore sicurezza IoT Edge.

È anche possibile verificare i messaggi inviati tra l'hub IoT e i dispositivi IoT Edge. Per visualizzare questi messaggi, è necessario usare l' [estensione Hub Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Per altre informazioni, vedere [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/) (Strumento utile quando si sviluppa con Azure IoT).

## <a name="restart-containers"></a>Riavviare i contenitori

Dopo avere esaminato i log e i messaggi per raccogliere informazioni, è possibile provare a riavviare i contenitori:

```cmd
iotedge restart <container name>
```

Riavviare i contenitori di runtime di IoT Edge:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

## <a name="check-your-firewall-and-port-configuration-rules"></a>Controllare le regole di configurazione del firewall e della porta

Azure IoT Edge consente la comunicazione da un server locale al cloud di Azure usando i protocolli dell'hub informazioni supportati, vedere [scelta di un protocollo di comunicazione](../iot-hub/iot-hub-devguide-protocols.md). Per una maggiore sicurezza, i canali di comunicazione tra Azure IoT Edge e hub IoT di Azure sono sempre configurati per essere in uscita. Questa configurazione si basa sul [modello di comunicazione assistita dei servizi](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), che consente di ridurre la superficie di attacco esplorabile da un'entità dannosa. Le comunicazioni in ingresso sono necessarie solo per scenari specifici in cui l'hub IoT di Azure deve eseguire il push dei messaggi al dispositivo Azure IoT Edge. I messaggi da cloud a dispositivo sono protetti tramite canali sicuri TLS e possono essere protetti ulteriormente tramite certificati X.509 e moduli di dispositivo TPM. Il gestore sicurezza di Azure IoT Edge stabilisce come attivare la comunicazione, vedere [Gestore sicurezza di Azure IoT Edge](../iot-edge/iot-edge-security-manager.md).

Sebbene IoT Edge offra una configurazione avanzata per la protezione del runtime e dei moduli distribuiti di Azure IoT Edge, dipende comunque dal computer e dalla configurazione di rete sottostanti. Di conseguenza, è fondamentale assicurarsi che le regole di rete e del firewall appropriate siano configurate per la comunicazione tra Edge e cloud. La tabella seguente può essere usata come linea guida per le regole del firewall di configurazione per i server sottostanti in cui è ospitato Azure IoT Edge Runtime:

|Protocollo|Porta|In arrivo|In uscita|Indicazioni|
|--|--|--|--|--|
|MQTT|8883|BLOCCATO (impostazione predefinita)|BLOCCATO (impostazione predefinita)|<ul> <li>Configurare i dati in uscita in modo che siano Aperti quando si usa MQTT come protocollo di comunicazione.<li>1883 per MQTT non è supportato da IoT Edge. <li>Le connessioni in ingresso devono essere bloccate.</ul>|
|AMQP|5671|BLOCCATO (impostazione predefinita)|APERTO (impostazione predefinita)|<ul> <li>Protocollo di comunicazione predefinito per IoT Edge. <li> Deve essere configurato per essere Aperto se Azure IoT Edge non è configurato per altri protocolli supportati o AMQP è il protocollo di comunicazione desiderato.<li>5672 per AMQP non è supportato da IoT Edge.<li>Bloccare questa porta quando Azure IoT Edge usa un protocollo supportato dall'hub IoT diverso.<li>Le connessioni in ingresso devono essere bloccate.</ul></ul>|
|HTTPS|443|BLOCCATO (impostazione predefinita)|APERTO (impostazione predefinita)|<ul> <li>Configurare le connessioni in uscita come Aperto sulla porta 443 per il provisioning di IoT Edge. Questa configurazione è necessaria quando si usano script manuali o il servizio Device Provisioning di Azure IoT. <li>La connessione In ingresso deve essere Aperta solo per scenari specifici: <ul> <li>  Se si dispone di un gateway trasparente con dispositivi foglia che possono inviare richieste di metodo. In questo caso, non occorre che la porta 443 sia aperta a reti esterne per connettersi a IotHub o fornire servizi IoTHub tramite Azure IoT Edge. Pertanto, la regola in ingresso potrebbe essere limitata all'apertura solo in ingresso dalla rete interna. <li> Per scenari da client a dispositivo (C2D).</ul><li>80 per HTTP non è supportato da IoT Edge.<li>Se i protocolli non HTTP (ad esempio, AMQP o MQTT) non possono essere configurati nell'azienda. I messaggi possono essere inviati tramite WebSocket. In questo caso, la porta 443 verrà usata per la comunicazione WebSocket.</ul>|

## <a name="next-steps"></a>Passaggi successivi

Se si ritiene di aver rilevato un bug nella piattaforma di IoT Edge, [Inviare un problema](https://github.com/Azure/iotedge/issues) in modo da poter migliorare l'esperienza.

In caso di altre domande, creare una [Richiesta di supporto](https://portal.azure.com/#create/Microsoft.Support) per assistenza.
