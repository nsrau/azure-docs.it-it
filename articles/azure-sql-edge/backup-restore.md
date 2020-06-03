---
title: Backup e ripristino di database - SQL Edge di Azure (anteprima)
description: Informazioni sulle funzionalità di backup e ripristino in SQL Edge di Azure (anteprima)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 902576f82faa18fbb0e7c7eaed5c06993bd379cc
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235175"
---
# <a name="backup-and-restore-databases-in-azure-sql-edge-preview"></a>Backup e ripristino di database in SQL Edge di Azure (anteprima) 

SQL Edge di Azure si basa sulle versioni più recenti del motore di database di Microsoft SQL Server in Linux, offrendo funzionalità di backup e ripristino del database simili a quelle disponibili in SQL Server in Linux e SQL Server in esecuzione nei contenitori. Il componente di backup e ripristino rappresenta uno strumento essenziale per la sicurezza e la protezione di dati archiviati nei database SQL Edge di Azure. Per ridurre al minimo il rischio di perdita irreparabile dei dati, si consiglia di eseguire il backup dei database a intervalli regolari per preservare le modifiche ai dati. Una strategia di backup e ripristino ben pianificata aiuta a proteggere i database dalla perdita di dati dovuta a vari errori. Testare la strategia ripristinando un set di backup e poi recuperando il database per prepararsi a rispondere in modo efficace a una situazione di emergenza.

Per altre informazioni sull'importanza dei backup, vedere [Backup e ripristino di database SQL Server](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/).

SQL Edge di Azure supporta il backup e il ripristino dalla risorsa di archiviazione locale o dai BLOB di Azure. Per altre informazioni sul backup e il ripristino da Archiviazione BLOB di Azure, vedere [Backup e ripristino di SQL Server con il servizio di archiviazione BLOB di Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) e [Backup di SQL Server nell'URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="backing-up-a-database-in-azure-sql-edge"></a>Backup di un database in SQL Edge di Azure

SQL Edge di Azure supporta gli stessi tipi di backup supportati da SQL Server. Per un elenco completo dei tipi di backup supportati in SQL Server, vedere [Panoramica del servizio Backup](/sql/relational-databases/backup-restore/backup-overview-sql-server/).

> [!IMPORTANT] 
> Per impostazione predefinita, i database creati in SQL Edge di Azure usano il modello di recupero con registrazione minima. Di conseguenza, tali backup del log non possono essere eseguiti su questi database. Se è necessario eseguire i backup del log in questi database, un amministratore deve modificare il modello di recupero del database in un modello di recupero con registrazione completa. Per un elenco completo dei modelli di recupero supportati da SQL Server, vedere [Panoramica del modello di recupero](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov).

### <a name="backup-to-local-disk"></a>Backup nel disco locale

Nell'esempio seguente, viene usato il comando Transact-SQL, BACKUP DATABASE, per creare un backup del database nel contenitore. Ai fini di questo esempio, viene creata una nuova cartella denominata "backup" per archiviare i file di backup.

1. Creare una cartella per i backup. Questo comando deve essere eseguito nell'host in cui è in esecuzione il contenitore di SQL Edge di Azure. Nel comando seguente sostituire **<AzureSQLEdge_Container_Name>** con il nome del contenitore SQL Edge di Azure nella distribuzione.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Connettersi all'istanza di SQL Edge di Azure usando SQL Server Management Studio (SSMS) o Azure Data Studio (ADS) ed eseguire il comando BACKUP DATABASE per eseguire il backup del database utente. Nell'esempio seguente viene eseguito il backup del database *IronOreSilicaPrediction*.

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. Dopo aver eseguito il comando e se il backup del database ha esito positivo, verranno visualizzati messaggi simili ai seguenti nella sezione dei risultati di SSMS o ADS.

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="backup-to-url"></a>Backup su URL

SQL Edge di Azure supporta i backup sia sui BLOB di pagine che sui BLOB in blocchi. Per altre informazioni sui BLOB in blocchi e sui BLOB di pagine, vedere [Eseguire il backup su BLOB in blocchi o BLOB di pagine](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-ver15#blockbloborpageblob). Nell'esempio seguente viene eseguito il backup del database *IronOreSilicaPrediction* in un BLOB in blocchi. 

1. Il primo passaggio per la configurazione dei backup sui BLOB in blocchi consiste nel generare un token di firma di accesso condiviso (SAS) che può essere usato per creare una credenziale di SQL Server in SQL Edge di Azure. Questo script crea una firma di accesso condiviso associata a un criterio di accesso archiviato. Per altre informazioni, vedere [Firme di accesso condiviso, parte 1: informazioni sul modello di firma di accesso condiviso](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/). Lo script scrive inoltre il comando T-SQL necessario per creare le credenziali in SQL Server. Lo script seguente presuppone che sia già presente una sottoscrizione di Azure con un account di archiviazione e un contenitore di archiviazione per i backup.

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    Dopo aver completato l'esecuzione dello script, copiare il comando CREATE CREDENTIAL in uno strumento di query, connettersi a un'istanza di SQL Server ed eseguire il comando per creare le credenziali con la firma di accesso condiviso.

2. Connettersi all'istanza di SQL Edge di Azure usando SQL Server Management Studio (SSMS) o Azure Data Studio (ADS) e creare le credenziali usando il comando del passaggio precedente. Assicurarsi di sostituire il comando CREATE CREDENTIAL con l'output effettivo del passaggio precedente.

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. Il comando seguente esegue un backup di *IronOreSilicaPrediction* nel contenitore di archiviazione di Azure.

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restoring-a-database-in-azure-sql-edge"></a>Ripristino di un database in SQL Edge di Azure

SQL Edge di Azure supporta il ripristino da un disco locale, un percorso di rete o un account di archiviazione BLOB di Azure. Per una panoramica del ripristino e del recupero in SQL Server, vedere [Panoramica del ripristino e del recupero](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server?view=sql-server-ver15). Per una panoramica del modello di recupero con registrazione minima in SQL Server, vedere [Ripristini di database completi (modello di recupero con registrazione minima)](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model?view=sql-server-ver15).

### <a name="restore-from-local-disk"></a>Ripristinare dal disco locale

Questo esempio usa il backup *IronOreSilicaPrediction* eseguito nell'esempio precedente per il ripristino come nuovo database con un nome diverso.

1. Se il file di backup del database non è già presente nel contenitore, è possibile usare il comando seguente per copiare il file nel contenitore. L'esempio seguente presuppone che il file di backup sia presente nell'host locale e che venga copiato nella cartella /var/opt/MSSQL/backup in un contenitore SQL Edge di Azure denominato sql1.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Connettersi all'istanza di SQL Edge di Azure usando SQL Server Management Studio (SSMS) o Azure Data Studio (ADS) per eseguire il comando di ripristino. Nell'esempio seguente, **IronOrePredictDB.bak** viene ripristinato per creare un nuovo database **IronOreSilicaPrediction_2**

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. Dopo aver eseguito il comando di ripristino e se l'operazione di ripristino ha avuto esito positivo, verranno visualizzati dei messaggi simili ai seguenti nella finestra di output. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Ripristino da un URL

SQL Edge di Azure supporta anche il ripristino di un database da un account di archiviazione di Azure. È possibile eseguire i ripristini dai backup dei BLOB in blocchi o dei BLOB di pagine. Nell'esempio seguente, viene ripristinato il file di backup del database *IronOreSilicaPrediction_2020_04_16.bak* in un BLOB in blocchi per creare il database *IronOreSilicaPrediction_3*.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```


