---
title: Monitoraggio di Azure i log del modello di dati per Backup di Azure
description: Questo articolo illustra il monitoraggio di Azure registra i dettagli del modello dati per i dati di Backup di Azure.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: dd4dad2cc3e541d3b6866c02341161dc1d9e1e6c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61234972"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modello di dati di Log Analytics per i dati di Backup di Azure

Usare il modello di dati di Log Analitica per creare avvisi personalizzati da Log Analitica.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

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
|AlertTimeToResolveInMinutes_s    | Number        |Tempo necessario per risolvere un avviso. Valore vuoto per gli avvisi attivi.         |
|AlertConsolidationStatus_s   |Text         |Identificare se l'avviso è un avviso consolidato o meno         |
|CountOfAlertsConsolidated_s     |Number         |Numero di avvisi consolidati in caso di un avviso consolidato          |
|AlertRaisedOn_s     |Text         |Tipo di entità che su cui viene generato l'avviso         |
|AlertCode_s     |Text         |Codice per identificare in modo univoco un tipo di avviso         |
|RecommendedAction_s   |Text         |Azione consigliata per risolvere l'avviso         |
| EventName_s |Text |Nome dell'evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Identificatore univoco dell'elemento di backup associato all'avviso |
| SchemaVersion_s |Text |Versione corrente dello schema, ad esempio **V2** |
| State_s |Text |Stato corrente dell'oggetto avviso, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo avviso |
| OperationName |Text |Nome dell'operazione corrente, ad esempio, Avviso |
| Category |Text |Categoria dei dati di diagnostica effettuato il push dei log di monitoraggio di Azure. Sempre AzureBackupReport |
| Resource |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedServerUniqueId_s |Text |Identificatore univoco del server protetto associato all'avviso |
| VaultUniqueId_s |Text |Identificatore univoco dell'insieme di credenziali protette associato all'avviso |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore univoco per la risorsa per ia quale vengono raccolti i dati. Ad esempio, un id risorsa dell'insieme di credenziali di Servizi di ripristino |
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
| BackupItemId_s |Text |Identificatore dell'elemento di backup |
| BackupItemName_s |Text |Nome dell'elemento di backup |
| BackupItemFriendlyName_s |Text |Nome descrittivo dell'elemento di backup |
| BackupItemType_s |Text |Tipo dell'elemento di backup, ad esempio macchina virtuale o FileFolder |
| ProtectionState_s |Text |Stato della protezione corrente dell'elemento di backup, ad esempio Protected o ProtectionStopped |
| ProtectionGroupName_s |Text | Nome del gruppo protezione dati di elemento di Backup è protetto, per SC DPM e MABS, se applicabile|
| SecondaryBackupProtectionState_s |Text |Indica se abilitare la protezione secondaria per l'elemento di backup|
| SchemaVersion_s |Text |Versione dello schema, ad esempio, **V2** |
| State_s |Text |Stato dell'oggetto dell'elemento di backup, ad esempio Attivo o Eliminato |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo elemento di backup |
| OperationName |Text |Nome dell'operazione, ad esempio BackupItem |
| Category |Text |Categoria dei dati di diagnostica effettuato il push dei log di monitoraggio di Azure. Sempre AzureBackupReport |
| Resource |Text |Risorsa per cui sono stati raccolti i dati, ad esempio il nome dell'insieme di credenziali dei Servizi di ripristino |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Id risorsa per i dati raccolti, ad esempio il id dell'insieme di credenziali dei Servizi di ripristino |
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
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, è **V2** |
| State_s |Text |Stato corrente dell'oggetto di associazione dell'elemento di backup, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| BackupItemSourceSize_s |Text | Dimensioni del front-end dell'elemento di backup |
| BackupManagementServerUniqueId_s |Text | Campo per identificare in modo univoco il Server di gestione di Backup di elemento di Backup è protetto tramite, se applicabile |
| Category |Text |Questo campo rappresenta la categoria di dati di diagnostica inseriti di Log Analytics, ovvero AzureBackupReport |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - BackupItemAssociation |
| Resource |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| PolicyUniqueId_g |Text |Identificatore univoco del criterio associato all'elemento di backup |
| ProtectedServerUniqueId_s |Text |Identificatore univoco del server protetto associato all'elemento di backup |
| VaultUniqueId_s |Text |Identificatore univoco dell'insieme di credenziali contenente l'elemento di backup |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore della risorsa per i dati raccolti. Ad esempio, id risorsa dell'insieme di credenziali di Servizi di ripristino |
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per cui i dati sono stati raccolti |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per cui i dati sono stati raccolti |
| ResourceProvider |Text |Provider di risorse per i dati raccolti, ad esempio, Microsoft. RecoveryServices |
| ResourceType |Text |Tipo di risorsa per cui sono stati raccolti i dati, ad esempio, gli Insiemi di credenziali |

