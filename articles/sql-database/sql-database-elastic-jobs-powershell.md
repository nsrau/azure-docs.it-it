<properties 
	pageTitle="Panoramica dei processi di database elastici" 
	description="Viene illustrato il servizio processo di database elastico" 
	services="sql-database" 
	documentationCenter=""  
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="ddove; sidneyh" />

# Creare e gestire processi di database elastici del database SQL tramite PowerShell (anteprima)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)

## Panoramica

La funzionalità**processi database elastici**(anteprima) consente di eseguire in maniera affidabile uno script Transact-SQL (T-SQL) o di applicare DACPAC in un gruppo di database che include una raccolta personalizzata di database, in tutti i database di un[pool di database elastici (anteprima)](sql-database-elastic-pool.md)o in una partizione impostata (creata utilizzando la [libreria client di database elastici](sql-database-elastic-database-client-library.md)). Mentre in anteprima,**processi database elastici**è attualmente un servizio Cloud Azure ospitato dal cliente che consente l'esecuzione di attività amministrative ad hoc e pianificate, dette processi. Utilizzando questa funzionalità, è possibile gestire grandi quantità di Database SQL di Azure in maniera semplice ed affidabile su larga scala tramite l'esecuzione di script Transact-SQL per eseguire operazioni amministrative, come ad esempio modifiche dello schema, gestione delle credenziali, aggiornamenti dei dati di riferimento, raccolta dei dati delle prestazioni o raccolta di dati di telemetria tenant (cliente). Per ulteriori informazioni sui processi dei database elastici, vedere [Panoramica dei processi dei database elastici](sql-database-elastic-jobs-overview.md).

Con le API di PowerShell per i **processi di database elastici**, si dispone di flessibilità per definire il gruppo di database sul quale verranno eseguiti gli script. Attualmente, la funzionalità **processi di database elastici** tramite il portale di Azure ha ridotto i set di funzionalità ed è limitata all’esecuzione sui pool di database elastici.

**Processi di database elastici** (anteprima) utilizza più componenti di Azure per definire i processi da eseguire, definire quando eseguire i processi, eseguire i processi, rilevare l'esito positivo o negativo dei processi e facoltativamente specificare una destinazione di risultati per le query che li restituiscono. Poiché le API Powershell incluse in questa versione di anteprima contengono funzionalità aggiuntive rispetto all'anteprima iniziale tramite il portale, è consigliabile installare la versione più recente dei componenti dei **processi di database elastici**. Se è già installata, è possibile aggiornare semplicemente i componenti dei **processi di database elastici**. Per ulteriori informazioni sull'installazione da [Nuget](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.Jobs), vedere [installare i componenti dei processi di database elastici](sql-database-elastic-jobs-service-installation.md).

In questo articolo verrà illustrato come creare tutto ciò che occorre per creare e gestire i **processi di database elastici**, fatta eccezione per la sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su VERSIONE DI PROVA GRATUITA nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo. Questo argomento supporta l'esempio trovato in [Introduzione agli strumenti del Database elastico](sql-database-elastic-scale-get-started.md). Al termine, si apprenderà come creare e gestire i processi per eseguire operazioni amministrative su un gruppo di database partizionati definito da un **set di partizioni** e in alternativa una raccolta personalizzata di database.

