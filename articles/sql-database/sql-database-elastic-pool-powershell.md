<properties 
   pageTitle="Creare e gestire un pool di database elastici di database SQL tramite PowerShell" 
   description="Creare e gestire un pool di database elastici di database SQL di Azure tramite PowerShell" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="06/24/2015"
   ms.author="adamkr; sstein"/>

# Creare e gestire un pool elastico di database SQL con PowerShell (anteprima)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


## Panoramica

In questo articolo viene illustrato come creare e gestire un pool elastico di database SQL tramite PowerShell.


I singoli passaggi per creare un pool elastico con Azure PowerShell sono suddivisi e illustrati per maggiore chiarezza. Per gli utenti che desiderano semplicemente un breve elenco di comandi, vedere la sezione **Riassumendo** nella parte inferiore di questo articolo.

In questo articolo verrà illustrato come creare tutto ciò che occorre per creare e configurare un pool elastico, fatta eccezione per la sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.

> [AZURE.NOTE]I pool elastici sono attualmente in anteprima e sono disponibili unicamente con i server di database SQL V12.


## Prerequisiti

Per creare un pool elastico con PowerShell, è necessario disporre di Azure PowerShell installato e in esecuzione e passarlo alla modalità di gestione delle risorse per accedere ai cmdlet di PowerShell di Gestione risorse di Azure.

Per scaricare e installare i moduli Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

I cmdlet per la creazione e la gestione dei database SQL di Azure e dei pool elastici si trovano nel modulo di Gestione risorse di Azure. Quando si avvia Azure PowerShell, i cmdlet nel modulo di Azure vengono importati per impostazione predefinita. Per passare al modulo AzureResourceManager, usare il cmdlet Switch-AzureMode.

	PS C:\>Switch-AzureMode -Name AzureResourceManager

Per informazioni dettagliate, vedere [Utilizzo di Windows PowerShell con Gestione risorse](powershell-azure-resource-manager.md).


## Configurare le credenziali e selezionare la sottoscrizione

Ora che si esegue il modulo di Gestione risorse di Azure, è possibile accedere a tutti i cmdlet necessari per creare e configurare un pool elastico. È necessario innanzitutto stabilire l'accesso al proprio account Azure. Eseguire le operazioni seguenti e verrà visualizzata una schermata di accesso per immettere le credenziali. Utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.

	PS C:\>Add-AzureAccount

Dopo aver effettuato l’accesso, sullo schermo dovrebbero essere visualizzate informazioni tra cui l’ID utilizzato per l’accesso con le sottoscrizioni Azure per le quali si dispone dell’accesso


### Selezionare la sottoscrizione ad Azure

Per selezionare la sottoscrizione, è necessario l’ID sottoscrizione o il nome della sottoscrizione (**-SubscriptionName**), che può essere copiato dal passaggio precedente o, se si dispone di più sottoscrizioni, è possibile eseguire il cmdlet **Get-AzureSubscription** e copiare le informazioni di sottoscrizione desiderate dal set di risultati. Una volta acquisita la sottoscrizione, eseguire il cmdlet seguente:

	PS C:\>Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000


## Creare un gruppo di risorse, il server e la regola firewall

Ora è possibile accedere per eseguire i cmdlet con la sottoscrizione ad Azure; pertanto il passaggio successivo consiste nello stabilire il gruppo di risorse che contiene il server in cui verrà creato il pool elastico. È possibile modificare il comando successivo per utilizzare qualsiasi percorso valido scelto. Eseguire **(Get-AzureLocation | where-object {$_.Name -eq "Microsoft.Sql/servers" }).Locations** per ottenere un elenco di percorsi validi.

Se si dispone già di un gruppo di risorse, è possibile andare al passaggio successivo, oppure è possibile eseguire il comando seguente per creare un nuovo gruppo di risorse:

	PS C:\>New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"

### Creare un server 

I pool elastici vengono creati all'interno dei server di database SQL di Azure. Se si dispone già di un server, è possibile andare al passaggio successivo oppure è possibile eseguire il comando seguente per creare un nuovo server V12. Sostituire ServerName con il nome del server. Deve essere univoco per i server SQL di Azure, per cui è possibile che venga visualizzato un messaggio di errore se il nome del server è già in uso. Vale inoltre la pena notare che il completamento di questo comando potrebbe richiedere alcuni minuti. Una volta creato il server, verranno visualizzati il prompt PowerShell e i dettagli del server. È possibile modificare il comando per utilizzare qualsiasi percorso valido scelto.

	PS C:\>New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Quando si esegue questo comando, viene visualizzata una finestra in cui vengono richiesti **Nome utente** e **Password**. Non si tratta delle credenziali di Azure. Immettere il nome utente e password che saranno le credenziali di amministratore che si desidera creare per il nuovo server.


### Configurare una regola firewall del server per consentire l'accesso al server

Definire una regola firewall per accedere al server. Eseguire il comando seguente sostituendo gli indirizzi IP di inizio e fine con i valori validi per il computer.

