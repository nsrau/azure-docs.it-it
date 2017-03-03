---
title: Copiare un database SQL di Azure tramite PowerShell | Documentazione Microsoft
description: Creare una copia di un database SQL di Azure tramite PowerShell
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 6d9839d7-9303-48d2-be0f-21ce84f95a94
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 09/08/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: edfbc0d94d9d5b33a25788772a98173187e394b8
ms.openlocfilehash: 031a17d179cee8d6cceb9ed3aefd993add1958b8
ms.lasthandoff: 02/11/2017


---
# <a name="copy-an-azure-sql-database-using-powershell"></a>Copiare un database SQL di Azure con PowerShell

Questo articolo illustra come copiare un database SQL con PowerShell nello stesso server, in un altro server e come copiare un database in un [pool elastico](sql-database-elastic-pool.md). L'operazione di copia di un database usa il cmdlet [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644\(v=azure.300\).aspx) . 

> [!NOTE]
> È possibile anche copiare un database SQL usando il [portale Azure](sql-database-copy-portal.md) o [Transact-SQL](sql-database-copy-transact-sql.md).
>

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Un database SQL di Azure (database da copiare). Se non si dispone di un database SQL, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).
* La versione più recente di Azure PowerShell. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

Molte nuove funzionalità del database SQL sono supportate solo con il [modello di distribuzione Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Negli esempi vengono quindi usati i [cmdlet di PowerShell per il database SQL di Azure](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx) per Resource Manager. I [cmdlet del database SQL di Azure (versione classica)](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) del modello di distribuzione classica esistenti sono supportati per compatibilità con le versioni precedenti, ma è consigliabile l'uso dei cmdlet per Resource Manager.

> [!NOTE]
> A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.
> 
> 

## <a name="copy-a-sql-database-to-the-same-server"></a>Copiare un database SQL nello stesso server
Per creare la copia nello stesso server, omettere il parametro `-CopyServerName` (o impostarlo sullo stesso server).

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## <a name="copy-a-sql-database-to-a-different-server"></a>Copiare un database SQL in un server diverso
Per creare la copia in un altro server, includere il parametro `-CopyServerName` e impostarlo su un server diverso. Il server *di copia* deve già esistere. Se si trova in un gruppo di risorse diverso, è necessario includere anche il parametro `-CopyResourceGroupName` .

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## <a name="copy-a-sql-database-into-an-elastic-pool"></a>Copiare un database SQL in un pool elastico
Per creare una copia di un database SQL in un pool, impostare il parametro `-ElasticPoolName` su un pool esistente.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## <a name="resolve-logins"></a>Risolvere gli account di accesso
Per risolvere gli account di accesso al termine dell'operazione di copia, vedere [Risolvere gli account di accesso](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)

## <a name="example-powershell-script"></a>Script di PowerShell di esempio
Lo script seguente si basa sul presupposto che tutti i gruppi di risorse, i server e il pool esistano già (sostituire i valori delle variabili con le risorse esistenti). Tutti gli elementi devono essere presenti, tranne la copia del database.

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId


    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"

    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"

    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName

    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName

    # Copy a database into an elastic pool
    # ---------------------------------------------
    $poolName = "pool1"

    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName





## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulla gestione di utenti e account di accesso durante la copia di un database in un server logico diverso, vedere [Come gestire la sicurezza dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).
* Per esportare un database in un file BACPAC usando PowerShell, vedere [Esportare il database in un file BACPAC usando PowerShell](sql-database-export-powershell.md).
* [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
* [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

## <a name="additional-resources"></a>Risorse aggiuntive
* [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644\(v=azure.300\).aspx)
* [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687\(v=azure.300\).aspx)
* [Gestire gli accessi](sql-database-manage-logins.md)
* [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)
* [Esportare il database in un BACPAC](sql-database-export.md)
* [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
* [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)
* [Informazioni di riferimento sui cmdlet PowerShell del database SQL di Azure](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx)


