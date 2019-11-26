---
title: Informazioni sulla configurazione del backup periodico in Azure Service Fabric | Microsoft Docs
description: Usare la funzionalità di backup e ripristino periodici di Service Fabric per abilitare il backup periodico dei dati delle applicazioni.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: chackdan
editor: hrushib
ms.assetid: FAA45B4A-0258-4CB3-A825-7E8F70F28401
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: hrushib
ms.openlocfilehash: e0c40c005c27130d422e0dacaae29461b65b7df7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232508"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Informazioni sulla configurazione del backup periodico in Azure Service Fabric

La configurazione del backup periodico dei servizi Reliable con stato o dei Reliable Actors è costituita dai passaggi seguenti:

1. **Creazione di criteri di backup**: in questa fase vengono creati uno o più criteri di backup, a seconda dei requisiti.

2. **Abilitazione del backup**: in questa fase i criteri di backup creati nel **passaggio 1** vengono associati alle entità corrispondenti: _Applicazione_, _Servizio_ o _Partizione_.

## <a name="create-backup-policy"></a>Crea criterio di backup

Un criterio di backup è costituito dalle configurazioni seguenti:

* **Auto restore on data loss** (Ripristino automatico per perdita di dati): specifica se attivare automaticamente il ripristino usando l'ultimo backup disponibile quando la partizione registra un evento di perdita di dati.

* **Max incremental backups** (N. massimo di backup incrementali): definisce il numero massimo di backup incrementali eseguibili tra due backup completi. Max incremental backups (N. massimo di backup incrementali) specifica il limite massimo. Un backup completo può essere eseguito prima del raggiungimento del numero di backup incrementali specificato se si verifica una delle situazioni seguenti:

    1. La replica non ha mai eseguito un backup completo dopo essere diventata primaria.

    2. Alcuni record di log successivi all'ultimo backup sono stati troncati.

    3. La replica ha superato il limite specificato in MaxAccumulatedBackupLogSizeInMB.

* **Pianificazione backup**: cadenza o frequenza di esecuzione dei backup periodici. È possibile pianificare l'esecuzione di backup a intervalli specificati o con un orario fisso ogni giorno o ogni settimana.

    1. **Frequency-based backup schedule** (Pianificazione backup basata sulla frequenza): questo tipo di pianificazione può essere usato quando è necessario eseguire il backup dei dati a intervalli fissi. L'intervallo di tempo desiderato tra due backup successivi può essere definito usando il formato ISO 8601. La pianificazione backup basata sulla frequenza supporta la risoluzione di intervalli fino a un minuto.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Time-based backup schedule** (Pianificazione backup basata su tempo): questo tipo di pianificazione può essere usato quando è necessario eseguire il backup dei dati in orari specifici ogni giorno o ogni settimana. Il tipo di frequenza di pianificazione può essere giornaliero o settimanale.
        1. **_Daily_ Time-based backup schedule** (Pianificazione backup basata su tempo - Giornaliera): questo tipo di pianificazione va usata quando è necessario eseguire il backup dei dati in orari specifici della giornata. Per fare ciò impostare `ScheduleFrequencyType` su _Giornaliera_ e impostare `RunTimes` sull'elenco di orari della giornata desiderati, in formato ISO 8601. La data specificata insieme all'ora viene ignorata. Ad esempio `0001-01-01T18:00:00` rappresenta le _18:00_ di ogni giorno e la parte della data _0001-01-01_ viene ignorata. L'esempio seguente visualizza la configurazione che attiva un backup giornaliero alle_9:00_ e alle _18:00_ di ogni giorno.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. **_Weekly_ Time-based backup schedule** (Pianificazione backup basata su tempo - Settimanale): questo tipo di pianificazione può essere usato quando è necessario eseguire il backup dei dati in orari specifici ogni giorno. Per fare ciò impostare `ScheduleFrequencyType` su _Settimanale_, quindi impostare `RunDays` sull'elenco dei giorni della settimana in cui va attivato il backup e impostare `RunTimes` sull'elenco di orari della giornata desiderati, in formato ISO 8601. La data specificata insieme all'ora viene ignorata. Elenco dei giorni della settimana in cui attivare il backup periodico. L'esempio seguente visualizza una configurazione che attiva il backup giornaliero alle _9:00_ e alle _18.00_ ogni giorno da lunedì a venerdì.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **Archivio backup**: specifica il percorso in cui caricare i backup. La destinazione di archiviazione può essere l'archivio BLOB di Azure o una condivisione di file.
    1. **Azure blob store** (Archivio BLOB di Azure): questo tipo di archiviazione va selezionato quando è necessario archiviare in Azure i backup generati. Questo tipo di archiviazione può essere usato sia dai cluster _autonomi_ che da quelli _basati su Azure_. La descrizione di questo tipo di archiviazione richiede la stringa di connessione e il nome del contenitore in cui devono essere caricati i backup. Se il contenitore con il nome specificato non è disponibile, viene creato durante il caricamento di un backup.
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **Condivisione file**: questo tipo di archiviazione deve essere selezionato per i cluster _autonomi_ quando è necessario archiviare il backup dei dati in locale. La descrizione di questo tipo di archiviazione richiede il percorso di condivisione file in cui devono essere caricati i backup. L'accesso alla condivisione file può essere configurato usando una delle seguenti opzioni
        1. _Autenticazione di Windows integrata_: l'accesso alla condivisione di file è consentito a tutti i computer appartenenti al cluster Service Fabric. In questo caso impostare i campi seguenti per configurare l'archiviazione di backup basata sulla _condivisione file_.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Protecting file share using user name and password_ (Condivisione file protetta da nome utente e password): l'accesso alla condivisione file è consentito a utenti specifici. La specifica di archiviazione basata sulla condivisione file consente anche di specificare un nome utente secondario e una password secondaria come credenziali di fallback, nel caso in cui l'autenticazione con il nome utente primario e la password primaria abbia esito negativo. In questo caso impostare i campi seguenti per configurare l'archiviazione di backup basata sulla _condivisione file_.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> Verificare che l'affidabilità di archiviazione soddisfi o superi i requisiti di affidabilità dei dati di backup.
