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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Ripristinare un database SQL di Azure a un momento precedente con il portale di Azure


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

Questo articolo illustra come ripristinare un database a un momento precedente tramite il portale di Azure.

Il [**ripristino temporizzato**](sql-database-point-in-time-restore.md) è una funzionalità self-service che consente di ripristinare un database dai backup automatici effettuati per tutti i database a un momento precedente all'interno del periodo di conservazione del database. Per altre informazioni sui backup automatici e sui periodi di conservazione dei database, vedere [Panoramica della continuità aziendale](sql-database-business-continuity.md).

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
- [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)



## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->