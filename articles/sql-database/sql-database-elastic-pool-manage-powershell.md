<properties 
    pageTitle="Monitorare e gestire un pool di database elastici (PowerShell) | Microsoft Azure" 
    description="Informazioni su come usare PowerShell per gestire un pool di database elastici."  
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="03/15/2016"
    ms.author="sstein"/>

# Monitorare, gestire e dimensionare un pool di database elastici con PowerShell 

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)


Informazioni su come gestire un [pool di database elastici](sql-database-elastic-pool.md) tramite i cmdlets PowerShell.

Per i codici di errore comuni, vedere [Codici di errore SQL per le applicazioni client del database SQL: errore di connessione e altri problemi del database](sql-database-develop-error-messages.md).

> [AZURE.NOTE] I pool di database elastici sono attualmente in anteprima e sono disponibili unicamente con i server di Database SQL V12. Se si usa un server di database SQL V11 è possibile [usare PowerShell per eseguire l'aggiornamento a V12 e creare un pool](sql-database-upgrade-server-portal.md) in un unico passaggio.

È necessario eseguire Azure PowerShell 1.0 o versione successiva. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).




## Creare un nuovo database elastico in un pool

Per creare un nuovo database direttamente all'interno di un pool, usare il cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) e impostare il parametro **ElasticPoolName**.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## Spostare un database autonomo in un pool

Per spostare un database esistente in un pool, usare il cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) e impostare il parametro **ElasticPoolName**.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## Modificare le impostazioni delle prestazioni di un pool

Per modificare le impostazioni delle prestazioni di un pool, usare il cmdlet [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx).

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## Ottenere lo stato delle operazioni dei pool

È possibile tenere traccia dello stato delle operazioni dei pool, inclusa la creazione e gli aggiornamenti, mediante il cmdlet [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx).

	Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## Ottenere lo stato dello spostamento di un database elastico all’interno o all’esterno di un pool

È possibile tenere traccia dello stato delle operazioni dei database elastici, inclusa la creazione e gli aggiornamenti, mediante il cmdlet [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx).

	Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Ottenere dati di utilizzo per un pool

Metriche che è possibile recuperare come percentuale del limite del pool di risorse:

* Utilizzo medio della CPU: cpu\_percent 
* Utilizzo medio di IO: data\_io\_percent 
* Utilizzo medio del log: log\_write\_percent 
* Utilizzo medio della memoria: memory\_percent 
* Utilizzo medio eDTU (come valore massimo di utilizzo di CPU/IO/Log) - DTU\_percent 
* Numero massimo di richieste utente simultanee (thread di lavoro): max\_concurrent\_requests 
* Numero massimo di sessioni utente simultanee: max\_concurrent\_sessions 
* Spazio di archiviazione totale per il pool elastico: storage\_in\_megabytes 


Granularità della metrica/periodi di conservazione:

* I dati verranno restituiti con una granularità di 5 minuti.  
* La conservazione dei dati è di 14 giorni.  


Questo cmdlet e questa API limitano il numero di righe che è possibile recuperare in una chiamata a 1000 righe (circa 3 giorni di dati con una granularità di 5 minuti). Tuttavia, questo comando può essere chiamato più volte con intervalli di tempo iniziale e finale differenti per recuperare ulteriori dati


Recuperare le metriche:

	$metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Ottenere altri giorni, ripetendo la chiamata e accodando i dati:

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
Formattare la tabella:

    $table = Format-MetricsAsTable $metrics 

Esportare in un file CSV:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

## Ottenere le metriche di consumo delle risorse per un database elastico

Queste API sono le stesse API (V12) correnti utilizzate per monitorare l'utilizzo delle risorse di un database autonomo, fatta eccezione per la seguente differenza semantica

* Per questa API, le metriche recuperate vengono espresse come percentuale del valore databaseDtuMax (o limite di utilizzo equivalente per la metrica sottostante, come CPU, IO e così via) impostato per tale pool. Ad esempio, l’utilizzo del 50% di una di queste metriche indica che il consumo di risorse specifico si trova al 50% del limite di utilizzo per DB per quella risorsa nel pool padre. 

Ottenere le metriche:

    $metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Ottenere altri giorni, se necessario, ripetendo la chiamata e accodando i dati:

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Formattare la tabella:

    $table = Format-MetricsAsTable $metrics 

Esportare in un file CSV:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## Esempio di come monitorare e gestire un pool di PowerShell


    $subscriptionId = '<Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'
    
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    
    Set-AzureRmSqlElasticPool –ResourceGroupName $resourceGroupName –ServerName $serverName –ElasticPoolName $poolName –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $databaseName 
    $startTime1 = '2/10/2016'
    $endTime1 = '2/14/2016'
    $startTime2 = '2/14/2016'
    $endTime2 = '2/18/2016'
    
    
    
    $metrics = (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics
    
    $metrics = $metrics + (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}
    
    $metrics = (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics = $metrics + (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## Passaggi successivi

- [Creare processi elastici](sql-database-elastic-jobs-overview.md): i processi elastici consentono di eseguire script T-SQL su un numero qualsiasi di database nel pool.


## Riferimento ai database elastici

Per ulteriori informazioni sui database elastici e sui pool di database elastici, inclusi i dettagli sulle API e sugli errori, vedere [Riferimento ai pool di database elastici](sql-database-elastic-pool-reference.md).

<!---HONumber=AcomDC_0330_2016-->