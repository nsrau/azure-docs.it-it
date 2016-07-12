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
- [Panoramica](sql-database-recovery-using-backups.md)
- [Ripristino di un database eliminato: PowerShell](sql-database-restore-deleted-database-powershell.md)

## Selezionare il database da ripristinare 

Per ripristinare un database nel portale di Azure, seguire questa procedura:

1.	Aprire il [portale di Azure](https://portal.azure.com).
2.  Sul lato sinistro della schermata fare clic su **SFOGLIA** > **Server SQL**.
3.  Passare al server con il database eliminato che si vuole ripristinare e selezionare il server
4.  Scorrere verso il basso fino alla sezione **Operazioni** nel pannello del server selezionare **Database eliminati**: ![Ripristinare un database SQL di Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  Fare clic sul database eliminato che si desidera ripristinare.
6.  Specificare un nome di database e fare clic su OK:

    ![Ripristinare un database SQL di Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)


## Passaggi successivi

- Per una panoramica sulla continuità aziendale, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
- Per informazioni sugli scenari di progettazione e ripristino della continuità aziendale, vedere [Scenari di continuità aziendale](sql-database-business-continuity-scenarios.md)
- Per altre informazioni sull'uso di backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
- Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Replica geografica attiva](sql-database-geo-replication-overview.md)
- Per altre informazioni sull'uso di backup automatici per l'archiviazione, vedere [Copia del database](sql-database-copy.md)

<!---HONumber=AcomDC_0629_2016-->