>

* **Criteri di conservazione**: specifica i criteri per conservare i backup nell'archiviazione configurata. Sono supportati solo i criteri di conservazione di base.
    1. **Criteri di conservazione di base**: questi criteri di conservazione consentono di garantire un utilizzo ottimale dello spazio di archiviazione rimuovendo i file di backup che non sono più necessari. Specificare `RetentionDuration` per impostare l'intervallo di tempo in cui i backup devono essere conservati nella risorsa di archiviazione. `MinimumNumberOfBackups` è un parametro facoltativo che può essere specificato per assicurarsi che venga sempre mantenuto il numero specificato di backup indipendentemente da `RetentionDuration`. L'esempio seguente illustra la configurazione che consente di conservare i backup per _10_ giorni e non consente che il numero di backup scenda a meno di _20_.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Abilitare il backup periodico
Dopo aver definito i criteri di backup in modo da soddisfare i requisiti per il backup dei dati, è necessario associare i criteri di backup a un'_applicazione_, a un _servizio_ o a una _partizione_.

### <a name="hierarchical-propagation-of-backup-policy"></a>Propagazione gerarchica dei criteri di backup
In Service Fabric la relazione tra applicazione, servizio e partizioni è gerarchica, come illustrato in [Modello applicativo](./service-fabric-application-model.md). Il criterio di backup può essere associato a un'_applicazione_, un _servizio_ o una _partizione_ nella gerarchia. Il criterio si propaga gerarchicamente al livello successivo. Se ad esempio è presente un solo criterio di backup associato a un'_applicazione_, tutte le partizioni con stato appartenenti a tutti i _servizi Reliable con stato_ e i _Reliable Actors_ dell'_applicazione_ vengono sottoposte a backup usando il criterio di backup. Se invece il criterio di backup è associato a un _servizio Reliable con stato_, tutte le partizioni del servizio vengono sottoposte a backup usando il criterio di backup.

### <a name="overriding-backup-policy"></a>Override dei criteri di backup
In un determinato scenario è possibile che il backup dei dati con la stessa pianificazione sia idoneo per tutti i servizi dell'applicazione ad eccezione di servizi specifici, che richiedono ad esempio una pianificazione più frequente o un backup in un account di archiviazione o una condivisione di file diversa. Per la risoluzione di queste situazioni, il servizio di ripristino del backup dispone di un'opzione per eseguire l'override del criterio propagato a livello del servizio e della partizione. Quando il criterio di backup è associato a un _servizio_ o una _partizione_, esegue l'override del criterio di backup propagato, se presente.

