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
	ms.date="07/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Archiviare un database SQL di Azure come file BACPAC tramite PowerShell

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


Questo articolo illustra come usare PowerShell per archiviare il database SQL di Azure come file BACPAC nell'archiviazione BLOB di Azure.

Quando si vuole creare un archivio per un database SQL di Azure, è possibile esportare lo schema e i dati del database in un file BACPAC. Un file BACPAC è semplicemente un file zip con estensione bacpac. Un file BACPAC in un secondo momento può essere archiviato nell'archiviazione BLOB di Azure o in un percorso locale e successivamente reimportato nel database SQL di Azure o in un'installazione locale di SQL Server.

***Considerazioni***

- Perché un archivio sia coerente dal punto di vista transazionale, è necessario assicurarsi che non si verifichi alcuna attività di scrittura durante l'esportazione oppure è necessario eseguire l'esportazione da una [copia coerente dal punto di vista transazionale](sql-database-copy.md) del database SQL di Azure
- La dimensione massima di un file BACPAC salvato nell'archivio BLOB di Azure è pari a 200 GB. Usare l'utilità del prompt dei comandi [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) per archiviare un file BACPAC più grande in un percorso locale. Questa utilità è disponibile sia in Visual Studio che in SQL Server. È inoltre possibile [scaricare](https://msdn.microsoft.com/library/mt204009.aspx) la versione più recente di SQL Server Data Tools per ottenere questa utilità.
- L'archiviazione Premium di file BACPAC in Azure non è supportata.
- Se l'operazione di esportazione dura oltre 20 ore, potrebbe essere annullata. Per migliorare le prestazioni durante l'esportazione è possibile:
 - Aumentare temporaneamente il livello di servizio.
 - Interrompere tutte le attività di lettura e scrittura durante l'esportazione.
 - Usare un indice cluster in tutte le tabelle di grandi dimensioni. Senza indici cluster, l'esportazione potrebbe non riuscire se dovesse durare più di 6 - 12 ore. Questo perché i servizi di esportazione devono completare la scansione della tabella prima di provare a esportarla per intero.
 
> [AZURE.NOTE] I BACPAC non sono destinati a essere utilizzati per il backup e le operazioni di ripristino. Il database SQL di Azure crea automaticamente i backup per ogni database dell’utente. Per conoscere i dettagli, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure.
- un database SQL di Azure.
- Un [account di archiviazione Azure standard](../storage/storage-create-storage-account.md) con un contenitore BLOB per archiviare il file BACPAC nell'archiviazione standard.


[AZURE.INCLUDE [Avviare la sessione di PowerShell](../../includes/sql-database-powershell.md)]


## Impostare le variabili per l'ambiente specifico

Esistono alcune variabili in cui è necessario sostituire i valori di esempio con i valori specifici per il database e l'account di archiviazione.

Sostituire con i valori specifici.

    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"


Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione per ottenere questi valori. È possibile trovare la chiave di accesso primaria facendo clic su **Tutte le impostazioni** e su **Chiavi** dal pannello dell'account di archiviazione.

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


Eseguendo il cmdlet **Get-Credential** si apre una finestra in cui vengono chiesti nome utente e password. Immettere l'account di accesso di amministrazione e la password per il server SQL (NON il nome utente e la password dell'account di Azure).

    $credential = Get-Credential

## Esportazione del database

Tale comando invia una richiesta di esportazione del database al servizio. A seconda delle dimensioni del database, l'operazione di esportazione potrebbe richiedere molto tempo.

> [AZURE.IMPORTANT] Per garantire un file BACPAC coerente dal punto di vista transazionale è necessario innanzitutto [creare una copia del database](sql-database-copy-powershell.md) e quindi esportare la copia del database.


    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName  $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password
    

## Monitorare lo stato dell’operazione di esportazione

Dopo aver eseguito **New-AzureRmSqlDatabaseExport** è possibile controllare lo stato della richiesta. L’esecuzione di tale comando immediatamente dopo la richiesta in genere restituisce **Stato: in sospeso** o **Stato: in esecuzione**. Eseguire il comando più volte fino a che non si visualizza**Stato: completato** nell'output.

L’esecuzione di questo comando richiede una password. Immettere la password dell’amministratore per il server SQL.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest .OperationStatusLink
    


## Esportare lo script di PowerShell di database SQL


    $ServerName = "servername"
    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName  $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey  StorageUri $StorageUri –AdministratorLogin $credential.UserName  –AdministratorPassword $credential.Password

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest .OperationStatusLink

## Passaggi successivi

- [Importare un database SQL di Azure](sql-database-import-powershell.md)


## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Esercitazioni di ripristino di emergenza](sql-database-disaster-recovery-drills.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0706_2016-->