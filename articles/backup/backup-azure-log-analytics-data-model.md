---
title: Modello di dati di Log Analytics per Backup di Azure
description: Questo articolo illustra i dettagli del modello di dati di Log Analytics per i dati di Backup di Azure.
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: dfd5c73d-0d34-4d48-959e-1936986f9fc0
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 041a8835a1dd185739b23d4073fd5811bb4490b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modello di dati di Log Analytics per i dati di Backup di Azure
In questo articolo viene descritto il modello di dati usato per il push dei dati di reporting a Log Analytics. Usando questo modello di dati, è possibile creare query personalizzate e dashboard e usarle in OMS. 

## <a name="using-azure-backup-data-model"></a>Uso del modello di dati di Backup di Azure
È possibile usare i campi seguenti forniti come parte del modello di dati per creare oggetti visivi, query personalizzate e dashboard in base alle esigenze.

### <a name="alert"></a>Avviso
Questa tabella offre dettagli sui campi relativi agli avvisi.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| AlertUniqueId_s |Text |ID univoco dell'avviso generato |
| AlertType_s |Text |Tipo di avviso generato, ad esempio Backup |
| AlertStatus_s |Text |Stato dell'avviso, ad esempio Active |
| AlertOccurenceDateTime_s |Data/ora |Data e ora in cui è stato creato l'avviso |
| AlertSeverity_s |Text |Gravità dell'avviso, ad esempio Critical |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Id univoco dell'elemento di backup a cui appartiene questo avviso |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **V1** |
| State_s |Text |Stato corrente dell'oggetto avviso, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo avviso |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - Avviso |
| Categoria |Text |Questo campo rappresenta la categoria di dati di diagnostica inseriti di Log Analytics, ovvero AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedServerUniqueId_s |Text |Id univoco dell'elemento protetto a cui appartiene questo avviso |
| VaultUniqueId_s |Text |Id univoco dell'elemento protetto a cui appartiene questo avviso |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Questo campo rappresenta l'id risorsa per cui sono stati raccolti i dati, verrà visualizzato l'id risorsa dell'insieme di credenziali dei servizi di ripristino |
| SubscriptionId |Text |Questo campo rappresenta l'id di sottoscrizione della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceGroup |Text |Questo campo rappresenta il gruppo di risorse della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceProvider |Text |Questo campo rappresenta il provider di risorse per cui sono stati raccolti i dati - Microsoft.RecoveryServices |
| ResourceType |Text |Questo campo rappresenta il tipo di risorsa per la quale sono stati raccolti i dati - Insiemi di credenziali |

### <a name="backupitem"></a>BackupItem
Questa tabella offre dettagli sui campi relativi agli elementi di backup.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |ID univoco dell'elemento di backup |
| BackupItemId_s |Text |ID dell'elemento di backup |
| BackupItemName_s |Text |Nome dell'elemento di backup |
| BackupItemFriendlyName_s |Text |Nome descrittivo dell'elemento di backup |
| BackupItemType_s |Text |Tipo dell'elemento di backup, ad esempio macchina virtuale o FileFolder |
| ProtectedServerName_s |Text |Nome del server protetto a cui appartiene l'elemento di backup |
| ProtectionState_s |Text |Stato della protezione corrente dell'elemento di backup, ad esempio Protected o ProtectionStopped |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **V1** |
| State_s |Text |Stato corrente dell'oggetto elemento di backup, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo elemento di backup |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - BackupItem |
| Categoria |Text |Questo campo rappresenta la categoria di dati di diagnostica inseriti di Log Analytics, ovvero AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Questo campo rappresenta l'id risorsa per cui sono stati raccolti i dati, verrà visualizzato l'id risorsa dell'insieme di credenziali dei servizi di ripristino |
| SubscriptionId |Text |Questo campo rappresenta l'id di sottoscrizione della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceGroup |Text |Questo campo rappresenta il gruppo di risorse della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceProvider |Text |Questo campo rappresenta il provider di risorse per cui sono stati raccolti i dati - Microsoft.RecoveryServices |
| ResourceType |Text |Questo campo rappresenta il tipo di risorsa per la quale sono stati raccolti i dati - Insiemi di credenziali |