### <a name="backupmanagementserver"></a>BackupManagementServer

Questa tabella fornisce dettagli sulle associazioni degli elementi di backup con varie entità.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Nome del Server di gestione di Backup        |
|AzureBackupAgentVersion_s     |Text         |Versione dell'agente Azure Backup nel Server di gestione di Backup          |
|BackupManagementServerVersion_s     |Text         |Versione del Server di gestione di Backup|
|BackupManagementServerOSVersion_s    |Text            |Versione del sistema operativo del Server di gestione di Backup|
|BackupManagementServerType_s     |Text         |Tipo del Server di gestione di Backup, come MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Campo per identificare in modo univoco il Server di gestione di Backup       |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore della risorsa per i dati raccolti. Ad esempio, id risorsa dell'insieme di credenziali di Servizi di ripristino |
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
| SchemaVersion_s |Text |Versione dello schema, ad esempio, **V2** |
| State_s |Text |Stato corrente dell'oggetto processo, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - Processo |
| Category |Text |Questo campo rappresenta la categoria dei dati di diagnostica effettuato il push dei log di monitoraggio di Azure, ovvero AzureBackupReport |
| Resource |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedServerUniqueId_s |Text |Identificatore univoco del server protetto associato al processo |
| ProtectedContainerUniqueId_s |Text | Id univoco per identificare il contenitore protetto che in cui viene eseguito il processo |
| VaultUniqueId_s |Text |Identificatore univoco dell'insieme di credenziali protette |
| JobOperation_s |Text |Operazione per la quale viene eseguito il processo, ad esempio Backup, Restore o Configure Backup |
| JobStatus_s |Text |Stato del processo completato, ad esempio Completed o Failed |
| JobFailureCode_s |Text |Stringa di codice contenente un errore e a causa della quale si è verificato un errore del processo |
| JobStartDateTime_s |Data/ora |Data e ora di avvio dell'esecuzione del processo |
| BackupStorageDestination_s |Text |Destinazione dell'archiviazione di backup, ad esempio Cloud o Disk  |
| AdHocOrScheduledJob_s |Text | Per specificare se il processo è Ad Hoc o pianificato |
| JobDurationInSecs_s | Number |Durata totale del processo in secondi |
| DataTransferredInMB_s | Number |Dati trasferiti in MB per il processo|
| JobUniqueId_g |Text |ID univoco per l'identificazione del processo |
| RecoveryJobDestination_s |Text | Destinazione di un processo di ripristino, in cui i dati vengono ripristinati |
| RecoveryJobRPDateTime_s |DateTime | La data, ora in cui è stato creato il punto di ripristino in fase di ripristino |
| RecoveryJobRPLocation_s |Text | Il percorso in cui è stato archiviato il punto di ripristino in fase di ripristino|
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore della risorsa per i dati raccolti. Ad esempio, id risorsa dell'insieme di credenziali di Servizi di ripristino|
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text |Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text |Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="policy"></a>Policy

Questa tabella offre dettagli sui campi relativi al criterio.

| Campo | Tipo di dati | Versioni applicabili | Descrizione |
| --- | --- | --- | --- |
| EventName_s |Text ||Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text ||Questo campo indica la versione corrente dello schema, è **V2** |
| State_s |Text ||Stato corrente dell'oggetto criteri, ad esempio Active o Deleted |
| BackupManagementType_s |Text ||Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| OperationName |Text ||Questo campo rappresenta il nome dell'operazione in corso - Criterio |
| Category |Text ||Questo campo rappresenta la categoria dei dati di diagnostica effettuato il push dei log di monitoraggio di Azure, ovvero AzureBackupReport |
| Resource |Text ||Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
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
| SynchronisationFrequencyPerDay_s |Numero intero |v2|Numero di volte in un giorno di che un file di backup è sincronizzata per SC DPM e MABS |
| DiffBackupFormat_s |Text |v2|Formato per i backup differenziali per SQL in backup di macchine Virtuali di Azure |
| DiffBackupTime_s |Time |v2|Tempo per i backup differenziali per SQL in Backup di macchine Virtuali di Azure|
| DiffBackupRetentionDuration_s |Numero decimale |v2|Durata di mantenimento dati per i backup differenziali per SQL in Backup di macchine Virtuali di Azure|
| LogBackupFrequency_s |Numero decimale |v2|Frequenza per i backup del Log per SQL|
| LogBackupRetentionDuration_s |Numero decimale |v2|Durata della conservazione dei backup dei Log per SQL in Backup di macchine Virtuali di Azure|
| DiffBackupDaysofTheWeek_s |Text |v2|Giorni della settimana per i backup differenziali per SQL in Backup di macchine Virtuali di Azure|
| SourceSystem |Text ||Sistema di origine dei dati correnti - Azure |
| ResourceId |Text ||Identificatore della risorsa per i dati raccolti. Ad esempio, id risorsa dell'insieme di credenziali di Servizi di ripristino |
| SubscriptionId |Text ||Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text ||Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text ||Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text ||Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="policyassociation"></a>PolicyAssociation