### <a name="example"></a>Esempio

Questo esempio usa una configurazione con due applicazioni, _MyApp_A_ e _MyApp_B_. L'applicazione _MyApp_A_ contiene due servizi Reliable con stato, _SvcA1_ & _SvcA3_ e un servizio Reliable Actor, _ActorA2_. _SvcA1_ contiene tre partizioni, mentre _ActorA2_ e _SvcA3_ contengono due partizioni ciascuno.  L'applicazione _MyApp_B_ contiene tre servizi Reliable con stato: _SvcB1_, _SvcB2_ e _SvcB3_. _SvcB1_ e _SvcB2_ contengono due partizioni ciascuno, mentre _SvcB3_ contiene tre partizioni.

Si supponga che i requisiti di backup dei dati di queste applicazioni siano i seguenti

1. MyApp_A
    1. Creare un backup giornaliero dei dati per tutte le partizioni di tutti i _servizi Reliable con stato_ e i _Reliable Actors_ appartenenti all'applicazione. Caricare i dati di backup nella posizione _BackupStore1_.

    2. Uno dei servizi, _SvcA3_, richiede il backup dei dati ogni ora.

    3. Il volume dei dati nella partizione _SvcA1_P2_ è superiore a quello previsto e i dati di backup corrispondenti devono essere archiviati in un'altra posizione di archiviazione, _BackupStore2_.

2. MyApp_B
    1. Creare il backup dei dati ogni domenica alle ore 8:00 per tutte le partizioni del servizio _SvcB1_. Caricare i dati di backup nella posizione _BackupStore1_.

    2. Creare il backup dei dati ogni giorno alle ore 8:00 per la partizione _SvcB2_P1_. Caricare i dati di backup nella posizione _BackupStore1_.

Per soddisfare questi requisiti di backup dei dati, vengono creati i criteri di backup da BP_1 a BP_5 e il backup viene implementato come indicato di seguito.
1. MyApp_A
    1. Creare un criterio di backup _BP_1_ con pianificazione del backup basata sulla frequenza (in cui la frequenza è impostata su 24 ore) e archiviazione del backup configurata per l'uso del percorso di archiviazione _BackupStore1_. Abilitare questo criterio per l'applicazione _MyApp_A_ usando l'API [Enable Application Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableapplicationbackup). Questa azione abilita il backup dei dati usando il criterio di backup _BP_1_ per tutte le partizioni di _servizi Reliable con stato_ e _Reliable Actors_ appartenenti all'applicazione  _MyApp_A_.

    2. Creare un criterio di backup _BP_2_ con pianificazione del backup basata sulla frequenza (in cui la frequenza è impostata su 1 ora) e archiviazione del backup configurata per l'uso del percorso di archiviazione _BackupStore1_. Abilitare questo criterio per il servizio _SvcA3_ usando l'API [Enable Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup). Questa azione esegue l'override del criterio propagato _BP_1_ abilitando esplicitamente il criterio di backup _BP_2_ per tutte le partizioni del servizio _SvcA3_: per queste partizioni viene usato il criterio di backup _BP_2_.

    3. Creare un criterio di backup _BP_3_ con pianificazione del backup basata sulla frequenza (in cui la frequenza è impostata su 24 ore) e archiviazione del backup configurata per l'uso del percorso di archiviazione _BackupStore2_. Abilitare questo criterio per la partizione _SvcA1_P2_ usando l'API [Enable Partition Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup). Questa azione esegue l'override del criterio propagato _BP_1_ e abilita in modo esplicito il criterio di backup _BP_3_ per la partizione _SvcA1_P2_.

2. MyApp_B
    1. Creare il criterio di backup _BP_4_ con pianificazione di backup basata su tempo, tipo di frequenza di pianificazione impostato su Settimanale, giorno di esecuzione impostato sulla domenica e orario di esecuzione impostato sulle 8:00. Archivio di backup configurato per l'uso del percorso di archiviazione _BackupStore1_. Abilitare questo criterio per il servizio _SvcB1_ usando l'API [Enable Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup). Questa azione abilita il backup dei dati usando il criterio di backup _BP_4_ per tutte le partizioni del servizio _SvcB1_.

    2. Creare il criterio di backup _BP_5_ con pianificazione di backup basata su tempo, tipo di frequenza di pianificazione impostato su Giornaliera, giorno di esecuzione impostato sulla domenica e orario di esecuzione impostato sulle 8:00. Archivio di backup configurato per l'uso del percorso di archiviazione _BackupStore1_. Abilitare questo criterio per la partizione _SvcB2_P1_ usando l'API [Enable Partition Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup). Questa azione abilita il backup dei dati usando il criterio di backup _BP_5_ per la partizione _SvcB2_P1_.

