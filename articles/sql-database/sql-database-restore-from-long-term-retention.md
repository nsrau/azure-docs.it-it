---
title: Ripristinare un database da un backup nell&quot;insieme di credenziali dei Servizi di ripristino di Azure | Documentazione Microsoft
description: Riferimento rapido sul ripristino di un database per un backup nell&quot;insieme di credenziali dei Servizi di ripristino di Azure e sullo spazio usato da questi backup
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
ms.date: 12/07/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6d729f89322b67fe75d2c0ad001e70b42ccedb7e
ms.openlocfilehash: cdb898ef86517b6eb6eed4b2ae38c4e327e78181


---
# <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Ripristinare un database da un backup nella conservazione dei backup a lungo termine

In questo argomento viene illustrato come ripristinare un database da un backup nella conservazione dei backup a lungo termine.

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

> [!TIP]
> Per un'esercitazione, vedere [Introduzione al backup e ripristino per la protezione dei dati e il ripristino](sql-database-get-started-backup-recovery.md)
>

## <a name="next-steps"></a>Passaggi successivi

- Per configurare la conservazione a lungo termine dei backup automatici nell'insieme di credenziali dei Servizi di ripristino di Azure, vedere [Configure long-term backup retention](sql-database-configure-long-term-retention.md) (Configurare la conservazione a lungo termine dei backup)
- Per visualizzare i backup nell'insieme di credenziali dei Servizi di ripristino di Azure, vedere [View backups in long-term retention](sql-database-view-backups-in-vault.md) (Visualizzare i backup nella conservazione a lungo termine)
- Per altre informazioni sui backup automatici generati dal servizio, vedere [backup automatici](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD)
- Per altre informazioni sulla conservazione dei backup a lungo termine, vedere [conservazione dei backup a lungo termine](sql-database-long-term-retention.md)
- Per altre informazioni sul ripristino da backup, vedere [ripristino dal backup](sql-database-recovery-using-backups.md)


<!--HONumber=Dec16_HO2-->


