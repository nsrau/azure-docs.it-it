---
title: Ripristino del backup in Azure Service Fabric
description: Usare la funzionalità di backup e ripristino periodici di Service Fabric per ripristinare i dati dal backup dei dati delle applicazioni.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 1737102ee652cc2263bd0a908c1336bc93a6757b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377906"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Ripristino del backup in Azure Service Fabric

I servizi Reliable con stato e Reliable Actors in Azure Service Fabric possono mantenere uno stato modificabile e autorevole dopo il completamento di una transazione di richiesta e risposta. Un servizio con stato potrebbe rimanere non disponibile a lungo o perdere informazioni a causa di una situazione di emergenza. In queste circostanze il servizio deve essere ripristinato dall'ultimo backup accettabile per poter continuare a funzionare.

Ad esempio, è possibile configurare un servizio in modo che esegua il backup dei suoi dati come misura di protezione dalle situazioni seguenti:

- **Caso di ripristino di emergenza:** perdita permanente di un intero cluster di Service Fabric.Case of disaster recovery: Permanent loss of an entire Service Fabric cluster.
- **Caso di perdita di dati**: perdita permanente della maggior parte delle repliche di una partizione del servizio.
- **Caso di perdita di dati**: cancellazione accidentale o danneggiamento del servizio. Ad esempio, un amministratore elimina accidentalmente il servizio.
- **Caso di danneggiamento dei dati**: I bug nel servizio causano il danneggiamento dei dati. Ad esempio, i dati si possono danneggiare quando un aggiornamento del codice di servizio scrive dati non corretti in una raccolta affidabile. In tal caso potrebbe essere necessario ripristinare uno stato precedente sia per il codice che per i dati.

## <a name="prerequisites"></a>Prerequisiti

- Per attivare un ripristino, il _servizio di analisi degli errori_ deve essere abilitato per il cluster.
- Il _servizio di ripristino backup_ ha creato il backup.
- Il ripristino può essere attivato solo in una partizione.
- Installare Microsoft.ServiceFabric.Powershell.Http Module [In Anteprima] per effettuare chiamate di configurazione.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Assicurarsi che Cluster sia `Connect-SFCluster` connesso utilizzando il comando prima di effettuare qualsiasi richiesta di configurazione utilizzando Microsoft.ServiceFabric.Powershell.Http Module.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggered-restore"></a>Ripristino attivato

Il ripristino può essere attivato per gli scenari seguenti:

- Ripristino dati in caso di _ripristino di emergenza_.
- Ripristino dei dati per _corruzione/perdita_di dati .

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Ripristino dati in caso di ripristino di emergenza

In caso di perdita di un intero cluster di Service Fabric, è possibile recuperare i dati delle partizioni del servizio Reliable con stato e Reliable Actors. Il backup desiderato può essere selezionato dall'elenco quando si usa [GetBackupAPI con i dettagli di archiviazione del backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). L'enumerazione del backup può essere relativa a un'applicazione, un servizio o una partizione.

Per l'esempio seguente supponiamo che il cluster andato perso sia lo stesso a cui si fa riferimento in [Abilita i backup periodici per servizio Reliable con stato e Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). In questo caso, l'app `SampleApp` viene distribuita con i criteri di backup abilitati e i backup sono configurati in Archiviazione di Azure.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell con Microsoft.ServiceFabric.Powershell.Http Module

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Rest Call con Powershell

Eseguire uno script di PowerShell per usare l'API REST per restituire un elenco dei backup creati per tutte le partizioni all'interno dell'applicazione `SampleApp`. Per elencare i backup disponibili, l'API deve disporre delle informazioni di archiviazione dei backup.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Esempio di output per l’esecuzione sopra indicata:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            :
```

Per attivare il ripristino, scegliere uno dei backup. Ad esempio, il backup corrente per il ripristino di emergenza potrebbe essere il seguente:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Per l'API di ripristino è necessario specificare i dettagli _BackupId_ e _BackupLocation_.

Occorre anche scegliere una partizione di destinazione nel cluster alternativo come descritto in dettaglio nello [schema di partizione](service-fabric-concepts-partitioning.md#get-started-with-partitioning). Il backup del cluster alternativo viene ripristinato nella partizione specificata nello schema di partizione dal cluster originale andato perso.

Se l'ID partizione nel cluster alternativo è `1c42c47f-439e-4e09-98b9-88b8f60800c6`, è possibile eseguirne il mapping all'ID partizione del cluster originale `974bd92a-b395-4631-8a7f-53bd4ae9cf22` confrontando le chiavi superiore e inferiore per il _partizionamento per intervalli (UniformInt64Partition)_.

Per il _partizionamento denominato_, il valore del nome viene confrontato per identificare la partizione di destinazione nel cluster alternativo.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell con Microsoft.ServiceFabric.Powershell.Http Module

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Rest Call con Powershell

Occorre richiedere il ripristino sulla partizione del cluster di backup usando l'[API di ripristino](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition) seguente:

```powershell

