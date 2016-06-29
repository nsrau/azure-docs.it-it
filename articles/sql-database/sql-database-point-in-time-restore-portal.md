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
	ms.date="06/09/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Ripristinare un database SQL di Azure a un momento precedente con il portale di Azure


> [AZURE.SELECTOR]
- [Panoramica](sql-database-point-in-time-restore.md)
- [Portale di Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

Questo articolo illustra come ripristinare un database a un momento precedente da [Backup automatici del database SQL](sql-database-automated-backups.md) tramite il portale di Azure.

## Selezionare un database da ripristinare a un momento precedente

Per ripristinare un database nel portale di Azure, seguire questa procedura:

1.	Aprire il [portale di Azure](https://portal.azure.com).
2.  Sul lato sinistro della schermata fare clic su **ESPLORA** > **Database SQL**.
3.  Passare al database da ripristinare e selezionarlo.
4.  Nella parte superiore del pannello del database selezionare **Ripristina**:

    ![Ripristinare un database SQL di Azure](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  Specificare un nome di database, il momento specifico, quindi fare clic su OK:

    ![Ripristinare un database SQL di Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)


## Passaggi successivi

- [Finalizzare il database SQL di Azure ripristinato](sql-database-recovered-finalize.md)
- [Ripristino temporizzato](sql-database-point-in-time-restore.md)
- [Ripristino temporizzato tramite l'API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [Backup automatici del database SQL](sql-database-automated-backups.md)

## Risorse aggiuntive

- [Ripristino di un database eliminato](sql-database-restore-deleted-database.md)
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Ripristino geografico](sql-database-geo-restore.md)
- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->