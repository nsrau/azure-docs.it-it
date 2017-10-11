---
title: Modello di dati per Backup di Microsoft Azure
description: Questo articolo illustra i dettagli del modello di dati di Power BI per i report di Backup di Azure.
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 0767c330-690d-474d-85a6-aa8ddc410bb2
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/26/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: efecbc9f1c410744f49795889c4ec3cc618f07e0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="data-model-for-azure-backup-reports"></a>Modello di dati per i report di Backup di Azure
Questo articolo descrive il modello di dati di Power BI usato per creare report di Backup di Azure. Con questo modello di dati è possibile filtrare report esistenti in base ai campi di interesse e, soprattutto, creare report personalizzati usando le tabelle e i campi del modello. 

## <a name="creating-new-reports-in-power-bi"></a>Creazione di nuovi report in Power BI
Power BI offre funzionalità di personalizzazione che consentono di [creare report tramite il modello di dati](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Uso del modello di dati di Backup di Azure
Per creare nuovi report e personalizzare report esistenti, è possibile usare i campi seguenti, disponibili all'interno del modello di dati.

### <a name="alert"></a>Avviso
Questa tabella presenta i campi e le aggregazioni di base per diversi campi correlati agli avvisi.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Numero intero |Numero di avvisi creati nel periodo di tempo selezionato |
| %ActiveAlertsCreatedInPeriod |Percentuale |Percentuale di avvisi attivi nel periodo di tempo selezionato |
| %CriticalAlertsCreatedInPeriod |Percentuale |Percentuale di avvisi critici nel periodo di tempo selezionato |
| AlertOccurenceDate |Data |Data di creazione dell'avviso |
| AlertSeverity |Text |Gravità dell'avviso, ad esempio, Critical |
| AlertStatus |Text |Stato dell'avviso, ad esempio, Active |
| AlertType |Text |Tipo di avviso generato, ad esempio, Backup |
| AlertUniqueId |Text |ID univoco dell'avviso generato |
| AsOnDateTime |Data/ora |Data e ora dell'aggiornamento più recente della riga selezionata |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Numero decimale |Tempo medio (in minuti) di risoluzione dell'avviso per il periodo di tempo selezionato |
| EntityState |Text |Stato corrente dell'oggetto avviso, ad esempio, Active o Deleted |

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
| BackupItemType |Text |Tipo dell'elemento di backup, ad esempio, VM o FileFolder |
| EntityState |Text |Stato corrente dell'oggetto elemento di backup, ad esempio, Active o Deleted |
| LastBackupDateTime |Data/ora |Data e ora del backup più recente dell'elemento di backup selezionato |
| LastBackupState |Text |Stato del backup più recente dell'elemento di backup selezionato, ad esempio, Successful o Failed |
| LastSuccessfulBackupDateTime |Data/ora |Data e ora del backup più recente con esito positivo dell'elemento di backup selezionato |
| ProtectionState |Text |Stato della protezione corrente dell'elemento di backup, ad esempio, Protected o ProtectionStopped |

### <a name="calendar"></a>Calendario
Questa tabella offre dettagli sui campi relativi al calendario.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| Data |Data |Data selezionata per il filtro dei dati |
| DateKey |Text |Chiave univoca per ogni elemento data |
| DayDiff |Numero decimale |Differenza relativa al giorno per il filtro dei dati. Ad esempio, 0 indica i dati del giorno corrente, -1 i dati del giorno precedente, 0 e -1 indicano i dati del giorno corrente e di quello precedente  |
| Mese |Text |Mese dell'anno selezionato per il filtro dei dati. Il mese inizia il primo giorno e finisce il 31° giorno |
| MonthDate | Data |Data di fine del mese, selezionata per il filtro dei dati |
| MonthDiff |Numero decimale |Differenza relativa al mese per il filtro dei dati. Ad esempio, 0 indica i dati del mese corrente, -1 i dati del mese precedente, 0 e -1 indicano i dati del mese corrente e di quello precedente |
| Settimana |Text |Settimana selezionata come filtro dei dati. La settimana inizia la domenica e finisce il sabato |
| WeekDate |Data |Data dell'ultima settimana del mese, selezionata come filtro dei dati |
| WeekDiff |Numero decimale |Differenza relativa alla settimana per il filtro dei dati. Ad esempio, 0 indica i dati della settimana corrente, -1 i dati della settimana precedente, 0 e -1 indicano i dati della settimana corrente e di quella precedente |
| Year |Text |Anno di calendario selezionato per il filtro dei dati |
| YearDate |Data |Data della fine dell'anno, selezionata come filtro dei dati |

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
| BackupStorageDestination |Text |Destinazione dell'archiviazione di backup, ad esempio, Cloud o Disk  |
| EntityState |Text |Stato corrente dell'oggetto processo, ad esempio, Active o Deleted |
| JobFailureCode |Text |Stringa di codice contenente un errore e a causa della quale si è verificato un errore del processo |
| JobOperation |Text |Operazione per la quale viene eseguito il processo, ad esempio Backup, Restore o Configure Backup |
| JobStartDate |Data |Data di avvio dell'esecuzione del processo |
| JobStartTime |Time |Ora di avvio dell'esecuzione del processo |
| Stato processo |Text |Stato del processo completato, ad esempio, Completed o Failed |
| JobUniqueId |Text |ID univoco per l'identificazione del processo |

### <a name="policy"></a>Criteri
Questa tabella presenta i campi e le aggregazioni di base per diversi campi correlati ai criteri.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| #Policies |Numero intero |Numero di criteri di backup presenti nel sistema |
| #PoliciesInUse |Numero intero |Numero di criteri attualmente in uso per la configurazione di backup |
| AsOnDateTime |Data/ora |Data e ora dell'aggiornamento più recente della riga selezionata |
| BackupDaysOfTheWeek |Text |Giorni della settimana per i quali sono stati pianificati backup |
| BackupFrequency |Text |Frequenza con cui vengono eseguiti i backup, ad esempio, daily o weekly |
| BackupTimes |Text |Data e ora in cui sono pianificati backup |
| DailyRetentionDuration |Numero intero |Durata totale di mantenimento dati in giorni per i backup configurati |
| DailyRetentionTimes |Text |Data e ora di configurazione del mantenimento dati giornaliero |
| EntityState |Text |Stato corrente dell'oggetto criteri, ad esempio, Active o Deleted |
| MonthlyRetentionDaysOfTheMonth |Text |Date del mese selezionate per il mantenimento dati mensile |
| MonthlyRetentionDaysOfTheWeek |Text |Giorni della settimana selezionati per il mantenimento dati mensile |
| MonthlyRetentionDuration |Numero decimale |Durata totale di mantenimento dati in mesi per i backup configurati |
| MonthlyRetentionFormat |Text |Tipo di configurazione per il mantenimento dati mensile, ad esempio, daily per la configurazione basata sui giorni o weekly per la configurazione basata sulle settimane |
| MonthlyRetentionTimes |Text |Data e ora di configurazione del mantenimento dati mensile |
| MonthlyRetentionWeeksOfTheMonth |Text |Settimane del mese per le quali è configurato il mantenimento dati mensile, ad esempio, First, Last e così via. |
| PolicyName |Text |Nome dei criteri definiti |
| PolicyUniqueId |Text |ID univoco per l'identificazione dei criteri |
| RetentionType |Text |Tipo di criteri di mantenimento dati, ad esempio, Daily, Weekly, Monthly, Yearly |
| WeeklyRetentionDaysOfTheWeek |Text |Giorni della settimana selezionati per il mantenimento dati settimanale |
| WeeklyRetentionDuration |Numero decimale |Durata totale del mantenimento dati settimanale in settimane per i backup configurati |
| WeeklyRetentionTimes |Text |Data e ora di configurazione del mantenimento dati settimanale |
| YearlyRetentionDaysOfTheMonth |Text |Date del mese selezionate per il mantenimento dati annuale |
| YearlyRetentionDaysOfTheWeek |Text |Giorni della settimana selezionati per il mantenimento dati annuale |
| YearlyRetentionDuration |Numero decimale |Durata totale di mantenimento dati in anni per i backup configurati |
| YearlyRetentionFormat |Text |Tipo di configurazione per il mantenimento dati annuale, ad esempio, daily per la configurazione basata sui giorni o weekly per la configurazione basata sulle settimane |
| YearlyRetentionMonthsOfTheYear |Text |Mesi dell'anno selezionati per il mantenimento dati annuale |
| YearlyRetentionTimes |Text |Data e ora di configurazione del mantenimento dati annuale |
| YearlyRetentionWeeksOfTheMonth |Text |Settimane del mese per le quali è configurato il mantenimento dati annuale, ad esempio, First, Last e così via. |

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
| BackupManagementType |Text |Tipo di provider per l'esecuzione del backup, ad esempio, IaaSVM o FileFolder |
| EntityState |Text |Stato corrente dell'oggetto server protetto, ad esempio, Active o Deleted |
| ProtectedServerFriendlyName |Text |Nome descrittivo del server protetto |
| ProtectedServerName |Text |Nome del server protetto |
| ProtectedServerType |Text |Tipo del server protetto di cui si esegue il backup, ad esempio, IaaSVMContainer |
| ProtectedServerName |Text |Nome del server protetto a cui appartiene l'elemento di backup |
| RegisteredContainerId |Text |ID del contenitore registrato per il backup |

### <a name="storage"></a>Archiviazione
Questa tabella presenta i campi e le aggregazioni di base per diversi campi correlati all'archiviazione.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| #ProtectedInstances |Numero decimale |Numero di istanze protette usato per il calcolo dell'archiviazione front-end a scopo di fatturazione, calcolato in base al valore più recente nel periodo selezionato |
| AsOnDateTime |Data/ora |Data e ora dell'aggiornamento più recente della riga selezionata |
| CloudStorageInMB |Numero decimale |Spazio di archiviazione nel cloud usato dal backup, calcolato in base al valore più recente nel periodo selezionato |
| EntityState |Text |Stato corrente dell'oggetto, ad esempio, Active o Deleted |
| LastUpdatedDate |Data |Data dell'aggiornamento più recente della riga selezionata |

### <a name="time"></a>Time
Questa tabella offre dettagli sui campi relativi al tempo.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| Hour |Time |Ora del giorno, ad esempio 13.00.00 |
| HourNumber |Numero decimale |Numero dell'ora nel giorno, ad esempio 13,00 |
| Minuto |Numero decimale |Minuto dell'ora |
| PeriodOfTheDay |Text |Periodo all'interno del giorno, ad esempio, 12.00-15.00 |
| Time |Time |Ora del giorno, ad esempio 00.00.01 |
| TimeKey |Text |Valore chiave per rappresentare l'ora |

### <a name="vault"></a>Insiemi di credenziali
Questa tabella presenta i campi e le aggregazioni di base per diversi campi correlati agli insiemi di credenziali.

| Campo | Tipo di dati | Descrizione |
| --- | --- | --- |
| #Vaults |Numero intero |Numero di insiemi di credenziali |
| AsOnDateTime |Data/ora |Data e ora dell'aggiornamento più recente della riga selezionata |
| AzureDataCenter |Text |Data center in cui si trova l'insieme di credenziali |
| EntityState |Text |Stato corrente dell'oggetto insieme di credenziali, ad esempio, Active o Deleted |
| StorageReplicationType |Text |Tipo di replica di archiviazione per l'insieme di credenziali, ad esempio, GeoRedundant |
| SubscriptionId |Text |ID sottoscrizione del cliente selezionato per la generazione di report |
| VaultName |Text |Nome dell'insieme di credenziali |
| VaultTags |Text |Tag associati all'insieme di credenziali |

## <a name="next-steps"></a>Passaggi successivi
Dopo aver rivisto il modello di dati per la creazione di report di Backup di Azure, fare riferimento agli articoli seguenti per altri dettagli sulla creazione e sulla visualizzazione di report in Power BI.

* [Creazione di report in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Applicazione di filtri ai report in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
