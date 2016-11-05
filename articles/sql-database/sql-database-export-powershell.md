---
title: Archiviare un database SQL di Azure come file BACPAC tramite PowerShell
description: Archiviare un database SQL di Azure come file BACPAC tramite PowerShell
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 08/15/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA

---
# Archiviare un database SQL di Azure come file BACPAC tramite PowerShell
> [!div class="op_single_selector"]
> * [Portale di Azure](sql-database-export.md)
> * [PowerShell](sql-database-export-powershell.md)
> 
> 

Questo articolo illustra come usare PowerShell per archiviare il database SQL di Azure come file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) nell'archiviazione BLOB di Azure.

Per creare un archivio di un database SQL di Azure, è possibile esportare lo schema di database e i dati in un file BACPAC. Un file BACPAC è semplicemente un file ZIP con estensione BACPAC. Il file BACPAC può essere successivamente memorizzato nell'archiviazione BLOB di Azure oppure nell'archiviazione di un percorso locale. È anche possibile importarlo nuovamente nel database di SQL di Azure o in un'installazione di SQL Server locale.

**Considerazioni**

* Perché un archivio sia coerente dal punto di vista transazionale, è necessario assicurarsi che non si verifichino attività di scrittura durante l'esportazione o che l'esportazione sia eseguita da una [copia coerente dal punto di vista transazionale](sql-database-copy.md) del database SQL di Azure.
* La dimensione massima di un file BACPAC salvato nell'archivio BLOB di Azure è pari a 200 GB. Per archiviare un file BACPAC più grande in un percorso locale, usare l'utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Questa utilità è disponibile sia in Visual Studio che in SQL Server. È inoltre possibile [scaricare](https://msdn.microsoft.com/library/mt204009.aspx) la versione più recente di SQL Server Data Tools per ottenere questa utilità.
* L'archiviazione Premium di file BACPAC in Azure non è supportata.
* Se l'operazione di esportazione dura oltre 20 ore, potrebbe essere annullata. Per migliorare le prestazioni durante l'esportazione è possibile:
  * Aumentare temporaneamente il livello di servizio.
  * Interrompere tutte le attività di lettura e scrittura durante l'esportazione.
  * Utilizzare un [indice cluster](https://msdn.microsoft.com/library/ms190457.aspx) con valori non null in tutte le tabelle di grandi dimensioni. Senza indici cluster, l'esportazione potrebbe non riuscire se dovesse durare più di 6 - 12 ore. Questo perché i servizi di esportazione devono completare la scansione della tabella prima di provare a esportarla per intero. Un modo valido di determinare se le tabelle sono ottimizzate per l'esportazione consiste nell'eseguire **DBCC SHOW\_STATISTICS** e verificare che il parametro *RANGE\_HI\_KEY* non sia null e il relativo valore abbia distribuzione valide. Per i dettagli, vedere [DBCC SHOW\_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> I BACPAC non sono destinati a essere utilizzati per il backup e le operazioni di ripristino. Il database SQL di Azure crea automaticamente i backup per ogni database dell’utente. Per conoscere i dettagli, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).
> 
> 

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure.
* un database SQL di Azure.
* Un [account di Archiviazione Standard di Azure](../storage/storage-create-storage-account.md) con un contenitore BLOB per archiviare il file BACPAC nella risorsa di archiviazione standard.

[!INCLUDE [Avviare la sessione di PowerShell](../../includes/sql-database-powershell.md)]

## Esportazione del database
Il cmdlet [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/mt707796.aspx) invia al servizio una richiesta di esportazione del database. A seconda delle dimensioni del database, l'operazione di esportazione potrebbe richiedere molto tempo.

> [!IMPORTANT]
> Per garantire un file BACPAC coerente dal punto di vista transazionale è necessario innanzitutto [creare una copia del database](sql-database-copy-powershell.md) e quindi esportare la copia del database.
> 
> 

     $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password


## Monitorare lo stato dell’operazione di esportazione
Dopo aver eseguito il cmdlet **New-AzureRmSqlDatabaseExport**, è possibile controllare lo stato della richiesta eseguendo il cmdlet [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/mt707794.aspx). L'esecuzione di questo cmdlet subito dopo la richiesta restituisce in genere **Status: InProgress**. A esportazione completa, il messaggio restituito è **Status : Succeeded**.

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## Esempio di esportazione di un database SQL
Nell'esempio seguente viene esportato un database SQL esistente in un file BACPAC e quindi viene spiegato come controllare lo stato dell'operazione di esportazione.

Per eseguire l'esempio, esistono alcune variabili da sostituire con i valori specifici relativi al database e all'account di archiviazione. Nel [Portale di Azure](https://portal.azure.com) accedere all'account di archiviazione per ottenerne il nome, insieme al nome del contenitore BLOB e al valore della chiave. Per trovare la chiave, fare clic su **Chiavi di accesso** nel pannello dell'account di archiviazione.

Sostituire quanto segue `VARIABLE-VALUES` con i valori relativi alle specifiche risorse di Azure. Il nome del database indica il database esistente che si desidera esportare.

    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## Passaggi successivi
* Per informazioni su come importare un database SQL di Azure tramite Powershell, vedere [Importare un file BACPAC per creare un nuovo database SQL di Azure tramite PowerShell](sql-database-import-powershell.md).

## Risorse aggiuntive
* [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/mt707796.aspx)
* [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/mt707794.aspx)

<!---HONumber=AcomDC_0817_2016-->