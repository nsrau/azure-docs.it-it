---
title: Importare un file BACPAC per creare un database SQL di Azure | Microsoft Docs
description: Creare un nuovo database SQL di Azure importando un file BACPAC.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 3eaa12b5ba735d1e2aa0e074054328942a3041eb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57900099"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Guida introduttiva: Importare un file BACPAC in un database di database SQL di Azure

È possibile importare un database SQL Server in un database di database SQL di Azure usando un file [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac). È possibile importare i dati da un file `BACPAC` archiviato in un archiviazione BLOB di Azure (solo archiviazione Standard) o da una risorsa di archiviazione locale in una posizione locale. Per ottimizzare la velocità di importazione fornendo un maggior numero di risorse più veloci, ridimensionare il database a un livello di servizio e a una dimensione di calcolo superiori durante il processo di importazione. Al termine dell'importazione, sarà quindi possibile ridurre le caratteristiche.

> [!NOTE]
> Il livello di compatibilità del database importato si basa sul livello di compatibilità del database di origine.
> [!IMPORTANT]
> Dopo aver importato il database, è possibile scegliere di usare il database al livello di compatibilità corrente (livello 100 per il database AdventureWorks2008R2) o a un livello superiore. Per altre informazioni sulle implicazioni e le opzioni per il funzionamento di un database a un livello di compatibilità specifico, vedere [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Livello di compatibilità ALTER DATABASE). Vedere anche [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) per informazioni sulle impostazioni a livello di database aggiuntive relative ai livelli di compatibilità.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Eseguire l'importazione da un file BACPAC nel portale di Azure

Il [portale di Azure](https://portal.azure.com) supporta *solo* la creazione di un database singolo nel database SQL di Azure e *solo* da un file BACPAC salvato nell'archivio BLOB di Azure.

> [!NOTE]
> [Un'istanza gestita](sql-database-managed-instance.md) non supporta attualmente la migrazione di un database in un database di istanza da un file BACPAC tramite il portale di Azure. Per importare un'istanza gestita, usare SQL Server Management Studio o SQLPackage.

1. Per importare da un file BACPAC in un nuovo database singolo usando il portale di Azure, aprire la pagina del server di database appropriata e quindi fare clic su **Importa database** sulla barra degli strumenti.  

   ![Importazione database 1](./media/sql-database-import/import1.png)

2. Selezionare l'account di archiviazione e il contenitore per il file BACPAC e quindi selezionare il file BACPAC da cui eseguire l'importazione.
3. Specificare la nuova dimensione del database (generalmente uguale all'origine) e specificare le credenziali dell'istanza SQL Server di destinazione. Per un elenco di valori possibili per un nuovo database SQL di Azure, vedere [Create Database](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current) (Creare database).

   ![Importazione database 2](./media/sql-database-import/import2.png)

4. Fare clic su **OK**.

5. Per monitorare lo stato di avanzamento dell'importazione, aprire la pagina del server del database e in **Impostazioni** selezionare **Cronologia importazioni/esportazioni**. Se l'operazione ha esito positivo, l'importazione visualizzerà lo stato **Completato**.

   ![Stato di importazione del database](./media/sql-database-import/import-status.png)

6. Per verificare che il database sia attivo sul server di database, selezionare **Database SQL** e verificare che il nuovo database sia **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Eseguire l'importazione da un file BACPAC usando SqlPackage

Per importare un database SQL Server tramite l'utilità della riga di comando [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage), vedere la sezione relativa a [parametri e proprietà dell'importazione](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage include le versioni più recenti di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). È possibile scaricare la versione più recente di [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) anche dall'Area download Microsoft.

Per la scalabilità e le prestazioni, è consigliabile usare SqlPackage, anziché il portale di Azure, nella maggior parte degli ambienti di produzione. Per informazioni da parte del team di consulenza clienti di SQL Server sull'uso di file `BACPAC` per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.

Per la scalabilità e le prestazioni, è consigliabile usare SqlPackage nella maggior parte degli ambienti di produzione. Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.

Il comando SqlPackage seguente importa il database **AdventureWorks2008R2** dall'archivio locale a un server di database SQL di Azure denominato **mynewserver20170403**. Crea un nuovo database denominato **myMigratedDatabase** con un livello di servizio **Premium** e un obiettivo di servizio **P6**. Modificare questi valori in base alle esigenze specifiche dell'ambiente.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Per connettersi a un server di database SQL con la gestione di un singolo database protetto da un firewall aziendale, è necessario che nel firewall sia aperta la porta 1433. Per connettersi a un'istanza gestita, è necessario disporre di una [connessione da punto a sito](sql-database-managed-instance-configure-p2s.md) o una connessione ExpressRoute.
>

Questo esempio illustra come importare un database usando SqlPackage con l'autenticazione universale di Active Directory.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>Importare in un database singolo da un file BACPAC usando PowerShell

> [!NOTE]
> [Un'istanza gestita](sql-database-managed-instance.md) attualmente non supporta la migrazione di un database in un database di istanza da un file BACPAC usando Azure PowerShell. Per importare un'istanza gestita, usare SQL Server Management Studio o SQLPackage.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure PowerShell per Resource Manager è ancora supportato dal Database SQL di Azure, ma i progetti di sviluppo future è per il modulo Az.Sql. Per questi cmdlet, vedere [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli AzureRm sono sostanzialmente identici.

Usare la [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) cmdlet per inviare una richiesta di database di importazione per il servizio Database SQL di Azure. A seconda delle dimensioni del database, l'importazione può richiedere del tempo.

 ```powershell
 $importRequest = New-AzSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 È possibile usare la [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) cmdlet per controllare lo stato di avanzamento dell'importazione. L'esecuzione di questo cmdlet subito dopo la richiesta restituisce in genere **Status: InProgress**. L'importazione è completa quando viene visualizzato il messaggio **Status: Succeeded**.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
> Per un altro esempio di script, vedere [Importare un database da un file BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Limitazioni

L'importazione in un database nel pool elastico non è supportata. È possibile importare i dati in un database singolo e quindi spostare quest'ultimo in un pool elastico.

## <a name="import-using-wizards"></a>Importazione con procedure guidate

È anche possibile usare queste procedure guidate.

- [Procedura guidata Importa applicazione livello dati in SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Importazione/Esportazione guidata SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come connettersi ed eseguire query su un database SQL importato, vedere [Guida introduttiva: Database SQL di Azure: usare SQL Server Management Studio per connettersi ed eseguire query sui dati](sql-database-connect-query-ssms.md).
- Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.
- Per una descrizione dell'intero processo di migrazione del database SQL Server, con raccomandazioni sulle prestazioni, vedere [Migrazione di un database SQL Server al database SQL di Azure](sql-database-single-database-migrate.md).
- Per informazioni su come gestire e condividere chiavi di archiviazione e firme di accesso condiviso in modo sicuro, vedere [Guida alla sicurezza di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
