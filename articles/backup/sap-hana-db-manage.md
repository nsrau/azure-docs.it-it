---
title: Gestire i database SAP HANA di cui è stato eseguito il backup nelle macchine virtuali di AzureManage backed up SAP HANA databases on Azure VMs
description: In questo articolo vengono appese attività comuni per la gestione e il monitoraggio dei database SAP HANA in esecuzione nelle macchine virtuali di Azure.In this article, learn common tasks for managing and monitoring SAP HANA databases that are running on Azure virtual machines.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 89fd7f23163d301817e767771257d9bc6f4ed526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480063"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Gestire e monitorare i database SAP HANA di cui è stato eseguito il backup

Questo articolo descrive le attività comuni per la gestione e il monitoraggio dei database SAP HANA in esecuzione in una macchina virtuale di Azure e di cui viene eseguito il backup in un insieme di credenziali di Azure Backup Recovery Services da parte del servizio Backup di Azure.This article describes common tasks for managing and monitoring SAP HANA databases that are running on an Azure virtual machine (VM) and that are backed up to an Azure Backup Recovery Services vault by the [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) service. Si apprenderà come monitorare processi e avvisi, attivare un backup su richiesta, modificare i criteri, arrestare e riprendere la protezione del database e annullare la registrazione di una macchina virtuale dai backup.

Se i backup non sono ancora stati configurati per i database SAP HANA, vedere Eseguire il backup dei [database SAP HANA nelle macchine virtuali](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)di Azure.

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorare i processi di backup manuale nel portaleMonitor manual backup jobs in the portal

Backup di Azure mostra tutti i processi attivati manualmente nella sezione Processi di backup nel portale di Azure.Azure Backup shows all manually triggered jobs in the **Backup jobs** section on Azure portal.

![Sezione Processi di backup](./media/sap-hana-db-manage/backup-jobs.png)

I processi visualizzati in questo portale includono l'individuazione e la registrazione dei database e le operazioni di backup e ripristino. In questa sezione non vengono visualizzati i processi pianificati, inclusi i backup del log. Anche i backup attivati manualmente dai client nativi SAP HANA (Studio/ Cockpit/ DBA Cockpit) non vengono visualizzati qui.

![Elenco processi di backup](./media/sap-hana-db-manage/backup-jobs-list.png)

Per altre informazioni sul monitoraggio, vedere [Monitoraggio nel portale](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) di Azure e Monitoraggio [tramite Monitoraggio di Azure.](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)

## <a name="view-backup-alerts"></a>Visualizzare gli avvisi di backup

Gli avvisi sono un semplice mezzo per monitorare i backup dei database SAP HANA. Gli avvisi ti aiutano a concentrarti sugli eventi che ti interessano di più senza perderti nella moltitudine di eventi che genera un backup. Backup di Azure consente di impostare gli avvisi e possono essere monitorati come segue:Azure Backup allows you to set alerts, and they can be monitored as follows:

