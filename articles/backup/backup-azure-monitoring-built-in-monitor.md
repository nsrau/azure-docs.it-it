---
title: 'Backup di Azure: Backup di Azure di monitoraggio di carichi di lavoro protetti'
description: Monitorare i carichi di lavoro di Backup di Azure usando il portale di Azure
services: backup
author: pvrk
manager: shivamg
keywords: Backup di Azure; Avvisi
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 8d3e3257f16fe4e0f846c2268bfefc2771387de6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809035"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitoraggio dei carichi di lavoro di Backup di Azure

Backup di Azure offre diverse soluzioni di backup in base alla topologia backup di un requisito dell'infrastruttura (On-premises vs Azure). Qualsiasi utente di backup o un amministratore dovrebbe essere cosa sta succedendo in tutte le soluzioni e previsto da notificare in scenari importanti. Questo articolo illustra le funzionalità di monitoraggio e notifiche fornite dal servizio di Backup di Azure.

## <a name="backup-jobs-in-recovery-services-vault"></a>Processi di backup nell'insieme di credenziali di servizi di ripristino

Backup di Azure offre predefinite di monitoraggio e avviso di funzionalità per i carichi di lavoro protetti da Backup di Azure. In servizi di ripristino dell'insieme di credenziali delle impostazioni, il **Monitoring** sezione fornisce processi incorporati e avvisi.

![Insieme di credenziali RS integrate di monitoraggio](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

I processi vengono generati quando vengono eseguite operazioni come la configurazione di backup, backup, ripristino, Elimina dati di backup e così via.

Processi dalle soluzioni di Backup di Azure seguenti vengono mostrati di seguito:

  - Backup di macchine virtuali di Azure
  - Backup di File di Azure
  - Backup di Azure del carico di lavoro, ad esempio SQL
  - Agente di Backup di Azure (MAB)

NON vengono visualizzati i processi da System Center Data Protection Manager (SC DPM), Server di Backup di Microsoft Azure (MABS).

> [!NOTE]
> Carichi di lavoro di Azure, ad esempio i backup di SQL all'interno di macchine virtuali di Azure hanno l'elevato numero di processi di backup. Ad esempio, i backup del log è possono eseguire ogni 15 minuti. Di conseguenza, per questi carichi di lavoro DB vengono visualizzate le operazioni di nessun utente ha attivato. Operazioni di backup pianificate non vengono visualizzate.

## <a name="backup-alerts-in-recovery-services-vault"></a>Avvisi di backup nell'insieme di credenziali di servizi di ripristino

Gli avvisi sono principalmente a scenari in cui gli utenti vengono informati in modo che sia possibile intraprendere le azioni opportune. Il **avvisi di Backup** sezione illustra gli avvisi generati dal servizio Backup di Azure. Questi avvisi sono definiti dal servizio e l'utente non può personalizzato crea tutti gli avvisi.

### <a name="alert-scenarios"></a>Scenari di avviso
Gli scenari seguenti sono definiti dal servizio come alertable scenari.

  - Errori di backup/ripristino
  - Backup completato con avvisi
  - Arrestare la protezione con Mantieni dati/arrestare la protezione dati con cancellazione dei dati

### <a name="exceptions-when-an-alert-is-not-raised"></a>Eccezioni quando non viene generato un avviso
Esistono alcune eccezioni quando non viene generato un avviso in caso di errore, sono:

  - L'utente ha annullato in modo esplicito il processo in esecuzione
  - Il processo ha esito negativo perché un altro processo di backup è in corso ("Nothing" per agire su perché è necessario attendere il completamento del processo precedente)
  - Il processo di backup della macchina virtuale non riesce perché la macchina virtuale di Azure sottoposti a backup non esiste più

Le eccezioni sopra sono progettate da comprendere che il risultato di queste operazioni, principalmente utente attivato, viene visualizzato immediatamente nei client di portale/Powershell/CLI. Di conseguenza, l'utente riconosce immediatamente e non richiede una notifica.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Gli avvisi da soluzioni di Backup di Azure seguenti sono illustrati di seguito:

  - Backup della macchina virtuale di Azure
  - Backup di File di Azure
  - Backup del carico di lavoro, ad esempio SQL
  - Agente di Backup di Azure (MAB)

> [!NOTE]
> Gli avvisi da System Center Data Protection Manager (SC DPM), Server di Backup di Microsoft Azure (MABS) non vengono visualizzati qui.

### <a name="alert-types"></a>Tipi di avviso
In base alla gravità dell'avviso, è possibile impostare gli avvisi in tre tipi:

  - **Critica**: In principio, qualsiasi backup o ripristino errori (pianificati o l'utente ha attivato) potrebbe causare la generazione di un avviso e viene visualizzato come un avviso critico e anche operazioni distruttive, ad esempio delete backup.
  - **Avviso**: Se l'operazione di backup ha esito positivo ma con alcuni avvisi, sono elencati come avvisi di avvertimento.
  - **Informativo**: A partire da oggi, nessun avviso informativo viene generato dal servizio Backup di Azure.

## <a name="notification-for-backup-alerts"></a>Notifica per gli avvisi di Backup

> [!NOTE]
> Configurazione della notifica può essere eseguita solo tramite il portale di Azure. Supporto del modello o API di Azure Resource Manager REST/Powershell/CLI non è supportato.

Una volta che viene generato un avviso, gli utenti vengono informati. Backup di Azure fornisce un meccanismo incorporato notifica tramite posta elettronica. È possibile specificare indirizzi di posta elettronica singoli o liste di distribuzione per ricevere una notifica quando viene generato un avviso. È anche possibile scegliere se ricevere una notifica per ogni singolo avviso o di raggrupparli in un riepilogo orario e quindi ricevere una notifica.

![Notifica di posta elettronica insieme di credenziali RS integrate](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Quando la notifica è configurata, si riceverà un messaggio di benvenuto o introduttivo. In questo modo viene confermato che Backup di Azure possono inviare messaggi di posta elettronica a questi indirizzi quando viene generato un avviso.<br>

Se la frequenza è impostata su un riepilogo orario e un avviso è stato generato e risolto entro un'ora, non sarà una parte del digest orario imminente.

> [!NOTE]
> 
> * Se un'operazione distruttiva, ad esempio **arrestare la protezione dati con cancellazione dei dati** viene eseguita, viene generato un avviso e viene inviata un'e-mail a proprietari di sottoscrizioni, gli amministratori e coamministratori anche se le notifiche non sono configurate per il servizio di ripristino volta.
> * Per configurare la notifica per usano processi completati [Log Analitica](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="next-steps"></a>Passaggi successivi

[Monitorare Azure backup carichi di lavoro tramite Monitoraggio di Azure](backup-azure-monitoring-use-azuremonitor.md)
