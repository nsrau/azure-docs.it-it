---
title: Monitorare backup di Azure con monitoraggio di Azure
description: Monitorare i carichi di lavoro di backup di Azure e creare avvisi personalizzati con monitoraggio di Azure.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: bdb59e5ec461288c89e4c7d036488b5eaeb9472a
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74554872"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitorare su larga scala tramite monitoraggio di Azure

Backup di Azure offre [funzionalità di monitoraggio e avviso predefinite](backup-azure-monitoring-built-in-monitor.md) in un insieme di credenziali di servizi di ripristino. Queste funzionalità sono disponibili senza alcuna infrastruttura di gestione aggiuntiva. Questo servizio incorporato è tuttavia limitato negli scenari seguenti:

- Se si esegue il monitoraggio dei dati da più insiemi di credenziali di servizi di ripristino tra sottoscrizioni
- Se il canale di notifica preferito *non* è un messaggio di posta elettronica
- Se gli utenti desiderano avvisi per altri scenari
- Per visualizzare le informazioni di un componente locale, ad esempio System Center Data Protection Manager in Azure, che il portale non visualizza nei [**processi di backup**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) o negli [**avvisi di backup**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Uso di Log Analytics area di lavoro

> [!NOTE]
> I dati dei backup delle macchine virtuali di Azure, dell'agente di backup di Azure, di System Center Data Protection Manager, dei backup SQL in macchine virtuali di Azure e dei backup di condivisione File di Azure vengono pompati nell'area di lavoro Log Analytics tramite le impostazioni di diagnostica. Il supporto per Backup di Microsoft Azure Server (MAB) verrà aggiunto a breve

Per monitorare/creare report su larga scala, è necessario disporre delle funzionalità di due servizi di Azure. *Le impostazioni di diagnostica* inviano dati da più risorse Azure Resource Manager a un'altra risorsa. *Log Analytics* genera avvisi personalizzati in cui è possibile usare i gruppi di azioni per definire altri canali di notifica.

Le sezioni seguenti illustrano in dettaglio come usare Log Analytics per monitorare backup di Azure su larga scala.

### <a name="configure-diagnostic-settings"></a>Configurare le impostazioni di diagnostica

Azure Resource Manager risorse, ad esempio l'insieme di credenziali di servizi di ripristino, registrare le informazioni sulle operazioni pianificate e le operazioni attivate dall'utente come dati di diagnostica.

Nella sezione monitoraggio selezionare impostazioni di **diagnostica** e specificare la destinazione per i dati di diagnostica dell'insieme di credenziali di servizi di ripristino.

![Impostazione di diagnostica dell'insieme di credenziali di servizi di ripristino, destinazione Log Analytics](media/backup-azure-monitoring-laworkspace/diagnostic-setting-new.png)

È possibile scegliere come destinazione un'area di lavoro Log Analytics da un'altra sottoscrizione. Per monitorare gli insiemi di credenziali tra le sottoscrizioni in un'unica posizione, selezionare la stessa area di lavoro Log Analytics per più insiemi di credenziali dei servizi di ripristino. Per eseguire il channeling di tutte le informazioni correlate a backup di Azure nell'area di lavoro Log Analytics, scegliere **risorsa specifica** nell'interruttore visualizzato e selezionare gli eventi seguenti: **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage**, **AddonAzureBackupProtectedInstance**. Vedere [questo articolo](backup-azure-diagnostic-events.md) per altre informazioni sulla configurazione delle impostazioni di diagnostica la.

> [!IMPORTANT]
> Al termine della configurazione, è necessario attendere 24 ore per il completamento del push di dati iniziale. Dopo il push iniziale dei dati, viene eseguito il push di tutti gli eventi, come descritto più avanti in questo articolo, nella [sezione frequenza](#diagnostic-data-update-frequency).

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Distribuire una soluzione nell'area di lavoro Log Analytics

> [!IMPORTANT]
> In backup di Azure è stato rilasciato un [modello](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) aggiornato a più visualizzazioni per il monitoraggio e la creazione di report basati su. Si noti che gli utenti che usavano la [soluzione precedente](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) continueranno a visualizzarlo nelle aree di lavoro anche dopo aver distribuito la nuova soluzione. Tuttavia, la soluzione precedente può fornire risultati non accurati a causa di alcune modifiche minime dello schema. Gli utenti devono quindi distribuire il nuovo modello.

Quando i dati si trova all'interno dell'area di lavoro Log Analytics, [distribuire un modello GitHub](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) in log Analytics per visualizzare i dati. Per identificare correttamente l'area di lavoro, assicurarsi di assegnarle lo stesso gruppo di risorse, il nome dell'area di lavoro e il percorso dell'area di lavoro. Installare quindi questo modello nell'area di lavoro.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Visualizzare i dati di backup di Azure tramite Log Analytics

Dopo la distribuzione del modello, la soluzione per il monitoraggio e la creazione di report in backup di Azure verrà visualizzata nell'area di riepilogo dell'area di lavoro. Per passare al riepilogo, seguire uno dei percorsi seguenti:

- **Monitoraggio di Azure**: nella sezione **Insights** Selezionare **altro** e quindi scegliere l'area di lavoro pertinente.
- **Aree**di lavoro log Analytics: selezionare l'area di lavoro pertinente e quindi in **generale**Selezionare **Riepilogo area**di lavoro.

![I riquadri di monitoraggio e report Log Analytics](media/backup-azure-monitoring-laworkspace/la-azurebackup-overview-dashboard.png)

Quando si seleziona uno dei riquadri panoramica, è possibile visualizzare altre informazioni. Ecco alcuni dei report che verranno visualizzati:

- Processi di backup non log

   ![Log Analytics grafici per i processi di backup](media/backup-azure-monitoring-laworkspace/la-azurebackup-backupjobsnonlog.png)

- Avvisi dal backup delle risorse di Azure

   ![Grafico Log Analytics per i processi di ripristino](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertsazure.png)

Analogamente, facendo clic sugli altri riquadri, sarà possibile visualizzare i report su processi di ripristino, archiviazione cloud, elementi di backup, avvisi da risorse locali backup e processi di backup del log.

Questi grafici sono forniti con il modello. Se necessario, è possibile modificare i grafici o aggiungere altri grafici.

### <a name="create-alerts-by-using-log-analytics"></a>Creazione di avvisi tramite Log Analytics

In monitoraggio di Azure è possibile creare avvisi personalizzati in un'area di lavoro Log Analytics. Nell'area di lavoro si usano i *gruppi di azioni di Azure* per selezionare il meccanismo di notifica preferito.

> [!IMPORTANT]
> Per informazioni sul costo della creazione di questa query, vedere [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

Selezionare uno dei grafici per aprire la sezione **logs** dell'area di lavoro log Analytics. Nella sezione **logs** modificare le query e creare avvisi su di essi.

![Creare un avviso in un'area di lavoro Log Analytics](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Quando si seleziona **nuova regola di avviso**, viene visualizzata la pagina di creazione degli avvisi di monitoraggio di Azure, come illustrato nella figura seguente. Qui la risorsa è già contrassegnata come area di lavoro Log Analytics e viene fornita l'integrazione del gruppo di azioni.

![Pagina Creazione avviso Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Condizione di avviso

La caratteristica di definizione di un avviso è la condizione di attivazione. Selezionare **condizione** per caricare automaticamente la query kusto nella pagina **log** , come illustrato nella figura seguente. Qui è possibile modificare la condizione in base alle esigenze. Per altre informazioni, vedere [esempi di query kusto](#sample-kusto-queries).

![Impostazione di una condizione di avviso](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Se necessario, è possibile modificare la query kusto. Scegliere una soglia, un punto e una frequenza. La soglia determina quando verrà generato l'avviso. Il periodo è l'intervallo di tempo in cui viene eseguita la query. Se, ad esempio, la soglia è maggiore di 0, il periodo è 5 minuti e la frequenza è 5 minuti, la regola esegue la query ogni 5 minuti, esaminando i 5 minuti precedenti. Se il numero di risultati è maggiore di 0, viene inviata una notifica tramite il gruppo di azioni selezionato.

#### <a name="alert-action-groups"></a>Gruppi di azioni di avviso

Usare un gruppo di azioni per specificare un canale di notifica. Per visualizzare i meccanismi di notifica disponibili, in **gruppi di azione**selezionare **Crea nuovo**.

![Meccanismi di notifica disponibili nella finestra "Aggiungi gruppo di azione"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

È possibile soddisfare tutti i requisiti di avviso e monitoraggio solo da Log Analytics oppure è possibile usare Log Analytics per integrare le notifiche predefinite.

Per altre informazioni, vedere [creare, visualizzare e gestire gli avvisi del log usando monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) e [creare e gestire gruppi di azioni nel portale di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Query kusto di esempio

I grafici predefiniti forniscono query kusto per gli scenari di base in cui è possibile compilare avvisi. È anche possibile modificare le query per ottenere i dati per i quali si desidera ricevere avvisi. Incollare le query kusto di esempio seguenti nella pagina **log** , quindi creare avvisi per le query:

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

- Tutti i processi di backup delle VM di Azure riusciti

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

- Tutti i processi dell'agente di backup di Azure riusciti

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

### <a name="diagnostic-data-update-frequency"></a>Frequenza di aggiornamento dei dati di diagnostica

I dati di diagnostica dell'insieme di credenziali vengono pompati nell'area di lavoro Log Analytics con un certo ritardo. Ogni evento arriva nell'area di lavoro Log Analytics da *20 a 30 minuti* dopo il push dall'insieme di credenziali dei servizi di ripristino. Ecco altri dettagli sul ritardo:

- Per tutte le soluzioni, viene eseguito il push degli avvisi predefiniti del servizio di backup non appena vengono creati. Quindi, vengono in genere visualizzati nell'area di lavoro Log Analytics dopo 20 o 30 minuti.
- Per tutte le soluzioni, i processi di backup su richiesta e i processi di ripristino vengono spostati non appena vengono *completati*.
- Per tutte le soluzioni ad eccezione di backup SQL, i processi di backup pianificati vengono inseriti non appena vengono *completati*.
- Per il backup SQL, poiché i backup del log possono essere eseguiti ogni 15 minuti, le informazioni per tutti i processi di backup pianificati completati, inclusi i log, vengono inserite in batch e inserite ogni 6 ore.
- Per tutte le soluzioni, altre informazioni, ad esempio l'elemento di backup, i criteri, i punti di ripristino, l'archiviazione e così via, vengono inserite almeno *una volta al giorno.*
- Una modifica alla configurazione del backup, ad esempio la modifica dei criteri o la modifica dei criteri, attiva un push di tutte le informazioni di backup correlate.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Uso dei log attività dell'insieme di credenziali di servizi di ripristino

> [!CAUTION]
> I passaggi seguenti si applicano solo ai *backup di macchine virtuali di Azure.* Non è possibile usare questi passaggi per soluzioni come l'agente di backup di Azure, i backup SQL in Azure o File di Azure.

È anche possibile usare i log attività per ricevere notifiche per eventi come il backup riuscito. Per iniziare, seguire questa procedura:

1. Accedere al portale di Azure.
1. Aprire l'insieme di credenziali dei servizi di ripristino pertinente.
1. Nelle proprietà dell'insieme di credenziali aprire la sezione **log attività** .

Per identificare il registro appropriato e creare un avviso:

1. Verificare di ricevere i log attività per i backup riusciti applicando i filtri mostrati nell'immagine seguente. Modificare il valore **TimeSpan** come necessario per visualizzare i record.

   ![Filtro per trovare i log attività per i backup di macchine virtuali di Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Selezionare il nome dell'operazione per visualizzare i dettagli pertinenti.
1. Selezionare **nuova regola di avviso** per aprire la pagina **Crea regola** .
1. Per creare un avviso, seguire la procedura descritta in [creare, visualizzare e gestire gli avvisi del log attività tramite monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Nuova regola di avviso](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Qui la risorsa è l'insieme di credenziali dei servizi di ripristino. Ripetere gli stessi passaggi per tutti gli insiemi di credenziali in cui si desidera ricevere una notifica tramite i log attività. La condizione non avrà una soglia, un punto o una frequenza perché questo avviso è basato sugli eventi. Non appena viene generato il log attività pertinente, viene generato l'avviso.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Uso di Log Analytics per il monitoraggio su larga scala

È possibile visualizzare tutti gli avvisi creati dai log attività e dalle aree di lavoro Log Analytics in monitoraggio di Azure. È sufficiente aprire il riquadro **avvisi** a sinistra.

Sebbene sia possibile ottenere le notifiche tramite i log attività, è consigliabile usare Log Analytics anziché i log attività per il monitoraggio su larga scala. Ecco perché:

- **Scenari limitati**: le notifiche tramite i log attività si applicano solo ai backup di macchine virtuali di Azure. Le notifiche devono essere impostate per ogni insieme di credenziali di servizi di ripristino.
- **Adattamento**per la definizione: l'attività di backup pianificata non rientra nella definizione più recente dei log attività. Viene invece allineato con i [log delle risorse](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-resource-logs-in-a-workspace). Questo allineamento provoca effetti imprevisti quando vengono modificati i dati che passano attraverso il canale del log attività.
- **Problemi con il canale del log attività**: negli insiemi di credenziali dei servizi di ripristino i log attività che vengono pompati da backup di Azure seguono un nuovo modello. Sfortunatamente, questa modifica influiscono sulla generazione dei log attività in Azure per enti pubblici, Azure Germania e Azure Cina 21Vianet. Se gli utenti di questi servizi cloud creano o configurano avvisi dai log attività in monitoraggio di Azure, gli avvisi non vengono attivati. Inoltre, in tutte le aree pubbliche di Azure, se un utente [raccoglie i log attività di servizi di ripristino in un'area di lavoro log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), questi log non vengono visualizzati.

Usare un'area di lavoro Log Analytics per il monitoraggio e l'invio di avvisi su larga scala per tutti i carichi di lavoro protetti da backup di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per creare query personalizzate, vedere [log Analytics modello di dati](backup-azure-log-analytics-data-model.md).
