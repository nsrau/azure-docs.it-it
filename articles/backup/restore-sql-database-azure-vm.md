---
title: Ripristinare i database di SQL Server in una macchina virtuale di Azure
description: Questo articolo descrive come ripristinare SQL Server database in esecuzione in una macchina virtuale di Azure e di cui viene eseguito il backup con backup di Azure.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 0dbf5c48884dc665355d2806ff343facfbeffc29
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171894"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Ripristinare il backup di database SQL Server in macchine virtuali di Azure

Questo articolo descrive come ripristinare un database di SQL Server in esecuzione in una macchina virtuale (VM) di Azure di cui è stato eseguito il backup del servizio [backup](backup-overview.md) di Azure in un insieme di credenziali di servizi di ripristino di backup di Azure.

Questo articolo descrive come ripristinare i database SQL Server. Per altre informazioni, vedere [eseguire il backup di database SQL Server nelle VM di Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Ripristinare un'ora o un punto di ripristino

Backup di Azure è in grado di ripristinare SQL Server database in esecuzione in macchine virtuali di Azure, come indicato di seguito:

- Ripristinare una data o un'ora specifica (al secondo) utilizzando i backup del log delle transazioni. Backup di Azure determina automaticamente il backup completo differenziale appropriato e la catena di backup del log necessari per il ripristino in base all'ora selezionata.
- Ripristinare un backup completo o differenziale specifico per eseguire il ripristino in un punto di ripristino specifico.

## <a name="prerequisites"></a>prerequisiti

Prima di ripristinare un database, tenere presente quanto segue:

- È possibile ripristinare il database in un'istanza di SQL Server nella stessa area di Azure.
- Il server di destinazione deve essere registrato nello stesso insieme di credenziali dell'origine.
- Per ripristinare un database crittografato con Transparent Data Encryption in un altro SQL Server, è necessario innanzitutto [ripristinare il certificato nel server di destinazione](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Prima di ripristinare il database "Master", avviare l'istanza di SQL Server in modalità utente singolo usando l'opzione di avvio **-m AzureWorkloadBackup**.
  - Il valore per **-m** è il nome del client.
  - La connessione può essere aperta solo dal nome client specificato.
- Per tutti i database di sistema (modello, Master, msdb), arrestare il servizio SQL Server Agent prima di avviare il ripristino.
- Chiudere tutte le applicazioni che potrebbero tentare di eseguire una connessione a uno di questi database.
- Se si dispone di più istanze in esecuzione in un server, tutte le istanze devono essere in esecuzione, in caso contrario il server non verrà visualizzato nell'elenco dei server di destinazione in cui ripristinare il database.

## <a name="restore-a-database"></a>Per ripristinare un database

Per eseguire il ripristino, sono necessarie le autorizzazioni seguenti:

- Autorizzazioni dell' **operatore di backup** nell'insieme di credenziali in cui si sta eseguendo il ripristino.
- **Collaboratore (scrittura)** accesso all'origine della macchina virtuale con backup eseguito.
- **Collaboratore (scrittura)** accesso alla macchina virtuale di destinazione:
  - Se si esegue il ripristino nella stessa VM, si tratta della VM di origine.
  - Se si esegue il ripristino in un percorso alternativo, si tratta della nuova VM di destinazione.

È possibile eseguire il ripristino nel modo seguente:

1. Aprire l'insieme di credenziali in cui è stata registrata la VM di SQL Server.
2. Nel dashboard dell'insieme di credenziali, in **Uso**, selezionare **Elementi di backup**.
3. In **Elementi di backup** in **Tipo di gestione dei backup** selezionare **SQL nella macchina virtuale di Azure**.

    ![Selezionare SQL nella macchina virtuale di Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Selezionare il database da ripristinare.

    ![Selezionare il database da ripristinare](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Verificare il menu database. Questi fornisce informazioni sul backup del database, tra cui:

    - I punti di ripristino meno recenti e più recenti.
    - Stato di backup del log per le ultime 24 ore per i database in modalità di recupero con registrazione completa e con registrazione minima delle operazioni bulk e configurati per i backup del log transazionale.

6. Selezionare **Ripristina**.

    ![Selezionare Ripristina](./media/backup-azure-sql-database/restore-db.png)

7. In **configurazione ripristino**specificare dove (o come) ripristinare i dati:
   - **Percorso alternativo**: ripristinare il database in un percorso alternativo e salvare il database di origine originale.
   - **Sovrascrivi database**: ripristina i dati nella stessa istanza di SQL Server dell'origine. Questa opzione sovrascrive il database originale.

    > [!IMPORTANT]
    > Se il database selezionato appartiene a un gruppo di disponibilità Always On, SQL Server non consente di sovrascrivere il database. È disponibile solo **Percorso alternativo**.
    >
   - **Ripristina come file**: anziché ripristinare come database, ripristinare i file di backup che possono essere ripristinati come database in un secondo momento in qualsiasi computer in cui i file sono presenti usando SQL Server Management Studio.
     ![menu configurazione ripristino](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Ripristinare in un percorso alternativo

1. Nel menu **configurazione ripristino** , in **where to Restore**, selezionare **percorso alternativo**.
2. Selezionare il nome di SQL Server e l'istanza in cui si vuole ripristinare il database.
3. Nella finestra di dialogo **Nome del database ripristinato** inserire il nome del database di destinazione.
4. Se applicabile, scegliere **Sovrascrivere se il database con lo stesso nome esiste già nell'istanza selezionata di SQL**.
5. Selezionare **OK**.

    ![Fornire valori per il menu Configurazione di ripristino](./media/backup-azure-sql-database/restore-configuration.png)

6. In **Seleziona punto di ripristino**selezionare se eseguire [il ripristino a un punto specifico nel tempo](#restore-to-a-specific-point-in-time) o per eseguire [il ripristino a un punto di ripristino specifico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Il ripristino temporizzato è disponibile solo per i backup del log per i database in modalità di recupero con registrazione completa e con registrazione minima delle operazioni bulk.

### <a name="restore-and-overwrite"></a>Ripristinare e sovrascrivere

1. Nel menu **configurazione ripristino** , in percorso **da ripristinare**selezionare **Sovrascrivi database** > **OK**.

    ![Selezionare Sovrascrivi database](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. In **Seleziona punto di ripristino**selezionare **registri (temporizzato)** per eseguire [il ripristino a un momento specifico](#restore-to-a-specific-point-in-time). In alternativa, selezionare **completo & differenziale** per ripristinare un [punto di ripristino specifico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Il ripristino temporizzato è disponibile solo per i backup del log per i database in modalità di recupero con registrazione completa e con registrazione minima delle operazioni bulk.

### <a name="restore-as-files"></a>Ripristina come file

Per ripristinare i dati di backup come file con estensione bak anziché come database, scegliere **Ripristina come file**. Una volta scaricati i file in un percorso specificato, è possibile utilizzare questi file in qualsiasi computer in cui si desidera ripristinarli come database. Grazie alla possibilità di spostare questi file in qualsiasi computer, è ora possibile ripristinare i dati tra sottoscrizioni e aree geografiche.

1. Nel menu **configurazione ripristino** , in percorso **da ripristinare**Selezionare **Ripristina come file**.
2. Selezionare il nome del SQL Server in cui si desidera ripristinare i file di backup.
3. Nel **percorso di destinazione nel server** immettere il percorso della cartella nel server selezionato nel passaggio 2. Si tratta del percorso in cui il servizio eseguirà il dump di tutti i file di backup necessari. In genere, un percorso di condivisione di rete o un percorso di una condivisione file di Azure montata quando viene specificato come percorso di destinazione consente di accedere più facilmente a questi file da altri computer nella stessa rete o con la stessa condivisione file di Azure montata su di essi.
4. Selezionare **OK**.

![Selezionare Ripristina come file](./media/backup-azure-sql-database/restore-as-files.png)

5. Selezionare il **punto di ripristino** corrispondente a cui verranno ripristinati tutti i file con estensione bak disponibili.

![Selezionare un punto di ripristino](./media/backup-azure-sql-database/restore-point.png)

6. Tutti i file di backup associati al punto di ripristino selezionato vengono scaricati nel percorso di destinazione. È possibile ripristinare i file come database in qualsiasi computer in cui sono presenti usando SQL Server Management Studio.

![File di backup ripristinati nel percorso di destinazione](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Ripristinare un punto temporizzato specifico

Se si è scelto **log (punto temporizzato)** come tipo di ripristino, eseguire le operazioni seguenti:

1. In **Data/ora ripristino**aprire il calendario. Nel calendario le date contenenti punti di ripristino vengono visualizzate in grassetto e la data corrente è evidenziata.
1. Selezionare una data con punti di ripristino. Non è possibile selezionare date senza punti di ripristino.

    ![Apri il calendario](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Dopo aver selezionato una data, il grafico della sequenza temporale indica i punti di recupero disponibili in un intervallo continuo.
1. Specificare un'ora per il ripristino nel grafico della sequenza temporale oppure selezionare un'ora. Selezionare **OK**.

    ![Selezionare un'ora di ripristino](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. Nel menu **configurazione avanzata** , se si desidera lasciare il database non operativo dopo il ripristino, abilitare **RESTORE WITH NORECOVERY**.
1. Se si vuole modificare il percorso di ripristino nel server di destinazione, specificare un nuovo percorso di destinazione.
1. Selezionare **OK**.

    ![Menu configurazione avanzata](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. Nel menu **Ripristina** selezionare **Ripristina** per avviare il processo di ripristino.
1. Tenere traccia dello stato di avanzamento del ripristino nell'area **notifiche** o monitorarlo selezionando **Ripristina processi** dal menu database.

    ![Avanzamento del processo di ripristino](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Ripristinare uno specifico punto di ripristino

Se si è scelto **Completo e differenziale** come tipo di ripristino, eseguire le operazioni seguenti:

1. Selezionare un punto di recupero dall’elenco e fare clic su **OK** per completare la procedura dei punti di ripristino.

    ![Scegliere un punto di ripristino con registrazione completa](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. Nel menu **configurazione avanzata** , se si desidera lasciare il database non operativo dopo il ripristino, abilitare **RESTORE WITH NORECOVERY**.
1. Se si vuole modificare il percorso di ripristino nel server di destinazione, specificare un nuovo percorso di destinazione.
1. Selezionare **OK**.

    ![Menu configurazione avanzata](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. Nel menu **Ripristina** selezionare **Ripristina** per avviare il processo di ripristino.
1. Tenere traccia dello stato di avanzamento del ripristino nell'area **notifiche** o monitorarlo selezionando **Ripristina processi** dal menu database.

    ![Avanzamento del processo di ripristino](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Ripristinare i database con un numero elevato di file

Se le dimensioni totali della stringa dei file in un database sono maggiori di un [limite specifico](backup-sql-server-azure-troubleshoot.md#size-limit-for-files), backup di Azure archivia l'elenco dei file di database in un componente Pit diverso in modo che non sia possibile impostare il percorso di ripristino di destinazione durante l'operazione di ripristino. In alternativa, i file verranno ripristinati nel percorso predefinito SQL.

  ![Ripristinare un database con file di grandi dimensioni](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>Passaggi successivi

[Gestione e monitoraggio](manage-monitor-sql-database-backup.md) SQL Server database di cui viene eseguito il backup da backup di Azure.
