---
title: Gestire i backup dei database SAP HANA su macchine virtuali di Azure
description: Questo articolo illustra alcune attività comuni per la gestione e il monitoraggio di database SAP HANA in esecuzione su macchine virtuali di Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: e257aa7771f6f76a4d53f16255c2f3cbb80c8967
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89377455"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Gestire e monitorare i database SAP HANA di cui è stato eseguito il backup

Questo articolo descrive le attività principali di gestione e monitoraggio dei database SAP HANA di cui è stato eseguito il backup in un insieme di credenziali di Servizi di ripristino di Backup di Azure e in esecuzione su una macchina virtuale di Azure, con il servizio [Backup di Azure](./backup-overview.md). Si apprenderà come monitorare processi e avvisi, attivare un backup su richiesta, modificare i criteri, arrestare e riprendere la protezione del database e annullare la registrazione di una macchina virtuale dai backup.

Se non sono ancora stati configurati backup per i database SAP HANA, vedere l'articolo [Eseguire il backup di database SAP HANA in macchine virtuali di Azure](./backup-azure-sap-hana-database.md).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorare i processi di backup manuali nel portale

Backup di Azure consente di visualizzare tutti i processi attivati manualmente nella sezione **Processi di backup** sul portale di Azure.

![Sezione Processi di backup](./media/sap-hana-db-manage/backup-jobs.png)

I processi visualizzati in questo portale includono l'individuazione e la registrazione dei database, nonché le operazioni di backup e ripristino. I processi pianificati, inclusi i backup del log, non sono illustrati in questa sezione. Neanche i backup attivati manualmente da client nativi di SAP HANA, ovvero Studio/Cockpit/DBA Cockpit, vengono visualizzati qui.

![Elenco dei processi di backup](./media/sap-hana-db-manage/backup-jobs-list.png)

Per altre informazioni sul monitoraggio, vedere l'articolo [Monitoring in the Azure portal](./backup-azure-monitoring-built-in-monitor.md) (Monitoraggio nel portale di Azure) e [Monitoring using Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md) (Eseguire il monitoraggio con Monitoraggio di Azure).

## <a name="view-backup-alerts"></a>Visualizzare gli avvisi di backup

Gli avvisi rappresentano un modo semplice per monitorare i backup dei database SAP HANA. Gli avvisi consentono di concentrarsi sugli eventi a cui si è maggiormente interessati senza perdersi nella moltitudine di eventi generati da un backup. Backup di Azure consente di impostare gli avvisi, che possono essere monitorati come segue:

