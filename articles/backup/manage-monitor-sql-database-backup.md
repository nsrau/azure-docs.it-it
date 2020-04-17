---
title: Gestire e monitorare i database di SQL Server in una macchina virtuale di AzureManage and monitor SQL Server DBs on an Azure VM
description: Questo articolo descrive come gestire e monitorare i database di SQL Server in esecuzione in una macchina virtuale di Azure.This article describes how to manage and monitor SQL Server databases that are running on an Azure VM.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 14e3a4797fe60a3d1857f1e6d947fa0c669bdcfe
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537305"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gestire e monitorare i database SQL Server di cui è stato eseguito il backup

Questo articolo descrive le attività comuni per la gestione e il monitoraggio dei database di SQL Server in esecuzione in una macchina virtuale di Azure e di cui viene eseguito il backup in un insieme di credenziali di Azure Backup Recovery Services da parte del servizio Backup di Azure.This article describes common tasks for managing and monitoring SQL Server databases that are running on an Azure virtual machine (VM) and that are backed up to an Azure Backup Recovery Services vault by the [Azure Backup](backup-overview.md) service. Verrà illustrato come monitorare processi e avvisi, arrestare e riprendere la protezione del database, eseguire processi di backup e annullare la registrazione di una macchina virtuale dai backup.

Se non sono ancora stati configurati i backup per i database di SQL Server, vedere Eseguire il backup dei database di SQL Server nelle macchine virtuali di AzureIf you haven't yet configured backups for your SQL Server databases, see [Back up SQL Server databases on Azure VMs](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorare i processi di backup manuale nel portaleMonitor manual backup jobs in the portal

Backup di Azure mostra tutti i processi attivati manualmente nel portale Processi di **backup.** I processi visualizzati in questo portale includono l'individuazione e la registrazione dei database e le operazioni di backup e ripristino.

