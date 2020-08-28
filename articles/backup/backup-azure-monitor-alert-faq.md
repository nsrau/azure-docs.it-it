---
title: Domande frequenti sul monitoraggio di avvisi e report
description: In questo articolo vengono fornite le risposte alle domande più comuni sull'avviso di monitoraggio di backup di Azure e sui report di backup di Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 4ce1d65414011b1e307cc16fe886adeb007b262c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89000720"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Avviso di monitoraggio di backup di Azure-Domande frequenti

Questo articolo risponde a domande comuni sul monitoraggio e la creazione di report di backup di Azure.

## <a name="configure-azure-backup-reports"></a>Configurare report di Backup di Azure

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Ricerca per categorie controllare se i dati di Reporting hanno iniziato a scorrere in un'area di lavoro di Log Analytics (LA)?

Passare all'area di lavoro LA configurata, passare alla voce di menu **logs** ed eseguire la query CoreAzureBackup | eseguire 1. Se viene visualizzato un record restituito, significa che i dati sono stati avviati nel flusso nell'area di lavoro. Il push di dati iniziale può richiedere fino a 24 ore.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Qual è la frequenza di push di dati in un'area di lavoro di LA?

Il pump dei dati di diagnostica dall'insieme di credenziali nell'area di lavoro Log Analytics viene eseguito con un certo ritardo. Ogni evento arriva nell'area di lavoro Log Analytics da 20 a 30 minuti dopo il push dall'insieme di credenziali di Servizi di ripristino. Ecco altri dettagli sul ritardo:

* Per tutte le soluzioni, il push degli avvisi predefiniti del servizio di backup viene eseguito non appena gli avvisi vengono creati. Questi pertanto vengono in genere visualizzati nell'area di lavoro Log Analytics dopo 20-30 minuti.
* Per tutte le soluzioni, il push dei processi di backup e ripristino su richiesta viene eseguito non appena questi processi vengono completati.
* Per tutte le soluzioni ad eccezione del backup SQL, il push dei processi di backup pianificati viene eseguito non appena questi processi vengono completati.
* Per il backup SQL, poiché i backup dei log possono essere eseguiti ogni 15 minuti, le informazioni per tutti i processi di backup pianificati completati, inclusi i log, vengono riunite in batch e il push dei batch viene eseguito ogni 6 ore.
* Per tutte le soluzioni, il push di altre informazioni, ad esempio l'elemento di backup, i criteri, i punti di ripristino, la risorsa di archiviazione e così via, viene eseguito almeno una volta al giorno.
* Una modifica alla configurazione di backup, ad esempio la sostituzione o la modifica dei criteri, attiva il push di tutte le informazioni di backup correlate.

### <a name="how-long-can-i-retain-reporting-data"></a>Per quanto tempo è possibile mantenere i dati dei report?

Dopo aver creato un'area di lavoro di LA, è possibile scegliere di conservare i dati per un massimo di 2 anni. Per impostazione predefinita, un'area di lavoro di LA mantiene i dati per 31 giorni.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>I dati nei report vengono visualizzati dopo aver configurato l'area di lavoro di LA?

 Tutti i dati generati dopo la configurazione delle impostazioni di diagnostica vengono inseriti nell'area di lavoro di e sono disponibili nei report. I processi in corso non vengono sottoposti a push per la creazione dei report. Dopo che il processo viene completato o ha esito negativo, viene inviato ai report.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>È possibile visualizzare i report negli insiemi di credenziali e nelle sottoscrizioni?

Sì, è possibile visualizzare i report negli insiemi di credenziali e nelle sottoscrizioni, nonché nelle aree. I dati possono trovarsi in una singola area di lavoro o in un gruppo di aree di lavoro di.

### <a name="can-i-view-reports-across-tenants"></a>È possibile visualizzare I report tra I tenant?

Se si è un utente di [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) con accesso delegato alle sottoscrizioni dei clienti o alle aree di lavoro di la, è possibile usare i report di backup per visualizzare i dati in tutti i tenant.

## <a name="recovery-services-vault"></a>Insieme di credenziali di Servizi di ripristino

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Quanto tempo è necessario per la reflection dello stato del processo dell'agente di backup di Azure nel portale?

Il portale di Azure può richiedere fino a 15 minuti per riflettere lo stato del processo dell'agente di backup di Azure.

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
