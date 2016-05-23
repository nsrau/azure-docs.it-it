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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Eseguire il ripristino geografico di un database SQL di Azure da un backup con ridondanza geografica tramite il portale di Azure


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

Questo articolo illustra come ripristinare un database in un nuovo server con il ripristino geografico tramite il portale di Azure.

Il [ripristino geografico](sql-database-geo-restore.md) consente di ripristinare un database da un backup con ridondanza geografica per crearne uno nuovo. Il database può essere creato su qualunque server in qualsiasi area di Azure. Poiché usa un backup con ridondanza geografica come origine, è possibile usarlo per ripristinare un database anche se il database è inaccessibile a causa di un'interruzione del servizio. Il ripristino geografico è abilitato automaticamente per tutti i livelli di servizio senza costi aggiuntivi.

## Selezionare il database da ripristinare

Per ripristinare un database nel portale di Azure, seguire questa procedura:

1.	Aprire il [portale di Azure](https://portal.azure.com).
2.  Sul lato sinistro della schermata fare clic su **Nuovo** > **Dati e archiviazione** > **Database SQL**.
3.  Selezionare **Backup** come origine e quindi selezionare il backup con ridondanza geografica dal quale eseguire il ripristino.

    ![Ripristinare un database SQL di Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  Specificare un nome di database, un server in cui si vuole ripristinare il database e quindi fare clic su Crea:

## Passaggi successivi

- [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)
- [Esercitazioni di ripristino di emergenza](sql-database-disaster-recovery-drills.md)


## Risorse aggiuntive

- [Ripristino geografico](sql-database-geo-restore.md)
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->