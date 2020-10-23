---
title: Modello di dati per gli eventi di diagnostica di backup di Azure
description: Questo modello di dati è in riferimento alla modalità specifica della risorsa di invio di eventi di diagnostica a Log Analytics (LA).
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 52c5c0694ed59aea20453ae7a2bd3209d76df433
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173976"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Modello di dati per gli eventi di diagnostica di backup di Azure

## <a name="coreazurebackup"></a>CoreAzureBackup

Questa tabella fornisce informazioni sulle entità di backup di base, ad esempio insiemi di credenziali ed elementi di backup.

| **Campo**                         | **Tipo di dati** | **Descrizione**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Text          | Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino. |
| OperationName                     | Text          | Questo campo rappresenta il nome dell'operazione corrente-BackupItem, BackupItemAssociation o ProtectedContainer. |
| Category                          | Text          | Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure. Ad esempio, CoreAzureBackup. |
| AgentVersion                      | Testo          | Numero di versione del backup dell'agente o dell'agente protezione (nel caso di SC DPM e MAB) |
| AzureBackupAgentVersion           | Text          | Versione dell'agente di Backup di Azure nel server di gestione di backup |
| AzureDataCenter                   | Testo          | Data Center in cui si trova l'insieme di credenziali                       |
| BackupItemAppVersion              | Text          | Versione dell'applicazione dell'elemento di backup                       |
| BackupItemFriendlyName            | Testo          | Nome descrittivo dell'elemento di backup                             |
| BackupItemName                    | Testo          | Nome dell'elemento di backup                                      |
| BackupItemProtectionState         | Text          | Stato di protezione dell'elemento di backup                          |
| BackupItemFrontEndSize            | Text          | Dimensioni front-end dell'elemento di backup                            |
| BackupItemType                    | Testo          | Tipo di elemento di backup. Ad esempio: VM, fileFolder             |
| BackupItemUniqueId                | Text          | Identificatore univoco dell'elemento di backup                         |
| BackupManagementServerType        | Testo          | Tipo di server di gestione di backup, come in MAB, SC DPM     |
| BackupManagementServerUniqueId    | Text          | Campo che identifica in modo univoco il server di gestione di backup      |
| BackupManagementType              | Testo          | Tipo di provider per il processo di backup del server. Ad esempio, IaaSVM, FileFolder |
| BackupManagementServerName        | Text          | Nome del server di gestione di backup                         |
| BackupManagementServerOSVersion   | Text          | Versione del sistema operativo del server di gestione di backup                   |
| BackupManagementServerVersion     | Text          | Versione del server di gestione di backup                      |
| LatestRecoveryPointLocation       | Text          | Posizione del punto di ripristino più recente per l'elemento di backup    |
| LatestRecoveryPointTime           | Datetime      | Data e ora del punto di ripristino più recente per l'elemento di backup   |
| OldestRecoveryPointLocation       | Text          | Posizione del punto di ripristino meno recente per l'elemento di backup    |
| OldestRecoveryPointTime           | Datetime      | Data e ora del punto di ripristino più recente per l'elemento di backup   |
| PolicyUniqueId                    | Text          | ID univoco per l'identificazione dei criteri                             |
| ProtectedContainerFriendlyName    | Testo          | Nome descrittivo del server protetto                        |
| ProtectedContainerLocation        | Testo          | Indica se il contenitore protetto si trova in locale o in Azure |
| ProtectedContainerName            | Testo          | Nome del contenitore protetto                            |
| ProtectedContainerOSType          | Testo          | Tipo di sistema operativo del contenitore protetto                          |
| ProtectedContainerOSVersion       | Text          | Versione del sistema operativo del contenitore protetto                        |
| ProtectedContainerProtectionState | Text          | Stato della protezione del contenitore protetto                  |
| ProtectedContainerType            | Text          | Indica se il contenitore protetto è un server o un contenitore  |
| ProtectedContainerUniqueId        | Testo          | ID univoco usato per identificare il contenitore protetto per tutti gli elementi eccetto le macchine virtuali di cui viene eseguito il backup con DPM, MABS |
| ProtectedContainerWorkloadType    | Testo          | Tipo del contenitore protetto di cui è stato eseguito il backup. Ad esempio, IaaSVMContainer |
| ProtectionGroupName               | Testo          | Nome del gruppo protezione dati in cui è protetto l'elemento di backup, per SC DPM e MAB, se applicabile |
| ResourceGroupName                 | Testo          | Gruppo di risorse della risorsa (ad esempio, l'insieme di credenziali di servizi di ripristino) per i dati raccolti |
| schemaVersion                     | Testo          | Questo campo indica la versione corrente dello schema. È **v2** |
| SecondaryBackupProtectionState    | Text          | Indica se la protezione secondaria è abilitata per l'elemento di backup  |
| Stato                             | Testo          | Stato dell'oggetto elemento di backup. Ad esempio Active o Deleted |
| StorageReplicationType            | Testo          | Tipo di replica di archiviazione per l'insieme di credenziali. Ad esempio, georidondante |
| SubscriptionId                    | Text          | Identificatore della sottoscrizione della risorsa, ad esempio l'insieme di credenziali di servizi di ripristino, per cui vengono raccolti i dati |
| VaultName                         | Text          | Nome dell'insieme di credenziali                                            |
| VaultTags                         | Testo          | Tag associati alla risorsa dell'insieme di credenziali                    |
| VaultUniqueId                     | Testo          | Identificatore univoco dell'insieme di credenziali                             |
| SourceSystem                      | Text          | Sistema di origine dei dati correnti-Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Questa tabella offre dettagli sui campi relativi agli avvisi.

| **Campo**                      | **Tipo di dati** | **Descrizione**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Identificatore univoco per la risorsa per ia quale vengono raccolti i dati. Ad esempio, un ID di risorsa dell'insieme di credenziali di servizi di ripristino |
| OperationName                  | Text          | Nome dell'operazione corrente. Ad esempio, avviso            |
| Category                       | Text          | Categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure-AddonAzureBackupAlerts |
| AlertCode                      | Testo          | Codice per identificare in modo univoco un tipo di avviso                     |
| AlertConsolidationStatus       | Text          | Identifica se l'avviso è un avviso consolidato o meno         |
| AlertOccurrenceDateTime        | Datetime      | Data e ora di creazione dell'avviso                     |
| AlertRaisedOn                  | Text          | Tipo di entità per cui viene generato l'avviso                        |
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
| RecommendedAction              | Text          | Azione consigliata per risolvere l'avviso                      |
| schemaVersion                  | Text          | Versione corrente dello schema, ad esempio **V2**            |
| Stato                          | Text          | Stato corrente dell'oggetto avviso, ad esempio Active o Deleted |
| StorageUniqueId                | Text          | ID univoco usato per identificare l'entità di archiviazione                |
| VaultUniqueId                  | Testo          | ID univoco usato per identificare l'insieme di credenziali correlato all'avviso    |
| SourceSystem                   | Text          | Sistema di origine dei dati correnti - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Questa tabella illustra i campi di base correlati alle istanze protette.

| **Campo**                      | **Tipo di dati** | **Descrizione**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Identificatore univoco per la risorsa in cui vengono raccolti i dati. Ad esempio, un ID risorsa dell'insieme di credenziali di Servizi di ripristino |
| OperationName                  | Text          | Nome dell'operazione, ad esempio ProtectedInstance         |
| Category                       | Text          | Categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure-AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Text          | ID univoco dell'elemento di backup                                 |
| BackupManagementServerUniqueId | Testo          | Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile |
| BackupManagementType           | Testo          | Tipo di provider per il processo di backup del server, ad esempio IaaSVM, fileFolder |
| ProtectedContainerUniqueId     | Testo          | ID univoco per identificare il contenitore protetto in cui viene eseguito il processo |
| ProtectedInstanceCount         | Testo          | Conteggio delle istanze protette per l'elemento di backup associato o il contenitore protetto in tale data e ora |
| schemaVersion                  | Text          | Versione corrente dello schema, ad esempio **V2**            |
| Stato                          | Testo          | Stato dell'oggetto elemento di backup, ad esempio Active, Deleted |
| VaultUniqueId                  | Testo          | Identificatore univoco dell'insieme di credenziali protetto associato all'istanza protetta |
| SourceSystem                   | Text          | Sistema di origine dei dati correnti - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Questa tabella offre dettagli sui campi relativi al processo.

| **Campo**                      | **Tipo di dati** | **Descrizione**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Identificatore della risorsa per i dati raccolti. Ad esempio, ID risorsa dell'insieme di credenziali di Servizi di ripristino |
| OperationName                  | Text          | Questo campo rappresenta il nome dell'operazione in corso - Processo    |
| Category                       | Text          | Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure-AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Text          | Campo che specifica se il processo è ad hoc o pianificato           |
| BackupItemUniqueId             | Text          | ID univoco usato per identificare l'elemento di backup correlato all'entità di archiviazione |
| BackupManagementServerUniqueId | Text          | ID univoco usato per identificare il server di gestione di backup correlato all'entità di archiviazione |
| BackupManagementType           | Testo          | Tipo di provider per l'esecuzione del backup, ad esempio IaaSVM o FileFolder a cui appartiene questo processo |
| DataTransferredInMB            | Number        | Dati trasferiti in MB per il processo                          |
| JobDurationInSecs              | Number        | Durata totale del processo in secondi                                |
| JobFailureCode                 | Text          | Stringa di codice contenente un errore e a causa della quale si è verificato un errore del processo    |
| JobOperation                   | Testo          | Operazione per cui viene eseguito il processo, ad esempio backup, Restore, Configure backup |
| JobOperationSubType            | Testo          | Sottotipo dell'operazione di processo. Ad esempio, "log", nel caso di un processo di backup del log |
| JobStartDateTime               | Datetime      | Data e ora di avvio dell'esecuzione del processo                       |
| Stato processo                      | Text          | Stato del processo completato, ad esempio Completed o Failed   |
| JobUniqueId                    | Text          | ID univoco per l'identificazione del processo                                |
| ProtectedContainerUniqueId     | Testo          | Identificatore univoco del server protetto associato al processo |
| RecoveryJobDestination         | Text          | Destinazione di un processo di ripristino in cui vengono ripristinati i dati   |
| RecoveryJobRPDateTime          | Datetime      | Data, ora in cui è stato creato il punto di ripristino da ripristinare |
| RecoveryJobLocation            | Testo          | Il percorso in cui è stato archiviato il punto di ripristino da ripristinare |
| RecoveryLocationType           | Testo          | Tipo di percorso di ripristino                                |
| schemaVersion                  | Text          | Versione corrente dello schema, ad esempio **V2**            |
| Stato                          | Testo          | Stato corrente dell'oggetto processo, ad esempio Active, Deleted |
| VaultUniqueId                  | Testo          | Identificatore univoco dell'insieme di credenziali protetto associato al processo |
| SourceSystem                   | Text          | Sistema di origine dei dati correnti - Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

Questa tabella offre dettagli sui campi relativi al criterio.

| **Campo**                       | **Tipo di dati**  | **Descrizione**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Text           | Identificatore univoco per la risorsa in cui vengono raccolti i dati. Ad esempio, un ID risorsa dell'insieme di credenziali di Servizi di ripristino |
| OperationName                   | Text           | Nome dell'operazione, ad esempio Policy o PolicyAssociation |
| Category                        | Text           | Categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure-AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Text           | Giorni della settimana per i quali sono stati pianificati backup            |
| BackupFrequency                 | Testo           | Frequenza con cui vengono eseguiti i backup. Ad esempio, ogni giorno, ogni settimana |
| BackupManagementType            | Testo           | Tipo di provider per il processo di backup del server. Ad esempio, IaaSVM, fileFolder |
| BackupManagementServerUniqueId  | Testo           | Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile |
| BackupTimes                     | Text           | Data e ora in cui sono pianificati backup                     |
| DailyRetentionDuration          | Numero intero   | Durata totale di mantenimento dati in giorni per i backup configurati      |
| DailyRetentionTimes             | Text           | Data e ora di configurazione del mantenimento dati giornaliero            |
| DiffBackupDaysOfTheWeek         | Testo           | Giorni della settimana per i backup differenziali per SQL nel backup di macchine virtuali di Azure |
| DiffBackupFormat                | Testo           | Formato per i backup differenziali per SQL nel backup di macchine virtuali di Azure   |
| DiffBackupRetentionDuration     | Numero decimale | Durata di conservazione per i backup differenziali per SQL nel backup di macchine virtuali di Azure |
| DiffBackupTime                  | Ora           | Tempo per i backup differenziali per SQL nel backup di macchine virtuali di Azure     |
| LogBackupFrequency              | Numero decimale | Frequenza per i backup dei log per SQL                            |
| LogBackupRetentionDuration      | Numero decimale | Durata del periodo di memorizzazione per i backup del log per SQL nel backup di macchine virtuali di Azure |
| MonthlyRetentionDaysOfTheMonth  | Testo           | Settimane del mese in cui è configurata la conservazione mensile.  Ad esempio, First, Last |
| MonthlyRetentionDaysOfTheWeek   | Text           | Giorni della settimana selezionati per il mantenimento dati mensile              |
| MonthlyRetentionDuration        | Testo           | Durata totale di mantenimento dati in mesi per i backup configurati    |
| MonthlyRetentionFormat          | Testo           | Tipo di configurazione per la conservazione mensile. Ad esempio, giornaliero per il giorno basato su, settimanalmente per la settimana |
| MonthlyRetentionTimes           | Text           | Data e ora di configurazione del mantenimento dati mensile           |
| MonthlyRetentionWeeksOfTheMonth | Testo           | Settimane del mese in cui è configurata la conservazione mensile.   Ad esempio, First, Last |
| PolicyName                      | Text           | Nome dei criteri definiti                                   |
| PolicyUniqueId                  | Text           | ID univoco per l'identificazione dei criteri                             |
| PolicyTimeZone                  | Testo           | Fuso orario in cui vengono specificati i campi temporali dei criteri nei log |
| RetentionDuration               | Testo           | Durata di mantenimento dei backup configurati                    |
| RetentionType                   | Testo           | Tipo di conservazione                                            |
| schemaVersion                   | Testo           | Questo campo indica la versione corrente dello schema, ovvero **v2** |
| Stato                           | Testo           | Stato corrente dell'oggetto criterio. Ad esempio, Active, Deleted |
| SynchronisationFrequencyPerDay  | Numero intero   | Numero di volte in un giorno in cui è stato sincronizzato un backup di file per SC DPM e MAB |
| VaultUniqueId                   | Testo           | ID univoco dell'insieme di credenziali a cui appartiene questo criterio          |
| WeeklyRetentionDaysOfTheWeek    | Text           | Giorni della settimana selezionati per il mantenimento dati settimanale               |
| WeeklyRetentionDuration         | Numero decimale | Durata totale di mantenimento settimanale in settimane per i backup configurati |
| WeeklyRetentionTimes            | Text           | Data e ora di configurazione del mantenimento dati settimanale            |
| YearlyRetentionDaysOfTheMonth   | Text           | Date del mese selezionate per il mantenimento dati annuale             |
| YearlyRetentionDaysOfTheWeek    | Testo           | Giorni della settimana selezionati per il mantenimento dati annuale               |
| YearlyRetentionDuration         | Numero decimale | Durata totale di mantenimento dati in anni per i backup configurati     |
| YearlyRetentionFormat           | Testo           | Tipo di configurazione per la conservazione annuale, ad esempio, giornaliera per il giorno in base, settimanale per la settimana |
| YearlyRetentionMonthsOfTheYear  | Text           | Mesi dell'anno selezionati per il mantenimento dati annuale             |
| YearlyRetentionTimes            | Text           | Data e ora di configurazione del mantenimento dati annuale            |
| YearlyRetentionWeeksOfTheMonth  | Testo           | Settimane del mese selezionate per la conservazione annuale             |
| SourceSystem                    | Text           | Sistema di origine dei dati correnti - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

Questa tabella offre dettagli sui campi relativi all'archiviazione.

| **Campo**                      | **Tipo di dati** | **Descrizione**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Identificatore della risorsa per i dati raccolti. Ad esempio, l'ID risorsa dell'insieme di credenziali di servizi di ripristino |
| OperationName                  | Text          | Questo campo rappresenta il nome dell'operazione corrente, ovvero storage o StorageAssociation |
| Category                       | Text          | Questo campo rappresenta la categoria di dati di diagnostica inseriti nei log di monitoraggio di Azure-AddonAzureBackupStorage |
| BackupItemUniqueId             | Testo          | ID univoco usato per identificare l'elemento di backup per le macchine virtuali di cui è stato eseguito il backup con DPM, MAB |
| BackupManagementServerUniqueId | Testo          | Campo per identificare in modo univoco il server di gestione di backup in cui è protetto l'elemento di backup, se applicabile |
| BackupManagementType           | Testo          | Tipo di provider per il processo di backup del server. Ad esempio, IaaSVM, fileFolder |
| PreferredWorkloadOnVolume      | Testo          | Carico di lavoro per il quale il volume è la risorsa di archiviazione preferita      |
| ProtectedContainerUniqueId     | Testo          | Identificatore univoco del contenitore protetto associato all'elemento di backup |
| schemaVersion                  | Testo          | Versione dello schema. Ad esempio, **v2**                   |
| Stato                          | Testo          | Stato dell'oggetto elemento di backup. Ad esempio, Active, Deleted |
| StorageAllocatedInMBs          | Number        | Dimensioni dello spazio di archiviazione allocato dall'elemento di backup corrispondente nello spazio di archiviazione corrispondente di tipo Disk |
| StorageConsumedInMBs           | Number        | Dimensioni dello spazio di archiviazione utilizzato dall'elemento di backup corrispondente nell'archivio corrispondente |
| StorageName                    | Testo          | Nome dell'entità di archiviazione. Ad esempio, E:\                      |
| StorageTotalSizeInGBs          | Text          | Dimensioni totali dello spazio di archiviazione, in GB, utilizzato dall'entità di archiviazione     |
| StorageType                    | Text          | Tipo di archiviazione, ad esempio Cloud, Volume, Disk             |
| StorageUniqueId                | Text          | ID univoco usato per identificare l'entità di archiviazione                |
| VaultUniqueId                  | Testo          | ID univoco usato per identificare l'insieme di credenziali correlato all'entità di archiviazione |
| VolumeFriendlyName             | Testo          | Nome descrittivo del volume di archiviazione                          |
| SourceSystem                   | Text          | Sistema di origine dei dati correnti - Azure                    |

## <a name="valid-operation-names-for-each-table"></a>Nomi di operazione validi per ogni tabella

A ogni record delle tabelle precedenti è associato un **nome dell'operazione**. Un nome di operazione descrive il tipo di record (e indica anche quali campi della tabella sono popolati per tale record). Ogni tabella (categoria) supporta uno o più nomi di operazione distinti. Di seguito è riportato un riepilogo dei nomi di operazione supportati per ognuna delle tabelle precedenti.

| **Nome tabella/categoria**                   | **Nomi di operazione supportati** | **Descrizione**              |
| ------------------------------------------- | ------------------------------|----------------------------- |
| CoreAzureBackup | BackupItem | Rappresenta un record contenente tutti i dettagli di un elemento di backup specificato, ad esempio ID, nome, tipo e così via. |
| CoreAzureBackup | BackupItemAssociation | Rappresenta un mapping tra un elemento di backup e il contenitore protetto associato (se applicabile). |
| CoreAzureBackup | BackupItemFrontEndSizeConsumption | Rappresenta un mapping tra un elemento di backup e le relative dimensioni front-end. |
| CoreAzureBackup | ProtectedContainer | Rappresenta un record contenente tutti i dettagli di un determinato contenitore protetto, ad esempio ID, nome, tipo e così via. |
| CoreAzureBackup | ProtectedContainerAssociation | Rappresenta un mapping tra un contenitore protetto e l'insieme di credenziali utilizzato per il backup. |
| CoreAzureBackup | Insiemi di credenziali | Rappresenta un record contenente tutti i dettagli di un determinato insieme di credenziali, ad esempio. ID, nome, tag, posizione e così via. |
| CoreAzureBackup | RecoveryPoint | Rappresenta un record contenente il punto di ripristino più vecchio e più recente per un determinato elemento di backup. |
| AddonAzureBackupJobs | Processo |  Rappresenta un record contenente tutti i dettagli di un determinato processo. Ad esempio, operazione di processo, ora di inizio, stato e così via. |
| AddonAzureBackupAlerts | Avviso | Rappresenta un record contenente tutti i dettagli di un determinato avviso. Ad esempio, ora di creazione dell'avviso, gravità, stato e così via.  |
| AddonAzureBackupStorage | Archiviazione | Rappresenta un record contenente tutti i dettagli di un'entità di archiviazione specificata. Ad esempio, nome archiviazione, tipo e così via. |
| AddonAzureBackupStorage | StorageAssociation | Rappresenta un mapping tra un elemento di backup e l'archiviazione cloud totale utilizzata dall'elemento di backup. |
| AddonAzureBackupProtectedInstance | ProtectedInstance | Rappresenta un record contenente il numero di istanze protette per ogni contenitore o elemento di backup. Per il backup delle macchine virtuali di Azure, il numero di istanze protette è disponibile a livello di elemento di backup, per altri carichi di lavoro è disponibile a livello di contenitore protetto. |
| AddonAzureBackupPolicy | Policy |  Rappresenta un record contenente tutti i dettagli dei criteri di backup e conservazione. Ad esempio, ID, nome, impostazioni di conservazione e così via. |
| AddonAzureBackupPolicy | PolicyAssociation | Rappresenta un mapping tra un elemento di backup e i criteri di backup applicati. |   

Spesso è necessario eseguire join tra tabelle diverse, nonché set di record diversi che fanno parte della stessa tabella, differenziati in base al nome dell'operazione, per ottenere tutti i campi necessari per l'analisi. Vedere le [query di esempio](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries) per iniziare. 

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come inviare i dati di diagnostica a Log Analytics](./backup-azure-diagnostic-events.md)
- [Informazioni su come scrivere query in tabelle specifiche delle risorse](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries)