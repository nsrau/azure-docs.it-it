---
title: Codice del file evento XEvent per il database SQL | Documentazione Microsoft
description: "Fornisce PowerShell e Transact-SQL per un esempio di codice in due fasi che illustra la destinazione del file evento in un evento esteso in Azure SQL Database. Archiviazione di Azure è una parte necessaria di questo scenario."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
tags: 
ms.assetid: bbb10ecc-739f-4159-b844-12b4be161231
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: genemi
ms.openlocfilehash: abf660e3fafd1a5020cdf9a6beb5b73252b72cfc
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Codice di destinazione del file evento per eventi estesi nel database SQL

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Si desidera un esempio di codice completo per un modo affidabile per acquisire e segnalare informazioni per un evento esteso.

In Microsoft SQL Server la [destinazione del file evento](http://msdn.microsoft.com/library/ff878115.aspx) viene utilizzata per archiviare l'output di eventi in un file di disco rigido locale. Tuttavia, tali file non sono disponibili per il Azure SQL Database. Invece, utilizziamo il servizio Archiviazione di Azure per supportare la destinazione del file evento.

Questo argomento presenta un esempio di codice in due fasi:

* PowerShell, per creare un contenitore di Archiviazione di Azure nel cloud.
* Transact-SQL:
  
  * per assegnare il contenitore di Archiviazione di Azure a una destinazione del file evento.
  * Per creare e avviare la sessione dell'evento e così via.

## <a name="prerequisites"></a>prerequisiti

* Un account e una sottoscrizione di Azure. È possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Qualsiasi database in cui è possibile creare una tabella.
  
  * Facoltativamente, è possibile [creare un database dimostrativo **AdventureWorksLT**](sql-database-get-started.md) in pochi minuti.
* SQL Server Management Studio (ssms.exe), idealmente l'ultima versione di aggiornamento mensile. 
  È possibile scaricare la versione più recente di ssms.exe da:
  
  * Argomento intitolato [SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
  * [Un collegamento diretto al download.](http://go.microsoft.com/fwlink/?linkid=616025)
* È necessario che i [moduli di Azure PowerShell](http://go.microsoft.com/?linkid=9811175) siano installati.
  
  * I moduli forniscono comandi come **New-AzureStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fase 1: Codice di PowerShell per il contenitore di archiviazione di Azure

Questo PowerShell è la fase 1 dell'esempio di codice in due fasi.

Lo script inizia con comandi di pulitura dopo un'eventuale esecuzione precedente ed è eseguibile di nuovo.

1. Incollare lo script di PowerShell in un editor di testo semplice, ad esempio Notepad.exe e salvare lo script come file con estensione **.ps1**.
2. Avviare PowerShell ISE come amministratore.
3. Al prompt dei comandi, digitare<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>e quindi premere INVIO.
4. In PowerShell ISE, aprire il file **.ps1** . Eseguire lo script.
5. Innanzitutto, lo script avvia una nuova finestra in cui si accede ad Azure.
   
   * Se si esegue nuovamente lo script senza interrompere la sessione, è possibile pratico decommentare il comando **Add-AzureAccount** .

![PowerShell ISE, con il modulo Azure installato, pronto per l'esecuzione di script.][30_powershell_ise]

### <a name="powershell-code"></a>Codice PowerShell

Questo script di PowerShell presuppone che si sia già eseguito il cmdlet Import-Module per il modulo AzureRm. Per la documentazione di riferimento, vedere [PowerShell Module Browser](https://docs.microsoft.com/powershell/module/).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Add-AzureRmAccount (or  Login-AzureRmAccount), just one time.';
#Add-AzureRmAccount;   # Same as  Login-AzureRmAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'West US';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.';

Select-AzureRmSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.';

If ($storageAccountName)
{
    Remove-AzureRmStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzureRmStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzureRmStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
Catch 
{
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzureRmStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```


Prendere nota dei valori nominati che lo script di PowerShell stampa alla fine. È necessario modificare tali valori nello script Transact-SQL che segue come fase 2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fase 2: Codice Transact-SQL che utilizza il contenitore di Archiviazione di Azure

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


> [!WARNING]
> Il valore della chiave di firma di accesso condiviso generata dallo script di PowerShell precedente potrebbe iniziare con un "?" (punto interrogativo). Quando si usa la chiave di firma di accesso condiviso nello script T-SQL seguente, è necessario *rimuovere il prefisso "?"*. Le attività in caso contrario potrebbero essere bloccate dalla protezione.


### <a name="transact-sql-code"></a>Codice Transact-SQL

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
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


Se la destinazione non può essere collegata durante l’esecuzione, è necessario arrestare e riavviare la sessione dell'evento:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


## <a name="output"></a>Output

Al termine dell'esecuzione dello script Transact-SQL, fare clic su una cella sotto l'intestazione della colonna **event_data_XML**. Viene visualizzato un elemento **<event>** che mostra un'istruzione UPDATE.

Di seguito è riportato un elemento **<event>** generato durante il test:


```xml
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


Lo script Transact-SQL precedente ha usato la funzione di sistema seguente per leggere l'event_file:

* [sys.fn_xe_file_target_read_file (Transact-SQL)](http://msdn.microsoft.com/library/cc280743.aspx)

Le opzioni avanzate per la visualizzazione di dati da eventi estesi sono illustrate all'indirizzo:

* [Visualizzazione avanzata dei dati di destinazione da eventi estesi](http://msdn.microsoft.com/library/mt752502.aspx)


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Conversione dell’esempio di codice da eseguire in SQL Server

Si supponga di voler eseguire l'esempio di Transact-SQL precedente in Microsoft SQL Server.

* Per semplicità, si desidera sostituire completamente l'uso del contenitore di Archiviazione di Azure con un semplice file, come **C:\myeventdata.xel**. Il file verrebbe scritto sul disco rigido locale del computer che ospita SQL Server.
* Non è necessaria alcuna tipologia di istruzioni di Transact-SQL per **CREATE MASTER KEY** e **CREATE CREDENTIAL**.
* Nell'istruzione **CREATE EVENT SESSION**, nella relativa clausola **ADD TARGET**, sostituire il valore di Http assegnato a **filename =** con una stringa di percorso completo **C:\myfile.xel**.
  
  * Nessun account di Archiviazione di Azure deve essere coinvolto.

## <a name="more-information"></a>Altre informazioni

Per ulteriori informazioni sugli account e i contenitori nel servizio Archiviazione di Azure, vedere:

* [Come usare l'archiviazione BLOB da .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Denominazione e riferimento a contenitori, BLOB e metadati](http://msdn.microsoft.com/library/azure/dd135715.aspx)
* [Lavorare con il contenitore radice](http://msdn.microsoft.com/library/azure/ee395424.aspx)
* [Lezione 1: Creare criteri di accesso archiviati e la firma di accesso condiviso in un contenitore di Azure](http://msdn.microsoft.com/library/dn466430.aspx)
  * [Lezione 2: Creare una credenziale di SQL Server usando una firma di accesso condiviso](http://msdn.microsoft.com/library/dn466435.aspx)
* [Eventi estesi per Microsoft SQL Server](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

