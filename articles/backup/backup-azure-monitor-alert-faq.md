---
title: Domande frequenti sul monitoraggio di avvisi e reportMonitoring Alert and Reports FAQ
description: In questo articolo vengono fornite le risposte alle domande comuni sull'avviso di monitoraggio di Backup di Azure e sui report di Backup di Azure.In this article, discover answers to common questions about the Azure Backup Monitoring Alert and Azure Backup reports.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: f5be97458ba658f315c31ae34e540842b64e3ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989570"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Avviso di monitoraggio backup di Azure - Domande frequenti

Questo articolo risponde alle domande comuni sul monitoraggio e la creazione di report di Backup di Azure.This article answers common questions about Azure Backup monitoring and reporting.

## <a name="configure-azure-backup-reports"></a>Configurare report di Backup di Azure

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Come è possibile verificare se i dati dei report hanno iniziato a fluire in un'area di lavoro di Log Analytics (LA)?

Passare all'area di lavoro di LA configurata, passare alla voce di menu **Registri** ed eseguire la query CoreAzureBackup . prendere 1. Se viene restituito un record, significa che i dati hanno iniziato a scorrere nell'area di lavoro. Il push dei dati iniziale può richiedere fino a 24 ore.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Qual è la frequenza di push dei dati in un'area di lavoro di LA?

I dati di diagnostica dell'insieme di credenziali vengono pompati nell'area di lavoro di Log Analytics con un certo ritardo. Ogni evento arriva all'area di lavoro di Log Analytics da 20 a 30 minuti dopo il push dall'insieme di credenziali di Servizi di ripristino. Ecco ulteriori dettagli sul ritardo:

* In tutte le soluzioni, gli avvisi predefiniti del servizio di backup vengono inviati non appena vengono creati. In genere vengono visualizzati nell'area di lavoro di Log Analytics dopo 20-30 minuti.
* In tutte le soluzioni, i processi di backup su richiesta e i processi di ripristino vengono inviati al termine.
* Per tutte le soluzioni ad eccezione del backup SQL, i processi di backup pianificati vengono inseriti al termine.
* Per il backup SQL, poiché i backup del log possono essere eseguiti ogni 15 minuti, le informazioni per tutti i processi di backup pianificati completati, inclusi i log, vengono inviate in batch e inviate ogni 6 ore.
* In tutte le soluzioni, altre informazioni, ad esempio l'elemento di backup, i criteri, i punti di ripristino, l'archiviazione e così via, vengono inviate almeno una volta al giorno.
* Una modifica nella configurazione di backup (ad esempio la modifica dei criteri o la modifica dei criteri) attiva un push di tutte le informazioni di backup correlate.

### <a name="how-long-can-i-retain-reporting-data"></a>Per quanto tempo posso conservare i dati di segnalazione?

Dopo aver creato un'area di lavoro LA, è possibile scegliere di conservare i dati per un massimo di 2 anni. Per impostazione predefinita, un'area di lavoro la conserva i dati per 31 giorni.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>Tutti i miei dati verranno visualizzati nei report dopo aver configurato l'area di lavoro di LA?

 Tutti i dati generati dopo aver configurato le impostazioni di diagnostica vengono inviati all'area di lavoro di LA ed è disponibile nei report. I processi in corso non vengono sottoposti a push per la creazione dei report. Al termine o all'esito negativo del processo, viene inviato ai report.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>È possibile visualizzare i report negli insiemi di credenziali e nelle sottoscrizioni?

Sì, è possibile visualizzare i report tra vault e sottoscrizioni, nonché regioni. I dati possono risiedere in una singola area di lavoro di LA o in un gruppo di aree di lavoro di LA.

### <a name="can-i-view-reports-across-tenants"></a>È possibile visualizzare i report tra gli inquilini?

Gli utenti di [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) con accesso delegato alle sottoscrizioni dei clienti o alle aree di lavoro DI La possono usare i rapporti di backup per visualizzare i dati in tutti i tenant.

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

Sì. Nelle seguenti situazioni, le notifiche non vengono inviate:

* Se le notifiche sono configurate su base oraria e un avviso viene generato e risolto entro l'ora
* Quando un processo viene annullato
* Se un secondo processo di backup non riesce perché è in corso il processo di backup originale

## <a name="next-steps"></a>Passaggi successivi

Leggere le altre domande frequenti:

* [Domande comuni](backup-azure-vm-backup-faq.md) sul backup della macchina virtuale di Azure.
* [Domande comuni](backup-azure-file-folder-backup-faq.md) sull'agente di Backup di Azure