* Accedere al [portale di Azure](https://portal.azure.com/).
* Nel dashboard dell'insieme di credenziali, selezionare **Avvisi di backup**.

  ![Avvisi di backup nel dashboard dell'insieme di credenziali](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Sarà possibile visualizzare gli avvisi:

  ![Elenco di avvisi di backup](./media/sap-hana-db-manage/backup-alerts-list.png)

* Selezionare gli avvisi per visualizzare altri dettagli:

  ![Dettagli dell'avviso](./media/sap-hana-db-manage/alert-details.png)

Attualmente, Backup di Azure consente l'invio di avvisi tramite posta elettronica. Tali avvisi sono:

* Attivati per tutti gli errori di backup.
* Consolidati a livello di database in base al codice di errore.
* Inviati solo per il primo errore di backup di un database.

Per altre informazioni sul monitoraggio, vedere l'articolo [Monitoring in the Azure portal](./backup-azure-monitoring-built-in-monitor.md) (Monitoraggio nel portale di Azure) e [Monitoring using Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md) (Eseguire il monitoraggio con Monitoraggio di Azure).

## <a name="management-operations"></a>Operazioni di gestione

Backup di Azure consente di semplificare la gestione di un database SAP HANA sottoposto a backup, grazie all'ampia varietà di operazioni di gestione supportate. Tali operazioni sono illustrate in modo più dettagliato nelle sezioni successive.

### <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

I backup vengono eseguiti in base alla pianificazione dei criteri. È possibile eseguire un backup su richiesta nel modo seguente:

1. Nel menu dell'insieme di credenziali selezionare **elementi di backup**.
2. In **elementi di backup**selezionare la macchina virtuale che esegue il database di SAP Hana, quindi selezionare **Esegui backup ora**.
3. In **backup ora**scegliere il tipo di backup che si desidera eseguire. Quindi scegliere **OK**. Questo backup verrà mantenuto in base ai criteri associati a questo elemento di backup.
4. Monitorare le notifiche del portale. È possibile monitorare l'avanzamento del processo nel dashboard dell'insieme di credenziali > **Processi di Backup** > **In corso**. A seconda delle dimensioni del database, la creazione del backup iniziale potrebbe richiedere un po' di tempo.

Per impostazione predefinita, la conservazione dei backup su richiesta è di 45 giorni.

### <a name="hana-native-client-integration"></a>Integrazione di client nativi HANA

#### <a name="backup"></a>Backup

I backup su richiesta attivati da uno dei client nativi HANA (per **Backint**) viene visualizzato nell'elenco backup della pagina **Elementi di backup**.

![Ultimi backup eseguiti](./media/sap-hana-db-manage/last-backups.png)

È anche possibile [monitorare questi backup](#monitor-manual-backup-jobs-in-the-portal) dalla pagina **Processi di backup**.

Questi backup su richiesta vengono visualizzati anche nell'elenco dei punti di ripristino per il ripristino.

![Elenco dei punti di ripristino](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>Restore

I ripristini generati da client nativi HANA (tramite **Backint**) per eseguire il ripristino nello stesso computer possono essere [monitorati](#monitor-manual-backup-jobs-in-the-portal) dalla pagina **Processi di backup**.

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Eseguire SAP HANA backup di Native client in un database con backup di Azure abilitato

Per eseguire il backup locale (tramite HANA Studio/Cockpit) di un database di cui è stato eseguito il backup con Backup di Azure:

1. Attendere il completamento di tutti i backup del log o completi per il database. Verificare lo stato in SAP HANA Studio/Cockpit.
2. Disabilitare i backup del log e impostare il catalogo di backup sul file system per il database pertinente.
3. A tal fine, fare doppio clic su **systemdb** > **Configurazione** > **Seleziona database** > **Filtro (log)** .
4. Impostare **enable_auto_log_backup** su **No**.
5. Impostare **log_backup_using_backint** su **False**.
6. Eseguire un backup completo su richiesta del database.
7. Attendere il completamento del backup completo e del catalogo.
8. Ripristinare le impostazioni precedenti a quelle per Azure:
   * Impostare **enable_auto_log_backup** su **Yes**.
   * Impostare **log_backup_using_backint** su **True**.

### <a name="change-policy"></a>Modificare i criteri

È possibile modificare i criteri sottostanti per un elemento di backup SAP HANA.

* Nel dashboard dell'insieme di credenziali, passare a **Elementi di backup**:

  ![Selezionare elementi di backup](./media/sap-hana-db-manage/backup-items.png)

* Scegliere **SAP HANA in una macchina virtuale di Azure**

  ![Scegliere SAP HANA in una macchina virtuale di Azure](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Scegliere l'elemento di backup di cui si desidera modificare i criteri sottostanti
* Selezionare i criteri di backup esistenti.

  ![Selezionare i criteri di backup esistenti](./media/sap-hana-db-manage/existing-backup-policy.png)

* Modificare i criteri scegliendoli dall'elenco. Se necessario, [creare un nuovo criterio di backup](./backup-azure-sap-hana-database.md#create-a-backup-policy).

  ![Scegliere un criterio dall'elenco a discesa](./media/sap-hana-db-manage/choose-backup-policy.png)

* Salvare le modifiche

  ![Salvare le modifiche](./media/sap-hana-db-manage/save-changes.png)

* La modifica dei criteri ha effetto su tutti gli elementi di backup associati e attiva processi **Configura protezione** corrispondenti.

>[!NOTE]
> Eventuali modifiche apportate al periodo di conservazione verranno applicate in modo retrospettivo a tutti i punti di ripristino meno recenti oltre a quelli nuovi.
>
> Non è possibile usare i criteri di backup incrementali per i database SAP HANA. Il backup incrementale non è attualmente supportato per questi database.

### <a name="modify-policy"></a>Modifica criterio

Modificare i criteri per modificare i tipi di backup, le frequenze e il periodo di mantenimento dati.

>[!NOTE]
>Eventuali modifiche apportate al periodo di conservazione verranno applicate in modo retroattivo a tutti i punti di ripristino precedenti, oltre a quelli nuovi.

1. Nel dashboard dell'insieme di credenziali, passare a **Gestisci > Criteri di backup** e scegliere i criteri da modificare.

   ![Scegliere i criteri da modificare](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Selezionare **Modifica**.

   ![Selezionare Modifica](./media/sap-hana-db-manage/modify-policy.png)

1. Scegliere la frequenza per i diversi tipi di backup.

   ![Scegliere la frequenza di backup](./media/sap-hana-db-manage/choose-frequency.png)

La modifica dei criteri ha effetto su tutti gli elementi di backup associati e attiva processi **Configura protezione** corrispondenti.

### <a name="inconsistent-policy"></a>Criteri non coerenti

Occasionalmente, un'operazione di modifica dei criteri può comportare una versione dei criteri **incoerente** per alcuni elementi di backup. Ciò si verifica quando il criterio **Configura protezione** corrispondente ha esito negativo per l'elemento di backup dopo l'attivazione di un'operazione di modifica dei criteri. Nella vista dell'elemento di backup viene visualizzato quanto segue:

![Criteri non coerenti](./media/sap-hana-db-manage/inconsistent-policy.png)

È possibile correggere la versione dei criteri per tutti gli elementi interessati con un solo clic:

![Correggere la versione dei criteri](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Arrestare la protezione per un database SAP HANA

È possibile arrestare la protezione di un database SAP HANA in due modi:

* Interrompere tutti i processi di backup futuri ed eliminare tutti i punti di recupero.
* Arrestare tutti i processi di backup futuri mantenendo tuttavia invariati i punti di ripristino.

Se si sceglie di lasciare invariati i punti di ripristino, tenere presente quanto segue:

* Tutti i punti di ripristino rimarranno invariati per sempre e tutte le eliminazioni verranno arrestate in caso di arresto della protezione con conservazione dei dati.
* Verranno addebitati i costi per l'istanza protetta e lo spazio di archiviazione utilizzato. Per altre informazioni, vedere [Prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).
* Se si elimina un'origine dati senza arrestare i backup, i nuovi backup avranno esito negativo.

Per interrompere la protezione per un database:

* Nel dashboard dell'insieme di credenziali, selezionare **Elementi di backup**.
* In **Tipo di gestione dei backup**, selezionare **SAP HANA in una macchina virtuale di Azure**

  ![Selezionare SAP HANA in una macchina virtuale di Azure](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Selezionare il database su cui si desidera interrompere la protezione:

  ![Selezionare il database su cui interrompere la protezione](./media/sap-hana-db-manage/select-database.png)

* Nel menu del database, selezionare **Interrompi backup**.

  ![Selezionare Interrompi backup](./media/sap-hana-db-manage/stop-backup.png)

* Nel menu **Interrompi Backup**, scegliere se mantenere o eliminare i dati. L'aggiunta di un motivo e commento è facoltativa.

  ![Selezionare Conserva dati o Elimina dati](./media/sap-hana-db-manage/retain-backup-data.png)

* Selezionare **Interrompi backup**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Riprendere la protezione per un database SAP HANA

Quando si arresta la protezione per il database SAP HANA, selezionando l'opzione **Conserva i dati di backup** è possibile riprendere la protezione in un secondo momento. Se non si conservano i dati di cui è stato eseguito il backup, non è possibile riprendere la protezione.

Per riprendere la protezione per un database SAP HANA:

* Aprire l'elemento di backup e selezionare **Riprendi backup**.

   ![Selezionare Riprendi backup](./media/sap-hana-db-manage/resume-backup.png)

* Selezionare un criterio dal menu **Criteri di backup** e quindi selezionare **Salva**.

### <a name="upgrading-from-sdc-to-mdc"></a>Aggiornamento da SDC a MDC

Informazioni su come continuare il backup per un database SAP HANA [dopo l'aggiornamento da SDC a MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid).

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>Aggiornamento da SDC a MDC senza modifiche al SID

Informazioni su come continuare il backup di un database di SAP HANA il cui [SID non è stato modificato dopo l'aggiornamento da SDC a MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid).

### <a name="unregister-an-sap-hana-instance"></a>Annullare la registrazione di un'istanza di SAP HANA

Annullare la registrazione di un'istanza di SAP HANA dopo aver disabilitato la protezione, ma prima di eliminare l'insieme di credenziali:

* Nel dashboard dell'insieme di credenziali, in **Gestisci**, selezionare **Infrastruttura di backup**.

   ![Selezionare Infrastruttura di backup](./media/sap-hana-db-manage/backup-infrastructure.png)

* Per **Tipo di gestione di backup**, selezionare **Workload in Azure VM** (Carico di lavoro in una macchina virtuale di Azure)

   ![Per Tipo di gestione di backup, selezionare Workload in Azure VM (Carico di lavoro in una macchina virtuale di Azure)](./media/sap-hana-db-manage/backup-management-type.png)

* In **Server protetti**, selezionare l'istanza di cui si vuole annullare la registrazione. Per eliminare l'insieme di credenziali, è necessario annullare la registrazione di tutti i server/le istanze.

* Fare clic con il pulsante destro del mouse sull'istanza protetta e selezionare **Annulla registrazione**.

   ![Selezionare Annulla registrazione](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Registrare nuovamente l'estensione sulla macchina virtuale del server SAP HANA

A volte l'estensione del carico di lavoro nella macchina virtuale può essere interessata per un motivo o un altro. In questi casi, tutte le operazioni attivate nella macchina virtuale inizieranno ad avere esito negativo. Potrà quindi essere necessario registrare nuovamente l'estensione nella macchina virtuale. L'operazione di ripetizione della registrazione reinstalla l'estensione di backup del carico di lavoro nella macchina virtuale affinché le operazioni possano procedere.

Usare questa opzione con cautela: quando viene attivata in una macchina virtuale con un'estensione già integra, questa operazione causa il riavvio dell'estensione. Ciò può comportare l'esito negativo di tutti i processi in corso. Prima di attivare l'operazione di ripetizione della registrazione, verificare la presenza di uno o più [sintomi](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [risolvere i problemi comuni che si verificano durante il backup di database SAP HANA.](./backup-azure-sap-hana-database-troubleshoot.md)
