---
title: Importare un file BACPAC per creare un database SQL di Azure
description: Creare un nuovo database in Database SQL di Azure o in Istanza gestita di SQL di Azure da un file BACPAC.
services: sql-database
ms.service: sql-db-mi
ms.subservice: migrate
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/29/2020
ms.openlocfilehash: 30a511caec82ead406f0a80f107e4261a707bfdb
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040174"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Avvio rapido: Importare un file BACPAC in un database in Database SQL di Azure o in Istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

È possibile importare un database SQL Server in Database SQL di Azure o in Istanza gestita di SQL usando un file [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac). È possibile importare i dati da un file BACPAC archiviato in un archiviazione BLOB di Azure (solo archiviazione Standard) o da una risorsa di archiviazione locale in una posizione locale. Per ottimizzare la velocità di importazione fornendo un maggior numero di risorse più veloci, ridimensionare il database a un livello di servizio e dimensioni di calcolo superiori durante il processo di importazione. Al termine dell'importazione, sarà quindi possibile ridurre le caratteristiche.

> [!NOTE]
> Il livello di compatibilità del database importato si basa sul livello di compatibilità del database di origine.

> [!IMPORTANT]
> Dopo aver importato il database, è possibile scegliere di usare il database al livello di compatibilità corrente (livello 100 per il database AdventureWorks2008R2) o a un livello superiore. Per altre informazioni sulle implicazioni e le opzioni per il funzionamento di un database a un livello di compatibilità specifico, vedere [ALTER DATABASE Compatibility Level](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Livello di compatibilità ALTER DATABASE). Vedere anche [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) per informazioni sulle impostazioni a livello di database aggiuntive relative ai livelli di compatibilità.

## <a name="using-azure-portal"></a>Uso del portale di Azure

Guardare questo video per informazioni su come eseguire l'importazione da un file BACPAC nel portale di Azure oppure continuare a leggere:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

