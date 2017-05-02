---
title: 'PowerShell: creare e gestire un pool elastico SQL di Azure | Microsoft Docs'
description: Informazioni su come usare PowerShell per gestire un pool elastico.
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 61289770-69b9-4ae3-9252-d0e94d709331
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 06/22/2016
ms.author: srinia
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 7ab1d760d26aac7fc185b0e9f5e4a7a47cc2eee5
ms.lasthandoff: 04/15/2017


---

# <a name="create-and-manage-an-elastic-pool-with-powershell"></a>Creare e gestire un pool elastico con PowerShell
Questo argomento illustra come creare e gestire [pool elastici](sql-database-elastic-pool.md) scalabili con PowerShell.  È anche possibile creare e gestire un pool elastico di Azure con il [portale di Azure](https://portal.azure.com/), l'API REST o [C#](sql-database-elastic-pool-manage-csharp.md). È anche possibile creare e spostare database verso e dai pool elastici usando [Transact-SQL](sql-database-elastic-pool-manage-tsql.md).

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-an-elastic-pool"></a>Creare un pool elastico
Il cmdlet [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378\(v=azure.300\).aspx) crea un pool elastico. I valori per eDTU per pool, DTU min e max sono vincolati dal valore del livello di servizio (Basic, Standard, Premium o Premium RS). Vedere [Limiti di archiviazione e di eDTU per pool elastici e database in pool](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

## <a name="create-a-pooled-database-in-an-elastic-pool"></a>Creare un nuovo database in pool in un pool elastico
Usare il cmdlet [New- AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) e impostare il parametro **ElasticPoolName** per il pool di destinazione. Per spostare un database esistente in un pool elastico, vedere [Spostare un database in un pool elastico](sql-database-elastic-pool-manage-powershell.md#move-a-database-into-an-elastic-pool).

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### <a name="complete-script"></a>Script completo
Questo script crea un gruppo di risorse di Azure e un server. Quando richiesto, specificare un nome utente dell'amministratore e una password per il nuovo server (non le credenziali di Azure).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB

## <a name="create-an-elastic-pool-and-add-multiple-pooled-databases"></a>Creare un pool elastico e aggiungere più database in pool
La creazione di molti database in un pool elastico può richiedere tempo quando viene eseguita tramite il portale o i cmdlet di PowerShell che creano un database singolo alla volta. Per automatizzare la creazione in un pool elastico, vedere [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).   

## <a name="move-a-database-into-an-elastic-pool"></a>Spostare un database in un pool elastico
È possibile spostare un database all'interno o all'esterno di un pool elastico con [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx).

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="change-performance-settings-of-an-elastic-pool"></a>Modificare le impostazioni delle prestazioni di un pool elastico
Quando le prestazioni ne risentono, è possibile modificare le impostazioni del pool per supportare la crescita. Usare il cmdlet [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511\(v=azure.300\).aspx). Impostare il parametro -Dtu sul numero di eDTU per pool. Per i valori possibili, vedere [Limiti di archiviazione e di eDTU dei pool elastici e dei database elastici](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).  

    Set-AzureRmSqlElasticPool -ResourceGroupName “resourcegroup1” -ServerName “server1” -ElasticPoolName “elasticpool1” -Dtu 1200 -DatabaseDtuMax 100 -DatabaseDtuMin 50

## <a name="get-the-status-of-pool-operations"></a>Ottenere lo stato delle operazioni dei pool
La creazione di un pool elastico può richiedere tempo. Per tenere traccia dello stato delle operazioni dei pool, inclusi la creazione e gli aggiornamenti, usare il cmdlet [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812\(v=azure.300\).aspx).

    Get-AzureRmSqlElasticPoolActivity -ResourceGroupName “resourcegroup1” -ServerName “server1” -ElasticPoolName “elasticpool1”

## <a name="get-the-status-of-moving-a-database-into-and-out-of-an-elastic-pool"></a>Ottenere lo stato dello spostamento di un database all'interno o all'esterno di un pool elastico
Lo spostamento di un database può richiedere tempo. Tenere traccia di un stato di spostamento mediante il cmdlet [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687\(v=azure.300\).aspx).

    Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="get-resource-usage-data-for-an-elastic-pool"></a>Ottenere i dati di utilizzo delle risorse per un pool elastico
Metriche che è possibile recuperare come percentuale del limite del pool di risorse:   

| Nome metrica | Descrizione |
|:--- |:--- |
| cpu\_percent |Utilizzo medio del calcolo espresso in percentuale del limite del pool. |
| physical\_data\_read\_percent |Utilizzo I/O medio espresso in percentuale sulla base del limite del pool. |
| log\_write\_percent |Utilizzo delle risorse di scrittura medio espresso in percentuale del limite del pool. |
| DTU\_consumption\_percent |Utilizzo di eDTU medio espresso in percentuale del limite di eDTU per pool |
| storage\_percent |Utilizzo di spazio di archiviazione medio espresso in percentuale del limite di archiviazione del pool. |
| workers\_percent |Numero massimo di ruoli di lavoro simultanei (richieste) espresso in percentuale sulla base del limite del pool. |
| sessions\_percent |Numero massimo di sessioni simultanee espresso in percentuale sulla base del limite del pool. |
| eDTU_limit |Impostazione del numero massimo DTU del pool elastico corrente per questo pool elastico durante l'intervallo. |
| storage\_limit |Impostazione del limite massimo di archiviazione del pool elastico corrente per questo pool elastico espresso in megabyte durante l'intervallo. |
| eDTU\_used |Numero medio di eDTU usato dal pool nell'intervallo. |
| storage\_used |Spazio medio di archiviazione usato dal pool nell'intervallo espresso in byte |

**Granularità della metrica/periodi di conservazione:**

* I dati vengono restituiti con una granularità di 5 minuti.  
* La conservazione dei dati è di 35 giorni.  

Questo cmdlet e questa API limitano il numero di righe che è possibile recuperare in una chiamata a 1000 righe, ovvero circa 3 giorni di dati con una granularità di 5 minuti. Tuttavia, questo comando può essere chiamato più volte con intervalli di tempo iniziale e finale differenti per recuperare ulteriori dati

Per recuperare le metriche:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  

## <a name="get-resource-usage-data-for-a-database-in-an-elastic-pool"></a>Ottenere i dati di utilizzo delle risorse per un database in un pool elastico
Queste API sono le stesse API usate per monitorare l'utilizzo delle risorse di un database singolo, tranne per la differenza semantica seguente: le metriche recuperate sono espresse in percentuale del numero massimo di eDTU per database (o di un limite equivalente per la metrica sottostante, ad esempio CPU o I/O) impostato per il pool. Ad esempio, l'utilizzo del 50% di una di queste metriche indica che il consumo di risorse specifico si trova al 50% del limite di utilizzo per database per quella risorsa nel pool padre.

Per recuperare le metriche:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

## <a name="add-an-alert-to-an-elastic-pool-resource"></a>Aggiungere un avviso a una risorsa di pool elastico
È possibile aggiungere regole di avviso a un pool elastico per l'invio di notifiche tramite posta elettronica oppure stringhe di avviso a [endpoint di URL](https://msdn.microsoft.com/library/mt718036.aspx) quando il pool elastico raggiunge la soglia di utilizzo impostata. Usare il cmdlet Add-AzureRmMetricAlertRule.

> [!IMPORTANT]
> Il monitoraggio dell'uso delle risorse per i pool elastici viene eseguito con un ritardo di almeno 20 minuti. Al momento non è possibile impostare avvisi inferiori ai 30 minuti per i pool elastici. Tutti gli avvisi impostati per i pool elastici impostati con un periodo (parametro denominato "-WindowSize" nell'API di PowerShell) inferiore a 30 minuti non possono essere attivati. Verificare che gli avvisi definiti per i pool elastici usino un periodo (WindowSize) di almeno 30 minuti.
>
>

In questo esempio viene aggiunto un avviso per ricevere una notifica quando il consumo di eDTU del pool elastico supera una soglia stabilita.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location =  '<location'                         # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name

    #$Target Resource ID
    $ResourceID = '/subscriptions/' + $subscriptionId + '/resourceGroups/' +$resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticpools/' + $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # create a unique rule name
    $alertName = $poolName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $ResourceID -MetricName "DTU_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

## <a name="add-alerts-to-all-databases-in-an-elastic-pool"></a>Aggiungere avvisi a tutti i database in un pool elastico
È possibile aggiungere regole di avviso a tutti i database di un pool elastico in modo che vengano inviate notifiche tramite posta elettronica o stringhe di avviso a [endpoint di URL](https://msdn.microsoft.com/library/mt718036.aspx) quando una risorsa raggiunge la soglia di utilizzo impostata.

> [!IMPORTANT]
> Il monitoraggio dell'uso delle risorse per i pool elastici viene eseguito con un ritardo di almeno 20 minuti. Al momento non è possibile impostare avvisi inferiori ai 30 minuti per i pool elastici. Tutti gli avvisi impostati per i pool elastici impostati con un periodo (parametro denominato "-WindowSize" nell'API di PowerShell) inferiore a 30 minuti non possono essere attivati. Verificare che gli avvisi definiti per i pool elastici usino un periodo (WindowSize) di almeno 30 minuti.
>
>

Questo esempio aggiunge un avviso a ognuno dei database di un pool elastico in modo che venga inviata una notifica quando il consumo di DTU del database supera una determinata soglia.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location = '<location'                          # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # Get resource usage metrics for a database in an elastic pool for the specified time interval.
    foreach ($db in $dbList)
    {
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName

    # create a unique rule name
    $alertName = $db.DatabaseName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName  -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $dbResourceId -MetricName "dtu_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

    # drop the alert rule
    #Remove-AzureRmAlertRule -ResourceGroup $resourceGroupName -Name $alertName
    }

## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools-in-a-subscription"></a>Raccogliere e monitorare i dati sull'utilizzo delle risorse in più pool in una sottoscrizione
In presenza di molti database in una sottoscrizione, è difficile monitorare separatamente i singoli pool elastici. I cmdlet di PowerShell per database SQL e le query T-SQL, invece, possono essere combinati per raccogliere dati sull'utilizzo delle risorse da più pool e i relativi database per il monitoraggio e l'analisi dell'utilizzo delle risorse. Nel repository di esempi relativi a SQL Server su GitHub è disponibile un' [implementazione di esempio](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) di un set di script di PowerShell di questo tipo, insieme alla documentazione relativa alle operazioni eseguite da tale implementazione e al relativo uso.

Per usare questa implementazione di esempio, seguire questa procedura.

1. Scaricare gli [script e la documentazione](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools):
2. Modificare gli script per l'ambiente. Specificare uno o più server in cui sono ospitati i pool elastici.
3. Specificare un database di telemetria in cui archiviare le metriche raccolte.
4. Personalizzare lo script per specificare la durata dell'esecuzione degli script.

A livello generale, gli script eseguono le operazioni seguenti:

* Enumera tutti i server in una determinata sottoscrizione di Azure (o in determinato elenco di server).
* Esegue un processo in background per ogni server. Il processo viene eseguito in un ciclo a intervalli regolari e raccoglie i dati di telemetria per tutti i pool nel server. Carica quindi i dati raccolti nel database di telemetria specificato.
* Enumera un elenco di database in ogni pool per raccogliere i dati sull'utilizzo delle risorse di database. Carica quindi i dati raccolti nel database di telemetria.

È possibile analizzare le metriche raccolte nel database di telemetria per monitorare l'integrità dei pool elastici e i database che contengono. Lo script installa anche una funzione con valori di tabella predefinita nel database di telemetria per aggregare le metriche per un determinato intervallo di tempo. Ad esempio, i risultati della funzione con valori di tabella possono essere usati per visualizzare i "primi N pool elastici con l'utilizzo eDTU massimo in un dato intervallo di tempo". Facoltativamente, è possibile usare strumenti di analisi come Excel o Power BI per eseguire una query e analizzare i dati raccolti.

### <a name="example-retrieve-resource-consumption-metrics-for-an-elastic-pool-and-its-databases"></a>Esempio: recuperare le metriche di consumo di risorse per un pool elastico e i relativi database
Questo esempio recupera le metriche di consumo per un determinato pool elastico e tutti i relativi database. I dati raccolti vengono formattati e scritti in un file in formato CSV. Il file può essere esplorato in Excel.

    $subscriptionId = '<Azure subscription id>'          # Azure subscription ID
    $resourceGroupName = '<resource group name>'             # Resource Group
    $serverName = <server name>                              # server name
    $poolName = <elastic pool name>                          # pool name

    # Login to Azure account and select the subscription.
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Get resource usage metrics for an elastic pool for the specified time interval.
    $startTime = '4/27/2016 00:00:00'  # start time in UTC
    $endTime = '4/27/2016 01:00:00'    # end time in UTC

    # Construct the pool resource ID and retrive pool metrics at 5-minute granularity.
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Get resource usage metrics for a database in an elastic pool for the specified time interval.
    $dbMetrics = @()
    foreach ($db in $dbList)
    {
        $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
        $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
    }

    #Optionally you can format the metrics and output as .csv file using the following script block.
    $command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) {
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name;
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId
          }
          $table = $table += $sx
      }
    }

    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
    }

    # Format and output pool metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv

    # Format and output database metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv

