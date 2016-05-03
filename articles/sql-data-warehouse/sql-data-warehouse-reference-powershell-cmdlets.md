<properties
   pageTitle="Cmdlet di PowerShell per Azure SQL Data Warehouse"
   description="I cmdlet principali di PowerShell per Azure SQL Data Warehouse, compresi quelli per sospendere e riavviare un database."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyama"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/02/2016"
   ms.author="sonyama;barbkess;mausher"/>

# Usare i cmdlet di PowerShell e le API REST con SQL Data Warehouse

SQL Data Warehouse può essere gestito tramite i cmdlet di Azure PowerShell o le API REST.

La maggior parte dei comandi definiti per il **database SQL di Azure** vengono usati anche per **SQL Data Warehouse**. Per un elenco aggiornato, vedere l'articolo relativo ai [cmdlet di Azure SQL](https://msdn.microsoft.com/library/mt574084.aspx). I cmdlet [Suspend-AzureRmSqlDatabase][] e [Resume-AzureRmSqlDatabase][] sono elementi aggiuntivi progettati per SQL Data Warehouse.

Allo stesso modo, le API REST per il **database di Azure SQL** possono essere usate anche per le istanze di **SQL Data Warehouse**. Per l'elenco aggiornato, vedere [Operazioni per i database SQL di Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx).

## Introduzione ai cmdlet di Azure PowerShell

1. Per scaricare il modulo di Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://aka.ms/webpi-azps). Per altre informazioni su questo programma di installazione, vedere [Come installare e configurare Azure PowerShell][].
2. Per avviare PowerShell, fare clic su **Start** e digitare **Windows PowerShell**.
3. Al prompt di PowerShell, eseguire questi comandi per accedere ad Azure Resource Manager e selezionare la sottoscrizione.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```


## Cmdlet di PowerShell usati di frequente

Questi cmdlet di PowerShell vengono spesso usati con Azure SQL Data Warehouse:


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


## Esempi per SQL Data Warehouse

Questi esempi si riferiscono a funzionalità che si applicano solo a SQL Data Warehouse.

### [Suspend-AzureRmSqlDatabase][]

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

### [Resume-AzureRmSqlDatabase][]

Fa riprendere le operazioni di un database denominato "Database02" ospitato su un server denominato "Server01". Il server è incluso in un gruppo di risorse denominato "ResourceGroup1".

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Come variazione, questo esempio recupera un database denominato "Database02" da un server denominato "Server01" incluso in un gruppo di risorse denominato "ResourceGroup1". L'oggetto recuperato viene inviato tramite pipe a [Resume-AzureRmSqlDatabase][].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] Se il server è foo.database.windows.net, usare "foo" come nome server nei cmdlet di PowerShell.


## Passaggi successivi
Per altre informazioni di riferimento, vedere la [panoramica degli argomenti di riferimento di SQL Data Warehouse][]. Per altri esempi di PowerShell, vedere:
- [Creare SQL Data Warehouse con PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
- [Ripristinare da snapshot](sql-data-warehouse-backup-and-restore-from-snapshot.md)
- [Eseguire il ripristino geografico da snapshot](sql-data-warehouse-backup-and-restore-from-geo-restore-snapshot.md)

<!--Image references-->

<!--Article references-->
[panoramica degli argomenti di riferimento di SQL Data Warehouse]: sql-data-warehouse-overview-reference.md
[Come installare e configurare Azure PowerShell]: ../articles/powershell-install-configure.md

<!--MSDN references-->
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureRmSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619337.aspx



<!--Other Web references-->

<!---HONumber=AcomDC_0420_2016-->