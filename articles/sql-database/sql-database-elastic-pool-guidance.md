<properties 
	pageTitle="Considerazioni su prezzo e prestazioni per un pool di database elastici di Database SQL di Azure" 
	description="Un pool di database elastico è una raccolta di risorse disponibili condivise da un gruppo di database elastici. Questo documento vengono fornite informazioni utili per valutare l'idoneità dell'utilizzo di un pool di database flessibile per un gruppo di database." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/26/2016" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Considerazioni di prezzo e prestazioni per un pool di database flessibile


Valutare se l'uso di un pool di database elastici per un gruppo di database è redditizio in base ai modelli di utilizzo del database e alle differenze di prezzo tra un pool di database elastici e singoli database. Vengono inoltre fornite informazioni aggiuntive per assistere nella determinazione delle dimensioni del pool corrente necessarie per un set di database SQL esistente.

- Per una panoramica dei pool di database elastico, vedere [Pool database elastici di Database SQL](sql-database-elastic-pool.md).
- Per informazioni dettagliate sui pool di database elastici, vedere [Riferimento ai pool di database elastici di database SQL](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE] I pool di database elastici sono attualmente in anteprima e sono disponibili unicamente con i server di Database SQL V12.

## Un pool elastico di database.

Gli ISV SaaS sviluppano applicazioni basate sui livelli di dati su larga scala costituiti a più database. Un modello comune di applicazione prevede l'assegnazione di un database a ogni cliente. Clienti diversi, tuttavia, hanno modelli di utilizzo differenti e non prevedibili. Per questo motivo, è difficile stabilire a priori i requisiti di risorse di ciascun utente di database. Di conseguenza, per garantire una velocità effettiva notevole e tempi di risposta rapidi, un ISV potrebbe effettuare un overprovisioning delle risorse con un considerevole aumento dei costi. In alternativa, l'ISV può dedicare minore e dei rischi per i propri clienti un'esperienza peggioramento delle prestazioni.

Pool di database flessibile in Database SQL Azure è consentire agli ISV di SaaS ottimizzare le prestazioni di prezzo per un gruppo di database all'interno di un budget prescritto mettendo a disposizione elasticità delle prestazioni per ogni database. I pool di database elastici abilitano l'ISV per l'acquisto di unità di transazione del database elastiche (eDTU) per un pool condiviso da più database per contenere imprevedibili periodi di utilizzo da parte dei singoli database. Il requisito di eDTU di un pool è determinato dall'utilizzo aggregato dei relativi database. La quantità di eDTU disponibili per il pool è controllata dal bilancio ISV. I pool di database elastici rendono più semplice agli ISV ragionare sull’impatto del budget sulle prestazioni e viceversa per il pool. Gli ISV aggiungono semplicemente il database al pool, impostano le necessarie garanzie eDTU o delimitano i database e quindi impostano il valore di eDTU del pool in base al loro budget. Utilizzando i pool di database elastici gli ISV possono aumentare con facilità i servizi offerti da una piccola nuova impresa a un'azienda matura in continua crescita.
  


## Quando prendere in considerazione un pool di database flessibile

I pool di database elastici sono adatti per un numero elevato di database con i modelli di utilizzo specifici. Per un determinato database, questo modello è caratterizzato da un utilizzo medio ridotto con picchi di utilizzo relativamente poco frequenti.

Più database è possibile aggiungere a un pool, maggiori diventano i risparmi. In base al modello di uso dell'applicazione, è possibile vedere i risparmi con un minor di 2 S3 database.

Le sezioni seguenti consentono di comprendere come valutare se la raccolta specifica di database trarrà vantaggio dall'utilizzo di un pool di database elastici. Gli esempi utilizzano pool di database elastici Standard, ma gli stessi principi si applicano anche ai pool Basic e Premium.

### Valutazione dei modelli di utilizzo di database

Nella figura seguente viene illustrato un esempio di un database che trascorre il tempo di inattività, ma anche periodicamente picchi di attività. Si tratta di un modello di utilizzo adatto per un pool di database elastici:
 
   ![un database][1]

Per il periodo di un'ora illustrato sopra, DB1 picchi Dtu fino a 90, ma l'utilizzo medio complessivo è < 5 Dtu. Per eseguire questo carico di lavoro in un singolo database, è necessario un livello di prestazioni S3. Questo tuttavia rimane la maggior parte delle risorse inutilizzate durante i periodi di minore attività.

