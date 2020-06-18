---
title: Modello di dati di Monitoraggio di Azure
description: In questo articolo si apprenderanno i dettagli del modello di dati di Log Analytics di Monitoraggio di Azure per i dati di Backup di Azure.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 78d43e4c65f31b47f4b6070f071c932692cee883
ms.sourcegitcommit: a3c6efa4d4a48e9b07ecc3f52a552078d39e5732
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83707990"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modello di dati di Log Analytics per i dati di Backup di Azure

Usare il modello di dati di Log Analytics per creare avvisi personalizzati da Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> Questo modello di dati si riferisce alla modalità Diagnostica di Azure di invio degli eventi di diagnostica a Log Analytics. Per conoscere il modello di dati per la nuova modalità specifica della risorsa, è possibile fare riferimento all'articolo seguente: [Modello di dati per gli eventi di diagnostica di Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

## <a name="using-azure-backup-data-model"></a>Uso del modello di dati di Backup di Azure

È possibile usare i campi seguenti forniti come parte del modello di dati per creare oggetti visivi, query personalizzate e dashboard in base alle esigenze.

### <a name="alert"></a>Avviso

Questa tabella offre dettagli sui campi relativi agli avvisi.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| AlertUniqueId_s |Text |Identificatore univoco dell'avviso generato |
| AlertType_s |Text |Tipo di avviso, ad esempio Backup |
| AlertStatus_s |Text |Stato dell'avviso, ad esempio Active |
| AlertOccurrenceDateTime_s |Data/ora |Data e ora in cui è stato creato l'avviso |
| AlertSeverity_s |Text |Gravità dell'avviso, ad esempio Critical |
|AlertTimeToResolveInMinutes_s    | Number        |Tempo impiegato per risolvere un avviso. Vuoto per gli avvisi attivi.         |
|AlertConsolidationStatus_s   |Text         |Identifica se l'avviso è un avviso consolidato o meno         |
|CountOfAlertsConsolidated_s     |Number         |Numero di avvisi consolidati se si tratta di un avviso consolidato          |
|AlertRaisedOn_s     |Text         |Tipo di entità per cui viene generato l'avviso         |
|AlertCode_s     |Text         |Codice per l'identificazione univoca di un tipo di avviso         |
|RecommendedAction_s   |Text         |Azione consigliata per risolvere l'avviso         |
| EventName_s |Text |Nome dell'evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Identificatore univoco dell'elemento di backup associato all'avviso |
| SchemaVersion_s |Text |Versione corrente dello schema, ad esempio **V2** |
| State_s |Text |Stato corrente dell'oggetto avviso, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo avviso |
| OperationName |Text |Nome dell'operazione corrente, ad esempio, Avviso |
| Category |Text |Categoria di dati di diagnostica di cui viene eseguito il push in log di Monitoraggio di Azure. Sempre AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedContainerUniqueId_s |Text |Identificatore univoco del server protetto associato all'avviso (ProtectedServerUniqueId_s in V1)|
| VaultUniqueId_s |Text |Identificatore univoco dell'insieme di credenziali protette associato all'avviso |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore univoco per la risorsa per ia quale vengono raccolti i dati. Ad esempio, un ID risorsa dell'insieme di credenziali di Servizi di ripristino |
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text |Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text |Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="backupitem"></a>BackupItem

