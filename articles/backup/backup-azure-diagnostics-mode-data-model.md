---
title: Modello di dati dei log di monitoraggio di Azure
description: Questo articolo illustra i dettagli del modello di dati Log Analytics di monitoraggio di Azure per i dati di backup di Azure.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: d14634c5e317682462e77e0549f064c75059f15c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586377"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modello di dati di Log Analytics per i dati di Backup di Azure

Usare il modello di dati Log Analytics per creare avvisi personalizzati da Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> Questo modello di dati si riferisce alla modalità Diagnostica di Azure di invio degli eventi di diagnostica a Log Analytics (LA). Per informazioni sul modello di dati per la nuova modalità specifica della risorsa, è possibile fare riferimento all'articolo [relativo al modello di dati per gli eventi di diagnostica di backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model) .

## <a name="using-azure-backup-data-model"></a>Uso del modello di dati di Backup di Azure

È possibile usare i campi seguenti forniti come parte del modello di dati per creare oggetti visivi, query personalizzate e dashboard in base alle esigenze.

### <a name="alert"></a>Avviso

Questa tabella offre dettagli sui campi relativi agli avvisi.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| AlertUniqueId_s |Testo |Identificatore univoco dell'avviso generato |
| AlertType_s |Testo |Tipo di avviso, ad esempio Backup |
| AlertStatus_s |Testo |Stato dell'avviso, ad esempio Active |
| AlertOccurrenceDateTime_s |Data/ora |Data e ora in cui è stato creato l'avviso |
| AlertSeverity_s |Testo |Gravità dell'avviso, ad esempio Critical |
|AlertTimeToResolveInMinutes_s    | Number        |Tempo impiegato per risolvere un avviso. Vuoto per gli avvisi attivi.         |
|AlertConsolidationStatus_s   |Testo         |Verificare se l'avviso è un avviso consolidato         |
|CountOfAlertsConsolidated_s     |Number         |Numero di avvisi consolidati se si tratta di un avviso consolidato          |
|AlertRaisedOn_s     |Testo         |Tipo di entità in cui viene generato l'avviso         |
|AlertCode_s     |Testo         |Codice per identificare in modo univoco un tipo di avviso         |
|RecommendedAction_s   |Testo         |Azione consigliata per risolvere l'avviso         |
| EventName_s |Testo |Nome dell'evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Testo |Identificatore univoco dell'elemento di backup associato all'avviso |
| SchemaVersion_s |Testo |Versione corrente dello schema, ad esempio **v2** |
| State_s |Testo |Stato corrente dell'oggetto avviso, ad esempio Active o Deleted |
| BackupManagementType_s |Testo |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo avviso |
| OperationName |Testo |Nome dell'operazione corrente, ad esempio, Avviso |
| Category |Testo |Categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure. Sempre AzureBackupReport |
| Risorsa |Testo |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedContainerUniqueId_s |Testo |Identificatore univoco del server protetto associato all'avviso (è stato ProtectedServerUniqueId_s in V1)|
| VaultUniqueId_s |Testo |Identificatore univoco dell'insieme di credenziali protette associato all'avviso |
| SourceSystem |Testo |Sistema di origine dei dati correnti - Azure |
| ResourceId |Testo |Identificatore univoco per la risorsa per ia quale vengono raccolti i dati. Ad esempio, un ID di risorsa dell'insieme di credenziali di servizi di ripristino |
| SubscriptionId |Testo |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Testo |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Testo |Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Testo |Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="backupitem"></a>BackupItem

