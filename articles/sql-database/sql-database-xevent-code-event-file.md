---
title: Codice del file evento XEvent per il database SQL | Microsoft Docs
description: Fornisce PowerShell e Transact-SQL per un esempio di codice in due fasi che illustra la destinazione del file evento in un evento esteso in Azure SQL Database. Archiviazione di Azure è una parte necessaria di questo scenario.
services: sql-database
documentationcenter: ''
author: MightyPen
manager: jhubbard
editor: ''
tags: ''

ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2016
ms.author: genemi

---
# Codice di destinazione del file evento per eventi estesi nel database SQL
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Si desidera un esempio di codice completo per un modo affidabile per acquisire e segnalare informazioni per un evento esteso.

In Microsoft SQL Server la [destinazione del file evento](http://msdn.microsoft.com/library/ff878115.aspx) viene utilizzata per archiviare l'output di eventi in un file di disco rigido locale. Tuttavia, tali file non sono disponibili per il Azure SQL Database. Invece, utilizziamo il servizio Archiviazione di Azure per supportare la destinazione del file evento.

Questo argomento presenta un esempio di codice in due fasi:

* PowerShell, per creare un contenitore di Archiviazione di Azure nel cloud.
* Transact-SQL:
  
  * per assegnare il contenitore di Archiviazione di Azure a una destinazione del file evento.
  * Per creare e avviare la sessione dell'evento e così via.

## Prerequisiti
* Un account e una sottoscrizione di Azure. È possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Qualsiasi database in cui è possibile creare una tabella.
  
  * Facoltativamente, è possibile [creare un database dimostrativo **AdventureWorksLT**](sql-database-get-started.md) in pochi minuti.
* SQL Server Management Studio (ssms.exe), idealmente l'ultima versione di aggiornamento mensile. È possibile scaricare la versione più recente di ssms.exe da:
  
  * Argomento intitolato [SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
  * [Un collegamento diretto al download.](http://go.microsoft.com/fwlink/?linkid=616025)
* È necessario che i [moduli di Azure PowerShell](http://go.microsoft.com/?linkid=9811175) siano installati.
  
  * I moduli forniscono comandi come **New-AzureStorageAccount**.

## Fase 1: Codice di PowerShell per il contenitore di archiviazione di Azure
Questo PowerShell è la fase 1 dell'esempio di codice in due fasi.

Lo script inizia con comandi di pulitura dopo un'eventuale esecuzione precedente ed è eseguibile di nuovo.

1. Incollare lo script di PowerShell in un editor di testo semplice, ad esempio Notepad.exe e salvare lo script come file con estensione **.ps1**.
2. Avviare PowerShell ISE come amministratore.
3. Al prompt dei comandi, digitare<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>e quindi premere INVIO.
4. In PowerShell ISE, aprire il file **.ps1**. Eseguire lo script.
5. Innanzitutto, lo script avvia una nuova finestra in cui si accede ad Azure.
   
   * Se si esegue nuovamente lo script senza interrompere la sessione, è possibile pratico decommentare il comando **Add-AzureAccount**.

![PowerShell ISE, con il modulo Azure installato, pronto per l'esecuzione di script.][30_powershell_ise]

&nbsp;

```
## TODO: Before running, find all 'TODO' and make each edit!!

#--------------- 1 -----------------------


# You can comment out or skip this Add-AzureAccount
# command after the first run.
# Current PowerShell environment retains the successful outcome.

'Expect a pop-up window in which you log in to Azure.'


Add-AzureAccount

#-------------- 2 ------------------------


'
TODO: Edit the values assigned to these variables, especially the first few!
'

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME'
$policySasExpiryTime = '2016-01-28T23:44:56Z'
$policySasStartTime  = '2015-08-01'


$storageAccountName     = 'gmstorageaccountxevent'
$storageAccountLocation = 'West US'
$contextName            = 'gmcontext'
$containerName          = 'gmcontainerxevent'
$policySasToken         = 'gmpolicysastoken'


# Leave this value alone, as 'rwl'.
$policySasPermission = 'rwl'

#--------------- 3 -----------------------


# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.'


Select-AzureSubscription -SubscriptionName $subscriptionName


#-------------- 4 ------------------------


'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.'


If ($storageAccountName)
{
    Remove-AzureStorageAccount -StorageAccountName $storageAccountName
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString()

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...'

New-AzureStorageAccount `
    -StorageAccountName $storageAccountName `
    -Location           $storageAccountLocation

[System.DateTime]::Now.ToString()

[System.Media.SystemSounds]::Beep.Play()


'
Get the primary access key for your storage account.
'


$primaryAccessKey_ForStorageAccount = `
    (Get-AzureStorageKey `
        -StorageAccountName $storageAccountName).Primary

"`$primaryAccessKey_ForStorageAccount = $primaryAccessKey_ForStorageAccount"

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
'

#--------------- 6 -----------------------


# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
'

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $primaryAccessKey_ForStorageAccount


'Create a container within the storage account.
'


$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context


'Create a security policy to be applied to the SAS token.
'

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime 

'
Generate a SAS token for the container.
'
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken
}
Catch 
{
    $Error[0].Exception.ToString()
}

#-------------- 7 ------------------------


'Display the values that YOU must edit into the Transact-SQL script next!:
'

"storageAccountName: $storageAccountName"
"containerName:      $containerName"
"sasTokenWithPolicy: $sasTokenWithPolicy"

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
'

'
(Later, return here to delete your Azure Storage account. See the preceding - Remove-AzureStorageAccount -StorageAccountName $storageAccountName)'

'
Now shift to the Transact-SQL portion of the two-part code sample!'

# EOFile
```


&nbsp;

Prendere nota dei valori nominati che lo script di PowerShell stampa alla fine. È necessario modificare tali valori nello script Transact-SQL che segue come fase 2.

## Fase 2: Codice Transact-SQL che utilizza il contenitore di Archiviazione di Azure
* Nella fase 1 di questo esempio di codice è stato eseguito uno script di PowerShell per creare un contenitore di Archiviazione di Azure.
* Successivamente nella fase 2, lo script Transact-SQL deve utilizzare il contenitore.

Lo script inizia con comandi di pulitura dopo un'eventuale esecuzione precedente ed è eseguibile di nuovo.

Lo script di PowerShell stampa alcuni valori denominati quando è terminato. È necessario modificare lo script di Transact-SQL per utilizzare tali valori. Trovare **TODO** nello script di Transact-SQL per individuare i punti di modifica.

1. Aprire SQL Server Management Studio (ssms.exe).
2. Connettersi al database di Azure SQL Database
3. Fare clic per aprire un nuovo riquadro di query.
4. Incollare il seguente script di Transact-SQL nel riquadro della query.
5. Trovare ogni **TODO** nello script e apportare le modifiche appropriate.
6. Salvare e quindi eseguire lo script.

&nbsp;

> [!WARNING]
> Il valore della chiave di firma di accesso condiviso generata dallo script di PowerShell precedente potrebbe iniziare con un "?" (punto interrogativo). Quando si usa la chiave di firma di accesso condiviso nello script T-SQL seguente, è necessario *rimuovere il prefisso "?"*. Le attività in caso contrario potrebbero essere bloccate dalla protezione.
> 
> 

&nbsp;

```
---- TODO: First, run the PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;
GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO


-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


&nbsp;

Se la destinazione non può essere collegata durante l’esecuzione, è necessario arrestare e riavviare la sessione dell'evento:

```
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


&nbsp;

## Output
Al termine dell'esecuzione dello script di Transact-SQL, fare clic su una cella sotto l’intestazione della colonna **event\_data\_XML**. Viene visualizzato un elemento **<event>** che mostra un'istruzione UPDATE.

Di seguito è riportato un elemento **<event>** generato durante il test:

& nbsp;

```
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

&nbsp;

Lo script Transact-SQL precedente ha usato la funzione di sistema seguente per leggere l'event\_file:

* [sys.fn\_xe\_file\_target\_read\_file (Transact-SQL)](http://msdn.microsoft.com/library/cc280743.aspx)

Le opzioni avanzate per la visualizzazione di dati da eventi estesi sono illustrate all'indirizzo:

* [Visualizzazione avanzata dei dati di destinazione da eventi estesi](http://msdn.microsoft.com/library/mt752502.aspx)

&nbsp;

## Conversione dell’esempio di codice da eseguire in SQL Server
Si supponga di voler eseguire l'esempio di Transact-SQL precedente in Microsoft SQL Server.

* Per semplicità si desidera sostituire completamente l'utilizzo del contenitore di Archiviazione di Azure con un semplice file, come **C:\\myeventdata.xel**. Il file verrebbe scritto sul disco rigido locale del computer che ospita SQL Server.
* Non è necessaria alcuna tipologia di istruzioni di Transact-SQL per **CREATE MASTER KEY** e **CREATE CREDENTIAL**.
* Nell’istruzione **CREATE EVENT SESSION**, nella relativa clausola **ADD TARGET**, sostituire il valore di Http assegnato a **filename =** con una stringa di percorso completo **C:\\myfile.xel**.
  
  * Nessun account di Archiviazione di Azure deve essere coinvolto.

## Altre informazioni
Per ulteriori informazioni sugli account e i contenitori nel servizio Archiviazione di Azure, vedere:

* [Come usare l'archiviazione BLOB da .NET](../storage/storage-dotnet-how-to-use-blobs.md)
* [Denominazione e riferimento a contenitori, BLOB e metadati](http://msdn.microsoft.com/library/azure/dd135715.aspx)
* [Lavorare con il contenitore radice](http://msdn.microsoft.com/library/azure/ee395424.aspx)
* [Lezione 1: Creare criteri di accesso archiviati e la firma di accesso condiviso in un contenitore di Azure](http://msdn.microsoft.com/library/dn466430.aspx)
  * [Lezione 2: Creare una credenziale di SQL Server usando una firma di accesso condiviso](http://msdn.microsoft.com/library/dn466435.aspx)

<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

<!---HONumber=AcomDC_0824_2016-->