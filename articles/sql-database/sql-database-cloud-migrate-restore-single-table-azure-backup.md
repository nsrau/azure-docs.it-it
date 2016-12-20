---
title: Ripristinare una singola tabella dal backup del database SQL di Azure | Documentazione Microsoft
description: Learn how to restore a single table from Azure SQL Database backup.
services: sql-database
documentationcenter: 
author: dalechen
manager: felixwu
editor: 
ms.assetid: 340b41bd-9df8-47fb-adfc-03216de38a5e
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: daleche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f792ad3da0037c55a41e50710cedcbcdf8ef0d74


---
# <a name="how-to-restore-a-single-table-from-an-azure-sql-database-backup"></a>Come ripristinare una singola tabella nel backup del database SQL di Azure
Potrebbe verificarsi una situazione in cui alcuni dati sono stati modificati accidentalmente in un database SQL e ora si desidera ripristinare la singola tabella interessata. Questo articolo descrive come ripristinare una singola tabella in un database da uno dei [backup automatici](sql-database-automated-backups.md)del database SQL.

## <a name="preparation-steps-rename-the-table-and-restore-a-copy-of-the-database"></a>Passaggi preliminari: rinominare la tabella e ripristinare una copia del database
1. Identificare la tabella nel database SQL di Azure che si desidera sostituire con la copia ripristinata. Utilizzare Microsoft SQL Management Studio per rinominare la tabella. Ad esempio, rinominare la tabella &lt;nometabella&gt;_old.
   
    **Nota** : per evitare il blocco, assicurarsi che non vi sia alcuna attività in esecuzione nella tabella che si sta rinominando. In caso di problemi, accertarsi di eseguire questa procedura durante una finestra di manutenzione.
2. Ripristinare un backup del database al momento specifico desiderato usando i passaggi di [Ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore).
   
    **Note**:
   
   * Il nome del database ripristinato sarà nel formato NomeDB+Timestamp. Ad esempio, **Adventureworks2012_2016-01-01T22-12Z**. In questo passaggio non verrà sovrascritto il nome del database esistente nel server. Si tratta di una misura di sicurezza che consente all'utente di verificare il database ripristinato prima di eliminare il database corrente e rinominare il database ripristinato per l'uso in produzione.
   * Il servizio esegue il backup automatico di tutti i livelli di prestazioni, da Basic a Premium, con varie metriche di conservazione dei backup, a seconda del livello:

| Ripristino DB | Livello Basic | Livelli Standard | Livelli Premium |
|:--- |:--- |:--- |:--- |
| Ripristino temporizzato |Qualsiasi punto di ripristino entro 7 giorni |Qualsiasi punto di ripristino entro 35 giorni |Qualsiasi punto di ripristino entro 35 giorni |

## <a name="copying-the-table-from-the-restored-database-by-using-the-sql-database-migration-tool"></a>Copia della tabella dal database ripristinato tramite lo strumento di migrazione del database SQL
1. Scaricare e installare la [Migrazione guidata database SQL di Microsoft Azure](https://sqlazuremw.codeplex.com).
2. Aprire la Migrazione guidata database SQL. Nella pagina **Seleziona processo** selezionare **Database in Analyze/Migrate** (Database in corso di analisi/migrazione) e quindi fare clic su **Avanti**.
   ![SQL Database Migration wizard - Select Process](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)
3. Nella finestra di dialogo **Connect to Server** (Connetti al server) immettere i valori seguenti:
   * **Server name**(Nome server): istanza di SQL Azure.
   * **Autenticazione**: **autenticazione di SQL Server**. Immettere le credenziali di accesso.
   * **Database**: **Master DB (List all databases)** (Database master (Elenca tutti i database)).
   * **Nota** : per impostazione predefinita, la procedura guidata salva le informazioni di accesso. Per modificare tale impostazione, selezionare **Forget Login Information**(Ignora informazioni di accesso).
     ![SQL Database Migration wizard - Select Source - step 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. Nella finestra di dialogo **Seleziona origine** selezionare il nome del database ripristinato nella sezione **Preparation steps** (Passaggi preliminari) come origine e quindi fare clic su **Avanti**.
   
    ![SQL Database Migration wizard - Select Source - step 2](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)
5. Nella finestra di dialogo **Seleziona oggetti** selezionare l'opzione **Seleziona oggetti di database specifici** e quindi scegliere una o più tabelle di cui eseguire la migrazione al server di destinazione.
   ![SQL Database Migration wizard - Choose Objects](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)
6. Nella pagina **Riepilogo Generazione guidata script** fare clic su **Sì** quando verrà richiesto se si è pronti a generare uno script SQL. È anche possibile salvare lo script TSQL per un uso successivo.
   ![SQL Database Migration wizard - Script Wizard Summary](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)
7. Nella pagina **Results Summary** (Riepilogo risultati) fare clic su **Avanti**.
   ![SQL Database Migration wizard - Results Summary](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)
8. Nella pagina **Setup Target Server Connection** (Configura connessione a server di destinazione) fare clic su **Connetti al server** e quindi immettere i dettagli seguenti:
   
   * **Server Name**(Nome server): istanza del server di destinazione.
   * **Autenticazione**: **autenticazione di SQL Server**. Immettere le credenziali di accesso.
   * **Database**: **Master DB (List all databases)** (Database master (Elenca tutti i database)). Questa opzione consente di elencare tutti i database nel server di destinazione.
     
     ![SQL Database Migration wizard - Setup Target Server Connection](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)
9. Fare clic su **Connetti**, selezionare il database di destinazione in cui si vuole spostare la tabella e fare clic su **Avanti**. In tal modo si dovrebbe interrompere l'esecuzione dello script generato in precedenza e dovrebbe essere visualizzata la tabella appena spostata copiata nel database di destinazione.

## <a name="verification-step"></a>Passaggio di verifica
1. Eseguire query e verificare la tabella appena copiata per assicurarsi che i dati siano inalterati. Dopo la conferma, è possibile rimuovere la tabella rinominata nella sezione **Preparation steps** (Passaggi preliminari). Ad esempio, &lt;nome tabella&gt;_old.

## <a name="next-steps"></a>Passaggi successivi
[Backup automatici del database SQL](sql-database-automated-backups.md)




<!--HONumber=Nov16_HO3-->