Questa tabella offre dettagli sui campi relativi agli elementi di backup.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| EventName_s |Testo |Nome dell'evento. Sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Testo |Identificatore univoco dell'elemento di backup |
| BackupItemId_s |Testo |Identificatore dell'elemento di backup (questo campo è solo per lo schema v1) |
| BackupItemName_s |Testo |Nome dell'elemento di backup |
| BackupItemFriendlyName_s |Testo |Nome descrittivo dell'elemento di backup |
| BackupItemType_s |Testo |Tipo dell'elemento di backup, ad esempio macchina virtuale o FileFolder |
| BackupItemProtectionState_s |Testo |Stato di protezione dell'elemento di backup |
| BackupItemAppVersion_s |Testo |Versione dell'applicazione dell'elemento di backup |
| ProtectionState_s |Testo |Stato della protezione corrente dell'elemento di backup, ad esempio Protected o ProtectionStopped |
| ProtectionGroupName_s |Testo | Nome del gruppo protezione dati in cui è protetto l'elemento di backup, per SC DPM e MAB, se applicabile|
| SecondaryBackupProtectionState_s |Testo |Indica se la protezione secondaria è abilitata per l'elemento di backup|
| SchemaVersion_s |Testo |Versione dello schema, ad esempio **v2** |
| State_s |Testo |Stato dell'oggetto dell'elemento di backup, ad esempio Attivo o Eliminato |
| BackupManagementType_s |Testo |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo elemento di backup |
| OperationName |Testo |Nome dell'operazione, ad esempio BackupItem |
| Category |Testo |Categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure. Sempre AzureBackupReport |
| Risorsa |Testo |Risorsa per cui sono stati raccolti i dati, ad esempio il nome dell'insieme di credenziali dei Servizi di ripristino |
| SourceSystem |Testo |Sistema di origine dei dati correnti - Azure |
| ResourceId |Testo |ID risorsa per i dati raccolti, ad esempio l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| SubscriptionId |Testo |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per i dati raccolti |
| ResourceGroup |Testo |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per i dati raccolti |
| ResourceProvider |Testo |Provider di risorse per i dati raccolti, ad esempio, Microsoft. RecoveryServices |
| ResourceType |Testo |Tipo di risorsa per i dati raccolti, ad esempio, gli Insiemi di credenziali |

### <a name="backupitemassociation"></a>BackupItemAssociation

Questa tabella fornisce dettagli sulle associazioni degli elementi di backup con varie entità.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| EventName_s |Testo |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Testo |ID univoco dell'elemento di backup |
| SchemaVersion_s |Testo |Questo campo indica la versione corrente dello schema, ovvero **v2** |
| State_s |Testo |Stato corrente dell'oggetto di associazione dell'elemento di backup, ad esempio Active o Deleted |
| BackupManagementType_s |Testo |Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| BackupItemSourceSize_s |Testo | Dimensioni front-end dell'elemento di backup |
| BackupManagementServerUniqueId_s |Testo | Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile |
| Category |Testo |Questo campo rappresenta la categoria di dati di diagnostica inseriti di Log Analytics, ovvero AzureBackupReport |
| OperationName |Testo |Questo campo rappresenta il nome dell'operazione in corso - BackupItemAssociation |
| Risorsa |Testo |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedContainerUniqueId_s |Testo |Identificatore univoco del server protetto associato all'elemento di backup (è stato ProtectedServerUniqueId_s in V1) |
| VaultUniqueId_s |Testo |Identificatore univoco dell'insieme di credenziali contenente l'elemento di backup |
| SourceSystem |Testo |Sistema di origine dei dati correnti - Azure |
| ResourceId |Testo |Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| SubscriptionId |Testo |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per cui i dati sono stati raccolti |
| ResourceGroup |Testo |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per cui i dati sono stati raccolti |
| ResourceProvider |Testo |Provider di risorse per i dati raccolti, ad esempio, Microsoft. RecoveryServices |
| ResourceType |Testo |Tipo di risorsa per cui sono stati raccolti i dati, ad esempio, gli Insiemi di credenziali |

### <a name="backupmanagementserver"></a>BackupManagementServer

Questa tabella fornisce dettagli sulle associazioni degli elementi di backup con varie entità.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
|BackupManagementServerName_s     |Testo         |Nome del server di gestione di backup        |
|AzureBackupAgentVersion_s     |Testo         |Versione dell'agente di backup di Azure nel server di gestione di backup          |
|BackupManagementServerVersion_s     |Testo         |Versione del server di gestione di backup|
|BackupManagementServerOSVersion_s    |Testo            |Versione del sistema operativo del server di gestione di backup|
|BackupManagementServerType_s     |Testo         |Tipo di server di gestione di backup, come MAB, SC DPM|
|BackupManagementServerUniqueId_s     |Testo         |Per identificare in modo univoco il server di gestione di backup       |
| SourceSystem |Testo |Sistema di origine dei dati correnti - Azure |
| ResourceId |Testo |Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| SubscriptionId |Testo |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per cui i dati sono stati raccolti |
| ResourceGroup |Testo |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per cui i dati sono stati raccolti |
| ResourceProvider |Testo |Provider di risorse per i dati raccolti, ad esempio, Microsoft. RecoveryServices |
| ResourceType |Testo |Tipo di risorsa per cui sono stati raccolti i dati, ad esempio, gli Insiemi di credenziali |

