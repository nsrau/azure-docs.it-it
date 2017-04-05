---
title: 'PowerShell: esportare un database SQL di Azure in un file BACPAC | Documentazione Microsoft'
description: Esportare un database SQL di Azure in un file BACPAC usando PowerShell
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 9439dd83-812f-4688-97ea-2a89a864d1f3
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 41774c7f1c038ee017d719e59ed79fb68999dac8
ms.lasthandoff: 03/29/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-to-a-bacpac-file-by-using-powershell"></a>Esportare un database SQL di Azure o SQL Server in un file BACPAC usando PowerShell

Questo articolo illustra come usare PowerShell per esportare un database SQL di Azure o SQL Server in un file BACPAC (archiviato in Archiviazione BLOB di Azure). Per una panoramica dell'esportazione in un file BACPAC, vedere [Esportare in un file BACPAC](sql-database-export.md).

> [!NOTE]
> È possibile anche esportare il file di database SQL di Azure in un file BACPAC usando il [portale di Azure](sql-database-export-portal.md), [SQL Server Management Studio](sql-database-export-ssms.md) o [SQLPackage](sql-database-export-sqlpackage.md).
>

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure.
* un database SQL di Azure.
* Un [account di Archiviazione Standard di Azure](../storage/storage-create-storage-account.md)con un contenitore BLOB per archiviare il file BACPAC nella risorsa di archiviazione standard.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="export-your-database"></a>Esportazione del database
Il cmdlet [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport) invia al servizio una richiesta di esportazione del database. A seconda delle dimensioni del database, l'operazione di esportazione potrebbe richiedere molto tempo.

> [!IMPORTANT]
> Per garantire un file BACPAC coerente dal punto di vista transazionale è necessario innanzitutto [creare una copia del database](scripts/sql-database-copy-database-to-new-server-powershell.md)e quindi esportare la copia del database.
> 
> 

     $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>Monitorare lo stato dell’operazione di esportazione
Dopo aver eseguito il cmdlet [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com//powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport), è possibile controllare lo stato della richiesta eseguendo il cmdlet [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/get-azurermsqldatabaseimportexportstatus). L'esecuzione di questo cmdlet subito dopo la richiesta restituisce in genere **Status: InProgress**. Al termine dell'esportazione, il messaggio restituito è **Status: Succeeded**.

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="export-sql-database-example"></a>Esempio di esportazione di un database SQL
Nell'esempio seguente viene esportato un database SQL esistente in un file BACPAC e quindi viene spiegato come controllare lo stato dell'operazione di esportazione.

Per eseguire l'esempio, esistono alcune variabili da sostituire con i valori specifici relativi al database e all'account di archiviazione. Nel [Portale di Azure](https://portal.azure.com)accedere all'account di archiviazione per ottenerne il nome, insieme al nome del contenitore BLOB e al valore della chiave. Per trovare la chiave, fare clic su **Chiavi di accesso** nel pannello dell'account di archiviazione.

Sostituire quanto segue `VARIABLE-VALUES` con i valori relativi alle specifiche risorse di Azure. Il nome del database indica il database esistente che si desidera esportare.

    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
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

## <a name="automate-export-using-azure-automation"></a>Automatizzare l'esportazione mediante Automazione di Azure

La funzionalità di esportazione automatizzata di database SQL di Azure, ora disponibile in anteprima, verrà ritirata il 1° marzo 2017. A partire dal 1° dicembre 2016, non è più possibile configurare l'esportazione automatizzata per database SQL. Tutti i processi di esportazione automatizzata esistenti continueranno a funzionare fino al 1° marzo 2017. Dal 1° dicembre 2016, è possibile usare la [conservazione dei backup a lungo termine](sql-database-long-term-retention.md) o [Automazione di Azure](../automation/automation-intro.md) per archiviare periodicamente i database SQL con PowerShell in base alla pianificazione desiderata. È possibile scaricare uno [script di esempio da GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export). 


## <a name="next-steps"></a>Passaggi successivi
* Per informazioni su come importare un database SQL di Azure tramite Powershell, vedere [Importare un file BACPAC per creare un nuovo database SQL di Azure tramite PowerShell](scripts/sql-database-import-from-bacpac-powershell.md).
* Per informazioni sull'importazione di un file BACPAC usando SQLPackage, vedere [Importare un file BACPAC nel database SQL di Azure usando SqlPackage](sql-database-import-sqlpackage.md)
* Per informazioni sull'importazione di un file BACPAC usando il portale di Azure, vedere [Importare un file BACPAC nel database SQL di Azure usando il portale di Azure](sql-database-import-portal.md)
* Per una descrizione dell'intero processo di migrazione del database SQL Server, tra cui raccomandazioni sulle prestazioni, vedere [Migrare un database SQL Server nel database SQL di Azure](sql-database-cloud-migrate.md).
* Per altre informazioni sulla conservazione dei backup a lungo termine di un backup del database SQL di Azure come alternativa ad esportare un database per scopi di archiviazione, vedere [Conservazione dei backup a lungo termine](sql-database-long-term-retention.md)
* Per informazioni sull'importazione di un file BACPAC in un database di SQL Server, vedere [Importare un file BACPAC per creare un nuovo database utente](https://msdn.microsoft.com/library/hh710052.aspx)



## <a name="additional-resources"></a>Risorse aggiuntive
* [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport)
* [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/get-azurermsqldatabaseimportexportstatus)