Se il server deve consentire l'accesso ad altri servizi Azure, aggiungere lo switch **- AllowAllAzureIPs**, che aggiungerà una regola firewall speciale e consentirà l’accesso al server per tutto il traffico Azure.

	PS C:\>New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Per ulteriori informazioni, vedere [Firewall di database SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Creare un pool elastico e dei database elastici

Ora si dispone di un gruppo di risorse, un server e una regola del firewall con una configurazione che consente di accedere al server. Il comando seguente creerà il pool elastico. Questo comando consente di creare un pool che condivide un totale di 400 DTU. Ogni database nel pool è garantito in modo da disporre sempre di 10 DTU (DatabaseDtuMin). I singoli database nel pool possono utilizzare un massimo di 100 DTU (DatabaseDtuMax). Per spiegazioni di parametro dettagliate, vedere [Pool elastici di database SQL di Azure](sql-database-elastic-pool.md).


	PS C:\>New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


### Creare o aggiungere database elastici a un pool

Il pool elastico creato nel passaggio precedente è vuoto, non contiene alcun database. Nelle sezioni seguenti viene illustrato come creare nuovi database all'interno del pool elastico, nonché come aggiungere database esistenti al pool.


### Creare un nuovo database elastico all'interno di un pool elastico

Per creare un nuovo database direttamente all'interno di un pool elastico, utilizzare il cmdlet **New-AzureSqlDatabase** e impostare il parametro **ElasticPoolName**.


	PS C:\>New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



### Spostare un database esistente in un pool elastico

Per spostare un database esistente in un pool elastico, utilizzare il cmdlet **Set-AzurSqlDatabase** e impostare il parametro **ElasticPoolName**.


A scopo dimostrativo, creare un database che non è in un pool elastico.

	PS C:\>New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -Edition "Standard"

Spostare il database esistente in un pool elastico.

	PS C:\>Set-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Monitoraggio dei database elastici e dei pool elastici

### Ottenere lo stato delle operazioni dei pool elastici

È possibile tenere traccia dello stato delle operazioni dei pool elastici, inclusa la creazione e gli aggiornamenti.

	PS C:\> Get-AzureSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


### Ottenere lo stato dello spostamento di un database elastico all’interno o all’esterno di un pool elastico

	PS C:\>Get-AzureSqlElasticPoolDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### Ottenere le metriche di consumo delle risorse per un pool elastico

Metriche che è possibile recuperare come percentuale del limite del pool di risorse:

* Utilizzo medio della CPU: cpu_percent 
* Utilizzo medio di IO: data_io_percent 
* Utilizzo medio del log: log_write_percent 
* Utilizzo medio della memoria: memory_percent 
* Utilizzo medio DTU (come valore massimo di utilizzo di CPU/IO/Log) - DTU_percent 
* Numero massimo di richieste utente simultanee (thread di lavoro): max_concurrent_requests 
* Numero massimo di sessioni utente simultanee: max_concurrent_sessions 
* Spazio di archiviazione totale per il pool elastico: storage_in_megabytes 


Granularità della metrica/periodi di conservazione:

* I dati verranno restituiti con una granularità di 5 minuti.  
* La conservazione dei dati è di 14 giorni.  


Questo cmdlet e questa API limitano il numero di righe che è possibile recuperare in una chiamata a 1000 righe (circa 3 giorni di dati con una granularità di 5 minuti). Tuttavia, questo comando può essere chiamato più volte con intervalli di tempo iniziale e finale differenti per recuperare ulteriori dati


Recuperare le metriche:

	PS C:\> $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Ottenere altri giorni, ripetendo la chiamata e accodando i dati:

	PS C:\> $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
Formattare la tabella:

    PS C:\> $table = Format-MetricsAsTable $metrics 

Esportare in un file CSV:

    PS C:\> foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

### Ottenere le metriche di consumo delle risorse per un database elastico

Queste API sono le stesse API (V12) correnti utilizzate per monitorare l'utilizzo delle risorse di un database autonomo, fatta eccezione per la seguente differenza semantica

* Per questa API, le metriche recuperate vengono espresse come percentuale del valore databaseDtuMax (o limite di utilizzo equivalente per la metrica sottostante, come CPU, IO e così via) impostato per tale pool elastico. Ad esempio, l’utilizzo del 50% di una di queste metriche indica che il consumo di risorse specifico si trova al 50% del limite di utilizzo per DB per quella risorsa nel pool elastico padre. 

Ottenere la metrica: PS C:\> $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

Ottenere altri giorni, se necessario, ripetendo la chiamata e accodando i dati:

    PS C:\> $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Formattare la tabella:

    PS C:\> $table = Format-MetricsAsTable $metrics 

Esportare in un file CSV:

    PS C:\> foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## Riassumendo


    Switch-AzureMode -Name AzureResourceManager
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000
    New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"
    New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"
    New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"
    New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100
    New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1" -MaxSizeBytes 10GB
    
    
    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## Passaggi successivi

Dopo aver creato un pool elastico, è possibile gestire i database elastici nel pool mediante la creazione di processi elastici. I processi elastici facilitano l’esecuzione di script T-SQL su qualsiasi numero di database nel pool.

Per ulteriori informazioni, vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md).


## Riferimento ai database elastici

Per ulteriori informazioni sui database elastici e sui pool di database elastici, inclusi i dettagli sulle API e sugli errori, vedere [Riferimento ai pool di database elastici](sql-database-elastic-pool-reference.md).

<!---HONumber=July15_HO4-->