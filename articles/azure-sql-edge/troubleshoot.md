---
title: Risoluzione dei problemi relativi alle distribuzioni di Azure SQL Edge
description: Informazioni sui possibili errori durante la distribuzione di Azure SQL Edge
keywords: SQL Edge, risoluzione dei problemi, errori di distribuzione
services: sql-edge
ms.service: sql-edge
ms.topic: troubleshooting
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: d8da8bcf3d2bb6b2af2b5c69ce003289d83d3884
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939392"
---
# <a name="troubleshooting-azure-sql-edge-deployments"></a>Risoluzione dei problemi relativi alle distribuzioni di Azure SQL Edge 

Questo articolo fornisce informazioni sui possibili errori riscontrati durante la distribuzione e l'uso di contenitori Edge di Azure SQL e offre tecniche di risoluzione dei problemi che consentono di risolvere questi problemi. 

Azure SQL Edge supporta due modelli di distribuzione: 
- Distribuzione connessa tramite Azure IoT Edge: Azure SQL Edge è disponibile in Azure Marketplace e può essere distribuito come modulo per [Azure IOT Edge](../iot-edge/about-iot-edge.md). Per altre informazioni, vedere [Distribuire SQL Edge di Azure](deploy-portal.md).<br>

- Distribuzione disconnessa: le immagini del contenitore Edge di Azure SQL possono essere estratte dall'hub Docker e distribuite come contenitore Docker autonomo o in un cluster kubernetes. Per altre informazioni, vedere [distribuire Azure SQL Edge con Docker](disconnected-deployment.md) e [distribuire un contenitore Edge di Azure SQL in Kubernetes](deploy-kubernetes.md).

## <a name="troubleshooting-iot-edge-device-and-deployments"></a>Risoluzione dei problemi relativi a IoT Edge dispositivo e distribuzioni

Se si verifica un errore durante la distribuzione di SQL Edge tramite Azure IoT Edge, assicurarsi che il `iotedge` servizio sia configurato e in esecuzione correttamente. I documenti seguenti possono essere utili per la risoluzione dei problemi relativi a Azure IoT Edge:
- [Problemi comuni e soluzioni per Azure IOT Edge](../iot-edge/troubleshoot-common-errors.md).
- [Risolvere i problemi del dispositivo IoT Edge](../iot-edge/troubleshoot.md)

## <a name="docker-command-errors"></a>Errori per i comandi Docker

Se vengono visualizzati errori per i comandi `docker`, verificare che il servizio Docker sia in esecuzione e provare a eseguirlo con autorizzazioni elevate.

Ad esempio, in Linux potrebbe essere visualizzato l'errore seguente quando si eseguono i comandi `docker`:

```output
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
```

Se si riceve questo errore in Linux, provare a eseguire gli stessi comandi preceduti da `sudo`. Se il tentativo non riesce, verificare che il servizio Docker sia in esecuzione e, se necessario, avviarlo.

```bash
sudo systemctl status docker
sudo systemctl start docker
```

In Windows assicurarsi di avviare PowerShell o il prompt dei comandi come amministratore.

## <a name="azure-sql-edge-container-startup-errors"></a>Errori di avvio del contenitore Edge di Azure SQL

Se non è possibile eseguire il contenitore SQL Edge, provare i test seguenti:

- Se si usa Azure IoT Edge, assicurarsi che le immagini del modulo siano state scaricate correttamente e che le variabili di ambiente e le opzioni di creazione del contenitore siano specificate correttamente nel manifesto del modulo.

- Se si usa la distribuzione basata su Docker o kubernetes, verificare che il `docker run` formato del comando sia corretto. Per altre informazioni, vedere [distribuire Azure SQL Edge con Docker](disconnected-deployment.md) e [distribuire un contenitore Edge di Azure SQL in Kubernetes](deploy-kubernetes.md).