Questa tabella offre dettagli sui campi relativi agli elementi di backup.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| EventName_s |Text |Nome dell'evento. Sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Identificatore univoco dell'elemento di backup |
| BackupItemId_s |Text |Identificatore dell'elemento di backup (solo per lo schema v1) |
| BackupItemName_s |Text |Nome dell'elemento di backup |
| BackupItemFriendlyName_s |Text |Nome descrittivo dell'elemento di backup |
| BackupItemType_s |Text |Tipo dell'elemento di backup, ad esempio macchina virtuale o FileFolder |
| BackupItemProtectionState_s |Text |Stato di protezione dell'elemento di backup |
| BackupItemAppVersion_s |Text |Versione dell'applicazione dell'elemento di backup |
| ProtectionState_s |Text |Stato della protezione corrente dell'elemento di backup, ad esempio Protected o ProtectionStopped |
| ProtectionGroupName_s |Text | Nome del gruppo protezione in cui è protetto l'elemento di backup, per SC DPM e MABS, se applicabile|
| SecondaryBackupProtectionState_s |Text |Indica se la protezione secondaria è abilitata per l'elemento di backup|
| SchemaVersion_s |Text |Versione dello schema, ad esempio **V2** |
| State_s |Text |Stato dell'oggetto dell'elemento di backup, ad esempio Attivo o Eliminato |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo elemento di backup |
| OperationName |Text |Nome dell'operazione, ad esempio BackupItem |
| Category |Text |Categoria di dati di diagnostica di cui viene eseguito il push in log di Monitoraggio di Azure. Sempre AzureBackupReport |
| Risorsa |Text |Risorsa per cui sono stati raccolti i dati, ad esempio il nome dell'insieme di credenziali dei Servizi di ripristino |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |ID risorsa per i dati raccolti, ad esempio l'ID risorsa dell'insieme di credenziali di Servizi di ripristino |
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per i dati raccolti |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per i dati raccolti |
| ResourceProvider |Text |Provider di risorse per i dati raccolti, ad esempio, Microsoft. RecoveryServices |
| ResourceType |Text |Tipo di risorsa per i dati raccolti, ad esempio, gli Insiemi di credenziali |

### <a name="backupitemassociation"></a>BackupItemAssociation

Questa tabella fornisce dettagli sulle associazioni degli elementi di backup con varie entità.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |ID univoco dell'elemento di backup |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **V2** |
| State_s |Text |Stato corrente dell'oggetto di associazione dell'elemento di backup, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| BackupItemSourceSize_s |Text | Dimensioni front-end dell'elemento di backup |
| BackupManagementServerUniqueId_s |Text | Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile |
| Category |Text |Questo campo rappresenta la categoria di dati di diagnostica inseriti di Log Analytics, ovvero AzureBackupReport |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - BackupItemAssociation |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedContainerUniqueId_s |Text |Identificatore univoco del server protetto associato all'elemento di backup (ProtectedServerUniqueId_s in V1) |
| VaultUniqueId_s |Text |Identificatore univoco dell'insieme di credenziali contenente l'elemento di backup |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore della risorsa per i dati raccolti. Ad esempio, ID risorsa dell'insieme di credenziali di Servizi di ripristino |
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per cui i dati sono stati raccolti |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per cui i dati sono stati raccolti |
| ResourceProvider |Text |Provider di risorse per i dati raccolti, ad esempio, Microsoft. RecoveryServices |
| ResourceType |Text |Tipo di risorsa per cui sono stati raccolti i dati, ad esempio, gli Insiemi di credenziali |

### <a name="backupmanagementserver"></a>BackupManagementServer

Questa tabella fornisce dettagli sulle associazioni degli elementi di backup con varie entità.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Nome del server di gestione di backup        |
|AzureBackupAgentVersion_s     |Text         |Versione dell'agente di Backup di Azure nel server di gestione di backup          |
|BackupManagementServerVersion_s     |Text         |Versione del server di gestione di backup|
|BackupManagementServerOSVersion_s    |Text            |Versione del sistema operativo del server di gestione di backup|
|BackupManagementServerType_s     |Text         |Tipo del server di gestione di backup, come MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Campo che identifica in modo univoco il server di gestione di backup       |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore della risorsa per i dati raccolti. Ad esempio, ID risorsa dell'insieme di credenziali di Servizi di ripristino |
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per cui i dati sono stati raccolti |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per cui i dati sono stati raccolti |
| ResourceProvider |Text |Provider di risorse per i dati raccolti, ad esempio, Microsoft. RecoveryServices |
| ResourceType |Text |Tipo di risorsa per cui sono stati raccolti i dati, ad esempio, gli Insiemi di credenziali |

