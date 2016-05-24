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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Ripristinare un database SQL di Azure con il portale di Azure


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

Questo articolo illustra come ripristinare un database SQL di Azure eliminato.

Nel caso in cui venisse eliminato un database, il database SQL di Azure consente di ripristinare il database eliminato al momento dell'eliminazione. Il database SQL di Azure archivia il backup del database eliminato per il periodo di conservazione del database.

Il periodo di conservazione di un database eliminato è determinato dal livello di servizio associato al database prima della rimozione o dal numero di giorni in cui il database esiste ancora (viene usato il valore più basso). Per altre informazioni sulla conservazione dei database, leggere la [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

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

- [Finalizzare il database SQL di Azure ripristinato](sql-database-recovered-finalize.md)
- [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)



## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->