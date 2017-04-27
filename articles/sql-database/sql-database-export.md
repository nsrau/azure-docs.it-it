---
title: Esportare un database SQL di Azure in un file BACPAC | Microsoft Docs
description: Esportare un database SQL di Azure in un file BACPAC usando il portale di Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: move data
ms.devlang: NA
ms.date: 04/05/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: eadd300dcda2f160589c5e8e4fb7508445ef9944
ms.lasthandoff: 04/10/2017


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Esportare un database SQL di Azure in un file BACPAC

Questo articolo illustra l'importazione di un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) in un database SQL di Azure. L'articolo descrive l'uso delle soluzioni seguenti:
- Il[portale di Azure](https://portal.azure.com)
- L'utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx)
- Il cmdlet [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport)
- L'[Esportazione guidata applicazione livello dati](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) in [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).

> [!IMPORTANT] 
> La funzionalità di esportazione automatizzata di database SQL di Azure, ora disponibile in anteprima, verrà ritirata il 1° marzo 2017. A partire dal 1° dicembre 2016, non è più possibile configurare l'esportazione automatizzata per database SQL. Tutti i processi di esportazione automatizzata esistenti continueranno a funzionare fino al 1° marzo 2017. Dal 1° dicembre 2016, è possibile usare la [conservazione dei backup a lungo termine](sql-database-long-term-retention.md
) o [Automazione di Azure](https://github.com/Microsoft/azure-docs-pr/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) per archiviare periodicamente i database SQL con PowerShell in base alla pianificazione desiderata. Per uno script di esempio, scaricare lo [script PowerShell di esempio](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) disponibile in Github.
>

> [!IMPORTANT] 
> La funzionalità di esportazione automatizzata di database SQL di Azure, ora disponibile in anteprima, verrà ritirata il 1° marzo 2017. A partire dal 1° dicembre 2016, non è più possibile configurare l'esportazione automatizzata per database SQL. Tutti i processi di esportazione automatizzata esistenti continueranno a funzionare fino al 1° marzo 2017. Dal 1° dicembre 2016, è possibile usare la [conservazione dei backup a lungo termine](sql-database-long-term-retention.md
) o [Automazione di Azure](https://github.com/Microsoft/azure-docs-pr/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) per archiviare periodicamente i database SQL con PowerShell in base alla pianificazione desiderata. Per uno script di esempio, scaricare lo [script PowerShell di esempio](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) disponibile in Github.
>

## <a name="overview"></a>Panoramica

Quando è necessario esportare un database per l'archiviazione o lo spostamento in un'altra piattaforma, è possibile esportare lo schema di database e i dati in un file BACPAC. Un file BACPAC è un file ZIP con un'estensione bacpac contenente i metadati e dati provenienti da un database di SQL Server. È possibile memorizzare questo tipo di file in un'archiviazione BLOB di Azure o in un'archiviazione locale e successivamente importarlo nuovamente nel database SQL di Azure o in un'installazione locale di SQL Server. 

> [!IMPORTANT]
> Se si sta eseguendo l'esportazione da SQL Server come preparazione alla migrazione nel database SQL di Azure, vedere [Migrare un database SQL Server nel database SQL di Azure](sql-database-cloud-migrate.md).
> 

## <a name="considerations"></a>Considerazioni

* Perché un'esportazione sia coerente dal punto di vista transazionale, è necessario assicurarsi che non si verifichino attività di scrittura durante l'esportazione o che l'esportazione sia eseguita da una [copia coerente dal punto di vista transazionale](sql-database-copy.md) del database SQL di Azure.
* Se si sta eseguendo l'esportazione nell'archiviazione BLOB, la dimensione massima di un file BACPAC è 200 GB. Per archiviare un file BACPAC di dimensioni maggiori, eseguire l'esportazione in una risorsa di archiviazione locale.
* L'esportazione di un file BACPAC in Archiviazione Premium di Azure usando i metodi descritti in questo articolo non è supportata.
* Se l'operazione di esportazione da un database SQL di Azure dura oltre 20 ore, potrebbe essere annullata. Per migliorare le prestazioni durante l'esportazione è possibile:
  * Aumentare temporaneamente il livello di servizio.
  * Interrompere tutte le attività di lettura e scrittura durante l'esportazione.
  * Utilizzare un [indice cluster](https://msdn.microsoft.com/library/ms190457.aspx) con valori non null in tutte le tabelle di grandi dimensioni. Senza indici cluster, l'esportazione potrebbe non riuscire se dovesse durare più di 6 - 12 ore. Questo perché i servizi di esportazione devono completare la scansione della tabella prima di provare a esportarla per intero. Un modo valido di determinare se le tabelle sono ottimizzate per l'esportazione consiste nell'eseguire **DBCC SHOW_STATISTICS** e verificare che il parametro *RANGE_HI_KEY* non sia null e il relativo valore abbia distribuzione valida. Per i dettagli, vedere [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> I BACPAC non sono destinati a essere utilizzati per il backup e le operazioni di ripristino. Il database SQL di Azure crea automaticamente i backup per ogni database dell’utente. Per altre informazioni, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md) e [Backup del database SQL](sql-database-automated-backups.md).  
> 

## <a name="azure-portal"></a>Portale di Azure

Per esportare un database tramite il portale di Azure, aprire la pagina per il database e fare clic su **Esporta** nella barra degli strumenti. Specificare il nome del file con estensione bacpac, l'account di archiviazione di Azure e un contenitore per l'esportazione e le credenziali per connettersi al database di origine.  

   ![Esportazione di un database](./media/sql-database-export/database-export.png)

Per monitorare lo stato di avanzamento dell'operazione di esportazione, aprire la pagina per il server logico contenente il database da esportare. Scorrere fino a **Operazioni** e quindi fare clic sulla cronologia di **Importazione/Esportazione**.

## <a name="sqlpackage-utility"></a>Utilità SQLPackage

Per esportare un database SQL tramite l'utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx), vedere la sezione relativa ai [parametri e proprietà dell'importazione](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties). L'utilità SQLPackage viene offerta con le versioni più recenti di [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). È tuttavia possibile scaricare la versione più recente di [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) direttamente dall'area download Microsoft.

È consigliabile usare l'utilità SQLPackage per la scalabilità e le prestazioni nella maggior parte degli ambienti di produzione. Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.

## <a name="sql-server-management-studio"></a>SQL Server Management Studio

Le versioni più recenti di SQL Server Management Studio forniscono inoltre una procedura guidata per esportare un database SQL di Azure in un file con estensione bacpac. Vedere [Esportazione guidata applicazione livello dati](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="powershell"></a>PowerShell

Usare il cmdlet [New-AzureRmSqlDatabaseImport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport) per inviare una richiesta di esportazione database al servizio database SQL di Azure. A seconda delle dimensioni del database, l'operazione di esportazione potrebbe richiedere molto tempo.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

Per verificare lo stato della richiesta di esportazione, usare il cmdlet [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/get-azurermsqldatabaseimportexportstatus). L'esecuzione di questo cmdlet subito dopo la richiesta restituisce in genere **Status: InProgress**. Al termine dell'esportazione, il messaggio restituito è **Status: Succeeded**.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

### <a name="export-sql-database-example"></a>Esempio di esportazione di un database SQL
L'esempio seguente esporta un database SQL di Azure esistente in un file con estensione bacpac e quindi illustra come controllare lo stato dell'operazione di esportazione.

Per eseguire l'esempio, esistono alcune variabili da sostituire con i valori specifici relativi al database e all'account di archiviazione. Nel [Portale di Azure](https://portal.azure.com)accedere all'account di archiviazione per ottenerne il nome, insieme al nome del contenitore BLOB e al valore della chiave. Per trovare la chiave, fare clic su **Chiavi di accesso** nel pannello dell'account di archiviazione.

Sostituire quanto segue `VARIABLE-VALUES` con i valori relativi alle specifiche risorse di Azure. Il nome del database indica il database esistente che si desidera esportare.

```powershell
$subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId

# Database to export
$DatabaseName = "DATABASE-NAME"
$ResourceGroupName = "RESOURCE-GROUP-NAME"
$ServerName = "SERVER-NAME
$serverAdmin = "ADMIN-NAME"
$serverPassword = "ADMIN-PASSWORD" 
$securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
$creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

# Generate a unique filename for the BACPAC
$bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

# Storage account info for the BACPAC
$BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
$BacpacUri = $BaseStorageUri + $bacpacFilename
$StorageKeytype = "StorageAccessKey"
$StorageKey = "YOUR STORAGE KEY"

$exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password

$exportRequest

# Check status of the export
Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla conservazione dei backup a lungo termine di un backup del database SQL di Azure come alternativa ad esportare un database per scopi di archiviazione, vedere [Conservazione dei backup a lungo termine](sql-database-long-term-retention.md).
- Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.
* Per informazioni sull'importazione di un file con estensione bacpac in un database di SQL Server, vedere [Importare un file BACPAC per creare un nuovo database utente](https://msdn.microsoft.com/library/hh710052.aspx).
* Per altre informazioni sull'esportazione di un file con estensione bacpac da un database di SQL Server, vedere [Esportazione guidata applicazione livello dati](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) e come [eseguire la migrazione del primo database](sql-database-migrate-your-sql-server-database.md).