Un pool di database elastici consente a queste DTU inutilizzate di essere condivise tra più database e quindi riduce la quantità totale di DTU necessarie e i costi complessivi.

Compila l'esempio precedente, si supponga che vi sono altri database con i modelli di utilizzo simili come DB1. Nelle due figure l'utilizzo di database di 4 e 20 sono disposti nello stesso grafico per illustrare la natura non sovrapposte del relativo utilizzo nel tempo:

   ![quattro database][2]

   ![venti database][3]

Dalla riga di colore nera nella figura precedente viene illustrato l'utilizzo di DTU di aggregazione in tutti i database di 20. Viene illustrato che l'utilizzo di DTU aggregato non mai supera le 100 DTU, ciò indica che i 20 database possono condividere 100 eDTU nel corso di tale periodo di tempo. Ciò comporta una riduzione di DTU di 20x e 13x una riduzione del prezzo rispetto all'inserimento di ogni database in livelli di prestazioni S3 per singoli database.


In questo esempio è ideale per i motivi seguenti:

- Esistono grandi differenze tra i picchi di utilizzo e l'utilizzo medio per ogni database.  
- Il picco di utilizzo per ogni database si verifica in diversi momenti nel tempo. 
- Le eDTU sono condivise da un numero elevato di database.


Il prezzo per un pool di database elastici è una funzione delle eDTU del pool. Mentre il prezzo unitario delle eDTU di un pool è 1,5 volte maggiore al prezzo unitario delle eDTU per un singolo database, le **eDTU del pool possono essere condivise da molti database e pertanto in molti casi è necessario un minor numero totale di eDTU**. Queste distinzioni nella determinazione dei prezzi e nella condivisione di eDTU costituiscono la base del potenziale risparmio sul prezzo che il pool è in grado di fornire.

<br>

Le seguenti regole relative al numero di database e l'utilizzo del database consentono di garantire che un pool di database elastici offra costi ridotti rispetto all'utilizzo di livelli di prestazioni per i singoli database.


### Numero minimo di database

Se la somma di DTU di livelli di prestazioni per singoli database è superiore di 1,5x rispetto ai DTU necessari per il pool, allora un pool elastico è più conveniente. Per le dimensioni disponibili vedere [Limiti di archiviazione e di eDTU per i pool di database elastici e i database elastici](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).


***Esempio***<br> Sono necessari almeno 2 database S3 o almeno 15 database S0 per un pool di database elastici di 100 eDTU per una riduzione dei costi maggiore rispetto all'utilizzo di livelli di prestazioni per singoli database.



### Numero massimo di picco contemporaneamente database

Condividendo eDTU, non tutti i database in un pool possono utilizzare contemporaneamente eDTU fino al limite disponibile quando si utilizzano livelli di prestazioni per singoli database. Meno database raggiungono il picco contemporaneamente, minore è il numero da impostare per le eDTU del pool e quindi più redditizio esso diventa. In generale, non più di un 2/3 (o 67%) dei database nel pool deve raggiungere il picco contemporaneamente al limite delle relative eDTU.

***Esempio***<br> Per ridurre i costi per 3 database S3 in un pool di 200 eDTU, al massimo 2 di questi database possono raggiungere contemporaneamente il picco del loro utilizzo. In caso contrario, se più di 2 di questi 4 database S3 raggiungono il picco contemporaneamente, il pool dovrebbe essere ridimensionato a più di 200 eDTU. E se il pool viene ridimensionato a più di 200 eDTU, più database S3 dovranno essere aggiunti al pool per mantenere i costi inferiori rispetto a quelli dei livelli di prestazioni per singoli database.


Si noti in questo esempio non prende in considerazione l'utilizzo di altri database nel pool. Se tutti i database con alcune utilizzo in qualsiasi punto nel tempo, minore di 2/3 (o 67%) dei database possono picco contemporaneamente.


### Utilizzo di DTU per ogni database

Una notevole differenza tra il picco e l'utilizzo medio di un database indica periodi prolungati di utilizzo ridotto e brevi periodi di utilizzo elevato. Questo modello di utilizzo è ideale per la condivisione delle risorse tra database. Un database deve essere considerato per un pool quando relativo picchi di utilizzo sono circa 1.5 volte maggiore relativo utilizzo medio.

    
***Esempio***<br> Un database S3 che raggiunge un picco di 100 DTU e utilizza in media 67 DTU o meno è un buon candidato per la condivisione di eDTU in un pool di database elastici. In alternativa, un database S1 che raggiunge il picco di 20 DTU e utilizza in media 13 DTU o meno è un buon candidato per un pool di database elastici.
    

