---
title: Creare e gestire processi elastici con PowerShell | Documentazione Microsoft
description: PowerShell viene utilizzato per gestire i pool del database SQL di Azure
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 737d8d13-5632-4e18-9cb0-4d3b8a19e495
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: b4c97e8f51581f9a3f7c5a8d8e82562255fe7b48
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="create-and-manage-sql-database-elastic-jobs-using-powershell-preview"></a>Creare e gestire processi elastici del database SQL con PowerShell (anteprima)

Le API di PowerShell per i **processi di database elastici** , in anteprima, consentono di definire il gruppo di database sul quale verranno eseguiti gli script. Questo articolo illustra come creare e gestire **processi di database elastici** con i cmdlet di PowerShell. Vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Prerequisiti
* Una sottoscrizione di Azure. Per una versione di valutazione gratuita, vedere [Versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Un set di database creato con gli strumenti di database elastici. Vedere [Iniziare a usare gli strumenti di database elastici](sql-database-elastic-scale-get-started.md).
* Azure PowerShell. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* **processi di database elastici** di PowerShell, vedere [Installing processi di database elastici](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Selezionare la sottoscrizione ad Azure
Per selezionare la sottoscrizione, è necessario l'ID sottoscrizione (**-SubscriptionId**) o il nome della sottoscrizione (**-SubscriptionName**). Se sono disponibili più sottoscrizioni, è possibile eseguire il cmdlet **Get-AzureRmSubscription** e copiare le informazioni sulla sottoscrizione desiderata dal set di risultati. Dopo aver ottenuto le informazioni della sottoscrizione, eseguire il commandlet seguente per impostare tale sottoscrizione come predefinita, vale a dire la destinazione per la creazione e la gestione dei processi:

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

L'utilizzo di [PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) è consigliato per sviluppare ed eseguire gli script di PowerShell sui processi di database elastici.

## <a name="elastic-database-jobs-objects"></a>Oggetti dei processi di database elastici
La tabella seguente include l'elenco di tutti i tipi di oggetto dei **processi di database elastici** con la relativa descrizione e le API di PowerShell rilevanti.

<table style="width:100%">
  <tr>
    <th>Tipo di oggetto</th>
    <th>Descrizione</th>
    <th>API correlate di PowerShell</th>
  </tr>
  <tr>
    <td>Credenziali</td>
    <td>Nome utente e password da utilizzare per la connessione ai database per l'esecuzione di script o l'applicazione di DACPAC. <p>La password viene crittografata prima dell’invio e archiviata nel database dei processi di database elastici.  La password viene decrittografata dal servizio dai processi di database elastici tramite la credenziale creata e caricata dallo script di installazione.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Script</td>
    <td>Script Transact-SQL da utilizzare per l'esecuzione nei database.  Lo script deve essere creato per essere idempotente, poiché il servizio ritenterà l'esecuzione dello script quando si verificheranno degli errori.
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
    <td>Pacchetto dell'<a href="https://msdn.microsoft.com/library/ee210546.aspx">applicazione livello dati </a> da applicare tra i database.

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

## <a name="supported-elastic-database-jobs-group-types"></a>Tipi di gruppo di processi di database elastici supportati
Il processo esegue script Transact-SQL (T-SQL) o applica DACPAC in un gruppo di database. Quando viene inviato un processo da eseguire in un gruppo di database, il processo si "espande" in processi figlio e ognuno completa l'esecuzione richiesta in un database singolo del gruppo. 

Si possono creare due tipi di gruppi: 

* [Mappa partizioni](sql-database-elastic-scale-shard-map-management.md) : quando viene inviato un processo destinato a una mappa partizioni, il processo esegue query sulla mappa partizioni per determinare il set di partizioni corrente e quindi crea processi figlio per ogni partizione nella mappa partizioni.
* Gruppo Raccolta personalizzata: set di database personalizzato. Quando un processo è destinato a una raccolta personalizzata, crea processi figlio per ogni database attualmente nella raccolta personalizzata.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Per impostare la connessione dei processi di database elastici
È necessario impostare una connessione al *database di controllo* dei processi prima di usare le API dei processi. L'esecuzione di questo cmdlet attiva la visualizzazione di una finestra delle credenziali che richiede il nome utente e la password creati durante l'installazione dei processi di database elastici. Tutti gli esempi forniti in questo argomento presuppongono che il primo passaggio sia già stato eseguito.

Aprire una connessione ai processi di database elastici:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Credenziali crittografate all'interno dei processi di database elastici
Le credenziali del database possono essere inserite nel *database di controllo* dei processi con la relativa password crittografata. È necessario archiviare le credenziali per abilitare l'esecuzione dei processi in un secondo momento tramite pianificazioni dei processi.

La crittografia funziona tramite un certificato creato come parte dello script di installazione. Lo script di installazione crea e carica il certificato nel servizio Cloud di Azure per la decrittografia delle password crittografate archiviate. In seguito, il servizio cloud di Azure archivia la chiave pubblica nel *database di controllo* dei processi che consente all'API di PowerShell o all'interfaccia del portale di Azure classico di crittografare una password fornita, senza richiedere l'installazione locale del certificato.

Le password delle credenziali vengono crittografate e protette dagli utenti con accesso in sola lettura agli oggetti dei processi di database elastici. È tuttavia possibile che un utente malintenzionato con accesso in lettura e scrittura agli oggetti dei processi di database elastici possa estrarre una password. Le credenziali sono progettate per essere riutilizzate sulle esecuzioni del processo. Le credenziali vengono passate al database di destinazione quando si stabiliscono connessioni. Attualmente non sono previste restrizioni per i database di destinazione usati per le singole credenziali, quindi un utente malintenzionato potrebbe aggiungere una destinazione di database per un database sotto il suo controllo. L'utente potrebbe quindi avviare un processo destinato a questo database per ottenere la password delle credenziali.

Le procedure consigliate per i processi di database elastici includono:

* Limitare l'utilizzo delle API a utenti attendibili.
* Le credenziali devono disporre dei privilegi minimi necessari per eseguire l'attività di processo.  Per altre informazioni, vedere l'articolo [Autorizzazioni in SQL Server](https://msdn.microsoft.com/library/bb669084.aspx) di MSDN.

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Per creare credenziali crittografate per l'esecuzione di processi nei database
Per creare nuove credenziali crittografate, il [**cmdlet Get-Credential**](https://technet.microsoft.com/library/hh849815.aspx) richiede un nome utente e una password che possono essere passati al [**cmdlet New-AzureSqlJobCredential**](/powershell/module/elasticdatabasejobs/new-azuresqljobcredential).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Per aggiornare le credenziali
Quando la password cambia, usare il [**cmdlet Set-AzureSqlJobCredential**](/powershell/module/elasticdatabasejobs/set-azuresqljobcredential) e impostare il parametro **CredentialName**.

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Per definire la destinazione di una mappa partizioni del database elastico
Per eseguire un processo su tutti i database in un set di partizioni, creato con la [libreria client dei database elastici](sql-database-elastic-database-client-library.md), usare una mappa partizioni come destinazione del database. Questo esempio richiede un'applicazione partizionata creata con la libreria client dei database elastici. Vedere l'esempio in [Iniziare a usare gli strumenti di database elastici](sql-database-elastic-scale-get-started.md).

Il database di gestione delle mappe partizioni deve essere impostato come destinazione di database e quindi si dovrà impostare la mappa partizioni specifica come destinazione.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Creare uno Script T-SQL per l'esecuzione tra database
Quando si creano script T-SQL per l'esecuzione, è consigliabile compilarli in modo che siano [idempotenti](https://en.wikipedia.org/wiki/Idempotence) e resilienti in caso di errori. I processi di database elastici ritenterà l'esecuzione di uno script ogni volta che l'esecuzione rileva un errore, indipendentemente dalla classificazione dell'errore.

Usare il [**cmdlet New-AzureSqlJobContent**](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent) per creare e salvare uno script per l'esecuzione e impostare i parametri **-ContentName** e **-CommandText**.

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

### <a name="create-a-new-script-from-a-file"></a>Creare un nuovo script da un file
Se lo script T-SQL è definito all'interno di un file, usare il codice seguente per importarlo:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Per aggiornare uno script T-SQL per l'esecuzione nei database
Questo script di PowerShell aggiorna il testo del comando T-SQL per uno script esistente.

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

### <a name="to-update-the-definition-to-an-existing-script"></a>Per aggiornare la definizione di uno script esistente
    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Creare un processo che esegua uno script in una mappa partizioni
Questo script di PowerShell avvia un processo per l'esecuzione di uno script in ogni partizione di una mappa partizioni di scalabilità elastica.

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

## <a name="to-execute-a-job"></a>Per eseguire un processo
Questo script di PowerShell esegue un processo esistente:

Aggiornare la variabile seguente per riflettere il nome del processo desiderato da eseguire:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution

## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Per recuperare lo stato di esecuzione di un singolo processo
Usare il [**cmdlet Get-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/get-azuresqljobexecution) e impostare il parametro **JobExecutionId** per visualizzare lo stato di esecuzione del processo.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Usare lo stesso cmdlet **Get-AzureSqlJobExecution** con il parametro **IncludeChildren** per visualizzare lo stato delle esecuzioni del processo figlio, ovvero lo stato specifico per ogni esecuzione del processo in ogni database di destinazione del processo.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Per visualizzare lo stato di più esecuzioni del processo
Il [**cmdlet Get-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/new-azuresqljob) ha più parametri facoltativi che possono essere usati per visualizzare più esecuzioni di processo, filtrate tramite i parametri forniti. Di seguito vengono illustrati alcuni dei possibili modi per utilizzare Get-AzureSqlJobExecution:

Recuperare tutte le esecuzioni attive di processo di primo livello:

    Get-AzureSqlJobExecution

Recuperare tutte le esecuzioni di processo di primo livello, incluse le esecuzioni di processo inattive:

    Get-AzureSqlJobExecution -IncludeInactive

Recuperare tutte le esecuzioni di processo figlio di un ID di esecuzione processo fornito, incluse le esecuzioni di processo inattive:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren

Recuperare tutte le esecuzioni di processo create utilizzando una pianificazione / processo di combinazione, inclusi i processi inattivi:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Recuperare tutti i processi destinati a una mappa di partizione specificata, inclusi i processi inattivi:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Recuperare tutti i processi destinati a una raccolta personalizzata specificata, inclusi i processi inattivi:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Recuperare l'elenco delle esecuzioni delle attività di processo in una esecuzione di processo specifica:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Recuperare i dettagli di esecuzione delle attività di processo:

Il seguente script PowerShell può essere utilizzato per visualizzare i dettagli di un'esecuzione delle attività di processo, che è particolarmente utile durante il debug degli errori di esecuzione.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="to-retrieve-failures-within-job-task-executions"></a>Per recuperare gli errori nelle esecuzioni delle attività di processo
L'oggetto **JobTaskExecution** include una proprietà per il ciclo di vita dell'attività insieme a una proprietà del messaggio. Se l'esecuzione di un'attività di processo non riesce, la proprietà del ciclo di vita verrà impostata su *Non riuscita* e la proprietà del messaggio verrà impostata sul messaggio di eccezione risultante e il relativo stack. Se un processo ha esito negativo, è importante visualizzare i dettagli delle attività di processo che non sono riuscite per un determinato processo.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>Per attendere il completamento dell'esecuzione del processo
Il seguente script PowerShell può essere utilizzato per attendere che un’attività di processo venga completata: 

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Creare un criterio di esecuzione personalizzata
I processi di database elastici supportano la creazione di criteri di esecuzione personalizzati che possono essere applicati all'avvio dei processi.

Criteri di esecuzione che attualmente consentono la definizione di:

* Nome: Identificatore del criterio di esecuzione.
* Timeout del processo: tempo totale prima che un processo venga annullato dai processi di database elastici.
* Intervallo tra tentativi iniziale: intervallo di attesa prima del primo tentativo.
* Intervallo massimo di tentativi: estremità degli intervalli tra i tentativi da utilizzare.
* Coefficiente di backoff dell’intervallo tra tentativi: coefficiente utilizzato per calcolare l’intervallo successivo tra i tentativi.  Viene utilizzata la seguente formula: (Intervallo tentativi iniziale) * Math.pow((Coefficiente di backoff dell’intervallo), (Numero di tentativi) - 2). 
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
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Aggiornare il criterio di esecuzione personalizzato
Aggiornare l'aggiornamento del criterio di esecuzione desiderato:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy

## <a name="cancel-a-job"></a>Annullare un processo
I processi di database elastici supportano le richieste di annullamento dei processi.  Se i processi di database elastici rilevano una richiesta di annullamento per un processo in fase di esecuzione, verrà effettuato un tentativo di arresto del processo.

E’ possibile cancellare un processo in due modi diversi tramite i processi di database elastici:

1. Annullare le attività attualmente in esecuzione: se viene rilevato un annullamento mentre un'attività è in esecuzione, l'annullamento verrà eseguito nell'aspetto dell'attività attualmente in esecuzione.  Ad esempio: se viene eseguita una query con esecuzione prolungata quando si tenta di eseguire un annullamento, si verificherà un tentativo di annullare la query.
2. Annullare i tentativi dell'attività: se viene rilevato un annullamento dal thread di controllo prima che venga avviata un'attività per l'esecuzione, il thread di controllo eviterà di avviare l'attività e dichiarerà annullata la richiesta.

Se viene richiesto un annullamento del processo per un processo padre, tale richiesta verrà rispettata per il processo padre e per tutti i relativi processi figlio.

Per inviare una richiesta di annullamento, usare il [**cmdlet Stop-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) e impostare il parametro **JobExecutionId**.

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Per eliminare un processo e la relativa cronologia in modo asincrono
I processi di database elastici supportano l'eliminazione asincrona dei processi. Un processo può essere contrassegnato per l'eliminazione e il sistema lo eliminerà con tutta la relativa cronologia dopo il completamento di tutte le esecuzioni di processo per tale processo. Il sistema non annullerà automaticamente le esecuzioni di processo attive.  

Richiamare [**Stop-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) per annullare le esecuzioni di processo attive.

Per attivare l'eliminazione di processi, usare il [**cmdlet Remove-AzureSqlJob**](/powershell/module/elasticdatabasejobs/remove-azuresqljob) e impostare il parametro **JobName**.

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName

## <a name="to-create-a-custom-database-target"></a>Per creare una destinazione di database personalizzata
È possibile definire destinazioni di database personalizzate per l'esecuzione diretta o per l'inclusione in un gruppo di database personalizzato. Ad esempio, poiché i **pool elastici** non sono ancora supportati direttamente se si usano le API di PowerShell, è possibile creare una destinazione di database personalizzata e una destinazione della raccolta di database personalizzata che comprenda tutti i database nel pool.

Impostare le seguenti variabili in modo da riflettere le informazioni desiderate sul database:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Per creare una destinazione per la raccolta di database personalizzata
Usare il cmdlet [**New-AzureSqlJobTarget**](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) per definire una destinazione per la raccolta di database personalizzata per abilitare l'esecuzione in più destinazioni di database definite. Dopo aver creato un gruppo di database, è possibile associarli alla destinazione della raccolta personalizzata.

Impostare le seguenti variabili in modo da riflettere la configurazione della destinazione della raccolta personalizzata desiderata:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Per aggiungere database a una destinazione della raccolta di database personalizzata
Per aggiungere un database a una raccolta personalizzata specifica, usare il cmdlet [**Add-AzureSqlJobChildTarget**](/powershell/module/elasticdatabasejobs/add-azuresqljobchildtarget).

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Verificare i database in una destinazione per la raccolta dei database personalizzata
Usare il cmdlet [**Get-AzureSqlJobTarget**](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) per recuperare i database figlio all'interno di una destinazione di una raccolta database personalizzata. 

    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Creare un processo per eseguire uno script in una destinazione di una raccolta database personalizzata
Usare il cmdlet [**New AzureSqlJob**](/powershell/module/elasticdatabasejobs/new-azuresqljob) per creare un processo rispetto a un gruppo di database definiti da una destinazione della raccolta di database personalizzata. I processi di database elastici espanderanno il processo in più processi figlio, ognuno corrispondente a un database associato alla destinazione di raccolta database personalizzata e eseguiranno lo script in tutti i database. Anche in questo caso, è importante che gli script siano idempotenti per essere flessibili ai tentativi.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Raccolta dei dati tra database
È possibile usare un processo per eseguire una query su un gruppo di database e inviare i risultati a una tabella specifica. E’ possibile eseguire una query sulla tabella dopo aver visualizzato i risultati della query da ciascun database. In questo modo si avrà un metodo asincrono per eseguire una query su molti database. I tentativi non riusciti vengono gestiti automaticamente tramite la ripetizione dei tentativi.

La tabella di destinazione specificata verrà creata automaticamente, se non esiste già. La nuova tabella corrisponde allo schema del set di risultati restituito. Se uno script restituisce più set di risultati, i processi di database elastici invieranno solo il primo set alla tabella di destinazione.

Lo script di PowerShell seguente esegue uno script e raccoglie i risultati in una tabella specificata. Questo script presuppone che sia stato creato uno script T-SQL che restituisce un singolo set di risultati e che sia stata creata una destinazione della raccolta di database personalizzata.

Questo script usa il cmdlet [**Get-AzureSqlJobTarget**](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget). Impostare i parametri per lo script, le credenziali e la destinazione di esecuzione:

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

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Per creare e avviare un processo per gli scenari di raccolta dati
Questo script usa il cmdlet [**Start-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/start-azuresqljobexecution).

    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>Per pianificare un trigger di esecuzione del processo
Lo script di PowerShell seguente può essere usato per creare una pianificazione ricorrente. Questo script usa l'intervallo di minuti, ma [**New-AzureSqlJobSchedule**](/powershell/module/elasticdatabasejobs/new-azuresqljobschedule) supporta anche i parametri -DayInterval, -HourInterval, -MonthInterval e -WeekInterval. Le pianificazioni che vengono eseguite una sola volta possono essere create specificando -OneTime.

Creare una nuova pianificazione:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Per attivare l'esecuzione di un processo in una pianificazione temporale
È possibile definire un trigger di processo per eseguire un processo in base a una pianificazione temporale. Il seguente script di PowerShell può essere utilizzato per creare un trigger di processo.

Usare [New-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/new-azuresqljobtrigger) e impostare le variabili seguenti in modo che corrispondano al processo e alla pianificazione desiderati:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    -JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Per rimuovere un'associazione pianificata per arrestare l'esecuzione di un processo in base a una pianificazione
Per sospendere l'esecuzione del processo ricorrente tramite un trigger di processo, è possibile rimuovere il trigger di processo. Rimuovere un trigger di processo per arrestare l'esecuzione di un processo in base a una pianificazione mediante il [**cmdlet Remove-AzureSqlJobTrigger**](/powershell/module/elasticdatabasejobs/remove-azuresqljobtrigger).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Recuperare i trigger di processo associati a una pianificazione temporale
Il seguente script PowerShell è utilizzabile per ottenere e visualizzare i trigger di processo registrati in una particolare pianificazione temporale.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Per recuperare i trigger di processo associati a un processo
Usare [Get AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/get-azuresqljobtrigger) per ottenere e visualizzare le pianificazioni che contengono un processo registrato.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Per creare un'applicazione livello dati (DACPAC) per l'esecuzione sui database
Per creare un'applicazione DACPAC, vedere [Applicazioni livello dati](https://msdn.microsoft.com/library/ee210546.aspx). Per distribuire un'applicazione DACPAC, usare il [cmdlet New-AzureSqlJobContent](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent). L'applicazione DACPAC deve essere accessibile al servizio. È consigliabile caricare nell'archiviazione di Azure un'applicazione DACPAC creata e creare una [Firma di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md) per DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Per aggiornare un'applicazione livello dati (DACPAC) per l'esecuzione nei database
I DACPAC esistenti registrati all’interno dei processi di database elastici possono essere aggiornati in modo da fare riferimento ai nuovo URI. Usare il [**cmdlet Set-AzureSqlJobContentDefinition**](/powershell/module/elasticdatabasejobs/set-azuresqljobcontentdefinition) per aggiornare l'URI DACPAC in una DACPAC registrata esistente:

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Per creare un processo per applicare un'applicazione livello dati (DACPAC) nei database
Dopo aver creato un DACPAC all'interno di processi di database elastici, è possibile creare un processo per applicare il DACPAC su un gruppo di database. Utilizzare il seguente script di PowerShell per creare un processo DACPAC su una raccolta personalizzata di database:

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->