- Se si riceve un errore come `failed to create endpoint CONTAINER_NAME on network bridge. Error starting proxy: listen tcp 0.0.0.0:1433 bind: address already in use.`, si sta tentando di eseguire il mapping della porta del contenitore 1433 a una porta già in uso. Questo problema può verificarsi se SQL Edge viene eseguito localmente nel computer host. Può anche verificarsi se si avviano due contenitori SQL Edge e si tenta di eseguirne il mapping alla stessa porta host. In tal caso, usare il parametro `-p` per eseguire il mapping della porta del contenitore 1433 a una porta host diversa. Ad esempio: 

    ```bash
    sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge-developer.
    ```

- Se si riceve un errore come `Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.30tdout=1&tail=all: dial unix /var/run/docker.sock: connect: permission denied` quando si tenta di avviare un contenitore, aggiungere l'utente al gruppo docker in Ubuntu. Quindi disconnettersi e accedere di nuovo perché questa modifica influirà sulle nuove sessioni. 

   ```bash
    usermod -aG docker $USER
   ```

- Controllare se sono presenti messaggi di errore dal contenitore.

   ```bash
   docker logs e69e056c702d
   ```

- Se si usa un software di gestione dei contenitori, assicurarsi che supporti l'esecuzione come root dei processi dei contenitori. Il processo sqlservr nel contenitore viene eseguito come root.

- Per impostazione predefinita, i contenitori di Azure SQL Edge vengono eseguiti come un utente non root denominato `mssql` . Se si usano punti di montaggio o volumi di dati per rendere permanente i dati, assicurarsi che l' `mssql` utente disponga delle autorizzazioni appropriate per il volume. Per altre informazioni, vedere [eseguire come utente non radice e salvare in modo](configure.md#run-azure-sql-edge-as-non-root-user) [permanente i dati](configure.md#persist-your-data).

- Se il contenitore Docker di SQL Edge viene chiuso immediatamente dopo l'avvio, controllare i log di Docker. Se si usa PowerShell in Windows con il comando `docker run`, usare le virgolette doppie anziché le virgolette singole. Con PowerShell Core usare le virgolette singole.

