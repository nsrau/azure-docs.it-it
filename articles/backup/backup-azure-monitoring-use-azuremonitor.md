---
title: 'Backup di Azure: Monitorare i Backup di Azure con monitoraggio di Azure'
description: Monitorare i carichi di lavoro di Backup di Azure e creare avvisi personalizzati usando monitoraggio di Azure
services: backup
author: pvrk
manager: shivamg
keywords: Log Analitica; Backup di Azure; Avvisi Impostazioni di diagnostica. Gruppi di azioni
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 1e85b633024b5a3e85874707ae9a1f068e7a328d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808514"
---
# <a name="monitoring-at-scale-using-azure-monitor"></a>Monitoraggio su larga scala tramite Monitoraggio di Azure

Il [predefiniti di monitoraggio e avviso articolo](backup-azure-monitoring-built-in-monitor.md) elencati il monitoraggio e avviso di funzionalità in un singolo insieme di credenziali e che è disponibile senza infrastruttura di gestione aggiuntive. Il servizio incorporato è tuttavia limitato negli scenari seguenti.

- Dati di monitoraggio da più insiemi di credenziali RS tra sottoscrizioni
- Se l'indirizzo di posta elettronica non è il canale di notifica preferito
- Se gli utenti desiderano ricevere un avviso per altri scenari
- Se si desidera visualizzare le informazioni dal componente in locale, ad esempio System Center DPM (SC DPM) in Azure, che non viene visualizzato nel [processi di Backup](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) oppure [avvisi di Backup](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) nel portale.

## <a name="using-log-analytics-workspace"></a>Uso dell'area di lavoro di Log Analitica

> [!NOTE]
> I dati da backup di macchine Virtuali di Azure, agenti MAB, System Center DPM (SC DPM), i backup di SQL in macchine virtuali di Azure sono viene eseguito il pump all'area di lavoro di Log Analitica tramite le impostazioni di diagnostica. Supporto per i backup di condivisioni File di Azure, Server di Backup di Microsoft Azure (MABS) sarà presto disponibile.