### <a name="job"></a>Processo

Questa tabella offre dettagli sui campi relativi al processo.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| EventName_s |Text |Nome dell'evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Identificatore univoco dell'elemento di backup |
| SchemaVersion_s |Text |Versione dello schema, ad esempio **V2** |
| State_s |Text |Stato corrente dell'oggetto processo, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - Processo |
| Category |Text |Questo campo rappresenta la categoria di dati di diagnostica di cui viene eseguito il push in log di Monitoraggio di Azure, ovvero AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedServerUniqueId_s |Text |Identificatore univoco del server protetto associato al processo |
| ProtectedContainerUniqueId_s |Text | ID univoco che identifica il contenitore protetto in cui viene eseguito il processo |
| VaultUniqueId_s |Text |Identificatore univoco dell'insieme di credenziali protette |
| JobOperation_s |Text |Operazione per la quale viene eseguito il processo, ad esempio Backup, Restore o Configure Backup |
| JobStatus_s |Text |Stato del processo completato, ad esempio Completed o Failed |
| JobFailureCode_s |Text |Stringa di codice contenente un errore e a causa della quale si è verificato un errore del processo |
| JobStartDateTime_s |Data/ora |Data e ora di avvio dell'esecuzione del processo |
| BackupStorageDestination_s |Text |Destinazione dell'archiviazione di backup, ad esempio Cloud o Disk  |
| AdHocOrScheduledJob_s |Text | Campo che specifica se il processo è ad hoc o pianificato |
| JobDurationInSecs_s | Number |Durata totale del processo in secondi |
| DataTransferredInMB_s | Number |Dati trasferiti in MB per il processo|
| JobUniqueId_g |Text |ID univoco per l'identificazione del processo |
| RecoveryJobDestination_s |Text | Destinazione di un processo di ripristino in cui vengono ripristinati i dati |
| RecoveryJobRPDateTime_s |Datetime | Data e ora in cui è stato creato il punto di ripristino in corso di ripristino |
| RecoveryJobRPLocation_s |Text | Posizione in cui è stato archiviato il punto di ripristino in corso di ripristino|
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore della risorsa per i dati raccolti. Ad esempio, ID risorsa dell'insieme di credenziali di Servizi di ripristino|
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text |Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text |Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="policy"></a>Policy

Questa tabella offre dettagli sui campi relativi al criterio.

| Campo | Tipo di dati | Versioni valide | Descrizione |
| --- | --- | --- | --- |
| EventName_s |Text ||Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text ||Questo campo indica la versione corrente dello schema, ovvero **V2** |
| State_s |Text ||Stato corrente dell'oggetto criteri, ad esempio Active o Deleted |
| BackupManagementType_s |Text ||Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| OperationName |Text ||Questo campo rappresenta il nome dell'operazione in corso - Criterio |
| Category |Text ||Questo campo rappresenta la categoria di dati di diagnostica di cui viene eseguito il push in log di Monitoraggio di Azure, ovvero AzureBackupReport |
| Risorsa |Text ||Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| PolicyUniqueId_g |Text ||ID univoco per l'identificazione dei criteri |
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
| SynchronisationFrequencyPerDay_s |Numero intero |v2|Numero di volte in un giorno in cui è stato sincronizzato un backup di file per SC DPM e MABS |
| DiffBackupFormat_s |Text |v2|Formato per i backup differenziali per SQL nel backup di macchine virtuali di Azure |
| DiffBackupTime_s |Tempo |v2|Tempo per i backup differenziali per SQL nel backup di macchine virtuali di Azure|
| DiffBackupRetentionDuration_s |Numero decimale |v2|Durata del periodo di conservazione per i backup differenziali per SQL nel backup di macchine virtuali di Azure|
| LogBackupFrequency_s |Numero decimale |v2|Frequenza per i backup dei log per SQL|
| LogBackupRetentionDuration_s |Numero decimale |v2|Durata del periodo di conservazione per i backup dei log per SQL nel backup di macchine virtuali di Azure|
| DiffBackupDaysofTheWeek_s |Text |v2|Giorni della settimana per i backup differenziali per SQL nel backup di macchine virtuali di Azure|
| SourceSystem |Text ||Sistema di origine dei dati correnti - Azure |
| ResourceId |Text ||Identificatore della risorsa per i dati raccolti. Ad esempio, ID risorsa dell'insieme di credenziali di Servizi di ripristino |
| SubscriptionId |Text ||Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text ||Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text ||Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text ||Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="policyassociation"></a>PolicyAssociation

Questa tabella fornisce dettagli sulle associazioni dei criteri con varie entità.

| Campo | Tipo di dati | Versioni valide | Descrizione |
| --- | --- | --- | --- |
| EventName_s |Text ||Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text ||Questo campo indica la versione corrente dello schema, ovvero **V2** |
| State_s |Text ||Stato corrente dell'oggetto criteri, ad esempio Active o Deleted |
| BackupManagementType_s |Text ||Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| OperationName |Text ||Questo campo rappresenta il nome dell'operazione in corso - PolicyAssociation |
| Category |Text ||Questo campo rappresenta la categoria di dati di diagnostica di cui viene eseguito il push in log di Monitoraggio di Azure, ovvero AzureBackupReport |
| Risorsa |Text ||Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| PolicyUniqueId_g |Text ||ID univoco per l'identificazione dei criteri |
| VaultUniqueId_s |Text ||ID univoco dell'insieme di credenziali a cui appartiene questo criterio |
| BackupManagementServerUniqueId_s |Text |v2 |Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile        |
| SourceSystem |Text ||Sistema di origine dei dati correnti - Azure |
| ResourceId |Text ||Identificatore della risorsa per i dati raccolti. Ad esempio, ID risorsa dell'insieme di credenziali di Servizi di ripristino |
| SubscriptionId |Text ||Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text ||Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text ||Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text ||Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="protected-container"></a>Contenitore protetto

Questa tabella illustra i campi di base sui contenitori protetti. (ProtectedServer in v1)

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Campo che identifica in modo univoco un contenitore protetto |
| ProtectedContainerOSType_s |Text |Tipo del sistema operativo del contenitore protetto |
| ProtectedContainerOSVersion_s |Text |Versione del sistema operativo del contenitore protetto |
| AgentVersion_s |Text |Numero di versione del backup dell'agente o dell'agente protezione (nel caso di SC DPM e MABS) |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup. Ad esempio, IaaSVM, FileFolder |
| EntityState_s |Text |Stato corrente dell'oggetto server protetto. Ad esempio Active o Deleted |
| ProtectedContainerFriendlyName_s |Text |Nome descrittivo del server protetto |
| ProtectedContainerName_s |Text |Nome del sistema operativo del contenitore protetto |
| ProtectedContainerWorkloadType_s |Text |Tipo del contenitore protetto di cui è stato eseguito il backup. Ad esempio, IaaSVMContainer |
| ProtectedContainerLocation_s |Text |Indica se il contenitore protetto si trova in locale o in Azure |
| ProtectedContainerType_s |Text |Indica se il contenitore protetto è un server o un contenitore |
| ProtectedContainerProtectionState_s’  |Text |Stato della protezione del contenitore protetto |

### <a name="storage"></a>Archiviazione

