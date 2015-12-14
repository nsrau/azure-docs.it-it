<properties 
    pageTitle="Importare un file BACPAC per creare un nuovo database di SQL Azure tramite PowerShell" 
    description="Importare un file BACPAC per creare un nuovo database di SQL Azure tramite PowerShell" 
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
    ms.date="12/01/2015"
    ms.author="sstein"/>

# Importare un file BACPAC per creare un nuovo database di SQL Azure tramite PowerShell

**Database singolo**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)


In questo articolo vengono fornite istruzioni per la creazione di un database SQL Azure importando un file BACPAC con PowerShell.

Un BACPAC è un file con estensione bacpac che contiene lo schema del database e dati. Per ulteriori informazioni, vedere il pacchetto di Backup (con estensione bacpac) in [Applicazioni livello dati](https://msdn.microsoft.com/library/ee210546.aspx).

Il database viene creato da un file BACPAC importato da un contenitore di BLOB di archiviazione di Azure. Se non si dispone di un file con estensione bacpac nell'archiviazione di Azure è possibile crearne uno seguendo i passaggi in [Creazione ed esportazione di un file BACPAC di un Database SQL di Azure](sql-database-export-powershell.md).

> [AZURE.NOTE]Il database SQL di Azure crea automaticamente e conserva i backup per ogni database dell’utente che è possibile ripristinare. Per ulteriori informazioni, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).


Per importare un database SQL è necessario quanto segue:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Un file con estensione bacpac (BACPAC) del database che si desidera ripristinare. Il file BACPAC deve essere inserito in un contenitore BLOB di un[account di archiviazione di Azure (classico)](storage-create-storage-account.md).


> [AZURE.IMPORTANT]Questo articolo contiene comandi per le versioni di Azure PowerShell fino alle versione 1.0 *esclusa* e versioni successive. È possibile controllare la versione di Azure PowerShell con il comando **Get-Module azure | format-table version**.



## Configurare le credenziali e selezionare la sottoscrizione

È innanzitutto necessario stabilire l'accesso all'account Azure, poi avviare PowerShell ed eseguire il cmdlet seguente. Nella schermata di accesso utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.

	Add-AzureAccount

Dopo aver effettuato l'accesso, sullo schermo verranno visualizzate informazioni tra cui l'ID usato per l'accesso con le sottoscrizioni di Azure per le quali si dispone dell'accesso.


### Selezionare la sottoscrizione ad Azure

Per selezionare la sottoscrizione, è necessario il relativo ID. che può essere copiato dal passaggio precedente o, se si dispone di più sottoscrizioni e si necessita di maggiori informazioni, è possibile eseguire il cmdlet **Get-AzureSubscription** e copiare le informazioni di sottoscrizione desiderate dal set di risultati. Una volta acquisito l’ID della sottoscrizione, eseguire il cmdlet seguente:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Dopo aver eseguito correttamente il cmdlet **Select-AzureSubscription**, verrà nuovamente visualizzato il prompt dei comandi di PowerShell. Se si dispone di più di una sottoscrizione, è possibile eseguire il cmdlet **Get-AzureSubscription** e verificare che nella sottoscrizione che si desidera usare sia visualizzato **IsCurrent : True**.


## Impostare le variabili per l'ambiente specifico

Esistono alcune variabili in cui è necessario sostituire i valori di esempio con i valori specifici per il database e l'account di archiviazione.

Il nome del server deve essere un server che è attualmente presente nella sottoscrizione selezionata nel passaggio precedente ed è il server in cui si vuole creare il database.

Il nome del database è il nome desiderato per il nuovo database.

    $ServerName = "servername"
    $DatabaseName = "databasename"


Le seguenti variabili sono dell'account di archiviazione in cui si trova il file BACPAC. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione per ottenere questi valori. È possibile trovare la chiave di accesso primaria facendo clic su **Tutte le impostazioni** e su **Chiavi** dal pannello dell'account di archiviazione.

Il nome del blob è il nome di un file con estensione bacpac esistente da cui si desidera creare il database. È necessario includere l'estensione bacpac.

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"

## Creare il puntatore per il proprio server e account di archiviazione

Eseguendo il cmdlet **Get-Credential** si apre una finestra in cui vengono chiesti nome utente e password. Immettere l'account di accesso di amministrazione e la password per il server SQL in cui si desidera creare il database ($ServerName dall'alto) e non il nome utente e la password dell'account Azure.

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## Importare il database

Tale comando invia una richiesta di importazione del database al servizio. A seconda delle dimensioni del database, l'operazione di esportazione potrebbe richiedere diverso tempo.

    $importRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## Monitorare lo stato dell’operazione

Dopo aver eseguito **Start-AzureSqlDatabaseImport** è possibile controllare lo stato della richiesta.

L’esecuzione di tale comando immediatamente dopo la richiesta in genere restituisce lo status **In sospeso** o **In esecuzione**. Eseguire il comando più volte fino a che non si visualizza **Stato: completato** nell'output.

L’esecuzione di questo comando richiede una password. Immettere l’account di accesso e la password dell’amministratore per il server SQL.


    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
 


## Creare uno script di ripristino di PowerShell per database SQL


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "nameofnewdatabase"

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $ImportRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    

## Passaggi successivi

- [Connettersi con SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)




## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Esercitazioni di ripristino di emergenza](sql-database-disaster-recovery-drills.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1203_2015-->