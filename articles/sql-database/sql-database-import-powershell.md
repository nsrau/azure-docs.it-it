---
title: Importare un file BACPAC per creare un database SQL di Azure tramite PowerShell | Documentazione Microsoft
description: Importare un file BACPAC per creare un database SQL di Azure tramite PowerShell
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 8d78da13-43fe-4447-92e0-0a41d0321fd4
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 02/07/2017
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: e6f0d661465c813ec310b8c69ab1ee06e4f95401
ms.openlocfilehash: 45ec817e62e7967549602adfd2c9d2d3f2484987


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>Importare un file BACPAC per creare un database SQL di Azure tramite PowerShell

In questo articolo vengono fornite istruzioni per la creazione di un database SQL Azure importando un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) con PowerShell.

## <a name="prequisites"></a>Prerequisiti

Per importare un database SQL è necessario quanto segue:

* Una sottoscrizione di Azure. Se è necessaria una sottoscrizione di Azure, fare semplicemente clic su **Versione di prova gratuita** nella parte superiore della pagina, quindi riprendere l'articolo e terminarlo.
* Il file BACPAC del database che si vuole importare. Il file BACPAC deve essere inserito in un contenitore BLOB di un [account di archiviazione di Azure](../storage/storage-create-storage-account.md) .

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="set-up-the-variables-for-your-environment"></a>Impostare le variabili per l'ambiente specifico
Esistono alcune variabili in cui è necessario sostituire i valori di esempio con i valori specifici per il database e l'account di archiviazione.

Il nome del server deve corrispondere a un server esistente nella sottoscrizione selezionata nel passaggio precedente. Tale server deve essere quello in cui si desidera creare il database. L'importazione di un database direttamente in un pool elastico non è supportata. È tuttavia possibile importarlo come database singolo e quindi spostare il database in un pool.

Il nome del database è il nome desiderato per il nuovo database.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


Le seguenti variabili sono dell'account di archiviazione in cui si trova il file BACPAC. Nel [portale di Azure](https://portal.azure.com)passare all'account di archiviazione per ottenere questi valori. È possibile trovare la chiave di accesso primaria facendo clic su **Tutte le impostazioni** e su **Chiavi** dal pannello dell'account di archiviazione.

Il nome del BLOB è il nome di un file BACPAC esistente da cui si desidera creare il database. È necessario includere l'estensione bacpac.

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


Eseguendo il cmdlet [Get-Credential](https://msdn.microsoft.com/library/azure/hh849815\(v=azure.300\).aspx) si apre una finestra in cui vengono richiesti nome utente e password. Immettere l'account di accesso di amministrazione e la password per il server di database SQL ($ServerName sopra) e non il nome utente e la password dell'account Azure.

    $credential = Get-Credential


## <a name="import-the-database"></a>Importare il database
Tale comando invia una richiesta di importazione del database al servizio. A seconda delle dimensioni del database, l'operazione di esportazione potrebbe richiedere diverso tempo.

    $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -StorageKeytype $StorageKeyType -StorageKey $StorageKey -StorageUri $StorageUri -AdministratorLogin $credential.UserName -AdministratorLoginPassword $credential.Password -Edition Standard -ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## <a name="monitor-the-progress-of-the-operation"></a>Monitorare lo stato dell’operazione
Dopo aver eseguito il cmdlet [New-AzureRmSqlDatabaseImport](https://msdn.microsoft.com/library/azure/mt707793\(v=azure.300\).aspx), è possibile controllare lo stato della richiesta eseguendo il cmdlet [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx).

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="sql-database-powershell-import-script"></a>Script di importazione di PowerShell per il database SQL
    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -StorageKeytype $StorageKeyType -StorageKey $StorageKey -StorageUri $StorageUri -AdministratorLogin $credential.UserName -AdministratorLoginPassword $credential.Password -Edition Standard -ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="next-steps"></a>Passaggi successivi
* Per informazioni su come connettersi ed eseguire query su un database SQL importato, vedere [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md).
* Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.
* Per una descrizione dell'intero processo di migrazione del database SQL Server, tra cui raccomandazioni sulle prestazioni, vedere [Migrare un database SQL Server nel database SQL di Azure](sql-database-cloud-migrate.md).





<!--HONumber=Feb17_HO2-->


