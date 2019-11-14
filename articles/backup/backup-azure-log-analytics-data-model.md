---
title: Modello di dati dei log di monitoraggio di Azure per backup di Azure
description: Questo articolo illustra i dettagli del modello di dati Log Analytics di monitoraggio di Azure per i dati di backup di Azure.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: dacurwin
ms.openlocfilehash: 283dc4c1ad4bc683833da3d689d842fa84079a00
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074938"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modello di dati di Log Analytics per i dati di Backup di Azure

Usare il modello di dati Log Analytics per creare avvisi personalizzati da Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>Uso del modello di dati di Backup di Azure

È possibile usare i campi seguenti forniti come parte del modello di dati per creare oggetti visivi, query personalizzate e dashboard in base alle esigenze.

### <a name="alert"></a>Avviso

Questa tabella offre dettagli sui campi relativi agli avvisi.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| AlertUniqueId_s |Text |Identificatore univoco dell'avviso generato |
| AlertType_s |Text |Tipo di avviso, ad esempio Backup |
| AlertStatus_s |Text |Stato dell'avviso, ad esempio Active |
| AlertOccurrenceDateTime_s |Data/ora |Data e ora in cui è stato creato l'avviso |
| AlertSeverity_s |Text |Gravità dell'avviso, ad esempio Critical |
|AlertTimeToResolveInMinutes_s    | NUMBER        |Tempo impiegato per risolvere un avviso. Vuoto per gli avvisi attivi.         |
|AlertConsolidationStatus_s   |Text         |Verificare se l'avviso è un avviso consolidato         |
|CountOfAlertsConsolidated_s     |NUMBER         |Numero di avvisi consolidati se si tratta di un avviso consolidato          |
|AlertRaisedOn_s     |Text         |Tipo di entità in cui viene generato l'avviso         |
|AlertCode_s     |Text         |Codice per identificare in modo univoco un tipo di avviso         |
|RecommendedAction_s   |Text         |Azione consigliata per risolvere l'avviso         |
| EventName_s |Text |Nome dell'evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Identificatore univoco dell'elemento di backup associato all'avviso |
| SchemaVersion_s |Text |Versione corrente dello schema, ad esempio **v2** |
| State_s |Text |Stato corrente dell'oggetto avviso, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo avviso |
| OperationName |Text |Nome dell'operazione corrente, ad esempio, Avviso |
| Categoria |Text |Categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure. Sempre AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedContainerUniqueId_s |Text |Identificatore univoco del server protetto associato all'avviso (è stato ProtectedServerUniqueId_s in V1)|
| VaultUniqueId_s |Text |Identificatore univoco dell'insieme di credenziali protette associato all'avviso |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore univoco per la risorsa per ia quale vengono raccolti i dati. Ad esempio, un ID di risorsa dell'insieme di credenziali di servizi di ripristino |
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text |Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text |Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="backupitem"></a>BackupItem

Questa tabella offre dettagli sui campi relativi agli elementi di backup.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| EventName_s |Text |Nome dell'evento. Sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Identificatore univoco dell'elemento di backup |
| BackupItemId_s |Text |Identificatore dell'elemento di backup (questo campo è solo per lo schema v1) |
| BackupItemName_s |Text |Nome dell'elemento di backup |
| BackupItemFriendlyName_s |Text |Nome descrittivo dell'elemento di backup |
| BackupItemType_s |Text |Tipo dell'elemento di backup, ad esempio macchina virtuale o FileFolder |
| BackupItemProtectionState_s |Text |Stato di protezione dell'elemento di backup |
| BackupItemAppVersion_s |Text |Versione dell'applicazione dell'elemento di backup |
| ProtectionState_s |Text |Stato della protezione corrente dell'elemento di backup, ad esempio Protected o ProtectionStopped |
| ProtectionGroupName_s |Text | Nome del gruppo protezione dati in cui è protetto l'elemento di backup, per SC DPM e MAB, se applicabile|
| SecondaryBackupProtectionState_s |Text |Indica se la protezione secondaria è abilitata per l'elemento di backup|
| SchemaVersion_s |Text |Versione dello schema, ad esempio **v2** |
| State_s |Text |Stato dell'oggetto dell'elemento di backup, ad esempio Attivo o Eliminato |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo elemento di backup |
| OperationName |Text |Nome dell'operazione, ad esempio BackupItem |
| Categoria |Text |Categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure. Sempre AzureBackupReport |
| Risorsa |Text |Risorsa per cui sono stati raccolti i dati, ad esempio il nome dell'insieme di credenziali dei Servizi di ripristino |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |ID risorsa per i dati raccolti, ad esempio l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per i dati raccolti |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per i dati raccolti |
| ResourceProvider |Text |Provider di risorse per i dati raccolti, ad esempio, Microsoft. RecoveryServices |
| ResourceType |Text |Tipo di risorsa per i dati raccolti, ad esempio, gli Insiemi di credenziali |

