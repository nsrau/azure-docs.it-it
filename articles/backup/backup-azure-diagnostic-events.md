---
title: Utilizzare le impostazioni di diagnostica per gli archivi di Servizi di ripristino
description: Questo articolo descrive come usare gli eventi di diagnostica vecchi e nuovi per Backup di Azure.This article describes how to use the old and new diagnostics events for Azure Backup.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: f11c9d2a5b48b9cd27ac6e6f8a00eb5ac0ac7a9d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617303"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Utilizzare le impostazioni di diagnostica per gli archivi di Servizi di ripristino

Backup di Azure invia eventi di diagnostica che possono essere raccolti e usati ai fini dell'analisi, degli avvisi e della creazione di report.

È possibile configurare le impostazioni di diagnostica per un insieme di credenziali di Servizi di ripristino tramite il portale di Azure accedendo all'insieme di credenziali e selezionando **Impostazioni di diagnostica**. La selezione **di Aggiungi impostazione diagnostica** consente di inviare uno o più eventi di diagnostica a un account di archiviazione, a un hub eventi o a un'area di lavoro di Log Analytics.Selecting : Add Diagnostic Setting lets you send one or more diagnostic events to a storage account, an event hub, or a Log Analytics workspace.

![Riquadro Impostazioni di diagnostica](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Eventi di diagnostica disponibili per gli utenti di Backup di AzureDiagnostics events available for Azure Backup users

Backup di Azure offre gli eventi di diagnostica seguenti. Ogni evento fornisce dati dettagliati su un set specifico di elementi correlati al backup:Each event provides detailed data on a specific set of backup-related artifacts:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstanceAddonAzureBackupProtectedInstance
* AddonAzureBackupJobsAddonAzureBackupJobs
* AddonAzureBackupPolicyAddonAzureBackupPolicy
* AddonAzureBackupStorageAddonAzureBackupStorage

Per altre informazioni, vedere Modello di dati per gli eventi di diagnostica di Backup di Azure.For more information, see [Data model for Azure Backup diagnostics events.](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

I dati per questi eventi possono essere inviati a un account di archiviazione, a un'area di lavoro di Log Analytics o a un hub eventi. Se si inviano questi dati a un'area di lavoro di Log Analytics, selezionare l'interruttore **Specifico della risorsa** nella schermata Impostazioni di **diagnostica.** Per altre informazioni, vedere le sezioni seguenti.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Usare le impostazioni di diagnostica con Log AnalyticsUse diagnostics settings with Log Analytics

È ora possibile usare Backup di Azure per inviare dati di diagnostica dell'insieme di credenziali alle tabelle di Log Analytics dedicate per il backup. Queste tabelle sono denominate [tabelle specifiche delle risorse.](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)

Per inviare i dati di diagnostica del vault a Log Analytics:

1. Accedere al vault e selezionare **Impostazioni di diagnostica**. Selezionare **: Aggiungi impostazione diagnostica**.
1. Assegnare un nome all'impostazione di diagnostica.
1. Selezionare la casella di controllo **Invia a Log Analytics** e selezionare un'area di lavoro di Log Analytics.
1. Selezionare **Specifico della** risorsa nell'interruttore e selezionare i sei eventi seguenti: **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage**e **AddonAzureBackupProtectedInstance**.
1. Selezionare **Salva**.

   ![Modalità specifica della risorsa](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Dopo il flusso dei dati nell'area di lavoro di Log Analytics, nell'area di lavoro vengono create tabelle dedicate per ognuno di questi eventi. È possibile eseguire query direttamente su una di queste tabelle. Se necessario, è anche possibile eseguire join o unioni tra queste tabelle.

> [!IMPORTANT]
> I sei eventi, ovvero CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolicy, AddonAzureBackupStorage e AddonAzureBackupProtectedInstance, sono supportati *solo* nella modalità specifica della risorsa nei report di [backup.](https://docs.microsoft.com/azure/backup/configure-reports) *Se si tenta di inviare dati per questi sei eventi in modalità di diagnostica di Azure, nessun dato sarà visibile nei rapporti di backup.*

## <a name="legacy-event"></a>Evento legacy

Tradizionalmente, tutti i dati di diagnostica correlati al backup per un insieme di credenziali erano contenuti in un singolo evento denominato AzureBackupReport.Tradizionalmente, all backup-related diagnostics data for a vault was contained in a single event called AzureBackupReport. The six events described here are, in essence, a decomposition of all the data contained in AzureBackupReport. 

Attualmente, continuiamo a supportare l'evento AzureBackupReport per la compatibilità con le versioni precedenti nei casi in cui gli utenti hanno query personalizzate esistenti su questo evento. Esempi sono gli avvisi di log personalizzati e le visualizzazioni personalizzate. Si consiglia di *passare ai [nuovi eventi](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users) il più presto possibile*. I nuovi eventi:

- Rendere i dati molto più facili da usare nelle query di log.
- Fornire una migliore individuabilità degli schemi e della relativa struttura.
- Migliorare le prestazioni sia per la latenza di inserimento che per i tempi di query. 

*L'evento legacy in modalità di diagnostica di Azure sarà deprecato. La scelta dei nuovi eventi potrebbe essere utile per evitare migrazioni complesse in un secondo momento.* La [nostra soluzione di reporting](https://docs.microsoft.com/azure/backup/configure-reports) che usa Log Analytics interromperà anche il supporto dei dati dell'evento legacy.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Passaggi per passare a nuove impostazioni di diagnostica per un'area di lavoro di Log AnalyticsSteps to move to new diagnostics settings for a Log Analytics workspace

1. Identificare gli insiemi di credenziali che inviano i dati alle aree di lavoro di Log Analytics utilizzando l'evento legacy e le sottoscrizioni a cui appartengono. Eseguire le aree di lavoro seguenti per identificare questi insiemi di credenziali e sottoscrizioni.

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId    
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault" 
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, hence you need to use isFuzzy
    let CombinedVaultTable = (){
        CombinedTable | union isfuzzy = true 
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti ( 
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

1. Usare i criteri predefiniti di [Azure in](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) Backup di Azure per aggiungere una nuova impostazione di diagnostica per tutti gli insiemi di credenziali in un ambito specificato. Questo criterio aggiunge una nuova impostazione di diagnostica agli insiemi di credenziali che non dispongono di un'impostazione di diagnostica o che non dispongono solo di un'impostazione di diagnostica legacy. Questo criterio può essere assegnato a un'intera sottoscrizione o gruppo di risorse alla volta. È necessario disporre dell'accesso Proprietario a ogni sottoscrizione per cui è assegnato il criterio.

È possibile scegliere di avere impostazioni di diagnostica separate per AzureBackupReport e i sei nuovi eventi fino a quando non è stata eseguita la migrazione di tutte le query personalizzate per usare i dati delle nuove tabelle. L'immagine seguente mostra un esempio di un vault con due impostazioni di diagnostica. La prima impostazione, denominata Setting1 , invia i dati di un evento AzureBackupReport a un'area di lavoro di Log Analytics in modalità di diagnostica di Azure.The first setting, named **Setting1**, sends data of an AzureBackupReport event to a Log Analytics workspace in Azure diagnostics mode. La seconda impostazione, denominata **Setting2**, invia i dati dei sei nuovi eventi di Backup di Azure a un'area di lavoro di Log Analytics in modalità specifica della risorsa.

![Due impostazioni](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> L'evento AzureBackupReport è supportato solo in modalità di diagnostica di Azure.The AzureBackupReport event is supported *only* in Azure diagnostics mode. *Se si tenta di inviare dati per questo evento in modalità specifica della risorsa, nessun dato verrà inviato all'area di lavoro di Log Analytics.*

> [!NOTE]
> L'interruttore per **la diagnostica** di Azure o specifico **della risorsa** viene visualizzato solo se l'utente seleziona Invia a **Log Analytics**. Per inviare dati a un account di archiviazione o a un hub eventi, un utente seleziona la destinazione richiesta e seleziona le caselle di controllo per gli eventi desiderati, senza input aggiuntivi. Anche in questo caso, è consigliabile non scegliere l'evento legacy AzureBackupReport in futuro.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Inviare eventi di Ripristino siti di Azure a Log AnalyticsSend Azure Site Recovery events to Log Analytics

Gli eventi di Backup di Azure e Ripristino sito di Azure vengono inviati dallo stesso insieme di credenziali di Servizi di ripristino. Azure Site Recovery non è attualmente disponibile per le tabelle specifiche delle risorse. Gli utenti che desiderano inviare eventi di Ripristino siti di Azure a Log Analytics devono usare *solo*la modalità di diagnostica di Azure, come illustrato nell'immagine. La scelta della modalità specifica della risorsa per gli eventi di Ripristino siti di *Azure impedirà l'invio dei dati necessari all'area di lavoro di Log Analytics*.

![Eventi di ripristino del sito](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Per riepilogare:

* Se la diagnostica di Log Analytics è già stata configurata con Diagnostica di Azure e sono state scritte query personalizzate, mantenere *intatta* tale impostazione fino a quando non si esegue la migrazione delle query per usare i dati dei nuovi eventi.
* Se si desidera eseguire anche l'onboarding in nuove tabelle, come è consigliabile creare una **nuova** impostazione di diagnostica, selezionare **Specifico risorsa**e selezionare i sei nuovi eventi.
* Se si inviano attualmente eventi di Ripristino siti di Azure a Log Analytics, *non* scegliere la modalità specifica della risorsa per questi eventi. In caso contrario, i dati per questi eventi non verranno inseriti nell'area di lavoro di Log Analytics.Otherwise, data for these events won't flow into your Log Analytics workspace. Creare invece un'impostazione di diagnostica aggiuntiva, selezionare **Diagnostica di Azure**e selezionare gli eventi di Ripristino siti di Azure pertinenti.

L'immagine seguente mostra un esempio di un utente che dispone di tre impostazioni di diagnostica per un vault. La prima impostazione, denominata Setting1 , invia i dati da un evento AzureBackupReport a un'area di lavoro di Log Analytics in modalità di diagnostica di Azure.The first setting, named **Setting1**, sends data from an AzureBackupReport event to a Log Analytics workspace in Azure diagnostics mode. La seconda impostazione, denominata **Setting2**, invia i dati dei sei nuovi eventi di Backup di Azure a un'area di lavoro di Log Analytics in modalità specifica della risorsa. La terza impostazione, denominata Setting3 , invia i dati dagli eventi di Azure Site Recovery a un'area di lavoro di Log Analytics in modalità di diagnostica di Azure.The third setting, named **Setting3**, sends data from the Azure Site Recovery events to a Log Analytics workspace in Azure diagnostics mode.

![Tre impostazioni](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sul modello di dati di Log Analytics per gli eventi di diagnosticaLearn the Log Analytics data model for the diagnostics events](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