Questa tabella fornisce dettagli sulle associazioni dei criteri con varie entità.

| Campo | Tipo di dati | Versioni applicabili | Descrizione |
| --- | --- | --- | --- |
| EventName_s |Text ||Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text ||Questo campo indica la versione corrente dello schema, è **V2** |
| State_s |Text ||Stato corrente dell'oggetto criteri, ad esempio Active o Deleted |
| BackupManagementType_s |Text ||Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| OperationName |Text ||Questo campo rappresenta il nome dell'operazione in corso - PolicyAssociation |
| Category |Text ||Questo campo rappresenta la categoria dei dati di diagnostica effettuato il push dei log di monitoraggio di Azure, ovvero AzureBackupReport |
| Resource |Text ||Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| PolicyUniqueId_g |Text ||ID univoco per l'identificazione dei criteri |
| VaultUniqueId_s |Text ||Id univoco dell'insieme di credenziali a cui appartiene questo criterio |
| BackupManagementServerUniqueId_s |Text |v2 |Campo per identificare in modo univoco il Server di gestione di Backup di elemento di Backup è protetto tramite, se applicabile        |
| SourceSystem |Text ||Sistema di origine dei dati correnti - Azure |
| ResourceId |Text ||Identificatore della risorsa per i dati raccolti. Ad esempio, id risorsa dell'insieme di credenziali di Servizi di ripristino |
| SubscriptionId |Text ||Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text ||Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text ||Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text ||Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="protected-container"></a>Contenitore protetto

Questa tabella presenta i campi di base sui contenitori protetti. (Era ProtectedServer nella versione 1)

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Campo per identificare in modo univoco un contenitore protetto |
| ProtectedContainerOSType_s |Text |Tipo del sistema operativo del contenitore protetto |
| ProtectedContainerOSVersion_s |Text |Versione del sistema operativo del contenitore protetto |
| AgentVersion_s |Text |Numero di versione dell'agente di Backup o l'agente protezione (in caso di SC DPM e MABS) |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup, ad esempio, IaaSVM o FileFolder |
| EntityState_s |Text |Stato corrente dell'oggetto server protetto, ad esempio, Active o Deleted |
| ProtectedContainerFriendlyName_s |Text |Nome descrittivo del server protetto |
| ProtectedContainerName_s |Text |Nome del contenitore protetto |
| ProtectedContainerWorkloadType_s |Text |Tipo di contenitore protetto di backup, ad esempio, IaaSVMContainer |
| ProtectedContainerLocation_s |Text |Se il contenitore protetto è in locale o in Azure |
| ProtectedContainerType_s |Text |Se il contenitore protetto è un server o un contenitore |

### <a name="storage"></a>Archiviazione

Questa tabella offre dettagli sui campi relativi all'archiviazione.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| CloudStorageInBytes_s |Numero decimale |Spazio di archiviazione nel cloud usato dal backup, calcolato in base al valore più recente |
| ProtectedInstances_s |Numero decimale |Numero di istanze protette usato per il calcolo dell'archiviazione front-end a scopo di fatturazione, calcolato in base al valore più recente |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, è **V2** |
| State_s |Text |Stato corrente dell'oggetto archiviazione, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per processo di backup del server, ad esempio, IaaSVM o FileFolder |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - Archiviazione |
| Category |Text |Questo campo rappresenta la categoria dei dati di diagnostica effettuato il push dei log di monitoraggio di Azure, ovvero AzureBackupReport |
| Resource |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedServerUniqueId_s |Text |Id univoco del server protetto per cui viene calcolata l'archiviazione |
| VaultUniqueId_s |Text |Id univoco dell'insieme di credenziali per cui viene calcolata l'archiviazione |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore della risorsa per i dati raccolti. Ad esempio, id risorsa dell'insieme di credenziali di Servizi di ripristino |
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text |Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text |Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="storageassociation"></a>StorageAssociation

Questa tabella presenta i campi correlati ad archiviazione di base la connessione di archiviazione ad altre entità.