## <a name="latency-of-elastic-pool-operations"></a>Latenza delle operazioni dei pool elastici
* La modifica del numero minimo di eDTU per database o del numero massimo di eDTU per database in genere viene completata entro 5 minuti.
* La modifica del numero di eDTU per pool dipende dallo spazio totale usato da tutti i database nel pool. Le modifiche richiedono una media di 90 minuti o meno per 100 GB. Ad esempio, se lo spazio totale utilizzato da tutti i database nel pool è pari a 200 GB, la latenza prevista per la modifica del numero di eDTU del pool per ogni pool è di 3 ore o meno.

Per la documentazione di riferimento sui cmdlet di Powershell, vedere:

* [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582\(v=azure.300\).aspx)
* [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403\(v=azure.300\).aspx)
* [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589\(v=azure.300\).aspx)

Il cmdlet Stop comporta l'annullamento non la sospensione. Non è possibile riprendere un aggiornamento, è possibile solamente riavviarlo dall'inizio. Il cmdlet Stop libera e rilascia tutte le risorse appropriate.

## <a name="next-steps"></a>Passaggi successivi
* [Creare processi elastici](sql-database-elastic-jobs-overview.md) : i processi elastici consentono di eseguire script T-SQL su un numero qualsiasi di database nel pool.
* Vedere [Aumentare il numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md): usare gli strumenti elastici per aumentare il numero di istanze, spostare dati, eseguire query o creare transazioni.

