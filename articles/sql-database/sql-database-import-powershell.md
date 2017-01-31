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
ms.date: 08/31/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 75bf523679c8d8ad6fbe4a8aa8a561d03008e59b
ms.openlocfilehash: 211f416d05b0ca998cd71a78d091b8efa39f6a7b


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>Importare un file BACPAC per creare un database SQL di Azure tramite PowerShell

> [!div class="op_single_selector"]
> * [Portale di Azure](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> 
> 

In questo articolo vengono fornite istruzioni per la creazione di un database SQL Azure importando un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) con PowerShell.

Il database viene creato da un file BACPAC (con estensione bacpac) importato da un contenitore BLOB del servizio di archiviazione di Azure. Se non si dispone di un file BACPAC in Archiviazione di Azure, vedere [Archiviare un database SQL di Azure come file BACPAC tramite PowerShell](sql-database-export-powershell.md). Se si dispone già di un file BACPAC che non è in Archiviazione di Azure, [usare AzCopy per caricarlo facilmente nell'account di archiviazione di Azure](../storage/storage-use-azcopy.md#blob-upload).

> [!NOTE]
> Il database SQL di Azure crea automaticamente e conserva i backup per ogni database dell’utente che è possibile ripristinare. Per conoscere i dettagli, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).
> 
> 

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
* Per informazioni su come connettersi ed eseguire query su un database SQL importato, vedere [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)




<!--HONumber=Dec16_HO3-->