$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
    BackupStorage  = $StorageInfo
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

È possibile monitorare lo stato di un ripristino con TrackRestoreProgress.

### <a name="using-service-fabric-explorer"></a>Uso di Service Fabric ExplorerUsing Service Fabric Explorer
È possibile attivare un ripristino da Service Fabric Explorer.You can trigger a restore from Service Fabric Explorer. Assicurarsi che la modalità avanzata sia stata abilitata nelle impostazioni di Service Fabric Explorer.Make sure Advanced Mode has been enabled in Service Fabric Explorer settings.
1. Selezionare le partizioni desiderate e fare clic su Azioni. 
2. Selezionare Trigger Partition Restore e inserire le informazioni per Azure:Select Trigger Partition Restore and fill in information for Azure:

    ![Ripristino della partizione di triggerTrigger Partition Restore][2]

    o FileShare:

    ![Trigger Partition Restore Fileshare][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>Ripristino dei dati per _la perdita di_/_dati_

In caso di _perdita dei dati_ o _danneggiamento dei dati_, per le partizioni sottoposte a backup del servizio Reliable con stato e Reliable Actors il ripristino può essere eseguito in uno qualsiasi dei backup scelti.

L'esempio seguente è la continuazione dell'esempio menzionato in [Abilita i backup periodici per servizio Reliable con stato e Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), dove per la partizione è abilitato un criterio di backup e il servizio esegue i backup con la frequenza indicata in Archiviazione di Azure.

Selezionare un backup dall'output di [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). In questo scenario il backup viene generato dallo stesso cluster usato in precedenza.

Per attivare il ripristino, scegliere un backup dall'elenco. Per il_danneggiamento_dei dati di _perdita_/di dati corrente, selezionare il backup seguente:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Per l'API di ripristino, specificare i dettagli _BackupId_ e _BackupLocation_. Poiché per il cluster è abilitato il backup, il _servizio di ripristino del backup_ di Service Fabric identifica la posizione di archiviazione corretta in base ai criteri di backup associati.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell con Microsoft.ServiceFabric.Powershell.Http Module

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>Rest Call con Powershell

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

È possibile monitorare lo stato del ripristino usando TrackRestoreProgress.

## <a name="track-restore-progress"></a>Monitorare lo stato del ripristino

Una partizione di un servizio Reliable con stato o Reliable Actors accetta solo una richiesta di ripristino alla volta. Una partizione accetta solo un'altra richiesta dopo il completamento della richiesta di ripristino corrente. Più richieste di ripristino possono essere attivate contemporaneamente in partizioni diverse.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell con Microsoft.ServiceFabric.Powershell.Http Module

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>Rest Call con Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

La richiesta di ripristino procede nell'ordine seguente:

1. **Accettato**: uno stato di ripristino _accettato_ indica che la partizione richiesta è stata attivata con i parametri di richiesta corretti.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **InProgress**: Uno stato di ripristino _InProgress_ indica che è in corso un ripristino nella partizione con il backup indicato nella richiesta. La partizione segnala lo stato _dataloss_ (perdita di dati).
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Esito**, **Errore**o **Timeout**: un ripristino richiesto può essere completato in uno dei seguenti stati. Il significato e la risposta di ogni stato sono riportati di seguito:
    - **Operazione riuscita:** uno stato di ripristino _riuscito_ indica uno stato di partizione ritrovato. La partizione indica gli stati _RestoredEpoch_ e _RestoredLSN_, oltre all'ora in formato UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Errore:** uno stato di ripristino _non riusciti_ indica l'errore della richiesta di ripristino. La causa dell'errore viene indicata.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Timeout:** uno stato di ripristino _Timeout_ indica che la richiesta è scaduta. Creare una nuova richiesta di ripristino con un valore di [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) maggiore. Il timeout predefinito è 10 minuti. Verificare che la partizione non sia nello stato di perdita dei dati prima di richiedere di nuovo il ripristino.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Ripristino automatico

È possibile configurare le partizioni del servizio Reliable con stato e Reliable Actors nel cluster di Service Fabric per il _ripristino automatico_. Nei criteri di backup impostare `AutoRestore` su _true_. L'abilitazione del _ripristino automatico_ ripristina automaticamente i dati dal backup più recente della partizione quando viene segnalata una perdita di dati. Per altre informazioni, vedere:

- [Abilitazione del ripristino automatico nei criteri di backup](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [Informazioni di riferimento sull'API RestorePartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
- [Informazioni di riferimento sull'API GetPartitionRestoreProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni sulla configurazione del backup periodico](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Informazioni di riferimento sull'API REST di ripristino backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png