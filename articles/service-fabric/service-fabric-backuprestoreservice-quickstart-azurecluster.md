---
title: Backup e ripristino periodici in Azure Service Fabric | Microsoft Docs
description: Usare la funzionalità di backup e ripristino periodici di Service Fabric per abilitare il backup periodico dei dati delle applicazioni.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: chackdan
editor: hrushib
ms.assetid: FAA58600-897E-4CEE-9D1C-93FACF98AD1C
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: d5d87f153b5835d5d2b38f380e5c77c03a68e1b5
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928237"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric"></a>Backup e ripristino periodici in Azure Service Fabric 
> [!div class="op_single_selector"]
> * [Cluster in Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Cluster autonomi](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Azure Service Fabric è una piattaforma di sistemi distribuiti che semplifica lo sviluppo e la gestione di applicazioni cloud basate su microservizi distribuiti e affidabili. Consente l'esecuzione di microservizi senza stato e con stato. I servizi con stato possono mantenere lo stato modificabile e autorevole oltre alla richiesta e alla risposta o una transazione completa. Se un servizio con stato non funziona per molto tempo o perde informazioni a causa di un'emergenza, potrebbe essere necessario ripristinare un backup recente del relativo stato per poter continuare a fornire il servizio dopo il back up.

Service Fabric consente di replicare lo stato tra più nodi per assicurarsi che il servizio sia a disponibilità elevata. Anche in caso di errore di un nodo nel cluster, il servizio rimarrà quindi comunque disponibile. In alcuni casi, tuttavia, è comunque preferibile che i dati del servizio siano protetti contro errori maggiori.
 
Ad esempio, è possibile che in un servizio sia consigliabile eseguire il backup dei dati per evitare gli scenari seguenti:
- Perdita definitiva di un intero cluster di Service Fabric.
- Perdita definitiva della maggior parte delle repliche di una partizione del servizio.
- Errori amministrativi che provocano l'eliminazione o il danneggiamento accidentale dello stato. Ad esempio, un amministratore con i privilegi sufficienti elimina accidentalmente il servizio.
- Bug nel servizio che provocano il danneggiamento dei dati. Ad esempio, questo problema può verificarsi quando un aggiornamento del codice di servizio inizia a scrivere dati non corretti in una raccolta Reliable Collections. In tal caso, potrebbe essere necessario ripristinare uno stato precedente sia per il codice che per i dati.
- Elaborazione dati offline. Potrebbe essere utile eseguire offline l'elaborazione dei dati per la business intelligence separatamente dal servizio che genera i dati.

Service Fabric fornisce un'API incorporata per eseguire [backup e ripristino](service-fabric-reliable-services-backup-restore.md) in un determinato momento. Gli sviluppatori di applicazioni possono usare queste API per eseguire periodicamente il backup dello stato del servizio. Inoltre, se gli amministratori del servizio desiderano attivare un backup dall'esterno del servizio in un momento specifico, ad esempio prima di aggiornare l'applicazione, gli sviluppatori devono esporre il backup (e il ripristino) come API dal servizio. Mantenere i backup rappresenta un costo aggiuntivo ulteriore. Ad esempio, potrebbe essere necessario eseguire cinque backup incrementali ogni mezz'ora, seguiti da un backup completo. Dopo il backup completo, è possibile eliminare i backup incrementali precedenti. Questo approccio richiede un codice aggiuntivo che comporta costi ulteriori durante lo sviluppo delle applicazioni.

Il servizio di backup e ripristino in Service Fabric consente di eseguire in modo semplice e automatico il backup delle informazioni archiviate nei servizi con stato. Il backup periodico dei dati delle applicazioni è fondamentale per la protezione dalla perdita di dati e la mancata disponibilità dei servizi. Service Fabric fornisce un servizio opzionale di backup e ripristino, che consente di configurare il backup periodico dei servizi Reliable con stato (inclusi i servizi Actor) senza dover scrivere alcun codice aggiuntivo. Facilita inoltre il ripristino dei backup precedentemente eseguiti. 


Service Fabric fornisce un set di API per ottenere le seguenti funzionalità relative alle funzioni di backup e ripristino periodico:

- Pianificazione del backup periodico dei servizi Reliable con stato e Reliable Actors con supporto per il caricamento del backup in posizioni di archiviazione (esterne). Posizioni di archiviazione supportate
    - Archiviazione di Azure
    - Condivisione di file (locale)
- Enumerazione di backup
- Attivare un backup ad hoc di una partizione
- Ripristino di una partizione utilizzando un backup precedente
- Sospensione temporanea dei backup
- Gestione della memorizzazione dei backup (a breve)

## <a name="prerequisites"></a>prerequisiti
* Service Fabric cluster con infrastruttura versione 6,4 o successiva. Fare riferimento a questo [articolo](service-fabric-cluster-creation-via-arm.md) per informazioni sulla procedura per la creazione del cluster di Service Fabric con il modello di risorse di Azure.
* Certificato X.509 per la crittografia dei segreti necessari per connettersi allo storage per archiviare i backup. Fare riferimento all’[articolo](service-fabric-cluster-creation-via-arm.md) per sapere come ottenere o creare un certificato X.509.
* L’applicazione Reliable con stato di Service Fabric generata utilizzando Service Fabric SDK versione 3.0 o successiva. Per le applicazioni destinate a .NET Core 2,0, l'applicazione deve essere compilata usando Service Fabric SDK versione 3,1 o successiva.
* Creare un account di archiviazione di Azure per i backup dell'applicazione.
* Installare il modulo Microsoft. ServiceFabric. PowerShell. http [in anteprima] per eseguire chiamate di configurazione.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Verificare che il cluster sia connesso usando il comando `Connect-SFCluster` prima di eseguire qualsiasi richiesta di configurazione usando il modulo Microsoft. ServiceFabric. PowerShell. http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Abilitare il servizio di backup e ripristino

### <a name="using-azure-portal"></a>Uso del portale di Azure

Abilitare `Include backup restore service` casella di controllo in `+ Show optional settings` nella scheda `Cluster Configuration`.

![Abilitare il servizio di ripristino di backup con il portale][1]


### <a name="using-azure-resource-manager-template"></a>Uso del modello di Azure Resource Manager
È innanzitutto necessario abilitare il _servizio di backup e ripristino_ nel cluster. Ottenere il modello per il cluster che si vuole distribuire. È possibile usare i [modelli di esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) o creare un modello di Resource Manager. Per abilitare il _servizio di backup e ripristino_, seguire questa procedura:

1. Verificare che `apiversion` sia impostato su per la risorsa **`2018-02-01`** e, se `Microsoft.ServiceFabric/clusters`non lo è, aggiornarlo come illustrato nel frammento seguente:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. A questo punto, abilitare il _servizio di backup e ripristino_ aggiungendo la sezione `addonFeatures`seguente sotto la sezione `properties` , come illustrato nel frammento seguente: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Configurare il certificato X.509 per la crittografia delle credenziali. Questo è importante per garantire che le credenziali fornite per connettersi alla risorsa di archiviazione vengano crittografate prima di renderle permanenti. Configurare il certificato di crittografia aggiungendo la sezione `BackupRestoreService` seguente sotto la sezione `fabricSettings`, come illustrato nel frammento seguente: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. Dopo avere aggiornato il modello di cluster con le modifiche precedenti, applicarle e consentire il completamento dell'aggiornamento/implementazione. Al termine della procedura, il _servizio di backup e ripristino_ inizia l'esecuzione del cluster. L'Uri di questo servizio è `fabric:/System/BackupRestoreService` e il servizio può essere individuato in Service Fabric Explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Abilita i backup periodici per servizio Reliable con stato e Reliable Actors
Seguire il procedimento per abilitare i backup periodici per servizio Reliable con stato e Reliable Actors. Questi passaggi presuppongono che
- Che il cluster sia installato utilizzando il certificato di sicurezza X.509 con _backup e ripristino del servizio_.
- Un servizio Reliable con stato venga distribuito nel cluster. Ai fini della presente Guida rapida, l'applicazione Uri è `fabric:/SampleApp` e il servizio Uri per il servizio Reliable con stato appartenente a questa applicazione è `fabric:/SampleApp/MyStatefulService`. Questo servizio viene distribuito con singola partizione e l'ID di partizione è `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.
- Il certificato client con ruolo di amministratore è installato in _My_ (_Personale_) nome di archivio _CurrentUser_ percorso del certificato dal computer da cui gli script seguenti verranno richiamati. Questo esempio utilizza `1b7ebe2174649c45474a4819dafae956712c31d3` come identificazione personale del certificato. Per altre informazioni sui certificati client, vedere [Controllo degli accessi in base al ruolo per i client di Service Fabric](service-fabric-cluster-security-roles.md){3}{4}.

### <a name="create-backup-policy"></a>Crea criterio di backup

Il primo passo consiste nel creare criteri di backup che descrivano la pianificazione del backup, la risorsa di archiviazione di destinazione per i dati di backup, il nome dei criteri e i backup incrementali massimi consentiti prima dell'avvio del backup completo e i criteri di conservazione per l'archivio di backup. 

Per l'archiviazione di backup, utilizzare l'archiviazione dell’account Azure creato in precedenza. Il contenitore `backup-container` è configurato per archiviare i backup. Un contenitore con questo nome viene creato, se non esiste già, durante il caricamento del backup. Popolare `ConnectionString` con una stringa di connessione valida per l'account di Archiviazione di Azure, sostituendo `account-name` con il nome dell'account di archiviazione, e `account-key` con la chiave dell'account di archiviazione.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell con il modulo Microsoft. ServiceFabric. PowerShell. http

Eseguire i cmdlet di PowerShell seguenti per creare nuovi criteri di backup. Sostituire `account-name` con il nome dell'account di archiviazione e `account-key` con la chiave dell'account di archiviazione.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>Chiamata REST con PowerShell

Eseguire lo script PowerShell seguente per richiamare le API REST necessarie per creare un nuovo criterio. Sostituire `account-name` con il nome dell'account di archiviazione e `account-key` con la chiave dell'account di archiviazione.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

```

### <a name="enable-periodic-backup"></a>Abilitare il backup periodico
Dopo aver definito il criterio di backup per soddisfare i requisiti di protezione dei dati dell'applicazione, il criterio di backup deve essere associato all'applicazione. A seconda del requisito, il criterio di backup può essere associato a un'applicazione, un servizio o una partizione.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell con il modulo Microsoft. ServiceFabric. PowerShell. http

```powershell

Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'

```
#### <a name="rest-call-using-powershell"></a>Chiamata REST con PowerShell

Eseguire lo script PowerShell seguente per richiamare l'API REST necessaria per associare il criterio di backup al nome `BackupPolicy1` creato nel passaggio precedente con l'applicazione `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

#### <a name="using-service-fabric-explorer"></a>Utilizzo di Service Fabric Explorer

1. Selezionare un'applicazione e passare ad azione. Fare clic su Abilita/Aggiorna backup applicazione.

    ![Abilitare il backup dell'applicazione][3]

2. Infine, selezionare il criterio desiderato e fare clic su Abilita backup.

    ![Seleziona criteri][4]


### <a name="verify-that-periodic-backups-are-working"></a>Verificare che i backup periodici funzionino

Dopo aver abilitato il backup a livello applicazione, tutte le partizioni appartenenti ai servizi Reliable con stato e Reliable Actors sotto l'applicazione inizieranno a essere sottoposte periodicamente a backup secondo il criterio di backup associato. 

![Evento di integrità del backup della partizione][0]

### <a name="list-backups"></a>Elenco dei backup

I backup associati a tutte le partizioni appartenenti ai servizi Reliable con stato e agli Reliable Actors dell'applicazione possono essere enumerati utilizzando gli API _GetBackups_. I backup possono essere enumerati per applicazione, servizio o partizione.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell con il modulo Microsoft. ServiceFabric. PowerShell. http

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>Chiamata REST con PowerShell

Eseguire il seguente script PowerShell per richiamare l'API HTTP ed elencare i backup creati per tutte le partizioni all'interno dell'applicazione `SampleApp`.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

#### <a name="using-service-fabric-explorer"></a>Utilizzo di Service Fabric Explorer

Per visualizzare i backup in Service Fabric Explorer, passare a una partizione e selezionare la scheda backup.

![Enumera backup][5]

## <a name="limitation-caveats"></a>Limitazioni/avvertenze
- Service Fabric cmdlet di PowerShell sono in modalità di anteprima.
- Nessun supporto per i cluster Service Fabric su Linux.

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni sulla configurazione del backup periodico](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Informazioni di riferimento sull'API REST di ripristino backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event-azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png