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
ms.openlocfilehash: 69604decab354368f336b85bfa1497671f0c3101
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730603"
---
#  <a name="restoring-backup-in-azure-service-fabric"></a>Ripristino del backup in Azure Service Fabric


I servizi Reliable con stato e Reliable Actors in Service Fabric possono mantenere lo stato modificabile e autorevole oltre alla richiesta e alla risposta o una transazione completa. Se un servizio con stato non funziona per molto tempo o perde informazioni a causa di un'emergenza, può essere necessario ripristinare l'ultimo backup accettabile del relativo stato per poter continuare a offrire il servizio dopo il ripristino.

Ad esempio, è possibile che in un servizio sia consigliabile eseguire il backup dei dati per evitare gli scenari seguenti:

- Perdita definitiva di un intero cluster di Service Fabric. **(Caso di ripristino di emergenza)**
- Perdita definitiva della maggior parte delle repliche di una partizione del servizio. **(Caso di perdita dei dati)**
- Errori amministrativi che provocano l'eliminazione o il danneggiamento accidentale dello stato. Ad esempio, un amministratore con i privilegi sufficienti elimina accidentalmente il servizio.**(Caso di perdita dei dati)**
- Bug nel servizio che provocano il danneggiamento dei dati. Ad esempio, i dati si possono danneggiare quando un aggiornamento del codice di servizio inizia a scrivere dati non corretti in una raccolta Reliable Collections. In tal caso, potrebbe essere necessario ripristinare uno stato precedente sia per il codice che per i dati. **(Caso di danneggiamento dei dati)**


## <a name="prerequisites"></a>Prerequisiti
* Per attivare, ripristinare il _servizio di analisi degli errori_ che deve essere abilitato per il cluster
* Il backup da ripristinare deve essere gestito dal _servizio di ripristino del backup_
* Il ripristino può essere attivato solo in una partizione.

## <a name="triggering-restore"></a>Attivazione del ripristino

Il ripristino può essere effettuato per gli scenari seguenti 
* Recupero dei dati in caso di _ripristino di emergenza_
* Ripristino dei dati in caso di _danneggiamento o perdita dei dati_



### <a name="data-restore-in-the-event-of-disaster-recovery-dr"></a>Recupero dei dati in caso di _ripristino di emergenza_
In caso di perdita di un intero cluster di Service Fabric, i dati per le partizioni del servizio Reliable con stato e Reliable Actors possono essere ripristinati in un cluster alternativo. Il backup può essere selezionato dall'enumerazione di [GetBackupAPI con i dettagli di archiviazione del backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). L'enumerazione del backup può essere relativa a un'applicazione, un servizio o una partizione.

Si supponga che il cluster perduto fosse il cluster menzionato in [Abilita i backup periodici per servizio Reliable con stato e Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), con `SampleApp` distribuito, in cui per la partizione erano abilitati criteri di backup e i backup venivano eseguiti in Archiviazione di Azure. 


Eseguire lo script PowerShell seguente per richiamare l'API REST ed enumerare i backup creati per tutte le partizioni all'interno dell'applicazione `SampleApp` nel cluster di Service Fabric perduto. L'API di enumerazione richiede informazioni sulla risorsa di archiviazione in cui sono archiviati i backup di un'applicazione, per consentire l'enumerazione dei backup disponibili. 

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



Per attivare il ripristino, è necessario scegliere un tipo di backup. Si supponga che per il ripristino di emergenza corrente il backup sia il seguente

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

Per l'API di ripristino, è necessario specificare i dettagli __BackupId__ e __BackupLocation__. La partizione nel cluster alternativo deve essere scelta in base allo [schema di partizione](service-fabric-concepts-partitioning.md#get-started-with-partitioning). È responsabilità dell'utente scegliere la partizione di destinazione per ripristinare il backup dal cluster alternativo in base allo schema di partizione del cluster originale perduto.

Si supponga che l'ID partizione nel cluster alternativo sia `1c42c47f-439e-4e09-98b9-88b8f60800c6` e venga mappato all'ID partizione del cluster originali `974bd92a-b395-4631-8a7f-53bd4ae9cf22` confrontando le chiavi superiore e inferiore per il _partizionamento per intervalli (UniformInt64Partition)_.

Per il _partizionamento denominato_, il valore del nome viene confrontato per identificare la partizione di destinazione nel cluster alternativo.