- Esaminare i [log degli errori di SQL Edge](#errorlogs).

## <a name="sql-edge-connection-failures"></a>Errori di connessione Edge SQL

Se non è possibile connettersi all'istanza di SQL Edge in esecuzione nel contenitore, provare i test seguenti:

- Verificare che il contenitore di SQL Edge sia in esecuzione esaminando la colonna **stato** dell' `docker ps -a` output. In caso contrario, usare `docker start <Container ID>` per avviarlo.

- Se è stato eseguito il mapping a una porta host non predefinita (non 1433), assicurarsi di specificare la porta nella stringa di connessione. È possibile visualizzare il mapping delle porte nella colonna **PORTS** dell'output di `docker ps -a`. Per altre informazioni sulla connessione a Azure SQL Edge, vedere [connettersi ed eseguire query in Azure SQL Edge](connect.md)

- Se SQL Edge è stato distribuito in precedenza con un volume di dati o un contenitore di volumi di dati con mapping e ora usa il volume di dati o il contenitore del volume di dati con mapping esistente, SQL Edge ignora il valore della `MSSQL_SA_PASSWORD` variabile di ambiente. Viene invece usata la password dell'utente SA precedentemente configurata. Questo problema si verifica perché SQL Edge riutilizza i file dei database master esistenti nel volume di cui è stato eseguito il mapping o nel contenitore del volume dati. Se si riscontra questo problema, è possibile utilizzare le opzioni seguenti:

    - Connettersi usando la password usata in precedenza, se è ancora disponibile.
    - Configurare SQL Edge per usare un volume o un contenitore di volumi di dati con mapping diverso.
    - Rimuovere i file di database master esistenti (Master. mdf e mastlog. MDF) dal volume mappato o dal contenitore del volume di dati.

- Esaminare i [log degli errori di SQL Edge](#errorlogs).

## <a name="sql-edge-setup-and-error-logs"></a><a id="errorlogs"></a> Installazione di SQL Edge e log degli errori

Per impostazione predefinita, i log degli errori di SQL Edge sono presenti nella directory **/var/opt/MSSQL/log** all'interno del contenitore ed è possibile accedervi usando uno dei modi seguenti:

- Se è stata montata una directory host in **/var/opt/mssql** al momento della creazione del contenitore, è invece possibile esaminare la sottodirectory **log** nel percorso mappato nell'host.
- Utilizzando un prompt dei comandi interattivo per connettersi al contenitore. Se il contenitore non è in esecuzione, avviare prima di tutto il contenitore. Usare quindi un prompt dei comandi interattivo per esaminare i log. È possibile ottenere l'ID del contenitore eseguendo il comando `docker ps`.

    ```bash
    docker start <ContainerID>
    docker exec -it <ContainerID> "/bin/bash"
    ```

    Dalla sessione bash all'interno del contenitore eseguire i comandi seguenti:

    ```bash
    cd /var/opt/mssql/log
    cat errorlog
    ```
- Se il contenitore SQL Edge è attivo e in esecuzione e si è in grado di connettersi all'istanza mediante gli strumenti client, è possibile utilizzare il stored procedure `sp_readerrorlog` per leggere il contenuto del log degli errori di SQL Edge.

## <a name="execute-commands-in-a-container"></a>Eseguire comandi in un contenitore

Se è disponibile un contenitore in esecuzione, è possibile eseguire i comandi all'interno del contenitore da un terminale host.

Per ottenere l'ID del contenitore, eseguire:

```bash
docker ps -a
```

Per avviare un terminale bash nel contenitore, eseguire:

```bash
docker exec -it <Container ID> /bin/bash
```

A questo punto è possibile eseguire i comandi come se fossero in esecuzione nel terminale all'interno del contenitore. Al termine, digitare `exit`. Viene così chiusa la sessione di esecuzione interattiva dei comandi, ma l'esecuzione del contenitore continua.

## <a name="troubleshooting-issues-with-data-streaming"></a>Risoluzione dei problemi relativi al flusso di dati

Per impostazione predefinita, i log del motore di flusso di Azure SQL Edge vengono scritti in un file denominato `current` nella directory **/var/opt/MSSQL/log/Services/00000001-0000-0000-0000-000000000000** È possibile accedere al file direttamente tramite il volume mappato o il contenitore del volume di dati oppure avviando una sessione interattiva del prompt dei comandi nel contenitore SQL Edge. 

Inoltre, se è possibile connettersi all'istanza di SQL Edge usando gli strumenti client, è possibile usare il comando T-SQL seguente per accedere al log del motore di flusso corrente. 

```sql

select value as log, try_convert(DATETIME2, substring(value, 0, 26)) as timestamp 
from 
    STRING_SPLIT
    (
        (
            select BulkColumn as logs
            FROM OPENROWSET (BULK '/var/opt/mssql/log/services/00000001-0000-0000-0000-000000000000/current', SINGLE_CLOB) MyFile
        ),
        CHAR(10)
    ) 
where datalength(value) > 0

```

### <a name="enabling-verbose-logging"></a>Abilitazione della registrazione dettagliata

Se il livello di registrazione predefinito per il motore di flusso non fornisce informazioni sufficienti, la registrazione del debug per il motore di flusso può essere abilitata in SQL Edge. Per abilitare la registrazione di debug `RuntimeLogLevel=debug` , aggiungere la variabile di ambiente alla distribuzione di SQL Edge. Dopo aver abilitato la registrazione del debug, provare a riprodurre il problema e verificare la presenza di eventuali messaggi o eccezioni rilevanti nei log. 



## <a name="next-steps"></a>Passaggi successivi

- [Machine Learning e intelligenza artificiale con ONNX in SQL Edge](onnx-overview.md)
- [Streaming dei dati in SQL Edge di Azure](stream-data.md)
- [Conservazione e pulizia dei dati](data-retention-overview.md)
- [Colmare i gap temporali e imputare i valori mancanti](imputing-missing-values.md)







