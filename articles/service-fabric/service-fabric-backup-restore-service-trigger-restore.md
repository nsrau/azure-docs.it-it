---
title: Ripristino del backup in Azure Service Fabric | Microsoft Docs
description: Usare la funzionalità di backup e ripristino periodici di Service Fabric per ripristinare i dati dal backup dei dati delle applicazioni.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 66df926eaaf771fdde4d2eb59772b9d38a87edf0
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313229"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Ripristino del backup in Azure Service Fabric

I servizi Reliable con stato e Reliable Actors in Azure Service Fabric possono mantenere uno stato modificabile e autorevole dopo il completamento di una transazione di richiesta e risposta. Un servizio con stato potrebbe rimanere non disponibile a lungo o perdere informazioni a causa di una situazione di emergenza. In queste circostanze il servizio deve essere ripristinato dall'ultimo backup accettabile per poter continuare a funzionare.

Ad esempio, è possibile configurare un servizio in modo che esegua il backup dei suoi dati come misura di protezione dalle situazioni seguenti:

- **Ripristino di emergenza**: Perdita definitiva di un intero cluster di Service Fabric.
- **Perdita di dati**: Perdita definitiva della maggior parte delle repliche di una partizione del servizio.
- **Perdita di dati**: eliminazione accidentale o danneggiamento del servizio. Ad esempio, un amministratore elimina accidentalmente il servizio.
- **Danneggiamento dei dati**: la presenza di bug nel servizio provoca il danneggiamento dei dati. Ad esempio, i dati si possono danneggiare quando un aggiornamento del codice di servizio scrive dati non corretti in una raccolta affidabile. In tal caso potrebbe essere necessario ripristinare uno stato precedente sia per il codice che per i dati.

## <a name="prerequisites"></a>Prerequisiti

- Per attivare un ripristino, il _servizio di analisi degli errori_ deve essere abilitato per il cluster.
- Il _servizio di ripristino backup_ ha creato il backup.
- Il ripristino può essere attivato solo in una partizione.

## <a name="triggered-restore"></a>Ripristino attivato

Il ripristino può essere attivato per gli scenari seguenti:

- Ripristino dati in caso di _ripristino di emergenza_.
- Ripristino dati in caso di _danneggiamento o perdita dei dati_.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Ripristino dati in caso di ripristino di emergenza

In caso di perdita di un intero cluster di Service Fabric, è possibile recuperare i dati delle partizioni del servizio Reliable con stato e Reliable Actors. Il backup desiderato può essere selezionato dall'elenco quando si usa [GetBackupAPI con i dettagli di archiviazione del backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). L'enumerazione del backup può essere relativa a un'applicazione, un servizio o una partizione.

Per l'esempio seguente supponiamo che il cluster andato perso sia lo stesso a cui si fa riferimento in [Abilita i backup periodici per servizio Reliable con stato e Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). In questo caso, l'app `SampleApp` viene distribuita con i criteri di backup abilitati e i backup sono configurati in Archiviazione di Azure.

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

Occorre richiedere il ripristino sulla partizione del cluster di backup usando l'[API di ripristino](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition) seguente:

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

È possibile monitorare lo stato di un ripristino con TrackRestoreProgress.

### <a name="data-restore-for-data-corruptiondata-loss"></a>Ripristino dati in caso di _danneggiamento_/_perdita dei dati_

In caso di _perdita dei dati_ o _danneggiamento dei dati_, per le partizioni sottoposte a backup del servizio Reliable con stato e Reliable Actors il ripristino può essere eseguito in uno qualsiasi dei backup scelti.

L'esempio seguente è la continuazione dell'esempio menzionato in [Abilita i backup periodici per servizio Reliable con stato e Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), dove per la partizione è abilitato un criterio di backup e il servizio esegue i backup con la frequenza indicata in Archiviazione di Azure.

Selezionare un backup dall'output di [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). In questo scenario il backup viene generato dallo stesso cluster usato in precedenza.

Per attivare il ripristino, scegliere un backup dall'elenco. Per lo scenario corrente di _perdita dei dati_/_danneggiamento dei dati_ selezionare il backup seguente:

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

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

La richiesta di ripristino procede nell'ordine seguente:

1. **Accettato**: lo stato del ripristino _Accettato_ indica che la partizione richiesta è stata attivata con i parametri di richiesta corretti.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **In corso**: lo stato del ripristino _In corso_ indica che è in corso un ripristino nella partizione con il backup indicato nella richiesta. La partizione segnala lo stato _dataloss_ (perdita di dati).
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Operazione riuscita**, **Operazione non riuscita** o **Timeout**: un ripristino richiesto può essere completato in uno degli stati seguenti. Il significato e la risposta di ogni stato sono riportati di seguito:
    - **Operazione riuscita**: lo stato _Operazione riuscita_ indica che lo stato della partizione è stato ripristinato. La partizione indica gli stati _RestoredEpoch_ e _RestoredLSN_, oltre all'ora in formato UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Operazione non riuscita**: lo stato del ripristino _Operazione non riuscita_ indica che la richiesta di ripristino ha avuto esito negativo. La causa dell'errore viene indicata.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Timeout**: lo stato del ripristino _Timeout_ indica che si è verificato il timeout della richiesta. Creare una nuova richiesta di ripristino con un valore di [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) maggiore. Il timeout predefinito è 10 minuti. Verificare che la partizione non sia nello stato di perdita dei dati prima di richiedere di nuovo il ripristino.
     
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