Vengono usate le funzionalità di due servizi di Azure - **le impostazioni di diagnostica** (per inviare dati da più risorse di Azure Resource Manager a un'altra risorsa) e **Log Analitica** (LA - generare avvisi personalizzati in cui è possibile definire altri canali di notifica con gruppi di azioni) per il monitoraggio su larga scala. Le seguenti sezioni dettagliate su come usare LA per monitorare i Backup di Azure su larga scala.

### <a name="configuring-diagnostic-settings"></a>Configurazione delle impostazioni di diagnostica

Una risorsa di Azure Resource Manager, ad esempio credenziali dei servizi di ripristino di Azure registra tutte le informazioni possibili sulle operazioni pianificate e le operazioni utente attivato come dati di diagnostica. Fare clic su 'impostazioni di diagnostica' nella sezione di monitoraggio e specificare la destinazione per i dati di diagnostica dell'insieme di credenziali RS.

![Impostazione di diagnostica di insieme di credenziali RS e come destinazione](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

È possibile selezionare un'area di lavoro LA da un'altra sottoscrizione come destinazione. *Se si seleziona l'area di lavoro LA stessa per più insiemi di credenziali RS, è possibile monitorare gli insiemi di credenziali tra le sottoscrizioni in un'unica posizione.* Selezionare 'AzureBackupReport' come l'accesso al canale tutte le informazioni all'area di lavoro LA relative al Backup di Azure.

> [!IMPORTANT]
> Dopo aver completato la configurazione, è necessario attendere 24 ore prima che venga completato il push dei dati iniziale. Successivamente tutti gli eventi vengono inseriti come indicato nella [sezione frequenza](#diagnostic-data-update-frequency).

### <a name="deploying-solution-to-log-analytics-workspace"></a>Distribuzione di soluzioni all'area di lavoro di Log Analitica

Una volta che i dati si trova all'interno dell'area di lavoro LA, [distribuire un modello di GitHub](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) nella per visualizzare i dati. Accertarsi di che assegnare lo stesso gruppo di risorse, nome area di lavoro e percorso area di lavoro per identificare in modo corretto l'area di lavoro e quindi installare il modello su di esso.

### <a name="view-azure-backup-data-using-log-analytics-la"></a>Visualizzare i dati di Backup di Azure con Log Analitica (LA)

Dopo aver distribuito il modello, la soluzione per il monitoraggio di Backup di Azure verrà visualizzata nell'area di riepilogo dell'area di lavoro. È possibile attraversare tramite

- Monitoraggio di Azure -> "Altro" nella sezione "Insights" e scegliere l'area di lavoro pertinente o
- Le aree di lavoro di log Analitica -> selezionare l'area di lavoro pertinente -> 'Workspace riepilogo' nella sezione generale.

![AzureBackupLAMonitoringTile](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

Nella selezione del riquadro, il modello della finestra di progettazione apre una serie di grafici sui dati di monitoraggio di base da Backup di Azure, ad esempio

#### <a name="all-backup-jobs"></a>Tutti i processi di Backup

![LABackupJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

#### <a name="restore-jobs"></a>Processi di ripristino

![LARestoreJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

#### <a name="inbuilt-azure-backup-alerts-for-azure-resources"></a>Avvisi di Backup di Azure incorporati per le risorse di Azure

![LAInbuiltAzureBackupAlertsForAzureResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

#### <a name="inbuilt-azure-backup-alerts-for-on-prem-resources"></a>Avvisi di Backup di Azure integrati per risorse locali

![LAInbuiltAzureBackupAlertsForOnPremResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

#### <a name="active-datasources"></a>Origini dati attive

![LAActiveBackedUpEntities](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

#### <a name="rs-vault-cloud-storage"></a>Archiviazione Cloud insieme di credenziali RS

![LARSVaultCloudStorage](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

I grafici precedenti vengono forniti con il modello e il cliente ha la possibilità di modificare o aggiungere più grafici.

> [!IMPORTANT]
> Quando si distribuisce il modello, essenzialmente crea un blocco di sola lettura e si vuole rimuovere in modo da modificare il modello e salvare. Per rimuovere i blocchi, esaminare il riquadro "Blocchi" nella sezione "Impostazioni" dell'area di lavoro di Log Analitica.

### <a name="create-alerts-using-log-analytics"></a>Creare gli avvisi tramite Log Analitica

Monitoraggio di Azure consente agli utenti di creare i propri avvisi dall'area di lavoro LA in cui è possibile *sfruttare i gruppi di azioni di Azure per selezionare il meccanismo di notifica preferito*. Fare clic su uno dei grafici in precedenza per aprire la sezione 'Log' dell'area di lavoro LA ***in cui è possibile modificare le query e creare avvisi su questi***.

![LACreateAlerts](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Facendo clic su "Nuova regola di avviso" come illustrato in precedenza viene visualizzata la schermata di creazione degli avvisi di monitoraggio di Azure.

Come si noterà in seguito, la risorsa è già contrassegnata come area di lavoro LA e viene offerta l'integrazione di gruppo di azione.

![LAAzureBackupCreateAlert](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

> [!IMPORTANT]
> Si noti che l'impatto dei prezzi pertinente la creazione di questa query viene fornito [qui](https://azure.microsoft.com/pricing/details/monitor/).

#### <a name="alert-condition"></a>Condizione di avviso

L'aspetto principale è la condizione di attivazione dell'avviso. Facendo clic su 'Condition' caricherà automaticamente la query Kusto nella schermata "Logs" come illustrato di seguito ed è possibile modificare in modo da adattare allo scenario. Vengono forniti alcuni esempi di query Kusto nel [sezione riportata di seguito](#sample-kusto-queries).

![LAAzureBackupAlertCondition](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Modificare la query Kusto, se necessario, selezionare la soglia a destra (che deciderà se verrà generato l'avviso), il periodo di corretto (intervallo di tempo per cui viene eseguita la query) e la relativa frequenza. Ad esempio: Se la soglia è superiore a 0, il periodo è 5 minuti e la frequenza è 5 minuti, quindi la regola viene convertita come "Eseguire la query ogni 5 minuti negli ultimi 5 minuti e se il numero di risultati è maggiore di 0, invia una notifica tramite il gruppo di azione selezionato"

#### <a name="action-group-integration"></a>Integrazione di gruppo di azione

Gruppi di azioni specificano i canali di notifica disponibili per l'utente. Facendo clic su "Crea nuovo" in "Gruppi di azioni" sezione mostra l'elenco dei meccanismi di notifica disponibili.

![LAAzureBackupNewActionGroup](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Altre informazioni sul [gli avvisi dall'area di lavoro LA](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) e circa [gruppi di azioni](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) dalla documentazione di monitoraggio di Azure.

Di conseguenza è possibile soddisfare tutti gli avvisi e monitoraggio dei requisiti from solo LA o usarla come una tecnica supplementare a meccanismi di notifica predefinite.

### <a name="sample-kusto-queries"></a>Esempi di query Kusto

I grafici predefiniti consente query Kusto per scenari di base su cui è possibile creare avvisi. È inoltre possibile modificare in modo da ottenere i dati che si desidera ricevere un avviso. Di seguito si forniscono alcuni esempi di query Kusto che è possibile incollare nella finestra "Logs" e quindi creare un avviso per la query.

#### <a name="all-successful-backup-jobs"></a>Tutti i processi di backup ha esito positivo

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Completed"
````

#### <a name="all-failed-backup-jobs"></a>Tutti i processi di backup non riusciti

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Failed"
````

#### <a name="all-successful-azure-vm-backup-jobs"></a>Tutti i processi di backup della macchina virtuale di Azure ha esito positivo

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

#### <a name="all-successful-sql-log-backup-jobs"></a>Tutti i processi di backup Log SQL ha esito positivo

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

#### <a name="all-successful-mab-agent-backup-jobs"></a>Tutti i processi di backup dell'agente MAB ha esito positivo

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

### <a name="diagnostic-data-update-frequency"></a>Frequenza di aggiornamento dei dati di diagnostica

I dati di diagnostica dall'insieme di credenziali vengano eseguito il pump all'area di lavoro LA con un ritardo. Ogni evento arriva all'area di lavoro LA ***con un ritardo di circa 20-30 minuti dopo che viene eseguito il push dall'insieme di credenziali RS.***

- Gli avvisi predefiniti del servizio di backup (su tutte le soluzioni) vengono eseguito il push non appena vengono creati. Questo significa in genere appaiono nell'area di lavoro LA dopo un ritardo di circa 20-30 minuti.
- I processi di backup ad hoc e processi di ripristino (per tutte le soluzioni) vengono eseguito il push appena essi ***completamento***.
- I processi di backup pianificati da tutte le soluzioni (ad eccezione di backup di SQL) vengono eseguito il push appena essi ***completamento***.
- Per il backup SQL, poiché possiamo avere i backup del log ogni 15 minuti, per tutti il processo pianificato completato backup, inclusi i log, le informazioni sono eseguite in batch e il push di ogni 6 ore.
- Viene effettuato il push di tutte le altre informazioni, ad esempio l'elemento di backup, criteri, i punti di ripristino, archiviazione e così via in tutte le soluzioni **almeno una volta al giorno.**
- Una modifica nella configurazione del backup, ad esempio la modifica dei criteri, criteri e così via di modifica attiva un push di tutte le relative informazioni di backup.

## <a name="using-rs-vaults-activity-logs"></a>I log attività usando l'insieme di credenziali RS

È anche possibile utilizzare i log attività di ricevere notifica degli eventi, ad esempio completamento del backup.

> [!CAUTION]
> **Tenere presente che questa opzione è disponibile solo per i backup di macchine Virtuali di Azure.** È possibile utilizzare questo per altre soluzioni, ad esempio Azure Backup Agent, i backup di SQL all'interno di Azure, i file di Azure, ecc.

### <a name="sign-in-into-azure-portal"></a>Accedere al portale di Azure

Accedi al portale di Azure e procedere con l'insieme di credenziali di servizi di ripristino di Azure pertinente e fare clic sulla sezione "Registro attività" nelle proprietà.

### <a name="identify-appropriate-log-and-create-alert"></a>Identificare il log appropriato e crea avviso

Applicare i filtri visualizzati nella figura seguente per verificare se si riceveranno log attività per i backup riusciti. Modificare l'intervallo di tempo in modo appropriato per visualizzare i record.

![Log attività per i backup di macchine Virtuali di Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

Fare clic sul nome dell'operazione verrà visualizzato l'operazione e i relativi dettagli.

![Nuova regola di avviso](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Fare clic su **nuova regola di avviso** per aprire il **Crea regola** dello schermo, qui è possibile creare avvisi usando i passaggi descritti in questo [articolo](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

In questo caso la risorsa è l'insieme di credenziali di servizi di ripristino se stesso e di conseguenza è necessario ripetere la stessa azione per tutti gli insiemi di credenziali in cui si desidera la notifica tramite i log attività. La condizione non sarà possibile qualsiasi soglia, periodo, frequenza poiché si tratta di un avviso basato su eventi. Non appena viene generato il log attività rilevanti, viene generato l'avviso.

## <a name="recommendation"></a>Recommendation

***Tutti gli avvisi creati dai log attività e LA le aree di lavoro possono essere visualizzati in Monitoraggio di Azure nel riquadro "Avvisi" a sinistra.***

Mentre la notifica tramite i log attività può essere utilizzata, ***servizio Backup di Azure consiglia di usare LA per il monitoraggio in scala e non i log attività per i motivi seguenti***.

- **Alcuni scenari:** Applicabile solo per i backup di macchine Virtuali di Azure e deve essere ripetuto per ogni insieme di credenziali RS.
- **Definizione di base:** L'attività di backup pianificato non è compatibile con l'ultima definizione del log attività e viene allineato [log di diagnostica](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-you-can-do-with-diagnostic-logs). Ciò ha comportato un impatto non previsto quando i dati distribuendo tramite i canali di log attività vengono modificati come evidenziato di seguito.
- **Problemi con canale del registro attività:** Si è passati a un nuovo modello di distribuzione dei log attività da Backup di Azure negli insiemi di credenziali di Servizi di ripristino. Sfortunatamente, lo spostamento è interessata la generazione dei log di attività nei cloud sovrani di Azure. Se gli utenti del Cloud sovrani di Azure creato/configurato tutti gli avvisi dai log attività tramite Monitoraggio di Azure, si potrebbe non essere attivate. Inoltre, in tutte le aree pubbliche di Azure, se un utente sta raccogliendo i log attività di Servizi di ripristino in un'area di lavoro di Log Analytics come indicato [qui](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), anche questi log non saranno visibili.

Di conseguenza, è consigliabile usare analisi di Log dell'area di lavoro per il monitoraggio e avvisi su larga scala per tutti i Backup di Azure di carichi di lavoro protetti.

## <a name="next-steps"></a>Passaggi successivi

- Fare riferimento a [modello di dati di Log analitica](backup-azure-log-analytics-data-model.md) per creare query personalizzate.