### <a name="backupitemassociation"></a>BackupItemAssociation

Questa tabella fornisce dettagli sulle associazioni degli elementi di backup con varie entità.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |ID univoco dell'elemento di backup |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **v2** |
| State_s |Text |Stato corrente dell'oggetto di associazione dell'elemento di backup, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| BackupItemSourceSize_s |Text | Dimensioni front-end dell'elemento di backup |
| BackupManagementServerUniqueId_s |Text | Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile |
| Categoria |Text |Questo campo rappresenta la categoria di dati di diagnostica inseriti di Log Analytics, ovvero AzureBackupReport |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - BackupItemAssociation |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedContainerUniqueId_s |Text |Identificatore univoco del server protetto associato all'elemento di backup (è stato ProtectedServerUniqueId_s in V1) |
| VaultUniqueId_s |Text |Identificatore univoco dell'insieme di credenziali contenente l'elemento di backup |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per cui i dati sono stati raccolti |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per cui i dati sono stati raccolti |
| ResourceProvider |Text |Provider di risorse per i dati raccolti, ad esempio, Microsoft. RecoveryServices |
| ResourceType |Text |Tipo di risorsa per cui sono stati raccolti i dati, ad esempio, gli Insiemi di credenziali |

### <a name="backupmanagementserver"></a>BackupManagementServer

Questa tabella fornisce dettagli sulle associazioni degli elementi di backup con varie entità.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Nome del server di gestione di backup        |
|AzureBackupAgentVersion_s     |Text         |Versione dell'agente di backup di Azure nel server di gestione di backup          |
|BackupManagementServerVersion_s     |Text         |Versione del server di gestione di backup|
|BackupManagementServerOSVersion_s    |Text            |Versione del sistema operativo del server di gestione di backup|
|BackupManagementServerType_s     |Text         |Tipo di server di gestione di backup, come MAB, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Per identificare in modo univoco il server di gestione di backup       |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per cui i dati sono stati raccolti |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per cui i dati sono stati raccolti |
| ResourceProvider |Text |Provider di risorse per i dati raccolti, ad esempio, Microsoft. RecoveryServices |
| ResourceType |Text |Tipo di risorsa per cui sono stati raccolti i dati, ad esempio, gli Insiemi di credenziali |

### <a name="job"></a>processo

Questa tabella offre dettagli sui campi relativi al processo.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| EventName_s |Text |Nome dell'evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Identificatore univoco dell'elemento di backup |
| SchemaVersion_s |Text |Versione dello schema, ad esempio **v2** |
| State_s |Text |Stato corrente dell'oggetto processo, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - Processo |
| Categoria |Text |Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure, è AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedServerUniqueId_s |Text |Identificatore univoco del server protetto associato al processo |
| ProtectedContainerUniqueId_s |Text | ID univoco per identificare il contenitore protetto in cui viene eseguito il processo |
| VaultUniqueId_s |Text |Identificatore univoco dell'insieme di credenziali protette |
| JobOperation_s |Text |Operazione per la quale viene eseguito il processo, ad esempio Backup, Restore o Configure Backup |
| JobStatus_s |Text |Stato del processo completato, ad esempio Completed o Failed |
| JobFailureCode_s |Text |Stringa di codice contenente un errore e a causa della quale si è verificato un errore del processo |
| JobStartDateTime_s |Data/ora |Data e ora di avvio dell'esecuzione del processo |
| BackupStorageDestination_s |Text |Destinazione dell'archiviazione di backup, ad esempio Cloud o Disk  |
| AdHocOrScheduledJob_s |Text | Campo per specificare se il processo è ad hoc o pianificato |
| JobDurationInSecs_s | NUMBER |Durata totale del processo in secondi |
| DataTransferredInMB_s | NUMBER |Dati trasferiti in MB per il processo|
| JobUniqueId_g |Text |ID univoco per identificare il processo |
| RecoveryJobDestination_s |Text | Destinazione di un processo di ripristino in cui vengono recuperati i dati |
| RecoveryJobRPDateTime_s |DateTime | Data, ora in cui è stato creato il punto di ripristino da ripristinare |
| RecoveryJobRPLocation_s |Text | Posizione in cui è stato archiviato il punto di ripristino da ripristinare|
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino|
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text |Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text |Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="policy"></a>Criteri

Questa tabella offre dettagli sui campi relativi al criterio.

| Campo | Tipo di dati | Versioni applicabili | DESCRIZIONE |
| --- | --- | --- | --- |
| EventName_s |Text ||Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text ||Questo campo indica la versione corrente dello schema, ovvero **v2** |
| State_s |Text ||Stato corrente dell'oggetto criteri, ad esempio Active o Deleted |
| BackupManagementType_s |Text ||Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| OperationName |Text ||Questo campo rappresenta il nome dell'operazione in corso - Criterio |
| Categoria |Text ||Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure, è AzureBackupReport |
| Risorsa |Text ||Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| PolicyUniqueId_g |Text ||ID univoco per identificare il criterio |
| PolicyName_s |Text ||Nome dei criteri definiti |
| BackupFrequency_s |Text ||Frequenza con cui vengono eseguiti i backup, ad esempio daily o weekly |
| BackupTimes_s |Text ||Data e ora in cui sono pianificati backup |
| BackupDaysOfTheWeek_s |Text ||Giorni della settimana per i quali sono stati pianificati backup |
| RetentionDuration_s |Numero intero ||Durata di mantenimento dei backup configurati |
| DailyRetentionDuration_s |Numero intero ||Durata totale di mantenimento dati in giorni per i backup configurati |
| DailyRetentionTimes_s |Text ||Data e ora di configurazione del mantenimento dati giornaliero |
| WeeklyRetentionDuration_s |Numero decimale ||Durata totale del mantenimento dati settimanale in settimane per i backup configurati |
| WeeklyRetentionTimes_s |Text ||Data e ora di configurazione del mantenimento dati settimanale |
| WeeklyRetentionDaysOfTheWeek_s |Text ||Giorni della settimana selezionati per il mantenimento dati settimanale |
| MonthlyRetentionDuration_s |Numero decimale ||Durata totale di mantenimento dati in mesi per i backup configurati |
| MonthlyRetentionTimes_s |Text ||Data e ora di configurazione del mantenimento dati mensile |
| MonthlyRetentionFormat_s |Text ||Tipo di configurazione per il mantenimento dati mensile, ad esempio daily per la configurazione basata sui giorni o weekly per la configurazione basata sulle settimane |
| MonthlyRetentionDaysOfTheWeek_s |Text ||Giorni della settimana selezionati per il mantenimento dati mensile |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||Settimane del mese per le quali è configurato il mantenimento dati mensile, ad esempio First, Last e così via |
| YearlyRetentionDuration_s |Numero decimale ||Durata totale di mantenimento dati in anni per i backup configurati |
| YearlyRetentionTimes_s |Text ||Data e ora di configurazione del mantenimento dati annuale |
| YearlyRetentionMonthsOfTheYear_s |Text ||Mesi dell'anno selezionati per il mantenimento dati annuale |
| YearlyRetentionFormat_s |Text ||Tipo di configurazione per il mantenimento dati annuale, ad esempio daily per la configurazione basata sui giorni o weekly per la configurazione basata sulle settimane | |
| YearlyRetentionDaysOfTheMonth_s |Text ||Date del mese selezionate per il mantenimento dati annuale |
| SynchronisationFrequencyPerDay_s |Numero intero |v2|Numero di volte in un giorno in cui è stato sincronizzato un backup di file per SC DPM e MAB |
| DiffBackupFormat_s |Text |v2|Formato per i backup differenziali per SQL nel backup di macchine virtuali di Azure |
| DiffBackupTime_s |Time |v2|Tempo per i backup differenziali per SQL nel backup di macchine virtuali di Azure|
| DiffBackupRetentionDuration_s |Numero decimale |v2|Durata di conservazione per i backup differenziali per SQL nel backup di macchine virtuali di Azure|
| LogBackupFrequency_s |Numero decimale |v2|Frequenza dei backup del log per SQL|
| LogBackupRetentionDuration_s |Numero decimale |v2|Durata del periodo di memorizzazione per i backup del log per SQL nel backup di macchine virtuali di Azure|
| DiffBackupDaysofTheWeek_s |Text |v2|Giorni della settimana per i backup differenziali per SQL nel backup di macchine virtuali di Azure|
| SourceSystem |Text ||Sistema di origine dei dati correnti - Azure |
| ResourceId |Text ||Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| SubscriptionId |Text ||Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text ||Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text ||Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text ||Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="policyassociation"></a>PolicyAssociation

