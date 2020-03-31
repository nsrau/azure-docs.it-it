---
title: Modello di dati per gli eventi di diagnostica di Backup di AzureData model for Azure Backup diagnostics events
description: Questo modello di dati fa riferimento alla modalità specifica della risorsa per l'invio di eventi di diagnostica a Log Analytics (LA).
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 267753ee1647739e36d92b64f50d8a8be87537d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77583385"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Modello di dati per gli eventi di diagnostica di Backup di AzureData Model for Azure Backup Diagnostics Events

## <a name="coreazurebackup"></a>CoreAzureBackup

Questa tabella fornisce informazioni sulle entità di backup principali, ad esempio gli insiemi di credenziali e gli elementi di backup.

| **Campo**                         | **Tipo di dati** | **Descrizione**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Testo          | Identificatore della risorsa per i dati raccolti. Ad esempio, ID risorsa dell'insieme di credenziali di Servizi di ripristino. |
| OperationName                     | Testo          | Questo campo rappresenta il nome dell'operazione corrente, ovvero BackupItem, BackupItemAssociation o ProtectedContainer. |
| Category                          | Testo          | Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di Monitoraggio di Azure.This field represents the category of diagnostics data pushed to Azure Monitor logs. Ad esempio, CoreAzureBackup.For example, CoreAzureBackup. |
| AgentVersion (VersioneAgente)                      | Testo          | Numero di versione del backup dell'agente o dell'agente protezione (in caso di DPM E MABS per SC) |
| AzureBackupAgentVersion           | Testo          | Versione dell'agente di Backup di Azure nel server di gestione di backup |
| AzureDataCenter                   | Testo          | Data center in cui si trova il vault                       |
| BackupItemAppVersion              | Testo          | Versione dell'applicazione dell'elemento di backup                       |
| BackupItemFriendlyName            | Testo          | Nome descrittivo dell'elemento di backup                             |
| BackupItemName                    | Testo          | Nome dell'elemento di backup                                      |
| BackupItemProtectionState         | Testo          | Stato di protezione dell'elemento di backup                          |
| BackupItemFrontEndSize            | Testo          | Dimensioni front-end dell'elemento di backup                            |
| BackupItemType                    | Testo          | Tipo di elemento di backup. Ad esempio: VM, FileFolder             |
| BackupItemUniqueId                | Testo          | Identificatore univoco dell'elemento di backup                         |
| Tipo Di BackupManagementServerType        | Testo          | Tipo di server di gestione del backup, come in MABS, SC DPM     |
| Id BackupManagementServerUniqueId    | Testo          | Campo per identificare in modo univoco il server di gestione di backup      |
| BackupManagementType              | Testo          | Tipo di provider per il server che esegue il processo di backup. Ad esempio, IaaSVM, FileFolder |
| NomeServerBackupManagementServerName        | Testo          | Nome del server di gestione di backup                         |
| BackupManagementServerOSVersion   | Testo          | Versione del sistema operativo del server di gestione di backup                   |
| BackupManagementServerVersion     | Testo          | Versione del server di gestione di backup                      |
| UltimaPosizionediscollegamento       | Testo          | Percorso del punto di ripristino più recente per l'elemento di backup    |
| Ultime RecoveryPointTime           | Datetime      | Data dell'ora dell'ultimo punto di ripristino per l'elemento di backup   |
| OldestRecoveryPointLocation       | Testo          | Percorso del punto di ripristino meno recente per l'elemento di backup    |
| OldestRecoveryPointTime           | Datetime      | Data dell'ora dell'ultimo punto di ripristino per l'elemento di backup   |
| PolicyUniqueId                    | Testo          | ID univoco per identificare il criterio                             |
| ProtectedContainerFriendlyName    | Testo          | Nome descrittivo del server protetto                        |
| ProtectedContainerLocation        | Testo          | Se il contenitore protetto si trova in locale o in Azure |
| ProtectedContainerName (NomeContenitore Protetto)            | Testo          | Nome del contenitore protetto                            |
| ProtectedContainerOSType          | Testo          | Tipo di sistema operativo del contenitore protetto                          |
| ProtectedContainerOSVersion       | Testo          | Versione del sistema operativo del contenitore protetto                        |
| ProtectedContainerProtectionState | Testo          | Stato di protezione del contenitore protetto                  |
| ProtectedContainerType (Tipodicontenitore Protetto)            | Testo          | Se il contenitore protetto è un server o un contenitore  |
| ProtectedContainerUniqueId        | Testo          | ID univoco utilizzato per identificare il contenitore protetto per tutti gli elementi tranne le macchine virtuali di cui è stato eseguito il backup tramite DPM, MABS |
| ProtectedContainerWorkloadType    | Testo          | Tipo di contenitore protetto di cui è stato eseguito il backup. Ad esempio, IaaSVMContainer |
| ProtectionGroupName               | Testo          | Nome del gruppo protezione dati in cui è protetto l'elemento di backup, per SC DPM e MABS, se applicabile |
| ResourceGroupName                 | Testo          | Gruppo di risorse della risorsa (ad esempio, l'insieme di credenziali di Servizi di ripristino) per i dati raccolti |
| schemaVersion                     | Testo          | Questo campo indica la versione corrente dello schema, è **V2** |
| SecondaryBackupProtectionState (Informazioni in base alla protezione dei nomi SecondaryBackup    | Testo          | Se la protezione secondaria è abilitata per l'elemento di backup  |
| State                             | Testo          | Stato dell'oggetto elemento di backup. Ad esempio, Attivo, Eliminato |
| StorageReplicationType            | Testo          | Tipo di replica di archiviazione per l'insieme di credenziali. Ad esempio, GeoRedundant |
| SubscriptionId                    | Testo          | Identificatore di sottoscrizione della risorsa (ad esempio, l'insieme di credenziali dei servizi di ripristino) per il quale vengono raccolti i dati |
| VaultName                         | Testo          | Nome dell'insieme di credenziali                                            |
| VaultTags                         | Testo          | Tag associati alla risorsa del vault                    |
| VaultUniqueId                     | Testo          | Identificatore univoco del vault                             |
| SourceSystem                      | Testo          | Sistema di origine dei dati correnti - Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Questa tabella offre dettagli sui campi relativi agli avvisi.

| **Campo**                      | **Tipo di dati** | **Descrizione**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Testo          | Identificatore univoco per la risorsa per ia quale vengono raccolti i dati. Ad esempio, un ID risorsa dell'insieme di credenziali dei servizi di ripristinoFor example, a Recovery Services vault resource ID |
| OperationName                  | Testo          | Nome dell'operazione corrente. Ad esempio, Alert            |
| Category                       | Testo          | Categoria di dati di diagnostica inseriti nei log di Monitoraggio di Azure - AddonAzureBackupAlerts |
| Codice avviso                      | Testo          | Codice per identificare in modo univoco un tipo di avvisoCode to uniquely identify an alert type                     |
| AlertConsolidationStatus       | Testo          | Identificare se l'avviso è un avviso consolidato o meno         |
| AlertOccurrenceDateTime        | Datetime      | Data e ora di creazione dell'avviso                     |
| AlertRaisedOn                  | Testo          | Tipo di entità su cui viene generato l'avviso                        |
| AlertSeverity                  | Testo          | Gravità dell'avviso Ad esempio, Critical                 |
| AlertStatus                    | Testo          | Stato dell'avviso. Ad esempio, Attivo                     |
| AlertTimeToResolveInMinutes (Avviso    | Number        | Tempo impiegato per risolvere un avviso. Vuoto per gli avvisi attivi.     |
| AlertType                      | Testo          | Tipo di avviso. Ad esempio, Backup                           |
| AlertUniqueId                  | Testo          | Identificatore univoco dell'avviso generato                    |
| BackupItemUniqueId             | Testo          | Identificatore univoco dell'elemento di backup associato all'avviso |
| Id BackupManagementServerUniqueId | Testo          | Campo per identificare in modo univoco il server di gestione di backup attraverso la protezione dell'elemento di backup, se applicabile |
| BackupManagementType           | Testo          | Tipo di provider per il server che esegue il processo di backup, ad esempio IaaSVM, FileFolder |
| CountOfAlertsConsolidated      | Number        | Numero di avvisi consolidati se si tratta di un avviso consolidato  |
| ProtectedContainerUniqueId     | Testo          | Identificatore univoco del server protetto associato all'avviso |
| RecommendedAction (Azione consigliata)              | Testo          | Azione consigliata per risolvere l'avviso                      |
| schemaVersion                  | Testo          | Versione corrente dello schema, ad esempio **V2**            |
| State                          | Testo          | Stato corrente dell'oggetto avviso, ad esempio Active o Deleted |
| StorageUniqueId                | Testo          | ID univoco utilizzato per identificare l'entità di archiviazione                |
| VaultUniqueId                  | Testo          | ID univoco utilizzato per identificare il vault correlato all'avviso    |
| SourceSystem                   | Testo          | Sistema di origine dei dati correnti - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstanceAddonAzureBackupProtectedInstance

In questa tabella vengono forniti i campi correlati alle istanze protette di base.

| **Campo**                      | **Tipo di dati** | **Descrizione**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Testo          | Identificatore univoco della risorsa in merito ai dati raccolti. Ad esempio, un ID risorsa dell'insieme di credenziali dei servizi di ripristinoFor example, a Recovery Services vault resource ID |
| OperationName                  | Testo          | Nome dell'operazione, ad esempio ProtectedInstance         |
| Category                       | Testo          | Categoria di dati di diagnostica inseriti nei log di Monitoraggio di Azure - AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Testo          | ID univoco dell'elemento di backup                                 |
| Id BackupManagementServerUniqueId | Testo          | Campo per identificare in modo univoco il server di gestione di backup attraverso la protezione dell'elemento di backup, se applicabile |
| BackupManagementType           | Testo          | Tipo di provider per il server che esegue il processo di backup, ad esempio IaaSVM, FileFolder |
| ProtectedContainerUniqueId     | Testo          | ID univoco per identificare il contenitore protetto in cui viene eseguito il processo |
| ProtectedInstanceCount (proprietà ProtectedInstanceCount)         | Testo          | Numero di istanze protette per l'elemento di backup associato o il contenitore protetto in tale data e oraCount of Protected Instances for the associated backup item or protected container on that date-time |
| schemaVersion                  | Testo          | Versione corrente dello schema, ad esempio **V2**            |
| State                          | Testo          | Stato dell'oggetto elemento di backup, ad esempio Attivo, Eliminato |
| VaultUniqueId                  | Testo          | Identificatore univoco del vault protetto associato all'istanza protetta |
| SourceSystem                   | Testo          | Sistema di origine dei dati correnti - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobsAddonAzureBackupJobs

Questa tabella offre dettagli sui campi relativi al processo.

| **Campo**                      | **Tipo di dati** | **Descrizione**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Testo          | Identificatore della risorsa per i dati raccolti. Ad esempio, ID risorsa dell'insieme di credenziali dei servizi di ripristinoFor example, Recovery Services vault resource ID |
| OperationName                  | Testo          | Questo campo rappresenta il nome dell'operazione in corso - Processo    |
| Category                       | Testo          | Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di Monitoraggio di Azure - AddonAzureBackupJobs |
| AdhocOrScheduledJob (AdhocOrScheduledJob)            | Testo          | Campo per specificare se il processo è ad hoc o Programmato           |
| BackupItemUniqueId             | Testo          | ID univoco utilizzato per identificare l'elemento di backup correlato all'entità di archiviazione |
| Id BackupManagementServerUniqueId | Testo          | ID univoco utilizzato per identificare il server di gestione del backup correlato all'entità di archiviazione |
| BackupManagementType           | Testo          | Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM, FileFolder a cui appartiene l'avviso |
| DataTransferredInMB            | Number        | Dati trasferiti in MB per il processo                          |
| JobDurationInSecs              | Number        | Durata totale del processo in secondi                                |
| JobFailureCode                 | Testo          | Stringa di codice contenente un errore e a causa della quale si è verificato un errore del processo    |
| JobOperation                   | Testo          | Operazione per cui viene eseguito il processo, ad esempio Backup, Ripristino, Configurazione backup |
| JobOperationSubType (ProcessoOperationSubTipo)            | Testo          | Sottotipo dell'operazione di processo. Ad esempio, "Log", nel caso del processo di backup del log |
| JobStartDateTime (UtenteIniziaDataOra)               | Datetime      | Data e ora di avvio dell'esecuzione del processo                       |
| Stato processo                      | Testo          | Stato del processo completato, ad esempio Completed o Failed   |
| JobUniqueId                    | Testo          | ID univoco per identificare il processo                                |
| ProtectedContainerUniqueId     | Testo          | Identificatore univoco del server protetto associato all'avviso |
| RecoveryJobDestination         | Testo          | Destinazione di un processo di ripristino in cui vengono recuperati i dati   |
| RecoveryJobRPDateTime          | Datetime      | Data, ora di creazione del punto di ripristino in fase di recupero |
| RecoveryJobLocation            | Testo          | Percorso in cui è stato archiviato il punto di ripristino in corso di ripristino |
| RecoveryLocationType           | Testo          | Tipo di percorso di ripristino                                |
| schemaVersion                  | Testo          | Versione corrente dello schema, ad esempio **V2**            |
| State                          | Testo          | Stato corrente dell'oggetto avviso, ad esempio Attivo, Eliminato |
| VaultUniqueId                  | Testo          | Identificatore univoco del vault protetto associato all'avviso |
| SourceSystem                   | Testo          | Sistema di origine dei dati correnti - Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicyAddonAzureBackupPolicy

Questa tabella offre dettagli sui campi relativi al criterio.

| **Campo**                       | **Tipo di dati**  | **Descrizione**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Testo           | Identificatore univoco della risorsa in merito ai dati raccolti. Ad esempio, un ID risorsa dell'insieme di credenziali dei servizi di ripristinoFor example, a Recovery Services vault resource ID |
| OperationName                   | Testo           | Nome dell'operazione, ad esempio Policy o PolicyAssociation |
| Category                        | Testo           | Categoria di dati di diagnostica inseriti nei log di Monitoraggio di Azure - AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Testo           | Giorni della settimana per i quali sono stati pianificati backup            |
| BackupFrequency                 | Testo           | Frequenza con cui vengono eseguiti i backup. Ad esempio, ogni giorno, ogni settimana |
| BackupManagementType            | Testo           | Tipo di provider per il server che esegue il processo di backup. Ad esempio, IaaSVM, FileFolder |
| Id BackupManagementServerUniqueId  | Testo           | Campo per identificare in modo univoco il server di gestione di backup attraverso la protezione dell'elemento di backup, se applicabile |
| BackupTimes                     | Testo           | Data e ora in cui sono pianificati backup                     |
| DailyRetentionDuration          | Numero intero   | Durata totale di mantenimento dati in giorni per i backup configurati      |
| DailyRetentionTimes             | Testo           | Data e ora di configurazione del mantenimento dati giornaliero            |
| DiffBackupDaysOfTheWeek         | Testo           | Giorni della settimana per i backup differenziali per SQL nel backup della macchina virtuale di Azure |
| Formato DiffBackup                | Testo           | Formato per i backup differenziali per SQL nel backup della macchina virtuale di AzureFormat for Differential backups for SQL in Azure VM backup   |
| DiffBackupRetentionDuration     | Numero decimale | Durata di conservazione per i backup differenziali per SQL nel backup della macchina virtuale di AzureRetention duration for Differential backups for SQL in Azure VM Backup |
| DiffBackupTime (ora di diff>)                  | Tempo           | Tempo per i backup differenziali per SQL nel backup della macchina virtuale di AzureTime for Differential backups for SQL in Azure VM Backup     |
| LogBackupFrequency              | Numero decimale | Frequenza dei backup del log per SQL                            |
| LogBackupRetentionDuration      | Numero decimale | Durata di conservazione per i backup del log per SQL nel backup della macchina virtuale di AzureRetention duration for Log backups for SQL in Azure VM Backup |
| MonthlyRetentionDaysOfTheMonth  | Testo           | Settimane del mese in cui è configurata la conservazione mensile.  Ad esempio, Primo, Ultimo, ecc. |
| MonthlyRetentionDaysOfTheWeek   | Testo           | Giorni della settimana selezionati per il mantenimento dati mensile              |
| MonthlyRetentionDuration        | Testo           | Durata totale di mantenimento dati in mesi per i backup configurati    |
| MonthlyRetentionFormat          | Testo           | Tipo di configurazione per la conservazione mensile. Ad esempio, ogni giorno per il giorno, settimanalmente per |
| MonthlyRetentionTimes           | Testo           | Data e ora di configurazione del mantenimento dati mensile           |
| MonthlyRetentionWeeksOfTheMonth | Testo           | Settimane del mese in cui è configurata la conservazione mensile.   Ad esempio, Primo, Ultimo, ecc. |
| PolicyName                      | Testo           | Nome dei criteri definiti                                   |
| PolicyUniqueId                  | Testo           | ID univoco per identificare il criterio                             |
| PolicyTime-one                  | Testo           | Fuso orario in cui i campi dell'ora dei criteri sono specificati nei registri |
| RetentionDuration               | Testo           | Durata di mantenimento dei backup configurati                    |
| RetentionType                   | Testo           | Tipo di conservazione                                            |
| schemaVersion                   | Testo           | Questo campo indica la versione corrente dello schema, è **V2** |
| State                           | Testo           | Stato corrente dell'oggetto criterio. Ad esempio, Attivo, Eliminato |
| SincronizzazioneFrequenzAFrequenza  | Numero intero   | Numero di volte in un giorno un backup di file viene sincronizzato per SC DPM e MABS |
| VaultUniqueId                   | Testo           | ID univoco del vault a cui appartiene questo criterio          |
| WeeklyRetentionDaysOfTheWeek    | Testo           | Giorni della settimana selezionati per il mantenimento dati settimanale               |
| WeeklyRetentionDuration         | Numero decimale | Durata totale della conservazione settimanale in settimane per i backup configurati |
| WeeklyRetentionTimes            | Testo           | Data e ora di configurazione del mantenimento dati settimanale            |
| YearlyRetentionDaysOfTheMonth   | Testo           | Date del mese selezionate per il mantenimento dati annuale             |
| YearlyRetentionDaysOfTheWeek    | Testo           | Giorni della settimana selezionati per il mantenimento dati annuale               |
| YearlyRetentionDuration         | Numero decimale | Durata totale di mantenimento dati in anni per i backup configurati     |
| YearlyRetentionFormat           | Testo           | Tipo di configurazione per la conservazione annuale, ad esempio giornaliera per il giorno, settimanale per la settimana |
| YearlyRetentionMonthsOfTheYear  | Testo           | Mesi dell'anno selezionati per il mantenimento dati annuale             |
| YearlyRetentionTimes            | Testo           | Data e ora di configurazione del mantenimento dati annuale            |
| YearlyRetentionWeeksOfTheMonth  | Testo           | Settimane del mese selezionato per la conservazione annuale             |
| SourceSystem                    | Testo           | Sistema di origine dei dati correnti - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorageAddonAzureBackupStorage

Questa tabella offre dettagli sui campi relativi all'archiviazione.

| **Campo**                      | **Tipo di dati** | **Descrizione**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Testo          | Identificatore della risorsa per i dati raccolti. Ad esempio, ID risorsa dell'insieme di credenziali dei servizi di ripristinoFor example, Recovery Services vault resource ID |
| OperationName                  | Testo          | Questo campo rappresenta il nome dell'operazione corrente - Storage or StorageAssociation |
| Category                       | Testo          | Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di Monitoraggio di Azure - AddonAzureBackupStorage |
| BackupItemUniqueId             | Testo          | ID univoco utilizzato per identificare l'elemento di backup per le macchine virtuali di cui è stato eseguito il backup tramite DPM, MABS |
| Id BackupManagementServerUniqueId | Testo          | Campo per identificare in modo univoco il server di gestione di backup attraverso la protezione dell'elemento di backup, se applicabile |
| BackupManagementType           | Testo          | Tipo di provider per il server che esegue il processo di backup. Ad esempio, IaaSVM, FileFolder |
| PreferredWorkloadOnVolumePreferredWorkloadOnVolume      | Testo          | Carico di lavoro per il quale questo volume è l'archiviazione preferitaWorkload for which this volume is the preferred storage      |
| ProtectedContainerUniqueId     | Testo          | Identificatore univoco del server protetto associato all'avviso |
| schemaVersion                  | Testo          | Versione dello schema. Ad esempio, **V2**                   |
| State                          | Testo          | Stato dell'oggetto elemento di backup. Ad esempio, Attivo, Eliminato |
| StorageAllocatedInMB          | Number        | Dimensione dello spazio di archiviazione allocato dall'elemento di backup corrispondente nell'archivio corrispondente di tipo Disco |
| StorageConsumedInMB           | Number        | Dimensioni dello spazio di archiviazione utilizzate dall'elemento di backup corrispondente nell'archivio corrispondente |
| StorageName                    | Testo          | Nome dell'entità di archiviazione. Ad esempio, E:                      |
| StorageTotalSizeInGB          | Testo          | Dimensioni totali dello spazio di archiviazione, in GB, utilizzate dall'entità di archiviazione     |
| StorageType                    | Testo          | Tipo di archiviazione, ad esempio Cloud, Volume, Disco             |
| StorageUniqueId                | Testo          | ID univoco utilizzato per identificare l'entità di archiviazione                |
| VaultUniqueId                  | Testo          | ID univoco utilizzato per identificare l'insieme di credenziali correlato all'entità di archiviazione |
| VolumeFriendlyName             | Testo          | Nome descrittivo del volume di archiviazione                          |
| SourceSystem                   | Testo          | Sistema di origine dei dati correnti - Azure                    |

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come inviare dati di diagnostica a Log Analytics](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)
- [Informazioni su come scrivere query in tabelle specifiche delle risorse](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor#sample-kusto-queries)