## L’euristica per confrontare la differenza di prezzo tra un pool di database elastici e singoli database 

L'euristica indicata di seguito consente di stimare se un pool di database elastici è più redditizio rispetto all'utilizzo di singoli database singoli.

1. Stimare le eDTU necessarie per il pool tramite gli elementi seguenti:
    
    MAX (* numero totale di database* * *utilizzo medio di DTU per DB*, *numero di database in picco contemporaneamente * * *picco di utilizzo di DTU per DB*)

2. Selezionare il valore più piccolo di eDTU disponibile per il pool che sia maggiore della stima del passaggio 1. Per le scelte di eDTU disponibili, vedere i valori validi per le eDTU elencati di seguito: [limiti di archiviazione e di eDTU per pool di database elastici e database elastici](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).


3. Calcolare il prezzo per il pool come segue:

    prezzo pool = *pool eDTUs* * *prezzo unitario di pool eDTU*

    Per informazioni sui prezzi, vedere [Dettagli prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/).


4. Confrontare il prezzo di pool dal passaggio 3 al prezzo di utilizzare i livelli di prestazioni appropriati per singoli database.



## Determinazione della dimensione migliore delle eDTU per il pool per database SQL esistenti 

La dimensione ottimale per un pool di database elastici dipende dalle eDTU di aggregazione e dalle risorse di archiviazione necessarie per tutti i database nel pool. Questo consente di individuare il più elevato tra le due quantità seguenti:

* Dtu massima utilizzata da tutti i database nel pool.
* Byte di archiviazione massima utilizzati da tutti i database nel pool. 

Per le dimensioni disponibili vedere [Limiti di archiviazione e di eDTU per i pool di database elastici e i database elastici](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).


### Utilizzare preparazione a livelli di servizio (STA) e viste a gestione dinamica (DMV) per il ridimensionamento delle indicazioni   

Le STA e le DMV offrono opzioni di strumentazione diverse e funzionalità per il ridimensionamento di un pool di database elastici. Indipendentemente dall'opzione di strumentazione utilizzata, la stima delle dimensioni deve essere utilizzata solo per la creazione e la valutazione iniziale di pool di database elastici. Una volta creato un pool, l'utilizzo delle risorse deve essere monitorato in modo accurato e le impostazioni delle prestazioni del pool regolate su e giù in base alle esigenze.

