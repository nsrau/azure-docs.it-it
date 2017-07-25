---
title: Importare un file BACPAC per creare un database SQL di Azure | Microsoft Docs
description: Creare un nuovo database SQL di Azure importando un file BACPAC.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: cf9a9631-56aa-4985-a565-1cacc297871d
ms.service: sql-database
ms.custom: load & move data
ms.devlang: NA
ms.date: 04/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 4ddbeb78208d8e32fe5e505f396fbfec4e5d6c0a
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017


---
# <a name="import-a-bacpac-file-to-a-new-azure-sql-database"></a>Importare un file BACPAC in un nuovo database SQL di Azure

Quando è necessario importare un database da un archivio o eseguire la migrazione da un'altra piattaforma, è possibile importare lo schema e i dati del database da un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Un file BACPAC è un file ZIP con un'estensione bacpac contenente i metadati e dati da un database di SQL Server. È possibile importare un file BACPAC dall'archiviazione BLOB di Azure (solo archiviazione standard) o dall'archiviazione locale in un percorso locale. Per ottimizzare la velocità di importazione, è consigliabile specificare un livello di servizio e prestazioni superiore, ad esempio un P6, e quindi applicare la scalabilità verso il basso in base alle esigenze dopo che l'importazione ha avuto esito positivo. Il livello di compatibilità del database dopo l'importazione si basa anche sul livello di compatibilità del database di origine. 

> [!IMPORTANT] 
> Dopo la migrazione del database al database SQL di Azure, è possibile scegliere per il funzionamento del database al livello di compatibilità corrente (livello 100 per il database AdventureWorks2008R2) o a un livello superiore. Per altre informazioni sulle implicazioni e le opzioni per il funzionamento di un database a un livello di compatibilità specifico, vedere [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Livello di compatibilità ALTER DATABASE). Vedere anche [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) per informazioni sulle impostazioni a livello di database aggiuntive relative ai livelli di compatibilità.   >

> [!NOTE]
> Per importare un file BACPAC in un nuovo database, è necessario prima creare un server logico di database SQL di Azure. Per un'esercitazione che spiega come eseguire la migrazione di un database SQL Server al database SQL di Azure tramite SQLPackage, vedere [Migrate a SQL Server Database](sql-database-migrate-your-sql-server-database.md) (Eseguire la migrazione di un database SQL Server)
>

## <a name="import-from-a-bacpac-file-using-azure-portal"></a>Importare da un file BACPAC tramite il portale di Azure

Questo articolo illustra come usare il [portale di Azure](https://portal.azure.com) per creare un database SQL di Azure a partire da un file BACPAC archiviato nell'archiviazione BLOB di Azure. L'importazione tramite il portale di Azure supporta solo l'importazione di un file BACPAC dall'archiviazione BLOB di Azure.

Per importare un database tramite il portale di Azure, aprire la pagina per il database e fare clic su **Importa** sulla barra degli strumenti. Specificare l'account di archiviazione e il contenitore e quindi selezionare il file BACPAC che si vuole importare. Selezionare la dimensione del nuovo database (in genere lo stesso dell'origine) e specificare le credenziali di SQL Server di destinazione.  

   ![Importazione di database](./media/sql-database-import/import.png)

Per monitorare lo stato di avanzamento dell'operazione di importazione, aprire la pagina per il server logico contenente il database importato. Scorrere fino a **Operazioni** e quindi fare clic sulla cronologia di **Importazione/Esportazione**.

### <a name="monitor-the-progress-of-an-import-operation"></a>Monitorare lo stato di un'operazione di importazione

Per monitorare lo stato di avanzamento dell'operazione di importazione, aprire la pagina per il server logico in cui viene importato il database. Scorrere fino a **Operazioni** e quindi fare clic sulla cronologia di **Importazione/Esportazione**.
   
   ![importazione](./media/sql-database-import/import-history.png)
   ![stato importazione](./media/sql-database-import/import-status.png)

Per verificare che il database sia attivo nel server, fare clic su **Database SQL** e verificare che il nuovo database sia **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importare da un file BACPAC tramite SQLPackage

Per importare un database SQL tramite l'utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx), vedere la sezione relativa ai [parametri e proprietà dell'importazione](https://msdn.microsoft.com/library/hh550080.aspx#Import Parameters and Properties). L'utilità SQLPackage viene offerta con le versioni più recenti di [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), oppure è possibile scaricare la versione più recente di [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) direttamente dall'area download Microsoft.

È consigliabile usare l'utilità SQLPackage per la scalabilità e le prestazioni nella maggior parte degli ambienti di produzione. Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.

Vedere il comando SQLPackage seguente per uno script di esempio che illustra come importare il database **AdventureWorks2008R2** dalla risorsa di archiviazione locale a un server logico del database SQL di Azure, chiamato **mynewserver20170403** in questo esempio. Questo script illustra la creazione di un nuovo database denominato **myMigratedDatabase**, con un livello di servizio **Premium** e un obiettivo di servizio **P6**. Sostituire i valori in base alle esigenze specifiche dell'ambiente.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=ServerAdmin;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

   ![importazione sqlpackage](./media/sql-database-migrate-your-sql-server-database/sqlpackage-import.png)

> [!IMPORTANT]
> Il server logico del database SQL di Azure è in ascolto sulla porta 1433. Se si sta tentando di connettersi a un server logico del database SQL di Azure dall'interno di un firewall aziendale, questa porta deve essere aperta.
>

Questo esempio illustra come importare un database usando SqlPackage.exe con l'autenticazione universale di Active Directory:

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Importare da un file BACPAC tramite PowerShell

Usare il cmdlet [AzureRmSqlDatabaseImport New](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) per inviare una richiesta di importazione database al servizio database SQL di Azure. A seconda delle dimensioni del database, l'operazione di esportazione potrebbe richiedere diverso tempo.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName $storageaccountname).Value[0] `
    -StorageUri "http://$storageaccountname.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "ServerAdmin" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "ASecureP@assw0rd" -AsPlainText -Force)

 ```

Per verificare lo stato della richiesta di importazione, usare il cmdlet [AzureRmSqlDatabaseImportExportStatus Get](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus). L'esecuzione di questo cmdlet subito dopo la richiesta restituisce in genere **Status: InProgress**. Al termine dell'esportazione, il messaggio restituito è **Status: Succeeded**.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
Per un altro esempio di script, vedere [Importare un database da un file BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni su come connettersi ed eseguire query su un database SQL importato, vedere [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md).
* Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.
* Per una descrizione dell'intero processo di migrazione del database SQL Server, tra cui raccomandazioni sulle prestazioni, vedere [Migrare un database SQL Server nel database SQL di Azure](sql-database-cloud-migrate.md).