Questa tabella offre dettagli sui campi relativi all'archiviazione.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| CloudStorageInBytes_s |Numero decimale |Spazio di archiviazione nel cloud usato dal backup, calcolato in base al valore più recente (solo per lo schema v1)|
| ProtectedInstances_s |Numero decimale |Numero di istanze protette usato per il calcolo dell'archiviazione front-end a scopo di fatturazione, calcolato in base al valore più recente |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **V2** |
| State_s |Text |Stato corrente dell'oggetto archiviazione, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - Archiviazione |
| Category |Text |Questo campo rappresenta la categoria di dati di diagnostica di cui viene eseguito il push in log di Monitoraggio di Azure, ovvero AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedServerUniqueId_s |Text |ID univoco del server protetto per cui viene calcolato lo spazio di archiviazione |
| VaultUniqueId_s |Text |ID univoco dell'insieme di credenziali per cui viene calcolato lo spazio di archiviazione |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore della risorsa per i dati raccolti. Ad esempio, ID risorsa dell'insieme di credenziali di Servizi di ripristino |
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text |Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text |Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |
| StorageUniqueId_s |Text |ID univoco usato per identificare l'entità di archiviazione |
| StorageType_s |Text |Tipo di archiviazione, ad esempio Cloud, Volume, Disk |
| StorageName_s |Text |Nome dell'entità di archiviazione, ad esempio E:\ |
| StorageTotalSizeInGBs_s |Text |Dimensioni totali dello spazio di archiviazione, in GB, utilizzato dall'entità di archiviazione|

### <a name="storageassociation"></a>StorageAssociation

Questa tabella illustra i campi di base correlati all'archiviazione che connettono l'archiviazione ad altre entità.

| Campo | Tipo di dati | Descrizione |
| --- | --- |  --- |
| StorageUniqueId_s |Text |ID univoco usato per identificare l'entità di archiviazione |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **V2** |
| BackupItemUniqueId_s |Text |ID univoco usato per identificare l'elemento di backup correlato all'entità di archiviazione |
| BackupManagementServerUniqueId_s |Text |ID univoco usato per identificare il server di gestione di backup correlato all'entità di archiviazione|
| VaultUniqueId_s |Text |ID univoco usato per identificare l'insieme di credenziali correlato all'entità di archiviazione|
| StorageConsumedInMBs_s |Number|Dimensioni dello spazio di archiviazione utilizzato dall'elemento di backup corrispondente nell'archivio corrispondente |
| StorageAllocatedInMBs_s |Number |Dimensioni dello spazio di archiviazione allocato dall'elemento di backup corrispondente nello spazio di archiviazione corrispondente di tipo Disk|

### <a name="vault"></a>Insiemi di credenziali

Questa tabella offre dettagli sui campi relativi all'insieme di credenziali.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **V2** |
| State_s |Text |Stato corrente dell'oggetto insieme di credenziali, ad esempio Active o Deleted |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - Insieme di credenziali |
| Category |Text |Questo campo rappresenta la categoria di dati di diagnostica di cui viene eseguito il push in log di Monitoraggio di Azure, ovvero AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| VaultUniqueId_s |Text |ID univoco dell'insieme di credenziali |
| VaultName_s |Text |Nome dell'insieme di credenziali |
| AzureDataCenter_s |Text |Data center in cui si trova l'insieme di credenziali |
| StorageReplicationType_s |Text |Tipo di replica di archiviazione per l'insieme di credenziali, ad esempio GeoRedundant |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore della risorsa per i dati raccolti. Ad esempio, ID risorsa dell'insieme di credenziali di Servizi di ripristino |
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text |Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text |Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="backup-management-server"></a>Server di gestione di backup

Questa tabella illustra i campi di base relativi ai server di gestione di backup.