Il [portale di Azure supporta](https://portal.azure.com) *solo* la creazione di un database singolo nel database SQL di Azure e *solo* da un file BACPAC salvato nell'archivio BLOB di Azure.

Per eseguire la migrazione di un database in un'[istanza gestita di SQL di Azure](../managed-instance/sql-managed-instance-paas-overview.md) da un file BACPAC, usare SQL Server Management Studio o SqlPackage. L'uso del portale di Azure o di Azure PowerShell non è attualmente supportato.

> [!NOTE]
> Nei computer che elaborano le richieste di importazione ed esportazione inviate tramite il portale di Azure o PowerShell deve essere archiviato il file BACPAC, oltre ai file temporanei generati da Data-Tier Application Framework (DacFX). Lo spazio su disco necessario varia significativamente tra i database con le stesse dimensioni. Potrebbe essere necessario spazio su disco fino a 3 volte la dimensione del database. I computer che eseguono le richieste di importazione ed esportazione hanno solo 450 GB di spazio su disco locale. Di conseguenza, alcune richieste potrebbero non riuscire restituendo l'errore `There is not enough space on the disk`. È possibile ovviare a questo problema eseguendo sqlpackage.exe in un computer con spazio su disco locale sufficiente. Per evitare il problema, è consigliabile usare SqlPackage per importare/esportare database di dimensioni superiori a 150 GB.

1. Per eseguire l'importazione da un file BACPAC in un nuovo database singolo usando il portale di Azure, aprire la pagina del server appropriato e quindi selezionare **Importa database** sulla barra degli strumenti.  

   ![Importazione database 1](./media/database-import/sql-server-import-database.png)

1. Selezionare l'account di archiviazione e il contenitore per il file BACPAC e quindi selezionare il file BACPAC da cui eseguire l'importazione.

1. Specificare la nuova dimensione del database (generalmente uguale all'origine) e specificare le credenziali dell'istanza SQL Server di destinazione. Per un elenco di valori possibili per un nuovo database in Database SQL di Azure, vedere [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Importazione database 2](./media/database-import/sql-server-import-database-settings.png)

1. Fare clic su **OK** .

1. Per monitorare lo stato di avanzamento dell'importazione, aprire la pagina del server del database e in **Impostazioni** selezionare **Cronologia importazioni/esportazioni** . Se l'operazione ha esito positivo, l'importazione visualizzerà lo stato **Completato** .

   ![Stato di importazione del database](./media/database-import/sql-server-import-database-history.png)

1. Per verificare che il database sia attivo sul server, selezionare **Database SQL** e verificare che il nuovo database sia **Online** .

## <a name="using-sqlpackage"></a>Uso di SqlPackage

Per importare un database SQL Server tramite l'utilità della riga di comando [SqlPackage](/sql/tools/sqlpackage), vedere la sezione relativa a [parametri e proprietà dell'importazione](/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage è incluso in [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools per Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt). È possibile scaricare la versione più recente di [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) anche dall'Area download Microsoft. 

Per la scalabilità e le prestazioni, è consigliabile usare SqlPackage, anziché il portale di Azure, nella maggior parte degli ambienti di produzione. Per informazioni da parte del team di consulenza clienti di SQL Server sull'uso di file `BACPAC` per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.

Il modello di provisioning basato su unità di elaborazione di database supporta valori per le dimensioni massime del database specifici per ogni livello. Quando si importa un database [usare uno di questi valori supportati](/sql/t-sql/statements/create-database-transact-sql). 

Il comando di SqlPackage seguente importa il database **AdventureWorks2008R2** dall'archivio locale in un server SQL logico denominato **mynewserver20170403** . Crea un nuovo database denominato **myMigratedDatabase** con un livello di servizio **Premium** e un obiettivo di servizio **P6** . Modificare questi valori in base alle esigenze specifiche dell'ambiente.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Per connettersi a un database SQL di Azure protetto da un firewall aziendale, è necessario che nel firewall sia aperta la porta 1433. Per connettersi a un'istanza gestita di SQL, è necessario disporre di una [connessione da punto a sito](../managed-instance/point-to-site-p2s-configure.md) o una connessione ExpressRoute.

Questo esempio illustra come importare un database usando SqlPackage con l'autenticazione universale di Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Utilizzo di PowerShell

> [!NOTE]
> Un'[istanza gestita di SQL](../managed-instance/sql-managed-instance-paas-overview.md) non supporta attualmente la migrazione di un database in un database dell'istanza da un file BACPAC tramite Azure PowerShell. Per eseguire l'importazione in un'istanza gestita di SQL, usare SQL Server Management Studio o SQLPackage.

> [!NOTE]
> Nei computer che elaborano le richieste di importazione ed esportazione inviate tramite il portale o PowerShell deve essere archiviato il file BACPAC, oltre ai file temporanei generati da Data-Tier Application Framework (DacFX). Lo spazio su disco necessario varia notevolmente tra i database con le stesse dimensioni. Potrebbe essere necessario spazio su disco fino a 3 volte la dimensione del database. I computer che eseguono le richieste di importazione ed esportazione hanno solo 450 GB di spazio su disco locale. Di conseguenza, alcune richieste potrebbero non riuscire restituendo l'errore "Lo spazio su disco non è sufficiente". È possibile ovviare a questo problema eseguendo sqlpackage.exe in un computer con spazio su disco locale sufficiente. Se occorre importare o esportare database di dimensioni superiori a 150 GB, usare SqlPackage per evitare questo problema.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Il modulo AzureRM continuerà a ricevere correzioni di bug almeno fino a dicembre 2020.  Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](/powershell/azure/new-azureps-module-az).

Usare il cmdlet [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) per inviare una richiesta di importazione database ad Azure. A seconda delle dimensioni del database, l'importazione può richiedere del tempo. Il modello di provisioning basato su unità di elaborazione di database supporta valori per le dimensioni massime del database specifici per ogni livello. Quando si importa un database [usare uno di questi valori supportati](/sql/t-sql/statements/create-database-transact-sql). 

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

È possibile usare il cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) per controllare lo stato dell'importazione. L'esecuzione di questo cmdlet subito dopo la richiesta restituisce in genere `Status: InProgress`. L'importazione è completa quando viene visualizzato il messaggio `Status: Succeeded`.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando [az-sql-db-import](/cli/azure/sql/db#az-sql-db-import) per inviare una richiesta di importazione database ad Azure. A seconda delle dimensioni del database, l'importazione può richiedere del tempo. Il modello di provisioning basato su unità di elaborazione di database supporta valori per le dimensioni massime del database specifici per ogni livello. Quando si importa un database [usare uno di questi valori supportati](/sql/t-sql/statements/create-database-transact-sql). 

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Per un altro esempio di script, vedere [Importare un database da un file BACPAC](scripts/import-from-bacpac-powershell.md).

## <a name="limitations"></a>Limitazioni

- L'importazione in un database nel pool elastico non è supportata. È possibile importare i dati in un database singolo e quindi spostare quest'ultimo in un pool elastico.
- Il servizio di importazione/esportazione non funziona quando l'opzione Consenti l'accesso a Servizi di Azure è disattivata. È però possibile aggirare il problema eseguendo manualmente sqlpackage.exe da una macchina virtuale di Azure o eseguendo l'esportazione direttamente nel codice usando l'API DACFx.
- L'importazione non supporta la specifica di una ridondanza di archiviazione di backup durante la creazione di un nuovo database, quindi viene usata la ridondanza geografica predefinita. Per aggirare il problema, creare prima di tutto un database vuoto con la ridondanza dell'archiviazione di backup desiderata usando il portale di Azure o PowerShell, quindi importare il file BACPAC in questo database vuoto. 

> [!NOTE]
> La ridondanza dell'archivio di backup configurabile del database SQL di Azure è attualmente disponibile in anteprima pubblica solo nell'area di Azure Asia sud-orientale.

## <a name="import-using-wizards"></a>Importazione con procedure guidate

È anche possibile usare queste procedure guidate.

- [Procedura guidata Importa applicazione livello dati in SQL Server Management Studio](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Importazione/Esportazione guidata SQL Server](/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come connettersi ed eseguire query su un database SQL di Azure, vedere [Avvio rapido: Database SQL di Azure: usare SQL Server Management Studio per connettersi ed eseguire query sui dati](connect-query-ssms.md).
- Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.
- Per una descrizione dell'intero processo di migrazione del database SQL Server, con raccomandazioni sulle prestazioni, vedere [Migrazione di un database SQL Server al database SQL di Azure](migrate-to-database-from-sql-server.md).
- Per informazioni su come gestire e condividere chiavi di archiviazione e firme di accesso condiviso in modo sicuro, vedere [Guida alla sicurezza di Archiviazione di Azure](../../storage/blobs/security-recommendations.md).
