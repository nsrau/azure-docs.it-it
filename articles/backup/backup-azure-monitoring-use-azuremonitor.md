---
title: 'Backup di Azure: Monitorare i Backup di Azure con monitoraggio di Azure'
description: Monitorare i carichi di lavoro di Backup di Azure e creare avvisi personalizzati usando monitoraggio di Azure.
services: backup
author: pvrk
manager: shivamg
keywords: Log Analitica; Backup di Azure; Avvisi Impostazioni di diagnostica. Gruppi di azioni
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: e2d4a235737789f2f5852c00218427613db3d558
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786324"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitorare su larga scala tramite Monitoraggio di Azure

Backup di Azure offre [monitoraggio e avviso di funzionalità predefinite](backup-azure-monitoring-built-in-monitor.md) in un insieme di credenziali di servizi di ripristino. Queste funzionalità sono disponibili senza infrastruttura di gestione aggiuntive. Ma questo servizio predefinito è limitato nei seguenti scenari:

- Se si esegue il monitoraggio dei dati da più insiemi di credenziali di servizi di ripristino tra sottoscrizioni
- Se il canale di notifica preferita *non* messaggio di posta elettronica
- Se gli utenti desidera che gli avvisi per altri scenari
- Se si desidera visualizzare le informazioni da un componente locale, ad esempio System Center Data Protection Manager in Azure, che non viene visualizzato il portale [ **processi di Backup** ](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) oppure [  **Avvisi di backup**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Uso dell'area di lavoro di Log Analitica

> [!NOTE]
> I dati da backup di macchine Virtuali di Azure, l'agente Azure Backup, System Center Data Protection Manager, i backup di SQL in macchine virtuali di Azure e i backup di condivisioni file di Azure vengano eseguito il pump all'area di lavoro di Log Analitica tramite le impostazioni di diagnostica. 

Per monitorare su larga scala, è necessario utilizzare le funzionalità di due servizi di Azure. *Le impostazioni di diagnostica* inviare dati da più risorse di Azure Resource Manager a un'altra risorsa. *Log Analitica* genera avvisi personalizzati in cui è possibile usare i gruppi di azioni per definire altri canali di notifica. 

Le sezioni seguenti illustrano come usare Log Analitica per monitorare i Backup di Azure su larga scala.

### <a name="configure-diagnostic-settings"></a>Configurare le impostazioni di diagnostica

Risorse di Azure Resource Manager, ad esempio l'insieme di credenziali di servizi di ripristino, registrano informazioni sulle operazioni pianificate e le operazioni attivate dall'utente come dati di diagnostica. 

Nella sezione di monitoraggio, selezionare **le impostazioni di diagnostica** e specificare la destinazione per i dati di diagnostica dell'insieme di credenziali di servizi di ripristino.

![Impostazione di diagnostica dell'insieme di credenziali di servizi di ripristino, come destinazione di Log Analitica](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

È possibile assegnare un'area di lavoro di Log Analitica da un'altra sottoscrizione. Per monitorare gli insiemi di credenziali tra le sottoscrizioni in un'unica posizione, selezionare la stessa area di lavoro di Log Analitica per più insiemi di credenziali di servizi di ripristino. Per tutte le informazioni correlate a Backup di Azure nell'area di lavoro di Log Analitica del canale, selezionare **AzureBackupReport** del log.

> [!IMPORTANT]
> Dopo aver completato la configurazione, è necessario attendere 24 ore per il push dei dati iniziali alla fine. Dopo che la iniziale push di dati, tutti gli eventi vengono inseriti come descritto più avanti in questo articolo, nelle [sezione frequenza](#diagnostic-data-update-frequency).

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Distribuire una soluzione all'area di lavoro di Log Analitica

Dopo che i dati si trova all'interno di area di lavoro di Log Analitica [distribuire un modello di GitHub](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) al Log Analitica per visualizzare i dati. Per identificare correttamente l'area di lavoro, accertarsi di che assegnare lo stesso gruppo di risorse, nome dell'area di lavoro e percorso area di lavoro. Installare quindi il modello nell'area di lavoro.

> [!NOTE]
> Se non hai avvisi, i processi di backup o i processi di ripristino nell'area di lavoro di Log Analitica, si può vedere un codice di errore "BadArgumentError" nel portale. Ignorare questo errore e continuare a usare la soluzione. Dopo che i dati rilevanti digitare avvio trasmesso all'area di lavoro, le visualizzazioni rifletterà lo stesso e non verrà più visualizzato l'errore.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Visualizzare i dati di Backup di Azure con Log Analitica

Dopo aver distribuito il modello, la soluzione per il monitoraggio di Backup di Azure verrà visualizzata nell'area di riepilogo dell'area di lavoro. Per andare al riepilogo, attenersi a uno di questi percorsi:

- **Monitoraggio di Azure**: Nel **Insights** sezione, selezionare **ulteriori** e quindi scegliere l'area di lavoro pertinente.
- **Registrare le aree di lavoro di Analitica**: Selezionare l'area di lavoro pertinente, quindi nella sezione **generali**, selezionare **riepilogo dell'area di lavoro**.

![Il riquadro di monitoraggio Log Analitica](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

Quando si seleziona il riquadro monitoraggio, il modello di progettazione si apre una serie di grafici sui dati di monitoraggio di base da Backup di Azure. Ecco alcuni dei grafici che si vedrà:

* Tutti i processi di backup

   ![Log Analitica grafici per i processi di backup](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

* Processi di ripristino

   ![Grafico del log Analitica per i processi di ripristino](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

* Avvisi di Backup di Azure incorporati per le risorse di Azure

   ![Grafico del log Analitica per gli avvisi di Backup di Azure predefiniti per le risorse di Azure](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

* Avvisi di Backup di Azure incorporati per le risorse locali

   ![Grafico del log Analitica per gli avvisi di Backup di Azure predefiniti per le risorse locali](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

* Origini dati attive

   ![Grafico del log Analitica per le entità del modello sottoposto a backup](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

* Servizi di ripristino insieme di credenziali di archiviazione cloud

   ![Grafico del log Analitica per l'archiviazione cloud insieme di credenziali di servizi di ripristino](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

Questi grafici sono forniti con il modello. È possibile modificare i grafici o se è necessario aggiungere più grafici.

> [!IMPORTANT]
> Quando si distribuisce il modello, si crea essenzialmente un blocco di sola lettura. Per modificare e salvare il modello, è necessario rimuovere il blocco. È possibile rimuovere un blocco nel **le impostazioni** sezione dell'area di lavoro di Log Analitica via le **blocca** riquadro.

### <a name="create-alerts-by-using-log-analytics"></a>Creare avvisi tramite Log Analitica

In Monitoraggio di Azure, è possibile creare avvisi in un'area di lavoro di Log Analitica. Nell'area di lavoro, si utilizza *gruppi di azioni di Azure* per selezionare il meccanismo di notifica preferito. 

> [!IMPORTANT]
> Per informazioni sui costi di creazione di questa query, vedere [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

Selezionare uno dei grafici per aprire la **registri** sezione dell'area di lavoro di Log Analitica. Nel **registri** sezione, modificare le query e creare avvisi su di essi.

![Creare un avviso in un'area di lavoro di Log Analitica](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Quando si seleziona **nuova regola di avviso**, viene visualizzata la pagina di creazione dell'avviso di monitoraggio di Azure, come illustrato nell'immagine seguente. In questo caso la risorsa è già contrassegnata come area di lavoro di Log Analitica e viene offerta l'integrazione di gruppo di azione.

![La pagina di creazione dell'avviso di Log Analitica](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Condizione di avviso

La caratteristica distintiva di un avviso è la condizione di attivazione del trigger. Selezionare **condizione** caricherà automaticamente la query Kusto sulle **log** pagina come illustrato nell'immagine seguente. Qui è possibile modificare la condizione in base alle esigenze. Per altre informazioni, vedere [query Kusto esempio](#sample-kusto-queries).

![Configurazione di una condizione di avviso](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Se necessario, è possibile modificare la query Kusto. Scegliere una soglia, periodo e frequenza. La soglia determina quando verrà generato l'avviso. Il periodo è il periodo di tempo in cui viene eseguita la query. Ad esempio, se la soglia è superiore a 0, il periodo è 5 minuti e la frequenza è 5 minuti, quindi la regola viene eseguita la query ogni 5 minuti, esaminare i precedenti 5 minuti. Se il numero di risultati è maggiore di 0, si riceverà una notifica tramite il gruppo di azione selezionata.

#### <a name="alert-action-groups"></a>Gruppi di azione di avviso

Usare un gruppo di azioni per specificare un canale di notifica. Per visualizzare i meccanismi di notifica disponibili, in **gruppi di azioni**, selezionare **Crea nuovo**.

![Meccanismi di notifica disponibili nella finestra "Aggiungi gruppo di azione"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

È possibile soddisfare tutti avvisi e monitoraggio dei requisiti da Log Analitica autonomo oppure è possibile usare Log Analitica per integrare le notifiche incorporate.

Per altre informazioni, vedere [crea, visualizzare e gestire gli avvisi del log tramite Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) e [creare e gestire gruppi di azione nel portale di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Esempi di query Kusto

I grafici predefiniti forniscono query Kusto per scenari di base su cui è possibile creare avvisi. È anche possibile modificare le query per ottenere i dati che si desidera essere avvisati. Incollare la query Kusto di esempio seguenti nella **registri** pagina e quindi creare avvisi per le query:

* Tutti i processi di backup ha esito positivo

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````
    
* Tutti i processi di backup non riusciti

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````
    
* Tutti i processi di backup della macchina virtuale di Azure ha esito positivo

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

* Tutti esito positivo SQL log i processi di backup

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

* Tutti i processi di agente di Backup di Azure ha esito positivo

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

I dati di diagnostica dall'insieme di credenziali vengano eseguito il pump all'area di lavoro di Log Analitica con un ritardo. Ogni evento arriva all'area di lavoro di Log Analitica *20-30 minuti* dopo che viene eseguito il push dall'insieme di credenziali di servizi di ripristino. Ecco ulteriori dettagli relativi al ritardo:

- Tra tutte le soluzioni, vengono inviati avvisi incorporati del servizio backup, non appena vengono create. Pertanto, vengono in genere visualizzati nell'area di lavoro di Log Analitica dopo 20-30 minuti.
- Tra tutte le soluzioni, i processi di backup ad hoc e processi di ripristino vengono eseguito il push appena essi *fine*.
- Per tutte le soluzioni, ad eccezione di backup di SQL, vengono inseriti appena man mano che i processi di backup pianificati *fine*.
- Per il backup SQL, poiché i backup del log possono essere eseguire ogni 15 minuti, le informazioni per tutti il processo pianificato completato backup, inclusi i log, sono in batch e il push di ogni 6 ore.
- In tutte le soluzioni, altre informazioni, ad esempio l'elemento di backup, criteri, i punti di ripristino, archiviazione e così via viene eseguito il push almeno *una volta al giorno.*
- Una modifica nella configurazione del backup (ad esempio, la modifica dei criteri o la modifica dei criteri) attiva un push di tutte le relative informazioni di backup.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Uso del log attività dell'insieme di credenziali di servizi di ripristino

> [!CAUTION]
> I passaggi seguenti si applicano solo a *backup di macchine Virtuali di Azure.* È possibile usare questi passaggi per soluzioni come l'agente di Backup di Azure, i backup di SQL all'interno di Azure o in file di Azure.

È anche possibile utilizzare i log attività di ricevere notifica degli eventi, ad esempio completamento del backup. Per iniziare, seguire questa procedura:

1. Accedere al portale di Azure.
1. Aprire l'insieme di credenziali di servizi di ripristino pertinente. 
1. Nelle proprietà dell'insieme di credenziali, aprire il **log attività** sezione.

Per identificare il log appropriato e creare un avviso:

1. Verificare che si ricevono i log attività per i backup riusciti applicando i filtri visualizzati nell'immagine seguente. Modifica il **Timespan** valore in base alle esigenze per visualizzare i record.

   ![Applicare un filtro per trovare i log attività per i backup di macchine Virtuali di Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Selezionare il nome dell'operazione per visualizzare i dettagli più importanti.
1. Selezionare **nuova regola di avviso** per aprire il **Crea regola** pagina. 
1. Creare un avviso seguendo i passaggi descritti in [crea, visualizzare e gestire gli avvisi del log attività usando monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Nuova regola di avviso](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

In questo caso la risorsa è l'insieme di credenziali di servizi di ripristino se stesso. È necessario ripetere gli stessi passaggi per tutti gli insiemi di credenziali in cui si desidera ricevere una notifica tramite i log attività. La condizione non disporrà di una soglia, il periodo o frequenza perché questo avviso è basato su eventi. Non appena viene generato il log attività rilevanti, viene generato l'avviso.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Uso di Log Analitica per monitorare su larga scala

È possibile visualizzare tutti gli avvisi creati dal log attività e le aree di lavoro di Log Analitica in Monitoraggio di Azure. Aprire il **avvisi** riquadro a sinistra.

Anche se è possibile ricevere notifiche tramite i log attività, è consigliabile usare Log Analitica piuttosto che i log attività per il monitoraggio su larga scala. Ecco perché:

- **Limitato a scenari**: Le notifiche tramite i log attività si applicano solo ai backup di macchine Virtuali di Azure. Le notifiche devono essere impostate per ogni insieme di credenziali di servizi di ripristino.
- **Definizione adatta**: L'attività di backup pianificato non è compatibile con l'ultima definizione del log attività. Al contrario, allineato [log di diagnostica](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-you-can-do-with-diagnostic-logs). Questo allineamento provoca effetti imprevisti quando i dati che passano attraverso l'attività di registrare le modifiche di canale.
- **Problemi con il canale del registro attività**: Negli insiemi di credenziali di servizi di ripristino, i log di attività che vengono eseguito il pump da Backup di Azure seguono un nuovo modello. Sfortunatamente, questa modifica interessa la generazione dei log di attività in Azure per enti pubblici, Azure Germania e Azure Cina 21Vianet. Se gli utenti di questi servizi cloud di creano o configurare gli avvisi dai log attività in Monitoraggio di Azure, non vengono generati avvisi. Inoltre, in tutte le aree pubbliche di Azure, se un utente [raccoglie i log attività di servizi di ripristino in un'area di lavoro di Log Analitica](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), questi log non vengono visualizzati.

Usare un'area di lavoro di Log Analitica per il monitoraggio e avvisi su larga scala per tutti i carichi di lavoro che sono protetti mediante Backup di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per creare query personalizzate, vedere [modello di dati di Log Analitica](backup-azure-log-analytics-data-model.md).
