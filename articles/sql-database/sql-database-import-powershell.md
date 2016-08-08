<properties 
    pageTitle="Importare un file BACPAC per creare un nuovo database SQL di Azure tramite PowerShell | Microsoft Azure" 
    description="Importare un file BACPAC per creare un nuovo database di SQL Azure tramite PowerShell" 
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
    ms.date="07/06/2016"
    ms.author="sstein"/>

# Importare un file BACPAC per creare un nuovo database di SQL Azure tramite PowerShell

**Database singolo**

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

In questo articolo vengono fornite istruzioni per la creazione di un database SQL Azure importando un file BACPAC con PowerShell.

Un BACPAC è un file con estensione bacpac che contiene lo schema del database e dati. Per ulteriori informazioni, vedere il pacchetto di Backup (con estensione bacpac) in [Applicazioni livello dati](https://msdn.microsoft.com/library/ee210546.aspx).

Il database viene creato da un file BACPAC importato da un contenitore di BLOB di archiviazione di Azure. Se non si dispone di un file con estensione .bacpac nell'archiviazione di Azure è possibile crearne uno seguendo i passaggi in [Archiviare un database SQL di Azure come file BACPAC tramite PowerShell](sql-database-export-powershell.md).

> [AZURE.NOTE] Il database SQL di Azure crea automaticamente e conserva i backup per ogni database dell'utente che è possibile ripristinare. Per conoscere i dettagli, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).


Per importare un database SQL è necessario quanto segue:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Un file con estensione bacpac (BACPAC) del database che si vuole importare. Il file BACPAC deve essere inserito in un contenitore BLOB di un[account di archiviazione di Azure (classico)](../storage/storage-create-storage-account.md).



[AZURE.INCLUDE [Avviare la sessione di PowerShell](../../includes/sql-database-powershell.md)]



## Impostare le variabili per l'ambiente specifico

Esistono alcune variabili in cui è necessario sostituire i valori di esempio con i valori specifici per il database e l'account di archiviazione.

Il nome del server deve essere un server che è attualmente presente nella sottoscrizione selezionata nel passaggio precedente ed è il server in cui si vuole creare il database. Si noti che non è supportata l'importazione di un database direttamente in un pool elastico, ma è possibile importarlo prima in un database singolo e poi spostare il database in un pool.

Il nome del database è il nome desiderato per il nuovo database.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


Le seguenti variabili sono dell'account di archiviazione in cui si trova il file BACPAC. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione per ottenere questi valori. È possibile trovare la chiave di accesso primaria facendo clic su **Tutte le impostazioni** e su **Chiavi** dal pannello dell'account di archiviazione.

Il nome del blob è il nome di un file con estensione bacpac esistente da cui si desidera creare il database. È necessario includere l'estensione bacpac.

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


Eseguendo il cmdlet **Get-Credential** si apre una finestra in cui vengono chiesti nome utente e password. Immettere l'account di accesso di amministrazione e la password per il server di database SQL ($ServerName sopra) e non il nome utente e la password dell'account Azure.

    $credential = Get-Credential


## Importare il database

Tale comando invia una richiesta di importazione del database al servizio. A seconda delle dimensioni del database, l'operazione di esportazione potrebbe richiedere diverso tempo.

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMxSize 50000
    

## Monitorare lo stato dell’operazione

Dopo aver eseguito **New-AzureRmSqlDatabaseImport** è possibile controllare lo stato della richiesta.

L’esecuzione di tale comando immediatamente dopo la richiesta in genere restituisce lo status **In sospeso** o **In esecuzione**. Eseguire il comando più volte fino a che non si visualizza **Stato: completato** nell'output.

L’esecuzione di questo comando richiede una password. Immettere l’account di accesso e la password dell’amministratore per il server SQL.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest .OperationStatusLink
 


## Script di importazione di PowerShell per il database SQL


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey  StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMxSize 50000
 
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest .OperationStatusLink

    

## Passaggi successivi

- Per informazioni su come connettersi ed eseguire query su un database SQL importato, vedere [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)

<!---HONumber=AcomDC_0727_2016-->