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
ms.custom: load & move data
ms.devlang: NA
ms.date: 10/11/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 4427b74f205dcd2eabf825bbfc345279dc303f96
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2017
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Esportare un database SQL di Azure in un file BACPAC

Quando è necessario esportare un database per l'archiviazione o lo spostamento in un'altra piattaforma, è possibile esportare lo schema di database e i dati in un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Un file BACPAC è un file ZIP con un'estensione bacpac contenente i metadati e dati da un database di SQL Server. È possibile memorizzare questo tipo di file in un'archiviazione BLOB di Azure o in un'archiviazione locale e successivamente importarlo nuovamente nel database SQL di Azure o in un'installazione locale di SQL Server. 

> [!IMPORTANT] 
> La funzionalità di esportazione automatizzata di database SQL di Azure è stata ritirata il 1° marzo 2017. È possibile usare la [conservazione dei backup a lungo termine](sql-database-long-term-retention.md
) o [Automazione di Azure](https://github.com/Microsoft/azure-docs/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) per archiviare periodicamente i database SQL con PowerShell in base alla pianificazione desiderata. Per un esempio, scaricare lo [script di PowerShell di esempio](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) da Github.
>

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Considerazioni relative all'esportazione di un database SQL di Azure

* Perché un'esportazione sia coerente dal punto di vista transazionale, è necessario assicurarsi che non si verifichino attività di scrittura durante l'esportazione o che l'esportazione sia eseguita da una [copia coerente dal punto di vista transazionale](sql-database-copy.md) del database SQL di Azure.
* Se si sta eseguendo l'esportazione nell'archiviazione BLOB, la dimensione massima di un file BACPAC è 200 GB. Per archiviare un file BACPAC di dimensioni maggiori, eseguire l'esportazione in una risorsa di archiviazione locale.
* L'esportazione di un file BACPAC in Archiviazione Premium di Azure usando i metodi descritti in questo articolo non è supportata.
* Se l'operazione di esportazione da un database SQL di Azure dura oltre 20 ore, potrebbe essere annullata. Per migliorare le prestazioni durante l'esportazione è possibile:
  * Aumentare temporaneamente il livello di servizio.
  * Interrompere tutte le attività di lettura e scrittura durante l'esportazione.
  * Utilizzare un [indice cluster](https://msdn.microsoft.com/library/ms190457.aspx) con valori non null in tutte le tabelle di grandi dimensioni. Senza indici cluster, l'esportazione potrebbe non riuscire se dovesse durare più di 6 - 12 ore. Questo perché i servizi di esportazione devono completare la scansione della tabella prima di provare a esportarla per intero. Un modo valido di determinare se le tabelle sono ottimizzate per l'esportazione consiste nell'eseguire **DBCC SHOW_STATISTICS** e verificare che il parametro *RANGE_HI_KEY* non sia null e il relativo valore abbia distribuzione valida. Per i dettagli, vedere [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> I BACPAC non sono destinati a essere utilizzati per il backup e le operazioni di ripristino. Il database SQL di Azure crea automaticamente i backup per ogni database dell'utente. Per altre informazioni, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md) e [Backup del database SQL](sql-database-automated-backups.md).  
> 

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Eseguire l'esportazione in un file BACPAC con il portale di Azure

Per esportare un database con il [portale di Azure](https://portal.azure.com), aprire la pagina relativa al database e fare clic su **Esporta** sulla barra degli strumenti. Specificare il nome del file BACPAC, l'account di archiviazione di Azure, un contenitore per l'esportazione e le credenziali per la connessione al database di origine.  

![Esportazione di un database](./media/sql-database-export/database-export.png)

Per monitorare lo stato di avanzamento dell'operazione di esportazione, aprire la pagina per il server logico contenente il database da esportare. Scorrere fino a **Operazioni** e quindi fare clic sulla cronologia di **Importazione/Esportazione**.

![Cronologia delle esportazioni](./media/sql-database-export/export-history.png)
![Stato nella cronologia delle esportazioni](./media/sql-database-export/export-history2.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Eseguire l'esportazione in un file BACPAC con l'utilità SQLPackage

Per esportare un database SQL tramite l'utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx), vedere la sezione relativa ai [parametri e proprietà dell'importazione](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties). L'utilità SQLPackage viene offerta con le versioni più recenti di [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). È tuttavia possibile scaricare la versione più recente di [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) direttamente dall'area download Microsoft.

È consigliabile usare l'utilità SQLPackage per la scalabilità e le prestazioni nella maggior parte degli ambienti di produzione. Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.

Questo esempio illustra come esportare un database usando SqlPackage.exe con l'autenticazione universale di Active Directory:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Eseguire l'esportazione in un file BACPAC con SQL Server Management Studio (SSMS)

Le versioni più recenti di SQL Server Management Studio includono anche una procedura guidata per l'esportazione di un database SQL di Azure in un file BACPAC. Vedere [Esportazione guidata applicazione livello dati](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Eseguire l'esportazione in un file BACPAC con PowerShell

Usare il cmdlet [New-AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport) per inviare una richiesta di esportazione database al servizio database SQL di Azure. A seconda delle dimensioni del database, l'operazione di esportazione potrebbe richiedere molto tempo.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

Per verificare lo stato della richiesta di esportazione, usare il cmdlet [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus). L'esecuzione di questo cmdlet subito dopo la richiesta restituisce in genere **Status: InProgress**. Al termine dell'esportazione, il messaggio restituito è **Status: Succeeded**.

```powershell
$exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    $exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla conservazione dei backup a lungo termine di un backup del database SQL di Azure come alternativa ad esportare un database per scopi di archiviazione, vedere [Conservazione dei backup a lungo termine](sql-database-long-term-retention.md).
- Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.
* Per informazioni sull'importazione di un file con estensione bacpac in un database di SQL Server, vedere [Importare un file BACPAC per creare un nuovo database utente](https://msdn.microsoft.com/library/hh710052.aspx).
* Per altre informazioni sull'esportazione di un file con estensione bacpac da un database di SQL Server, vedere [Esportazione guidata applicazione livello dati](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) e come [eseguire la migrazione del primo database](sql-database-migrate-your-sql-server-database.md).
* Se si sta eseguendo l'esportazione da SQL Server come preparazione alla migrazione nel database SQL di Azure, vedere [Migrare un database SQL Server nel database SQL di Azure](sql-database-cloud-migrate.md).
