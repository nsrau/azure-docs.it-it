<properties
	pageTitle="Ripristinare un database SQL di Azure eliminato (Portale di Azure) | Microsoft Azure"
	description="Ripristinare un database SQL di Azure (Portale di Azure)"
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
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Ripristinare un database SQL di Azure con il portale di Azure

> [AZURE.SELECTOR]
- [Panoramica](sql-database-restore-deleted-database.md)
- [Portale di Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)


## Selezionare il database da ripristinare 

Per ripristinare un database nel portale di Azure, seguire questa procedura:

1.	Aprire il [portale di Azure](https://portal.azure.com).
2.  Sul lato sinistro della schermata fare clic su **ESPLORA** > **Server SQL**.
3.  Passare al server con il database eliminato che si vuole ripristinare e selezionare il server
4.  Scorrere verso il basso fino alla sezione **Operazioni** nel pannello del server selezionare **Database eliminati**: ![Ripristinare un database SQL di Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  Fare clic sul database eliminato che si desidera ripristinare.
6.  Specificare un nome di database e fare clic su OK:

    ![Ripristinare un database SQL di Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## Passaggi successivi

- Per informazioni dettagliate su come ripristinare un database eliminato tramite PowerShell, vedere l'articolo su come [ripristinare un database eliminato tramite PowerShell](sql-database-restore-deleted-database-powershell.md).
- Per informazioni su come ripristinare un database eliminato, vedere [ripristinare un database eliminato utilizzando l'API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Per informazioni dettagliate su come ripristinare un database eliminato, vedere l'articolo su come [ripristinare un database eliminato](sql-database-restore-deleted-database.md).
- Per informazioni dettagliate sul backup automatico dei database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).

## Risorse aggiuntive

- [Ripristino temporizzato](sql-database-point-in-time-restore.md)
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Ripristino geografico](sql-database-geo-restore.md)
- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->