<properties
	pageTitle="Introduzione ai processi di database elastici"
	description="come utilizzare i processi di database elastici"
	services="sql-database"
	documentationCenter=""  
	manager="jhubbard"
	authors="ddove"/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="ddove" />  

# Introduzione ai processi di Database Elastici

I processi di database elastici (anteprima) per il database SQL di Azure consentono di eseguire in maniera affidabile script T-SQL che si estendono su più database e effettuano tentativi automatici per garantire il completamento delle operazioni . Per ulteriori informazioni sulla funzionalità dei processi di database elastici, vedere la [panoramica della funzionalità](sql-database-elastic-jobs-overview.md).

Questo argomento supporta l'esempio presentato in [Introduzione agli strumenti del Database elastico](sql-database-elastic-scale-get-started.md). Al termine, si apprenderà come creare e gestire processi di gestione di un gruppo di database correlati. Non è necessario usare gli strumenti di scalabilità elastica per sfruttare i vantaggi dei processi elastici.

## Prerequisiti

Scaricare ed eseguire [Introduzione allo strumento di esempio del Database elastico](sql-database-elastic-scale-get-started.md).

## Creare un gestore mappe partizione utilizzando l'applicazione di esempio

Di seguito si creerà un gestore mappe partizione con diverse partizioni, seguita dall'inserimento di dati nelle partizioni. Se si dispone già di programma di installazione di partizioni con dati partizionati in essi, è possibile ignorare i passaggi seguenti e passare alla sezione successiva.

