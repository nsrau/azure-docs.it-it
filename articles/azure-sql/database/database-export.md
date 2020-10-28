---
title: Esportare un database SQL di Azure in un file BACPAC (il portale di Azure)
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Esportare un database in un file BACPAC usando il portale di Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
author: stevestein
ms.custom: sqldbrb=2
ms.author: sstein
ms.reviewer: ''
ms.date: 07/16/2019
ms.topic: how-to
ms.openlocfilehash: 7dc6cd580687544226b61a29ca9ccf2d1b8dff42
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92671537"
---
# <a name="export-to-a-bacpac-file---azure-sql-database-and-azure-sql-managed-instance"></a>Esportare in un file BACPAC: database SQL di Azure e Istanza gestita SQL di Azure

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Quando è necessario esportare un database per l'archiviazione o lo spostamento in un'altra piattaforma, è possibile esportare lo schema di database e i dati in un file [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#Anchor_4). Un file BACPAC è un file ZIP con estensione BACPAC contenente i metadati e i dati del database. Un file BACPAC può essere archiviato nell'archiviazione BLOB di Azure o in una risorsa di archiviazione locale in un percorso locale e successivamente nuovamente importato nel database SQL di Azure, in Istanza gestita di Azure SQL o in un'istanza di SQL Server.

## <a name="considerations"></a>Considerazioni

- Affinché un'esportazione sia coerente a livello di transazione, è necessario assicurarsi che non venga eseguita alcuna attività di scrittura durante l'esportazione o che l'esportazione venga eseguita da una [copia coerente](database-copy.md) a livello di transazione del database.
- Se si sta eseguendo l'esportazione nell'archiviazione BLOB, la dimensione massima di un file BACPAC è 200 GB. Per archiviare un file BACPAC di dimensioni maggiori, eseguire l'esportazione in una risorsa di archiviazione locale.
- L'esportazione di un file BACPAC in Archiviazione Premium di Azure usando i metodi descritti in questo articolo non è supportata.
- L'archiviazione dietro un firewall non è attualmente supportata.
- Se l'operazione di esportazione dura oltre 20 ore, potrebbe essere annullata. Per migliorare le prestazioni durante l'esportazione è possibile:

  - Aumentare temporaneamente le dimensioni di calcolo.
  - Interrompere tutte le attività di lettura e scrittura durante l'esportazione.
  - Utilizzare un [indice cluster](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described) con valori non null in tutte le tabelle di grandi dimensioni. Senza indici cluster, l'esportazione potrebbe non riuscire se dovesse durare più di 6 - 12 ore. Questo perché i servizi di esportazione devono completare la scansione della tabella prima di provare a esportarla per intero. Un modo valido di determinare se le tabelle sono ottimizzate per l'esportazione consiste nell'eseguire **DBCC SHOW_STATISTICS** e verificare che il parametro *RANGE_HI_KEY* non sia null e il relativo valore abbia distribuzione valida. Per i dettagli, vedere [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql).

> [!NOTE]
> I BACPAC non sono destinati a essere utilizzati per il backup e le operazioni di ripristino. Azure crea automaticamente i backup per ogni database utente. Per altre informazioni, vedere [Panoramica sulla continuità aziendale](business-continuity-high-availability-disaster-recover-hadr-overview.md) e [Backup del database SQL](automated-backups-overview.md).

## <a name="the-azure-portal"></a>Portale di Azure

L'esportazione di un BACPAC di un database da [Azure SQL istanza gestita](../managed-instance/sql-managed-instance-paas-overview.md) usando il portale di Azure non è attualmente supportata. In alternativa, usare SQL Server Management Studio o SqlPackage.

> [!NOTE]
> Nei computer che elaborano le richieste di importazione ed esportazione inviate tramite il portale di Azure o PowerShell deve essere archiviato il file BACPAC, oltre ai file temporanei generati da Data-Tier Application Framework (DacFX). Lo spazio su disco necessario varia significativamente tra i database con le stesse dimensioni. Potrebbe essere necessario spazio su disco fino a 3 volte la dimensione del database. I computer che eseguono la richiesta di importazione/esportazione hanno solo spazio su disco locale 450GB. Di conseguenza, alcune richieste potrebbero non riuscire restituendo l'errore `There is not enough space on the disk`. È possibile ovviare a questo problema eseguendo sqlpackage.exe in un computer con spazio su disco locale sufficiente. Si consiglia di usare [SqlPackage](#sqlpackage-utility) per importare/esportare database di dimensioni superiori a 150 GB per evitare questo problema.

1. Per esportare un database con il [portale di Azure](https://portal.azure.com), aprire la pagina relativa al database e fare clic su **Esporta** sulla barra degli strumenti.

   ![Schermata che evidenzia il pulsante Esporta.](./media/database-export/database-export1.png)

2. Specificare il nome del file BACPAC, selezionare un account di archiviazione di Azure esistente e un contenitore per l'esportazione e quindi fornire le credenziali appropriate per l'accesso al database di origine. È necessario un **account di accesso amministratore** di SQL Server anche se si è l'amministratore di Azure, perché un amministratore di Azure non equivale ad avere le autorizzazioni di amministratore nel database SQL di Azure o in azure SQL istanza gestita.

    ![Esportazione di un database](./media/database-export/database-export2.png)

3. Fare clic su **OK** .

4. Per monitorare lo stato dell'operazione di esportazione, aprire la pagina del server contenente il database da esportare. Passare a **Impostazioni** e quindi fare clic su **Cronologia importazioni/esportazioni** .

   ![Cronologia esportazioni](./media/database-export/export-history.png)

## <a name="sqlpackage-utility"></a>Utilità SQLPackage

Per esportare un database nel database SQL tramite l'utilità della riga di comando [SqlPackage](/sql/tools/sqlpackage) , vedere [esportare parametri e proprietà](/sql/tools/sqlpackage#export-parameters-and-properties). L'utilità SQLPackage viene offerta con le versioni più recenti di [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools per Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt). È tuttavia possibile scaricare la versione più recente di [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) direttamente dall'area download Microsoft.

È consigliabile usare l'utilità SQLPackage per la scalabilità e le prestazioni nella maggior parte degli ambienti di produzione. Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.

Questo esempio illustra come esportare un database usando SqlPackage.exe con l'autenticazione universale di Active Directory:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Le versioni più recenti di SQL Server Management Studio offrono una procedura guidata per esportare un database nel database SQL di Azure o in un database SQL Istanza gestita in un file BACPAC. Vedere [Esportazione guidata applicazione livello dati](/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="powershell"></a>PowerShell

> [!NOTE]
> Il [istanza gestita SQL di Azure](../managed-instance/sql-managed-instance-paas-overview.md) attualmente non supporta l'esportazione di un database in un file BACPAC usando Azure PowerShell. Per esportare un'istanza gestita in un file BACPAC, usare SQL Server Management Studio o SQLPackage.

Usare il cmdlet [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) per inviare una richiesta di esportazione del database al servizio database SQL di Azure. A seconda delle dimensioni del database, l'operazione di esportazione potrebbe richiedere molto tempo.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Per controllare lo stato della richiesta di esportazione, usare il cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . L'esecuzione di questo cmdlet subito dopo la richiesta restituisce in genere **Status: InProgress** . Al termine dell'esportazione, il messaggio restituito è **Status: Succeeded** .

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla conservazione dei backup a lungo termine di un database singolo e per i database in pool come alternativa all'esportazione di un database per scopi di archiviazione, vedere [conservazione dei backup a lungo termine](long-term-retention-overview.md). È possibile usare processi di SQL Agent per pianificare [backup dei database di sola copia](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) come alternativa alla conservazione dei backup a lungo termine.
- Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.
- Per informazioni sull'importazione di un file BACPAC in un database di SQL Server, vedere [Importare un file BACPAC in un database di SQL Server](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database).
- Per altre informazioni sull'esportazione di un file con estensione bacpac da un database di SQL Server, vedere [Esportare un'applicazione livello dati](/sql/relational-databases/data-tier-applications/export-a-data-tier-application).
- Per informazioni sull'uso del servizio migrazione dati per eseguire la migrazione di un database, vedere [eseguire la migrazione da SQL Server al database SQL di Azure offline con DMS](../../dms/tutorial-sql-server-to-azure-sql.md).
- Se si sta eseguendo l'esportazione da SQL Server come preparazione alla migrazione nel database SQL di Azure, vedere [Migrare un database SQL Server nel database SQL di Azure](migrate-to-database-from-sql-server.md).
- Per informazioni su come gestire e condividere chiavi di archiviazione e firme di accesso condiviso in modo sicuro, vedere [Guida alla sicurezza di Archiviazione di Azure](../../storage/blobs/security-recommendations.md).