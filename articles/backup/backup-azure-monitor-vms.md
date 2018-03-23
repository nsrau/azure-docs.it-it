---
title: Monitorare gli avvisi di backup per le macchine virtuali di Azure | Microsoft Docs
description: Monitorare gli eventi e gli avvisi dai processi di backup delle macchine virtuali di Azure. Inviare messaggi di posta elettronica in base agli avvisi.
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: ''
ms.assetid: fed32015-2db2-44f8-b204-d89f6fd1bea2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: markgal;trinadhk;giridham;
ms.openlocfilehash: fbdce5c244d733a2978d473f01c8d875cbeaa65e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Monitorare gli avvisi per i backup della macchina virtuale di Azure
Gli avvisi sono risposte del servizio che informano che è stata raggiunta o superata la soglia di un evento. Sapere quando i problemi hanno inizio può essere determinante per contenere i costi aziendali. Gli avvisi in genere non seguono una pianificazione e quindi è utile sapere appena possibile quando un avviso viene generato. Quando ad esempio un processo di backup o ripristino non riesce, l'avviso verrà generato entro cinque minuti dall'errore. Nel dashboard dell'insieme di credenziali il riquadro Avvisi di backup visualizza gli eventi di livello critico e avviso. Nelle impostazioni di Avvisi di backup è possibile visualizzare tutti gli eventi. Ma cosa fare se un avviso viene generato mentre si lavora a un problema diverso? Non sapere quando l'avviso viene generato può essere un piccolo inconveniente o può compromettere i dati. Per verificare che chi di dovere sia informato dell'avviso e sappia quando viene generato, configurare il servizio per l'invio tramite posta elettronica di notifiche di avviso. Per informazioni dettagliate sulla configurazione di notifiche di posta elettronica, vedere [Configurare le notifiche](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Dove è possibile trovare informazioni sugli avvisi?
Per visualizzare informazioni sull'evento che ha generato un avviso, è necessario aprire la sezione Avvisi di backup. È possibile aprire la sezione Avvisi di backup in due modi: dal riquadro Avvisi di backup nel dashboard dell'insieme di credenziali o dalla sezione Avvisi ed eventi.

Per aprire il pannello Avvisi di backup dal riquadro Avvisi di backup:

* Nel riquadro **Avvisi di backup** nel dashboard dell'insieme di credenziali fare clic su **Critico** o **Avviso** per visualizzare gli eventi operativi per il livello di gravità in questione.

    ![Riquadro Avvisi di backup](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Per aprire il pannello Avvisi di backup dalla sezione Avvisi ed eventi:

1. Nel dashboard dell'insieme di credenziali fare clic su **Tutte le impostazioni**. ![Pulsante Tutte le impostazioni](./media/backup-azure-monitor-vms/all-settings-button.png)
2. Nel pannello **Impostazioni** fare clic su **Avvisi ed eventi**. ![Pulsante Avvisi ed eventi](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. Nel pannello **Avvisi ed eventi** fare clic su **Avvisi di backup**. ![Pulsante Avvisi di backup](./media/backup-azure-monitor-vms/backup-alerts.png)

    La sezione **Avvisi di backup** si apre e visualizza gli avvisi filtrati.

    ![Riquadro Avvisi di backup](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Per visualizzare informazioni dettagliate su un avviso specifico, nell'elenco di eventi fare clic sull'avviso per aprire la sezione **Dettagli**.

    ![Dettagli dell'evento](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Per personalizzare gli attributi visualizzati nell'elenco, vedere [Visualizzare altri attributi degli eventi](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>Configurare le notifiche
 È possibile configurare il servizio per l'invio di notifiche di posta elettronica per gli avvisi generati nell'ultima ora o quando si verificano tipi specifici di eventi.

Per configurare le notifiche di posta elettronica per gli avvisi

1. Scegliere **Configurare le notifiche**

    ![Menu Avvisi di backup](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    Si apre la sezione Configura notifiche.

    ![Pannello Configura notifiche](./media/backup-azure-monitor-vms/configure-notifications.png)
2. Nella sezione Configura notifiche fare clic su **Sì** per Notifiche tramite posta elettronica.

    Le finestre di dialogo Destinatari e Gravità presentano un asterisco perché tali informazioni sono obbligatorie. Specificare almeno un indirizzo di posta elettronica e selezionare almeno un valore per Gravità.
3. Nella finestra di dialogo **Destinatari (indirizzo di posta elettronica)** digitare gli indirizzi di posta elettronica delle persone che ricevono le notifiche. Usare il formato: username@domainname.com. Separare più indirizzi di posta elettronica con un punto e virgola (;).
4. Nell'area **Notifica** scegliere **Per ogni avviso** per inviare la notifica quando viene generato l'avviso specificato o **Riepilogo orario** per inviare un riepilogo dell'ora passata.
5. Nella finestra di dialogo **Gravità** scegliere uno o più livelli che dovranno attivare la notifica di posta elettronica.
6. Fare clic su **Save**.

   ### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Quali tipi di avviso sono disponibili per il backup di VM IaaS di Azure?
   | Livello avviso | Avvisi inviati |
   | --- | --- |
   | Critico | per errore di backup, errore di ripristino |
   | Avviso | per i processi di backup completati con avvisi, ad esempio in caso di errore di alcuni writer durante la creazione di uno snapshot |
   | Informazioni | attualmente non sono disponibili avvisi informativi per il backup di macchine virtuali di Azure |

### <a name="are-there-situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Esistono situazioni in cui il messaggio e-mail non viene inviato anche se sono configurate le notifiche?
Esistono situazioni in cui un avviso non viene inviato anche se le notifiche sono state configurate correttamente. Le notifiche di posta elettronica non vengono inviate nei casi seguenti per ridurre la frequenza degli avvisi:

* Se le notifiche sono configurate per il riepilogo orario e un avviso viene generato e risolto entro l'ora.
* Il processo viene annullato.
* Viene attivato un processo di backup che non riesce ed è in corso un altro processo di backup.
* Viene avviato un processo di backup pianificato per una macchina virtuale basata su Resource Manager, ma la macchina virtuale non esiste più.

## <a name="using-activity-logs-to-get-notifications-for-successful-backups"></a>Uso di log attività per ricevere notifiche di backup riusciti

Se si vuole ricevere una notifica al termine dei backup, è possibile usare avvisi basati sui [log attività](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) dell'insieme di credenziali.

### <a name="login-into-azure-portal"></a>Eseguire l'accesso al portale di Azure
Eseguire l'accesso al portale di Azure, passare all'insieme di credenziali pertinente di Servizi di ripristino di Azure e fare clic sulla sezione Log attività nelle proprietà.

### <a name="identify-appropriate-log"></a>Identificare il log appropriato

Applicare i filtri visualizzati nella figura seguente per verificare se si riceveranno log attività per i backup riusciti. Modificare l'intervallo di tempo in modo appropriato per visualizzare i record.

![Log attività](./media/backup-azure-monitor-vms/activity-logs-identify.png)

È possibile fare clic sul segmento JSON per ottenere altri dettagli e visualizzarli in un editor di testo tramite un'operazione di copia e incolla. Verranno visualizzati i dettagli dell'insieme di credenziali e l'elemento che ha attivato il log attività, ad esempio l'elemento di backup.

Fare quindi clic su Aggiungi avviso del log attività per generare avvisi per tutti questi log.

### <a name="add-activity-log-alert"></a>Aggiungi avviso del log attività

Facendo clic su Aggiungi avviso del log attività verrà visualizzata una schermata come quella illustrata di seguito.

![Avviso del log attività](./media/backup-azure-monitor-vms/activity-logs-alerts-successful.png)
    
La sottoscrizione e il gruppo di risorse vengono usati per archiviare l'avviso. I criteri saranno precompilati. Assicurarsi che tutti i valori siano pertinenti per il requisito.

Per i backup riusciti, il livello viene contrassegnato come Informativo e lo stato come Completato.

Se si seleziona una risorsa nel campo specifico illustrato in precedenza, l'avviso verrà generato quando i log attività vengono registrati per la risorsa o l'insieme di credenziali. Se si vuole che la regola sia applicabile a tutti gli insiemi di credenziali, lasciare vuoto il campo Risorsa.

### <a name="define-action-on-alert-firing"></a>Definire un'azione all'attivazione di un avviso

Usare il gruppo di azioni per definire l'azione dopo la generazione di un avviso. È possibile fare clic su Tipo di azione per altre informazioni sulle azioni disponibili, ad esempio posta elettronica, SMS, integrazione con Gestione dei servizi IT e così via.

![Gruppo di azioni del log attività](./media/backup-azure-monitor-vms/activity-logs-alerts-action-group.png)


Dopo aver fatto clic su OK, verrà generato un avviso del log attività e i successivi log attività registrati per i backup riusciti genereranno l'azione in base al valore definito nel gruppo di azioni.

### <a name="limitations-on-alerts"></a>Limitazioni per gli avvisi
Gli avvisi basati su eventi sono soggetti alle limitazioni seguenti:

1. Gli avvisi vengono attivati in tutte le macchine virtuali nell'insieme di credenziali di Servizi di ripristino. Non è possibile personalizzare l'avviso per un subset di macchine virtuali in un insieme di credenziali di Servizi di ripristino.
2. Gli avvisi vengono inviati da "alerts-noreply@mail.windowsazure.com". Attualmente non è possibile modificare il mittente del messaggio di posta elettronica.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come ricreare una macchina virtuale da un punto di ripristino, vedere [Ripristinare macchine virtuali in Azure](backup-azure-arm-restore-vms.md).

Per informazioni sulla protezione delle macchine virtuali, vedere [Primo approccio: Proteggere le VM di Azure con un insieme di credenziali dei servizi di ripristino](backup-azure-vms-first-look-arm.md). 

Per altre informazioni sulle attività di gestione per i backup delle macchine virtuali, vedere l'articolo [Gestire i backup delle macchine virtuali di Azure](backup-azure-manage-vms.md).