### <a name="backupitemassociation"></a>BackupItemAssociation
Questa tabella fornisce dettagli sulle associazioni degli elementi di backup con varie entità.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |ID univoco dell'elemento di backup |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **V1** |
| State_s |Text |Stato corrente dell'oggetto di associazione dell'elemento di backup, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo elemento di backup |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - BackupItemAssociation |
| Categoria |Text |Questo campo rappresenta la categoria di dati di diagnostica inseriti di Log Analytics, ovvero AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| PolicyUniqueId_g |Text |Id univoco per identificare il criterio e a quale elemento di backup è associato |
| ProtectedServerUniqueId_s |Text |Id univoco del server protetto a cui appartiene questo elemento di backup |
| VaultUniqueId_s |Text |Id univoco dell'insieme di credenziali a cui appartiene questo elemento di backup |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Questo campo rappresenta l'id risorsa per cui sono stati raccolti i dati, verrà visualizzato l'id risorsa dell'insieme di credenziali dei servizi di ripristino |
| SubscriptionId |Text |Questo campo rappresenta l'id di sottoscrizione della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceGroup |Text |Questo campo rappresenta il gruppo di risorse della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceProvider |Text |Questo campo rappresenta il provider di risorse per cui sono stati raccolti i dati - Microsoft.RecoveryServices |
| ResourceType |Text |Questo campo rappresenta il tipo di risorsa per la quale sono stati raccolti i dati - Insiemi di credenziali |

### <a name="job"></a>Processo
Questa tabella offre dettagli sui campi relativi al processo.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Id univoco dell'elemento di backup a cui appartiene questo processo |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **V1** |
| State_s |Text |Stato corrente dell'oggetto processo, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo processo |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - Processo |
| Categoria |Text |Questo campo rappresenta la categoria di dati di diagnostica inseriti di Log Analytics, ovvero AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedServerUniqueId_s |Text |Id univoco dell'elemento protetto a cui appartiene questo processo |
| VaultUniqueId_s |Text |Id univoco dell'elemento protetto a cui appartiene questo processo |
| JobOperation_s |Text |Operazione per la quale viene eseguito il processo, ad esempio Backup, Restore o Configure Backup |
| JobStatus_s |Text |Stato del processo completato, ad esempio Completed o Failed |
| JobFailureCode_s |Text |Stringa di codice contenente un errore e a causa della quale si è verificato un errore del processo |
| JobStartDateTime_s |Data/ora |Data e ora di avvio dell'esecuzione del processo |
| BackupStorageDestination_s |Text |Destinazione dell'archiviazione di backup, ad esempio Cloud o Disk  |
| JobDurationInSecs_s | Number |Durata totale del processo in secondi |
| DataTransferredInMB_s | Number |Dati trasferiti in MB per il processo|
| JobUniqueId_g |Text |ID univoco per l'identificazione del processo |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Questo campo rappresenta l'id risorsa per cui sono stati raccolti i dati, verrà visualizzato l'id risorsa dell'insieme di credenziali dei servizi di ripristino |
| SubscriptionId |Text |Questo campo rappresenta l'id di sottoscrizione della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceGroup |Text |Questo campo rappresenta il gruppo di risorse della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceProvider |Text |Questo campo rappresenta il provider di risorse per cui sono stati raccolti i dati - Microsoft.RecoveryServices |
| ResourceType |Text |Questo campo rappresenta il tipo di risorsa per la quale sono stati raccolti i dati - Insiemi di credenziali |

### <a name="policy"></a>Criterio
Questa tabella offre dettagli sui campi relativi al criterio.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **V1** |
| State_s |Text |Stato corrente dell'oggetto criteri, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo criterio |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - Criterio |
| Categoria |Text |Questo campo rappresenta la categoria di dati di diagnostica inseriti di Log Analytics, ovvero AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| PolicyUniqueId_g |Text |ID univoco per l'identificazione dei criteri |
| PolicyName_s |Text |Nome dei criteri definiti |
| BackupFrequency_s |Text |Frequenza con cui vengono eseguiti i backup, ad esempio daily o weekly |
| BackupTimes_s |Text |Data e ora in cui sono pianificati backup |
| BackupDaysOfTheWeek_s |Text |Giorni della settimana per i quali sono stati pianificati backup |
| RetentionDuration_s |Numero intero |Durata di mantenimento dei backup configurati |
| DailyRetentionDuration_s |Numero intero |Durata totale di mantenimento dati in giorni per i backup configurati |
| DailyRetentionTimes_s |Text |Data e ora di configurazione del mantenimento dati giornaliero |
| WeeklyRetentionDuration_s |Numero decimale |Durata totale del mantenimento dati settimanale in settimane per i backup configurati |
| WeeklyRetentionTimes_s |Text |Data e ora di configurazione del mantenimento dati settimanale |
| WeeklyRetentionDaysOfTheWeek_s |Text |Giorni della settimana selezionati per il mantenimento dati settimanale |
| MonthlyRetentionDuration_s |Numero decimale |Durata totale di mantenimento dati in mesi per i backup configurati |
| MonthlyRetentionTimes_s |Text |Data e ora di configurazione del mantenimento dati mensile |
| MonthlyRetentionFormat_s |Text |Tipo di configurazione per il mantenimento dati mensile, ad esempio daily per la configurazione basata sui giorni o weekly per la configurazione basata sulle settimane |
| MonthlyRetentionDaysOfTheWeek_s |Text |Giorni della settimana selezionati per il mantenimento dati mensile |
| MonthlyRetentionWeeksOfTheMonth_s |Text |Settimane del mese per le quali è configurato il mantenimento dati mensile, ad esempio First, Last e così via |
| YearlyRetentionDuration_s |Numero decimale |Durata totale di mantenimento dati in anni per i backup configurati |
| YearlyRetentionTimes_s |Text |Data e ora di configurazione del mantenimento dati annuale |
| YearlyRetentionMonthsOfTheYear_s |Text |Mesi dell'anno selezionati per il mantenimento dati annuale |
| YearlyRetentionFormat_s |Text |Tipo di configurazione per il mantenimento dati annuale, ad esempio daily per la configurazione basata sui giorni o weekly per la configurazione basata sulle settimane |
| YearlyRetentionDaysOfTheMonth_s |Text |Date del mese selezionate per il mantenimento dati annuale |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Questo campo rappresenta l'id risorsa per cui sono stati raccolti i dati, verrà visualizzato l'id risorsa dell'insieme di credenziali dei servizi di ripristino |
| SubscriptionId |Text |Questo campo rappresenta l'id di sottoscrizione della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceGroup |Text |Questo campo rappresenta il gruppo di risorse della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceProvider |Text |Questo campo rappresenta il provider di risorse per cui sono stati raccolti i dati - Microsoft.RecoveryServices |
| ResourceType |Text |Questo campo rappresenta il tipo di risorsa per la quale sono stati raccolti i dati - Insiemi di credenziali |

