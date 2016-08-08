<properties
	pageTitle="Ripristinare un database SQL di Azure da un backup con ridondanza geografica (portale di Azure) | Microsoft Azure"
	description="Eseguire il ripristino geografico di un database SQL di Azure da un backup con ridondanza geografica (portale di Azure)."
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


# Eseguire il ripristino geografico di un database SQL di Azure da un backup con ridondanza geografica tramite il portale di Azure


> [AZURE.SELECTOR]
- [Panoramica](sql-database-recovery-using-backups.md)
- [Ripristino geografico: PowerShell](sql-database-geo-restore-powershell.md)

Questo articolo illustra come ripristinare un database in un nuovo server con il ripristino geografico tramite il portale di Azure.

## Selezionare il database da ripristinare

Per ripristinare un database nel portale di Azure, seguire questa procedura:

1.	Aprire il [portale di Azure](https://portal.azure.com).
2.  Sul lato sinistro della schermata fare clic su **Nuovo** > **Dati e archiviazione** > **Database SQL**.
3.  Selezionare **Backup** come origine, quindi selezionare il backup con ridondanza geografica da ripristinare.

    ![Ripristinare un database SQL di Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  Specificare un nome di database, un server in cui si vuole ripristinare il database e quindi fare clic su Crea:


## Passaggi successivi

- Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
- Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
- Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
- Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md)
- Per altre informazioni sull'uso dei backup automatici per l'archiviazione, vedere [Copiare un database SQL di Azure](sql-database-copy.md)

<!---HONumber=AcomDC_0727_2016-->