|Campo  |Tipo di dati  | Descrizione  |
|---------|---------|----------|
|BackupManagementServerName_s     |Text         |Nome del server di gestione di backup        |
|AzureBackupAgentVersion_s     |Text         |Versione dell'agente di Backup di Azure nel server di gestione di backup          |
|BackupManagementServerVersion_s     |Text         |Versione del server di gestione di backup|
|BackupManagementServerOSVersion_s     |Text            |Versione del sistema operativo del server di gestione di backup|
|BackupManagementServerType_s     |Text         |Tipo del server di gestione di backup, come MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Campo che identifica in modo univoco il server di gestione di backup       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Questa tabella specifica i carichi di lavoro a cui è associato un volume.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| StorageUniqueId_s |Text |ID univoco usato per identificare l'entità di archiviazione |
| BackupItemType_s |Text |Carichi di lavoro per cui questo volume è la risorsa di archiviazione preferita|

### <a name="protectedinstance"></a>ProtectedInstance

Questa tabella illustra i campi di base correlati alle istanze protette.

| Campo | Tipo di dati |Versioni valide | Descrizione |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|ID univoco usato per identificare l'elemento di backup per le macchine virtuali di cui viene eseguito il backup con DPM, MABS|
| ProtectedContainerUniqueId_s |Text |v2|ID univoco usato per identificare il contenitore protetto per tutti gli elementi eccetto le macchine virtuali di cui viene eseguito il backup con DPM, MABS|
| ProtectedInstanceCount_s |Text |v2|Conteggio delle istanze protette per l'elemento di backup associato o il contenitore protetto in tale data e ora|

### <a name="recoverypoint"></a>RecoveryPoint

Questa tabella illustra i campi di base correlati ai punti di ripristino.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |ID univoco usato per identificare l'elemento di backup per le macchine virtuali di cui viene eseguito il backup con DPM, MABS|
| OldestRecoveryPointTime_s |Text |Data e ora del punto di ripristino meno recente per l'elemento di backup|
| OldestRecoveryPointLocation_s |Text |Posizione del punto di ripristino meno recente per l'elemento di backup|
| LatestRecoveryPointTime_s |Text |Data e ora del punto di ripristino più recente per l'elemento di backup|
| LatestRecoveryPointLocation_s |Text |Posizione del punto di ripristino più recente per l'elemento di backup|

## <a name="sample-kusto-queries"></a>Query Kusto di esempio

Di seguito sono riportati alcuni esempi che facilitano la scrittura di query sui dati di Backup di Azure che si trovano nella tabella Diagnostica di Azure:

- Tutti i processi di backup completati

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

- Tutti i processi di backup di macchine virtuali di Azure completati

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

- Tutti i processi di backup di log SQL completati

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

- Tutti i processi dell'agente di Backup di Azure completati

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

## <a name="v1-schema-vs-v2-schema"></a>Schema V1 e schema V2 schema
In precedenza, i dati di diagnostica per l'agente di Backup di Azure e per il backup delle macchine virtuali di Azure venivano inviati a una tabella di Diagnostica di Azure in uno schema denominato ***Schema V1***. Successivamente sono state aggiunte nuove colonne per supportare altri scenari e altri carichi di lavoro ed è stato eseguito il push dei dati di diagnostica in un nuovo schema denominato ***Schema V2***. 

Per motivi di compatibilità con le versioni precedenti, i dati di diagnostica per l'agente di Backup di Azure e per il backup delle macchine virtuali di Azure vengono attualmente inviati alla tabella di Diagnostica di Azure sia nello schema V1 che nello schema V2 (con lo schema V1 ora in un percorso di deprecazione). È possibile identificare quali record in Log Analytics appartengono allo schema V1 filtrando i record per SchemaVersion_s=="V1" nelle query di log. 

Fare riferimento alla terza colonna, 'Description', nel [modello di dati ](https://docs.microsoft.com/azure/backup/backup-azure-diagnostics-mode-data-model#using-azure-backup-data-model) descritto in precedenza per identificare le colonne che appartengono solo allo schema V1.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato il modello di dati, è possibile iniziare [la creazione di query personalizzate](../azure-monitor/learn/tutorial-logs-dashboards.md) nei log di Monitoraggio di Azure per creare un dashboard personalizzato.