### <a name="policyassociation"></a>PolicyAssociation
Questa tabella fornisce dettagli sulle associazioni dei criteri con varie entità.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **V1** |
| State_s |Text |Stato corrente dell'oggetto criteri, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo criterio |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - PolicyAssociation |
| Categoria |Text |Questo campo rappresenta la categoria di dati di diagnostica inseriti di Log Analytics, ovvero AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| PolicyUniqueId_g |Text |ID univoco per l'identificazione dei criteri |
| VaultUniqueId_s |Text |Id univoco dell'insieme di credenziali a cui appartiene questo criterio |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Questo campo rappresenta l'id risorsa per cui sono stati raccolti i dati, verrà visualizzato l'id risorsa dell'insieme di credenziali dei servizi di ripristino |
| SubscriptionId |Text |Questo campo rappresenta l'id di sottoscrizione della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceGroup |Text |Questo campo rappresenta il gruppo di risorse della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceProvider |Text |Questo campo rappresenta il provider di risorse per cui sono stati raccolti i dati - Microsoft.RecoveryServices |
| ResourceType |Text |Questo campo rappresenta il tipo di risorsa per la quale sono stati raccolti i dati - Insiemi di credenziali |

### <a name="protectedserver"></a>ProtectedServer
Questa tabella offre dettagli sui campi relativi al server protetto.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| ProtectedServerName_s |Text |Nome del server protetto |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **V1** |
| State_s |Text |Stato corrente dell'oggetto server protetto, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo server protetto |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - Protected Server |
| Categoria |Text |Questo campo rappresenta la categoria di dati di diagnostica inseriti di Log Analytics, ovvero AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedServerUniqueId_s |Text |Id univoco del server protetto |
| RegisteredContainerId_s |Text |ID del contenitore registrato per il backup |
| ProtectedServerType_s |Text |Tipo di server protetto di cui si esegue il backup, ad esempio Windows |
| ProtectedServerFriendlyName_s |Text |Nome descrittivo del server protetto |
| AzureBackupAgentVersion_s |Text |Numero di versione dell'agente di Backup di Azure |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Questo campo rappresenta l'id risorsa per cui sono stati raccolti i dati, verrà visualizzato l'id risorsa dell'insieme di credenziali dei servizi di ripristino |
| SubscriptionId |Text |Questo campo rappresenta l'id di sottoscrizione della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceGroup |Text |Questo campo rappresenta il gruppo di risorse della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceProvider |Text |Questo campo rappresenta il provider di risorse per cui sono stati raccolti i dati - Microsoft.RecoveryServices |
| ResourceType |Text |Questo campo rappresenta il tipo di risorsa per la quale sono stati raccolti i dati - Insiemi di credenziali |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
Questa tabella fornisce dettagli sulle associazioni dei server protetti con altre entità.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **V1** |
| State_s |Text |Stato corrente dell'oggetto associazione del server protetto, ad esempio, Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo server protetto |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - ProtectedServerAssociation |
| Categoria |Text |Questo campo rappresenta la categoria di dati di diagnostica inseriti di Log Analytics, ovvero AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedServerUniqueId_s |Text |Id univoco del server protetto |
| VaultUniqueId_s |Text |Id univoco dell'insieme di credenziali a cui appartiene questo server protetto |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Questo campo rappresenta l'id risorsa per cui sono stati raccolti i dati, verrà visualizzato l'id risorsa dell'insieme di credenziali dei servizi di ripristino |
| SubscriptionId |Text |Questo campo rappresenta l'id di sottoscrizione della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceGroup |Text |Questo campo rappresenta il gruppo di risorse della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceProvider |Text |Questo campo rappresenta il provider di risorse per cui sono stati raccolti i dati - Microsoft.RecoveryServices |
| ResourceType |Text |Questo campo rappresenta il tipo di risorsa per la quale sono stati raccolti i dati - Insiemi di credenziali |