Questa tabella fornisce dettagli sulle associazioni dei criteri con varie entità.

| Campo | Tipo di dati | Versioni applicabili | DESCRIZIONE |
| --- | --- | --- | --- |
| EventName_s |Text ||Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text ||Questo campo indica la versione corrente dello schema, ovvero **v2** |
| State_s |Text ||Stato corrente dell'oggetto criteri, ad esempio Active o Deleted |
| BackupManagementType_s |Text ||Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| OperationName |Text ||Questo campo rappresenta il nome dell'operazione in corso - PolicyAssociation |
| Categoria |Text ||Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure, è AzureBackupReport |
| Risorsa |Text ||Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| PolicyUniqueId_g |Text ||ID univoco per identificare il criterio |
| VaultUniqueId_s |Text ||ID univoco dell'insieme di credenziali a cui appartiene questo criterio |
| BackupManagementServerUniqueId_s |Text |v2 |Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile        |
| SourceSystem |Text ||Sistema di origine dei dati correnti - Azure |
| ResourceId |Text ||Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| SubscriptionId |Text ||Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text ||Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text ||Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text ||Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="protected-container"></a>Contenitore protetto

Questa tabella fornisce i campi di base sui contenitori protetti. (ProtectedServer in V1)

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Campo per identificare in modo univoco un contenitore protetto |
| ProtectedContainerOSType_s |Text |Tipo di sistema operativo del contenitore protetto |
| ProtectedContainerOSVersion_s |Text |Versione del sistema operativo del contenitore protetto |
| AgentVersion_s |Text |Numero di versione del backup dell'agente o dell'agente protezione (in caso di SC DPM e MAB) |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup. Ad esempio, IaaSVM, fileFolder |
| EntityState_s |Text |Stato corrente dell'oggetto server protetto. Ad esempio, Active, Deleted |
| ProtectedContainerFriendlyName_s |Text |Nome descrittivo del server protetto |
| ProtectedContainerName_s |Text |Nome del contenitore protetto |
| ProtectedContainerWorkloadType_s |Text |Tipo del contenitore protetto di cui è stato eseguito il backup. Ad esempio, IaaSVMContainer |
| ProtectedContainerLocation_s |Text |Indica se il contenitore protetto si trova in locale o in Azure |
| ProtectedContainerType_s |Text |Indica se il contenitore protetto è un server o un contenitore |
| ProtectedContainerProtectionState_s '  |Text |Stato di protezione del contenitore protetto |

### <a name="storage"></a>Archiviazione

Questa tabella offre dettagli sui campi relativi all'archiviazione.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| CloudStorageInBytes_s |Numero decimale |Archiviazione di backup nel cloud usata dai backup, calcolata in base al valore più recente (questo campo è solo per lo schema v1)|
| ProtectedInstances_s |Numero decimale |Numero di istanze protette usato per il calcolo dell'archiviazione front-end a scopo di fatturazione, calcolato in base al valore più recente |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **v2** |
| State_s |Text |Stato corrente dell'oggetto archiviazione, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - Archiviazione |
| Categoria |Text |Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure, è AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedServerUniqueId_s |Text |ID univoco del server protetto per cui viene calcolata l'archiviazione |
| VaultUniqueId_s |Text |Viene calcolato l'ID univoco dell'insieme di credenziali per l'archiviazione |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text |Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text |Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |
| StorageUniqueId_s |Text |ID univoco usato per identificare l'entità di archiviazione |
| StorageType_s |Text |Tipo di archiviazione, ad esempio cloud, volume, disco |
| StorageName_s |Text |Nome dell'entità di archiviazione, ad esempio E:\ |
| StorageTotalSizeInGBs_s |Text |Dimensioni totali dello spazio di archiviazione, in GB, utilizzate dall'entità di archiviazione|

### <a name="storageassociation"></a>StorageAssociation

