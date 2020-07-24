---
title: Eseguire il monitoraggio di Backup di Azure con Monitoraggio di Azure
description: Eseguire il monitoraggio dei carichi di lavoro di Backup di Azure e creare avvisi personalizzati con Monitoraggio di Azure.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: fbd1c7f5e7fab9f77815e782160e855a9a854dc9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054613"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Eseguire il monitoraggio su larga scala tramite Monitoraggio di Azure

Backup di Azure offre [funzionalità di monitoraggio e avviso predefinite](backup-azure-monitoring-built-in-monitor.md) in un insieme di credenziali di Servizi di ripristino. Queste funzionalità sono disponibili senza alcuna infrastruttura di gestione aggiuntiva. Questo servizio predefinito è tuttavia limitato negli scenari seguenti:

- Se si esegue il monitoraggio dei dati da più insiemi di credenziali di Servizi di ripristino in più sottoscrizioni
- Se il canale di notifica preferito *non* è la posta elettronica
- Se gli utenti vogliono ricevere avvisi per più scenari
- Se si vogliono visualizzare informazioni di un componente locale, ad esempio System Center Data Protection Manager in Azure, che il portale non visualizza in [**Processi di backup**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) o [**Avvisi di backup**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Uso di un'area di lavoro Log Analytics

### <a name="create-alerts-by-using-log-analytics"></a>Creare avvisi tramite Log Analytics

In Monitoraggio di Azure è possibile creare avvisi personalizzati in un'area di lavoro Log Analytics. Nell'area di lavoro si usano *gruppi di azioni di Azure* per selezionare il meccanismo di notifica preferito.

> [!IMPORTANT]
> Per informazioni sul costo della creazione di questa query, vedere [Prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

Aprire la sezione **Log** dell'area di lavoro Log Analytics e creare una query per i propri log. Quando si seleziona **Nuova regola di avviso**, viene visualizzata la pagina di creazione degli avvisi di Monitoraggio di Azure, come illustrato nella figura seguente.

![Creare un avviso in un'area di lavoro Log Analytics](media/backup-azure-monitoring-laworkspace/custom-alert.png)

Qui la risorsa è già contrassegnata come area di lavoro Log Analytics ed è disponibile l'integrazione del gruppo di azioni.

![Pagina di creazione degli avvisi di Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Condizione di avviso

La caratteristica che definisce un avviso è la condizione di attivazione dell'avviso stesso. Selezionare **Condizione** per caricare automaticamente la query Kusto nella pagina **Log**, come illustrato nella figura seguente. Qui è possibile modificare la condizione in base alle esigenze. Per altre informazioni, vedere [Query Kusto di esempio](#sample-kusto-queries).

![Impostazione di una condizione di avviso](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Se necessario, è possibile modificare la query Kusto. Scegliere una soglia, un periodo e una frequenza. La soglia determina quando verrà generato l'avviso. Il periodo è l'intervallo di tempo in cui viene eseguita la query. Se, ad esempio, la soglia è Maggiore di 0, il periodo è 5 minuti e la frequenza è 5 minuti, la regola esegue la query ogni 5 minuti, esaminando i 5 minuti precedenti. Se il numero di risultati è maggiore di 0, si riceve una notifica attraverso il gruppo di azioni selezionato.

> [!NOTE]
> Per eseguire la regola di avviso una volta al giorno, in tutti gli eventi e i log creati nel giorno specificato modificare il valore di 'Periodo' e 'Frequenza' in 1440, ovvero 24 ore.

#### <a name="alert-action-groups"></a>Gruppi di azioni di avviso

Usare un gruppo di azioni per specificare un canale di notifica. Per visualizzare i meccanismi di notifica disponibili, in **Gruppi di azioni** selezionare **Crea nuovo**.

![Meccanismi di notifica disponibili nella finestra "Aggiungi gruppo di azioni"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

È possibile soddisfare tutti i requisiti di avviso e monitoraggio solo da Log Analytics oppure è possibile usare Log Analytics per integrare le notifiche predefinite.

Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi dei log tramite Monitoraggio di Azure](../azure-monitor/platform/alerts-log.md) e [Creare e gestire gruppi di azioni nel portale di Azure](../azure-monitor/platform/action-groups.md).

### <a name="sample-kusto-queries"></a>Query Kusto di esempio

I grafi predefiniti mettono a disposizione query Kusto per gli scenari di base in cui è possibile creare avvisi. È anche possibile modificare le query per ottenere i dati per i quali si vogliono ricevere avvisi. Incollare le query Kusto di esempio seguenti nella pagina **Log** e quindi creare avvisi per le query:

- Tutti i processi di backup completati

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    ````

- Tutti i processi di backup non riusciti

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Failed"
    ````

- Tutti i processi di backup di macchine virtuali di Azure completati

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
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

- Tutti i processi di backup di log SQL completati

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
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

- Tutti i processi dell'agente di Backup di Azure completati

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
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

- Spazio di archiviazione di backup utilizzato per ogni elemento di backup

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

Il pump dei dati di diagnostica dall'insieme di credenziali nell'area di lavoro Log Analytics viene eseguito con un certo ritardo. Ogni evento arriva nell'area di lavoro Log Analytics *da 20 a 30 minuti* dopo il push dall'insieme di credenziali di Servizi di ripristino. Ecco altri dettagli sul ritardo:

- Per tutte le soluzioni, il push degli avvisi predefiniti del servizio di backup viene eseguito non appena gli avvisi vengono creati. Questi pertanto vengono in genere visualizzati nell'area di lavoro Log Analytics dopo 20-30 minuti.
- Per tutte le soluzioni, il push dei processi di backup e ripristino su richiesta viene eseguito non appena questi processi *vengono completati*.
- Per tutte le soluzioni ad eccezione del backup SQL, il push dei processi di backup pianificati viene eseguito non appena questi processi *vengono completati*.
- Per il backup SQL, poiché i backup dei log possono essere eseguiti ogni 15 minuti, le informazioni per tutti i processi di backup pianificati completati, inclusi i log, vengono riunite in batch e il push dei batch viene eseguito ogni 6 ore.
- Per tutte le soluzioni, il push di altre informazioni, ad esempio l'elemento di backup, i criteri, i punti di ripristino, la risorsa di archiviazione e così via, viene eseguito almeno *una volta al giorno.*
- Una modifica alla configurazione di backup, ad esempio la sostituzione o la modifica dei criteri, attiva il push di tutte le informazioni di backup correlate.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Uso dei log attività dell'insieme di credenziali di Servizi di ripristino

> [!CAUTION]
> La procedura seguente si applica solo al backup di *macchine virtuali di Azure.* Non è possibile usare questa procedura per soluzioni quali l'agente di Backup di Azure, i backup SQL in Azure o File di Azure.

È anche possibile usare i log attività per ricevere notifiche relative agli eventi, ad esempio la corretta esecuzione del backup. Per iniziare, seguire questa procedura:

1. Accedere al portale di Azure.
2. Aprire l'insieme di credenziali di Servizi di ripristino pertinente.
3. Nelle proprietà dell'insieme di credenziali aprire la sezione **Log attività**.

Per identificare il log appropriato e creare un avviso:

1. Verificare se si ricevono log attività per i backup riusciti applicando i filtri visualizzati nella figura seguente. Cambiare il valore di **Intervallo di tempo** come necessario per visualizzare i record.

   ![Filtro per trovare i log attività per i backup di macchine virtuali di Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

2. Selezionare il nome dell'operazione per visualizzare i dettagli pertinenti.
3. Selezionare **Nuova regola di avviso** per aprire la pagina **Crea regola**.
4. Per creare un avviso, seguire la procedura descritta in [Creare, visualizzare e gestire gli avvisi del log attività usando Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md).

   ![Nuova regola di avviso](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Qui la risorsa è l'insieme di credenziali di Servizi di ripristino stesso. Ripetere la stessa procedura per tutti gli insiemi di credenziali per i quali si vuole ricevere una notifica attraverso i log attività. La condizione non avrà una soglia, un periodo o una frequenza, perché questo avviso si basa su eventi. Non appena viene generato il log attività pertinente, viene emesso l'avviso.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Uso di Log Analytics per eseguire il monitoraggio su larga scala

È possibile visualizzare tutti gli avvisi creati a partire da log attività e da aree di lavoro Log Analytics in Monitoraggio di Azure. È sufficiente aprire il riquadro **Avvisi** a sinistra.

Anche se è possibile ricevere notifiche tramite i log attività, per il monitoraggio su larga scala è consigliabile usare Log Analytics anziché i log attività. Ecco perché:

- **Scenari con limitazioni**: le notifiche tramite i log attività si applicano solo ai backup di macchine virtuali di Azure. È necessario configurare le notifiche per ogni insieme di credenziali di Servizi di ripristino.
- **Corrispondenza con la definizione**: l'attività di backup pianificata non rientra nella definizione più recente dei log attività, mentre è allineata con i [log delle risorse](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace). Questo allineamento causa effetti imprevisti quando i dati che passano attraverso il canale dei log attività cambiano.
- **Problemi con il canale dei log attività**: negli insiemi di credenziali di Servizi di ripristino i log attività di cui viene eseguito il pump da Backup di Azure seguono un nuovo modello. Questa modifica purtroppo influisce sulla generazione dei log attività in Azure per enti pubblici, Azure Germania e Azure Cina (21Vianet). Se gli utenti di questi servizi cloud creano o configurano avvisi da log attività in Monitoraggio di Azure, gli avvisi non vengono attivati. In tutte le aree pubbliche di Azure, poi, se un utente [raccoglie i log attività di Servizi di ripristino in un'area di lavoro Log Analytics](../azure-monitor/platform/activity-log.md), questi log non vengono visualizzati.

Usare un'area di lavoro Log Analytics per il monitoraggio e l'invio di avvisi su larga scala per tutti i carichi di lavoro protetti da Backup di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per creare query personalizzate, vedere [Modello di dati di Log Analytics](backup-azure-reports-data-model.md).
