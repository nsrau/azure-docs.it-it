---
title: Gestire e monitorare i database di SQL Server in una macchina virtuale di Azure con backup eseguito da Backup di Azure | Microsoft Docs
description: Questo articolo descrive come ripristinare i database SQL Server di cui è stato eseguito il backup con Backup di Azure in esecuzione su una macchina virtuale di Azure
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 1c2ce0ba42f0bc3efd1dcc951113b05ab6941b98
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430955"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gestire e monitorare i database SQL Server di cui è stato eseguito il backup 


Questo articolo descrive le attività principali di gestione e monitoraggio dei database SQL Server di cui è stato eseguito il backup in un insieme di credenziali di Servizi di ripristino di Backup di Azure, in esecuzione su una macchina virtuale di Azure, con il servizio [Backup di Azure](backup-overview.md). Attività, tra cui il monitoraggio di processi e avvisi, l'arresto e la ripresa della protezione dei database, l'esecuzione dei processi di backup e l'annullamento della registrazione di una macchina virtuale dal backup.


> [!NOTE]
> Il backup dei database SQL Server, in esecuzione su una macchina virtuale di Azure con Backup di Azure, è attualmente in anteprima pubblica.


Se non è stato ancora configurato il backup dei database SQL Server, seguire le istruzioni in [questo articolo](backup-azure-sql-database.md)

## <a name="monitor-backup-jobs"></a>Monitorare i processi di backup

###  <a name="monitor-ad-hoc-jobs-in-the-portal"></a>Monitorare i processi ad hoc nel portale

Backup di Azure mostra tutti i processi attivati manualmente nel portale **processi di Backup**, tra cui l'individuazione e la registrazione di database e le operazioni di backup e ripristino.

![Portale dei processi di backup](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> I processi di backup pianificati non vengono visualizzati nel portale **Processi di Backup**. Usare SQL Server Management Studio per monitorare i processi di backup pianificati, come descritto nella sezione successiva.
>

### <a name="monitor-backup-jobs-with-sql-server-management-studio"></a>Monitorare i processi di backup con SQL Server Management Studio 

Backup di Azure usa le API native di SQL per tutte le operazioni di backup.

Con le API native, è possibile recuperare tutte le informazioni sui processi dalla [tabella dei set di backup di SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) nel database msdb.

L'esempio seguente è una query per recuperare tutti i processi di backup per un database denominato **DB1**. Personalizzare la query per eseguire un monitoraggio avanzato.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

## <a name="view-backup-alerts"></a>Visualizzare gli avvisi di backup

Con i backup del log eseguiti ogni 15 minuti, monitorare i processi di backup può essere faticoso. Backup di Azure semplifica il monitoraggio con avvisi di posta elettronica.

- Per tutti gli errori di backup vengono inviati avvisi.
- Gli avvisi vengono consolidati a livello di database in base al codice di errore.
- Viene inviato un avviso tramite posta elettronica solo per il primo errore di backup di un database. 

Per monitorare gli avvisi di backup:

1. Accedere alla propria sottoscrizione di Azure nel [portale di Azure](https://portal.azure.com) per monitorare gli avvisi del database.

2. Nel dashboard dell'insieme di credenziali, selezionare **Avvisi ed eventi**.

   ![Selezionare Avvisi ed eventi](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. In **Avvisi ed eventi**, selezionare **avvisi di Backup**.

   ![Selezionare Avvisi di backup](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Interrompere la protezione di un database di SQL Server

È possibile interrompere i backup di un database di SQL Server in due modi:

* Interrompere tutti i processi di backup futuri ed eliminare tutti i punti di recupero.
* Interrompere tutti i processi di backup futuri mantenendo però i punti di recupero invariati.

Si noti che:

Se si lasciano i punti di recupero, i punti verranno eliminati in base ai criteri di backup. Verranno addebitati i costi per l'istanza protetta e lo spazio di archiviazione usato, fino a quando non verranno eliminati tutti i punti di recupero. [Altre informazioni](https://azure.microsoft.com/pricing/details/backup/) sui prezzi.
- Quando si lasciano intatti i punti di recupero, anche se scadono in base ai criteri di conservazione, Backup di Azure mantiene sempre un ultimo punto di recupero finché i dati di backup non vengono eliminati in modo esplicito.
- Se si elimina un'origine dati senza interrompere il backup, i nuovi backup inizieranno a dare esito negativo. Anche in questo caso, i punti di recupero precedenti scadranno in base ai criteri, ma un ultimo punto di recupero verrà sempre mantenuto fino a quando non si arresta il backup e si eliminano i dati.
- Non è possibile interrompere i backup per un database abilitato per la protezione automatica, fino a quando non viene disabilitata la protezione automatica.

Per interrompere la protezione per un database:

1. Nel dashboard dell'insieme di credenziali, in **Uso**, selezionare **Elementi di Backup**.

    ![Si aprirà l'elenco Elementi di backup](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

2. In **Tipo di gestione dei backup** selezionare **SQL nella macchina virtuale di Azure**.

    ![Selezionare SQL nella macchina virtuale di Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)


3. Selezionare il database per cui si desidera interrompere la protezione.

    ![Selezionare il database per interrompere la protezione](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)


5. Nel menu del database, selezionare **Interrompi backup**.

    ![Selezionare Interrompi backup](./media/backup-azure-sql-database/stop-db-button.png)


6. Nel menu **Interrompi Backup**, scegliere se mantenere o eliminare i dati. L'aggiunta di un motivo e commento è facoltativa.

    ![Menu Interrompi backup](./media/backup-azure-sql-database/stop-backup-button.png)

7. Fare clic su **Interrompi backup**.

  

### <a name="resume-protection-for-a-sql-database"></a>Riprendere la protezione per un database SQL

Se è stata selezionata l'opzione **Conserva i dati di backup** quando è stata interrotta la protezione per il database SQL, è possibile riprendere la protezione. Se i dati di backup non vengono conservati, non sarà possibile riprendere la protezione.

1. Per riprendere la protezione del database SQL, aprire l'elemento di backup e selezionare **Riprendi backup**.

    ![Selezionare Riprendi backup per riprendere la protezione del database](./media/backup-azure-sql-database/resume-backup-button.png)

2. Selezionare un criterio dal menu **Criteri di backup** e quindi selezionare **Salva**.

## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

È possibile eseguire diversi tipi di backup su richiesta:

* Backup completo
* Backup completo solo copia
* Backup differenziale
* Backup dei log

[Altre informazioni](backup-architecture.md#sql-server-backup-types) sui tipi backup di SQL Server.

## <a name="unregister-a-sql-server-instance"></a>Annullare un’istanza SQL &Server

Annullare la registrazione di un'istanza di SQL Server dopo aver disabilitato la protezione, ma prima di eliminare l'insieme di credenziali:

1. Nel dashboard dell'insieme di credenziali, in **Gestisci**, selezionare **Infrastruttura di backup**.  

   ![Selezionare Infrastruttura di backup](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. In **Server di gestione** selezionare **Server protetti**.

   ![Selezionare Server protetti](./media/backup-azure-sql-database/protected-servers.png)


3. In **Server protetti** selezionare il server di cui si vuole annullare la registrazione. Per eliminare l'insieme di credenziali, è necessario annullare la registrazione di tutti i server.

4. Fare clic sul server protetto > **Elimina**.

   ![Selezionare Elimina](./media/backup-azure-sql-database/delete-protected-server.png)


## <a name="next-steps"></a>Passaggi successivi

[Rivedere](backup-sql-server-azure-troubleshoot.md) la risoluzione dei problemi relativi al backup del database di SQL Server.
