---
title: Backup su richiesta in Azure Service Fabric | Microsoft Docs
description: Usare la funzionalità di backup e ripristino di Service Fabric per il backup dei dati delle applicazioni secondo le esigenze.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 02DA262A-EEF6-4F90-842E-FFC4A09003E5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 35d97f1da6b5d1c75073264c70e1cd1607d5fe0d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730584"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Backup su richiesta in Azure Service Fabric

I dati dei servizi Reliable con stato e Reliable Actors possono eseguire il backup per indirizzare gli scenari di perdita dati o emergenza.

Service Fabric è dotato di funzionalità per il [backup periodico dei dati](service-fabric-backuprestoreservice-quickstart-azurecluster.md) e il backup dei dati in base alle necessità. Il backup su richiesta è utile poiché protegge dalla _perdita di dati_/_danneggiamento dei dati_ causati da modifiche pianificate nel servizio sottostante o nel relativo ambiente.

La funzionalità di backup su richiesta è utile per acquisire lo stato dei servizi, prima di qualsiasi operazione attivata manualmente relative all'assistenza o all'ambiente del servizio. Come modificare i file binari del servizio, vale a dire, l'aggiornamento o il downgrade del servizio, in quanto avrà i dati protetti contro il danneggiamento dei dati dal bug nel codice dell'applicazione.

## <a name="triggering-on-demand-backup"></a>Attivazione di un backup su richiesta

Il backup su richiesta richiede i dettagli di archiviazione per il caricamento dei file di backup. Il backup su richiesta verrà eseguito nella risorsa di archiviazione specificata nel criterio di backup periodico o nella risorsa di archiviazione specificata nella richiesta di backup su richiesta.

### <a name="on-demand-backup-to-storage-specified-by-periodic-backup-policy"></a>Backup su richiesta sull'archiviazione specificata dai criteri di backup periodici

La partizione di un servizio Reliable con stato o Reliable Actors può essere richiesta per un ulteriore backup sull'archiviazione specificata nel criterio di backup periodico secondo le esigenze. 

Il caso seguente è la continuazione dell'esempio menzionato in [Abilita i backup periodici per servizio Reliable con stato e Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), dove la partizione ha un criterio di backup abilitato e gestisce il backup con la frequenza indicata in archiviazione di Azure.

Il backup su richiesta per l'ID di partizione `974bd92a-b395-4631-8a7f-53bd4ae9cf22` può essere attivato dall'API [BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition). 

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Lo [stato del backup su richiesta](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress) può essere tracciato dall'API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress).

### <a name="on-demand-backup-to-specified-storage"></a>Backup su richiesta sull'archiviazione specificata

Il backup su richiesta può essere richiesto per una partizione di un servizio Reliable con stato o Reliable Actors insieme alle informazioni di archiviazione. Le informazioni di archiviazione devono essere fornite come parte della richiesta di backup su richiesta.

Il backup su richiesta per l'ID di partizione `974bd92a-b395-4631-8a7f-53bd4ae9cf22` può essere attivato dall'API [BackupPartition] https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) con le informazioni di archiviazione di Azure come mostrato di seguito.

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

Lo [stato del backup su richiesta](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress) può essere tracciato dall'API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress).


## <a name="tracking-on-demand-backup-progress"></a>Tenere traccia dello stato di backup su richiesta

Una partizione di un servizio Reliable con stato o Reliable Actors accetta solo una richiesta di backup su richiesta alla volta. Un'altra richiesta può essere accettata solo quando la richiesta di backup su richiesta corrente è stata completata. 

Più richieste di backup su richiesta possono essere attivate contemporaneamente in partizioni diverse.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Lo stato di avanzamento della richiesta di backup può presentare uno dei seguenti stati

* **Accettato** -il backup è stato avviato nella partizione ed è in corso.
    ```
    BackupState             : Accepted
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          : 
    EpochOfLastBackupRecord : 
    LsnOfLastBackupRecord   : 0
    FailureError            : 
    ```
    
* **Success (Operazione completata)/Failure (Errore)/Timeout**: un backup richiesto può essere completato in uno qualsiasi dei seguenti stati. Il significato e la risposta di ogni stato sono riportati di seguito.

    * **Operazione completata** -lo stato di backup _Success_ indica che lo stato della partizione ha eseguito correttamente il backup. La risposta specifica __BackupEpoch__ e __BackupLSN__ per la partizione oltre all'orario in formato UTC.
        ```
        BackupState             : Success
        TimeStampUtc            : 2018-11-21T20:00:01Z
        BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
        BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
        EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
        LsnOfLastBackupRecord   : 36
        FailureError            : 
        ```
        
    * **Errore** -lo stato di backup _Failure_ indica che l'errore si è verificato durante il backup dello stato della partizione. La causa dell'errore verrà indicata nella risposta.
        ```
        BackupState             : Failure
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
        ```
       
    * **Timeout** -lo stato di backup _Timeout_ indica che il backup dello stato di partizione non è stato creato nell'intervallo di tempo prestabilito, il valore di timeout predefinito è 10 minuti. In questo scenario è consigliabile avviare una nuova richiesta di backup con maggiore [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) nella richiesta di backup su richiesta.

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
- [Informazioni di riferimento sull'API REST di ripristino backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

