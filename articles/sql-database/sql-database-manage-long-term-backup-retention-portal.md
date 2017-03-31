---
title: 'Portale di Azure: Gestire la conservazione dei backup del database a lungo termine | Documentazione Microsoft'
description: Riferimento rapido su come configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici di un database SQL di Azure in un insieme di credenziali di Servizi di ripristino di Azure tramite il portale di Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/22/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: fa4cf56c10f885a2fd4b62e08a369a51d1280ab3
ms.lasthandoff: 03/25/2017


---
# <a name="configure-manage-and-restore-from-long-term-retention-of-database-backups-in-an-azure-recovery-services-vault-using-the-azure-portal"></a>Configurare, gestire e ripristinare dalla conservazione a lungo termine di backup di un database in un insieme di credenziali di Servizi di ripristino di Azure tramite il portale di Azure

Questo argomento illustra come configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure tramite il portale di Azure. È inoltre possibile eseguire questa attività tramite [PowerShell](sql-database-manage-long-term-backup-retention-powershell.md).

Per altre informazioni, vedere l'articolo sulla [conservazione dei backup a lungo termine](sql-database-long-term-retention.md).

> [!TIP]
> Per un'esercitazione, vedere l'[introduzione al backup e ripristino per la protezione e il ripristino dei dati tramite il portale di Azure](sql-database-get-started-backup-recovery-portal.md).
>

## <a name="configure-long-term-retention-using-the-azure-portal"></a>Configurare la conservazione a lungo termine nel portale di Azure

1. Aprire il pannello **SQL Server** per il server in uso.

    ![pannello di sql server](./media/sql-database-get-started/sql-server-blade.png)

2. Fare clic su **Conservazione backup a lungo termine**.

   ![collegamento conservazione backup a lungo termine](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. Nel pannello **Conservazione del backup a lungo termine** leggere e accettare le condizioni preliminari (se non sono già state accettate o se questa funzionalità non è più in versione di anteprima).

   ![accettare le condizioni preliminari](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Per configurare la conservazione a lungo termine dei backup per un database, selezionare il database nella griglia e quindi fare clic su **Configura** sulla barra degli strumenti.

   ![selezione del database per la conservazione backup a lungo termine](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. Nel pannello **Configura** fare clic su **Configura le impostazioni obbligatorie** in **Insiemi di credenziali dei servizi di ripristino**.

   ![collegamento configurazione dell'insieme di credenziali](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. Nel pannello **Insiemi di credenziali dei servizi di ripristino** selezionare un insieme di credenziali esistente, se disponibile. In caso contrario, se per la propria sottoscrizione non è presente alcun insieme di credenziali dei servizi di ripristino, fare clic per chiudere il flusso e creare un insieme di credenziali dei servizi di ripristino.

   ![collegamento creazione nuovo insieme di credenziali](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. Nel pannello **Insiemi di credenziali dei servizi di ripristino** fare clic su **Aggiungi**.

   ![collegamento aggiunta nuovo insieme di credenziali](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. Nel pannello **Insiemi di credenziali dei servizi di ripristino** specificare un nome valido per il nuovo insieme di credenziali dei servizi di ripristino.

   ![nome nuovo insieme di credenziali](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Selezionare la propria sottoscrizione e il relativo gruppo di risorse e quindi specificare il percorso per l'insieme di credenziali. Al termine, fare clic su **Crea**.

   ![creazione nuovo insieme di credenziali](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > L'insieme di credenziali deve trovarsi nella stessa area del server logico di Azure SQL e deve usare lo stesso gruppo di risorse del server logico.
   >

10. Dopo aver creato il nuovo insieme di credenziali, eseguire i passaggi necessari per tornare al pannello **Insiemi di credenziali dei servizi di ripristino**.

11. Nel pannello **Insiemi di credenziali dei servizi di ripristino** fare clic sull'insieme di credenziali e quindi fare clic su **Seleziona**.

   ![selezione insieme di credenziali esistente](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. Nel pannello **Configura** specificare un nome valido per il nuovo criterio di conservazione, modificare i criteri di conservazione predefiniti in base alle esigenze e quindi fare clic su **OK**.

   ![definizione dei criteri di conservazione](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

13. Nel pannello **Conservazione del backup a lungo termine** fare clic su **Salva** e quindi fare clic su **OK** per applicare i criteri di conservazione dei backup a lungo termine a tutti i database selezionati.

   ![definizione dei criteri di conservazione](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Fare clic su **Salva** per abilitare la conservazione dei backup a lungo termine usando i nuovi criteri per l'insieme di credenziali di Servizi di ripristino di Azure appena configurato.

   ![definizione dei criteri di conservazione](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)


## <a name="restore-from-long-term-backup-retention-using-the-azure-portal"></a>Eseguire il ripristino dalla conservazione del backup a lungo termine usando il portale di Azure

1. Nel pannello **Backup dell'insieme di credenziali Azure** fare clic sul backup da ripristinare e quindi su **Seleziona**.

    ![selezione del backup dell'insieme di credenziali](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. Nella casella di testo **Nome database** immettere un nome per il database ripristinato.

    ![nuovo nome database](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Fare clic su **OK** per ripristinare il database dal backup presente nell'insieme di credenziali al nuovo database.

4. Sulla barra degli strumenti fare clic sull'icona di notifica per visualizzare lo stato del processo di ripristino.

    ![avanzamento del processo di ripristino dall'insieme di credenziali](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Al termine del processo di ripristino, aprire il pannello **Database SQL** per visualizzare il database appena ripristinato.

    ![database ripristinato dall'insieme di credenziali](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

## <a name="view-long-term-backup-retention-information-using-the-azure-portal"></a>Visualizzare le informazioni della conservazione del backup a lungo termine usando il portale di Azure 

1. Aprire il pannello per l'insieme di credenziali dei Servizi di ripristino di Azure (andare a **Tutte le risorse** e selezionarlo dall'elenco delle risorse correlate alla sottoscrizione) per visualizzare la quantità di spazio di archiviazione usata dai backup del database nell'insieme di credenziali.

   ![visualizzazione dell'insieme di credenziali dei servizi di ripristino con i backup](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Aprire il pannello **Database SQL** per il database.

    ![nuovo pannello del database di esempio](./media/sql-database-get-started/new-sample-db-blade.png)

3. Sulla barra degli strumenti fare clic su **Ripristina**.

    ![barra degli strumenti di ripristino](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. Nel pannello di ripristino fare clic su **A lungo termine**.

5. Nei backup dell'insieme di credenziali di Azure fare clic su **Selezionare un backup** per visualizzare i backup di database disponibili nella conservazione dei backup a lungo termine.

    ![backup nell'insieme di credenziali](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

> [!TIP]
> Per un'esercitazione, vedere [Introduzione a Backup e ripristino per la protezione dei dati e il ripristino](sql-database-get-started-backup-recovery-portal.md)

## <a name="next-steps"></a>Passaggi successivi

- Per gestire i backup nella conservazione a lungo termine tramite PowerShell, vedere l'articolo sulla [gestione della conservazione dei backup a lungo termine con PowerShell](sql-database-manage-long-term-backup-retention-powershell.md)
- Per informazioni sui backup automatici generati dal servizio, vedere l'articolo relativo ai [backup automatici](sql-database-automated-backups.md)
- Per altre informazioni sulla conservazione dei backup a lungo termine, vedere [conservazione dei backup a lungo termine](sql-database-long-term-retention.md)
- Per altre informazioni sul ripristino da backup, vedere [ripristino dal backup](sql-database-recovery-using-backups.md)
