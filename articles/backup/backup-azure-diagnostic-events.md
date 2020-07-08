---
title: Usare le impostazioni di diagnostica per gli insiemi di credenziali dei servizi di ripristino
description: Questo articolo descrive come usare i vecchi e i nuovi eventi di diagnostica per backup di Azure.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: be99b73a4dac12c9e70e4cb8a85f34b97f5c42d7
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85854809"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Usare le impostazioni di diagnostica per gli insiemi di credenziali dei servizi di ripristino

Backup di Azure invia eventi di diagnostica che possono essere raccolti e usati a scopo di analisi, avviso e creazione di report.

È possibile configurare le impostazioni di diagnostica per un insieme di credenziali di servizi di ripristino tramite il portale di Azure passando all'insieme di credenziali e selezionando **impostazioni di diagnostica**. Selezionando **+ Aggiungi impostazioni di diagnostica** è possibile inviare uno o più eventi di diagnostica a un account di archiviazione, un hub eventi o un'area di lavoro log Analytics.

![Riquadro Impostazioni di diagnostica](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Eventi di diagnostica disponibili per gli utenti di backup di Azure

Backup di Azure fornisce gli eventi di diagnostica seguenti. Ogni evento fornisce dati dettagliati su un set specifico di elementi correlati al backup:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

Se si usa l' [evento legacy](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event) AzureBackupReport, si consiglia di passare all'uso degli eventi precedenti al più presto.

Per altre informazioni, vedere [modello di dati per gli eventi di diagnostica di backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model).

I dati relativi a questi eventi possono essere inviati a un account di archiviazione, a un'area di lavoro Log Analytics o a un hub eventi. Se si inviano i dati a un'area di lavoro Log Analytics, selezionare l'interruttore specifico per la **risorsa** nella schermata **impostazioni di diagnostica** . Per altre informazioni, vedere le sezioni seguenti.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Usare le impostazioni di diagnostica con Log Analytics

È ora possibile usare backup di Azure per inviare i dati di diagnostica dell'insieme di credenziali a tabelle Log Analytics dedicate per il backup. Queste tabelle sono denominate [tabelle specifiche delle risorse](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific).

Per inviare i dati di diagnostica dell'insieme di credenziali a Log Analytics:

1. Passare all'insieme di credenziali e selezionare **impostazioni di diagnostica**. Selezionare **+ Aggiungi impostazioni di diagnostica**.
1. Assegnare un nome all'impostazione di diagnostica.
1. Selezionare la casella **di controllo Invia a log Analytics** e selezionare un'area di lavoro log Analytics.
1. Selezionare **risorsa specifica** nell'interruttore e selezionare i sei eventi seguenti: **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage**e **AddonAzureBackupProtectedInstance**.
1. Selezionare **Salva**.

   ![Modalità specifica della risorsa](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Al termine del flusso di dati nell'area di lavoro Log Analytics, le tabelle dedicate per ognuno di questi eventi vengono create nell'area di lavoro. È possibile eseguire una query direttamente su una di queste tabelle. Se necessario, è anche possibile eseguire join o unioni tra queste tabelle.

> [!IMPORTANT]
> I sei eventi, ovvero CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolicy, AddonAzureBackupStorage e AddonAzureBackupProtectedInstance, sono supportati *solo* nella modalità specifica della risorsa nei [report di backup](https://docs.microsoft.com/azure/backup/configure-reports). *Se si tenta di inviare i dati per questi sei eventi in modalità diagnostica di Azure, nessun dato sarà visibile nei report di backup.*

## <a name="legacy-event"></a>Evento legacy

Tradizionalmente, tutti i dati di diagnostica correlati al backup per un insieme di credenziali sono contenuti in un singolo evento denominato AzureBackupReport. I sei eventi descritti di seguito sono essenzialmente una scomposizione di tutti i dati contenuti in AzureBackupReport.

Attualmente, continuiamo a supportare l'evento AzureBackupReport per la compatibilità con le versioni precedenti nei casi in cui gli utenti hanno query personalizzate esistenti su questo evento. Gli esempi sono gli avvisi del log personalizzati e le visualizzazioni personalizzate. Si *consiglia di passare ai [nuovi eventi](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users) il prima possibile*. Nuovi eventi:

* Rendere i dati più semplici da usare nelle query di log.
* Fornire una migliore individuabilità degli schemi e della relativa struttura.
* Migliorare le prestazioni tra la latenza di inserimento e i tempi di esecuzione delle query.

*La fine dell'evento legacy in modalità diagnostica di Azure verrà deprecata. La scelta dei nuovi eventi può essere utile per evitare migrazioni complesse in un secondo*momento. La [soluzione](https://docs.microsoft.com/azure/backup/configure-reports) per la creazione di report che usa log Analytics interrompe anche il supporto dei dati dell'evento legacy.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Passaggi per passare alle nuove impostazioni di diagnostica per un'area di lavoro Log Analytics

1. Identificare gli insiemi di credenziali che inviano dati alle aree di lavoro Log Analytics usando l'evento legacy e le sottoscrizioni a cui appartengono. Eseguire le aree di lavoro seguenti per identificare gli insiemi di credenziali e le sottoscrizioni.

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

1. Usare le [definizioni di criteri di Azure predefinite](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) in backup di Azure per aggiungere una nuova impostazione di diagnostica per tutti gli insiemi di credenziali in un ambito specifico. Questo criterio aggiunge una nuova impostazione di diagnostica agli insiemi di credenziali che non hanno un'impostazione di diagnostica o hanno solo un'impostazione di diagnostica legacy. Questo criterio può essere assegnato a un'intera sottoscrizione o a un gruppo di risorse alla volta. È necessario disporre dell'accesso proprietario a ogni sottoscrizione per cui sono assegnati i criteri.

È possibile scegliere di disporre di impostazioni di diagnostica separate per AzureBackupReport e dei sei nuovi eventi fino a quando non è stata eseguita la migrazione di tutte le query personalizzate per utilizzare i dati delle nuove tabelle. La figura seguente mostra un esempio di insieme di credenziali con due impostazioni di diagnostica. La prima impostazione, denominata **Setting1**, invia i dati di un evento AzureBackupReport a un'area di lavoro log Analytics in modalità diagnostica di Azure. La seconda impostazione, denominata **Setting2**, invia i dati dei sei nuovi eventi di backup di Azure a un'area di lavoro log Analytics nella modalità specifica della risorsa.

![Due impostazioni](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> L'evento AzureBackupReport è supportato *solo* in modalità diagnostica di Azure. *Se si tenta di inviare dati per questo evento nella modalità specifica della risorsa, non verrà eseguito il flusso di dati nell'area di lavoro Log Analytics.*

> [!NOTE]
> L'interruttore per **diagnostica di Azure** o **risorsa specifica** viene visualizzato solo se l'utente seleziona **Invia a log Analytics**. Per inviare dati a un account di archiviazione o a un hub eventi, un utente seleziona la destinazione richiesta e seleziona le caselle di controllo per gli eventi desiderati, senza input aggiuntivi. Anche in questo caso, si consiglia di non scegliere il AzureBackupReport dell'evento legacy in futuro.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Inviare eventi Azure Site Recovery al Log Analytics

Backup di Azure e gli eventi di Azure Site Recovery vengono inviati dallo stesso insieme di credenziali di servizi di ripristino. Azure Site Recovery non è attualmente disponibile per le tabelle specifiche delle risorse. Gli utenti che desiderano inviare eventi Azure Site Recovery ai Log Analytics vengono indirizzati all'uso *solo*della modalità diagnostica di Azure, come illustrato nell'immagine. Se *si sceglie la modalità specifica della risorsa per gli eventi di Azure Site Recovery, i dati necessari non vengono inviati all'area di lavoro di log Analytics*.

![Eventi Site Recovery](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Per concludere:

* Se è già stata configurata la diagnostica Log Analytics con Diagnostica di Azure e sono state scritte query personalizzate, mantenere l'impostazione *intatta* fino a quando non si esegue la migrazione delle query per usare i dati dei nuovi eventi.
* Se si desidera eseguire l'onboarding anche nelle nuove tabelle, è consigliabile creare una **nuova** impostazione di diagnostica, selezionare specifico per la **risorsa**e selezionare i sei nuovi eventi.
* Se si sta inviando attualmente Azure Site Recovery eventi a Log Analytics, *non* scegliere la modalità specifica della risorsa per questi eventi. In caso contrario, i dati per questi eventi non verranno inseriti nell'area di lavoro Log Analytics. In alternativa, creare un'impostazione di diagnostica aggiuntiva, selezionare **diagnostica di Azure**e selezionare gli eventi di Azure Site Recovery pertinenti.

La figura seguente mostra un esempio di un utente che dispone di tre impostazioni di diagnostica per un insieme di credenziali. La prima impostazione, denominata **Setting1**, invia dati da un evento AzureBackupReport a un'area di lavoro log Analytics in modalità diagnostica di Azure. La seconda impostazione, denominata **Setting2**, invia i dati dai sei nuovi eventi di backup di Azure a un'area di lavoro log Analytics nella modalità specifica della risorsa. La terza impostazione, denominata **Setting3**, invia i dati dagli eventi Azure Site Recovery a un'area di lavoro log Analytics in modalità diagnostica di Azure.

![Tre impostazioni](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sul modello di dati Log Analytics per gli eventi di diagnostica](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
