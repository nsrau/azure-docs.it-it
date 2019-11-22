---
title: Modello di dati per gli eventi di diagnostica di backup di Azure
description: Questo modello di dati è in riferimento alla modalità specifica della risorsa di invio di eventi di diagnostica a Log Analytics (LA).
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 86416d0d74296069878aa7f33b549102a52ea488
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281077"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Modello di dati per gli eventi di diagnostica di backup di Azure

## <a name="coreazurebackup"></a>CoreAzureBackup

Questa tabella fornisce informazioni sulle entità di backup di base, ad esempio insiemi di credenziali ed elementi di backup.

| **Campo**                         | **Tipo di dati** | **Descrizione**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Text          | Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino. |
| OperationName                     | Text          | Questo campo rappresenta il nome dell'operazione corrente-BackupItem, BackupItemAssociation o ProtectedContainer. |
| Categoria                          | Text          | Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure. Ad esempio, CoreAzureBackup. |
| AgentVersion                      | Text          | Numero di versione del backup dell'agente o dell'agente protezione (in caso di SC DPM e MAB) |
| AzureBackupAgentVersion           | Text          | Versione dell'agente di backup di Azure nel server di gestione di backup |
| AzureDataCenter                   | Text          | Data Center in cui si trova l'insieme di credenziali                       |
| BackupItemAppVersion              | Text          | Versione dell'applicazione dell'elemento di backup                       |
| BackupItemFriendlyName            | Text          | Nome descrittivo dell'elemento di backup                             |
| BackupItemName                    | Text          | Nome dell'elemento di backup                                      |
| BackupItemProtectionState         | Text          | Stato di protezione dell'elemento di backup                          |
| BackupItemFrontEndSize            | Text          | Dimensioni front-end dell'elemento di backup                            |
| BackupItemType                    | Text          | Tipo di elemento di backup. Ad esempio: VM, fileFolder             |
| BackupItemUniqueId                | Text          | Identificatore univoco dell'elemento di backup                         |
| BackupManagementServerType        | Text          | Tipo di server di gestione di backup, come in MAB, SC DPM     |
| BackupManagementServerUniqueId    | Text          | Per identificare in modo univoco il server di gestione di backup      |
| BackupManagementType              | Text          | Tipo di provider per il processo di backup del server. Ad esempio, IaaSVM, fileFolder |
| BackupManagementServerName        | Text          | Nome del server di gestione di backup                         |
| BackupManagementServerOSVersion   | Text          | Versione del sistema operativo del server di gestione di backup                   |
| BackupManagementServerVersion     | Text          | Versione del server di gestione di backup                      |
| LatestRecoveryPointLocation       | Text          | Posizione del punto di ripristino più recente per l'elemento di backup    |
| LatestRecoveryPointTime           | DateTime      | Data e ora dell'ultimo punto di ripristino per l'elemento di backup   |
| OldestRecoveryPointLocation       | Text          | Posizione del punto di ripristino meno recente per l'elemento di backup    |
| OldestRecoveryPointTime           | DateTime      | Data e ora dell'ultimo punto di ripristino per l'elemento di backup   |
| PolicyUniqueId                    | Text          | ID univoco per identificare il criterio                             |
| ProtectedContainerFriendlyName    | Text          | Nome descrittivo del server protetto                        |
| ProtectedContainerLocation        | Text          | Indica se il contenitore protetto si trova in locale o in Azure |
| ProtectedContainerName            | Text          | Nome del contenitore protetto                            |
| ProtectedContainerOSType          | Text          | Tipo di sistema operativo del contenitore protetto                          |
| ProtectedContainerOSVersion       | Text          | Versione del sistema operativo del contenitore protetto                        |
| ProtectedContainerProtectionState | Text          | Stato di protezione del contenitore protetto                  |
| ProtectedContainerType            | Text          | Indica se il contenitore protetto è un server o un contenitore  |
| ProtectedContainerUniqueId        | Text          | ID univoco usato per identificare il contenitore protetto per tutti gli elementi eccetto le macchine virtuali di cui è stato eseguito il backup con DPM, MAB |
| ProtectedContainerWorkloadType    | Text          | Tipo del contenitore protetto di cui è stato eseguito il backup. Ad esempio, IaaSVMContainer |
| Nomegruppoprotezione               | Text          | Nome del gruppo protezione dati in cui è protetto l'elemento di backup, per SC DPM e MAB, se applicabile |
| ResourceGroupName                 | Text          | Gruppo di risorse della risorsa (ad esempio, l'insieme di credenziali di servizi di ripristino) per i dati raccolti |
| SchemaVersion                     | Text          | Questo campo indica la versione corrente dello schema, ovvero **v2** |
| SecondaryBackupProtectionState    | Text          | Indica se la protezione secondaria è abilitata per l'elemento di backup  |
| Stato                             | Text          | Stato dell'oggetto elemento di backup. Ad esempio, Active, Deleted |
| StorageReplicationType            | Text          | Tipo di replica di archiviazione per l'insieme di credenziali. Ad esempio, georidondante |
| SubscriptionId                    | Text          | Identificatore della sottoscrizione della risorsa, ad esempio l'insieme di credenziali di servizi di ripristino, per cui vengono raccolti i dati |
| VaultName                         | Text          | Nome dell'insieme di credenziali                                            |
| VaultTags                         | Text          | Tag associati alla risorsa dell'insieme di credenziali                    |
| VaultUniqueId                     | Text          | Identificatore univoco dell'insieme di credenziali                             |
| SourceSystem                      | Text          | Sistema di origine dei dati correnti-Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Questa tabella offre dettagli sui campi relativi agli avvisi.

| **Campo**                      | **Tipo di dati** | **Descrizione**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Identificatore univoco per la risorsa per ia quale vengono raccolti i dati. Ad esempio, un ID di risorsa dell'insieme di credenziali di servizi di ripristino |
| OperationName                  | Text          | Nome dell'operazione corrente. Ad esempio, avviso            |
| Categoria                       | Text          | Categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure-AddonAzureBackupAlerts |
| AlertCode                      | Text          | Codice per identificare in modo univoco un tipo di avviso                     |
| AlertConsolidationStatus       | Text          | Verificare se l'avviso è un avviso consolidato         |
| AlertOccurrenceDateTime        | DateTime      | Data e ora di creazione dell'avviso                     |
| AlertRaisedOn                  | Text          | Tipo di entità in cui viene generato l'avviso                        |
| AlertSeverity                  | Text          | Gravità dell'avviso Ad esempio, Critical                 |
| AlertStatus                    | Text          | Stato dell'avviso. Ad esempio, Active                     |
| AlertTimeToResolveInMinutes    | NUMBER        | Tempo impiegato per risolvere un avviso. Vuoto per gli avvisi attivi.     |
| AlertType                      | Text          | Tipo di avviso. Ad esempio, backup                           |
| AlertUniqueId                  | Text          | Identificatore univoco dell'avviso generato                    |
| BackupItemUniqueId             | Text          | Identificatore univoco dell'elemento di backup associato all'avviso |
| BackupManagementServerUniqueId | Text          | Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile |
| BackupManagementType           | Text          | Tipo di provider per il processo di backup del server, ad esempio IaaSVM, fileFolder |
| CountOfAlertsConsolidated      | NUMBER        | Numero di avvisi consolidati se si tratta di un avviso consolidato  |
| ProtectedContainerUniqueId     | Text          | Identificatore univoco del server protetto associato all'avviso |
| RecommendedAction              | Text          | Azione consigliata per risolvere l'avviso                      |
| SchemaVersion                  | Text          | Versione corrente dello schema, ad esempio **v2**            |
| Stato                          | Text          | Stato corrente dell'oggetto avviso, ad esempio Active o Deleted |
| StorageUniqueId                | Text          | ID univoco usato per identificare l'entità di archiviazione                |
| VaultUniqueId                  | Text          | ID univoco usato per identificare l'insieme di credenziali correlato all'avviso    |
| SourceSystem                   | Text          | Sistema di origine dei dati correnti - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Questa tabella fornisce i campi correlati alle istanze protette di base.

| **Campo**                      | **Tipo di dati** | **Descrizione**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Identificatore univoco per la risorsa in cui vengono raccolti i dati. Ad esempio, un ID di risorsa dell'insieme di credenziali di servizi di ripristino |
| OperationName                  | Text          | Nome dell'operazione, ad esempio ProtectedInstance         |
| Categoria                       | Text          | Categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure-AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Text          | ID univoco dell'elemento di backup                                 |
| BackupManagementServerUniqueId | Text          | Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile |
| BackupManagementType           | Text          | Tipo di provider per il processo di backup del server, ad esempio IaaSVM, fileFolder |
| ProtectedContainerUniqueId     | Text          | ID univoco per identificare il contenitore protetto in cui viene eseguito il processo |
| ProtectedInstanceCount         | Text          | Conteggio delle istanze protette per l'elemento di backup associato o il contenitore protetto in tale data e ora |
| SchemaVersion                  | Text          | Versione corrente dello schema, ad esempio **v2**            |
| Stato                          | Text          | Stato dell'oggetto elemento di backup, ad esempio Active, Deleted |
| VaultUniqueId                  | Text          | Identificatore univoco dell'insieme di credenziali protetto associato all'istanza protetta |
| SourceSystem                   | Text          | Sistema di origine dei dati correnti - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Questa tabella offre dettagli sui campi relativi al processo.

| **Campo**                      | **Tipo di dati** | **Descrizione**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| OperationName                  | Text          | Questo campo rappresenta il nome dell'operazione in corso - Processo    |
| Categoria                       | Text          | Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure-AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Text          | Campo per specificare se il processo è ad hoc o pianificato           |
| BackupItemUniqueId             | Text          | ID univoco usato per identificare l'elemento di backup correlato all'entità di archiviazione |
| BackupManagementServerUniqueId | Text          | ID univoco usato per identificare il server di gestione di backup correlato all'entità di archiviazione |
| BackupManagementType           | Text          | Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM, fileFolder a cui appartiene questo avviso |
| DataTransferredInMB            | NUMBER        | Dati trasferiti in MB per il processo                          |
| JobDurationInSecs              | NUMBER        | Durata totale del processo in secondi                                |
| JobFailureCode                 | Text          | Stringa di codice contenente un errore e a causa della quale si è verificato un errore del processo    |
| JobOperation                   | Text          | Operazione per cui viene eseguito il processo, ad esempio backup, Restore, Configure backup |
| JobOperationSubType            | Text          | Sottotipo dell'operazione di processo. Ad esempio, "log", nel caso di un processo di backup del log |
| JobStartDateTime               | DateTime      | Data e ora di avvio dell'esecuzione del processo                       |
| Stato processo                      | Text          | Stato del processo completato, ad esempio Completed o Failed   |
| JobUniqueId                    | Text          | ID univoco per identificare il processo                                |
| ProtectedContainerUniqueId     | Text          | Identificatore univoco del server protetto associato all'avviso |
| RecoveryJobDestination         | Text          | Destinazione di un processo di ripristino in cui vengono recuperati i dati   |
| RecoveryJobRPDateTime          | DateTime      | Data, ora in cui è stato creato il punto di ripristino da ripristinare |
| RecoveryJobLocation            | Text          | Posizione in cui è stato archiviato il punto di ripristino da ripristinare |
| RecoveryLocationType           | Text          | Tipo di percorso di ripristino                                |
| SchemaVersion                  | Text          | Versione corrente dello schema, ad esempio **v2**            |
| Stato                          | Text          | Stato corrente dell'oggetto avviso, ad esempio Active, Deleted |
| VaultUniqueId                  | Text          | Identificatore univoco dell'insieme di credenziali protetto associato all'avviso |
| SourceSystem                   | Text          | Sistema di origine dei dati correnti - Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

Questa tabella offre dettagli sui campi relativi al criterio.

| **Campo**                       | **Tipo di dati**  | **Descrizione**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Text           | Identificatore univoco per la risorsa in cui vengono raccolti i dati. Ad esempio, un ID di risorsa dell'insieme di credenziali di servizi di ripristino |
| OperationName                   | Text           | Nome dell'operazione, ad esempio Policy o PolicyAssociation |
| Categoria                        | Text           | Categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure-AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Text           | Giorni della settimana per i quali sono stati pianificati backup            |
| BackupFrequency                 | Text           | Frequenza con cui vengono eseguiti i backup. Ad esempio, ogni giorno, ogni settimana |
| BackupManagementType            | Text           | Tipo di provider per il processo di backup del server. Ad esempio, IaaSVM, fileFolder |
| BackupManagementServerUniqueId  | Text           | Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile |
| BackupTimes                     | Text           | Data e ora in cui sono pianificati backup                     |
| DailyRetentionDuration          | Numero intero   | Durata totale di mantenimento dati in giorni per i backup configurati      |
| DailyRetentionTimes             | Text           | Data e ora di configurazione del mantenimento dati giornaliero            |
| DiffBackupDaysOfTheWeek         | Text           | Giorni della settimana per i backup differenziali per SQL nel backup di macchine virtuali di Azure |
| DiffBackupFormat                | Text           | Formato per i backup differenziali per SQL nel backup di macchine virtuali di Azure   |
| DiffBackupRetentionDuration     | Numero decimale | Durata di conservazione per i backup differenziali per SQL nel backup di macchine virtuali di Azure |
| DiffBackupTime                  | Time           | Tempo per i backup differenziali per SQL nel backup di macchine virtuali di Azure     |
| LogBackupFrequency              | Numero decimale | Frequenza dei backup del log per SQL                            |
| LogBackupRetentionDuration      | Numero decimale | Durata del periodo di memorizzazione per i backup del log per SQL nel backup di macchine virtuali di Azure |
| MonthlyRetentionDaysOfTheMonth  | Text           | Settimane del mese in cui è configurata la conservazione mensile.  Ad esempio, First, Last e così via. |
| MonthlyRetentionDaysOfTheWeek   | Text           | Giorni della settimana selezionati per il mantenimento dati mensile              |
| MonthlyRetentionDuration        | Text           | Durata totale di mantenimento dati in mesi per i backup configurati    |
| MonthlyRetentionFormat          | Text           | Tipo di configurazione per la conservazione mensile. Ad esempio, giornaliero per il giorno basato su, settimanalmente per la settimana |
| MonthlyRetentionTimes           | Text           | Data e ora di configurazione del mantenimento dati mensile           |
| MonthlyRetentionWeeksOfTheMonth | Text           | Settimane del mese in cui è configurata la conservazione mensile.   Ad esempio, First, Last e così via. |
| PolicyName                      | Text           | Nome dei criteri definiti                                   |
| PolicyUniqueId                  | Text           | ID univoco per identificare il criterio                             |
| PolicyTimeZone                  | Text           | Fuso orario in cui vengono specificati i campi temporali dei criteri nei log |
| RetentionDuration               | Text           | Durata di mantenimento dei backup configurati                    |
| RetentionType                   | Text           | Tipo di conservazione                                            |
| SchemaVersion                   | Text           | Questo campo indica la versione corrente dello schema, ovvero **v2** |
| Stato                           | Text           | Stato corrente dell'oggetto criterio. Ad esempio, Active, Deleted |
| SynchronisationFrequencyPerDay  | Numero intero   | Numero di volte in un giorno in cui è stato sincronizzato un backup di file per SC DPM e MAB |
| VaultUniqueId                   | Text           | ID univoco dell'insieme di credenziali a cui appartiene questo criterio          |
| WeeklyRetentionDaysOfTheWeek    | Text           | Giorni della settimana selezionati per il mantenimento dati settimanale               |
| WeeklyRetentionDuration         | Numero decimale | Durata totale di mantenimento settimanale in settimane per i backup configurati |
| WeeklyRetentionTimes            | Text           | Data e ora di configurazione del mantenimento dati settimanale            |
| YearlyRetentionDaysOfTheMonth   | Text           | Date del mese selezionate per il mantenimento dati annuale             |
| YearlyRetentionDaysOfTheWeek    | Text           | Giorni della settimana selezionati per il mantenimento dati annuale               |
| YearlyRetentionDuration         | Numero decimale | Durata totale di mantenimento dati in anni per i backup configurati     |
| YearlyRetentionFormat           | Text           | Tipo di configurazione per la conservazione annuale, ad esempio, giornaliera per il giorno in base, settimanale per la settimana |
| YearlyRetentionMonthsOfTheYear  | Text           | Mesi dell'anno selezionati per il mantenimento dati annuale             |
| YearlyRetentionTimes            | Text           | Data e ora di configurazione del mantenimento dati annuale            |
| YearlyRetentionWeeksOfTheMonth  | Text           | Settimane del mese selezionate per la conservazione annuale             |
| SourceSystem                    | Text           | Sistema di origine dei dati correnti - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

Questa tabella offre dettagli sui campi relativi all'archiviazione.

| **Campo**                      | **Tipo di dati** | **Descrizione**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| OperationName                  | Text          | Questo campo rappresenta il nome dell'operazione corrente, ovvero storage o StorageAssociation |
| Categoria                       | Text          | Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure-AddonAzureBackupStorage |
| BackupItemUniqueId             | Text          | ID univoco usato per identificare l'elemento di backup per le macchine virtuali di cui è stato eseguito il backup con DPM, MAB |
| BackupManagementServerUniqueId | Text          | Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile |
| BackupManagementType           | Text          | Tipo di provider per il processo di backup del server. Ad esempio, IaaSVM, fileFolder |
| PreferredWorkloadOnVolume      | Text          | Carico di lavoro per il quale il volume è la risorsa di archiviazione preferita      |
| ProtectedContainerUniqueId     | Text          | Identificatore univoco del server protetto associato all'avviso |
| SchemaVersion                  | Text          | Versione dello schema. Ad esempio, **v2**                   |
| Stato                          | Text          | Stato dell'oggetto elemento di backup. Ad esempio, Active, Deleted |
| StorageAllocatedInMBs          | NUMBER        | Dimensioni dello spazio di archiviazione allocato dall'elemento di backup corrispondente nello spazio di archiviazione corrispondente di tipo Disk |
| StorageConsumedInMBs           | NUMBER        | Dimensioni dello spazio di archiviazione utilizzato dall'elemento di backup corrispondente nell'archivio corrispondente |
| StorageName                    | Text          | Nome dell'entità di archiviazione. Ad esempio, E:\                      |
| StorageTotalSizeInGBs          | Text          | Dimensioni totali dello spazio di archiviazione, in GB, utilizzate dall'entità di archiviazione     |
| StorageType                    | Text          | Tipo di archiviazione, ad esempio cloud, volume, disco             |
| StorageUniqueId                | Text          | ID univoco usato per identificare l'entità di archiviazione                |
| VaultUniqueId                  | Text          | ID univoco usato per identificare l'insieme di credenziali correlato all'entità di archiviazione |
| VolumeFriendlyName             | Text          | Nome descrittivo del volume di archiviazione                          |
| SourceSystem                   | Text          | Sistema di origine dei dati correnti - Azure                    |

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come inviare i dati di diagnostica a Log Analytics](https://aka.ms/AA6il6r)