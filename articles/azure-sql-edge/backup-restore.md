---
title: Eseguire il backup e il ripristino di database-Azure SQL Edge (anteprima)
description: Informazioni sulle funzionalità di backup e ripristino in Azure SQL Edge (anteprima).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 92a37babbcc0bbba3845267ca2eb0f95b9fceafa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84667863"
---
# <a name="back-up-and-restore-databases-in-azure-sql-edge-preview"></a>Eseguire il backup e il ripristino di database in Azure SQL Edge (anteprima) 

Azure SQL Edge è basato sulle versioni più recenti del Microsoft SQL Server motore di database in Linux. Offre funzionalità di database di backup e ripristino simili a quelle disponibili in SQL Server in Linux e SQL Server in esecuzione nei contenitori. Il componente di backup e ripristino offre una protezione essenziale per la protezione dei dati archiviati nei database di Azure SQL Edge. 

Per ridurre al minimo il rischio di una perdita di dati irreversibile, è consigliabile eseguire periodicamente il backup dei database per mantenere le modifiche ai dati a intervalli regolari. Una strategia di backup e ripristino ben pianificata aiuta a proteggere i database dalla perdita di dati dovuta a vari errori. Testare la strategia ripristinando un set di backup e quindi recuperando il database per prepararsi a rispondere in modo efficace a una situazione di emergenza.

Per altre informazioni sui motivi per cui i backup sono importanti, vedere [backup e ripristino di database SQL Server](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/).

Azure SQL Edge consente di eseguire il backup e il ripristino dalla risorsa di archiviazione locale e dai BLOB di Azure. Per altre informazioni, vedere [SQL Server backup e ripristino con archiviazione BLOB di Azure](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) e [SQL Server backup nell'URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="back-up-a-database-in-azure-sql-edge"></a>Eseguire il backup di un database in Azure SQL Edge

Azure SQL Edge supporta gli stessi tipi di backup SQL Server. Per un elenco completo, vedere [Panoramica del backup](/sql/relational-databases/backup-restore/backup-overview-sql-server/).

> [!IMPORTANT] 
> Per impostazione predefinita, i database creati in Azure SQL Edge usano il modello di recupero con registrazione minima. Di conseguenza, non è possibile eseguire backup del log in questi database. Se è necessario eseguire questa operazione, è necessario disporre di un amministratore per modificare il modello di recupero del database nel modello di recupero con esecuzione completa. Per un elenco completo dei modelli di recupero supportati da SQL Server, vedere [Panoramica del modello di recupero](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov).

### <a name="back-up-to-local-disk"></a>Esegui il backup su disco locale

Nell'esempio seguente viene usato il `BACKUP DATABASE` comando Transact-SQL per creare un backup del database nel contenitore. Ai fini di questo esempio, viene creata una nuova cartella denominata *backup* per archiviare i file di backup.

1. Creare una cartella per i backup. Eseguire questo comando nell'host in cui è in esecuzione il contenitore Edge di Azure SQL. Nel comando seguente sostituire **<AzureSQLEdge_Container_Name>** con il nome del contenitore Edge di Azure SQL nella distribuzione.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Connettersi all'istanza di Azure SQL Edge usando SQL Server Management Studio (SSMS) o usando Azure Data Studio. Eseguire il `BACKUP DATABASE` comando per eseguire il backup del database utente. Nell'esempio seguente si sta eseguendo il backup del database *IronOreSilicaPrediction* .

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. Dopo aver eseguito il comando, se il backup del database ha esito positivo, verranno visualizzati messaggi simili ai seguenti nella sezione dei risultati di SSMS o Azure Data Studio.

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

### <a name="back-up-to-url"></a>URL di Backup in

SQL Edge di Azure supporta i backup sia sui BLOB di pagine che sui BLOB in blocchi. Per altre informazioni, vedere [backup di BLOB in blocchi e BLOB di pagine](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-ver15#blockbloborpageblob). Nell'esempio seguente viene eseguito il backup del database *IronOreSilicaPrediction* in un BLOB in blocchi. 

1. Per configurare i backup per i BLOB in blocchi, è necessario innanzitutto generare un token di firma di accesso condiviso che è possibile usare per creare una credenziale di SQL Server in Azure SQL Edge. Lo script crea una firma di accesso condiviso associata a un criterio di accesso archiviato. Per altre informazioni, vedere [firme di accesso condiviso, parte 1: informazioni sul modello SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/). Lo script scrive inoltre il comando T-SQL necessario per creare le credenziali in SQL Server. Lo script seguente presuppone che sia già presente una sottoscrizione di Azure con un account di archiviazione e un contenitore di archiviazione per i backup.

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

    Una volta eseguito correttamente lo script, copiare il `CREATE CREDENTIAL` comando in uno strumento di query. Connettersi quindi a un'istanza di SQL Server ed eseguire il comando per creare le credenziali con la firma di accesso condiviso.

2. Connettersi all'istanza di Azure SQL Edge usando SSMS o Azure Data Studio e creare le credenziali usando il comando del passaggio precedente. Assicurarsi di sostituire il `CREATE CREDENTIAL` comando con l'output effettivo del passaggio precedente.

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

## <a name="restore-a-database-in-azure-sql-edge"></a>Ripristinare un database in Azure SQL Edge

In Azure SQL Edge è possibile eseguire il ripristino da un disco locale, da un percorso di rete o da un account di archiviazione BLOB di Azure. Per ulteriori informazioni sul ripristino e il ripristino in SQL Server, vedere Panoramica del ripristino [e del ripristino](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server?view=sql-server-ver15). Per una panoramica del modello di recupero con registrazione minima in SQL Server, vedere [ripristini di database completi (modello di recupero con registrazione minima)](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model?view=sql-server-ver15).

### <a name="restore-from-a-local-disk"></a>Ripristinare da un disco locale

Questo esempio usa il backup di *IronOreSilicaPrediction* creato nell'esempio precedente. A questo punto, verrà ripristinato come nuovo database con un nome diverso.

1. Se il file di backup del database non è già presente nel contenitore, è possibile usare il comando seguente per copiare il file nel contenitore. Nell'esempio seguente si presuppone che il file di backup sia presente nell'host locale e che venga copiato nella cartella/var/opt/MSSQL/backup in un contenitore Edge di Azure SQL denominato *SQL1*.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Connettersi all'istanza di Azure SQL Edge usando SSMS o Azure Data Studio per eseguire il comando Restore. Nell'esempio seguente viene ripristinato **IronOrePredictDB. bak** per creare un nuovo database, **IronOreSilicaPrediction_2**.

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. Dopo aver eseguito il comando Restore, se l'operazione di ripristino ha avuto esito positivo, nella finestra di output verranno visualizzati messaggi simili al seguente. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Ripristino da un URL

SQL Edge di Azure supporta anche il ripristino di un database da un account di archiviazione di Azure. È possibile eseguire il ripristino da BLOB in blocchi o backup di BLOB di pagine. Nell'esempio seguente viene ripristinato il file di backup del database *IronOreSilicaPrediction_2020_04_16. bak* in un BLOB in blocchi per creare il database *IronOreSilicaPrediction_3*.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```


