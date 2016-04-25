<properties 
    pageTitle="Archiviare un database SQL di Azure come file BACPAC tramite PowerShell" 
    description="Archiviare un database SQL di Azure come file BACPAC tramite PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Archiviare un database SQL di Azure come file BACPAC tramite PowerShell

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


Questo articolo illustra come usare PowerShell per archiviare il database SQL di Azure come file BACPAC nell'archivio BLOB di Azure.

Quando si vuole creare un archivio per un database SQL di Azure, è possibile esportare lo schema e i dati del database in un file BACPAC. Un file BACPAC è semplicemente un file zip con estensione bacpac. Un file BACPAC in un secondo momento può essere archiviato nell'archivio BLOB di Azure o in un percorso locale e successivamente reimportato nel database SQL di Azure o in un'istallazione locale di SQL Server.

***Considerazioni***

- Perché un archivio sia coerente dal punto di vista transazionale, è necessario assicurarsi che non si verifichi alcuna attività di scrittura durante l'esportazione oppure è necessario eseguire l'esportazione da una [copia coerente dal punto di vista transazionale](sql-database-copy.md) del database SQL di Azure
- La dimensione massima di un file BACPAC salvato nell'archivio BLOB di Azure è pari a 200 GB. Usare l'utilità del prompt dei comandi [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) per archiviare un file BACPAC più grande in un percorso locale. Questa utilità è disponibile sia in Visual Studio che in SQL Server. È inoltre possibile [scaricare](https://msdn.microsoft.com/library/mt204009.aspx) la versione più recente di SQL Server Data Tools per ottenere questa utilità.
- L'archiviazione Premium di file BACPAC in Azure non è supportata.
- Se l'operazione di esportazione dura oltre 20 ore può essere annullata. Per migliorare le prestazioni durante l'esportazione, è possibile:
 - Aumentare temporaneamente il livello di servizio 
 - Sospendere tutte le attività di lettura e scrittura durante l'esportazione
 - Usare un indice cluster in tutte le tabelle di grandi dimensioni. Senza indici cluster, l'esportazione potrebbe non riuscire se dovesse durare più di 6 - 12 ore. I servizi di esportazione devono infatti completare la scansione della tabella per tentare di esportare l'intera tabella
 
> [AZURE.NOTE] I BACPAC non sono destinati a essere utilizzati per il backup e le operazioni di ripristino. Il database SQL di Azure crea automaticamente i backup per ogni database dell’utente. Per ulteriori informazioni, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. 
- Database SQL di Azure. 
- Un [account di archiviazione Azure standard](../storage/storage-create-storage-account.md) con un contenitore BLOB per archiviare il file BACPAC nell'archiviazione standard.
- Azure PowerShell. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).


## Configurare le credenziali e selezionare la sottoscrizione

È innanzitutto necessario stabilire l'accesso all'account Azure, poi avviare PowerShell ed eseguire il cmdlet seguente. Nella schermata di accesso utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.

	Add-AzureAccount

Dopo aver effettuato l'accesso, sullo schermo verranno visualizzate informazioni tra cui l'ID usato per l'accesso con le sottoscrizioni di Azure per le quali si dispone dell'accesso.


### Selezionare la sottoscrizione ad Azure

Per selezionare la sottoscrizione, è necessario l’ID sottoscrizione o il nome della sottoscrizione (**-SubscriptionName**) È possibile copiare l'ID sottoscrizione dalle informazioni visualizzate nel passaggio precedente o, se si dispone di più sottoscrizioni e si necessita di altre informazioni, è possibile eseguire il cmdlet **Get-AzureSubscription** e copiare le informazioni di sottoscrizione desiderate dal set di risultati. Una volta acquisita la sottoscrizione, eseguire il cmdlet seguente:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Dopo aver eseguito correttamente il cmdlet **Select-AzureSubscription**, verrà nuovamente visualizzato il prompt dei comandi di PowerShell. Se si dispone di più di una sottoscrizione, è possibile eseguire il cmdlet **Get-AzureSubscription** e verificare che nella sottoscrizione che si desidera usare sia visualizzato **IsCurrent:True**.


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

> [AZURE.IMPORTANT] Per garantire un file BACPAC coerente dal punto di vista transazionale è necessario innanzitutto [creare una copia del database](sql-database-copy-powershell.md) e quindi esportare la copia del database.


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

<!---HONumber=AcomDC_0413_2016-->