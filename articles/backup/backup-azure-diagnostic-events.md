---
title: Uso delle impostazioni di diagnostica per gli insiemi di credenziali dei servizi di ripristino
description: Articolo che descrive come usare i vecchi e i nuovi eventi di diagnostica per backup di Azure
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 875adb82aeeb56b378a84ca01e716c7189abc64f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281103"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Uso delle impostazioni di diagnostica per gli insiemi di credenziali dei servizi di ripristino

Backup di Azure invia eventi di diagnostica che possono essere raccolti e usati a scopo di analisi, avvisi e report. 

È possibile configurare le impostazioni di diagnostica per un insieme di credenziali di servizi di ripristino tramite il portale di Azure, passando all'insieme di credenziali e facendo clic sulla voce di menu **impostazioni di diagnostica** . Facendo clic su **+ Aggiungi impostazione diagnostica** è possibile inviare uno o più eventi di diagnostica a un account di archiviazione, a un hub eventi o a un'area di lavoro log Analytics (la).

![Pannello impostazioni di diagnostica](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Eventi di diagnostica disponibili per gli utenti di backup di Azure

Backup di Azure fornisce gli eventi di diagnostica seguenti, ognuno dei quali fornisce dati dettagliati su un set specifico di elementi correlati al backup:
* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage 

[Modello di dati per gli eventi di diagnostica di backup di Azure](https://aka.ms/diagnosticsdatamodel)

I dati per questi eventi possono essere inviati a un account di archiviazione, a un'area di lavoro o a un hub eventi. Se si inviano questi dati a un'area di lavoro di LA, è necessario selezionare l'interruttore specifico per le **risorse** nella schermata delle **impostazioni di diagnostica** . per altre informazioni, vedere le sezioni seguenti.

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Uso delle impostazioni di diagnostica con Log Analytics (LA)

In linea con la roadmap di Azure Log Analytics, backup di Azure consente ora di inviare i dati di diagnostica dell'insieme di credenziali alle tabelle di LA dedicata per il backup. Queste tabelle sono denominate [tabelle specifiche delle risorse](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific).

Per inviare i dati di diagnostica dell'insieme di credenziali a LA:
1.  Passare all'insieme di credenziali e fare clic su **impostazioni di diagnostica**. Fare clic su **+ Aggiungi impostazioni di diagnostica**.
2.  Assegnare un nome all'impostazione di diagnostica.
3.  Selezionare la casella **Invia a log Analytics** e selezionare un'area di lavoro log Analytics.
4.  Selezionare la **risorsa specifica** nell'interruttore e verificare i sei eventi seguenti: **CoreAzureBackup**, **AddonAzureBackupAlerts**, **AddonAzureBackupProtectedInstance**, **AddonAzureBackupJobs**, **AddonAzureBackupPolicy**e **AddonAzureBackupStorage**.
5.  Fare clic su **Save**.

![Modalità specifica della risorsa](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Una volta che i dati passano nell'area di lavoro di LA, le tabelle dedicate per ognuno di questi eventi vengono create nell'area di lavoro. Se necessario, è possibile eseguire direttamente una query su una di queste tabelle e anche eseguire join o unioni tra queste tabelle.

> [!IMPORTANT]
> I sei eventi precedenti, ovvero CoreAzureBackup, AddonAzureBackupAlerts, AddonAzureBackupProtectedInstance, AddonAzureBackupJobs, AddonAzureBackupPolicy e AddonAzureBackupStorage, sono supportati **solo** in modalità specifica della risorsa. **Si noti che se si tenta di inviare i dati per questi sei eventi in modalità Diagnostica di Azure, non viene eseguito il flusso di dati nell'area di lavoro di LA.**

## <a name="legacy-event"></a>Evento legacy

Tradizionalmente, tutti i dati di diagnostica correlati al backup per un insieme di credenziali sono contenuti in un singolo evento denominato "AzureBackupReport". I sei eventi descritti in precedenza sono essenzialmente una scomposizione di tutti i dati contenuti in AzureBackupReport. 

Attualmente, continuiamo a supportare l'evento AzureBackupReport per la compatibilità con le versioni precedenti, nei casi in cui gli utenti hanno query personalizzate esistenti su questo evento, ad esempio, avvisi del log personalizzati, visualizzazioni personalizzate e così via. Tuttavia, si consiglia di scegliere i nuovi eventi per tutte le nuove impostazioni di diagnostica nell'insieme di credenziali, poiché in questo modo i dati risultano molto più semplici da usare nelle query di log, offre una migliore individuabilità degli schemi e della relativa struttura, migliora le prestazioni in entrambi i sistemi di inserimento latenza e tempi di esecuzione delle query. Il supporto per l'uso della modalità di Diagnostica di Azure verrà eliminato gradualmente e quindi la scelta dei nuovi eventi potrebbe essere utile per evitare migrazioni complesse in un secondo momento.

È possibile scegliere di creare impostazioni di diagnostica separate per AzureBackupReport e i sei nuovi eventi, fino a quando non sarà stata eseguita la migrazione di tutte le query personalizzate per utilizzare i dati delle nuove tabelle. L'immagine seguente mostra un esempio di insieme di credenziali con due impostazioni di diagnostica. La prima impostazione, denominata **Setting1** , invia i dati dell'evento AzureBackupReport a un'area di lavoro la in modalità AzureDiagnostics. La seconda impostazione, denominata **Setting2** , invia i dati dei sei nuovi eventi di backup di Azure a un'area di lavoro la in modalità specifica della risorsa.

![Due impostazioni](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> L'evento AzureBackupReport è supportato **solo** in modalità diagnostica di Azure. **Si noti che se si tenta di inviare i dati per questo evento in modalità specifica della risorsa, non verrà eseguito il flusso di dati nell'area di lavoro di LA.**

> [!NOTE]
> L'interruttore per Diagnostica di Azure/specifico di risorsa viene visualizzato solo se l'utente verifica l' **invio al log Analytics**. Per inviare dati a un account di archiviazione o a un hub eventi, un utente può semplicemente selezionare la destinazione richiesta e controllare gli eventi desiderati, senza input aggiuntivi. Anche in questo caso, è consigliabile non scegliere il AzureBackupReport dell'evento legacy, in futuro.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Utenti che inviano eventi di Azure Site Recovery al Log Analytics (LA)

Backup di Azure e gli eventi di Azure Site Recovery vengono inviati dallo stesso insieme di credenziali di servizi di ripristino. Poiché Azure Site Recovery non è attualmente caricato in tabelle specifiche delle risorse, gli utenti che desiderano inviare eventi Azure Site Recovery a LA vengono indirizzati all'uso **della modalità diagnostica di Azure** (vedere l'immagine seguente). Se **si sceglie la modalità specifica della risorsa per gli eventi di Azure Site Recovery, i dati richiesti non vengono inviati all'area di lavoro la**.

![Eventi Site Recovery](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Per riepilogare le sfumature precedenti:

* Se LA diagnostica è già stata configurata con Diagnostica di Azure e sono state scritte query personalizzate su di essa, mantenere **intatta**tale impostazione fino a quando non si esegue la migrazione delle query per usare i dati dei nuovi eventi.
* Se si vuole eseguire l'onboarding anche nelle nuove tabelle (come consigliato), creare una **nuova** impostazione di diagnostica, scegliere **specifica della risorsa** e selezionare i sei nuovi eventi come specificato in precedenza.
* Se si stanno inviando eventi Azure Site Recovery a LA, **non scegliere la** modalità specifica della risorsa per questi eventi. in caso contrario, i dati per questi eventi non verranno inseriti nell'area di lavoro di la. In alternativa, creare un' **impostazione di diagnostica aggiuntiva**, selezionare **diagnostica di Azure**e scegliere gli eventi Azure Site Recovery pertinenti.

L'immagine seguente mostra un esempio di un utente che dispone di tre impostazioni di diagnostica per un insieme di credenziali. La prima impostazione, denominata **Setting1** , invia i dati dall'evento AzureBackupReport a un'area di lavoro la in modalità AzureDiagnostics. La seconda impostazione, denominata **Setting2** , invia i dati dai sei nuovi eventi di backup di Azure a un'area di lavoro di la in modalità specifica della risorsa. La terza impostazione, denominata **Setting3**, invia i dati dagli eventi di Azure Site Recovery a un'area di lavoro di la in modalità diagnostica di Azure.

![Tre impostazioni](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sul modello di dati Log Analytics per gli eventi di diagnostica](https://aka.ms/diagnosticsdatamodel)