![Portale Processi di backup](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Nel portale **Processi di** backup non vengono visualizzati i processi di backup pianificati. Usare SQL Server Management Studio per monitorare i processi di backup pianificati, come descritto nella sezione successiva.
>

Per informazioni dettagliate sugli scenari di monitoraggio, vedere [Monitoraggio nel portale](backup-azure-monitoring-built-in-monitor.md) di Azure e Monitoraggio [tramite Monitoraggio di Azure.](backup-azure-monitoring-use-azuremonitor.md)  

## <a name="view-backup-alerts"></a>Visualizzare gli avvisi di backup

Con i backup del log eseguiti ogni 15 minuti, monitorare i processi di backup può essere faticoso. Backup di Azure semplifica il monitoraggio inviando avvisi tramite posta elettronica. Gli avvisi e-mail sono:

- Attivato per tutti gli errori di backup.
- Consolidato a livello di database in base al codice di errore.
- Inviato solo per il primo errore di backup di un database.

Per monitorare gli avvisi di backup del database:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel dashboard dell'insieme di credenziali, selezionare **Avvisi ed eventi**.

   ![Selezionare Avvisi ed eventi](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. In **Avvisi ed eventi**, selezionare **avvisi di Backup**.

   ![Selezionare Avvisi di backup](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Interrompere la protezione di un database di SQL Server

È possibile interrompere i backup di un database di SQL Server in due modi:

- Interrompere tutti i processi di backup futuri ed eliminare tutti i punti di recupero.
- Arrestare tutti i processi di backup futuri e lasciare intatti i punti di ripristino.

Se si sceglie di lasciare invariati i punti di ripristino, tenere presente quanto segue:

- Tutti i punti di ripristino rimarranno invariati per sempre, tutte le eliminazioni verranno arrestate in caso di arresto della protezione con conservazione dei dati.
- Verranno addebitati i addebiti per l'istanza protetta e l'archiviazione utilizzata. Per altre informazioni, vedere Prezzi di Backup di [Azure.For more information,](https://azure.microsoft.com/pricing/details/backup/)see Azure Backup pricing .
- Se si elimina un'origine dati senza arrestare i backup, i nuovi backup avranno esito negativo. I punti di ripristino precedenti scadranno in base ai criteri, ma un ultimo punto di ripristino verrà sempre mantenuto fino a quando non si interrompono i backup e si eliminano i dati.

Per interrompere la protezione per un database:

1. Nel dashboard dell'insieme di credenziali, selezionare **Elementi di backup.**

2. In **Tipo di gestione backup**selezionare SQL in VM di **Azure.**

    ![Selezionare SQL nella macchina virtuale di Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Selezionare il database per cui si desidera interrompere la protezione.

    ![Selezionare il database per interrompere la protezione](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. Nel menu del database, selezionare **Interrompi backup**.

    ![Selezionare Interrompi backup](./media/backup-azure-sql-database/stop-db-button.png)

5. Nel menu **Interrompi backup,** scegliere se conservare o eliminare i dati. Se lo si desidera, fornire un motivo e un commento.

    ![Conservare o eliminare i dati nel menu Interrompi backup](./media/backup-azure-sql-database/stop-backup-button.png)

6. Selezionare **Interrompi backup**.

> [!NOTE]
>
>Per ulteriori informazioni sull'opzione di eliminazione dei dati, vedere le domande frequenti riportate di seguito:For more information about the delete data option, see the FAQ below:
>
>- [Se si elimina un database da un'istanza protetta automatica, cosa accadrà ai backup?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Se si interrompe il funzionamento di backup di un database autoprotected quale sarà il suo comportamento?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Riprendere la protezione per un database SQL

Quando si interrompe la protezione per il database SQL, se si seleziona l'opzione Mantieni dati di **backup,** è possibile riprendere la protezione in un secondo momento. Se non si mantengono i dati di backup, non è possibile riprendere la protezione.

Per riprendere la protezione per un database SQL:

1. Aprire l'elemento di backup e selezionare **Riprendi backup**.

    ![Selezionare Riprendi backup per riprendere la protezione del database](./media/backup-azure-sql-database/resume-backup-button.png)

2. Selezionare un criterio dal menu **Criteri di backup** e quindi selezionare **Salva**.

## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

È possibile eseguire diversi tipi di backup su richiesta:

- Backup completo
- Backup completo solo copia
- Backup differenziale
- Backup dei log

Sebbene sia necessario specificare la durata di conservazione per il backup completo di sola copia, l'intervallo di conservazione per il backup completo su richiesta verrà impostato automaticamente su 45 giorni dall'ora corrente.

Per altre informazioni, vedere [Tipi di backup di SQL Server](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Annullare un’istanza SQL &Server

Annullare la registrazione di un'istanza di SQL Server dopo aver disabilitato la protezione ma prima di eliminare l'insieme di credenziali:

1. Nel dashboard dell'insieme di credenziali, in **Gestisci,** selezionare **Infrastruttura di backup**.  

   ![Selezionare Infrastruttura di backup](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. In **Server di gestione** selezionare **Server protetti**.

   ![Selezionare Server protetti](./media/backup-azure-sql-database/protected-servers.png)

3. In **Server protetti** selezionare il server di cui si vuole annullare la registrazione. Per eliminare l'insieme di credenziali, è necessario annullare la registrazione di tutti i server.

4. Fare clic con il pulsante destro del mouse sul server protetto e scegliere **Annulla registrazione**.

   ![Selezionare Elimina](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="modify-policy"></a>Modifica dei criteri

Modificare i criteri per modificare la frequenza di backup o l'intervallo di conservazione.

> [!NOTE]
> Qualsiasi cambiamento nel periodo di conservazione verrà applicato retroattivamente a tutti i vecchi punti di ripristino oltre a quelli nuovi.

Nel dashboard dell'insieme di credenziali passare a **Gestisci** > criteri di**backup** e scegliere il criterio da modificare.

  ![Gestire i criteri di backup](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Modificare i criteri di backup](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

La modifica dei criteri influirà su tutti gli elementi di backup associati e attiverà i processi di protezione di **configurazione** corrispondenti.

### <a name="inconsistent-policy"></a>Politica incoerente

In alcuni modi, un'operazione di modifica dei criteri può portare a una versione dei criteri **incoerente** per alcuni elementi di backup. Ciò si verifica quando il processo di **protezione di configurazione** corrispondente ha esito negativo per l'elemento di backup dopo l'attivazione di un'operazione di modifica dei criteri. Viene visualizzato come segue nella visualizzazione degli elementi di backup:

  ![Politica incoerente](./media/backup-azure-sql-database/inconsistent-policy.png)

È possibile correggere la versione dei criteri per tutti gli elementi interessati in un solo clic:You can fix the policy version for all impacted items in one click:

  ![Correggere i criteri incoerenti](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Registrare nuovamente l'estensione nella macchina virtuale di SQL ServerRe-register extension on the SQL Server VM

In alcuni stati, l'estensione del carico di lavoro nella macchina virtuale può influire su un motivo o per l'altro. In questi casi, tutte le operazioni attivate nella macchina virtuale inizieranno a non riuscire. Potrebbe quindi essere necessario registrare nuovamente l'estensione nella macchina virtuale. **L'operazione** di nuovo registrazione reinstalla l'estensione di backup del carico di lavoro nella macchina virtuale per continuare le operazioni.

Utilizzare questa opzione con cautela; Quando viene attivata in una macchina virtuale con un'estensione già integra, questa operazione causerà il riavvio dell'estensione. Ciò può comportare l'esito negativo di tutti i processi in corso. Si deseleziona la presenza di uno o più [sintomi](backup-sql-server-azure-troubleshoot.md#re-registration-failures) prima di attivare l'operazione di reregistrazione.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere [Risolvere i problemi relativi ai backup in un database](backup-sql-server-azure-troubleshoot.md)di SQL Server.
