---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/26/2019
ms.author: alkohli
ms.openlocfilehash: f3bb391dceb1948820d00c0d09229f2c106ffc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68601344"
---
In un dispositivo Data Box Edge con il ruolo di calcolo configurato, è disponibile un sottoinsieme di comandi docker per monitorare o risolvere i problemi relativi ai moduli. Per visualizzare un elenco dei comandi disponibili, [connettersi all'interfaccia](#connect-to-the-powershell-interface) di PowerShell e usare la `dkrdbe` funzione.

```powershell
[10.100.10.10]: PS>dkrdbe -?
Usage: dkrdbe COMMAND

Commands:
   image [prune]
   images
   inspect
   login
   logout
   logs
   port
   ps
   pull
   start
   stats
   stop
   system [df]
   top

[10.100.10.10]: PS>
```
La tabella seguente contiene una breve `dkrdbe`descrizione dei comandi disponibili per:

|command  |Descrizione |
|---------|---------|
|`image`     | Gestire le immagini. Per rimuovere le immagini inutilizzate, utilizzare:`dkrdbe image prune -a -f`       |
|`images`     | Elencare le immagini         |
|`inspect`     | Restituire informazioni di basso livello sugli oggetti DockerReturn low-level information on Docker objects         |
|`login`     | Accedere a un registro Docker         |
|`logout`     | Disconnettersi da un Registro di sistema Docker         |
|`logs`     | Recuperare i log di un contenitore        |
|`port`     | Elencare i mapping delle porte o un mapping specifico per il contenitoreList port mappings or a specific mapping for the container        |
|`ps`     | Elencare i contenitori        |
|`pull`     | Estrarre un'immagine o un repository da un Registro di sistema         |
|`start`     | Avviare uno o più contenitori arrestatiStart one or more stopped containers         |
|`stats`     | Visualizzare un flusso live delle statistiche sull'utilizzo delle risorse dei contenitori         |
|`stop`     | Arrestare uno o più contenitori in esecuzione        |
|`system`     | Finestra Mobile gestione         |
|`top`     | Visualizzare i processi in esecuzione di un contenitoreDisplay the running processes of a container         |

Per ottenere informazioni su qualsiasi `dkrdbe <command-name> --help`comando disponibile, utilizzare .

Ad esempio, per comprendere `port` l'utilizzo del comando, digitare:

```powershell
[10.100.10.10]: P> dkrdbe port --help

Usage:  dkr port CONTAINER [PRIVATE_PORT[/PROTO]]

List port mappings or a specific mapping for the container
[10.100.10.10]: P> dkrdbe login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[10.100.10.10]: PS>
```

I comandi disponibili `dkrdbe` per la funzione utilizzano gli stessi parametri utilizzati per i normali comandi docker. Per le opzioni e i parametri utilizzati con il comando docker, passare a Utilizzare la riga di [comando Docker](https://docs.docker.com/engine/reference/commandline/docker/).

### <a name="to-check-if-the-module-deployed-successfully"></a>Per verificare se il modulo è stato distribuito correttamente

I moduli di calcolo sono contenitori in cui è implementata una logica di business. Per verificare se un modulo di `ps` calcolo viene distribuito correttamente, eseguire il comando e verificare se il contenitore (corrispondente al modulo di calcolo) è in esecuzione.

Per ottenere l'elenco di tutti i contenitori (inclusi `ps -a` quelli in pausa), eseguire il comando.

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

Se si è verificato un errore durante la creazione `logs edgeAgent`dell'immagine contenitore o durante il pull dell'immagine, eseguire .  `EdgeAgent`è il contenitore di runtime IoT Edge responsabile del provisioning di altri contenitori.

Poiché `logs edgeAgent` esegue il dump di tutti i registri, un `--tail 20`buon modo per visualizzare gli errori recenti consiste nell'utilizzare l'opzione .


```powershell
[10.100.10.10]: PS>dkrdbe logs edgeAgent --tail 20
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Connected socket /var/run/iotedge/mgmt.sock
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Sending request http://mgmt.sock/modules?api-version=2018-06-28
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Getting edge agent config...
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Obtained edge agent config
2019-02-28 23:38:23.469 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Edgelet.ModuleManagementHttpClient] - Received a valid Http response from unix:///var/run/iotedge/mgmt.soc
k for List modules
--------------------CUT---------------------
--------------------CUT---------------------
08:28.1007774+00:00","restartCount":0,"lastRestartTimeUtc":"2019-02-26T20:08:28.1007774+00:00","runtimeStatus":"running","version":"1.0","status":"running","restartPolicy":"always
","type":"docker","settings":{"image":"edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64","imageHash":"sha256:47778be0602fb077d7bc2aaae9b0760fbfc7c058bf4df192f207ad6cbb96f7cc","c
reateOptions":"{\"HostConfig\":{\"Binds\":[\"/home/hcsshares/share4-dl460:/home/input\",\"/home/hcsshares/share4-iot:/home/output\"]}}"},"env":{}}
2019-02-28 23:38:28.480 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Planners.HealthRestartPlanner] - HealthRestartPlanner created Plan, with 0 command(s).
```

### <a name="to-get-container-logs"></a>Per ottenere i log del contenitoreTo get container logs

Per ottenere i log per un contenitore specifico, elencare innanzitutto il contenitore e quindi ottenere i log per il contenitore a cui si è interessati.

1. [Connettersi all'interfaccia](#connect-to-the-powershell-interface)di PowerShell .
2. Per ottenere l'elenco dei `ps` contenitori in esecuzione, eseguire il comando .

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. Prendere nota dell'ID contenitore per il contenitore per il quali sono necessari i log.

4. Per ottenere i log per un `logs` contenitore specifico, eseguire il comando che fornisce l'ID del contenitore.

    ```powershell
    [10.100.10.10]: PS>dkrdbe logs d99e2f91d9a8
    02/26/2019 18:21:45: Info: Opening module client connection.
    02/26/2019 18:21:46: Info: Initializing with input: /home/input, output: /home/output.
    02/26/2019 18:21:46: Info: IoT Hub module client initialized.
    02/26/2019 18:22:24: Info: Received message: 1, SequenceNumber: 0 CorrelationId: , MessageId: 081886a07e694c4c8f245a80b96a252a Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\__Microsoft Data Box Edge__\\Upload\\Errors.xml","ShareName":"share4-dl460"}]
    02/26/2019 18:22:24: Info: Moving input file: /home/input/__Microsoft Data Box Edge__/Upload/Errors.xml to /home/output/__Microsoft Data Box Edge__/Upload/Errors.xml
    02/26/2019 18:22:24: Info: Processed event.
    02/26/2019 18:23:38: Info: Received message: 2, SequenceNumber: 0 CorrelationId: , MessageId: 30714d005eb048e7a4e7e3c22048cf20 Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\f [10]","ShareName":"share4-dl460"}]
    02/26/2019 18:23:38: Info: Moving input file: /home/input/f [10] to /home/output/f [10]
    02/26/2019 18:23:38: Info: Processed event.
    ```

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>Per monitorare le statistiche di utilizzo del dispositivo

Per monitorare la memoria, l'utilizzo della CPU `stats` e le informazioni di I/O nel dispositivo, utilizzare il comando .

1. [Connettersi all'interfaccia](#connect-to-the-powershell-interface)di PowerShell .
2. Eseguire `stats` il comando in modo da disabilitare il flusso live ed eseguire il pull solo del primo risultato.

   ```powershell
   dkrdbe stats --no-stream
   ```

   Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet:

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

