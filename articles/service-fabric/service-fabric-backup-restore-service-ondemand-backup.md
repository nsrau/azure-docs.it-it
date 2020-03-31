---
title: Backup su richiesta in Azure Service Fabric
description: Usare la funzionalità di backup e ripristino di Service Fabric per eseguire il backup dei dati delle applicazioni secondo le esigenze.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d5eada62bec49fe771373671e9438d2786d6b165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458430"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Backup su richiesta in Azure Service Fabric

È possibile eseguire il backup dei dati dei servizi Reliable con stato e Reliable Actors per far fronte a situazioni di emergenza o di perdita di dati.

Service Fabric offre funzionalità per il [backup periodico dei dati](service-fabric-backuprestoreservice-quickstart-azurecluster.md) e il backup dei dati in base alle necessità. Il backup su richiesta è utile perché protegge dal_danneggiamento_ dei dati di _perdita_/di dati a causa di modifiche pianificate nel servizio sottostante o nel relativo ambiente.

Le funzionalità di backup su richiesta sono utili per acquisire lo stato dei servizi prima di attivare manualmente un'operazione relativa a un servizio o a un ambiente di servizio. Se ad esempio si modificano i file binari di un servizio durante l'aggiornamento o il downgrade, il backup su richiesta può contribuire a proteggere i dati dal danneggiamento a causa di bug nel codice dell'applicazione.
## <a name="prerequisites"></a>Prerequisiti

- Installare Microsoft.ServiceFabric.Powershell.Http Module [In Anteprima] per effettuare chiamate di configurazione.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Assicurarsi che Cluster sia `Connect-SFCluster` connesso utilizzando il comando prima di effettuare qualsiasi richiesta di configurazione utilizzando Microsoft.ServiceFabric.Powershell.Http Module.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>Attivazione di un backup su richiesta

Il backup su richiesta richiede i dettagli di archiviazione per il caricamento dei file di backup. Occorre specificare la posizione del backup su richiesta, nei criteri di backup periodico o in una richiesta di backup su richiesta.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Backup su richiesta nello spazio di archiviazione specificato dai criteri di backup periodico

È possibile configurare i criteri di backup periodico in modo da usare una partizione di un servizio Reliable con stato o Reliable Actor per i backup su richiesta aggiuntivi nello spazio di archiviazione.

Il caso descritto di seguito è la continuazione dello scenario menzionato in [Abilita i backup periodici per servizio Reliable con stato e Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). In questo caso si abilitano i criteri di backup per l'uso di una partizione e viene eseguito un backup in base alla frequenza impostata in Archiviazione di Azure.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell con Microsoft.ServiceFabric.Powershell.Http Module

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>Rest Call con Powershell

Usare l'API [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) per configurare l'attivazione del backup su richiesta per l'ID di partizione `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Usare l'API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) per abilitare il monitoraggio dello [stato del backup su richiesta](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Backup su richiesta sull'archiviazione specificata

È possibile richiedere il backup su richiesta per una partizione di un servizio Reliable con stato o Reliable Actors. Le informazioni di archiviazione devono essere fornite come parte della richiesta di backup su richiesta.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell con Microsoft.ServiceFabric.Powershell.Http Module

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Rest Call con Powershell

Usare l'API [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) per configurare l'attivazione del backup su richiesta per l'ID di partizione `974bd92a-b395-4631-8a7f-53bd4ae9cf22`. Includere le informazioni di Archiviazione di Azure seguenti:

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

È possibile usare l'API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) per configurare il monitoraggio dello [stato del backup su richiesta](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="using-service-fabric-explorer"></a>Uso di Service Fabric ExplorerUsing Service Fabric Explorer
Assicurarsi che la modalità avanzata sia stata abilitata nelle impostazioni di Service Fabric Explorer.Make sure Advanced Mode has been enabled in Service Fabric Explorer settings.
1. Selezionare le partizioni desiderate e fare clic su Azioni. 
2. Selezionare Trigger Partition Backup e immettere le informazioni per Azure:Select Trigger Partition Backup, and fill in information for Azure:

    ![Backup della partizione triggerTrigger Partition Backup][0]

    o FileShare:

    ![FileFile di backup della partizione trigger][1]

## <a name="tracking-on-demand-backup-progress"></a>Tenere traccia dello stato di backup su richiesta

Una partizione di un servizio Reliable con stato o Reliable Actors accetta solo una richiesta di backup su richiesta alla volta. Un'altra richiesta può essere accettata solo dopo che la richiesta di backup su richiesta corrente è stata completata.

Partizioni diverse possono attivare richieste di backup su richiesta contemporaneamente.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell con Microsoft.ServiceFabric.Powershell.Http Module

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>Rest Call con Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Gli stati delle richieste di backup su richiesta sono i seguenti:

- **Accettato**: il backup è stato avviato nella partizione ed è in corso.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Esito**, **Errore**o **Timeout**: un backup su richiesta può essere completato in uno dei seguenti stati:
  - **Operazione riuscita:** uno stato di backup _riuscito_ indica che il backup dello stato della partizione è stato eseguito correttamente. La risposta specifica _BackupEpoch_ e _BackupLSN_ per la partizione, oltre all'orario in formato UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Errore:** uno stato di backup _dell'errore_ indica che si è verificato un errore durante il backup dello stato della partizione. La causa dell'errore è indicata nella risposta.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Timeout:** uno stato di backup _Timeout_ indica che non è stato possibile creare il backup dello stato della partizione in un determinato periodo di tempo. Il valore di timeout predefinito è 10 minuti. In questo caso, avviare una nuova richiesta di backup su richiesta con un valore di [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) maggiore.
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulla configurazione del backup periodico](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Informazioni di riferimento sull'API BackupRestore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png