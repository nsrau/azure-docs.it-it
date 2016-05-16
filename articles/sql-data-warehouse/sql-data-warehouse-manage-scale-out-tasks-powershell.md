<properties
   pageTitle="Gestire le operazioni di scalabilità per Azure SQL Data Warehouse (PowerShell) | Microsoft Azure"
   description="Attività di PowerShell per scalare orizzontalmente le prestazioni per Azure SQL Data Warehouse. Modificare le risorse di calcolo cambiando il numero di DWU. In alternativa, sospendere e riprendere le risorse di calcolo per ridurre i costi."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Gestire le operazioni di scalabilità per Azure SQL Data Warehouse (PowerShell)

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-overview-scalability.md)
- [Portale](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)

La scalatura orizzontale elastica delle risorse di calcolo e di memoria consente di soddisfare le variazioni di domanda del carico di lavoro e di contenere i costi, riducendo le risorse durante i periodi non di picco.

Questa raccolta di attività usa i cmdlet PowerShell per:

- Scalare le prestazioni modificando le DWU
- Sospendere l'attività delle risorse di calcolo
- Ripristinare l'attività delle risorse di calcolo

Per altre informazioni, vedere [Panoramica sulla scalabilità delle prestazioni][].

<a name="before-you-begin-bk"></a>

## Prima di iniziare

### Installare la versione più recente di Azure PowerShell.

> [AZURE.NOTE]  Per usare Azure PowerShell con SQL Data Warehouse, è necessario installare Azure PowerShell 1.0.3 o versione successiva. Per verificare la versione corrente, eseguire il comando **Get-Module -ListAvailable -Name Azure**. È possibile installare la versione più recente usando [Microsoft Web Platform Installer][]. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell][].

### Introduzione ai cmdlet di Azure PowerShell

Attività iniziali

1. Aprire Azure PowerShell. 
2. Al prompt di PowerShell, eseguire questi comandi per accedere ad Azure Resource Manager e selezionare la sottoscrizione.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>

## Scalare le prestazioni

[AZURE.INCLUDE [Descrizione della scalatura di DWU di SQL Data Warehouse](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Per modificare il numero di DWU usare il cmdlet PowerShell [Set-AzureRmSqlDatabase][]. L'esempio seguente imposta l'obiettivo del livello di servizio su DW1000 per il database MySQLDW ospitato nel server MyServer.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## Sospendere il calcolo

[AZURE.INCLUDE [Descrizione della sospensione di SQL Data Warehouse](../../includes/sql-data-warehouse-pause-description.md)]

Per sospendere l'esecuzione di un database, usare il cmdlet [Suspend-AzureRmSqlDatabase][]. L'esempio seguente sospende il database Database02 ospitato sul server Server01. Il server appartiene al gruppo di risorse di Azure ResourceGroup1.

> [AZURE.NOTE] Se il server è foo.database.windows.net, usare "foo" come nome server nei cmdlet di PowerShell.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
Come variazione, il database dell'esempio seguente viene recuperato nell'oggetto $database. Quindi l'oggetto viene inviato tramite pipe a [Suspend-AzureRmSqlDatabase][]. I risultati vengono archiviati nell'oggetto resultDatabase. Il comando finale mostra i risultati.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## Riprendere il calcolo

[AZURE.INCLUDE [Descrizione della riattivazione di SQL Data Warehouse](../../includes/sql-data-warehouse-resume-description.md)]

Per avviare un database, usare il cmdlet [Resume-AzureRmSqlDatabase][]. L'esempio seguente avvia il database Database02 ospitato sul server Server01. Il server appartiene al gruppo di risorse di Azure ResourceGroup1.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

Come variazione, il database dell'esempio seguente viene recuperato nell'oggetto $database. Quindi l'oggetto viene inviato tramite pipe a [Resume-AzureRmSqlDatabase][] e i risultati vengono archiviati in $resultDatabase. Il comando finale mostra i risultati.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## Passaggi successivi

Per altre attività di gestione, vedere [Panoramica della gestione][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Panoramica della gestione]: ./sql-data-warehouse-overview-manage.md
[Panoramica sulla scalabilità delle prestazioni]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->