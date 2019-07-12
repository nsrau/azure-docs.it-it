---
title: Usare Backup di Azure per ripristinare i database di SQL Server sottoposta a backup in una macchina virtuale di Azure | Microsoft Docs
description: Questo articolo descrive come ripristinare i database di SQL Server in esecuzione in una VM di Azure e che viene eseguito il backup con Backup di Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: raynew
ms.openlocfilehash: 856f45f448aa843e9dc04ec3b6a60841cfe33227
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704873"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Ripristinare il backup di database SQL Server in macchine virtuali di Azure

Questo articolo descrive come ripristinare un database di SQL Server in esecuzione in una macchina virtuale di Azure (VM) che il [Backup di Azure](backup-overview.md) service è eseguito il backup in un insieme di credenziali di servizi di ripristino di Backup di Azure.

Questo articolo descrive come ripristinare i database SQL Server. Per altre informazioni, vedere [backup dei database di SQL Server in macchine virtuali di Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Ripristinare in un'ora o un punto di ripristino

Backup di Azure è possibile ripristinare i database di SQL Server in esecuzione su macchine virtuali di Azure come indicato di seguito:

- Ripristinare una data specifica o un'ora (al secondo) con i backup del log delle transazioni. Backup di Azure determina automaticamente il backup completo differenziale appropriato e la catena di backup del log necessari per ripristinare in base l'ora selezionata.
- Ripristinare un backup completo o differenziale specifico da ripristinare in un punto di recupero specifico.


## <a name="prerequisites"></a>Prerequisiti

Prima di ripristinare un database, tenere presente quanto segue:

- È possibile ripristinare il database in un'istanza di SQL Server nella stessa area di Azure.
- Il server di destinazione deve essere registrato nello stesso insieme di credenziali dell'origine.
- Per ripristinare un database crittografato con TDE in un altro Server SQL, è necessario innanzitutto [ripristinare il certificato nel server di destinazione](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Prima di ripristinare il database "master", avviare l'istanza di SQL Server in modalità utente singolo usando l'opzione di avvio **-m AzureWorkloadBackup**.
    - Il valore per **-m** è il nome del client.
    - Solo il nome client specificato può aprire la connessione.
- Per tutti i database di sistema (modello, master, msdb), arrestare il servizio SQL Server Agent prima di attivare il ripristino.
- Chiudere tutte le applicazioni tentino di richiedere una connessione a nessuno di questi database.
- Se si dispone di più istanze in esecuzione in un server, tutte le istanze dovrebbe essere disponibile e in esecuzione in caso contrario, il server non sarà visibile nell'elenco dei server di destinazione per il ripristino di database.

## <a name="restore-a-database"></a>Per ripristinare un database

Per ripristinare, sono necessarie le autorizzazioni seguenti:

* **Operatore di backup** autorizzazioni nell'insieme di credenziali in cui si sta eseguendo il ripristino.
* **Collaboratore (scrittura)** accesso all'origine della macchina virtuale con backup eseguito.
* **Collaboratore (scrittura)** accesso alla macchina virtuale di destinazione:
    - Se si sta ripristinando nella stessa VM, si tratta della VM di origine.
    - Se si esegue il ripristino in un percorso alternativo, questa è la nuova macchina virtuale di destinazione.

È possibile eseguire il ripristino nel modo seguente:
1. Aprire l'insieme di credenziali in cui è stata registrata la VM di SQL Server.
2. Nel dashboard dell'insieme di credenziali, in **Uso**, selezionare **Elementi di backup**.
3. In **Elementi di backup** in **Tipo di gestione dei backup** selezionare **SQL nella macchina virtuale di Azure**.

    ![Selezionare SQL nella macchina virtuale di Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Selezionare il database da ripristinare.

    ![Selezionare il database da ripristinare](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Verificare il menu database. Questi fornisce informazioni sul backup del database, tra cui:

    * I punti di ripristino meno recenti e più recenti.
    * Lo stato del backup del log per le ultime 24 ore per i database in modalità di recupero full e bulk-logged e che sono configurati per i backup di log delle transazioni.

6. Selezionare **Restore DB**.

    ![Selezionare Ripristina database](./media/backup-azure-sql-database/restore-db-button.png)

7. Nelle **ripristino della configurazione**, specificare dove ripristinare i dati:
   - **Percorso alternativo**: Ripristinare il database in un percorso alternativo e mantenere il database di origine.
   - **Sovrascrivi database**: ripristina i dati nella stessa istanza di SQL Server dell'origine. Questa opzione sovrascrive il database originale.

     > [!Important]
     > Se il database selezionato appartiene a un gruppo di disponibilità Always On, SQL Server non consente di sovrascrivere il database. È disponibile solo **Percorso alternativo**.
     >

     ![Menu Configurazione di ripristino](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Ripristinare in un percorso alternativo

1. Nel **ripristino della configurazione** menu, sotto **posizione di ripristinare**, selezionare **percorso alternativo**.
2. Selezionare il nome di SQL Server e l'istanza in cui si vuole ripristinare il database.
3. Nella finestra di dialogo **Nome del database ripristinato** inserire il nome del database di destinazione.
4. Se applicabile, scegliere **Sovrascrivere se il database con lo stesso nome esiste già nell'istanza selezionata di SQL**.
5. Selezionare **OK**.

    ![Fornire valori per il menu Configurazione di ripristino](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. Nelle **Seleziona punto di ripristino**, selezionare se si desidera [ripristinare a uno specifico punto nel tempo](#restore-to-a-specific-point-in-time) o a [ripristinare in un punto di recupero specifico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Il ripristino temporizzato in è disponibile solo per i backup del log per i database in modalità di recupero completo e con registrazione minima delle operazioni bulk.

### <a name="restore-and-overwrite"></a>Ripristinare e sovrascrivere

1. Nel **ripristino della configurazione** menu, sotto **posizione di ripristinare**, selezionare **sovrascrivere DB** > **OK**.

    ![Selezionare Sovrascrivi database](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. Nelle **Seleziona punto di ripristino**, selezionare **log (momento nel tempo)** al [ripristinare a uno specifico punto nel tempo](#restore-to-a-specific-point-in-time). Oppure selezionare **completo e differenziale** ripristinare un' [punto di recupero specifico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Il ripristino temporizzato in è disponibile solo per i backup del log per i database in modalità di recupero completo e con registrazione minima delle operazioni bulk.

### <a name="restore-to-a-specific-point-in-time"></a>Ripristinare un punto temporizzato specifico

Se si è scelto **log (punto temporizzato)** come tipo di ripristino, eseguire le operazioni seguenti:

1.  Sotto **ripristinare data/ora**, aprire il calendario. Le date con i punti di ripristino vengono visualizzate in grassetto nel calendario e viene evidenziata la data corrente.
1. Selezionare una data che contiene i punti di ripristino. Non è possibile selezionare le date con nessun punto di ripristino.

    ![Aprire il calendario](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Dopo aver selezionato una data, il grafico della sequenza temporale indica i punti di recupero disponibili in un intervallo continuo.
1. Specificare un'ora per il ripristino nel grafico della sequenza temporale, o selezionare un'ora. Selezionare **OK**.

    ![Selezionare un'ora di ripristino](./media/backup-azure-sql-database/recovery-point-logs-graph.png)


1. Nel **Advanced Configuration** menu, se si desidera mantenere il database non operativo dopo il ripristino, abilitare **ripristino con NORECOVERY**.
1. Se si vuole modificare il percorso di ripristino nel server di destinazione, specificare un nuovo percorso di destinazione.
1. Selezionare **OK**.

    ![Menu configurazione avanzata](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. Nel menu **Ripristina** selezionare **Ripristina** per avviare il processo di ripristino.
1. Tenere traccia dello stato di ripristino nel **notifiche** area o ne tiene traccia selezionando **processi di ripristino** dal menu di database.

    ![Avanzamento del processo di ripristino](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Ripristinare uno specifico punto di ripristino

Se si è scelto **Completo e differenziale** come tipo di ripristino, eseguire le operazioni seguenti:

1. Selezionare un punto di recupero dall’elenco e fare clic su **OK** per completare la procedura dei punti di ripristino.

    ![Scegliere un punto di ripristino con registrazione completa](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. Nel **Advanced Configuration** menu, se si desidera mantenere il database non operativo dopo il ripristino, abilitare **ripristino con NORECOVERY**.
1. Se si vuole modificare il percorso di ripristino nel server di destinazione, specificare un nuovo percorso di destinazione.
1. Selezionare **OK**.

    ![Menu configurazione avanzata](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. Nel menu **Ripristina** selezionare **Ripristina** per avviare il processo di ripristino.
1. Tenere traccia dello stato di ripristino nel **notifiche** area o ne tiene traccia selezionando **processi di ripristino** dal menu di database.

    ![Avanzamento del processo di ripristino](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Ripristinare i database con numero elevato di file

Se la dimensione della stringa totale dei file in un database è supera a un [particolare limite](backup-sql-server-azure-troubleshoot.md#size-limit-for-files), Backup di Azure archivia l'elenco dei file di database in un componente pit diverso modo che non sarà in grado di impostare il percorso di ripristino di destinazione durante il ripristino operazione. I file verranno ripristinati nel percorso predefinito SQL.

  ![Ripristinare i Database con file di grandi dimensioni](./media/backup-azure-sql-database/restore-large-files.jpg)


## <a name="next-steps"></a>Passaggi successivi

[Gestire e monitorare](manage-monitor-sql-database-backup.md) i database di SQL Server che vengono eseguito il backup per Backup di Azure.