## Prerequisiti
* Una sottoscrizione di Azure. Per una versione di valutazione gratuita, vedere [Versione di valutazione gratuita di un mese](http://azure.microsoft.com/pricing/free-trial/).
* Il pacchetto **Processi di database elastici** di PowerShell deve innanzitutto essere scaricato ed importato e i componenti dei processi di database elastici devono essere installati. Seguire la procedura: [Installazione dei processi di database elastici](sql-database-elastic-jobs-service-installation.md).
* Azure PowerShell 0.8.16 o versione successiva. Installare la versione più recente (0.9.5) tramite l’[installazione guidata piattaforma Web](http://go.microsoft.com/fwlink/p/?linkid=320376). Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

### Selezionare la sottoscrizione ad Azure

Per selezionare la sottoscrizione, è necessario l’ID sottoscrizione (**-SubscriptionId**) o il nome della sottoscrizione (**-SubscriptionName**). Se si dispone di più sottoscrizioni è possibile eseguire il cmdlet **Get-AzureSubscription** e copiare le informazioni sulla sottoscrizione desiderata dal set di risultati. Dopo aver ottenuto le informazioni della sottoscrizione, eseguire il commandlet seguente per impostare tale sottoscrizione come predefinita, vale a dire la destinazione per la creazione e la gestione dei processi:

	Select-AzureSubscription -SubscriptionId {SubscriptionID}

[PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) è consigliato per sviluppare ed eseguire gli script di PowerShell con i processi di database elastici.

## Oggetti dei processi di database elastici

Nella tabella seguente sono elencati tutti i tipi di oggetto dei **processi di database elastici** con la relativa descrizione e le rilevanti API di PowerShell.

<table style="width:100%">
  <tr>
    <th>Tipo di oggetto</th>
    <th>Descrizione</th>
    <th>API correlate di PowerShell</th>
  </tr>
  <tr>
    <td>Credenziali</td>
    <td>Nome utente e password da utilizzare per la connessione ai database per l'esecuzione di script o l'applicazione di DACPAC. <p>La password viene crittografata prima dell’invio e archiviata nel database dei processi di database elastici. La password viene decrittografata dal servizio dai processi di database elastici tramite la credenziale creata e caricata dallo script di installazione.</td>
	<td><p>Get-AzureSqlJobCredential</p>
	<p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Script</td>
    <td>Script Transact-SQL da utilizzare per l'esecuzione nei database. Lo script deve essere creato per essere idempotente, poiché il servizio ritenterà l'esecuzione dello script quando si verificheranno degli errori.
	</td>
	<td>
	<p>Get-AzureSqlJobContent</p>
	<p>Get-AzureSqlJobContentDefinition</p>
	<p>New-AzureSqlJobContent</p>
	<p>Set-AzureSqlJobContentDefinition</p>
	</td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Applicazione livello dati </a> pacchetto da applicare tra i database.

	</td>
	<td>
	<p>Get-AzureSqlJobContent</p>
	<p>New-AzureSqlJobContent</p>
	<p>Set-AzureSqlJobContentDefinition</p>
	</td>
  </tr>
  <tr>
    <td>Destinazione del database</td>
    <td>Nome del database e del server che si riferisce a un Database SQL di Azure.

	</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	</td>
  </tr>
  <tr>
    <td>Destinazione di partizionamento della mappa</td>
    <td>Combinazione di una destinazione di database e una credenziale da utilizzare per determinare le informazioni archiviate all'interno di una mappa di partizionamento di un database elastico.
	</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	<p>Set-AzureSqlJobTarget</p>
	</td>
  </tr>
<tr>
    <td>Destinazione di una raccolta personalizzata</td>
    <td>Gruppo definito di database da utilizzare collettivamente per l'esecuzione.</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	</td>
  </tr>
<tr>
    <td>Destinazione figlio di una raccolta personalizzata</td>
    <td>Destinazione di database a cui fa riferimento una raccolta personalizzata.</td>
	<td>
	<p>Add-AzureSqlJobChildTarget</p>
	<p>Remove-AzureSqlJobChildTarget</p>
	</td>
  </tr>

<tr>
    <td>Job</td>
    <td>
	<p>Definizione dei parametri per un processo che possono essere utilizzati per attivare l'esecuzione o per soddisfare una pianificazione.</p>
	</td>
	<td>
	<p>Get-AzureSqlJob</p>
	<p>New-AzureSqlJob</p>
	<p>Set-AzureSqlJob</p>
	</td>
  </tr>

<tr>
    <td>Esecuzione del processo</td>
    <td>
	<p>Contenitore di attività necessarie per soddisfare l'esecuzione di uno script o l’applicazione di un DACPAC a una destinazione utilizzando le credenziali per le connessioni di database con gestione degli errori gestiti in base ai criteri di esecuzione.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecution</p>
	<p>Start-AzureSqlJobExecution</p>
	<p>Stop-AzureSqlJobExecution</p>
	<p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Esecuzione dell'attività di processo</td>
    <td>
	<p>Singola unità di lavoro per soddisfare un processo.</p>
	<p>Se un'attività di processo non è in grado di essere eseguita con successo, verrà registrato il messaggio di eccezione risultante e una nuova attività di processo corrispondente verrà creata ed eseguita in base al criterio di esecuzione specificato.</p></p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecution</p>
	<p>Start-AzureSqlJobExecution</p>
	<p>Stop-AzureSqlJobExecution</p>
	<p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Criterio di esecuzione del processo</td>
    <td>
	<p>Controlla le sospensioni dell’esecuzione dei processi, gli intervalli tra i tentativi, e i limiti dei tentativi.</p>
	<p>I Processi di database elastici includono un criterio di esecuzione di processo predefinito che provoca essenzialmente infiniti tentativi quando si verificano errori di attività di processo con backoff esponenziale di intervalli tra ogni tentativo.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecutionPolicy</p>
	<p>New-AzureSqlJobExecutionPolicy</p>
	<p>Set-AzureSqlJobExecutionPolicy</p>
	</td>
  </tr>

<tr>
    <td>Pianificazione</td>
    <td>
	<p>Specifiche relative al tempo utilizzate affinché l'esecuzione avvenga ad intervalli ricorrenti o una sola volta.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobSchedule</p>
	<p>New-AzureSqlJobSchedule</p>
	<p>Set-AzureSqlJobSchedule</p>
	</td>
  </tr>

<tr>
    <td>Trigger del processo</td>
    <td>
	<p>Un mapping tra un processo e una pianificazione per l'avvio dell’esecuzione del processo in base alla pianificazione.</p>
	</td>
	<td>
	<p>New-AzureSqlJobTrigger</p>
	<p>Remove-AzureSqlJobTrigger</p>
	</td>
  </tr>
</table>

## Tipi di gruppo di processi di database elastici supportati
I **Processi di database elastici** consentono l'esecuzione di script Transact-SQL (T-SQL) o l'applicazione di DACPAC su un gruppo di database. Quando viene inviato un processo da eseguire in un gruppo di database, i processi di database elastici "espanderà" il processo in processi figlio dove ogni processo esegue l’esecuzione richiesta su un singolo database del gruppo.
 
Di seguito è presente un elenco di tipi di gruppo attualmente supportati:

* [Mapping della partizione](sql-database-elastic-scale-shard-map-management.md): quando un processo viene inviato per un mapping della partizione, i processi eseguiranno la query prima sul mapping della partizione per determinare il set di partizioni corrente e poi espanderanno il processo ai processi figlio corrispondenti a ogni partizione all'interno del mapping della partizione.
* Raccolta personalizzata: Specificata per fare riferimento a un set personalizzato definito di database. Quando un processo viene inviato per una raccolta personalizzata, i processi espanderanno il processo ai processi figlio corrispondenti a ogni database attualmente definito all'interno della raccolta personalizzata.

## Impostare la connessione dei processi di database elastici
Dopo aver caricato il modulo di PowerShell, la connessione deve essere impostata per il *Database di controllo*dei processi di database elastici prima di utilizzare le API dei processi. La chiamata di questo cmdlet attiverà una finestra credenziale di pop up con la richiesta di nome utente/password forniti durante l'installazione dei processi di database elastici. Tutti gli esempi forniti in questo argomento presuppongono che il primo passaggio sia già stato eseguito.

Aprire una connessione ai processi di database elastici:

	Use-AzureSqlJobConnection -CurrentAzureSubscription 

## Credenziali crittografate all'interno dei processi di database elastici
Le credenziali del database possono essere inserite nel *Database di controllo* dei processi di database elastici con la relativa password crittografata. È necessario archiviare le credenziali per abilitare l’esecuzione dei processi in un secondo momento, incluso l'utilizzo delle pianificazioni dei processi.
 
La crittografia funziona tramite un certificato creato come parte dello script di installazione. Lo script di installazione crea e carica il certificato nel servizio Cloud di Azure per la decrittografia delle password crittografate archiviate. Il servizio Cloud di Azure archiviano in un secondo momento la chiave pubblica all'interno del *Database di controllo* dei processi di database elastici che consente all'interfaccia del portale di Azure o all’API PowerShell di crittografare una password fornita senza richiedere che il certificato venga installato localmente.
 
Mentre le password delle credenziali vengono crittografate e protette dagli utenti con accesso in sola lettura agli oggetti dei processi di database elastici, è possibile che un utente malintenzionato con accesso in lettura e scrittura agli oggetti dei processi di database elastici rubi una password. Le credenziali sono progettate per essere riutilizzate sulle esecuzioni del processo. Le credenziali vengono passate al database di destinazione quando si stabiliscono connessioni. Attualmente non esistono restrizioni sui database di destinazione utilizzati per ciascuna credenziale, pertanto è possibile che un utente malintenzionato aggiunga una destinazione di database per un database in loro controllo e successivamente avvii un processo che fa riferimento a questo database per ottenere la password della credenziale.

Sono disponibili le procedure consigliate per i processi di database elastici:

* Limitare l'utilizzo delle API a utenti attendibili.
* Le credenziali devono disporre dei privilegi minimi necessari per eseguire l'attività di processo. Ulteriori informazioni possono essere visualizzate all'interno dell’articolo [autorizzazioni e permessi](https://msdn.microsoft.com/library/bb669084.aspx) di MSDN di SQL Server.

### Creazione di una credenziale crittografia per l'esecuzione del processo tra database
Per creare una nuova credenziale crittografata, il cmdlet Get-Credential richiederà un nome utente e una password che possono essere utilizzati per il cmdlet **New-AzureSqlJobCredential**.

	$credentialName = "{Credential Name}"
	$databaseCredential = Get-Credential
	$credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
	Write-Output $credential

### Aggiornamento delle credenziali
Per aggiornare e cambiare una credenziale esistente utilizzare il cmdlet **Set-AzureSqlJobCredential** e impostare il parametro **CredentialName**.

	$credentialName = "{Credential Name}"
	Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## Definizione di una destinazione di mappa di partizionamento dei database elastici
Eseguire un processo su tutti i database in un set di partizioni (creato utilizzando [libreria client di database elastici](sql-database-elastic-database-client-library.md)) utilizzando una mappa di partizionamento come destinazione per il database. In questo esempio viene mostrata la creazione di un'applicazione partizionata utilizzando la libreria client di database elastici. Scaricare ed eseguire [Introduzione agli strumenti del Database elastico](sql-database-elastic-scale-get-started.md).

###Creare un gestore mappe partizione utilizzando l'applicazione di esempio

Di seguito si creerà un gestore mappe partizione con diverse partizioni, seguita dall'inserimento di dati nelle partizioni. Se si dispone già di un programma di installazione di partizioni, è possibile ignorare i passaggi seguenti e passare alla sezione successiva.

1. Compilare ed eseguire l’applicazione di esempio **Introduzione agli strumenti del Database elastico**. Seguire i passaggi fino al passaggio 7 nella sezione [Scaricare ed eseguire l'app di esempio](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). Alla fine del passaggio 7, verrà visualizzato il seguente prompt dei comandi:

	![Aprire il prompt dei comandi.][1]

2.  Nella finestra di comando, digitare "1" e premere **Invio**. Viene creato il gestore delle mappe partizioni e aggiunge due partizioni al server. Digitare "3" e premere **Invio**; ripetere l'azione quattro volte. Consente di inserire righe di dati di esempio nelle partizioni.
  
3.  Il [portale di anteprima di Azure](https://portal.azure.com) deve visualizzare tre nuovi database nel server v12:

	![Conferma di Visual Studio][2]

Creare ora una destinazione di partizionamento della mappa, utilizzando il cmdlet **New-AzureSqlJobTarget**. Il database di gestione della mappa di partizione deve essere impostato come destinazione di database e quindi il mapping di partizione specifico viene specificato come destinazione.

	$shardMapCredentialName = "{Credential Name}"
	$shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
	$shardMapDatabaseServerName = "{ShardMapServerName}"
	$shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
	$shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
	$shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
	Write-Output $shardMapTarget

## Creare uno Script T-SQL per l'esecuzione tra database

Quando si creano script T-SQL per l'esecuzione, è estremamente consigliato compilarli in modo che siano idempotenti e flessibili in caso di errori. I processi di database elastici ritenterà l'esecuzione di uno script ogni volta che l'esecuzione rileva un errore, indipendentemente dalla classificazione dell'errore.

Utilizzare il cmdlet **New-AzureSqlJobContent** per creare e salvare uno script per l'esecuzione e impostare i parametri **- ContentName presente** e **- CommandText**.

	$scriptName = "Create a TestTable"

	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
		CREATE TABLE TestTable(
			TestTableId INT PRIMARY KEY IDENTITY,
			InsertionTime DATETIME2
		);
	END
	GO
	INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
	GO"

	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### Creare un nuovo script da un file
Se lo script T-SQL è definito all'interno di un file, lo script seguente potrebbe essere utilizzato per importare lo script:

	$scriptName = "My Script Imported from a File"
	$scriptPath = "{Path to SQL File}"
	$scriptCommandText = Get-Content -Path $scriptPath
	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### Aggiornamento di uno Script T-SQL per l'esecuzione tra database  

Il seguente script PowerShell può essere utilizzato per aggiornare il testo del comando T-SQL per uno script esistente.

Impostare le seguenti variabili in modo da riflettere la definizione dello script desiderata da impostare:

	$scriptName = "Create a TestTable"
	$scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
	CREATE TABLE TestTable(
		TestTableId INT PRIMARY KEY IDENTITY,
		InsertionTime DATETIME2
	);
	END
	GO

	IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
	BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
	END
	GO

	INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
	GO"

### Aggiornare la definizione di uno script esistente

	Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

##Creare un processo per eseguire uno script in un mapping della partizione

Il seguente script PowerShell può essere utilizzato per avviare un processo per l'esecuzione di uno script su ogni partizione in una mappa di partizionamento di una scala elastica.

Impostare le seguenti variabili in modo da riflettere lo script e la destinazione desiderati:

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$shardMapServerName = "{Shard Map Server Name}"
	$shardMapDatabaseName = "{Shard Map Database Name}"
	$shardMapName = "{Shard Map Name}"
	$credentialName = "{Credential Name}"
	$shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
	$job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
	Write-Output $job

##Eseguire un processo 

Il seguente script PowerShell può essere utilizzato per eseguire un processo esistente:

Aggiornare la variabile seguente per riflettere il nome del processo desiderato da eseguire:

	$jobName = "{Job Name}"
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
	Write-Output $jobExecution
 
## Recuperare lo stato di un singolo processo di esecuzione

Utilizzare il cmdlet**Get-AzureSqlJobExecution** e impostare il parametro **JobExecutionId** per visualizzare lo stato dell'esecuzione del processo.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
	Write-Output $jobExecution

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

I processi di database elastici supportano le richieste di annullamento dei processi. Se i processi di database elastici rilevano una richiesta di annullamento per un processo in fase di esecuzione, verrà effettuato un tentativo di arresto del processo.

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

Il seguente script di PowerShell può essere utilizzato per creare una pianificazione ricorrente. Questo script utilizza un intervallo minuti, ma New-AzureSqlJobSchedule supporta anche i parametri - DayInterval, -HourInterval, -MonthInterval e -WeekInterval. Le pianificazioni che vengono eseguite una sola volta possono essere create specificando -OneTime.

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

### Recuperare i trigger di processo associati a una pianificazione temporale

Il seguente script PowerShell è utilizzabile per ottenere e visualizzare i trigger di processo registrati in una particolare pianificazione temporale.

	$scheduleName = "{Schedule Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
	Write-Output $jobTriggers

### Recuperare i trigger di processo associati a un processo 

Il seguente script PowerShell è utilizzabile per ottenere e visualizzare le pianificazioni che contengono un processo registrato.

	$jobName = "{Job Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
	Write-Output $jobTriggers

## Creare una distribuzione di applicazione livello dati (DACPAC) per l'esecuzione su database

I processi di database elastici possono essere utilizzati per distribuire un'applicazione livello dati (DACPAC) a un gruppo di database. Per creare DACPAC, fare riferimento a questa documentazione. Affinché i processi di database elastici possono distribuire DACPAC in un gruppo di database, il DACPAC deve essere accessibile al servizio. È consigliabile caricare un DACPAC creato in archiviazione di Azure e creare un URI firmato per il DACPAC.

Per inserire DACPAC in processi di database elastici, è possibile utilizzare il seguente script PowerShell:

	$dacpacUri = "{Uri}"
	$dacpacName = "{Dacpac Name}"
	$dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
	Write-Output $dacpac

### Aggiornare una distribuzione di applicazione livello dati (DACPAC) per l'esecuzione su database

I DACPAC esistenti registrati all’interno dei processi di database elastici possono essere aggiornati in modo da fare riferimento ai nuovo URI. Il seguente script PowerShell può essere utilizzato per aggiornare l’URI DACPAC su un DACPAC registrato esistente:

	$dacpacName = "{Dacpac Name}"
	$newDacpacUri = "{Uri}"
	$updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
	Write-Output $updatedDacpac

## Creare un processo per la distribuzione di applicazione livello dati (DACPAC) per l'esecuzione su database

Dopo aver creato un DACPAC all'interno di processi di database elastici, è possibile creare un processo per applicare il DACPAC su un gruppo di database. Utilizzare il seguente script di PowerShell per creare un processo DACPAC su una raccolta personalizzata di database:

	$jobName = "{Job Name}"
	$dacpacName = "{Dacpac Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $dacpacName -TargetId $target.TargetId
	Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->

<!---HONumber=August15_HO7-->