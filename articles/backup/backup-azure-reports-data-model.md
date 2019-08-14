---
title: Modello di dati per Backup di Microsoft Azure
description: Questo articolo illustra i dettagli del modello di dati di Power BI per i report di Backup di Azure.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: dacurwin
ms.openlocfilehash: 96adca2da28517c28ba3583f5d15f07311d2792a
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954500"
---
# <a name="data-model-for-azure-backup-reports"></a>Modello di dati per i report di Backup di Azure
Questo articolo descrive il modello di dati di Power BI usato per creare report di Backup di Azure. Con questo modello di dati è possibile filtrare report esistenti in base ai campi di interesse e, soprattutto, creare report personalizzati usando le tabelle e i campi del modello. 

## <a name="creating-new-reports-in-power-bi"></a>Creazione di nuovi report in Power BI
Power BI offre funzionalità di personalizzazione che consentono di [creare report tramite il modello di dati](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Uso del modello di dati di Backup di Azure
Per creare nuovi report e personalizzare report esistenti, è possibile usare i campi seguenti, disponibili all'interno del modello di dati.

### <a name="alert"></a>Avviso
Questa tabella fornisce le aggregazioni e i campi di base per diversi campi relativi agli avvisi.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Numero intero |Numero di avvisi creati nel periodo di tempo selezionato |
| %ActiveAlertsCreatedInPeriod |Percentuale |Percentuale di avvisi attivi nel periodo di tempo selezionato |
| %CriticalAlertsCreatedInPeriod |Percentuale |Percentuale di avvisi critici nel periodo di tempo selezionato |
| AlertOccurrenceDate |Date |Data di creazione dell'avviso |
| AlertSeverity |Text |Gravità dell'avviso Ad esempio, Critical |
| AlertStatus |Text |Stato dell'avviso. Ad esempio, Active |
| AlertType |Text |Tipo dell'avviso generato. Ad esempio, backup |
| AlertUniqueId |Text |ID univoco dell'avviso generato |
| AsOnDateTime |Data/ora |Data e ora dell'aggiornamento più recente della riga selezionata |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Numero decimale |Tempo medio (in minuti) di risoluzione dell'avviso per il periodo di tempo selezionato |
| EntityState |Text |Stato corrente dell'oggetto avviso. Ad esempio, Active, Deleted |

### <a name="backup-item"></a>Elementi di backup
Questa tabella presenta i campi e le aggregazioni di base per diversi campi correlati agli elementi di backup.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| #BackupItems |Numero intero |Numero di elementi di backup |
| #UnprotectedBackupItems |Numero intero |Numero di elementi di backup arrestati per protezione o configurati per il backup, ma con backup non avviato|
| AsOnDateTime |Data/ora |Data e ora dell'aggiornamento più recente della riga selezionata |
| BackupItemFriendlyName |Text |Nome descrittivo dell'elemento di backup |
| BackupItemId |Text |ID dell'elemento di backup |
| BackupItemName |Text |Nome dell'elemento di backup |
| BackupItemType |Text |Tipo di elemento di backup. Ad esempio, VM, fileFolder |
| EntityState |Text |Stato corrente dell'oggetto elemento di backup. Ad esempio, Active, Deleted |
| LastBackupDateTime |Data/ora |Data e ora del backup più recente dell'elemento di backup selezionato |
| LastBackupState |Text |Stato dell'ultimo backup per l'elemento di backup selezionato. Ad esempio, operazione riuscita, non riuscita |
| LastSuccessfulBackupDateTime |Data/ora |Data e ora del backup più recente con esito positivo dell'elemento di backup selezionato |
| ProtectionState |Text |Stato di protezione corrente dell'elemento di backup. Ad esempio, protected, protected o protectionstopped |

### <a name="calendar"></a>Calendar
Questa tabella offre dettagli sui campi relativi al calendario.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| Date |Date |Data selezionata per il filtro dei dati |
| DateKey |Text |Chiave univoca per ogni elemento data |
| DayDiff |Numero decimale |Differenza nel giorno per il filtro dei dati. Ad esempio, 0 indica i dati del giorno corrente,-1 indica i dati del giorno precedente, 0 e-1 indicano i dati del giorno corrente e quello precedente  |
| Mese |Text |Mese dell'anno selezionato per il filtro dei dati. Il mese inizia il primo giorno e finisce il 31° giorno |
| MonthDate | Date |Data di fine del mese, selezionata per il filtro dei dati |
| MonthDiff |Numero decimale |Differenza nel mese per il filtro dei dati. Ad esempio, 0 indica i dati del mese corrente,-1 indica i dati del mese precedente, 0 e-1 indicano i dati per il mese corrente e quello precedente |
| Settimana |Text |Settimana selezionata come filtro dei dati. La settimana inizia la domenica e finisce il sabato |
| WeekDate |Date |Data dell'ultima settimana del mese, selezionata come filtro dei dati |
| WeekDiff |Numero decimale |Differenza nella settimana per il filtro dei dati. Ad esempio, 0 indica i dati della settimana corrente,-1 indica i dati della settimana precedente, 0 e-1 indicano i dati per la settimana corrente e quella precedente |
| Year |Text |Anno di calendario selezionato per il filtro dei dati |
| YearDate |Date |Data della fine dell'anno, selezionata come filtro dei dati |

### <a name="job"></a>Processo
Questa tabella presenta i campi e le aggregazioni di base per diversi campi correlati ai processi.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| #JobsCreatedInPeriod |Numero intero |Numero di processi creati nel periodo di tempo selezionato |
| %FailuresForJobsCreatedInPeriod |Percentuale |Percentuale complessiva degli errori dei processi nel periodo di tempo selezionato |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Numero decimale |Valore dell'80° percentile in MB dei dati trasferiti per processi di **backup** creati nel periodo di tempo selezionato |
| AsOnDateTime |Data/ora |Data e ora dell'aggiornamento più recente della riga selezionata |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Numero decimale |Durata media espressa in minuti per i processi di **backup completati** creati nel periodo di tempo selezionato |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Numero decimale |Durata media espressa in minuti per i processi di **ripristino completati** creati nel periodo di tempo selezionato |
| BackupStorageDestination |Text |Destinazione dell'archiviazione di backup. Ad esempio, cloud, disco  |
| EntityState |Text |Stato corrente dell'oggetto processo. Ad esempio, Active, Deleted |
| JobFailureCode |Text |Stringa di codice contenente un errore e a causa della quale si è verificato un errore del processo |
| JobOperation |Text |Operazione per cui viene eseguito il processo. Ad esempio, backup, Restore, Configure backup |
| JobStartDate |Date |Data di avvio dell'esecuzione del processo |
| JobStartTime |Time |Ora di avvio dell'esecuzione del processo |
| Stato processo |Text |Stato del processo completato. Ad esempio, Completed, failed |
| JobUniqueId |Text |ID univoco per l'identificazione del processo |

### <a name="policy"></a>Criteri
Questa tabella presenta i campi e le aggregazioni di base per diversi campi correlati ai criteri.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| #Policies |Numero intero |Numero di criteri di backup presenti nel sistema |
| #PoliciesInUse |Numero intero |Numero di criteri attualmente in uso per la configurazione di backup |
| AsOnDateTime |Data/ora |Data e ora dell'aggiornamento più recente della riga selezionata |
| BackupDaysOfTheWeek |Text |Giorni della settimana per i quali sono stati pianificati backup |
| BackupFrequency |Text |Frequenza con cui vengono eseguiti i backup. Ad esempio, ogni giorno, ogni settimana |
| BackupTimes |Text |Data e ora in cui sono pianificati backup |
| DailyRetentionDuration |Numero intero |Durata totale di mantenimento dati in giorni per i backup configurati |
| DailyRetentionTimes |Text |Data e ora di configurazione del mantenimento dati giornaliero |
| EntityState |Text |Stato corrente dell'oggetto criterio. Ad esempio, Active, Deleted |
| MonthlyRetentionDaysOfTheMonth |Text |Date del mese selezionate per il mantenimento dati mensile |
| MonthlyRetentionDaysOfTheWeek |Text |Giorni della settimana selezionati per il mantenimento dati mensile |
| MonthlyRetentionDuration |Numero decimale |Durata totale di mantenimento dati in mesi per i backup configurati |
| MonthlyRetentionFormat |Text |Tipo di configurazione per la conservazione mensile. Ad esempio, giornaliero per il giorno basato su, settimanalmente per la settimana |
| MonthlyRetentionTimes |Text |Data e ora di configurazione del mantenimento dati mensile |
| MonthlyRetentionWeeksOfTheMonth |Text |Settimane del mese in cui è configurata la conservazione mensile. Ad esempio, First, Last e così via. |
| PolicyName |Text |Nome dei criteri definiti |
| PolicyUniqueId |Text |ID univoco per l'identificazione dei criteri |
| RetentionType |Text |Tipo di criteri di conservazione. Ad esempio, giornaliera, settimanale, mensile, annuale |
| WeeklyRetentionDaysOfTheWeek |Text |Giorni della settimana selezionati per il mantenimento dati settimanale |
| WeeklyRetentionDuration |Numero decimale |Durata totale del mantenimento dati settimanale in settimane per i backup configurati |
| WeeklyRetentionTimes |Text |Data e ora di configurazione del mantenimento dati settimanale |
| YearlyRetentionDaysOfTheMonth |Text |Date del mese selezionate per il mantenimento dati annuale |
| YearlyRetentionDaysOfTheWeek |Text |Giorni della settimana selezionati per il mantenimento dati annuale |
| YearlyRetentionDuration |Numero decimale |Durata totale di mantenimento dati in anni per i backup configurati |
| YearlyRetentionFormat |Text |Tipo di configurazione per la conservazione annuale. Ad esempio, giornaliero per il giorno basato su, settimanalmente per la settimana |
| YearlyRetentionMonthsOfTheYear |Text |Mesi dell'anno selezionati per il mantenimento dati annuale |
| YearlyRetentionTimes |Text |Data e ora di configurazione del mantenimento dati annuale |
| YearlyRetentionWeeksOfTheMonth |Text |Settimane del mese in cui è configurata la conservazione annuale. Ad esempio, First, Last e così via. |

### <a name="protected-server"></a>Server protetti
Questa tabella presenta i campi e le aggregazioni di base per diversi campi correlati ai server protetti.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| #ProtectedServers |Numero intero |Numero di server protetti |
| AsOnDateTime |Data/ora |Data e ora dell'aggiornamento più recente della riga selezionata |
| AzureBackupAgentOSType |Text |Tipo del sistema operativo dell'agente di Backup di Azure |
| AzureBackupAgentOSVersion |Text |Versione del sistema operativo dell'agente di Backup di Azure |
| AzureBackupAgentUpdateDate |Text |Data di aggiornamento dell'agente di Backup di Azure |
| AzureBackupAgentVersion |Text |Numero di versione dell'agente di Backup di Azure |
| BackupManagementType |Text |Tipo di provider per l'esecuzione del backup. Ad esempio, IaaSVM, fileFolder |
| EntityState |Text |Stato corrente dell'oggetto server protetto. Ad esempio, Active, Deleted |
| ProtectedServerFriendlyName |Text |Nome descrittivo del server protetto |
| ProtectedServerName |Text |Nome del server protetto |
| ProtectedServerType |Text |Tipo di server protetto di cui è stato eseguito il backup. Ad esempio, IaaSVMContainer |
| ProtectedServerName |Text |Nome del server protetto a cui appartiene l'elemento di backup |
| RegisteredContainerId |Text |ID del contenitore registrato per il backup |

### <a name="storage"></a>Archiviazione
Questa tabella presenta i campi e le aggregazioni di base per diversi campi correlati all'archiviazione.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| #ProtectedInstances |Numero decimale |Numero di istanze protette usato per il calcolo dell'archiviazione front-end a scopo di fatturazione, calcolato in base al valore più recente nel periodo selezionato |
| AsOnDateTime |Data/ora |Data e ora dell'aggiornamento più recente della riga selezionata |
| CloudStorageInMB |Numero decimale |Spazio di archiviazione nel cloud usato dal backup, calcolato in base al valore più recente nel periodo selezionato |
| EntityState |Text |Stato corrente dell'oggetto. Ad esempio, Active, Deleted |
| LastUpdatedDate |Date |Data dell'aggiornamento più recente della riga selezionata |

### <a name="time"></a>Time
Questa tabella offre dettagli sui campi relativi al tempo.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| Hour |Time |Ora del giorno. Ad esempio, 1:00:00 PM |
| HourNumber |Numero decimale |Numero ora del giorno. Ad esempio, 13,00 |
| Minuto |Numero decimale |Minuto dell'ora |
| PeriodOfTheDay |Text |Slot del periodo di tempo nel giorno. Ad esempio, 12-3 AM |
| Time |Time |Ora del giorno. Ad esempio, 12:00:01 AM |
| TimeKey |Text |Valore chiave per rappresentare l'ora |

### <a name="vault"></a>Insiemi di credenziali
Questa tabella presenta i campi e le aggregazioni di base per diversi campi correlati agli insiemi di credenziali.

| Campo | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| #Vaults |Numero intero |Numero di insiemi di credenziali |
| AsOnDateTime |Data/ora |Data e ora dell'aggiornamento più recente della riga selezionata |
| AzureDataCenter |Text |Data center in cui si trova l'insieme di credenziali |
| EntityState |Text |Stato corrente dell'oggetto dell'insieme di credenziali. Ad esempio, Active, Deleted |
| StorageReplicationType |Text |Tipo di replica di archiviazione per l'insieme di credenziali. Ad esempio, georidondante |
| SubscriptionId |Text |ID sottoscrizione del cliente selezionato per la generazione di report |
| VaultName |Text |Nome dell'insieme di credenziali |
| VaultTags |Text |Tag associati all'insieme di credenziali |

## <a name="next-steps"></a>Passaggi successivi
Dopo aver rivisto il modello di dati per la creazione di report di Backup di Azure, fare riferimento agli articoli seguenti per altri dettagli sulla creazione e sulla visualizzazione di report in Power BI.

* [Creazione di report in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Applicazione di filtri ai report in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
