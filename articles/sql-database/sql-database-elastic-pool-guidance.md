<properties 
	pageTitle="Considerazioni di prezzo e prestazioni per un pool database elastica | Pool di database elastica Database SQL Azure" 
	description="Un pool di database elastico è una raccolta di risorse disponibili condivise da un gruppo di database elastici. Questo documento vengono fornite informazioni utili per valutare l'idoneità dell'utilizzo di un pool di database flessibile per un gruppo di database." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/24/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Considerazioni di prezzo e prestazioni per un pool di database flessibile


Questo documento vengono fornite indicazioni per valutare se si utilizza un pool di database flessibile per un gruppo di database è economicamente efficiente in base ai modelli di utilizzo del database e dei prezzi delle differenze tra un pool elastico e singoli database. Vengono inoltre fornite informazioni aggiuntive per assistere nella determinazione delle dimensioni del pool corrente necessari per un set di database SQL esistente.

- Per una panoramica dei pool di database flessibile, vedere [pool database elastica di Database SQL](sql-database-elastic-pool.md).
- Per informazioni dettagliate sui pool di database flessibile, vedere [riferimento al Database SQL database elastica pool](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE]I pool elastici sono attualmente in anteprima e sono disponibili unicamente con i server di database SQL V12.

## Un pool elastico di database.

Gli ISV SaaS sviluppare applicazioni basate sui livelli di dati su larga scala costituita da più database. Un modello comune di applicazione è per ognuno di questi database che consente ai clienti diversi con i modelli di utilizzo di variabili in modo univoco e imprevedibili. Può essere difficile per gli ISV stimare i requisiti di risorse di ciascun database singolarmente. In questi casi, l'ISV può overprovision le risorse a un costo considerevole per garantire velocità effettiva favorevole e tempi di risposta per tutti i database. In alternativa, l'ISV può dedicare minore e dei rischi per i propri clienti un'esperienza peggioramento delle prestazioni.

Pool di database flessibile in Database SQL Azure è consentire agli ISV di SaaS ottimizzare le prestazioni di prezzo per un gruppo di database all'interno di un budget prescritto mettendo a disposizione elasticità delle prestazioni per ogni database. Pool elastico abilitare l'ISV per l'acquisto di velocità effettiva unità database (Dtu) per un pool elastico condiviso da più database per contenere imprevedibili periodi di utilizzo dai singoli database. Il requisito di DTU di un pool elastico è determinato dall'utilizzo di aggregata dei relativi database. La quantità di Dtu disponibili per il pool elastico è controllata dal bilancio ISV. Pool elastico semplifica per gli ISV a motivo tramite l'impatto del budget sulle prestazioni e viceversa per il pool. Gli ISV aggiunge semplicemente il database al pool elastico, imposta le necessarie garanzie DTU o delimitatori per i database e quindi imposta il valore di DTU del pool in base al loro budget. Utilizzando pool elastico ISV possono aumentare con facilità i servizi offerti da un avvio snello da un'azienda maturo in crescenti scala.
  


## Quando prendere in considerazione un pool di database flessibile

Pool elastico sono adatti per un numero elevato di database con i modelli di utilizzo specifico. Per un determinato database, questo modello è caratterizzato da un utilizzo medio ridotto con picchi di utilizzo relativamente poco frequenti.

Più database è possibile aggiungere a un pool di maggiore che diventano i risparmi, ma a seconda del modello l'utilizzo dell'applicazione, è possibile vedere risparmi con minor 4 S3 database.

Nelle sezioni seguenti consentono di comprendere come valutare se l'insieme specifico di database trarranno vantaggio dall'utilizzo di un pool elastico.

### Valutazione dei modelli di utilizzo di database

Nella figura seguente viene illustrato un esempio di un database che trascorre il tempo di inattività, ma anche periodicamente picchi di attività. Si tratta di un modello di utilizzo è adatto per un pool elastico:
 
   ![un database][1]

Per il periodo di un'ora illustrato sopra, DB1 picchi Dtu fino a 90, ma l'utilizzo medio complessivo è < 5 Dtu. Per eseguire questo carico di lavoro in un singolo database, è necessario un livello di prestazioni S3. Questo tuttavia rimane la maggior parte delle risorse inutilizzate durante i periodi di minore attività.

Un pool elastico consente a questi Dtu inutilizzati essere condivisi tra più database e quindi riduce la quantità totale di Dtu necessarie e i costi complessivi.

Compila l'esempio precedente, si supponga che vi sono altri database con i modelli di utilizzo simili come DB1. Nelle due figure l'utilizzo di database di 4 e 20 sono disposti nello stesso grafico per illustrare la natura non sovrapposte del relativo utilizzo nel tempo:

   ![quattro database][2]

   ![venti database][3]

Dalla riga di colore nera nella figura precedente viene illustrato l'utilizzo di DTU di aggregazione in tutti i database di 20. Viene illustrato l'utilizzo di DTU aggregata mai supera 100 Dtu che indica che i 20 database possono condividere 100 Dtu nel periodo di tempo. Ciò comporta una riduzione di Dtu di 20x e 6 una riduzione del prezzo rispetto all'inserimento di ogni database in livelli di prestazioni S3 per singoli database.


In questo esempio è ideale per i motivi seguenti:

- Esistono grandi differenze tra i picchi di utilizzo e l'utilizzo medio per ogni database.  
- Il picco di utilizzo per ogni database si verifica in diversi momenti nel tempo. 
- Dtu sono condivisi tra un numero elevato di database.


Il prezzo per un pool elastico è una funzione di pool di Dtu e il numero di database all'interno di esso. Il prezzo unitario DTU di un pool in vigore dei prezzi dei 3 volte maggiore del prezzo unitario DTU per un singolo database, Dtu pool può essere condiviso da molti database mentre è pertanto in molti casi sono necessari un minor numero di Dtu totale. Queste distinzioni nella determinazione dei prezzi e condivisione DTU costituiscono la base del potenziale risparmio prezzo in grado di fornire pool.

<br>

Le seguenti regole relative al numero di database e l'utilizzo del database consentono di garantire che un pool elastico offre costi ridotti rispetto all'utilizzo di livelli di prestazioni per i singoli database. Le indicazioni si basano sui prezzi di disponibilità generale (GA). Si noti che i prezzi GA vengono scontati del 50% durante l'anteprima e pertanto queste regole deve essere considerati relativamente prudenti.


### Numero minimo di database

