---
title: Gestire e monitorare i database di SQL Server in una VM di Azure sottoposti a backup per Backup di Azure | Microsoft Docs
description: Questo articolo descrive come gestire e monitorare i database di SQL Server in esecuzione in una VM di Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: raynew
ms.openlocfilehash: ea5495867d5f453db014e000e01d533d049dc628
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787732"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gestire e monitorare i database SQL Server di cui è stato eseguito il backup

Questo articolo vengono descritte attività comuni per la gestione e monitoraggio dei database di SQL Server in esecuzione in una macchina virtuale di Azure (VM) e che viene eseguito il backup per un servizi di ripristino di Backup di Azure dell'insieme di credenziali per il [Backup di Azure](backup-overview.md) servizio. Si apprenderà come monitorare i processi e avvisi, arrestare e riprendere la protezione dei database, eseguire i processi di backup e annullare la registrazione di una macchina virtuale dal backup.

Se è ancora stato configurato i backup per i database di SQL Server, vedere [backup dei database di SQL Server in macchine virtuali di Azure](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorare i processi di backup manuali nel portale

Backup di Azure Mostra tutti i processi attivati manualmente nel **processi di Backup** portale. I processi possibile visualizzare in questa individuazione database di inclusione del portale e la registrazione e il backup e le operazioni di ripristino.

![Il portale di processi di Backup](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Il **processi di Backup** portale non visualizza i processi di backup pianificati. Usare SQL Server Management Studio per monitorare i processi di backup pianificati, come descritto nella sezione successiva.
>

Per informazioni dettagliate sugli scenari di monitoraggio, visitare [il monitoraggio nel portale di Azure](backup-azure-monitoring-built-in-monitor.md) e [monitoraggio tramite Monitoraggio di Azure](backup-azure-monitoring-use-azuremonitor.md).  


## <a name="view-backup-alerts"></a>Visualizzare gli avvisi di backup

Con i backup del log eseguiti ogni 15 minuti, monitorare i processi di backup può essere faticoso. Backup di Azure semplifica il monitoraggio mediante l'invio di avvisi tramite posta elettronica. Avvisi di posta elettronica sono:

- Attivata per tutti gli errori di backup.
- Consolidato a livello di database per il codice di errore.
- Inviati solo per errori di backup prima di un database.

Per monitorare gli avvisi di backup di database:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel dashboard dell'insieme di credenziali, selezionare **Avvisi ed eventi**.

   ![Selezionare Avvisi ed eventi](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. In **Avvisi ed eventi**, selezionare **avvisi di Backup**.

   ![Selezionare Avvisi di backup](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Interrompere la protezione di un database di SQL Server

È possibile interrompere i backup di un database di SQL Server in due modi:

* Interrompere tutti i processi di backup futuri ed eliminare tutti i punti di recupero.
* Arrestare tutti i processi di backup futuri e lasciare invariati i punti di ripristino.

Se si sceglie di lasciare i punti di ripristino, tenere presente questi dettagli:

* Tutti i punti di ripristino rimane invariati per sempre, l'eliminazione di tutti i deve arrestare all'arresto protezione dati con Mantieni dati.
* Ti verrà addebitata per istanza protetta e di spazio di archiviazione usato. Per altre informazioni, vedere [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).
* Se si elimina un'origine dati senza interrompere i backup, i nuovi backup non riuscirà.

Per interrompere la protezione per un database:

1. Nel dashboard dell'insieme di credenziali, selezionare **elementi di Backup**.

2. Sotto **tipo di gestione di Backup**, selezionare **SQL nella macchina virtuale di Azure**.

    ![Selezionare SQL nella macchina virtuale di Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Selezionare il database per cui si desidera interrompere la protezione.

    ![Selezionare il database per interrompere la protezione](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. Nel menu del database, selezionare **Interrompi backup**.

    ![Selezionare Interrompi backup](./media/backup-azure-sql-database/stop-db-button.png)


5. Nel **Interrompi Backup** menu, scegliere se mantenere o eliminare dati. Se si desidera, specificare un motivo e commento.

    ![Mantenere o eliminare dati nel menu Interrompi Backup](./media/backup-azure-sql-database/stop-backup-button.png)

6. Selezionare **Interrompi backup**.


## <a name="resume-protection-for-a-sql-database"></a>Riprendere la protezione per un database SQL

Quando si arresta la protezione per il database SQL, se si seleziona il **conserva i dati di Backup** opzione, è possibile riprendere la protezione in un secondo momento. Se si non mantengono i dati di backup, è Impossibile riprendere la protezione.

Per riprendere la protezione per un database SQL:

1. Aprire l'elemento di backup e selezionare **Riprendi backup**.

    ![Selezionare Riprendi backup per riprendere la protezione del database](./media/backup-azure-sql-database/resume-backup-button.png)

2. Selezionare un criterio dal menu **Criteri di backup** e quindi selezionare **Salva**.

## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

È possibile eseguire diversi tipi di backup su richiesta:

* Backup completo
* Backup completo solo copia
* Backup differenziale
* Backup dei log

Anche se è necessario specificare la durata di mantenimento dei backup completi di sola copia, l'intervallo di conservazione per altri tipi di backup viene impostato automaticamente su 30 giorni dall'ora corrente. <br/>
Per altre informazioni, vedere [tipi di backup di SQL Server](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Annullare un’istanza SQL &Server

Annullare la registrazione di un'istanza di SQL Server dopo aver disattivato la protezione, ma prima di eliminare l'insieme di credenziali:

1. Nel dashboard dell'insieme di credenziali, sotto **Manage**, selezionare **infrastruttura di Backup**.  

   ![Selezionare Infrastruttura di backup](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. In **Server di gestione** selezionare **Server protetti**.

   ![Selezionare Server protetti](./media/backup-azure-sql-database/protected-servers.png)

3. In **Server protetti** selezionare il server di cui si vuole annullare la registrazione. Per eliminare l'insieme di credenziali, è necessario annullare la registrazione di tutti i server.

4. Il server protetto e scegliere **Elimina**.

   ![Selezionare Elimina](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Ripetere la registrazione di estensione in VM di SQL Server

In alcuni casi, l'estensione del carico di lavoro della macchina virtuale può risultare alterata per un motivo o l'altro. In questi casi, tutte le operazioni attivate nella macchina virtuale inizierà a non riuscire. È quindi potrebbe essere necessario registrare di nuovo l'estensione nella macchina virtuale. **Registrare nuovamente** operazione reinstalla l'estensione di backup del carico di lavoro nella macchina virtuale per le operazioni continuare.  <br>

È consigliabile usare questa opzione con cautela. Quando viene attivato in una macchina virtuale con un'estensione già integra, questa operazione causerà l'estensione ottenere riavviato. Questo può comportare tutti i processi in corso a non riuscire. Controllare per uno o più i [sintomi](backup-sql-server-azure-troubleshoot.md#symptoms) prima di attivare l'operazione di ripetere la registrazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [risolvere i problemi di backup in un database di SQL Server](backup-sql-server-azure-troubleshoot.md).
