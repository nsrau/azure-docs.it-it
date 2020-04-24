---
title: Modello di dati per gli eventi di diagnostica di backup di Azure
description: Questo modello di dati è in riferimento alla modalità specifica della risorsa di invio di eventi di diagnostica a Log Analytics (LA).
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 267753ee1647739e36d92b64f50d8a8be87537d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77583385"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Modello di dati per gli eventi di diagnostica di backup di Azure

## <a name="coreazurebackup"></a>CoreAzureBackup

Questa tabella fornisce informazioni sulle entità di backup di base, ad esempio insiemi di credenziali ed elementi di backup.

| **Campo**                         | **Tipo di dati** | **Descrizione**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Testo          | Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino. |
| OperationName                     | Testo          | Questo campo rappresenta il nome dell'operazione corrente-BackupItem, BackupItemAssociation o ProtectedContainer. |
| Category                          | Testo          | Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure. Ad esempio, CoreAzureBackup. |
| AgentVersion                      | Testo          | Numero di versione del backup dell'agente o dell'agente protezione (in caso di SC DPM e MAB) |
| AzureBackupAgentVersion           | Testo          | Versione dell'agente di backup di Azure nel server di gestione di backup |
| AzureDataCenter                   | Testo          | Data Center in cui si trova l'insieme di credenziali                       |
| BackupItemAppVersion              | Testo          | Versione dell'applicazione dell'elemento di backup                       |
| BackupItemFriendlyName            | Testo          | Nome descrittivo dell'elemento di backup                             |
| BackupItemName                    | Testo          | Nome dell'elemento di backup                                      |
| BackupItemProtectionState         | Testo          | Stato di protezione dell'elemento di backup                          |
| BackupItemFrontEndSize            | Testo          | Dimensioni front-end dell'elemento di backup                            |
| BackupItemType                    | Testo          | Tipo di elemento di backup. Ad esempio: VM, fileFolder             |
| BackupItemUniqueId                | Testo          | Identificatore univoco dell'elemento di backup                         |
| BackupManagementServerType        | Testo          | Tipo di server di gestione di backup, come in MAB, SC DPM     |
| BackupManagementServerUniqueId    | Testo          | Per identificare in modo univoco il server di gestione di backup      |
| BackupManagementType              | Testo          | Tipo di provider per il processo di backup del server. Ad esempio, IaaSVM, fileFolder |
| BackupManagementServerName        | Testo          | Nome del server di gestione di backup                         |
| BackupManagementServerOSVersion   | Testo          | Versione del sistema operativo del server di gestione di backup                   |
| BackupManagementServerVersion     | Testo          | Versione del server di gestione di backup                      |
| LatestRecoveryPointLocation       | Testo          | Posizione del punto di ripristino più recente per l'elemento di backup    |
| LatestRecoveryPointTime           | Datetime      | Data e ora dell'ultimo punto di ripristino per l'elemento di backup   |
| OldestRecoveryPointLocation       | Testo          | Posizione del punto di ripristino meno recente per l'elemento di backup    |
| OldestRecoveryPointTime           | Datetime      | Data e ora dell'ultimo punto di ripristino per l'elemento di backup   |
| PolicyUniqueId                    | Testo          | ID univoco per identificare il criterio                             |
| ProtectedContainerFriendlyName    | Testo          | Nome descrittivo del server protetto                        |
| ProtectedContainerLocation        | Testo          | Indica se il contenitore protetto si trova in locale o in Azure |
| ProtectedContainerName            | Testo          | Nome del contenitore protetto                            |
| ProtectedContainerOSType          | Testo          | Tipo di sistema operativo del contenitore protetto                          |
| ProtectedContainerOSVersion       | Testo          | Versione del sistema operativo del contenitore protetto                        |
| ProtectedContainerProtectionState | Testo          | Stato di protezione del contenitore protetto                  |
| ProtectedContainerType            | Testo          | Indica se il contenitore protetto è un server o un contenitore  |
| ProtectedContainerUniqueId        | Testo          | ID univoco usato per identificare il contenitore protetto per tutti gli elementi eccetto le macchine virtuali di cui è stato eseguito il backup con DPM, MAB |
| ProtectedContainerWorkloadType    | Testo          | Tipo del contenitore protetto di cui è stato eseguito il backup. Ad esempio, IaaSVMContainer |
| ProtectionGroupName               | Testo          | Nome del gruppo protezione dati in cui è protetto l'elemento di backup, per SC DPM e MAB, se applicabile |
| ResourceGroupName                 | Testo          | Gruppo di risorse della risorsa (ad esempio, l'insieme di credenziali di servizi di ripristino) per i dati raccolti |
| schemaVersion                     | Testo          | Questo campo indica la versione corrente dello schema, ovvero **v2** |
| SecondaryBackupProtectionState    | Testo          | Indica se la protezione secondaria è abilitata per l'elemento di backup  |
| State                             | Testo          | Stato dell'oggetto elemento di backup. Ad esempio, Active, Deleted |
| StorageReplicationType            | Testo          | Tipo di replica di archiviazione per l'insieme di credenziali. Ad esempio, georidondante |
| SubscriptionId                    | Testo          | Identificatore della sottoscrizione della risorsa, ad esempio l'insieme di credenziali di servizi di ripristino, per cui vengono raccolti i dati |
| VaultName                         | Testo          | Nome dell'insieme di credenziali                                            |
| VaultTags                         | Testo          | Tag associati alla risorsa dell'insieme di credenziali                    |
| VaultUniqueId                     | Testo          | Identificatore univoco dell'insieme di credenziali                             |
| SourceSystem                      | Testo          | Sistema di origine dei dati correnti-Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Questa tabella offre dettagli sui campi relativi agli avvisi.

| **Campo**                      | **Tipo di dati** | **Descrizione**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Testo          | Identificatore univoco per la risorsa per ia quale vengono raccolti i dati. Ad esempio, un ID di risorsa dell'insieme di credenziali di servizi di ripristino |
| OperationName                  | Testo          | Nome dell'operazione corrente. Ad esempio, avviso            |
| Category                       | Testo          | Categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure-AddonAzureBackupAlerts |
| AlertCode                      | Testo          | Codice per identificare in modo univoco un tipo di avviso                     |
| AlertConsolidationStatus       | Testo          | Verificare se l'avviso è un avviso consolidato         |
| AlertOccurrenceDateTime        | Datetime      | Data e ora di creazione dell'avviso                     |
| AlertRaisedOn                  | Testo          | Tipo di entità in cui viene generato l'avviso                        |
| AlertSeverity                  | Testo          | Gravità dell'avviso Ad esempio, Critical                 |
| AlertStatus                    | Testo          | Stato dell'avviso. Ad esempio, Active                     |
| AlertTimeToResolveInMinutes    | Number        | Tempo impiegato per risolvere un avviso. Vuoto per gli avvisi attivi.     |
| AlertType                      | Testo          | Tipo di avviso. Ad esempio, backup                           |
| AlertUniqueId                  | Testo          | Identificatore univoco dell'avviso generato                    |
| BackupItemUniqueId             | Testo          | Identificatore univoco dell'elemento di backup associato all'avviso |
| BackupManagementServerUniqueId | Testo          | Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile |
| BackupManagementType           | Testo          | Tipo di provider per il processo di backup del server, ad esempio IaaSVM, fileFolder |
| CountOfAlertsConsolidated      | Number        | Numero di avvisi consolidati se si tratta di un avviso consolidato  |
| ProtectedContainerUniqueId     | Testo          | Identificatore univoco del server protetto associato all'avviso |
| RecommendedAction              | Testo          | Azione consigliata per risolvere l'avviso                      |
| schemaVersion                  | Testo          | Versione corrente dello schema, ad esempio **v2**            |
| State                          | Testo          | Stato corrente dell'oggetto avviso, ad esempio Active o Deleted |
| StorageUniqueId                | Testo          | ID univoco usato per identificare l'entità di archiviazione                |
| VaultUniqueId                  | Testo          | ID univoco usato per identificare l'insieme di credenziali correlato all'avviso    |
| SourceSystem                   | Testo          | Sistema di origine dei dati correnti - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Questa tabella fornisce i campi correlati alle istanze protette di base.

| **Campo**                      | **Tipo di dati** | **Descrizione**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Testo          | Identificatore univoco per la risorsa in cui vengono raccolti i dati. Ad esempio, un ID di risorsa dell'insieme di credenziali di servizi di ripristino |
| OperationName                  | Testo          | Nome dell'operazione, ad esempio ProtectedInstance         |
| Category                       | Testo          | Categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure-AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Testo          | ID univoco dell'elemento di backup                                 |
| BackupManagementServerUniqueId | Testo          | Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile |
| BackupManagementType           | Testo          | Tipo di provider per il processo di backup del server, ad esempio IaaSVM, fileFolder |
| ProtectedContainerUniqueId     | Testo          | ID univoco per identificare il contenitore protetto in cui viene eseguito il processo |
| ProtectedInstanceCount         | Testo          | Conteggio delle istanze protette per l'elemento di backup associato o il contenitore protetto in tale data e ora |
| schemaVersion                  | Testo          | Versione corrente dello schema, ad esempio **v2**            |
| State                          | Testo          | Stato dell'oggetto elemento di backup, ad esempio Active, Deleted |
| VaultUniqueId                  | Testo          | Identificatore univoco dell'insieme di credenziali protetto associato all'istanza protetta |
| SourceSystem                   | Testo          | Sistema di origine dei dati correnti - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Questa tabella offre dettagli sui campi relativi al processo.

| **Campo**                      | **Tipo di dati** | **Descrizione**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Testo          | Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| OperationName                  | Testo          | Questo campo rappresenta il nome dell'operazione in corso - Processo    |
| Category                       | Testo          | Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure-AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Testo          | Campo per specificare se il processo è ad hoc o pianificato           |
| BackupItemUniqueId             | Testo          | ID univoco usato per identificare l'elemento di backup correlato all'entità di archiviazione |
| BackupManagementServerUniqueId | Testo          | ID univoco usato per identificare il server di gestione di backup correlato all'entità di archiviazione |
| BackupManagementType           | Testo          | Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM, fileFolder a cui appartiene questo avviso |
| DataTransferredInMB            | Number        | Dati trasferiti in MB per il processo                          |
| JobDurationInSecs              | Number        | Durata totale del processo in secondi                                |
| JobFailureCode                 | Testo          | Stringa di codice contenente un errore e a causa della quale si è verificato un errore del processo    |
| JobOperation                   | Testo          | Operazione per cui viene eseguito il processo, ad esempio backup, Restore, Configure backup |
| JobOperationSubType            | Testo          | Sottotipo dell'operazione di processo. Ad esempio, "log", nel caso di un processo di backup del log |
| JobStartDateTime               | Datetime      | Data e ora di avvio dell'esecuzione del processo                       |
| Stato processo                      | Testo          | Stato del processo completato, ad esempio Completed o Failed   |
| JobUniqueId                    | Testo          | ID univoco per identificare il processo                                |
| ProtectedContainerUniqueId     | Testo          | Identificatore univoco del server protetto associato all'avviso |
| RecoveryJobDestination         | Testo          | Destinazione di un processo di ripristino in cui vengono recuperati i dati   |
| RecoveryJobRPDateTime          | Datetime      | Data, ora in cui è stato creato il punto di ripristino da ripristinare |
| RecoveryJobLocation            | Testo          | Posizione in cui è stato archiviato il punto di ripristino da ripristinare |
| RecoveryLocationType           | Testo          | Tipo di percorso di ripristino                                |
| schemaVersion                  | Testo          | Versione corrente dello schema, ad esempio **v2**            |
| State                          | Testo          | Stato corrente dell'oggetto avviso, ad esempio Active, Deleted |
| VaultUniqueId                  | Testo          | Identificatore univoco dell'insieme di credenziali protetto associato all'avviso |
| SourceSystem                   | Testo          | Sistema di origine dei dati correnti - Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

Questa tabella offre dettagli sui campi relativi al criterio.

| **Campo**                       | **Tipo di dati**  | **Descrizione**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Testo           | Identificatore univoco per la risorsa in cui vengono raccolti i dati. Ad esempio, un ID di risorsa dell'insieme di credenziali di servizi di ripristino |
| OperationName                   | Testo           | Nome dell'operazione, ad esempio Policy o PolicyAssociation |
| Category                        | Testo           | Categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure-AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Testo           | Giorni della settimana per i quali sono stati pianificati backup            |
| BackupFrequency                 | Testo           | Frequenza con cui vengono eseguiti i backup. Ad esempio, ogni giorno, ogni settimana |
| BackupManagementType            | Testo           | Tipo di provider per il processo di backup del server. Ad esempio, IaaSVM, fileFolder |
| BackupManagementServerUniqueId  | Testo           | Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile |
| BackupTimes                     | Testo           | Data e ora in cui sono pianificati backup                     |
| DailyRetentionDuration          | Numero intero   | Durata totale di mantenimento dati in giorni per i backup configurati      |
| DailyRetentionTimes             | Testo           | Data e ora di configurazione del mantenimento dati giornaliero            |
| DiffBackupDaysOfTheWeek         | Testo           | Giorni della settimana per i backup differenziali per SQL nel backup di macchine virtuali di Azure |
| DiffBackupFormat                | Testo           | Formato per i backup differenziali per SQL nel backup di macchine virtuali di Azure   |
| DiffBackupRetentionDuration     | Numero decimale | Durata di conservazione per i backup differenziali per SQL nel backup di macchine virtuali di Azure |
| DiffBackupTime                  | Tempo           | Tempo per i backup differenziali per SQL nel backup di macchine virtuali di Azure     |
| LogBackupFrequency              | Numero decimale | Frequenza dei backup del log per SQL                            |
| LogBackupRetentionDuration      | Numero decimale | Durata del periodo di memorizzazione per i backup del log per SQL nel backup di macchine virtuali di Azure |
| MonthlyRetentionDaysOfTheMonth  | Testo           | Settimane del mese in cui è configurata la conservazione mensile.  Ad esempio, First, Last e così via. |
| MonthlyRetentionDaysOfTheWeek   | Testo           | Giorni della settimana selezionati per il mantenimento dati mensile              |
| MonthlyRetentionDuration        | Testo           | Durata totale di mantenimento dati in mesi per i backup configurati    |
| MonthlyRetentionFormat          | Testo           | Tipo di configurazione per la conservazione mensile. Ad esempio, giornaliero per il giorno basato su, settimanalmente per la settimana |
| MonthlyRetentionTimes           | Testo           | Data e ora di configurazione del mantenimento dati mensile           |
| MonthlyRetentionWeeksOfTheMonth | Testo           | Settimane del mese in cui è configurata la conservazione mensile.   Ad esempio, First, Last e così via. |
| PolicyName                      | Testo           | Nome dei criteri definiti                                   |
| PolicyUniqueId                  | Testo           | ID univoco per identificare il criterio                             |
| PolicyTimeZone                  | Testo           | Fuso orario in cui vengono specificati i campi temporali dei criteri nei log |
| RetentionDuration               | Testo           | Durata di mantenimento dei backup configurati                    |
| RetentionType                   | Testo           | Tipo di conservazione                                            |
| schemaVersion                   | Testo           | Questo campo indica la versione corrente dello schema, ovvero **v2** |
| State                           | Testo           | Stato corrente dell'oggetto criterio. Ad esempio, Active, Deleted |
| SynchronisationFrequencyPerDay  | Numero intero   | Numero di volte in un giorno in cui è stato sincronizzato un backup di file per SC DPM e MAB |
| VaultUniqueId                   | Testo           | ID univoco dell'insieme di credenziali a cui appartiene questo criterio          |
| WeeklyRetentionDaysOfTheWeek    | Testo           | Giorni della settimana selezionati per il mantenimento dati settimanale               |
| WeeklyRetentionDuration         | Numero decimale | Durata totale di mantenimento settimanale in settimane per i backup configurati |
| WeeklyRetentionTimes            | Testo           | Data e ora di configurazione del mantenimento dati settimanale            |
| YearlyRetentionDaysOfTheMonth   | Testo           | Date del mese selezionate per il mantenimento dati annuale             |
| YearlyRetentionDaysOfTheWeek    | Testo           | Giorni della settimana selezionati per il mantenimento dati annuale               |
| YearlyRetentionDuration         | Numero decimale | Durata totale di mantenimento dati in anni per i backup configurati     |
| YearlyRetentionFormat           | Testo           | Tipo di configurazione per la conservazione annuale, ad esempio, giornaliera per il giorno in base, settimanale per la settimana |
| YearlyRetentionMonthsOfTheYear  | Testo           | Mesi dell'anno selezionati per il mantenimento dati annuale             |
| YearlyRetentionTimes            | Testo           | Data e ora di configurazione del mantenimento dati annuale            |
| YearlyRetentionWeeksOfTheMonth  | Testo           | Settimane del mese selezionate per la conservazione annuale             |
| SourceSystem                    | Testo           | Sistema di origine dei dati correnti - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

Questa tabella offre dettagli sui campi relativi all'archiviazione.

| **Campo**                      | **Tipo di dati** | **Descrizione**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Testo          | Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| OperationName                  | Testo          | Questo campo rappresenta il nome dell'operazione corrente, ovvero storage o StorageAssociation |
| Category                       | Testo          | Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure-AddonAzureBackupStorage |
| BackupItemUniqueId             | Testo          | ID univoco usato per identificare l'elemento di backup per le macchine virtuali di cui è stato eseguito il backup con DPM, MAB |
| BackupManagementServerUniqueId | Testo          | Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile |
| BackupManagementType           | Testo          | Tipo di provider per il processo di backup del server. Ad esempio, IaaSVM, fileFolder |
| PreferredWorkloadOnVolume      | Testo          | Carico di lavoro per il quale il volume è la risorsa di archiviazione preferita      |
| ProtectedContainerUniqueId     | Testo          | Identificatore univoco del server protetto associato all'avviso |
| schemaVersion                  | Testo          | Versione dello schema. Ad esempio, **v2**                   |
| State                          | Testo          | Stato dell'oggetto elemento di backup. Ad esempio, Active, Deleted |
| StorageAllocatedInMBs          | Number        | Dimensioni dello spazio di archiviazione allocato dall'elemento di backup corrispondente nello spazio di archiviazione corrispondente di tipo Disk |
| StorageConsumedInMBs           | Number        | Dimensioni dello spazio di archiviazione utilizzato dall'elemento di backup corrispondente nell'archivio corrispondente |
| StorageName                    | Testo          | Nome dell'entità di archiviazione. Ad esempio, E:\                      |
| StorageTotalSizeInGBs          | Testo          | Dimensioni totali dello spazio di archiviazione, in GB, utilizzate dall'entità di archiviazione     |
| StorageType                    | Testo          | Tipo di archiviazione, ad esempio cloud, volume, disco             |
| StorageUniqueId                | Testo          | ID univoco usato per identificare l'entità di archiviazione                |
| VaultUniqueId                  | Testo          | ID univoco usato per identificare l'insieme di credenziali correlato all'entità di archiviazione |
| VolumeFriendlyName             | Testo          | Nome descrittivo del volume di archiviazione                          |
| SourceSystem                   | Testo          | Sistema di origine dei dati correnti - Azure                    |

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come inviare i dati di diagnostica a Log Analytics](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)
- [Informazioni su come scrivere query in tabelle specifiche delle risorse](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor#sample-kusto-queries)