Questa tabella fornisce i campi di base relativi all'archiviazione che connettono l'archiviazione ad altre entità.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- |  --- |
| StorageUniqueId_s |Text |ID univoco usato per identificare l'entità di archiviazione |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **v2** |
| BackupItemUniqueId_s |Text |ID univoco usato per identificare l'elemento di backup correlato all'entità di archiviazione |
| BackupManagementServerUniqueId_s |Text |ID univoco usato per identificare il server di gestione di backup correlato all'entità di archiviazione|
| VaultUniqueId_s |Text |ID univoco usato per identificare l'insieme di credenziali correlato all'entità di archiviazione|
| StorageConsumedInMBs_s |NUMBER|Dimensioni dello spazio di archiviazione utilizzato dall'elemento di backup corrispondente nell'archivio corrispondente |
| StorageAllocatedInMBs_s |NUMBER |Dimensioni dello spazio di archiviazione allocato dall'elemento di backup corrispondente nello spazio di archiviazione corrispondente di tipo Disk|

### <a name="vault"></a>Insiemi di credenziali

Questa tabella offre dettagli sui campi relativi all'insieme di credenziali.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **v2** |
| State_s |Text |Stato corrente dell'oggetto insieme di credenziali, ad esempio Active o Deleted |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - Insieme di credenziali |
| Categoria |Text |Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure, è AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| VaultUniqueId_s |Text |ID univoco dell'insieme di credenziali |
| VaultName_s |Text |Nome dell'insieme di credenziali |
| AzureDataCenter_s |Text |Data center in cui si trova l'insieme di credenziali |
| StorageReplicationType_s |Text |Tipo di replica di archiviazione per l'insieme di credenziali, ad esempio GeoRedundant |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text |Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text |Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="backup-management-server"></a>Server di gestione di backup

Questa tabella fornisce i campi di base relativi ai server di gestione di backup.

|Campo  |Tipo di dati  | DESCRIZIONE  |
|---------|---------|----------|
|BackupManagementServerName_s     |Text         |Nome del server di gestione di backup        |
|AzureBackupAgentVersion_s     |Text         |Versione dell'agente di backup di Azure nel server di gestione di backup          |
|BackupManagementServerVersion_s     |Text         |Versione del server di gestione di backup|
|BackupManagementServerOSVersion_s     |Text            |Versione del sistema operativo del server di gestione di backup|
|BackupManagementServerType_s     |Text         |Tipo di server di gestione di backup, come MAB, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Per identificare in modo univoco il server di gestione di backup       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Questa tabella specifica i carichi di lavoro a cui è associato un volume.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| StorageUniqueId_s |Text |ID univoco usato per identificare l'entità di archiviazione |
| BackupItemType_s |Text |Carichi di lavoro per cui il volume è la risorsa di archiviazione preferita|

### <a name="protectedinstance"></a>ProtectedInstance

Questa tabella fornisce i campi correlati alle istanze protette di base.

| Campo | Tipo di dati |Versioni applicabili | DESCRIZIONE |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|ID univoco usato per identificare l'elemento di backup per le macchine virtuali di cui è stato eseguito il backup con DPM, MAB|
| ProtectedContainerUniqueId_s |Text |v2|ID univoco usato per identificare il contenitore protetto per tutti gli elementi eccetto le macchine virtuali di cui è stato eseguito il backup con DPM, MAB|
| ProtectedInstanceCount_s |Text |v2|Conteggio delle istanze protette per l'elemento di backup associato o il contenitore protetto in tale data e ora|

### <a name="recoverypoint"></a>RecoveryPoint

Questa tabella fornisce i campi di base relativi ai punti di ripristino.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |ID univoco usato per identificare l'elemento di backup per le macchine virtuali di cui è stato eseguito il backup con DPM, MAB|
| OldestRecoveryPointTime_s |Text |Data e ora del punto di ripristino meno recente per l'elemento di backup|
| OldestRecoveryPointLocation_s |Text |Posizione del punto di ripristino meno recente per l'elemento di backup|
| LatestRecoveryPointTime_s |Text |Data e ora dell'ultimo punto di ripristino per l'elemento di backup|
| LatestRecoveryPointLocation_s |Text |Posizione del punto di ripristino più recente per l'elemento di backup|

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato il modello di dati, è possibile iniziare a [creare query personalizzate](../azure-monitor/learn/tutorial-logs-dashboards.md) nei log di monitoraggio di Azure per creare un dashboard personalizzato.