* Accedere al [portale](https://portal.azure.com/)di Azure .
* Nel dashboard dell'insieme di credenziali selezionare **Avvisi di backup**.

  ![Avvisi di backup nel dashboard dell'insieme di credenziali](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Sarete in grado di vedere gli avvisi:

  ![Elenco degli avvisi di backup](./media/sap-hana-db-manage/backup-alerts-list.png)

* Fare clic sugli avvisi per visualizzare ulteriori dettagli:

  ![Dettagli dell'avviso](./media/sap-hana-db-manage/alert-details.png)

Oggi, Backup di Azure consente l'invio di avvisi tramite posta elettronica. Questi avvisi sono:

* Attivato per tutti gli errori di backup.
* Consolidato a livello di database in base al codice di errore.
* Inviato solo per il primo errore di backup di un database.

Per altre informazioni sul monitoraggio, vedere [Monitoraggio nel portale](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) di Azure e Monitoraggio [tramite Monitoraggio di Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="management-operations"></a>Operazioni di gestione

Backup di Azure semplifica la gestione di un database SAP HANA di cui è stato eseguito il backup, con un'abbondanza di operazioni di gestione supportate. Queste operazioni vengono descritte in modo più dettagliato nelle sezioni seguenti.

### <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

I backup vengono eseguiti in base alla pianificazione dei criteri. È possibile eseguire un backup su richiesta come segue:

1. Selezionare **Elementi di backup** dal menu dell'insieme di credenziali.
2. In **Elementi di backup**selezionare la macchina virtuale che esegue il database SAP HANA e quindi fare clic su Backup **ora**.
3. In **Backup ora**, utilizzare il controllo calendario per selezionare l'ultimo giorno in cui deve essere mantenuto il punto di ripristino. Fare quindi clic su **OK**.
4. Monitorare le notifiche del portale. È possibile monitorare l'avanzamento del processo nel dashboard dell'insieme di credenziali > **processi** > di backup**in corso.** A seconda delle dimensioni del database, la creazione del backup iniziale potrebbe richiedere un po' di tempo.

### <a name="hana-native-client-integration"></a>Integrazione client nativa HANA

Ora i backup completi su richiesta attivati da uno qualsiasi dei client nativi HANA verranno visualizzati come backup completo nella pagina Elementi di **backup.**

![Ultimi backup eseguiti](./media/sap-hana-db-manage/last-backups.png)

Questi backup completi ad hoc verranno visualizzati anche nell'elenco dei punti di ripristino per il ripristino.

![Elenco dei punti di ripristino](./media/sap-hana-db-manage/list-restore-points.png)

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Eseguire il backup del client nativo SAP HANA in un database con il backup di Azure abilitatoRun SAP HANA native client backup on a database with Azure backup enabled

Se si vuole eseguire un backup locale (utilizzando HANA Studio / Cockpit) di un database di cui viene eseguito il backup con Backup di Azure, eseguire le operazioni seguenti:

1. Attendere il completamento dei backup completi o del log per il completamento del database. Controllare lo stato in SAP HANA Studio/Cockpit.
2. Disabilitare i backup del log e impostare il catalogo di backup sul file system per il database pertinente.
3. A tale scopo, fare doppio clic su **systemdb** > **Configuration** > **Select Database** > **Filter (Log)**.
4. Impostare **enable_auto_log_backup** su **No**.
5. Impostare **log_backup_using_backint** **su False**.
6. Eseguire un backup completo su richiesta del database.
7. Attendere il completamento del backup completo e del backup del catalogo.
8. Ripristinare le impostazioni precedenti a quelle per Azure:Revert the previous settings back to those for Azure:
   * Impostare **enable_auto_log_backup** **su Sì**.
   * Impostare **log_backup_using_backint** su **True**.

### <a name="change-policy"></a>Modificare i criteri

È possibile modificare i criteri sottostanti per un elemento di backup SAP HANA.

* Nel dashboard dell'insieme di credenziali, passare a **Elementi di backup:**

  ![Selezionare gli elementi di backup](./media/sap-hana-db-manage/backup-items.png)

* Scegliere SAP HANA nella macchina virtuale di **AzureChoose SAP HANA in Azure VM**

  ![Scegliere SAP HANA nella macchina virtuale di AzureChoose SAP HANA in Azure VM](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Scegliere l'elemento di backup di cui si desidera modificare i criteri sottostanti
* Fare clic sul criterio di backup esistente

  ![Selezionare i criteri di backup esistenti](./media/sap-hana-db-manage/existing-backup-policy.png)

* Modificare il criterio scegliendo dall'elenco. Se necessario, [creare un nuovo criterio di backup.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy)

  ![Scegli criterio da elenco a discesa](./media/sap-hana-db-manage/choose-backup-policy.png)

* Salvare le modifiche

  ![Salvare le modifiche](./media/sap-hana-db-manage/save-changes.png)

* La modifica dei criteri influirà su tutti gli elementi di backup associati e attiverà i processi di protezione di **configurazione** corrispondenti.

>[!NOTE]
> Qualsiasi cambiamento nel periodo di conservazione verrà applicato retroattivamente a tutti i vecchi punti di ripristino oltre a quelli nuovi.
>
> I criteri di backup incrementale non possono essere utilizzati per i database SAP HANA. Il backup incrementale non è attualmente supportato per questi database.

### <a name="modify-policy"></a>Modifica criterio

Modificare i criteri per modificare i tipi di backup, le frequenze e l'intervallo di conservazione.

>[!NOTE]
>Qualsiasi cambiamento nel periodo di conservazione verrà applicato retroattivamente a tutti i punti di ripristino meno recenti, oltre a quelli nuovi.

1. Nel dashboard dell'insieme di credenziali passare a **Gestisci > criteri di backup** e scegliere il criterio da modificare.

   ![Scegliere il criterio da modificare](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Selezionare **Modifica**.

   ![Selezionare Modifica](./media/sap-hana-db-manage/modify-policy.png)

1. Scegliere la frequenza per i tipi di backup.

   ![Scegliere la frequenza di backup](./media/sap-hana-db-manage/choose-frequency.png)

La modifica dei criteri influirà su tutti gli elementi di backup associati e attiverà i processi di protezione di **configurazione** corrispondenti.

### <a name="inconsistent-policy"></a>Politica incoerente

In alcuni casi, un'operazione di modifica dei criteri può portare a una versione dei criteri **incoerente** per alcuni elementi di backup. Ciò si verifica quando il processo di **protezione di configurazione** corrispondente ha esito negativo per l'elemento di backup dopo l'attivazione di un'operazione di modifica dei criteri. Viene visualizzato come segue nella visualizzazione degli elementi di backup:

![Politica incoerente](./media/sap-hana-db-manage/inconsistent-policy.png)

È possibile correggere la versione dei criteri per tutti gli elementi interessati in un solo clic:You can fix the policy version for all impacted items in one click:

![Correggere la versione dei criteriFix policy version](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Arrestare la protezione per un database SAP HANA

È possibile arrestare la protezione di un database SAP HANA in due modi:

* Interrompere tutti i processi di backup futuri ed eliminare tutti i punti di recupero.
* Arrestare tutti i processi di backup futuri mantenendo tuttavia invariati i punti di ripristino.

Se si sceglie di lasciare invariati i punti di ripristino, tenere presente quanto segue:

* Tutti i punti di ripristino rimarranno invariati per sempre, tutte le eliminazioni verranno arrestate in caso di arresto della protezione con conservazione dei dati.
* Verranno addebitati i addebiti per l'istanza protetta e l'archiviazione utilizzata. Per altre informazioni, vedere Prezzi di Backup di [Azure.For more information,](https://azure.microsoft.com/pricing/details/backup/)see Azure Backup pricing .
* Se si elimina un'origine dati senza arrestare i backup, i nuovi backup avranno esito negativo.

Per interrompere la protezione per un database:

* Nel dashboard dell'insieme di credenziali, selezionare **Elementi di backup.**
* In **Tipo di gestione backup**selezionare SAP **HANA nella macchina virtuale** di Azure

  ![Selezionare SAP HANA nella macchina virtuale di AzureSelect SAP HANA in Azure VM](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Selezionare il database per il quale si desidera interrompere la protezione:

  ![Selezionare il database per arrestare la protezione](./media/sap-hana-db-manage/select-database.png)

* Nel menu del database, selezionare **Interrompi backup**.

  ![Selezionare Interrompi backup](./media/sap-hana-db-manage/stop-backup.png)

* Nel menu **Interrompi backup,** scegliere se conservare o eliminare i dati. Se lo si desidera, fornire un motivo e un commento.

  ![Selezionare Conservare o eliminare i dati](./media/sap-hana-db-manage/retain-backup-data.png)

* Selezionare **Interrompi backup**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Riprendere la protezione per un database SAP HANAResume protection for an SAP HANA database

Quando si interrompe la protezione per il database SAP HANA, se si seleziona l'opzione **Mantieni dati di backup,** è possibile riprendere la protezione in un secondo momento. Se non si conservano i dati di cui è stato eseguito il backup, non sarà possibile riprendere la protezione.

Per riprendere la protezione per un database SAP HANA:

* Aprire l'elemento di backup e selezionare **Riprendi backup**.

   ![Selezionare il backup del curriculum](./media/sap-hana-db-manage/resume-backup.png)

* Selezionare un criterio dal menu **Criteri di backup** e quindi selezionare **Salva**.

### <a name="upgrading-from-sap-hana-10-to-20"></a>Aggiornamento da SAP HANA 1.0 a 2.0

Informazioni su come continuare il backup per un database SAP HANA [dopo l'aggiornamento da SAP HANA 1.0 a 2.0.](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20)

### <a name="upgrading-without-a-sid-change"></a>Aggiornamento senza modifica del SID

Informazioni su come continuare il backup di un database SAP HANA il cui [SID non è stato modificato dopo l'aggiornamento.](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change)

### <a name="unregister-an-sap-hana-instance"></a>Annullare la registrazione di un'istanza SAP HANAUnregister an SAP HANA instance

Annullare la registrazione di un'istanza SAP HANA dopo aver disabilitato la protezione ma prima di eliminare l'insieme di credenziali:

* Nel dashboard dell'insieme di credenziali, in **Gestisci,** selezionare **Infrastruttura di backup**.

   ![Selezionare Infrastruttura di backup](./media/sap-hana-db-manage/backup-infrastructure.png)

* Selezionare il tipo di gestione del backup come carico di lavoro nella macchina virtuale di **AzureSelect** the Backup Management **type** as Workload in Azure VM

   ![Selezionare il tipo di gestione del backup come carico di lavoro nella macchina virtuale di AzureSelect the Backup Management type as Workload in Azure VM](./media/sap-hana-db-manage/backup-management-type.png)

* In **Server protetti**selezionare l'istanza di cui annullare la registrazione. Per eliminare l'insieme di credenziali, è necessario annullare la registrazione di tutti i server/istanze.

* Fare clic con il pulsante destro del mouse sull'istanza protetta e scegliere **Annulla registrazione**.

   ![Selezionare unregister](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Registrare nuovamente l'estensione nella macchina virtuale del server SAP HANARe-register extension on the SAP HANA server VM

A volte l'estensione del carico di lavoro nella macchina virtuale può avere un impatto per un motivo o un altro. In questi casi, tutte le operazioni attivate nella macchina virtuale inizieranno a non riuscire. Potrebbe quindi essere necessario registrare nuovamente l'estensione nella macchina virtuale. L'operazione di nuovo registrazione reinstalla l'estensione di backup del carico di lavoro nella macchina virtuale per continuare le operazioni.

Usare questa opzione con cautela: quando viene attivata in una macchina virtuale con un'estensione già integra, questa operazione causerà il riavvio dell'estensione. Ciò potrebbe causare l'esito negativo di tutti i processi in corso. Verificare la presenza di uno o più [sintomi](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) prima di attivare l'operazione di registrazione di nuovo.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [risolvere i problemi comuni durante il backup dei database SAP HANA.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