Il ripristino viene richiesto per la partizione del cluster di backup dalla seguente [API di ripristino](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)

```powershell 
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 
Lo stato di avanzamento del ripristino può essere [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress)

### <a name="data-restore-in-the-event-of-data-corruption--data-loss"></a>Ripristino dei dati in caso di _danneggiamento o perdita dei dati_

Per i casi di _perdita dei dati_ o _danneggiamento dei dati_, per le partizioni del servizio Reliable con stato e Reliable Actors il ripristino può essere eseguito in uno qualsiasi dei backup scelti. Il caso seguente è la continuazione dell'esempio menzionato in [Abilita i backup periodici per servizio Reliable con stato e Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), dove la partizione ha un criterio di backup abilitato e gestisce il backup con la frequenza indicata in un'archiviazione di Azure. 

Il backup da usare viene selezionato dall'output di [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). In questo scenario il backup viene generato dallo stesso cluster usato in precedenza.
Per attivare il ripristino, è necessario scegliere un tipo di backup dall'elenco. Si supponga che il backup da usare per lo scenario corrente di _perdita dei dati_ / _danneggiamento dei dati_ sia quello seguente

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

Per l'API di ripristino, è necessario specificare i dettagli __BackupId__ e __BackupLocation__. Poiché per il cluster è abilitato il backup, il _servizio di ripristino del backup_ di Service Fabric identifica la posizione di archiviazione corretta in base ai criteri di backup associati.

```powershell
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4', 
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Lo stato di avanzamento del ripristino può essere [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress)


## <a name="tracking-restore-progress"></a>Monitoraggio dell'avanzamento del ripristino

Una partizione di un servizio Reliable con stato o Reliable Actors accetta solo una richiesta di ripristino alla volta. Un'altra richiesta può essere accettata solo quando la richiesta di ripristino corrente è stata completata. Più richieste di ripristino possono essere attivate contemporaneamente in partizioni diverse.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
 
$restoreResponse = (ConvertFrom-Json $response.Content) 
$restoreResponse | Format-List
```

La richiesta di ripristino procede nell'ordine seguente

1. __Accepted__ (Accettato): lo stato del ripristino _Accepted_ indica che la richiesta è stata attivata con i parametri di richiesta corretti.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
2. __InProgress__ (In corso): lo stato del ripristino _InProgress_ indica che la partizione verrà sottoposta a un ripristino con il backup indicato nella richiesta. La partizione segnalerà lo stato _dataloss_ (perdita di dati).
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. __Success (Operazione completata)__/ __Failure (Errore)__/ __Timeout__: un ripristino richiesto può essere completato in uno qualsiasi dei seguenti stati. Il significato e la risposta di ogni stato sono riportati di seguito.
       
    * __Success__ (Operazione completata): lo stato _Success_ indica che lo stato della partizione è ripristinato. La risposta specifica RestoreEpoch e RestordLSN per la partizione oltre all'orario in formato UTC. 
    
        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```
        
    *. __Failure__ (Errore): lo stato del ripristino _Failure_ indica che la richiesta di ripristino ha avuto esito negativo. La causa dell'errore verrà indicata nella richiesta.
        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    *. __Timeout__: lo stato del ripristino _Timeout_ indica che si è verificato il timeout della richiesta. Si consiglia di inviare una nuova richiesta di ripristino con un valore [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) maggiore; il timeout predefinito è 10 minuti. È consigliabile verificare che la partizione non sia nello stato di perdita dei dati, prima di richiedere di nuovo il ripristino.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="auto-restore"></a>Ripristino automatico

Le partizioni per il servizio Reliable con stato e Reliable Actors nel cluster di Service Fabric possono essere configurate per il _ripristino automatico_. Quando si creano i criteri di backup, è possibile che per i criteri `AutoRestore` sia impostato su _true_.  L'abilitazione del _ripristino automatico_ per una partizione consente di ripristinare i dati dal backup più recente se viene segnalata una perdita di dati.
 
 [Abilitazione del ripristino automatico nei criteri di backup](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)


[Informazioni di riferimento sull'API RestorePartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
[Informazioni di riferimento sull'API GetPartitionRestoreProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni sulla configurazione del backup periodico](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Informazioni di riferimento sull'API REST di ripristino backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