GA prezzi, un pool elastico diventa più di una scelta conveniente prestazioni se 1 DTU può essere condiviso da più di tre database. Ciò significa che la somma di Dtu di livelli di prestazioni per singoli database è superiore a 3 volte Dtu del pool. Limiti di archiviazione e di [DTU dei pool elastici e dei database elastici](sql-database-elastic-pool-reference.md#dtu-and-storage-limits-for-elastic-pools-and-elastic-databases)


***Esempio***<br> sono necessari almeno 4 database S3 o almeno 36 S0 database per un pool elastica DTU 100 per la riduzione dei costi maggiore rispetto all'utilizzo di livelli di prestazioni per singoli database. (Si noti che con i prezzi di anteprima, il punto di pareggio sui prezzi in base al numero di database riduce a 2 database S3 o 17 S0 database).



### Numero massimo di picco contemporaneamente database

Condividendo Dtu, non tutti i database in un pool possono utilizzare contemporaneamente Dtu fino al limite disponibile quando si utilizzano livelli di prestazioni per singoli database. I database meno picco che contemporaneamente, minore il pool che può essere impostato il valore di DTU e più economicamente efficiente che diventa. In generale, non superiore a 1/3 dei database nel pool deve picco contemporaneamente al superamento del limite DTU.

***Esempio***<br> per ridurre i costi per i database S3 4 in un pool di DTU 200, al massimo 2 di questi database possono contemporaneamente picco nel relativo utilizzo. In caso contrario, se più di 2 di questi database S3 4 picco contemporaneamente, il pool di sarebbe essere ridimensionato a più di 200 Dtu. E se il pool viene ridimensionato a più di 200 Dtu, più database S3 dovranno essere aggiunti al pool a contenere i costi inferiori a livelli di prestazioni per singoli database.


Si noti in questo esempio non prende in considerazione l'utilizzo di altri database nel pool. Se tutti i database con alcune utilizzo in qualsiasi punto nel tempo, minore di 1/3 dei database possono picco contemporaneamente.


### Utilizzo di DTU per ogni database

Una notevole differenza tra il picco e l'utilizzo medio di un database indica periodi prolungati di utilizzo ridotto e brevi periodi di utilizzo elevato. Questo modello di utilizzo è ideale per la condivisione delle risorse tra database. Un database deve essere considerato per un pool quando relativo picchi di utilizzo sono circa 3 volte maggiore relativo utilizzo medio.

    
***Esempio***<br> database S3 un picco di 100 Dtu e medio utilizza 30 Dtu o meno è un buon candidato per la condivisione di Dtu in un pool elastico. In alternativa, un database di S1 picchi di 20 Dtu e medio utilizza 7 Dtu o meno è un buon candidato per un pool elastico.
    

## Euristica per confrontare la differenza tra un pool elastico e singoli database dei prezzi 

L'euristica indicata di seguito consentono di stimare se un pool elastico è più conveniente rispetto all'utilizzo di singoli database singoli.

1. Stima le Dtu necessarie per il pool dagli elementi seguenti:
    
    MAX (* numero totale di database * * *DTU utilizzo per DB medio*, *numero di picco contemporaneamente DBs* * *utilizzo di DTU di picco per DB*)

2. Selezionare il valore più piccolo disponibile DTU per il pool è maggiore della stima del passaggio 1. Per le scelte DTU disponibili, vedere i valori validi per Dtu elencati di seguito: [limiti di archiviazione e di DTU di pool elastico e database elastici](sql-database-elastic-pool-reference.md#dtu-and-storage-limits-for-elastic-pools-and-elastic-databases).

    


3. Calcolare il prezzo per il pool come segue:

    prezzo pool = (* pool Dtu * * *prezzo unitario DTU pool*) + (* totale numero DBs * * *prezzo unitario pool DB*)

    Per informazioni sui prezzi, vedere [Dettagli prezzi del database SQL](http://azure.microsoft.com/pricing/details/sql-database/).


4. Confrontare il prezzo di pool dal passaggio 3 al prezzo di utilizzare i livelli di prestazioni appropriati per singoli database.



## Determinazione del pool migliore dimensione DTU per database SQL esistente 

La dimensione ottimale per un pool elastica dipende dalla Dtu di aggregazione e necessarie per tutti i database nel pool di risorse di archiviazione. Questo consente di individuare il più elevato tra le due quantità seguenti:

* Dtu massima utilizzata da tutti i database nel pool.
* Byte di archiviazione massima utilizzati da tutti i database nel pool. 

Si noti che per il livello di servizio Standard, 1 GB di spazio di archiviazione è consentita per ogni 1 DTU configurato per il pool. Ad esempio, se viene configurato un pool con 200 Dtu, il limite di archiviazione è pari a 200 GB.

### Utilizzare preparazione a livelli di servizio (STA) e viste a gestione dinamica (DMV) per il ridimensionamento delle indicazioni   

STA e viste a gestione dinamica offrono opzioni diversi strumenti e funzionalità per il ridimensionamento di un pool elastico. Indipendentemente dall'opzione di strumenti utilizzato, la stima delle dimensioni deve essere utilizzata solo per la creazione di pool elastico e valutazione iniziale. Una volta creato un pool elastico, l'utilizzo delle risorse deve essere monitorato in modo accurato e le impostazioni delle prestazioni del pool di regolato su e giù in base alle esigenze.

**STA**<br>STA è uno strumento incorporato nel portale di Azure che valuta l'utilizzo delle risorse cronologico dei database in un server di Database SQL esistente e consiglia una configurazione appropriata pool elastica automaticamente.

**Lo strumento di dimensionamento DMV**<br>lo strumento di dimensionamento DMV viene fornito come uno script di PowerShell e consente di personalizzare le stime di ridimensionamento di un pool flessibile per i database esistenti in un server.

### Scelta tra strumenti STA e DMV 

Selezionare lo strumento appropriato per l'analisi dell'applicazione specifica. Nella tabella seguente vengono riepilogate le differenze tra questi strumenti di dimensionamento di 2:

| Funzionalità | STA | Viste a gestione dinamica |
| :--- | :--- | :--- |
| Granularità di campioni di dati utilizzati nell'analisi. | 5 secondi | 5 secondi |
| Considera prezzi differenze tra i livelli di un pool e le prestazioni per singoli database. | Sì | No |
| Consente di personalizzare l'elenco dei database analizzati all'interno di un server. | No | Sì |
| Consente di personalizzare il periodo di tempo utilizzato per l'analisi. | No | Sì |


### Stima delle dimensioni del pool elastica utilizzando STA  

Azure valuta la cronologia di utilizzo dei database e consiglia un pool elastico quando è più conveniente rispetto all'uso di livelli di prestazioni per singoli database. Se un pool elastico è consigliato, lo strumento fornisce un elenco dei database consigliati, nonché la quantità di pool Dtu e le impostazioni di DTU min e max per ogni database elastica. Affinché un database devono essere considerati un candidato per un pool elastico, deve esistere per almeno 14 giorni. L'anteprima pubblica pool database elastica è limitato a Standard, in modo che i database Premium non sono ancora considerati come candidati per un pool elastico.

Quando si aggiunge un pool elastico a un server esistente, è disponibile nel portale di Azure STA. Se sono disponibili per il server consigli per un pool elastico, vengono visualizzati nel "elastica Pool del Database' blade di creazione. I clienti sempre possono modificare le configurazioni consigliate per creare i propri raggruppamento pool elastica.

### Stima delle dimensioni del pool elastica utilizzando viste a gestione dinamica (DMV) 

Il [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV misura l'utilizzo delle risorse di un singolo database. Questa DMV fornisce l'utilizzo di CPU, i/o, log e log di un database come percentuale del limite del livello delle prestazioni del database. Questi dati possono essere utilizzati per calcolare l'utilizzo del valore di DTU di un database di ogni 15 secondi.

L'aggregazione DTU utilizzo per un pool elastico in un intervallo può essere stimato sommando l'utilizzo di DTU per tutti i database candidati durante tale periodo di 15 secondi. A seconda degli obiettivi di prestazioni specifici, è consigliabile annullare una piccola percentuale dei dati di esempio. Novantanovesimo valore percentile Dtu aggregato in tutti gli intervalli di tempo, ad esempio, può essere applicato per escludere gli outlier e fornire un pool elastico DTU per occupare il 99% degli intervalli di tempo campione.

## Script di PowerShell per la stima dell'utilizzo di database aggregata DTU

Viene fornito uno script di PowerShell di esempio per stimare i valori di DTU di aggregazione per i database utente in un server di Database SQL.

Lo script raccoglie solo i dati mentre è in esecuzione. Per un carico di lavoro di produzione tipico è consigliabile eseguire lo script per almeno un giorno, sebbene una settimana o anche più probabile che fornirà una stima più accurata. Eseguire lo script per un periodo di tempo che rappresenta il carico di lavoro tipico di database.

> [AZURE.IMPORTANT]È necessario mantenere aperta la finestra di PowerShell durante l'esecuzione dello script. Non chiudere la finestra di PowerShell senza aver prima eseguito lo script per un periodo di tempo sufficiente e acquisire dati sufficienti per rappresentare il carico di lavoro tipico che si estende su entrambi i tempi dell'utilizzo di picco e normale.

### Prerequisiti degli script 

Installare i componenti seguenti prima di eseguire lo script.:

- con gli strumenti della riga di [comando di PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
- Il [feature pack di SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=42295).


### Dettagli script


È possibile eseguire lo script dal computer locale o una macchina virtuale nel cloud. Se viene eseguito dal computer locale, si possono causare costi di uscita di dati perché lo script deve scaricare dati da database di destinazione. Di seguito viene illustrata la stima di volume dati basata sul numero di database di destinazione e la durata dell'esecuzione dello script. Per dati di Azure i costi di trasferimento fare riferimento a [Dettagli prezzi di trasferimento dati](http://azure.microsoft.com/pricing/details/data-transfers/).
       
 -     1 database all'ora = 38KB
 -     1 database al giorno = 900KB
 -     1 database settimana = 6MB
 -     100 database al giorno = 90MB
 -     500 database per ogni settimana = 3GB

Lo script consente di escludere determinati database non sono buoni candidati per l'offerta di anteprima pubblica corrente del livello del Pool elastica Standard. Se si desidera escludere database aggiuntivi dal server di destinazione, è possibile modificare lo script per soddisfare i criteri specificati. Per impostazione predefinita, lo script non viene compilato le seguenti informazioni:

* Tutti i database Premium nel server V12.
* P2 e P3 database nei server V11.
* Database (database già in un pool elastico) elastici.
* Il database del server master.

Lo script è necessario un database di output per memorizzare dati intermedi per l'analisi. È possibile utilizzare un database nuovo o esistente. Anche se non si tratti tecnicamente necessaria per l'esecuzione dello strumento, il database di output deve essere in un server diverso per evitare di interferire con il risultato dell'analisi. Indicare il livello di prestazioni del database di output di almeno S0 o versione successiva. Quando si raccolgono un lungo periodo di dati per un numero elevato di database, è possibile effettuare l'aggiornamento del database di output a un livello di prestazioni superiore.

Lo script deve fornire le credenziali per connettersi al server di destinazione (il candidato pool database elastica) con il nome completo del server come "abcdef.database.windows.net". Lo script non supporta attualmente l'analisi di più server contemporaneamente.



Dopo l'invio di valori per il set iniziale di parametri, viene richiesto di accedere all'account azure. Si tratta per l'accesso al server di destinazione, non il server di database di output.
	
Se si verificano i seguenti avvisi durante l'esecuzione di script possono essere ignorati:

- Avviso: Il cmdlet Switch-AzureMode è deprecato.
- Avviso: Impossibile ottenere informazioni del servizio SQL Server. Tentativo di connettersi a WMI su 'Microsoft.Azure.Commands.Sql.dll' non riuscito con il seguente errore: il server RPC non è disponibile.

Al completamento dello script sarà il numero stimato di Dtu necessarie per un pool flessibile per contenere tutti i database candidati nel server di destinazione. Questo valore di DTU stimato utilizzabile per la creazione e configurazione di un pool flessibile per contenere questi database. Dopo la creazione del pool e i database vengono spostati nel pool, deve essere controllato attentamente per alcuni giorni e le modifiche alla configurazione del pool di DTU devono essere prese in base alle esigenze.


Per selezionare l'intero script per la copia, fare clic su qualsiasi testo nello script 3 volte (clic tre volte).

    
    param (
    [Parameter(Mandatory=$true)][string]$AzureSubscriptionName, # Azure Subscription name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$ResourceGroupName, # Resource Group name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$servername, # full server name like "abcdefg.database.windows.net"
    [Parameter(Mandatory=$true)][string]$username, # user name
    [Parameter(Mandatory=$true)][string]$serverPassword, # password
    [Parameter(Mandatory=$true)][string]$outputServerName, # metrics collection database for analysis. full server name like "zyxwvu.database.windows.net"
    [Parameter(Mandatory=$true)][string]$outputdatabaseName,
    [Parameter(Mandatory=$true)][string]$outputDBUsername,
    [Parameter(Mandatory=$true)][string]$outputDBpassword,
    [Parameter(Mandatory=$true)][int]$duration_minutes # How long to run. Recommend to run for the period of time when your typical workload is running. At least 10 mins.
    )
    
    Add-AzureAccount 
    Select-AzureSubscription $AzureSubscriptionName
    Switch-AzureMode AzureResourceManager
    
    $server = Get-AzureSqlServer -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    
    # Check version/upgrade status of the server
    $upgradestatus = Get-AzureSqlServerUpgrade -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    $version = ""
    if ([string]::IsNullOrWhiteSpace($server.ServerVersion)) 
    {
    $version = $upgradestatus.Status
    }
    else
    {
    $version = $server.ServerVersion
    }
    
    # For Elastic database pool candidates, we exclude master, Premium SKU (P1 in previous version still qualifies), and any databases that are already in a pool. You may add more to the excluded list below as needed
    if ($version -in ("12.0", "Completed")) # This is on Azure Database V12. 
    {
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceLevelObjectiveName -notlike ("P*") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notlike ("P*")}
    }
    else # This is previous versions of Azure Database
    {
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool", "P2", "P3") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool", "P2", "P3")}
    }
    
    # Due to pricing model difference, we don't recommend to put less than 3 databases in a pool. 
    if ($ListOfDBs.Count -lt 3)
    {
    Write-Host "There are less than 3 qualified standalone databases in this server. Not a good candidate for Elastic Database Pool!" -ForegroundColor Red
    }
    
    $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
    $destinationTableName = "resource_stats_output"
    
    # Create a table in output database for metrics collection
    $sql = "
    IF  NOT EXISTS (SELECT * FROM sys.objects 
    WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))
    
    BEGIN
    Create Table $($destinationTableName) (database_name varchar(128), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
    Create Clustered Index ci_endtime ON $($destinationTableName) (end_time);
    END
    TRUNCATE TABLE $($destinationTableName);
    "
    Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 120 
    
    # waittime (minutes) is interval between data collection queries in the loop below.
    $Waittime = 10
    $end_Time = [DateTime]::UtcNow
    $start_time = $end_time.AddMinutes(-$Waittime)
    $finish_time = $end_Time.AddMinutes($duration_minutes)
    
    While ($end_time -lt $finish_time)
    {
     foreach ($db in $ListOfDBs)
     {
    if ($version -in ("12.0", "Completed")) # for V12 databases 
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'S3' THEN 100
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
    SELECT @dbname as database_name, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    else
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'P1' THEN 100 -- for old version of SQL database, P1 has same performance capacity as S3.
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
    SELECT @dbname as database_name, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    #write-host $sql
    
    $result = Invoke-Sqlcmd -ServerInstance $servername -Database $db.DatabaseName -Username $username -Password $serverPassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 3600 
    #bulk copy the metrics to output database
    $bulkCopy = new-object ("Data.SqlClient.SqlBulkCopy") $outputConnectionString 
    $bulkCopy.BulkCopyTimeout = 600
    $bulkCopy.DestinationTableName = "$destinationTableName";
    $bulkCopy.WriteToServer($result);
    
     }
    
     $start_time = $start_time.AddMinutes($Waittime)
     $end_time = $end_time.AddMinutes($Waittime)
     Write-Host $start_time
     Write-Host $end_time
     do {
    Start-Sleep 1
    }
    until (([DateTime]::UtcNow) -ge $end_time)
    }
    
    
    # Analysis query that does aggregation of the resource metrics to calculate pool size.
    $sql = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
    WITH group_stats AS
    (
    SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
    FROM resource_stats_output
    GROUP BY end_time
    )
    -- calculate aggregate storage and DTUs for all DBs in the group
    , group_DTU AS
    (
    SELECT end_time, avg_group_Storage, 
    (SELECT Max(v)
       FROM (VALUES (avg_group_cpu), (avg_group_log), (avg_group_io)) AS value(v)) AS avg_group_DTU
    FROM group_stats
    )
    -- Get top 1 percent of the storage and DTU utilization samples.
    , top1_percent AS (
    SELECT TOP 1 PERCENT avg_group_Storage, avg_group_dtu FROM group_dtu ORDER BY [avg_group_DTU] DESC
    )
    
    -- Max and 99th percentile DTU for the given list of databases if converted into an elastic pool. Storage is increased by factor of 1.25 to accommodate for future growth. Currently storage limit of the pool is determined by the amount of DTUs based on 1GB/DTU.
    --SELECT MAX(avg_group_Storage)*1.25/1024.0 AS Group_Storage_DTU, MAX(avg_group_dtu) AS Group_Performance_DTU, MIN(avg_group_dtu) AS Group_Performance_DTU_99th_percentile FROM top1_percent;
    SELECT @DTU_Storage = MAX(avg_group_Storage)*1.25/1024.0, @DTU_Perf_99 = MIN(avg_group_dtu) FROM top1_percent;
    IF @DTU_Storage > @DTU_Perf_99 
    SELECT @DTU_Storage AS "Pool Size DTU dominated by storage"
    ELSE 
    SELECT @DTU_Perf_99 AS "Pool Size DTU dominated by resource consumption";'
    
    #write-host $sql
    Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    
    

## Riepilogo

Non tutti i singoli database sono candidati ottimali per pool database elastica. Database con i modelli di utilizzo sono caratterizzati da basso utilizzo medio e i picchi di utilizzo relativamente poco frequenti sono adatte alla pool elastico. Modelli di utilizzo di applicazione sono dinamici, utilizzare le informazioni e strumenti descritti in questo articolo per una valutazione iniziale per verificare se un pool database elastica è una buona scelta per alcuni o tutti i database. In questo articolo è solo un punto di partenza per facilitare la decisione relativa al o meno un pool database elastica è una scelta ideale. Tenere presente che è necessario monitorare continuamente l'utilizzo delle risorse cronologico (utilizzando Apartment o DMV) e rivalutare costantemente i livelli di prestazioni di tutti i database. Tenere presente che è possibile spostare facilmente i database e pool elastico e se si dispone di un numero molto elevato di database può avere più pool di dimensioni variabili che è possibile dividere i database in.



<!--Image references-->
[1]: ./media/sql-database-elastic-pool-guidance/one-database.png
[2]: ./media/sql-database-elastic-pool-guidance/four-databases.png
[3]: ./media/sql-database-elastic-pool-guidance/twenty-databases.png

<!---HONumber=July15_HO4-->