**STA**<br>STA è uno strumento incorporato nel [portale di Azure](https://portal.azure.com) che valuta automaticamente la cronologia d’utilizzo delle risorse dei database in un server di Database SQL esistente e consiglia una configurazione appropriata del pool di database elastici. Per ulteriori informazioni, vedere [Consigli sui livelli di prezzo dei pool di database elastici](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations).

**Strumento di dimensionamento DMV**<br>Lo strumento di dimensionamento DMV viene fornito come uno script di PowerShell e consente di personalizzare le stime di dimensionamento di un pool di database elastici per database esistenti in un server.

### Scelta tra strumenti STA e DMV 

Selezionare lo strumento appropriato per l'analisi dell'applicazione specifica. Nella tabella seguente vengono riepilogate le differenze tra questi strumenti di dimensionamento di 2:

| Funzionalità | STA | Viste a gestione dinamica |
| :--- | :--- | :--- |
| Granularità di campioni di dati utilizzati nell'analisi. | 5 secondi | 5 secondi |
| Considera prezzi differenze tra i livelli di un pool e le prestazioni per singoli database. | Sì | No |
| Consente di personalizzare l'elenco dei database analizzati all'interno di un server. | No | Sì |
| Consente di personalizzare il periodo di tempo utilizzato per l'analisi. | No | Sì |


### Stima delle dimensioni del pool elastica utilizzando STA  

STA valuta la cronologia di utilizzo dei database e consiglia un pool di database elastici quando è più conveniente rispetto all'uso di livelli di prestazioni per singoli database. Se un pool è consigliato, lo strumento fornisce un elenco dei database consigliati, nonché la quantità di pool eDTU e le impostazioni di eDTU min e max per ogni database elastico. Affinché un database possa essere considerato un candidato per un pool, deve esistere per almeno 7 giorni.

Quando si aggiunge un pool di database elastici a un server esistente, STA è disponibile nel portale. Se sono disponibili consigli per un pool di database elastici per tale server, essi vengono visualizzati nella pagina di creazione di "Pool di database elastici”. I clienti possono sempre modificare le configurazioni consigliate per creare i propri raggruppamenti di pool di database elastici.

Per ulteriori informazioni, vedere [Consigli sui livelli di prezzo dei pool di database elastici](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations).

### Stima delle dimensioni del pool elastica utilizzando viste a gestione dinamica (DMV) 

Il DMV [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) misura l'utilizzo delle risorse di un singolo database. Questa DMV fornisce l'utilizzo di CPU, i/o, log e log di un database come percentuale del limite del livello delle prestazioni del database. Questi dati possono essere utilizzati per calcolare l'utilizzo del valore di DTU di un database di ogni 15 secondi.

L'utilizzo aggregato di eDTU per un pool di database elastici in un intervallo di 15 secondi può essere stimato sommando l'utilizzo di eDTU per tutti i database candidati durante tale periodo. A seconda degli obiettivi di prestazioni specifici, è consigliabile annullare una piccola percentuale dei dati di esempio. Ad esempio, un 99esimo valore percentile di eDTU aggregate in tutti gli intervalli di tempo, può essere applicato per escludere gli outlier e fare in modo che le eDTU di un pool di database elastici occupino il 99% degli intervalli di tempo campione.

## Script di PowerShell per la stima dell'utilizzo di eDTU di database aggregate

Viene fornito uno script di PowerShell di esempio per stimare i valori di eDTU di aggregazione per i database utente in un server di Database SQL.

Lo script raccoglie solo i dati mentre è in esecuzione. Per un carico di lavoro di produzione tipico è consigliabile eseguire lo script per almeno un giorno, sebbene una settimana o anche più probabile che fornirà una stima più accurata. Eseguire lo script per un periodo di tempo che rappresenta il carico di lavoro tipico di database.

> [AZURE.IMPORTANT] È necessario mantenere aperta la finestra di PowerShell durante l'esecuzione dello script. Non chiudere la finestra di PowerShell senza aver prima eseguito lo script per un periodo di tempo sufficiente e acquisire dati sufficienti per rappresentare il carico di lavoro tipico che si estende su entrambi i tempi dell'utilizzo di picco e normale.

### Prerequisiti degli script 

Installare i componenti seguenti prima di eseguire lo script.:

- La versione più recente di [Strumenti della riga di comando di PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
- Il [pacchetto delle funzionalità di SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=42295).


### Dettagli script


È possibile eseguire lo script dal computer locale o una macchina virtuale nel cloud. Se viene eseguito dal computer locale, si possono causare costi di uscita di dati perché lo script deve scaricare dati da database di destinazione. Di seguito viene illustrata la stima di volume dati basata sul numero di database di destinazione e la durata dell'esecuzione dello script. Per dati di Azure i costi di trasferimento fare riferimento a [Dettagli prezzi di trasferimento dati](https://azure.microsoft.com/pricing/details/data-transfers/).
       
 -     1 database all'ora = 38KB
 -     1 database al giorno = 900KB
 -     1 database settimana = 6MB
 -     100 database al giorno = 90MB
 -     500 database per ogni settimana = 3GB

Lo script consente di escludere determinati database non sono buoni candidati per l'offerta di anteprima pubblica corrente del livello del Pool elastica Standard. Se si desidera escludere database aggiuntivi dal server di destinazione, è possibile modificare lo script per soddisfare i criteri specificati. Per impostazione predefinita, lo script non viene compilato le seguenti informazioni:

* Database (database già in un pool elastico) elastici.
* Il database del server master.

Lo script è necessario un database di output per memorizzare dati intermedi per l'analisi. È possibile utilizzare un database nuovo o esistente. Anche se non si tratti tecnicamente necessaria per l'esecuzione dello strumento, il database di output deve essere in un server diverso per evitare di interferire con il risultato dell'analisi. Indicare il livello di prestazioni del database di output di almeno S0 o versione successiva. Quando si raccolgono un lungo periodo di dati per un numero elevato di database, è possibile effettuare l'aggiornamento del database di output a un livello di prestazioni superiore.

Lo script deve fornire le credenziali per connettersi al server di destinazione (il candidato pool database elastica) con il nome completo del server come "abcdef.database.windows.net". Lo script non supporta attualmente l'analisi di più server contemporaneamente.


Dopo l'invio di valori per il set iniziale di parametri, viene richiesto di accedere all'account Azure. Si tratta per l'accesso al server di destinazione, non il server di database di output.
	
Se si verificano i seguenti avvisi durante l'esecuzione di script possono essere ignorati:

- Avviso: Il cmdlet Switch-AzureMode è deprecato.
- Avviso: Impossibile ottenere informazioni del servizio SQL Server. Tentativo di connettersi a WMI su 'Microsoft.Azure.Commands.Sql.dll' non riuscito con il seguente errore: il server RPC non è disponibile.

Al completamento dello script il risultato sarà il numero stimato di eDTU necessarie per un pool elastico affinché contenga tutti i database candidati nel server di destinazione. Questo valore di eDTU stimato può essere utilizzabile per creare e configurare un pool di database elastici che contenga questi database. Dopo la creazione del pool e lo spostamento dei database nel pool, esso deve essere controllato attentamente per alcuni giorni e devono essere effettuate le modifiche necessarie alla configurazione delle eDTU del pool in base alle esigenze.

> [AZURE.IMPORTANT] Questo script contiene comandi per le versioni di Azure PowerShell fino alle versione 1.0 *esclusa* e versioni successive. È possibile controllare la versione di Azure PowerShell con il comando **Get-Module azure | format-table version**. Per informazioni dettagliate, vedere [Deprecazione di Switch-AzureMode, in Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).


    
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
    
    # For Elastic database pool candidates, we exclude master, and any databases that are already in a pool. You may add more databases to the excluded list below as needed
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool")}
    
    $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
    $destinationTableName = "resource_stats_output"
    
    # Create a table in output database for metrics collection
    $sql = "
    IF  NOT EXISTS (SELECT * FROM sys.objects 
    WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))
    
    BEGIN
    Create Table $($destinationTableName) (database_name varchar(128), slo varchar(20), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
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
    Write-Host "Collecting metrics..." 
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
    WHEN 'P1' THEN 125
    WHEN 'P2' THEN 250
    WHEN 'P3' THEN 1000
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
    SELECT @dbname as database_name, @SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
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
    WHEN 'P1' THEN 100
    WHEN 'P2' THEN 200
    WHEN 'P3' THEN 800
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
    SELECT @dbname as database_name, @SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    
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
    
    Write-Host "Analyzing the collected metrics...."
    # Analysis query that does aggregation of the resource metrics to calculate pool size.
    $sql1 = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
    WITH group_stats AS
    (
    SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
    FROM resource_stats_output 
    WHERE slo LIKE '
    
    $sql2 = '
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
    SELECT ''Total number of DTUs dominated by storage: '' + convert(varchar(100), @DTU_Storage)
    ELSE 
    SELECT ''Total number of DTUs dominated by resource consumption: '' + convert(varchar(100), @DTU_Perf_99)'
    
    #check if there are any web/biz edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'shared%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "Shared*")
    {
    write-host "`nWeb/Business edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'Shared%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
    
    #check if there are any basic edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'Basic%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "Basic*")
    {
    write-host "`nBasic edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'Basic%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]} 
    }
    
    #check if there are any standard edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'S%' AND slo NOT LIKE 'Shared%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "S*")
    {
    write-host "`nStandard edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'S%' AND slo NOT LIKE 'Shared%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
    
    #check if there are any premium edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'P%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "P*")
    {
    write-host "`nPremium edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'P%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
        

## Riepilogo

Non tutti i singoli database sono candidati ottimali per pool database elastica. I database con i modelli di utilizzo che sono caratterizzati da una bassa media di utilizzo e picchi di utilizzo relativamente poco frequenti sono candidati adatti per i pool di database elastici. Modelli di utilizzo di applicazione sono dinamici, utilizzare le informazioni e strumenti descritti in questo articolo per una valutazione iniziale per verificare se un pool database elastica è una buona scelta per alcuni o tutti i database. In questo articolo è solo un punto di partenza per facilitare la decisione relativa al o meno un pool database elastica è una scelta ideale. Tenere presente che è necessario monitorare continuamente l'utilizzo delle risorse cronologico (utilizzando Apartment o DMV) e rivalutare costantemente i livelli di prestazioni di tutti i database. Tenere presente che è possibile spostare facilmente i database fuori e dentro i pool di database elastici e se si dispone di un numero molto elevato di database è possibile avere più pool di dimensioni variabili in cui possono essere divisi i database.



<!--Image references-->
[1]: ./media/sql-database-elastic-pool-guidance/one-database.png
[2]: ./media/sql-database-elastic-pool-guidance/four-databases.png
[3]: ./media/sql-database-elastic-pool-guidance/twenty-databases.png

<!---HONumber=AcomDC_0302_2016-->