1. Compilare ed eseguire l’applicazione di esempio **Introduzione agli strumenti del Database elastico**. Seguire i passaggi fino al passaggio 7 nella sezione [Scaricare ed eseguire l'app di esempio](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). Alla fine del passaggio 7, verrà visualizzato il seguente prompt dei comandi:

	![Aprire il prompt dei comandi.][1]

2.  Nella finestra di comando, digitare "1" e premere **Invio**. Viene creato il gestore delle mappe partizioni e aggiunge due partizioni al server. Digitare "3" e premere **Invia**. Ripetere l'operazione quattro volte. Consente di inserire righe di dati di esempio nelle partizioni.

3.  Nel [portale di Azure](https://portal.azure.com) dovrebbero essere visualizzati tre nuovi database nel server v12:

	![Conferma di Visual Studio][2]

	A questo punto, si creerà un insieme di database personalizzati che riflette tutti i database del mapping della partizione. Questo consentirà di creare ed eseguire un processo che aggiunge una nuova tabella tra le partizioni.

Si creerà una destinazione di partizionamento della mappa, utilizzando il cmdlet **New-AzureSqlJobTarget**. Il database di gestione della mappa di partizione deve essere impostato come destinazione di database e quindi il mapping di partizione specifico viene specificato come destinazione. In questo caso, invece, tutti i database nel server dovranno essere enumerati e aggiunti alla nuova raccolta personalizzata, ad eccezione del database master.

##Crea una raccolta personalizzata e aggiunge tutti i database nel server alla destinazione della raccolta personalizzata ad eccezione del database master.


	$customCollectionName = "dbs_in_server"
	New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 
	$ResourceGroupName = "ddove_samples"
	$ServerName = "samples"
	$dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
	$dbsinserver | %{
    $currentdb = $_.DatabaseName 
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason
                
        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target." 
        }

        else
        {
            throw $_
        }
    
    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
}
	
## Creare uno Script T-SQL per l'esecuzione tra database

	$scriptName = "NewTable"
	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
	BEGIN
		CREATE TABLE Test(
			TestId INT PRIMARY KEY IDENTITY,
			InsertionTime DATETIME2
		);
	END
	GO
	INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
	GO"

	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

##Creare il processo per eseguire uno script in un gruppo personalizzato di database

	$jobName = "create on server dbs"
	$scriptName = "NewTable"
	$customCollectionName = "dbs_in_server"
	$credentialName = "ddove66"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
	Write-Output $job


##Eseguire il processo 

Il seguente script PowerShell può essere utilizzato per eseguire un processo esistente:

Aggiornare la variabile seguente per riflettere il nome del processo desiderato da eseguire:

	$jobName = "create on server dbs"
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
	Write-Output $jobExecution
 
## Recuperare lo stato di un singolo processo di esecuzione

Utilizzare la stessa cmdlet**Get-AzureSqlJobExecution**con il parametro**IncludeChildren**per visualizzare lo stato di esecuzioni del processo figlio, vale a dire lo stato specifico per ogni esecuzione del processo in tutti i database di destinazione del processo.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
	Write-Output $jobExecutions 

## Visualizzare lo stato su più esecuzioni del processo

Il cmdlet **Get-AzureSqlJobExecution**ha più parametri facoltativi che possono essere utilizzati per visualizzare più esecuzioni di processo, filtrate tramite i parametri forniti. Di seguito vengono illustrati alcuni dei possibili modi per utilizzare Get-AzureSqlJobExecution:

Recuperare tutte le esecuzioni attive di processo di primo livello:

	Get-AzureSqlJobExecution

Recuperare tutte le esecuzioni di processo di primo livello, incluse le esecuzioni di processo inattive:

	Get-AzureSqlJobExecution -IncludeInactive

Recuperare tutte le esecuzioni di processo figlio di un ID di esecuzione processo fornito, incluse le esecuzioni di processo inattive:

	$parentJobExecutionId = "{Job Execution Id}"
	Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

Recuperare tutte le esecuzioni di processo create utilizzando una pianificazione / processo di combinazione, inclusi i processi inattivi:

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Recuperare tutti i processi destinati a una mappa di partizione specificata, inclusi i processi inattivi:

	$shardMapServerName = "{Shard Map Server Name}"
	$shardMapDatabaseName = "{Shard Map Database Name}"
	$shardMapName = "{Shard Map Name}"
	$target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

Recuperare tutti i processi destinati a una raccolta personalizzata specificata, inclusi i processi inattivi:

	$customCollectionName = "{Custom Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
Recuperare l'elenco delle esecuzioni delle attività di processo in una esecuzione di processo specifica:

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Write-Output $jobTaskExecutions 

Recuperare i dettagli di esecuzione delle attività di processo:

Il seguente script PowerShell può essere utilizzato per visualizzare i dettagli di un'esecuzione delle attività di processo, che è particolarmente utile durante il debug degli errori di esecuzione.

	$jobTaskExecutionId = "{Job Task Execution Id}"
	$jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
	Write-Output $jobTaskExecution

## Recuperare gli errori all'interno delle esecuzioni delle attività di processo

L'oggetto JobTaskExecution include una proprietà per il ciclo di vita dell'attività insieme ad una proprietà del messaggio. Se un'esecuzione delle attività di processo ha esito negativo,la proprietà del ciclo di vita verrà impostata su*Non riuscita*e la proprietà del messaggio verrà impostata sul messaggio di eccezione risultante e il relativo stack. Se un processo ha esito negativo, è importante visualizzare i dettagli delle attività di processo che non sono riuscite per un determinato processo.

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Foreach($jobTaskExecution in $jobTaskExecutions) 
		{
		if($jobTaskExecution.Lifecycle -ne 'Succeeded')
    		{
        	Write-Output $jobTaskExecution
    		}
		}

## In attesa del completamento dell’esecuzione del processo

Il seguente script PowerShell può essere utilizzato per attendere che un’attività di processo venga completata:

	$jobExecutionId = "{Job Execution Id}"
	Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## Creare un criterio di esecuzione personalizzata

I processi di database elastici supportano la creazione di criteri di esecuzione personalizzati che possono essere applicati all'avvio dei processi.
  
Criteri di esecuzione che attualmente consentono la definizione di:

* Nome: Identificatore del criterio di esecuzione.
* Timeout del processo: tempo totale prima che un processo venga annullato dai processi di database elastici.
* Intervallo tra tentativi iniziale: intervallo di attesa prima del primo tentativo.
* Intervallo massimo di tentativi: estremità degli intervalli tra i tentativi da utilizzare.
* Coefficiente di backoff dell’intervallo tra tentativi: coefficiente utilizzato per calcolare l’intervallo successivo tra i tentativi. Viene utilizzata la seguente formula: (Intervallo tentativi iniziale) * Math.pow((Coefficiente di backoff dell’intervallo), (Numero di tentativi) - 2).
* Numero massimo di tentativi: Il numero massimo di tentativi all'interno di un processo.

Il criterio di esecuzione predefinito utilizza i valori seguenti:

* Nome: Criterio di esecuzione predefinito
* Timeout del processo: 1 settimana
* Intervallo tra tentativi iniziale: 100 millisecondi
* Intervallo massimo tra tentativi: 30 minuti
* Coefficiente di intervallo tra tentativi: 2
* Numero massimo di tentativi: 2,147,483,647

Creare il criterio di esecuzione desiderato:

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 10
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $executionPolicy

### Aggiornare il criterio di esecuzione personalizzato

Aggiornare l'aggiornamento del criterio di esecuzione desiderato:

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 15
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $updatedExecutionPolicy
 
## Annullare un processo

I processi di database elastico supportano le richieste di annullamento dei processi. Se i processi di database elastici rilevano una richiesta di annullamento per un processo in fase di esecuzione, verrà effettuato un tentativo di arresto del processo.

E’ possibile cancellare un processo in due modi diversi tramite i processi di database elastici:

1. Annullamento delle attività attualmente in esecuzione: se viene rilevato un annullamento mentre un'attività è attualmente in esecuzione, si tenterà di cancellare l’aspetto di esecuzione corrente dell’attività. Ad esempio: se viene eseguita una query con esecuzione prolungata quando si tenta di eseguire un annullamento, si verificherà un tentativo di annullare la query.
2. Annullamento attività tentativi: Se un annullamento viene rilevato dal thread di controllo prima dell’avvio dell’esecuzione di un'attività, il thread di controllo eviterà l’avvio dell'attività e annullerà la richiesta.

Se viene richiesto un annullamento del processo per un processo padre, tale richiesta verrà rispettata per il processo padre e per tutti i relativi processi figlio.
 
Per inviare una richiesta di annullamento, utilizzare il cmdlet**Stop-AzureSqlJobExecution**e impostare il parametro**JobExecutionId**.

	$jobExecutionId = "{Job Execution Id}"
	Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## Eliminare un processo in base al nome e la cronologia del processo

I processi di database elastici supportano l'eliminazione asincrona dei processi. Un processo può essere contrassegnato per l'eliminazione e il sistema lo eliminerà con tutta la relativa cronologia dopo il completamento di tutte le esecuzioni di processo per tale processo. Il sistema non annullerà automaticamente le esecuzioni di processo attive.

Al contrario, è necessario richiamare Stop-AzureSqlJobExecution per annullare le esecuzioni di processo attive.

Per attivare l'eliminazione di processi, utilizzare il cmdlet**Remove-AzureSqlJob**e impostare il parametro**JobName**.

	$jobName = "{Job Name}"
	Remove-AzureSqlJob -JobName $jobName
 
## Creare una destinazione database personalizzata
Le destinazioni personalizzate per i database possono essere definite nei processi di database elastici che possono essere utilizzati per l'esecuzione diretta o per l'inclusione in un gruppo di database personalizzato. Poiché i**pool di database elastici**non sono ancora direttamente supportati tramite le API PowerShell, è sufficiente creare una destinazione database personalizzata e una destinazione per la raccolta dei database personalizzata che comprenda tutti i database nel pool.

Impostare le seguenti variabili in modo da riflettere le informazioni desiderate sul database:

	$databaseName = "{Database Name}"
	$databaseServerName = "{Server Name}"
	New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## Creare una destinazione per la raccolta dei database personalizzata
È possibile definire una destinazione per la raccolta dei database personalizzata per consentire l'esecuzione in più destinazioni dei database definiti. Dopo aver creato un gruppo di database, i database possono essere associati alla destinazione della raccolta personalizzata.

Impostare le seguenti variabili in modo da riflettere la configurazione della destinazione della raccolta personalizzata desiderata:

	$customCollectionName = "{Custom Database Collection Name}"
	New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### Aggiungere database a una destinazione per la raccolta dei database personalizzata

Le destinazioni di database possono essere associate alle destinazioni delle raccolte di database personalizzate per creare un gruppo di database. Ogni volta che viene creato un processo destinato a una destinazione della raccolta di database personalizzata, esso verrà esteso ai database associati al gruppo al momento dell’esecuzione.

Aggiungere il database desiderato a una raccolta personalizzata specifica:

	$serverName = "{Database Server Name}"
	$databaseName = "{Database Name}"
	$customCollectionName = "{Custom Database Collection Name}"
	Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### Verificare i database in una destinazione per la raccolta dei database personalizzata

Utilizzare il cmdlet**Get-AzureSqlJobTarget**per recuperare i database figlio all'interno di una destinazione di una raccolta database personalizzata.
 
	$customCollectionName = "{Custom Database Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
	Write-Output $childTargets

### Creare un processo per eseguire uno script in una destinazione di una raccolta database personalizzata

Utilizzare il cmdlet**New-AzureSqlJob**per creare un processo su un gruppo di database definito da una destinazione di raccolta database personalizzata. I processi di database elastici espanderanno il processo in più processi figlio, ognuno corrispondente a un database associato alla destinazione di raccolta database personalizzata e eseguiranno lo script in tutti i database. Anche in questo caso, è importante che gli script siano idempotenti per essere flessibili ai tentativi.

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
	Write-Output $job

## Raccolta dei dati tra database

I**Processi di database elastici**supportano l'esecuzione di una query su un gruppo di database e inviano i risultati alla tabella del database specificata. E’ possibile eseguire una query sulla tabella dopo aver visualizzato i risultati della query da ciascun database. Questo fornisce un meccanismo asincrono per eseguire una query in molti database. I casi di errore, come quando ad esempio uno dei database viene reso temporaneamente non disponibile, vengono gestiti automaticamente tramite tentativi.

La tabella di destinazione specificata verrà creata automaticamente se non esiste ancora una corrispondenza con lo schema del set di risultati restituito. Se un'esecuzione di script restituisce più set di risultati, i processi di database elastici invieranno solo il primo risultato alla tabella di destinazione specificata.

Il seguente script PowerShell consente di eseguire uno script che raccolga i propri risultati in una tabella specificata. Questo script presuppone che uno script T-SQL sia stato creato e che restituisca un singolo set di risultati e che una destinazione di raccolta database personalizzata sia stata creata.

Impostare quanto segue in modo da riflettere lo script, le credenziali e la destinazione di esecuzione desiderati:

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$executionCredentialName = "{Execution Credential Name}"
	$customCollectionName = "{Custom Collection Name}"
	$destinationCredentialName = "{Destination Credential Name}"
	$destinationServerName = "{Destination Server Name}"
	$destinationDatabaseName = "{Destination Database Name}"
	$destinationSchemaName = "{Destination Schema Name}"
	$destinationTableName = "{Destination Table Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### Creare e avviare un processo per scenari di raccolta dati
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
	Write-Output $job
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName
	Write-Output $jobExecution

## Creare una pianificazione per l'esecuzione del processo utilizzando un trigger di processo

Il seguente script di PowerShell può essere utilizzato per creare una pianificazione ricorrente. Questo script usa l'intervallo di minuti, ma New-AzureSqlJobSchedule supporta anche i parametri -DayInterval, -HourInterval, -MonthInterval e -WeekInterval. Le pianificazioni che vengono eseguite una sola volta possono essere create specificando -OneTime.

Creare una nuova pianificazione:

	$scheduleName = "Every one minute"
	$minuteInterval = 1
	$startTime = (Get-Date).ToUniversalTime()
	$schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
	Write-Output $schedule

### Creare un trigger di processo per eseguire un processo in una pianificazione temporale

È possibile definire un trigger di processo per eseguire un processo in base a una pianificazione temporale. Il seguente script di PowerShell può essere utilizzato per creare un trigger di processo.

Impostare le seguenti variabili in modo che corrispondano al processo e alla pianificazione desiderati:

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	$jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
	Write-Output $jobTrigger

### Rimuovere un'associazione pianificata per arrestare l'esecuzione di processo pianificata

Per sospendere l'esecuzione del processo ricorrente tramite un trigger di processo, è possibile rimuovere il trigger di processo. Rimuovere un trigger di processo per arrestare l’esecuzione di un processo in base a una pianificazione mediante il cmdlet **Remove-AzureSqlJobTrigger**.

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName





## Importare i risultati della query database elastica in Excel

 È possibile importare i risultati da di una query a un file di Excel.

1. Avviare Excel 2013.
2. 	Individuare il **dati** della barra multifunzione.
3. 	Fare clic su**Da altre fonti**quindi**Da SQL Server**.

	![Importazione di Excel da altre origini][5]
4. 	In **Connessione guidata dati** digitare le credenziali di accesso e il nome del server. Quindi fare clic su **Avanti**.
5. 	Nella finestra di dialogo **Selezionare il database che contiene i dati che si desidera**, selezionare il database **ElasticDBQuery**.
6. 	Selezionare la tabella **Clienti** nella visualizzazione elenco e fare clic su **Avanti**. Fare clic su **Fine**.
7. 	Nel modulo **Importazione dati** sotto **Selezionare come si desidera visualizzare i dati nella cartella di lavoro**, selezionare **Tabella** e fare clic su **OK**.

Tutte le righe dalla tabella **Clienti**, archiviate in diverse partizioni sono riportate nel foglio Excel.

## Passaggi successivi
È ora possibile usare le funzioni dei dati di Excel. Usare la stringa di connessione con il nome del server, il nome del database e le credenziali per connettere gli strumenti di integrazione e di Business Intelligence al database di query elastico. Assicurarsi che SQL Server sia supportato come origine dati per lo strumento. Fare riferimento al database di query elastico e alle tabelle esterne come a qualsiasi database di SQL Server e tabella di SQL Server a cui ci si connette con lo strumento.

### Costi
L'uso della funzione di query di database elastico non comporta alcun costo aggiuntivo. In questo momento questa funzionalità è disponibile solo sui database premium come punto finale, tuttavia, le partizioni possono essere di qualsiasi livello di servizio.

Per informazioni sui prezzi, vedere [Dettagli prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->  
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->  

<!---HONumber=AcomDC_0907_2016-->