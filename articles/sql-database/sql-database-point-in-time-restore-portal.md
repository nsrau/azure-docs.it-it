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
	ms.date="06/17/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
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

- Per una panoramica sulla continuità aziendale, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
- Per informazioni sugli scenari di progettazione e ripristino della continuità aziendale, vedere [Scenari di continuità aziendale](sql-database-business-continuity-scenarios.md)
- Per altre informazioni sull'uso di backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
- Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Replica geografica attiva](sql-database-geo-replication-overview.md)
- Per altre informazioni sull'uso di backup automatici per l'archiviazione, vedere [Copia del database](sql-database-copy.md)

<!---HONumber=AcomDC_0629_2016-->