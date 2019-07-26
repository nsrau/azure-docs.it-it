---
title: 'Backup di Azure: Monitorare i carichi di lavoro protetti di backup di Azure'
description: Monitorare i carichi di lavoro di backup di Azure usando portale di Azure
author: pvrk
manager: shivamg
keywords: Backup di Azure; Avvisi
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: b41b32943aa0113a7653c8d2eb74fd04afb2e080
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465842"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitoraggio dei carichi di lavoro di backup di Azure

Backup di Azure offre più soluzioni di backup in base al requisito di backup e alla topologia dell'infrastruttura (in locale rispetto ad Azure). Eventuali utenti o amministratori di backup dovrebbero vedere cosa accade in tutte le soluzioni e dovrebbero ricevere notifiche in scenari importanti. Questo articolo descrive in dettaglio le funzionalità di monitoraggio e notifica fornite dal servizio backup di Azure.

## <a name="backup-jobs-in-recovery-services-vault"></a>Processi di backup nell'insieme di credenziali di servizi di ripristino

Backup di Azure offre funzionalità di monitoraggio e avviso predefinite per i carichi di lavoro protetti da backup di Azure. Nelle impostazioni dell'insieme di credenziali di servizi di ripristino, la sezione **monitoraggio** include processi e avvisi incorporati.

![Monitoraggio incorporato dell'insieme di credenziali RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

I processi vengono generati quando vengono eseguite operazioni quali la configurazione di backup, backup, ripristino, eliminazione del backup e così via.

I processi dalle seguenti soluzioni di backup di Azure sono illustrati di seguito:

  - Backup di macchine virtuali di Azure
  - Backup di file di Azure
  - Backup del carico di lavoro di Azure, ad esempio SQL
  - Agente di Backup di Azure (MAB)

I processi da System Center Data Protection Manager (SC-DPM), server di Backup di Microsoft Azure (MAB) non vengono visualizzati.

> [!NOTE]
> I carichi di lavoro di Azure, ad esempio i backup SQL nelle macchine virtuali di Azure, hanno un numero elevato di processi di backup. Ad esempio, i backup del log possono essere eseguiti ogni 15 minuti. Per questi carichi di lavoro di database vengono quindi visualizzate solo le operazioni attivate dall'utente. Le operazioni di backup pianificate non vengono visualizzate.

## <a name="backup-alerts-in-recovery-services-vault"></a>Avvisi di backup nell'insieme di credenziali di servizi di ripristino

Gli avvisi sono essenzialmente scenari in cui gli utenti vengono informati in modo che possano intraprendere le azioni rilevanti. La sezione **avvisi di backup** Mostra gli avvisi generati dal servizio backup di Azure. Questi avvisi sono definiti dal servizio e l'utente non può creare avvisi personalizzati.

### <a name="alert-scenarios"></a>Scenari di avviso
Gli scenari seguenti sono definiti dal servizio come scenari di avviso.

  - Errori di backup/ripristino
  - Backup completato con avvisi per l'agente di Backup di Azure (MAB)
  - Arrestare la protezione con Mantieni dati/arresta la protezione con Elimina dati

### <a name="exceptions-when-an-alert-is-not-raised"></a>Eccezioni quando non viene generato un avviso
Quando un avviso non viene generato in caso di errore, si verificano alcune eccezioni:

  - L'utente ha annullato esplicitamente il processo in esecuzione
  - Il processo ha esito negativo perché è in corso un altro processo di backup (non è necessario agire da qui perché è sufficiente attendere il completamento del processo precedente)
  - Il processo di backup della VM non riesce perché la macchina virtuale di Azure di cui è stato eseguito il backup non esiste più

Le eccezioni sopra riportate sono progettate dal comprendere che il risultato di queste operazioni (principalmente attivato dall'utente) viene visualizzato immediatamente nei client Portal/PS/CLI. Di conseguenza, l'utente è immediatamente consapevole e non necessita di una notifica.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Gli avvisi delle seguenti soluzioni di backup di Azure sono illustrati di seguito:

  - Backup di macchine virtuali di Azure
  - Backup di File di Azure
  - Backup del carico di lavoro di Azure, ad esempio SQL
  - Agente di Backup di Azure (MAB)

> [!NOTE]
> Gli avvisi di System Center Data Protection Manager (SC-DPM), server di Backup di Microsoft Azure (MAB) non vengono visualizzati qui.

### <a name="alert-types"></a>Tipi di avviso
In base alla gravità dell'avviso, gli avvisi possono essere definiti in tre tipi:

  - **Critica**: In linea di principio, qualsiasi errore di backup o ripristino (pianificato o attivato dall'utente) provocherebbe la generazione di un avviso e verrebbe visualizzato come avviso critico e anche da operazioni distruttive come l'eliminazione del backup.
  - **Avviso**: Se l'operazione di backup ha esito positivo ma con pochi avvisi, vengono elencati come avvisi di avviso.
  - **Informazioni**: A partire da oggi, il servizio backup di Azure non genera alcun avviso informativo.

## <a name="notification-for-backup-alerts"></a>Notifica per gli avvisi di backup

> [!NOTE]
> La configurazione della notifica può essere eseguita solo tramite il portale di Azure. Il supporto per PS/CLI/API REST/Azure Resource Manager modello non è supportato.

Quando viene generato un avviso, viene inviata una notifica agli utenti. Backup di Azure fornisce un meccanismo di notifica incorporato tramite posta elettronica. È possibile specificare singoli indirizzi di posta elettronica o liste di distribuzione per ricevere una notifica quando viene generato un avviso. È anche possibile scegliere se ricevere una notifica per ogni singolo avviso o raggrupparli in un digest orario e quindi ricevere una notifica.

![Notifica di posta elettronica incorporate dell'insieme di credenziali RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Quando la notifica viene configurata, si riceverà un messaggio di benvenuto o introduttivo. Questo conferma che backup di Azure può inviare messaggi di posta elettronica a questi indirizzi quando viene generato un avviso.<br>

Se la frequenza è stata impostata su un digest orario e un avviso viene generato e risolto entro un'ora, non sarà parte del digest orario imminente.

> [!NOTE]
>
> * Se viene eseguita un'operazione distruttiva, ad esempio **Arresta la protezione con l'eliminazione dei dati** , viene generato un avviso e viene inviato un messaggio di posta elettronica ai proprietari, agli amministratori e ai coamministratori della sottoscrizione anche se le notifiche non sono configurate per l'insieme di credenziali del servizio di ripristino.
> * Per configurare la notifica per i processi riusciti, utilizzare [log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="next-steps"></a>Passaggi successivi

[Monitorare i carichi di lavoro di backup di Azure con monitoraggio di Azure](backup-azure-monitoring-use-azuremonitor.md)