### <a name="storage"></a>Archiviazione
Questa tabella offre dettagli sui campi relativi all'archiviazione.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| CloudStorageInBytes_s |Numero decimale |Spazio di archiviazione nel cloud usato dal backup, calcolato in base al valore più recente |
| ProtectedInstances_s |Numero decimale |Numero di istanze protette usato per il calcolo dell'archiviazione front-end a scopo di fatturazione, calcolato in base al valore più recente |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **V1** |
| State_s |Text |Stato corrente dell'oggetto archiviazione, ad esempio Active o Deleted |
| BackupManagementType_s |Text |Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questa archiviazione |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - Archiviazione |
| Categoria |Text |Questo campo rappresenta la categoria di dati di diagnostica inseriti di Log Analytics, ovvero AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| ProtectedServerUniqueId_s |Text |Id univoco del server protetto per cui viene calcolata l'archiviazione |
| VaultUniqueId_s |Text |Id univoco dell'insieme di credenziali per cui viene calcolata l'archiviazione |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Questo campo rappresenta l'id risorsa per cui sono stati raccolti i dati, verrà visualizzato l'id risorsa dell'insieme di credenziali dei servizi di ripristino |
| SubscriptionId |Text |Questo campo rappresenta l'id di sottoscrizione della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceGroup |Text |Questo campo rappresenta il gruppo di risorse della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceProvider |Text |Questo campo rappresenta il provider di risorse per cui sono stati raccolti i dati - Microsoft.RecoveryServices |
| ResourceType |Text |Questo campo rappresenta il tipo di risorsa per la quale sono stati raccolti i dati - Insiemi di credenziali |

### <a name="vault"></a>Insiemi di credenziali
Questa tabella offre dettagli sui campi relativi all'insieme di credenziali.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| EventName_s |Text |Questo campo rappresenta il nome di questo evento, che è sempre AzureBackupCentralReport |
| SchemaVersion_s |Text |Questo campo indica la versione corrente dello schema, ovvero **V1** |
| State_s |Text |Stato corrente dell'oggetto insieme di credenziali, ad esempio Active o Deleted |
| OperationName |Text |Questo campo rappresenta il nome dell'operazione in corso - Insieme di credenziali |
| Categoria |Text |Questo campo rappresenta la categoria di dati di diagnostica inseriti di Log Analytics, ovvero AzureBackupReport |
| Risorsa |Text |Si tratta della risorsa per cui sono stati raccolti i dati, verrà visualizzato il nome dell'insieme di credenziali dei servizi di ripristino |
| VaultUniqueId_s |Text |Id univoco dell'insieme di credenziali |
| VaultName_s |Text |Nome dell'insieme di credenziali |
| AzureDataCenter_s |Text |Data center in cui si trova l'insieme di credenziali |
| StorageReplicationType_s |Text |Tipo di replica di archiviazione per l'insieme di credenziali, ad esempio GeoRedundant |
| SourceSystem |Text |Sistema di origine dei dati correnti - Azure |
| ResourceId |Text |Questo campo rappresenta l'id risorsa per cui sono stati raccolti i dati, verrà visualizzato l'id risorsa dell'insieme di credenziali dei servizi di ripristino |
| SubscriptionId |Text |Questo campo rappresenta l'id di sottoscrizione della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceGroup |Text |Questo campo rappresenta il gruppo di risorse della risorsa (insieme di credenziali RS) per il quale sono stati raccolti dati |
| ResourceProvider |Text |Questo campo rappresenta il provider di risorse per cui sono stati raccolti i dati - Microsoft.RecoveryServices |
| ResourceType |Text |Questo campo rappresenta il tipo di risorsa per la quale sono stati raccolti i dati - Insiemi di credenziali |

## <a name="next-steps"></a>Passaggi successivi
Dopo aver verificato il modello di dati per la creazione di report di Backup di Azure, è possibile iniziare [la creazione di dashboard](../log-analytics/log-analytics-dashboards.md) in Log Analytics e OMS.