Il diagramma seguente visualizza i criteri di backup abilitati in modo esplicito e i criteri di backup propagati.

![Gerarchia dell'applicazione in Service Fabric][0]

## <a name="disable-backup"></a>Disabilitare il backup
È possibile disabilitare i criteri di backup quando non è necessario eseguire il backup dei dati. Un criterio di backup abilitato per un'_applicazione_ può essere disabilitato solo nella stessa _applicazione_ usando l'API [Disable Application Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableapplicationbackup); un criterio di backup abilitato per un _servizio_ può essere disabilitato nello stesso _servizio_ usando l'API [Disable Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableservicebackup); un criterio di backup abilitato per una _partizione_ può essere disabilitato nella stessa _partizione_ usando l'API [Disable Partition Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disablepartitionbackup).

* La disabilitazione di un criterio di backup per un'_applicazione_ arresta tutti i backup dei dati periodici che si verificano in seguito alla propagazione del criterio di backup alle partizioni del servizio Reliable con stato o alle partizioni Reliable Actor.

* La disabilitazione di un criterio di backup per un _servizio_ arresta tutti i backup dei dati periodici che si verificano in seguito alla propagazione del criterio di backup alle partizioni del _servizio_.

* La disabilitazione di un criterio di backup per una _partizione_ arresta tutti i backup periodici dei dati eseguiti nella partizione in base a tale criterio.

* Durante la disabilitazione del backup per un'entità (applicazione/servizio/partizione) è possibile impostare `CleanBackup` su _true_ per eliminare tutti i backup nella risorsa di archiviazione configurata.
    ```json
    {
        "CleanBackup": true 
    }
    ```

## <a name="suspend--resume-backup"></a>Sospendere e riprendere il backup
In determinati casi può risultare necessario sospendere temporaneamente il backup periodico dei dati. In tali situazioni, a seconda delle esigenze, è possibile usare l'API di sospensione del backup a livello di _applicazione_, _servizio_ o _partizione_. La sospensione del backup periodico è transitiva per il sottoalbero della gerarchia dell'applicazione dal punto in cui viene applicata. 

* Se la sospensione viene implementata in un'_applicazione_ usando l'API [Suspend Application Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendapplicationbackup), il backup periodico dei dati viene sospeso per tutti i servizi e le partizioni sotto tale applicazione.

* Se la sospensione viene implementata in un _servizio_ usando l'API [Suspend Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendservicebackup), il backup periodico dei dati viene sospeso per tutte le partizioni sotto tale servizio.

* Se la sospensione viene implementata in una _partizione_ usando l'API [Suspend Partition Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendpartitionbackup), il backup periodico dei dati viene sospeso per tutti i dati inclusi in tale partizione.

Quando la sospensione non è più necessaria è possibile ripristinare il backup periodico dei dati usando le rispettive API di ripresa del backup. Il backup periodico deve essere ripresto nella stessa _applicazione_, _servizio_ o _partizione_ in cui è stato sospeso.

* Se la sospensione è stata applicata in un'_applicazione_, il backup deve essere ripreso usando l'API [Resume Application Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeapplicationbackup). 

* Se la sospensione è stata applicata in un _servizio_, il backup deve essere ripreso usando l'API [Resume Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeservicebackup).

* Se la sospensione è stata applicata in una _partizione_, il backup deve essere ripreso usando l'API [Resume Partition Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumepartitionbackup).

### <a name="difference-between-suspend-and-disable-backups"></a>Differenza tra sospensione e disabilitazione dei backup
È opportuno usare la funzionalità di disabilitazione dei backup quando i backup non sono più necessari per un'applicazione, un servizio o una partizione specifica. È possibile anche richiamare una richiesta di disabilitazione dei backup insieme al parametro di pulizia dei backup per essere certi che vengano eliminati anche tutti i backup esistenti. È opportuno invece usare la funzionalità di sospensione nei casi in cui si intende disattivare temporaneamente i backup, ad esempio quando il disco locale è pieno o il caricamento di una copia di backup non riesce a causa di un problema di rete e così via. 

Mentre la disabilitazione può essere richiamata solo a un livello precedentemente abilitato in modo esplicito per il backup, la sospensione può essere applicata a qualsiasi livello attualmente abilitato per il backup, direttamente o tramite ereditarietà/gerarchia. Se, ad esempio, il backup viene abilitato al livello di un'applicazione, è possibile richiamare la disabilitazione solo al livello dell'applicazione, mentre la sospensione può essere richiamata anche per qualsiasi servizio o partizione presente nell'applicazione. 

## <a name="auto-restore-on-data-loss"></a>Ripristino automatico in caso di perdita di dati
La partizione del servizio potrebbe perdere dati a causa di errori imprevisti. Ad esempio, il disco per due su tre repliche per una partizione, inclusa la replica primaria, viene danneggiato o cancellato.

Quando Service Fabric rileva che nella partizione si verifica la perdita di dati, chiama il metodo di interfaccia `OnDataLossAsync` per la partizione e prevede che la partizione esegua l'azione necessaria per bloccare la perdita di dati. In questa situazione, se il flag `AutoRestoreOnDataLoss` del criterio di backup in vigore nella partizione è impostato su `true` il ripristino viene attivato automaticamente usando il backup disponibile più recente per la partizione.

## <a name="get-backup-configuration"></a>Ottenere la configurazione del backup
API diverse consentono di ottenere informazioni di configurazione del backup a livello di _applicazione_, _servizio_ e _partizione_. Le API sono rispettivamente [Get Application Backup Configuration Info](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo), [Get Service Backup Configuration Info](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo) e [Get Partition Backup Configuration Info](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo). In generale queste API restituiscono il criterio di backup applicabile, l'ambito di applicazione del criterio e i dettagli della sospensione del backup. Segue una breve descrizione dei risultati restituiti da queste API.

- Get Application Backup Configuration Info: specifica i dettagli del criterio di backup applicato all'applicazione e di tutti i criteri sottoposti a override nei servizi e nelle partizioni appartenenti all'applicazione. Include anche informazioni di sospensione per l'applicazione e i servizi e le partizioni corrispondenti.

- Get Service Backup Configuration Info: specifica i dettagli del criterio di backup in vigore nel servizio, l'ambito in cui il criterio è stato applicato e tutti i criteri sottoposti a override nelle partizioni del servizio. Include anche le informazioni di sospensione per il servizio e le relative partizioni.

- Get Partition Backup Configuration Info: specifica i dettagli del criterio di backup in vigore nella partizione e l'ambito in cui il criterio è stato applicato. Include anche le informazioni di sospensione per le partizioni.

## <a name="list-available-backups"></a>Elencare i backup disponibili

È possibile elencare i backup disponibili tramite l'API Get Backup List. La chiamata dell'API restituisce informazioni su tutti i backup disponibili nell'archivio di backup, configurato nei criteri di backup applicabili. Sono disponibili diverse varianti dell'API, che consentono di elencare i backup disponibili appartenenti a un'applicazione, un servizio o una partizione. Queste API supportano il recupero del backup disponibile _più recente_ di tutte le partizioni applicabili o il filtraggio dei backup in base alla _data di inizio_ e alla _data fine_.

Le API supportano anche l'impaginazione dei risultati: quando il parametro _MaxResults_ è impostato su un numero intero positivo diverso da zero, l'API restituisce un numero massimo di voci informative sul backup pari a _MaxResults_. Se il numero di voci informative sul backup è superiore al valore di _MaxResults_ viene restituito un token di continuazione. Il parametro del token di continuazione valido può essere usato per ottenere il set di risultati successivo. Quando il valore del token di continuazione valido viene passato alla chiamata successiva dell'API, l'API restituisce il set di risultati successivo. Se vengono restituiti tutti i risultati disponibili, la risposta non include un token di continuazione.

Di seguito sono elencate informazioni concise sulle varianti supportate.

- [Get Application Backup List](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): restituisce l'elenco dei backup disponibili per ogni partizione appartenente a un'applicazione Service Fabric specifica.

- [Get Service Backup List](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackuplist): restituisce l'elenco dei backup disponibili per ogni servizio appartenente a un'applicazione Service Fabric specifica.
 
- [Get Partition Backup List](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): restituisce l'elenco dei backup disponibili per la partizione specificata.

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni di riferimento sull'API REST di ripristino backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png
