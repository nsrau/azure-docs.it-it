<properties
   pageTitle="Cmdlet di PowerShell per Azure SQL Data Warehouse"
   description="I cmdlet principali di PowerShell per Azure SQL Data Warehouse, compresi quelli per sospendere e riavviare un database."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/18/2016"
   ms.author="sonyama;barbkess;mausher"/>

# Usare i cmdlet di PowerShell e le API REST con SQL Data Warehouse

Molte attività di amministrazione di SQL Data Warehouse possono essere gestite tramite i cmdlet di Azure PowerShell o le API REST. Di seguito sono riportati alcuni esempi su come usare i comandi di PowerShell per automatizzare le attività comuni in SQL Data Warehouse. Per altri esempi, vedere [Gestire le operazioni di scalabilità con REST][].

> [AZURE.NOTE]  Per usare Azure PowerShell con SQL Data Warehouse, è necessario installare Azure PowerShell versione 1.0.3 o successiva. È possibile controllare la versione in uso eseguendo **Get-Module -ListAvailable -Name Azure**. È possibile installare la versione più recente usando [Installazione guidata piattaforma Web Microsoft][]. Per altre informazioni sull'installazione della versione più recente, vedere [Come installare e configurare Azure PowerShell][].

## Introduzione ai cmdlet di Azure PowerShell

1. Aprire Windows PowerShell. 
2. Al prompt di PowerShell, eseguire questi comandi per accedere ad Azure Resource Manager e selezionare la sottoscrizione.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## Esempio di sospensione di SQL Data Warehouse

Sospende un database denominato "Database02" ospitato su un server denominato "Server01". Il server si trova in un gruppo di risorse di Azure denominato "ResourceGroup1".

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Come variazione, questo esempio invia tramite pipe l'oggetto recuperato a [Suspend-AzureRmSqlDatabase][]. Il database viene pertanto sospeso. Il comando finale mostra i risultati.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## Esempio di avvio di SQL Data Warehouse

Fa riprendere le operazioni di un database denominato "Database02" ospitato su un server denominato "Server01". Il server è incluso in un gruppo di risorse denominato "ResourceGroup1".

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Come variazione, questo esempio recupera un database denominato "Database02" da un server denominato "Server01" incluso in un gruppo di risorse denominato "ResourceGroup1". Invia tramite pipe l'oggetto recuperato a [Resume-AzureRmSqlDatabase][].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] Se il server è foo.database.windows.net, usare "foo" come nome server nei cmdlet di PowerShell.

## Cmdlet di PowerShell usati di frequente

Questi cmdlet di PowerShell vengono spesso usati con Azure SQL Data Warehouse.

- [Get-AzureRmSqlDatabase][]
- [Get-AzureRmSqlDeletedDatabaseBackup][]
- [Get-AzureRmSqlDatabaseRestorePoints][]
- [New-AzureRmSqlDatabase][]
- [Remove-AzureRmSqlDatabase][]
- [Restore-AzureRmSqlDatabase][] 
- [Resume-AzureRmSqlDatabase][]
- [Select-AzureRmSubscription][]
- [Set-AzureRmSqlDatabase][]
- [Suspend-AzureRmSqlDatabase][]

## Passaggi successivi
Per altri esempi di PowerShell, vedere:

- [Creare SQL Data Warehouse con PowerShell][]
- [Ripristino del database][]

Per un elenco di tutte le attività che possono essere automatizzate tramite PowerShell, vedere [Cmdlet del database di SQL di Azure][]. Per un elenco di attività che possono essere automatizzate con REST, vedere [Operazioni per i database SQL di Azure][].

<!--Image references-->

<!--Article references-->
[Come installare e configurare Azure PowerShell]: ./powershell-install-configure.md
[Creare SQL Data Warehouse con PowerShell]: ./sql-data-warehouse-get-started-provision-powershell.md
[Ripristino del database]: ./sql-data-warehouse-manage-database-restore-powershell.md
[Gestire le operazioni di scalabilità con REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Cmdlet del database di SQL di Azure]: https://msdn.microsoft.com/library/mt574084.aspx
[Operazioni per i database SQL di Azure]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Installazione guidata piattaforma Web Microsoft]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0608_2016-->