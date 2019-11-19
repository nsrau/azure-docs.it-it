---
title: Domande frequenti sul monitoraggio di avvisi e report
description: In questo articolo vengono fornite le risposte alle domande più comuni sull'avviso di monitoraggio di backup di Azure e sui report di backup di Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 9cf7bf49d29b5faa9811a591b45179fe83c1d483
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172918"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Avviso di monitoraggio di backup di Azure-Domande frequenti

Questo articolo risponde a domande comuni sull'avviso di monitoraggio di Azure.

## <a name="configure-azure-backup-reports"></a>Configurare report di Backup di Azure

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Come verificare se è stato avviato il trasferimento dei dati dei report in un account di archiviazione?

Passare all'account di archiviazione configurato e selezionare i contenitori. Se il contenitore include una voce per insights-logs-azurebackupreport, indica che è stato avviato il trasferimento dei dati dei report.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Qual è la frequenza del push di dati nell'account di archiviazione e nel pacchetto di contenuto di Backup di Azure in Power BI?

  Per gli utenti al primo tentativo sono necessarie circa 24 ore per il push dei dati in un account di archiviazione. Dopo aver completato il push iniziale, i dati vengono aggiornati con la frequenza illustrata nella figura riportata di seguito.

* I dati correlati a **processi**, **avvisi**, **elementi di backup**, **insiemi di credenziali**, **server protetti** e **criteri** vengono sottoposti a push in un account di archiviazione a ogni accesso.

* I dati correlati all'**archiviazione** vengono sottoposti a push nell'account di archiviazione di un cliente ogni 24 ore.

    ![Frequenza del push di dati dei report di Backup di Azure](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

* Power BI esegue un [aggiornamento pianificato una volta al giorno](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). È possibile eseguire un aggiornamento manuale dei dati in Power BI per il pacchetto di contenuto.

### <a name="how-long-can-i-retain-reports"></a>Per quanto tempo è possibile conservare i report?

Quando si configura un account di archiviazione, è possibile selezionare un periodo di conservazione per i dati dei report al suo interno. Eseguire il passaggio 6 nella sezione precedente [Configurare l'account di archiviazione per i report](backup-azure-configure-reports.md#configure-storage-account-for-reports). È anche possibile [analizzare i report in Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) e salvarli per un periodo di conservazione più lungo, in base alle esigenze.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Tutti i dati saranno visibili nei report dopo aver configurato l'account di archiviazione?

 Tutti i dati generati dopo aver configurato l'account di archiviazione verranno sottoposti a push nell'account di archiviazione e saranno disponibili nei report. I processi in corso non vengono sottoposti a push per la creazione dei report. Dopo che il processo viene completato o ha esito negativo, viene inviato ai report.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Se è già stato configurato l'account di archiviazione per visualizzare i report, è possibile modificare la configurazione per usare un altro account di archiviazione?

Sì, è possibile modificare la configurazione in modo che punti a un account di archiviazione diverso. Durante la connessione al pacchetto di contenuto di Backup di Azure usare l'account di archiviazione appena configurato. Dopo aver configurato un account di archiviazione diverso, i nuovi dati verranno trasferiti in questo account di archiviazione. I dati meno recenti (prima di modificare la configurazione) rimarranno tuttavia nell'account di archiviazione precedente.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>È possibile visualizzare i report negli insiemi di credenziali e nelle sottoscrizioni?

Sì, è possibile configurare lo stesso account di archiviazione per vari insiemi di credenziali per visualizzare i report tra loro. È anche possibile configurare lo stesso account di archiviazione per gli insiemi di credenziali nelle sottoscrizioni. È quindi possibile usare questo account di archiviazione durante la connessione al pacchetto di contenuto di Backup di Azure in Power BI per visualizzare i report. L'account di archiviazione selezionato deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Dopo quanto tempo lo stato del processo dell'agente di Backup di Azure viene indicato nel portale?

Il portale di Azure può impiegare fino a 15 minuti per visualizzare lo stato del processo dell'agente di Backup di Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Quando un processo di backup non riesce, quanto tempo passa prima che venga generato un avviso?

Un avviso viene generato entro 20 minuti dall'errore di backup di Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Esiste un caso in cui non viene inviato un messaggio di posta elettronica se le notifiche sono configurate?

Sì. Le notifiche non vengono inviate nei casi seguenti.

* Se le notifiche sono configurate su base oraria e un avviso viene generato e risolto entro l'ora
* Quando un processo viene annullato
* Se un secondo processo di backup non riesce perché è in corso il processo di backup originale

## <a name="recovery-services-vault"></a>Insieme di credenziali dei servizi di ripristino

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Dopo quanto tempo lo stato del processo dell'agente di Backup di Azure viene indicato nel portale?

Il portale di Azure può impiegare fino a 15 minuti per visualizzare lo stato del processo dell'agente di Backup di Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Quando un processo di backup non riesce, quanto tempo passa prima che venga generato un avviso?

Un avviso viene generato entro 20 minuti dall'errore di backup di Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Esiste un caso in cui non viene inviato un messaggio di posta elettronica se le notifiche sono configurate?

Sì. Nelle situazioni seguenti le notifiche non vengono inviate:

* Se le notifiche sono configurate su base oraria e un avviso viene generato e risolto entro l'ora
* Quando un processo viene annullato
* Se un secondo processo di backup non riesce perché è in corso il processo di backup originale

## <a name="next-steps"></a>Passaggi successivi

Leggere le altre domande frequenti:

* [Domande comuni](backup-azure-vm-backup-faq.md) sul backup della macchina virtuale di Azure.
* [Domande comuni](backup-azure-file-folder-backup-faq.md) sull'agente di Backup di Azure