| Campo | Tipo di dati | Descrizione |
| --- | --- |  --- |
| StorageUniqueId_s |Text |Id univoco utilizzato per identificare l'entità di archiviazione |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, è **V2** |
| BackupItemUniqueId_s |Text |Id univoco utilizzato per identificare l'elemento di backup correlato all'entità di archiviazione |
| BackupManagementServerUniqueId_s |Text |Id univoco utilizzato per identificare il server di gestione di backup correlato all'entità di archiviazione|
| VaultUniqueId_s |Text |Id univoco utilizzato per identificare l'insieme di credenziali correlate all'entità di archiviazione|
| StorageConsumedInMBs_s |Number|Dimensioni di archiviazione usata dall'elemento di backup corrispondente nella risorsa di archiviazione corrispondente |
| StorageAllocatedInMBs_s |Number |Dimensioni della memoria allocata per l'elemento di backup corrispondente nella risorsa di archiviazione corrispondente di tipo disco|

### <a name="vault"></a>Insiemi di credenziali

Questa tabella offre dettagli sui campi relativi all'insieme di credenziali.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, è **V2** |
| State_s |Text |Stato corrente dell'oggetto insieme di credenziali, ad esempio Active o Deleted |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - Insieme di credenziali |
| Category |Text |Questo campo rappresenta la categoria dei dati di diagnostica effettuato il push dei log di monitoraggio di Azure, ovvero AzureBackupReport |
| Resource |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| VaultUniqueId_s |Text |Id univoco dell'insieme di credenziali |
| VaultName_s |Text |Nome dell'insieme di credenziali |
| AzureDataCenter_s |Text |Data center in cui si trova l'insieme di credenziali |
| StorageReplicationType_s |Text |Tipo di replica di archiviazione per l'insieme di credenziali, ad esempio GeoRedundant |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Identificatore della risorsa per i dati raccolti. Ad esempio, id risorsa dell'insieme di credenziali di Servizi di ripristino |
| SubscriptionId |Text |Identificatore della sottoscrizione della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceGroup |Text |Gruppo di risorse della risorsa (ad es. credenziali di Servizi di ripristino) per il quale vengono raccolti i dati |
| ResourceProvider |Text |Provider di risorse per il quale vengono raccolti i dati. Ad esempio, Microsoft.RecoveryServices |
| ResourceType |Text |Tipo di risorse per il quale vengono raccolti i dati. Ad esempio, Insieme di credenziali |

### <a name="backup-management-server"></a>Server di gestione di backup

Questa tabella presenta i campi di base sui server di gestione di Backup.

|Campo  |Tipo di dati  | Descrizione  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Text         |Nome del Server di gestione di Backup        |
|AzureBackupAgentVersion_s     |Text         |Versione dell'agente Azure Backup nel Server di gestione di Backup          |
|BackupManagmentServerVersion_s     |Text         |Versione del Server di gestione di Backup|
|BackupManagmentServerOSVersion_s     |Text            |Versione del sistema operativo del Server di gestione di Backup|
|BackupManagementServerType_s     |Text         |Tipo del Server di gestione di Backup, come MABS, SC DPM|
|BackupManagmentServerUniqueId_s     |Text         |Campo per identificare in modo univoco il Server di gestione di Backup       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Questa tabella specifica il carico di lavoro che è associato a un Volume.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| StorageUniqueId_s |Text |Id univoco utilizzato per identificare l'entità di archiviazione |
| BackupItemType_s |Text |I carichi di lavoro per cui questo volume è l'archiviazione preferita|

### <a name="protectedinstance"></a>ProtectedInstance

Questa tabella presenta i campi correlati di base di istanze protette.

| Campo | Tipo di dati |Versioni applicabili | Descrizione |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|Id univoco utilizzato per identificare l'elemento di backup per macchine virtuali di un backup con DPM, backup di Microsoft AZURE|
| ProtectedContainerUniqueId_s |Text |v2|Id univoco utilizzato per identificare il contenitore protetto di tutto ad eccezione di macchine virtuali di un backup con DPM, backup di Microsoft AZURE|
| ProtectedInstanceCount_s |Text |v2|Numero di istanze protette per l'elemento di backup associato o un contenitore protetto in tale data e ora|

### <a name="recoverypoint"></a>RecoveryPoint

Questa tabella fornisce il ripristino di base selezionare i campi correlati.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |Id univoco utilizzato per identificare l'elemento di backup per macchine virtuali di un backup con DPM, backup di Microsoft AZURE|
| OldestRecoveryPointTime_s |Text |Ora del punto di ripristino meno recente dell'elemento di backup|
| OldestRecoveryPointLocation_s |Text |Posizione del punto di ripristino meno recente dell'elemento di backup|
| LatestRecoveryPointTime_s |Text |Ora data dell'ultimo punto di ripristino per l'elemento di backup|
| LatestRecoveryPointLocation_s |Text |Posizione del punto di ripristino più recente dell'elemento di backup|

## <a name="next-steps"></a>Passaggi successivi

Dopo aver verificato il modello di dati, è possibile avviarlo [creare query personalizzate](../azure-monitor/learn/tutorial-logs-dashboards.md) nei log di monitoraggio di Azure per creare un dashboard personalizzato.
