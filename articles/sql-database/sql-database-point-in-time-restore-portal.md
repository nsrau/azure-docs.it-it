<properties
	pageTitle="Ripristinare un database SQL di Azure a un momento precedente (portale di Azure) | Microsoft Azure"
	description="Ripristinare un database SQL di Azure a un momento precedente."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="07/17/2016"
	ms.author="sstein"
	ms.workload="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Ripristinare un database SQL di Azure a un momento precedente con il portale di Azure


> [AZURE.SELECTOR]
- [Panoramica](sql-database-recovery-using-backups.md)
- [Ripristino temporizzato: PowerShell](sql-database-point-in-time-restore-powershell.md)

Questo articolo illustra come ripristinare un database a un momento precedente usando i [backup automatici del database SQL](sql-database-automated-backups.md) tramite il portale di Azure.

## Selezionare un database da ripristinare a un momento precedente

Per ripristinare un database nel portale di Azure, seguire questa procedura:

1.	Aprire il [portale di Azure](https://portal.azure.com).
2.  Sul lato sinistro della schermata fare clic su **SFOGLIA** > **Database SQL**.
3.  Passare al database da ripristinare e selezionarlo.
4.  Nella parte superiore del pannello del database selezionare **Ripristina**:

    ![Ripristinare un database SQL di Azure](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  Specificare un nome di database, il momento specifico, quindi fare clic su OK:

    ![Ripristinare un database SQL di Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)



## Passaggi successivi

- Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
- Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
- Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
- Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md)
- Per altre informazioni sull'uso dei backup automatici per l'archiviazione, vedere [Copiare un database SQL di Azure](sql-database-copy.md)

<!---HONumber=AcomDC_0727_2016-->