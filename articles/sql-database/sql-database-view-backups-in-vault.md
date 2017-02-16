---
title: Visualizzare i backup nell&quot;insieme di credenziali dei Servizi di ripristino di Azure | Documentazione Microsoft
description: Riferimento rapido su come visualizzare i backup nell&quot;insieme di credenziali dei Servizi di ripristino di Azure e lo spazio usato da questi backup
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
ms.openlocfilehash: 723be587128c131670c9905572d808c176362fb8


---
# <a name="view-information-about-your-database-backups-in-long-term-backup-retention"></a>Visualizzare informazioni sui backup del database nella conservazione dei backup a lungo termine

In questo argomento procedurale viene illustrato come visualizzare le informazioni sui backup del database nella conservazione dei backup a lungo termine.

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
> Per un'esercitazione, vedere [Introduzione a Backup e ripristino per la protezione dei dati e il ripristino](sql-database-get-started-backup-recovery.md)
>

## <a name="next-steps"></a>Passaggi successivi

- Per configurare la conservazione a lungo termine di backup automatizzati in un insieme di credenziali di Servizi di ripristino di Azure, vedere [](sql-database-configure-long-term-retention.md)
- Per ripristinare un database da una conservazione dei backup a lungo termine, vedere l'articolo relativo al [ripristino da una conservazione a lungo termine](sql-database-restore-from-long-term-retention.md)
- Per informazioni sui backup automatici generati dal servizio, vedere l'articolo relativo ai [backup automatici](sql-database-automated-backups.md)
- Per altre informazioni sulla conservazione dei backup a lungo termine, vedere [conservazione dei backup a lungo termine](sql-database-long-term-retention.md)
- Per altre informazioni sul ripristino da backup, vedere [ripristino dal backup](sql-database-recovery-using-backups.md)


<!--HONumber=Dec16_HO2-->


