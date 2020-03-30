---
title: Monitor Azure Backup with Azure Monitor
description: Monitorare i carichi di lavoro di Backup di Azure e creare avvisi personalizzati tramite Monitoraggio di Azure.Monitor Azure Backup workloads and create custom alerts by using Azure Monitor.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 547cef66be9902468f4e2755c31e5f586eccad5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459515"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitor at scale by using Azure Monitor

Backup di Azure offre [funzionalità di monitoraggio e avviso predefinite](backup-azure-monitoring-built-in-monitor.md) in un insieme di credenziali di Servizi di ripristino. Queste funzionalità sono disponibili senza alcuna infrastruttura di gestione aggiuntiva. Ma questo servizio incorporato è limitato negli scenari seguenti:But this built-in service is limited in the following scenarios:

- Se si monitorano i dati da più vault di Servizi di ripristino tra sottoscrizioni
- Se il canale di notifica preferito *non* è e-mail
- Se gli utenti desiderano avvisi per più scenari
- Se si desidera visualizzare informazioni da un componente locale, ad esempio System Center Data Protection Manager in Azure, che il portale non viene visualizzato nei processi di [**backup**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) o avvisi di [**backup**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Utilizzo dell'area di lavoro di Log Analytics

### <a name="create-alerts-by-using-log-analytics"></a>Creare avvisi tramite Log AnalyticsCreate alerts by using Log Analytics

In Monitoraggio di Azure è possibile creare avvisi personalizzati in un'area di lavoro di Log Analytics.In Azure Monitor, you can create your own alerts in a Log Analytics workspace. Nell'area di lavoro si usano i gruppi di azioni di *Azure* per selezionare il meccanismo di notifica preferito.

> [!IMPORTANT]
> Per informazioni sul costo di creazione della query, vedere Prezzi di [Monitoraggio di Azure.](https://azure.microsoft.com/pricing/details/monitor/)

Aprire la sezione **Log** dell'area di lavoro Log Analytics e creare una query per i propri Log. Quando si seleziona **Nuova regola**di avviso , viene visualizzata la pagina di creazione degli avvisi di Azure Monitor, come illustrato nell'immagine seguente.

![Creare un avviso in un'area di lavoro di Log AnalyticsCreate an alert in a Log Analytics workspace](media/backup-azure-monitoring-laworkspace/custom-alert.png)

In questo caso la risorsa è già contrassegnata come area di lavoro di Log Analytics e viene fornita l'integrazione del gruppo di azioni.

![Pagina di creazione degli avvisi di Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Condizione di avviso

La caratteristica distintiva di un avviso è la sua condizione di attivazione. Selezionare **Condizione** per caricare automaticamente la query Kusto nella pagina **Registri,** come illustrato nell'immagine seguente. Qui è possibile modificare la condizione in base alle proprie esigenze. Per ulteriori informazioni, consultate [Query Kusto di esempio.](#sample-kusto-queries)

![Impostazione di una condizione di avviso](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Se necessario, è possibile modificare la query Kusto. Scegliere una soglia, un periodo e una frequenza. La soglia determina quando verrà generato l'avviso. Il periodo è l'intervallo di tempo in cui viene eseguita la query. Ad esempio, se la soglia è maggiore di 0, il periodo è di 5 minuti e la frequenza è di 5 minuti, la regola esegue la query ogni 5 minuti, esaminando i 5 minuti precedenti. Se il numero di risultati è maggiore di 0, si riceve una notifica tramite il gruppo di azioni selezionato.

#### <a name="alert-action-groups"></a>Gruppi di azioni di avviso

Utilizzare un gruppo di azioni per specificare un canale di notifica. Per visualizzare i meccanismi di notifica disponibili, in **Gruppi di azioni**selezionare Crea **nuovo**.

![Meccanismi di notifica disponibili nella finestra "Aggiungi gruppo di azioni"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

È possibile soddisfare tutti i requisiti di avviso e monitoraggio da Log Analytics da solo, oppure è possibile utilizzare Log Analytics per integrare le notifiche predefinite.

Per altre informazioni, vedere [Creare, visualizzare e gestire avvisi di log tramite Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) e Creare e gestire gruppi di azioni nel portale di Azure.For more information, see Create, view, and manage log alerts by using Azure Monitor and Create and manage action groups in the Azure [portal.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

### <a name="sample-kusto-queries"></a>Query Kusto di esempio

I grafici predefiniti forniscono query Kusto per scenari di base in cui è possibile creare avvisi. È inoltre possibile modificare le query per ottenere i dati su cui si desidera ricevere avvisi. Incollare le query Kusto di esempio seguenti nella pagina **Registri** e quindi creare avvisi sulle query:

- Tutti i processi di backup riusciti

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    ````

- Tutti i processi di backup non riusciti

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Failed"
    ````

- Tutti i processi di backup della macchina virtuale di Azure riuscitiAll successful Azure VM backup jobs

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Tutti i processi di backup del log SQL riusciti

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Tutti i processi dell'agente di Backup di Azure riuscitiAll successful Azure Backup agent jobs

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Spazio di archiviazione di backup utilizzato per elemento di backup

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId 
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs 
    | sort by StorageConsumedInMBs desc
    ````


### <a name="diagnostic-data-update-frequency"></a>Frequenza di aggiornamento dei dati di diagnostica

I dati di diagnostica dell'insieme di credenziali vengono pompati nell'area di lavoro di Log Analytics con un certo ritardo. Ogni evento arriva all'area di lavoro di Log Analytics *da 20 a 30 minuti* dopo il push dall'insieme di credenziali di Servizi di ripristino. Ecco ulteriori dettagli sul ritardo:

- In tutte le soluzioni, gli avvisi predefiniti del servizio di backup vengono inviati non appena vengono creati. In genere vengono visualizzati nell'area di lavoro di Log Analytics dopo 20-30 minuti.
- In tutte le soluzioni, i processi di backup su richiesta e i processi di ripristino vengono inviati al *termine.*
- Per tutte le soluzioni ad eccezione del backup SQL, i processi di backup pianificati vengono inseriti al *termine.*
- Per il backup SQL, poiché i backup del log possono essere eseguiti ogni 15 minuti, le informazioni per tutti i processi di backup pianificati completati, inclusi i log, vengono inviate in batch e inviate ogni 6 ore.
- In tutte le soluzioni, altre informazioni, ad esempio l'elemento di backup, i criteri, i punti di ripristino, l'archiviazione e così via, vengono inviate almeno *una volta al giorno.*
- Una modifica nella configurazione di backup (ad esempio la modifica dei criteri o la modifica dei criteri) attiva un push di tutte le informazioni di backup correlate.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Utilizzo dei registri attività dell'insieme di credenziali dei servizi di ripristino

> [!CAUTION]
> I passaggi seguenti si applicano solo ai backup delle macchine virtuali di Azure.The following steps apply only to *Azure VM backups.* Non è possibile usare questi passaggi per soluzioni come l'agente di Backup di Azure, i backup SQL all'interno di Azure o i file di Azure.You can't use these steps for solutions such as the Azure Backup agent, SQL backups within Azure, or Azure Files.

È inoltre possibile utilizzare i log attività per ottenere notifiche per eventi quali l'esito positivo del backup. Per iniziare, attenersi alla seguente procedura:

1. Accedere al portale di Azure.
1. Aprire l'insieme di credenziali dei servizi di ripristino pertinente.
1. Nelle proprietà del vault, aprire la sezione **Registro attività.**

Per identificare il log appropriato e creare un avviso:

1. Verificare che si ricevano i log attività per il corretto backup applicando i filtri illustrati nell'immagine seguente. Modificare il valore **Dissidazione** in base alle esigenze per visualizzare i record.

   ![Filtro per trovare i log attività per i backup delle macchine virtuali di AzureFiltering to find activity logs for Azure VM backups](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Selezionare il nome dell'operazione per visualizzare i dettagli pertinenti.
1. Selezionare **Nuova regola di avviso** per aprire la pagina Crea **regola.**
1. Creare un avviso seguendo la procedura descritta in Creare, visualizzare e gestire gli avvisi del [log attività tramite Monitoraggio di Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

   ![Nuova regola di avviso](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Qui la risorsa è l'insieme di credenziali dei servizi di ripristino stesso. Ripetere gli stessi passaggi per tutti gli insiemi di credenziali in cui si desidera ricevere una notifica tramite i registri attività. La condizione non avrà una soglia, un periodo o una frequenza perché questo avviso è basato sugli eventi. Non appena viene generato il log attività pertinente, viene generato l'avviso.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Utilizzo di Log Analytics per il monitoraggio su larga scala

È possibile visualizzare tutti gli avvisi creati dai log attività e dalle aree di lavoro di Log Analytics in Monitoraggio di Azure.You can view all alerts created from activity logs and Log Analytics workspaces in Azure Monitor. Basta aprire il riquadro **Avvisi** a sinistra.

Sebbene sia possibile ricevere notifiche tramite i log attività, è consigliabile usare Log Analytics anziché i log attività per il monitoraggio su larga scala. La ragione di questo comportamento è la seguente:

- **Scenari limitati:** le notifiche tramite log attività si applicano solo ai backup delle macchine virtuali di Azure.Limited scenarios: Le notifiche tramite log attività si applicano solo ai backup delle macchine virtuali di Azure.Limited scenarios: Le notifiche devono essere impostate per ogni insieme di credenziali di Servizi di ripristino.
- **Adattamento definizione**: L'attività di backup pianificata non rientra nell'ultima definizione dei log attività. Al contrario, si allinea con [i log delle risorse.](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-platform-logs-in-a-workspace) Questo allineamento causa effetti imprevisti quando i dati che passano attraverso il canale del log attività cambiano.
- **Problemi con il canale del log attività:** negli insiemi di credenziali di Servizi di ripristino, i log attività che vengono pompati da Backup di Azure seguono un nuovo modello. Sfortunatamente, questa modifica influisce sulla generazione di log attività in Azure per enti pubblici, Azure Germania e Azure China 21Vianet. Se gli utenti di questi servizi cloud creano o configurano avvisi dai log attività in Monitoraggio di Azure, gli avvisi non vengono attivati. Inoltre, in tutte le aree pubbliche di Azure, se un utente raccoglie i log attività di Servizi di [ripristino in un'area](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)di lavoro di Log Analytics, questi log non vengono visualizzati.

Usare un'area di lavoro di Log Analytics per il monitoraggio e gli avvisi su larga scala per tutti i carichi di lavoro protetti da Backup di Azure.Use a Log Analytics workspace for monitoring and alerting at scale for all your workloads that are protected by Azure Backup.

## <a name="next-steps"></a>Passaggi successivi

Per creare query personalizzate, vedere Modello di dati di [Log Analytics](backup-azure-reports-data-model.md).
