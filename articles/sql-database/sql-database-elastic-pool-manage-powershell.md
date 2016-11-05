---
title: Monitorare e gestire un pool di database elastici (PowerShell) | Microsoft Docs
description: Informazioni su come usare PowerShell per gestire un pool di database elastici.
services: sql-database
documentationcenter: ''
author: srinia
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 06/22/2016
ms.author: srinia

---
# Monitorare e gestire un pool di database elastici con PowerShell
> [!div class="op_single_selector"]
> * [Portale di Azure](sql-database-elastic-pool-manage-portal.md)
> * [PowerShell](sql-database-elastic-pool-manage-powershell.md)
> * [C#](sql-database-elastic-pool-manage-csharp.md)
> * [T-SQL](sql-database-elastic-pool-manage-tsql.md)
> 
> 

Gestire un [pool di database elastici](sql-database-elastic-pool.md) tramite i cmdlet di PowerShell.

Per i codici di errore comuni, vedere [Codici di errore SQL per le applicazioni client del database SQL: errore di connessione e altri problemi del database](sql-database-develop-error-messages.md).

I valori per i pool sono disponibili in [Limiti di archiviazione e di eDTU dei pool elastici e dei database elastici](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

## Prerequisiti
* Azure PowerShell 1.0 o versioni successive. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).
* I pool di database elastici sono disponibili unicamente nei server database SQL V12. Se si usa un server di database SQL V11, [usare PowerShell per eseguire l'aggiornamento a V12 e creare un pool](sql-database-upgrade-server-portal.md) in un unico passaggio.

## Spostare un database in un pool elastico
È possibile spostare un database all'interno o all'esterno di un pool con [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx).

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Modificare le impostazioni delle prestazioni di un pool
Quando le prestazioni ne risentono, è possibile modificare le impostazioni del pool per supportare la crescita. Usare il cmdlet [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx). Impostare il parametro -Dtu sul numero di eDTU per pool. Per i valori possibili, vedere [Limiti di archiviazione e di eDTU dei pool elastici e dei database elastici](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## Ottenere lo stato delle operazioni dei pool
La creazione di un pool può richiedere tempo. Per tenere traccia dello stato delle operazioni dei pool, creazione e aggiornamenti inclusi, usare il cmdlet [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx).

    Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## Ottenere lo stato dello spostamento di un database elastico all’interno o all’esterno di un pool
Lo spostamento di un database può richiedere tempo. Tenere traccia di un stato di spostamento mediante il cmdlet [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx).

    Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Ottenere dati di utilizzo delle risorse per un pool
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
| eDTU\_limit |Impostazione del numero massimo DTU del pool elastico corrente per questo pool elastico durante l'intervallo. |
| storage\_limit |Impostazione del limite massimo di archiviazione del pool elastico corrente per questo pool elastico espresso in megabyte durante l'intervallo. |
| eDTU\_used |Numero medio di eDTU usato dal pool nell'intervallo. |
| storage\_used |Spazio medio di archiviazione usato dal pool nell'intervallo espresso in byte |

**Granularità della metrica/periodi di conservazione:**

* I dati verranno restituiti con una granularità di 5 minuti.
* La conservazione dei dati è di 35 giorni.

Questo cmdlet e questa API limitano il numero di righe che è possibile recuperare in una chiamata a 1000 righe (circa 3 giorni di dati con una granularità di 5 minuti). Tuttavia, questo comando può essere chiamato più volte con intervalli di tempo iniziale e finale differenti per recuperare ulteriori dati

Per recuperare le metriche:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  


## Ottenere i dati di utilizzo delle risorse per un database elastico
Queste API sono le stesse API (V12) correnti usate per monitorare l'utilizzo delle risorse di un database autonomo, fatta eccezione per la seguente differenza semantica.

Per questa API, le metriche recuperate vengono espresse come percentuale del numero massimo di eDTU (o limite di utilizzo equivalente per la metrica sottostante come CPU, IO e così via) impostato per tale pool. Ad esempio, l'utilizzo del 50% di una di queste metriche indica che il consumo di risorse specifico si trova al 50% del limite di utilizzo per database per quella risorsa nel pool padre.

Per recuperare le metriche:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

## Aggiungere un avviso a una risorsa di pool
È possibile aggiungere regole di avviso alle risorse per inviare notifiche tramite posta elettronica oppure stringhe di avviso a [endpoint di URL](https://msdn.microsoft.com/library/mt718036.aspx) quando la risorsa raggiunge la soglia di utilizzo impostata. Usare il cmdlet Add-AzureRmMetricAlertRule.

> [!IMPORTANT]
> Il monitoraggio dell'utilizzo delle risorse per i pool elastici viene eseguito con un ritardo di almeno 20 minuti. Al momento non è possibile impostare avvisi inferiori ai 30 minuti per i pool elastici. Tutti gli avvisi per i pool elastici impostati con una durata (parametro denominato "-WindowSize" nell'API di PowerShell) inferiore a 30 minuti non possono essere attivati. Controllare che gli avvisi definiti per i pool elastici utilizzino un periodo (WindowSize) di almeno 30 minuti.
> 
> 

In questo esempio viene aggiunto un avviso per ricevere una notifica quando il consumo di eDTU del pool supera una soglia stabilita.

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

## Aggiungere avvisi a tutti i database di un pool
È possibile aggiungere regole di avviso a tutti i database di un pool elastico in modo che vengano inviate notifiche tramite posta elettronica o stringhe di avviso a [endpoint di URL](https://msdn.microsoft.com/library/mt718036.aspx) quando una risorsa raggiunge la soglia di utilizzo impostata.

> [!IMPORTANT]
> Il monitoraggio dell'utilizzo delle risorse per i pool elastici viene eseguito con un ritardo di almeno 20 minuti. Al momento non è possibile impostare avvisi inferiori ai 30 minuti per i pool elastici. Tutti gli avvisi per i pool elastici impostati con una durata (parametro denominato "-WindowSize" nell'API di PowerShell) inferiore a 30 minuti non possono essere attivati. Controllare che gli avvisi definiti per i pool elastici utilizzino un periodo (WindowSize) di almeno 30 minuti.
> 
> 

In questo esempio viene aggiunto un avviso a ciascuno dei database di un pool in modo che venga inviata una notifica quando il consumo di DTU del database supera una determinata soglia.

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

    # Get resource usage metrics for a database in an elastic database for the specified time interval.
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



## Raccogliere e monitorare i dati sull'utilizzo delle risorse in più pool in una sottoscrizione
In presenza di un numero elevato di database in una sottoscrizione, è difficile monitorare separatamente i singoli pool elastici. I cmdlet di PowerShell per database SQL e le query T-SQL, invece, possono essere combinati per raccogliere dati sull'utilizzo delle risorse da più pool e i relativi database per il monitoraggio e l'analisi dell'utilizzo delle risorse. Nel repository di esempi relativi a SQL Server su GitHub è disponibile un'[implementazione di esempio](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) di un set di script di PowerShell di questo tipo, insieme alla documentazione relativa alle operazioni eseguite da tale implementazione e al relativo uso.

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

## Esempio: recuperare le metriche di consumo di risorse per un pool e i relativi database
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

    # Construct the pool resource ID and retrive pool metrics at 5 minute granularity.
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime) 

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Get resource usage metrics for a database in an elastic database for the specified time interval.
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



## Latenza delle operazioni dei pool elastici
* La modifica del numero minimo di eDTU per database o del numero massimo di eDTU per database in genere viene completata entro 5 minuti.
* La modifica del numero di eDTU per pool dipende dallo spazio totale usato da tutti i database nel pool. Le modifiche richiedono una media di 90 minuti o meno per 100 GB. Ad esempio, se lo spazio totale utilizzato da tutti i database nel pool è pari a 200 GB, la latenza prevista per la modifica del numero di eDTU del pool per ogni pool è di 3 ore o meno.

## Eseguire la migrazione dei server da V11 a V12
I cmdlet di PowerShell consentono di avviare, arrestare o monitorare un aggiornamento alla versione 12 del database SQL di Azure dalla versione 11 o qualsiasi altra versione precedente alla 12.

* [Eseguire l'aggiornamento a database SQL V12 tramite PowerShell](sql-database-upgrade-server-powershell.md)

Per la documentazione di riferimento sui cmdlet di Powershell, vedere:

* [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
* [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
* [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

Il cmdlet Stop comporta l'annullamento non la sospensione. Non è possibile riprendere un aggiornamento, è possibile solamente riavviarlo dall'inizio. Il cmdlet Stop libera e rilascia tutte le risorse appropriate.

## Passaggi successivi
* [Creare processi elastici](sql-database-elastic-jobs-overview.md): i processi elastici consentono di eseguire script T-SQL su un numero qualsiasi di database nel pool.
* Vedere l'articolo sull'[aumento del numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md): usare gli strumenti di database elastici per aumentare il numero di istanze, spostare dati, eseguire query o creare transazioni.

<!---HONumber=AcomDC_0706_2016-->