### <a name="job"></a>Processo

Questa tabella offre dettagli sui campi relativi al processo.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| EventName_s |Testo |Nome dell'evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Testo |Identificatore univoco dell'elemento di backup |
| SchemaVersion_s |Testo |Versione dello schema, ad esempio **v2** |
| State_s |Testo |Stato corrente dell'oggetto processo, ad esempio Active o Deleted |
| BackupManagementType_s |Testo |Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| OperationName |Testo |Questo campo rappresenta il nome dell'operazione in corso - Processo |
| Category |Testo |Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure, è AzureBackupReport |
| Risorsa |Testo |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedServerUniqueId_s |Testo |Identificatore univoco del server protetto associato al processo |
| ProtectedContainerUniqueId_s |Testo | ID univoco per identificare il contenitore protetto in cui viene eseguito il processo |
| VaultUniqueId_s |Testo |Identificatore univoco dell'insieme di credenziali protette |
| JobOperation_s |Testo |Operazione per la quale viene eseguito il processo, ad esempio Backup, Restore o Configure Backup |
| JobStatus_s |Testo |Stato del processo completato, ad esempio Completed o Failed |
| JobFailureCode_s |Testo |Stringa di codice contenente un errore e a causa della quale si è verificato un errore del processo |
| JobStartDateTime_s |Data/ora |Data e ora di avvio dell'esecuzione del processo |
| BackupStorageDestination_s |Testo |Destinazione dell'archiviazione di backup, ad esempio Cloud o Disk  |
| AdHocOrScheduledJob_s |Testo | Campo per specificare se il processo è ad hoc o pianificato |
| JobDurationInSecs_s | Number |Durata totale del processo in secondi |
| DataTransferredInMB_s | Number |Dati trasferiti in MB per il processo|
| JobUniqueId_g |Testo |ID univoco per identificare il processo |
| RecoveryJobDestination_s |Testo | Destinazione di un processo di ripristino in cui vengono recuperati i dati |
| RecoveryJobRPDateTime_s |Datetime | Data, ora in cui è stato creato il punto di ripristino da ripristinare |
| RecoveryJobRPLocation_s |Testo | Posizione in cui è stato archiviato il punto di ripristino da ripristinare|
| SourceSystem |Testo |Sistema di origine dei dati correnti - Azure |
| ResourceId |Testo |Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino|
| SubscriptionId |Testo |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Testo |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Testo |Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Testo |Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="policy"></a>Condizione

Questa tabella offre dettagli sui campi relativi al criterio.

