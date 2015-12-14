<properties 
    pageTitle="Creare ed esportare un file BACPAC di un database SQL di Azure tramite PowerShell" 
    description="Creare ed esportare un file BACPAC di un database SQL di Azure tramite PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="12/01/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Creare ed esportare un file BACPAC di un database SQL di Azure tramite PowerShell

**Database singolo**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


In questo articolo vengono fornite istruzioni per l'esportazione di un file BACPAC del database SQL Azure con PowerShell.

Un [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) è un file con estensione .bacpac che contiene lo schema del database e i dati. L'utilizzo primario per un file BACPAC è quello di spostare un database da un server all’altro, a [migrazione di un database locale nel cloud](sql-database-cloud-migrate.md), e di archiviare un database esistente in un formato aperto.

> [AZURE.NOTE]I BACPAC non sono destinati a essere utilizzati per il backup e le operazioni di ripristino. Il database SQL di Azure crea automaticamente i backup per ogni database dell’utente. Per ulteriori informazioni, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).


Il file BACPAC viene esportato in un contenitore di BLOB di archiviazione di Azure che è possibile scaricare una volta che l'operazione è stata completata correttamente.


Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Database SQL di Azure. Se non si dispone di un database SQL, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).
- Un [account di archiviazione Azure](storage-create-storage-account.md) con un contenitore di BLOB per archiviare il BACPAC. Attualmente l'account di archiviazione deve utilizzare il modello di distribuzione classica, quindi selezionate **Classico** durante la creazione di un account di archiviazione.
- Azure PowerShell. Per scaricare e installare i moduli Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).



## Configurare le credenziali e selezionare la sottoscrizione

È innanzitutto necessario stabilire l'accesso all'account Azure, poi avviare PowerShell ed eseguire il cmdlet seguente. Nella schermata di accesso utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.

	Add-AzureAccount

Dopo aver effettuato l'accesso, sullo schermo verranno visualizzate informazioni tra cui l'ID usato per l'accesso con le sottoscrizioni di Azure per le quali si dispone dell'accesso.


### Selezionare la sottoscrizione ad Azure

Per selezionare la sottoscrizione, è necessario l’ID sottoscrizione o il nome della sottoscrizione (**-SubscriptionName**) È possibile copiare l'ID sottoscrizione dalle informazioni visualizzate nel passaggio precedente o, se si dispone di più sottoscrizioni e si necessita di altre informazioni, è possibile eseguire il cmdlet **Get-AzureSubscription** e copiare le informazioni di sottoscrizione desiderate dal set di risultati. Una volta acquisita la sottoscrizione, eseguire il cmdlet seguente:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Dopo aver eseguito correttamente il cmdlet **Select-AzureSubscription**, verrà nuovamente visualizzato il prompt dei comandi di PowerShell. Se si dispone di più di una sottoscrizione, è possibile eseguire il cmdlet **Get-AzureSubscription** e verificare che nella sottoscrizione che si desidera usare sia visualizzato **IsCurrent : True**.


## Impostare le variabili per l'ambiente specifico

Esistono alcune variabili in cui è necessario sostituire i valori di esempio con i valori specifici per il database e l'account di archiviazione.

Sostituire i nomi del server e del database con il server e database che sono attualmente presenti nell'account. Per il nome del BLOB, immettere il nome del file BACPAC che verrà creato. Immettere il nome desiderato per il file BACPAC e includere l'estensione bacpac.

    $ServerName = "servername"
    $DatabaseName = "nameofdatabasetoexport"
    $BlobName = "filename.bacpac"

Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione per ottenere questi valori. È possibile trovare la chiave di accesso primaria facendo clic su **Tutte le impostazioni** e su **Chiavi** dal pannello dell'account di archiviazione.

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"

## Creare il puntatore per il proprio server e account di archiviazione

Eseguendo il cmdlet **Get-Credential** si apre una finestra in cui vengono chiesti nome utente e password. Immettere l'account di accesso di amministrazione e la password per il server SQL (NON il nome utente e la password dell'account di Azure).

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## Esportazione del database

Tale comando invia una richiesta di esportazione del database al servizio. A seconda delle dimensioni del database, l'operazione di esportazione potrebbe richiedere molto tempo.

> [AZURE.IMPORTANT]Per garantire un file BACPAC coerente dal punto di vista transazionale è necessario innanzitutto [creare una copia del database](sql-database-copy-powershell.md) e quindi esportare la copia del database.


    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## Monitorare lo stato dell’operazione di esportazione

Dopo aver eseguito **Start-AzureSqlDatabaseExport** è possibile controllare lo stato della richiesta. L’esecuzione di tale comando immediatamente dopo la richiesta in genere restituisce **Stato: in sospeso** o **Stato: in esecuzione**. Eseguire il comando più volte fino a che non si visualizza**Stato: completato** nell'output.

L’esecuzione di questo comando richiede una password. Immettere la password dell’amministratore per il server SQL.


    Get-AzureSqlDatabaseImportExportStatus -RequestId $exportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    


## Esportare lo script di PowerShell di database SQL


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "databasename"
    $BlobName = "bacpacfilename"
    
    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $exportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    


## Passaggi successivi

- [Importare un database SQL di Azure](sql-database-import-powershell.md)


## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Esercitazioni di ripristino di emergenza](sql-database-disaster-recovery-drills.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1203_2015-->