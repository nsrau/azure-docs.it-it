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
	ms.date="06/17/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Eseguire il ripristino geografico di un database SQL di Azure da un backup con ridondanza geografica tramite il portale di Azure


> [AZURE.SELECTOR]
- [Panoramica](sql-database-geo-restore.md)
- [Portale di Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

Questo articolo illustra come ripristinare un database in un nuovo server con il ripristino geografico tramite il portale di Azure.

## Selezionare il database da ripristinare

Per ripristinare un database nel portale di Azure, seguire questa procedura:

1.	Aprire il [portale di Azure](https://portal.azure.com).
2.  Sul lato sinistro della schermata fare clic su **Nuovo** > **Dati e archiviazione** > **Database SQL**.
3.  Selezionare **Backup** come origine e quindi selezionare il backup con ridondanza geografica da ripristinare.

    ![Ripristinare un database SQL di Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  Specificare un nome di database, un server in cui si vuole ripristinare il database e quindi fare clic su Crea:

## Passaggi successivi

- Per informazioni dettagliate su come ripristinare un database SQL di Azure mediante il portale di Azure da un backup con ridondanza geografica, vedere l'articolo sul [ripristino geografico nel portale di Azure](sql-database-geo-restore-portal.md).
- Per informazioni dettagliate su come ripristinare un database SQL di Azure da un backup con ridondanza geografica, vedere l'articolo sul [ripristino geografico tramite PowerShell](sql-database-geo-restore.md).
- Per una spiegazione completa su come eseguire il ripristino dopo un'interruzione del servizio, vedere l'articolo sul [ripristino dopo un'interruzione](sql-database-disaster-recovery.md).

## Risorse aggiuntive

- [Scenari di continuità aziendale](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->