| Campo | Tipo di dati | Versioni applicabili | Descrizione |
| --- | --- | --- | --- |
| EventName_s |Testo ||Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Testo ||Questo campo indica la versione corrente dello schema, ovvero **v2** |
| State_s |Testo ||Stato corrente dell'oggetto criteri, ad esempio Active o Deleted |
| BackupManagementType_s |Testo ||Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| OperationName |Testo ||Questo campo rappresenta il nome dell'operazione in corso - Criterio |
| Category |Testo ||Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure, è AzureBackupReport |
| Risorsa |Testo ||Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| PolicyUniqueId_g |Testo ||ID univoco per identificare il criterio |
| PolicyName_s |Testo ||Nome dei criteri definiti |
| BackupFrequency_s |Testo ||Frequenza con cui vengono eseguiti i backup, ad esempio daily o weekly |
| BackupTimes_s |Testo ||Data e ora in cui sono pianificati backup |
| BackupDaysOfTheWeek_s |Testo ||Giorni della settimana per i quali sono stati pianificati backup |
| RetentionDuration_s |Numero intero ||Durata di mantenimento dei backup configurati |
| DailyRetentionDuration_s |Numero intero ||Durata totale di mantenimento dati in giorni per i backup configurati |
| DailyRetentionTimes_s |Testo ||Data e ora di configurazione del mantenimento dati giornaliero |
| WeeklyRetentionDuration_s |Numero decimale ||Durata totale del mantenimento dati settimanale in settimane per i backup configurati |
| WeeklyRetentionTimes_s |Testo ||Data e ora di configurazione del mantenimento dati settimanale |
| WeeklyRetentionDaysOfTheWeek_s |Testo ||Giorni della settimana selezionati per il mantenimento dati settimanale |
| MonthlyRetentionDuration_s |Numero decimale ||Durata totale di mantenimento dati in mesi per i backup configurati |
| MonthlyRetentionTimes_s |Testo ||Data e ora di configurazione del mantenimento dati mensile |
| MonthlyRetentionFormat_s |Testo ||Tipo di configurazione per il mantenimento dati mensile, ad esempio daily per la configurazione basata sui giorni o weekly per la configurazione basata sulle settimane |
| MonthlyRetentionDaysOfTheWeek_s |Testo ||Giorni della settimana selezionati per il mantenimento dati mensile |
| MonthlyRetentionWeeksOfTheMonth_s |Testo ||Settimane del mese per le quali è configurato il mantenimento dati mensile, ad esempio First, Last e così via |
| YearlyRetentionDuration_s |Numero decimale ||Durata totale di mantenimento dati in anni per i backup configurati |
| YearlyRetentionTimes_s |Testo ||Data e ora di configurazione del mantenimento dati annuale |
| YearlyRetentionMonthsOfTheYear_s |Testo ||Mesi dell'anno selezionati per il mantenimento dati annuale |
| YearlyRetentionFormat_s |Testo ||Tipo di configurazione per il mantenimento dati annuale, ad esempio daily per la configurazione basata sui giorni o weekly per la configurazione basata sulle settimane | |
| YearlyRetentionDaysOfTheMonth_s |Testo ||Date del mese selezionate per il mantenimento dati annuale |
| SynchronisationFrequencyPerDay_s |Numero intero |v2|Numero di volte in un giorno in cui è stato sincronizzato un backup di file per SC DPM e MAB |
| DiffBackupFormat_s |Testo |v2|Formato per i backup differenziali per SQL nel backup di macchine virtuali di Azure |
| DiffBackupTime_s |Tempo |v2|Tempo per i backup differenziali per SQL nel backup di macchine virtuali di Azure|
| DiffBackupRetentionDuration_s |Numero decimale |v2|Durata di conservazione per i backup differenziali per SQL nel backup di macchine virtuali di Azure|
| LogBackupFrequency_s |Numero decimale |v2|Frequenza dei backup del log per SQL|
| LogBackupRetentionDuration_s |Numero decimale |v2|Durata del periodo di memorizzazione per i backup del log per SQL nel backup di macchine virtuali di Azure|
| DiffBackupDaysofTheWeek_s |Testo |v2|Giorni della settimana per i backup differenziali per SQL nel backup di macchine virtuali di Azure|
| SourceSystem |Testo ||Sistema di origine dei dati correnti - Azure |
| ResourceId |Testo ||Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| SubscriptionId |Testo ||Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Testo ||Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Testo ||Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Testo ||Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="policyassociation"></a>PolicyAssociation

Questa tabella fornisce dettagli sulle associazioni dei criteri con varie entità.

| Campo | Tipo di dati | Versioni applicabili | Descrizione |
| --- | --- | --- | --- |
| EventName_s |Testo ||Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Testo ||Questo campo indica la versione corrente dello schema, ovvero **v2** |
| State_s |Testo ||Stato corrente dell'oggetto criteri, ad esempio Active o Deleted |
| BackupManagementType_s |Testo ||Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| OperationName |Testo ||Questo campo rappresenta il nome dell'operazione in corso - PolicyAssociation |
| Category |Testo ||Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure, è AzureBackupReport |
| Risorsa |Testo ||Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| PolicyUniqueId_g |Testo ||ID univoco per identificare il criterio |
| VaultUniqueId_s |Testo ||ID univoco dell'insieme di credenziali a cui appartiene questo criterio |
| BackupManagementServerUniqueId_s |Testo |v2 |Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile        |
| SourceSystem |Testo ||Sistema di origine dei dati correnti - Azure |
| ResourceId |Testo ||Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| SubscriptionId |Testo ||Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Testo ||Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Testo ||Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Testo ||Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="protected-container"></a>Contenitore protetto

