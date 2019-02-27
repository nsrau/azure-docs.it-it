---
title: Ripristinare database SQL Server con backup eseguito su una macchina virtuale di Azure con Backup di Azure | Microsoft Docs
description: Questo articolo descrive come ripristinare i database SQL Server di cui è stato eseguito il backup con Backup di Azure in esecuzione in una macchina virtuale di Azure
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 9b741f8562ae2e81d297357afd3b0e0e3976a248
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445441"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Ripristinare il backup di database SQL Server in macchine virtuali di Azure 


Questo articolo descrive come ripristinare un database SQL Server in esecuzione in una macchina virtuale di Azure, di cui è stato eseguito il backup in un insieme di credenziali di Servizi di ripristino con il servizio [Backup di Azure](backup-overview.md).


> [!NOTE]
> Il backup dei database SQL Server, in esecuzione in una macchina virtuale di Azure con Backup di Azure, è attualmente in anteprima pubblica.


Questo articolo descrive come ripristinare i database SQL Server. Se non è stato configurato il backup per i database, seguire le istruzioni in [questo articolo](backup-azure-sql-database.md)



## <a name="about-restoring-databases"></a>Informazioni sul ripristino dei database

Backup di Azure può ripristinare database SQL Server in esecuzione nelle macchine virtuali di Azure seguendo questa procedura:

- Ripristinare una data specifica o un'ora (al secondo), usando i backup del log delle transazioni. In base ai tempi di ripristino specificati, Backup di Azure determina automaticamente i backup completi, differenziali e la catena di backup del log necessari per ripristinare in base all'ora selezionata.
- Ripristinare un backup completo o differenziale specifico per il ripristino fino a un punto di recupero anziché un'ora specifica.


### <a name="prerequisites"></a>Prerequisiti

Prima di ripristinare un database, tenere presente quanto segue:

- È possibile ripristinare il database in un'istanza di SQL Server nella stessa area di Azure.
- Il server di destinazione deve essere registrato nello stesso insieme di credenziali dell'origine.
- Per ripristinare un database crittografato TDE in un altro SQL Server, innanzitutto [ripristinare il certificato nel server di destinazione](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Prima di attivare il ripristino del database "master", avviare un'istanza SQL Server in modalità utente singolo con opzione  **-m AzureWorkloadBackup**.
    - Il valore per **-m** è il nome del client.
    - Per aprire la connessione è consentito solo il nome client specificato.
- Per tutti i database di sistema (modello, master, msdb), arrestare il servizio SQL Agent prima di attivare il ripristino.
- Chiudere tutte le applicazioni che potrebbero provare a usare una connessione di uno di questi database.

## <a name="restore-a-database"></a>Per ripristinare un database

Per ripristinare sono necessarie le autorizzazioni seguenti:

* Autorizzazioni di **Operatore di backup** per l'insieme di credenziali in cui si esegue il ripristino.
* **Collaboratore (scrittura)** accesso all'origine della macchina virtuale con backup eseguito.
* **Collaboratore (scrittura)** accesso alla macchina virtuale di destinazione:
    - Se si sta ripristinando nella stessa macchina virtuale, questa sarà la macchina virtuale di origine.
    - Se si esegue il ripristino in un percorso alternativo, questa sarà la nuova macchina virtuale di destinazione. 

È possibile eseguire il ripristino nel modo seguente:
1. Aprire l'insieme di credenziali in cui è stata registrata la VM di SQL Server.
2. Nel dashboard dell'insieme di credenziali, in **Uso**, selezionare **Elementi di backup**.

    ![Si aprirà l'elenco Elementi di backup](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. In **Elementi di backup** in **Tipo di gestione dei backup** selezionare **SQL nella macchina virtuale di Azure**.

    ![Selezionare SQL nella macchina virtuale di Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Selezionare il database da ripristinare.

    ![Selezionare il database da ripristinare](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Verificare il menu database. Questi fornisce informazioni sul backup del database, tra cui: 

    * I punti di ripristino meno recenti e più recenti.
    * Lo stato del backup del log nelle ultime 24 ore, per i database con modello di recupero con registrazione completa e con registrazione minima delle operazioni bulk, se configurato per i backup del log delle transazioni.

6. Fare clic su **Ripristina database**. 

    ![Selezionare Ripristina database](./media/backup-azure-sql-database/restore-db-button.png)

7. In **Configurazione di ripristino**, specificare dove si vogliono ripristinare i dati:
    - **Percorso alternativo**: consente di ripristinare il database in un percorso diverso mantenendo il database di origine.
    - **Sovrascrivi database**: ripristina i dati nella stessa istanza di SQL Server dell'origine. L'effetto di questa opzione è che si sovrascrive il database originale.

    > [!Important]
    > Se il database selezionato appartiene a un gruppo di disponibilità Always On, SQL Server non consente di sovrascrivere il database. È disponibile solo **Percorso alternativo**.
    >

    ![Menu Configurazione di ripristino](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Ripristinare in un percorso alternativo

1. Nel menu **Configurazione di ripristino** in **Posizione ripristino**, selezionare **Percorso alternativo**.
2. Selezionare il nome di SQL Server e l'istanza in cui si vuole ripristinare il database.
3. Nella casella **Nome del database ripristinato** inserire il nome del database di destinazione.
4. Se applicabile, scegliere **Sovrascrivere se il database con lo stesso nome esiste già nell'istanza selezionata di SQL**.
5. Fare clic su **OK**.

    ![Fornire valori per il menu Configurazione di ripristino](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. In **Seleziona punto di ripristino**, selezionare [ripristinare un punto specifico temporizzato](#restore-to-a-specific-point-in-time), oppure ripristinare un [punto di ripristino specifico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Il punto di ripristino temporizzato è disponibile solo per i backup del log per i database con modello di recupero, registrazione completa e registrazione minima delle operazioni bulk. 


### <a name="restore-and-overwrite"></a>Ripristinare e sovrascrivere

1. Nel menu **Configurazione di ripristino** in **Posizione di ripristino**, selezionare **Sovrascrivi database** > **OK**.

    ![Selezionare Sovrascrivi database](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. In **Seleziona punto di ripristino**, selezionare **Log (punto temporizzato) per [ripristinare uno specifico punto nel tempo](#restore-to-a-specific-point-in-time) o **Completo e differenziale** per ripristinare un [punto di recupero specifico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Il ripristino temporizzato è disponibile solo per i backup del log per i database con modello di recupero con registrazione completa e con registrazione minima delle operazioni bulk. 




    
### <a name="restore-to-a-specific-point-in-time"></a>Ripristinare un punto temporizzato specifico

Se si è scelto **log (punto temporizzato)** come tipo di ripristino, eseguire le operazioni seguenti:

1.  Sotto **Data/ora di ripristino** selezionare l'icona del calendario. Nel **calendario**, le date in grassetto contengono punti di recupero e la data corrente è evidenziata.
2. Selezionare una data con punti di recupero. Non è possibile selezionare le date senza punti di ripristino.

    ![Aprire il calendario](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

3. Dopo aver selezionato una data, il grafico della sequenza temporale indica i punti di recupero disponibili in un intervallo continuo.
4. Specificare un'ora per il ripristino usando il grafico della sequenza temporale, o selezionare un'ora. Fare quindi clic su **OK**.

    ![Aprire il calendario](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

 
4. Se si vuole mantenere il database non operativo dopo il ripristino, abilitare **ripristino con NORECOVERY** nel menu**Configurazione avanzata**.
5. Se si vuole modificare il percorso di ripristino nel server di destinazione, specificare un nuovo percorso di destinazione.
6. Fare clic su **OK**.

    ![Menu configurazione avanzata](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)


7. Nel menu **Ripristina** selezionare **Ripristina** per avviare il processo di ripristino.
8. Tenere traccia dell'operazione di ripristino nell’area **notifiche** oppure scegliere **Processi di ripristino** dal menu del database.

    ![Avanzamento del processo di ripristino](./media/backup-azure-sql-database/restore-job-notification.png)



### <a name="restore-to-a-specific-restore-point"></a>Ripristinare uno specifico punto di ripristino

Se si è scelto **Completo e differenziale** come tipo di ripristino, eseguire le operazioni seguenti:


1. Selezionare un punto di recupero dall’elenco e fare clic su **OK** per completare la procedura dei punti di ripristino.

    ![Scegliere un punto di ripristino con registrazione completa](./media/backup-azure-sql-database/choose-fd-recovery-point.png)
        
2. Se si vuole mantenere il database non operativo dopo il ripristino, abilitare **ripristino con NORECOVERY** nel menu**Configurazione avanzata**.
3. Se si vuole modificare il percorso di ripristino nel server di destinazione, specificare un nuovo percorso di destinazione. 
4. Fare clic su **OK**.

    ![Menu configurazione avanzata](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

7. Nel menu **Ripristina** selezionare **Ripristina** per avviare il processo di ripristino.
8. Tenere traccia dell'operazione di ripristino nell’area **notifiche** oppure scegliere **Processi di ripristino** dal menu del database.

    ![Avanzamento del processo di ripristino](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="next-steps"></a>Passaggi successivi

[Gestire e monitorare i database SQL Server di cui è stato eseguito il backup](manage-monitor-sql-database-backup.md).
