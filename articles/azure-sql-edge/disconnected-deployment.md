---
title: 'Distribuire SQL Edge di Azure con Docker: SQL Edge di Azure'
description: Informazioni sulla distribuzione di SQL Edge di Azure con Docker
keywords: SQL Edge, contenitore, docker
services: sql-edge
ms.service: sql-edge
ms.topic: quickstart
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: fce098767fffd36376399bbd9396699e3d9fbfd3
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392079"
---
# <a name="deploy-azure-sql-edge-with-docker"></a>Distribuire SQL Edge di Azure con Docker

In questa guida di avvio rapido si usa Docker per il pull e l'esecuzione dell'immagine del contenitore di SQL Edge di Azure. Ci si connette quindi con **sqlcmd** per creare il primo database ed eseguire query.

Questa immagine è costituita da SQL Edge di Azure basato su Ubuntu 18.04. Può essere usata con il motore Docker 1.8 o versione successiva su Linux o in Docker per Mac/Windows.

## <a name="prerequisites"></a>Prerequisites

- Motore Docker 1.8 o versione successiva in qualsiasi distribuzione di Linux supportata oppure Docker per Mac/Windows. Per altre informazioni, vedere [Installare Docker](https://docs.docker.com/engine/installation/). Poiché le immagini SQL Edge di Azure sono basate su Ubuntu 18.04, è consigliabile usare un host Docker Ubuntu 18.04.
- Driver di archiviazione **overlay2** Docker. Si tratta dell'impostazione predefinita per la maggior parte degli utenti. Se non si sta usando questo provider di archiviazione ed è necessario cambiarlo, vedere le istruzioni e gli avvisi nella [documentazione di Docker per la configurazione di overlay2.](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#configure-docker-with-the-overlay-or-overlay2-storage-driver)
- Almeno 10 GB di spazio su disco.
- Almeno 1 GB di RAM.
- [Requisiti hardware per SQL Edge di Azure](./features.md#hardware-support).


## <a name="pull-and-run-the-container-image"></a>Effettuare il pull ed eseguire l'immagine del contenitore

Prima di iniziare la procedura seguente, assicurarsi di aver selezionato la shell preferita (Bash, PowerShell o cmd) all'inizio di questo articolo.

1. Eseguire il pull dell'immagine del contenitore di SQL Edge di Azure dal Registro Container Microsoft.

    - Eseguire il pull dell'immagine del contenitore di SQL Edge di Azure
        ```bash
        sudo docker pull mcr.microsoft.com/azure-sql-edge:latest 
        ```

> [!NOTE]
> Per i comandi Bash in questo articolo, si usa `sudo`. In macOS e Windows, sudo potrebbe non essere necessario. In Linux, se non si vuole usare sudo per eseguire Docker, è possibile configurare un gruppo docker e aggiungere utenti a tale gruppo. Per altre informazioni, vedere [Post-installation steps for Linux](https://docs.docker.com/engine/install/linux-postinstall/) (Passaggi post-installazione per Linux).

Il comando precedente esegue il pull delle ultime immagini del contenitore di SQL Edge di Azure. Per visualizzare tutte le immagini disponibili, vedere la [pagina dell'hub Docker su azure-sql-egde](https://hub.docker.com/_/microsoft-azure-sql-edge).

2. Per eseguire l'immagine del contenitore con Docker, è possibile usare il comando seguente da una shell Bash (Linux/macOS) o da un prompt dei comandi PowerShell con privilegi elevati.
    
    - Avviare un'istanza di SQL Edge di Azure in esecuzione come edizione Developer
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```

    - Avviare un'istanza di SQL Edge di Azure in esecuzione come edizione Premium
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -e 'MSSQL_PID=Premium' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```
    > [!NOTE]
    > Se si usa PowerShell in Windows per eseguire questi comandi, usare le virgolette doppie anziché le virgolette singole.


    > [!NOTE]
    > La password deve essere conforme ai criteri password predefiniti del motore di database SQL di Microsoft, altrimenti il contenitore non potrà configurare il motore SQL e smetterà di funzionare. Per impostazione predefinita, la password deve contenere almeno 8 caratteri, appartenenti a tre dei quattro set seguenti: lettere maiuscole, lettere minuscole, cifre in base 10 e simboli. È possibile esaminare il log degli errori eseguendo il comando [docker logs](https://docs.docker.com/engine/reference/commandline/logs/).
    
    La tabella seguente offre una descrizione dei parametri dell'esempio `docker run` precedente:

    | Parametro | Descrizione |
    |-----|-----|
    | **-e "ACCEPT_EULA=Y"** |  Impostare la variabile **ACCEPT_EULA** su qualsiasi valore per confermare l'accettazione delle [condizioni di licenza](https://go.microsoft.com/fwlink/?linkid=2139274). Impostazione obbligatoria per l'immagine di SQL Edge di Azure. |
    | **-e "MSSQL_SA_PASSWORD=yourStrong(!)Password"** | Specificare la password complessa composta da almeno 8 caratteri e conforme ai [requisiti per le password di SQL Edge di Azure](/sql/relational-databases/security/password-policy). Impostazione obbligatoria per l'immagine di SQL Edge di Azure. |
    | **-p 1433:1433** | Eseguire il mapping di una porta TCP nell'ambiente host (primo valore) con una porta TCP nel contenitore (secondo valore). In questo esempio SQL Edge di Azure è in ascolto sulla porta TCP 1433 nel contenitore e questo è esposto alla porta 1433 nell'host. |
    | **--name azuresqledge** | Specificare un nome personalizzato per il contenitore, invece di un nome generato in modo casuale. Se si eseguono più contenitori, non è possibile riutilizzare questo stesso nome. |
    | **-d** | Eseguire il contenitore in background (daemon) |

    Per un elenco completo di tutte le variabili di ambiente di SQL Edge di Azure, vedere [Configurare SQL Edge di Azure con le variabili di ambiente](configure.md#configure-by-using-environment-variables). È anche possibile usare un [file mssql.conf](configure.md#configure-by-using-an-mssqlconf-file) per configurare i contenitori di SQL Edge di Azure.

3. Per visualizzare i contenitori di Docker, usare il comando `docker ps`.
   
   ```bash
    sudo docker ps -a
   ```

4. Se nella colonna **STATUS** è impostato lo stato **Up** , SQL Edge di Azure è in esecuzione nel contenitore e in ascolto sulla porta specificata nella colonna **PORTS**. Se la colonna **STATUS** del contenitore di SQL Edge di Azure è impostata su **Exited** , vedere la sezione relativa alla risoluzione dei problemi della documentazione di SQL Edge di Azure.

    Anche il parametro `-h` (nome host) è utile, ma non viene usato in questa esercitazione per semplicità. Cambia il nome interno del contenitore sostituendolo con un valore personalizzato. È il nome che viene restituito nella query Transact-SQL seguente:

    ```sql
    SELECT @@SERVERNAME,
        SERVERPROPERTY('ComputerNamePhysicalNetBIOS'),
        SERVERPROPERTY('MachineName'),
        SERVERPROPERTY('ServerName')
    ```

    L'impostazione di `-h` e `--name` sullo stesso valore è un buon modo per identificare facilmente il contenitore di destinazione.

5. Come passaggio finale, cambiare la password dell'amministratore di sistema, perché il valore di `SA_PASSWORD` è visibile nell'output di `ps -eax` e viene archiviato nella variabile di ambiente con lo stesso nome. Vedere i passaggi seguenti.

## <a name="change-the-sa-password"></a>Cambiare la password dell'amministratore di sistema

L'account **SA** è un amministratore di sistema dell'istanza di SQL Edge di Azure creato durante l'installazione. Dopo aver creato il contenitore SQL Edge di Azure, la variabile di ambiente `MSSQL_SA_PASSWORD` specificata diventa individuabile eseguendo `echo $SA_PASSWORD` nel contenitore. Per motivi di sicurezza, modificare la password dell'amministratore di sistema.

1. Scegliere una password complessa da usare per l'utente SA.

2. Usare `docker exec` per eseguire **sqlcmd** per modificare la password usando Transact-SQL. Nell'esempio seguente sostituire la vecchia password, `<YourStrong!Passw0rd>`, e la nuova password, `<YourNewStrong!Passw0rd>`, con le password personali.

   ```bash
   sudo docker exec -it azuresqledge /opt/mssql-tools/bin/sqlcmd \
      -S localhost -U SA -P "<YourStrong@Passw0rd>" \
      -Q 'ALTER LOGIN SA WITH PASSWORD="<YourNewStrong@Passw0rd>"'
   ```

## <a name="connect-to-azure-sql-edge"></a>Connettersi a SQL Edge di Azure

La procedura seguente usa lo strumento da riga di comando di SQL Edge di Azure, **sqlcmd** , all'interno del contenitore per stabilire la connessione a SQL Edge di Azure.

> [!NOTE]
> Lo strumento sqlcmd non è disponibile nella versione ARM64 di contenitori di SQL Edge.

1. Usare il comando `docker exec -it` per avviare una shell Bash interattiva all'interno del contenitore in esecuzione. Nell'esempio seguente `azuresqledge` è il nome specificato dal parametro `--name`quando è stato creato il contenitore.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. Una volta all'interno del contenitore, eseguire la connessione in locale con sqlcmd. Sqlcmd non è incluso nel percorso per impostazione predefinita, quindi occorre specificare il percorso completo.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > È possibile omettere la password nella riga di comanda perché sia richiesto di essere immessa.

3. Se la connessione viene eseguita correttamente, il prompt dei comandi **sqlcmd** sarà: `1>`.

## <a name="create-and-query-data"></a>Creare i dati e recuperarli tramite query

Nelle sezioni seguenti viene descritto l'uso di **sqlcmd** e Transact-SQL per creare un nuovo database, aggiungere dati ed eseguire una query semplice.

### <a name="create-a-new-database"></a>Creare un nuovo database

La seguente procedura consente di creare un nuovo database denominato `TestDB`.

1. Dal prompt dei comandi **sqlcmd** incollare il comando seguente di Transact-SQL per creare un database di test:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. Nella riga successiva scrivere una query perché vengano restituiti i nomi di tutti database nel server:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Inserire i dati

Creare poi una nuova tabella `Inventory` e inserire due nuove righe.

1. Dal prompt dei comandi **sqlcmd** spostare il contesto nel nuovo database `TestDB`:

   ```sql
   USE TestDB
   ```

2. Creare una nuova tabella denominata `Inventory`:

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Inserire i dati nella nuova tabella:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. Digitare `GO` per eseguire i comandi precedenti:

   ```sql
   GO
   ```

### <a name="select-data"></a>Selezionare i dati

A questo punto, eseguire una query per restituire i dati dalla tabella `Inventory`.

1. Dal prompt dei comandi **sqlcmd** immettere una query che restituisca le righe dalla tabella `Inventory` che ne contiene oltre 152:

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Eseguire il comando seguente:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Uscire dal prompt dei comandi sqlcmd

1. Per terminare la sessione **sqlcmd** , digitare `QUIT`:

   ```sql
   QUIT
   ```

2. Per uscire dal prompt dei comandi interattivo nel contenitore, digitare `exit`. Dopo la chiusura della shell Bash interattiva, il contenitore continua l'esecuzione.

## <a name="connect-from-outside-the-container"></a> Eseguire la connessione dall'esterno del contenitore

È anche possibile connettersi all'istanza di SQL Edge di Azure nel computer che esegue Docker da uno strumento esterno Linux, Windows o macOS che supporti le connessioni SQL. Per altre informazioni sulla connessione a un contenitore di SQL Edge dall'esterno, vedere [Connettersi ed eseguire query in SQL Edge di Azure](connect.md).

## <a name="remove-your-container"></a>Rimuovere il contenitore

Se si vuole rimuovere il contenitore di SQL Edge di Azure usato in questa esercitazione, eseguire i comandi seguenti:

```bash
sudo docker stop azuresqledge
sudo docker rm azuresqledge
```

> [!WARNING]
> Se si arresta e si rimuove un contenitore, i dati di SQL Edge di Azure eventualmente presenti nel contenitore vengono eliminati in modo definitivo. Se occorre conservare i dati, [creare un file di backup e copiarlo all'esterno del contenitore](backup-restore.md) oppure usare una [tecnica di persistenza dei dati del contenitore](configure.md#persist-your-data).

## <a name="next-steps"></a>Passaggi successivi

- [Machine Learning e intelligenza artificiale con ONNX in SQL Edge](onnx-overview.md).
- [Creazione di una soluzione IoT end-to-end con SQL Edge usando IoT Edge](tutorial-deploy-azure-resources.md).
- [Streaming dei dati in SQL Edge di Azure](stream-data.md)