Questa tabella fornisce i campi di base sui contenitori protetti. (ProtectedServer in V1)

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Testo | Campo per identificare in modo univoco un contenitore protetto |
| ProtectedContainerOSType_s |Testo |Tipo di sistema operativo del contenitore protetto |
| ProtectedContainerOSVersion_s |Testo |Versione del sistema operativo del contenitore protetto |
| AgentVersion_s |Testo |Numero di versione del backup dell'agente o dell'agente protezione (in caso di SC DPM e MAB) |
| BackupManagementType_s |Testo |Tipo di provider per l'esecuzione del backup. Ad esempio, IaaSVM, fileFolder |
| EntityState_s |Testo |Stato corrente dell'oggetto server protetto. Ad esempio, Active, Deleted |
| ProtectedContainerFriendlyName_s |Testo |Nome descrittivo del server protetto |
| ProtectedContainerName_s |Testo |Nome del contenitore protetto |
| ProtectedContainerWorkloadType_s |Testo |Tipo del contenitore protetto di cui è stato eseguito il backup. Ad esempio, IaaSVMContainer |
| ProtectedContainerLocation_s |Testo |Indica se il contenitore protetto si trova in locale o in Azure |
| ProtectedContainerType_s |Testo |Indica se il contenitore protetto è un server o un contenitore |
| ProtectedContainerProtectionState_s '  |Testo |Stato di protezione del contenitore protetto |

### <a name="storage"></a>Archiviazione

Questa tabella offre dettagli sui campi relativi all'archiviazione.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| CloudStorageInBytes_s |Numero decimale |Archiviazione di backup nel cloud usata dai backup, calcolata in base al valore più recente (questo campo è solo per lo schema v1)|
| ProtectedInstances_s |Numero decimale |Numero di istanze protette usato per il calcolo dell'archiviazione front-end a scopo di fatturazione, calcolato in base al valore più recente |
| EventName_s |Testo |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Testo |Questo campo indica la versione corrente dello schema, ovvero **v2** |
| State_s |Testo |Stato corrente dell'oggetto archiviazione, ad esempio Active o Deleted |
| BackupManagementType_s |Testo |Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| OperationName |Testo |Questo campo rappresenta il nome dell'operazione in corso - Archiviazione |
| Category |Testo |Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure, è AzureBackupReport |
| Risorsa |Testo |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedServerUniqueId_s |Testo |ID univoco del server protetto per cui viene calcolata l'archiviazione |
| VaultUniqueId_s |Testo |Viene calcolato l'ID univoco dell'insieme di credenziali per l'archiviazione |
| SourceSystem |Testo |Sistema di origine dei dati correnti - Azure |
| ResourceId |Testo |Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| SubscriptionId |Testo |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Testo |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Testo |Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Testo |Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |
| StorageUniqueId_s |Testo |ID univoco usato per identificare l'entità di archiviazione |
| StorageType_s |Testo |Tipo di archiviazione, ad esempio cloud, volume, disco |
| StorageName_s |Testo |Nome dell'entità di archiviazione, ad esempio E:\ |
| StorageTotalSizeInGBs_s |Testo |Dimensioni totali dello spazio di archiviazione, in GB, utilizzate dall'entità di archiviazione|

### <a name="storageassociation"></a>StorageAssociation

Questa tabella fornisce i campi di base relativi all'archiviazione che connettono l'archiviazione ad altre entità.

| Campo | Tipo di dati | Descrizione |
| --- | --- |  --- |
| StorageUniqueId_s |Testo |ID univoco usato per identificare l'entità di archiviazione |
| SchemaVersion_s |Testo |Questo campo indica la versione corrente dello schema, ovvero **v2** |
| BackupItemUniqueId_s |Testo |ID univoco usato per identificare l'elemento di backup correlato all'entità di archiviazione |
| BackupManagementServerUniqueId_s |Testo |ID univoco usato per identificare il server di gestione di backup correlato all'entità di archiviazione|
| VaultUniqueId_s |Testo |ID univoco usato per identificare l'insieme di credenziali correlato all'entità di archiviazione|
| StorageConsumedInMBs_s |Number|Dimensioni dello spazio di archiviazione utilizzato dall'elemento di backup corrispondente nell'archivio corrispondente |
| StorageAllocatedInMBs_s |Number |Dimensioni dello spazio di archiviazione allocato dall'elemento di backup corrispondente nello spazio di archiviazione corrispondente di tipo Disk|

### <a name="vault"></a>Insiemi di credenziali

