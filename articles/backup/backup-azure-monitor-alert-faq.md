---
title: Domande frequenti sul monitoraggio di avvisi e report
description: In questo articolo vengono fornite le risposte alle domande più comuni sull'avviso di monitoraggio di backup di Azure e sui report di backup di Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: f5be97458ba658f315c31ae34e540842b64e3ec4
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989570"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Avviso di monitoraggio di backup di Azure-Domande frequenti

Questo articolo risponde a domande comuni sul monitoraggio e la creazione di report di backup di Azure.

## <a name="configure-azure-backup-reports"></a>Configurare report di Backup di Azure

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Ricerca per categorie controllare se i dati di Reporting hanno iniziato a scorrere in un'area di lavoro di Log Analytics (LA)?

Passare all'area di lavoro LA configurata, passare alla voce di menu **logs** ed eseguire la query CoreAzureBackup | eseguire 1. Se viene visualizzato un record restituito, significa che i dati sono stati avviati nel flusso nell'area di lavoro. Il push di dati iniziale può richiedere fino a 24 ore.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Qual è la frequenza di push di dati in un'area di lavoro di LA?

I dati di diagnostica dell'insieme di credenziali vengono pompati nell'area di lavoro Log Analytics con un certo ritardo. Ogni evento arriva nell'area di lavoro Log Analytics da 20 a 30 minuti dopo il push dall'insieme di credenziali dei servizi di ripristino. Ecco altri dettagli sul ritardo:

* Per tutte le soluzioni, viene eseguito il push degli avvisi predefiniti del servizio di backup non appena vengono creati. Quindi, vengono in genere visualizzati nell'area di lavoro Log Analytics dopo 20 o 30 minuti.
* Per tutte le soluzioni, i processi di backup su richiesta e i processi di ripristino vengono spostati non appena vengono completati.
* Per tutte le soluzioni ad eccezione di backup SQL, i processi di backup pianificati vengono inseriti non appena vengono completati.
* Per il backup SQL, poiché i backup del log possono essere eseguiti ogni 15 minuti, le informazioni per tutti i processi di backup pianificati completati, inclusi i log, vengono inserite in batch e inserite ogni 6 ore.
* Per tutte le soluzioni, altre informazioni, ad esempio l'elemento di backup, i criteri, i punti di ripristino, l'archiviazione e così via, vengono inserite almeno una volta al giorno.
* Una modifica alla configurazione del backup, ad esempio la modifica dei criteri o la modifica dei criteri, attiva un push di tutte le informazioni di backup correlate.

### <a name="how-long-can-i-retain-reporting-data"></a>Per quanto tempo è possibile mantenere i dati dei report?

Dopo aver creato un'area di lavoro di LA, è possibile scegliere di conservare i dati per un massimo di 2 anni. Per impostazione predefinita, un'area di lavoro di LA mantiene i dati per 31 giorni.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>I dati nei report vengono visualizzati dopo aver configurato l'area di lavoro di LA?

 Tutti i dati generati dopo la configurazione delle impostazioni di diagnostica vengono inseriti nell'area di lavoro di e sono disponibili nei report. I processi in corso non vengono sottoposti a push per la creazione dei report. Una volta completato o non superato il processo, questo viene inviato ai report.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>È possibile visualizzare i report negli insiemi di credenziali e nelle sottoscrizioni?

Sì, è possibile visualizzare i report negli insiemi di credenziali e nelle sottoscrizioni, nonché nelle aree. I dati possono trovarsi in una singola area di lavoro o in un gruppo di aree di lavoro di.

### <a name="can-i-view-reports-across-tenants"></a>È possibile visualizzare I report tra I tenant?

Se si è un utente di [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) con accesso delegato alle sottoscrizioni dei clienti o alle aree di lavoro di la, è possibile usare i report di backup per visualizzare i dati in tutti i tenant.

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
