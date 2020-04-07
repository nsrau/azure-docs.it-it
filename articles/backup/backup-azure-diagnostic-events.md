---
title: Utilizzo delle impostazioni di diagnostica per gli archivi dei servizi di ripristinoUsing diagnostics settings for Recovery Services Vaults
description: Un articolo che descrive come usare gli eventi di diagnostica vecchi e nuovi per Backup di AzureAn article describing how to use the old and new diagnostics events for Azure Backup
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: d10bedf3818559971eff12624152d0e797f6c3cc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672791"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Uso delle impostazioni di diagnostica per gli insiemi di credenziali di Servizi di ripristino

Backup di Azure invia eventi di diagnostica che possono essere raccolti e usati ai fini dell'analisi, degli avvisi e della creazione di report. 

È possibile configurare le impostazioni di diagnostica per un vault di Servizi di ripristino tramite il portale di Azure, passando all'insieme di credenziali e facendo clic sulla voce di menu **Impostazioni di diagnostica.** Facendo clic su **Aggiungi impostazioni di diagnostica** è possibile inviare uno o più eventi di diagnostica a un account di archiviazione, a un hub eventi o a un'area di lavoro di Log Analytics (LA).

![Pannello Impostazioni di diagnostica](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Eventi di diagnostica disponibili per gli utenti di Backup di AzureDiagnostics Events Available for Azure Backup Users

Backup di Azure offre gli eventi di diagnostica seguenti, ognuno dei quali fornisce dati dettagliati su un set specifico di elementi correlati al backup:Azure Backup provides the following diagnostic events, each of which provides detailed data on a specific set of backup-related artifacts:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstanceAddonAzureBackupProtectedInstance
* AddonAzureBackupJobsAddonAzureBackupJobs
* AddonAzureBackupPolicyAddonAzureBackupPolicy
* AddonAzureBackupStorageAddonAzureBackupStorage

[Modello di dati per gli eventi di diagnostica di Backup di AzureData Model for Azure Backup Diagnostics Events](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

I dati per questi eventi possono essere inviati a un account di archiviazione, a un'area di lavoro LA o a un hub eventi. Se si inviano questi dati a un'area di lavoro di LA, è necessario selezionare l'interruttore **Specifico della risorsa** nella schermata Impostazioni di **diagnostica** (vedere ulteriori informazioni nelle sezioni seguenti).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Utilizzo delle impostazioni di diagnostica con Log Analytics (LA)

In linea con la roadmap di Azure Log Analytics, Backup di Azure consente ora di inviare i dati di diagnostica dell'insieme di credenziali alle tabelle LA dedicate per il backup. Queste sono denominate [tabelle specifiche delle](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)risorse .

Per inviare i dati di diagnostica del vault a LA:

1.    Passare al vault e fare clic su **Impostazioni di diagnostica**. Fare clic su **Aggiungi impostazione diagnostica**.
2.    Assegnare un nome all'impostazione Diagnostica.Name a name to the Diagnostics setting.
3.    Selezionare la casella **Invia a Log Analytics** e selezionare un'area di lavoro di Log Analytics.
4.    Selezionare **Specifico risorsa** nell'interruttore e controllare i sei eventi seguenti: **CoreAzureBackup**, **AddonAzureBackupAlerts**, **AddonAzureBackupProtectedInstance**, **AddonAzureBackupJobs**, **AddonAzureBackupPolicy**e **AddonAzureBackupStorage**.
5.    Fare clic su **Save**.

![Modalità specifica delle risorse](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Una volta che i dati vengono trasmessi nell'area di lavoro di LA, nell'area di lavoro vengono create tabelle dedicate per ognuno di questi eventi. È possibile eseguire query direttamente su una di queste tabelle ed eseguire anche join o unioni tra queste tabelle, se necessario.

> [!IMPORTANT]
> I sei eventi precedenti, ovvero CoreAzureBackup, AddonAzureBackupAlerts, AddonAzureBackupProtectedInstance, AddonAzureBackupJobs, AddonAzureBackupPolicy e AddonAzureBackupStorage, sono supportati **solo** in modalità specifica delle risorse nei report di [backup.](https://docs.microsoft.com/azure/backup/configure-reports) **Si noti che se si tenta di inviare dati per questi sei eventi in modalità diagnostica di Azure, nessun dato sarà visibile nei rapporti di backup.**

## <a name="legacy-event"></a>Evento legacy

Tradizionalmente, tutti i dati di diagnostica correlati al backup per un insieme di credenziali sono stati contenuti in un singolo evento denominato 'AzureBackupReport'. I sei eventi descritti in precedenza sono, in sostanza, una scomposizione di tutti i dati contenuti in AzureBackupReport.The six events above are, in essence, a decomposition of all the data contained in AzureBackupReport. 

Attualmente, continuiamo a supportare l'evento AzureBackupReport per la compatibilità con le versioni precedenti, nei casi in cui gli utenti hanno query personalizzate esistenti su questo evento, ad esempio avvisi di log personalizzati, visualizzazioni personalizzate e così via. Tuttavia, **è consigliabile passare ai [nuovi eventi](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users) **il più presto possibile, poiché ciò rende i dati molto più facili da utilizzare nelle query di log, fornisce una migliore individuabilità degli schemi e la relativa struttura, migliora le prestazioni sia per la latenza di inserimento che per i tempi di query. 

**L'evento legacy in modalità Diagnostica di Azure sarà deprecato e pertanto la scelta dei nuovi eventi può essere utile per evitare migrazioni complesse in un secondo momento.** La [nostra soluzione di reporting](https://docs.microsoft.com/azure/backup/configure-reports) che sfrutta Log Analytics interromperà anche il supporto dei dati dell'evento legacy.

### <a name="steps-to-move-to-new-diagnostics-settings-to-log-analytics-workspace"></a>Passaggi per passare a nuove impostazioni di diagnostica (nell'area di lavoro di Log Analytics)

1. Identificare gli insiemi di credenziali che inviano i dati alle aree di lavoro di Log Analytics utilizzando l'evento legacy e le sottoscrizioni a cui appartengono. Eseguire le aree di lavoro seguenti per identificare gli insiemi di credenziali e le sottoscrizioni seguenti:

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

2. Usare i [criteri di Azure](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) predefiniti di Backup di Azure per aggiungere una nuova impostazione di diagnostica per tutti gli insiemi di credenziali in un ambito specificato. Questo criterio aggiunge una nuova impostazione di diagnostica agli insiemi di credenziali che non dispongono di un'impostazione di diagnostica (o) dispongono solo di un'impostazione di diagnostica legacy. Questo criterio può essere assegnato a un'intera sottoscrizione o gruppo di risorse alla volta. Si noti che è necessario l'accesso "Proprietario" a ogni sottoscrizione per cui è assegnato il criterio.

È possibile scegliere di avere impostazioni di diagnostica separate per AzureBackupReport e i sei nuovi eventi, fino a quando non è stata eseguita la migrazione di tutte le query personalizzate per usare i dati delle nuove tabelle. L'immagine seguente mostra un esempio di un vault con due impostazioni di diagnostica. La prima impostazione, denominata Setting1 invia i dati dell'evento AzureBackupReport a un'area di lavoro la La in modalità AzureDiagnostics.The first setting, named **Setting1** sends data of AzureBackupReport event to an LA Workspace in AzureDiagnostics mode. La seconda impostazione, denominata **Setting2,** invia i dati dei sei nuovi eventi di Backup di Azure a un'area di lavoro LA in modalità Specifica risorsa.

![Due impostazioni](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> L'evento AzureBackupReport è supportato solo in modalità diagnostica di Azure.The AzureBackupReport event is supported **only** in Azure Diagnostics Mode. **Si noti che se si tenta di inviare i dati per questo evento in modalità specifica della risorsa, nessun dato verrà eseguito nell'area di lavoro di LO.**

> [!NOTE]
> L'interruttore Diagnostica di Azure/Specifico risorsa viene visualizzato solo se l'utente seleziona **Invia a Log Analytics**. Per inviare dati a un account di archiviazione o a un hub eventi, un utente può semplicemente selezionare la destinazione richiesta e controllare gli eventi desiderati, senza ulteriori input. Anche in questo caso, si consiglia di non scegliere l'evento legacy AzureBackupReport, in futuro.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Utenti che inviano eventi di ripristino del sito di Azure a Log Analytics (LA)

Gli eventi di Backup di Azure e di Ripristino di azure vengono inviati dallo stesso insieme di credenziali di Servizi di ripristino. Poiché Azure Site Recovery non è attualmente instato in elenco in tabelle specifiche delle risorse, gli utenti che desiderano inviare gli eventi di Ripristino di sito di Azure a LA devono usare **solo** la modalità diagnostica di Azure (vedere l'immagine seguente). La scelta della modalità specifica della risorsa per gli eventi di ripristino del sito di **Azure impedirà l'invio dei dati necessari all'area**di lavoro di LA .

![Eventi di ripristino del sito](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Per riassumere le sfumature di cui sopra:

* Se la diagnostica LA è già stata configurata con Diagnostica di Azure e sono state scritte query personalizzate, mantenere **intatta**tale impostazione fino a quando non si esegue la migrazione delle query per usare i dati dei nuovi eventi.
* Se si desidera eseguire l'onboarding anche in nuove tabelle (come consigliato), creare una **nuova** impostazione di diagnostica, scegliere **Specifico risorsa** e selezionare i sei nuovi eventi come specificato in precedenza.
* Se si inviano attualmente gli eventi di ripristino del sito di Azure a Los Angeles, non scegliere la modalità Specifica risorsa per questi eventi, altrimenti i dati per questi eventi non verranno trasportati nell'area di lavoro di La.If you are currently sending Azure Site Recovery Events to LA, **do not** choose Resource Specific mode for these events, otherwise data for these events will not flow into your LA Workspace. Creare invece **un'impostazione di diagnostica aggiuntiva,** selezionare Diagnostica di **Azure**e scegliere gli eventi di Ripristino siti di Azure pertinenti.

L'immagine seguente mostra un esempio di un utente con tre impostazioni di diagnostica per un vault. La prima impostazione, denominata Setting1 invia i dati dall'evento AzureBackupReport a un'area di lavoro la-AREA di lavoro in modalità AzureDiagnostics.The first setting, named **Setting1** sends data from AzureBackupReport event to an LA Workspace in AzureDiagnostics mode. La seconda impostazione, denominata **Setting2,** invia i dati dai sei nuovi eventi di Backup di Azure a un'area di lavoro LA in modalità Specifica delle risorse. La terza impostazione, denominata Setting3 , invia i dati dagli eventi di Azure Site Recovery a un'area di lavoro DI LA in modalità diagnostica di Azure.The third setting, named **Setting3**, sends data from the Azure Site Recovery events to an LA Workspace in Azure Diagnostics Mode.

![Tre impostazioni](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sul modello di dati di Log Analytics per gli eventi di diagnosticaLearn the Log Analytics Data Model for the Diagnostics Events](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