Questa tabella offre dettagli sui campi relativi all'insieme di credenziali.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| EventName_s |Testo |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Testo |Questo campo indica la versione corrente dello schema, ovvero **v2** |
| State_s |Testo |Stato corrente dell'oggetto insieme di credenziali, ad esempio Active o Deleted |
| OperationName |Testo |Questo campo rappresenta il nome dell'operazione in corso - Insieme di credenziali |
| Category |Testo |Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure, è AzureBackupReport |
| Risorsa |Testo |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| VaultUniqueId_s |Testo |ID univoco dell'insieme di credenziali |
| VaultName_s |Testo |Nome dell'insieme di credenziali |
| AzureDataCenter_s |Testo |Data center in cui si trova l'insieme di credenziali |
| StorageReplicationType_s |Testo |Tipo di replica di archiviazione per l'insieme di credenziali, ad esempio GeoRedundant |
| SourceSystem |Testo |Sistema di origine dei dati correnti - Azure |
| ResourceId |Testo |Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| SubscriptionId |Testo |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Testo |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Testo |Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Testo |Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="backup-management-server"></a>Server di gestione di backup

Questa tabella fornisce i campi di base relativi ai server di gestione di backup.

|Campo  |Tipo di dati  | Descrizione  |
|---------|---------|----------|
|BackupManagementServerName_s     |Testo         |Nome del server di gestione di backup        |
|AzureBackupAgentVersion_s     |Testo         |Versione dell'agente di backup di Azure nel server di gestione di backup          |
|BackupManagementServerVersion_s     |Testo         |Versione del server di gestione di backup|
|BackupManagementServerOSVersion_s     |Testo            |Versione del sistema operativo del server di gestione di backup|
|BackupManagementServerType_s     |Testo         |Tipo di server di gestione di backup, come MAB, SC DPM|
|BackupManagementServerUniqueId_s     |Testo         |Per identificare in modo univoco il server di gestione di backup       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Questa tabella specifica i carichi di lavoro a cui è associato un volume.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| StorageUniqueId_s |Testo |ID univoco usato per identificare l'entità di archiviazione |
| BackupItemType_s |Testo |Carichi di lavoro per cui il volume è la risorsa di archiviazione preferita|

### <a name="protectedinstance"></a>ProtectedInstance

Questa tabella fornisce i campi correlati alle istanze protette di base.

| Campo | Tipo di dati |Versioni applicabili | Descrizione |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Testo |v2|ID univoco usato per identificare l'elemento di backup per le macchine virtuali di cui è stato eseguito il backup con DPM, MAB|
| ProtectedContainerUniqueId_s |Testo |v2|ID univoco usato per identificare il contenitore protetto per tutti gli elementi eccetto le macchine virtuali di cui è stato eseguito il backup con DPM, MAB|
| ProtectedInstanceCount_s |Testo |v2|Conteggio delle istanze protette per l'elemento di backup associato o il contenitore protetto in tale data e ora|

### <a name="recoverypoint"></a>RecoveryPoint

Questa tabella fornisce i campi di base relativi ai punti di ripristino.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| BackupItemUniqueId_s |Testo |ID univoco usato per identificare l'elemento di backup per le macchine virtuali di cui è stato eseguito il backup con DPM, MAB|
| OldestRecoveryPointTime_s |Testo |Data e ora del punto di ripristino meno recente per l'elemento di backup|
| OldestRecoveryPointLocation_s |Testo |Posizione del punto di ripristino meno recente per l'elemento di backup|
| LatestRecoveryPointTime_s |Testo |Data e ora dell'ultimo punto di ripristino per l'elemento di backup|
| LatestRecoveryPointLocation_s |Testo |Posizione del punto di ripristino più recente per l'elemento di backup|

## <a name="sample-kusto-queries"></a>Query kusto di esempio

Di seguito sono riportati alcuni esempi che consentono di scrivere query sui dati di backup di Azure che si trovano nella tabella Diagnostica di Azure:

- Tutti i processi di backup riusciti

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Tutti i processi di backup non riusciti

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Tutti i processi di backup delle VM di Azure riusciti

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Tutti i processi di backup del log SQL riusciti

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Tutti i processi dell'agente di backup di Azure riusciti

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````
    
## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato il modello di dati, è possibile iniziare a [creare query personalizzate](../azure-monitor/learn/tutorial-logs-dashboards.md) nei log di monitoraggio di Azure per creare un dashboard personalizzato.
