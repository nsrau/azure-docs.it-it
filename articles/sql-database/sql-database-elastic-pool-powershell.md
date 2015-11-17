<properties 
    pageTitle="Creare un pool di database elastici di database SQL di Azure con PowerShell | Microsoft Azure" 
    description="Creare un pool di database elastici per condividere risorse tra più database SQL di Azure." 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="11/06/2015"
    ms.author="adamkr; sstein"/>

# Creare un pool di database elastici con PowerShell

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


Questo articolo illustra come creare un [pool di database elastici](sql-database-elastic-pool.md) con i cmdlet di PowerShell.

> [AZURE.NOTE]I pool di database elastici sono attualmente in anteprima e sono disponibili unicamente con i server di Database SQL V12. Se si usa un server di database SQL V11 è possibile [usare PowerShell per eseguire l'aggiornamento a V12 e creare un pool](sql-database-upgrade-server.md) in un unico passaggio.

Questo articolo descrive come creare tutto ciò che occorre, incluso il server V12, per creare e configurare un pool di database elastici, fatta eccezione per la sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.




I singoli passaggi per creare un pool di database elastici con Azure PowerShell sono suddivisi e illustrati per maggiore chiarezza. Per gli utenti che desiderano semplicemente un breve elenco di comandi, vedere la sezione **Riassumendo** nella parte inferiore di questo articolo.

> [AZURE.IMPORTANT]Iniziando con la versione di anteprima di Azure PowerShell 1.0, il cmdlet Switch-AzureMode non è più disponibile e i cmdlet che sono stati nel modulo Azure ResourceManager sono stati rinominati. Gli esempi in questo articolo utilizzano nuove convenzioni di denominazione dell’Anteprima di PowerShell 1.0. Per informazioni dettagliate, vedere [Deprecazione di Switch-AzureMode in Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).

Per eseguire i cmdlet di PowerShell, è necessario che Azure PowerShell sia installato e in esecuzione. A causa della rimozione di Switch-AzureMode, occorre scaricare e installare la versione più recente di Azure PowerShell eseguendo l'[Installazione guidata piattaforma Web](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).




## Configurare le credenziali e selezionare la sottoscrizione

Ora che si esegue il modulo di Gestione risorse di Azure, è possibile accedere a tutti i cmdlet necessari per creare e configurare un pool di database elastici. È necessario innanzitutto stabilire l'accesso al proprio account Azure. Eseguire le operazioni seguenti e verrà visualizzata una schermata di accesso per immettere le credenziali. Utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.

	Add-AzureRmAccount

Dopo aver effettuato l’accesso, sullo schermo dovrebbero essere visualizzate informazioni tra cui l’ID utilizzato per l’accesso con le sottoscrizioni Azure per le quali si dispone dell’accesso


### Selezionare la sottoscrizione ad Azure

Per selezionare la sottoscrizione, è necessario l'ID sottoscrizione o il nome sottoscrizione (**-SubscriptionName**). che può essere copiato dal passaggio precedente o, se si dispone di più sottoscrizioni, è possibile eseguire il cmdlet **Get-AzureSubscription** e copiare le informazioni di sottoscrizione desiderate dal set di risultati. Una volta acquisita la sottoscrizione, eseguire il cmdlet seguente:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000


## Creare un gruppo di risorse, il server e la regola firewall

Ora è possibile accedere per eseguire i cmdlet con la sottoscrizione ad Azure; pertanto il passaggio successivo consiste nello stabilire il gruppo di risorse che contiene il server in cui verrà creato il pool di database elastici. È possibile modificare il comando successivo per utilizzare qualsiasi percorso valido scelto. Eseguire **(Get-AzureLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** per ottenere un elenco di percorsi validi.

Se si dispone già di un gruppo di risorse, è possibile andare al passaggio successivo, oppure è possibile eseguire il comando seguente per creare un nuovo gruppo di risorse:

	New-AzureRmResourceGroup -Name "resourcegroup1" -Location "West US"

### Creare un server 

I pool di database elastici vengono creati all'interno dei server di database SQL di Azure. Se si ha già un server, è possibile andare al passaggio successivo o eseguire il cmdlet [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx) per creare un nuovo server V12. Sostituire ServerName con il nome del server. Deve essere univoco per i server SQL di Azure, altrimenti se il nome del server è già in uso verrà visualizzato un messaggio di errore. Vale inoltre la pena notare che il completamento di questo comando potrebbe richiedere alcuni minuti. Una volta creato il server, verranno visualizzati il prompt PowerShell e i dettagli del server. È possibile modificare il comando per utilizzare qualsiasi percorso valido scelto.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Quando si esegue questo comando, viene visualizzata una finestra in cui vengono richiesti **Nome utente** e **Password**. Non si tratta delle credenziali di Azure. Immettere il nome utente e password che saranno le credenziali di amministratore che si desidera creare per il nuovo server.


### Configurare una regola firewall del server per consentire l'accesso al server

Definire una regola firewall per accedere al server. Eseguire il comando [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603586.aspx) sostituendo gli indirizzi IP di inizio e fine con i valori validi per il computer locale.

Se il server deve consentire l'accesso ad altri servizi Azure, aggiungere lo switch **- AllowAllAzureIPs**, che aggiungerà una regola firewall speciale e consentirà l’accesso al server per tutto il traffico Azure.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Per ulteriori informazioni, vedere [Firewall di database SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Creare un pool di database elastici e i database elastici

Ora si dispone di un gruppo di risorse, un server e una regola del firewall con una configurazione che consente di accedere al server. Il cmdlet[New AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) creerà il pool di database elastici. Questo comando consente di creare un pool che condivide un totale di 400 eDTU. Ogni database nel pool è garantito in modo da disporre sempre di 10 eDTU (DatabaseDtuMin). I singoli database nel pool possono utilizzare un massimo di 100 eDTU (DatabaseDtuMax). Per spiegazioni di parametro dettagliate, vedere [Pool elastici di database SQL di Azure](sql-database-elastic-pool.md).


	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


### Creare o aggiungere database elastici in un pool di database elastici

Il pool creato nel passaggio precedente è vuoto, non contiene alcun database elastico. Nelle sezioni seguenti viene illustrato come creare nuovi database elastici all'interno del pool, nonché come aggiungere database esistenti al pool.

*Dopo la creazione di un pool è anche possibile usare Transact-SQL per creare nuovi database elastici nel pool e per spostare i database esistenti all'esterno o all'interno del pool. Per informazioni dettagliate, vedere [Riferimento al pool di database elastici - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

### Creare un nuovo database elastico all'interno di un pool di database elastici

Per creare un nuovo database direttamente all'interno di un pool, usare il cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) e impostare il parametro **ElasticPoolName**.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



### Spostare un database esistente in un pool di database elastici

Per spostare un database esistente in un pool, usare il cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) e impostare il parametro **ElasticPoolName**.


A scopo dimostrativo, creare un database che non è in un pool di database elastici.

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -Edition "Standard"

Spostare il database esistente nel pool di database elastici.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Modificare le impostazioni delle prestazioni di un pool di database elastici


    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## Monitoraggio dei database elastici e dei pool di database elastici

### Ottenere lo stato delle operazioni dei pool di database elastici

È possibile tenere traccia dello stato delle operazioni dei pool di database elastici, inclusa la creazione e gli aggiornamenti.

	Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


### Ottenere lo stato dello spostamento di un database elastico all’interno o all’esterno di un pool di database elastici

	Get-AzureRmSqlElasticPoolDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### Ottenere le metriche di consumo delle risorse per un pool di database elastici

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

	$metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Ottenere altri giorni, ripetendo la chiamata e accodando i dati:

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
Formattare la tabella:

    $table = Format-MetricsAsTable $metrics 

Esportare in un file CSV:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

### Ottenere le metriche di consumo delle risorse per un database elastico

Queste API sono le stesse API (V12) correnti utilizzate per monitorare l'utilizzo delle risorse di un database autonomo, fatta eccezione per la seguente differenza semantica

* Per questa API, le metriche recuperate vengono espresse come percentuale del valore databaseDtuMax (o limite di utilizzo equivalente per la metrica sottostante, come CPU, IO e così via) impostato per tale pool di database elastici. Ad esempio, l’utilizzo del 50% di una di queste metriche indica che il consumo di risorse specifico si trova al 50% del limite di utilizzo per DB per quella risorsa nel pool padre di database elastici. 

Ottenere la metrica: $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

Ottenere altri giorni, se necessario, ripetendo la chiamata e accodando i dati:

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Formattare la tabella:

    $table = Format-MetricsAsTable $metrics 

Esportare in un file CSV:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## Riassumendo


    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000
    New-AzureRmResourceGroup -Name "resourcegroup1" -Location "West US"
    New-AzureRmSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"
    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100
    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1" -MaxSizeBytes 10GB
    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## Passaggi successivi

Dopo aver creato un pool di database elastici, è possibile gestire i database elastici nel pool mediante la creazione di processi elastici. I processi elastici facilitano l’esecuzione di script T-SQL su qualsiasi numero di database nel pool. Per ulteriori informazioni, vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md).


## Riferimento ai database elastici

Per ulteriori informazioni sui database elastici e sui pool di database elastici, inclusi i dettagli sulle API e sugli errori, vedere [Riferimento ai pool di database elastici](sql-database-elastic-pool-reference.md).

<!---HONumber=Nov15_HO3-->