<properties
   pageTitle="Ripristinare un database in Azure SQL Data Warehouse (Portale) | Microsoft Azure"
   description="Attività del portale di Azure per il ripristino di un database attivo o eliminato in Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/04/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Ripristinare un database in Azure SQL Data Warehouse (Portale)

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-restore-database-overview.md)
- [Portale](sql-data-warehouse-restore-database-portal.md)
- [PowerShell](sql-data-warehouse-restore-database-powershell.md)
- [REST](sql-data-warehouse-manage-restore-database-rest-api.md)

Attività di PowerShell per il ripristino di un database attivo o eliminato in Azure SQL Data Warehouse.

Attività contenute in questo argomento:

- Ripristino di un database attivo
- Ripristino di un database eliminato

## Prima di iniziare

Verificare la capacità DTU del database SQL. SQL Data Warehouse si ripristina su un nuovo database nel server logico di SQL. Per questo motivo, è importante verificare che il server SQL di cui si esegue il ripristino abbia una capacità DTU sufficiente per il nuovo database SQL Server. Vedere questo post di blog per altre informazioni su [come visualizzare e aumentare la quota DTU][].


## Ripristino di un database attivo

Per ripristinare un database:

1. Accedere al [Portale di Azure][].
2. Sul lato sinistro della schermata fare clic su **SFOGLIA**, quindi su **Database SQL**.
3. Individuare e selezionare il database.
4. Nella parte superiore del pannello database fare clic su **Ripristina**.
5. Specificare un nuovo **Nome database**, quindi selezionare un **Punto di ripristino** e fare clic su **Crea**.
6. Viene avviato il processo di ripristino del database che sarà possibile monitorare tramite **NOTIFICHE**.


## Ripristino di un database eliminato

Per ripristinare un database eliminato:

1. Accedere al [Portale di Azure][].
2. Sul lato sinistro della schermata fare clic su **SFOGLIA**, quindi su **Server SQL**.
3. Individuare il server e selezionarlo.
4. Scorrere verso il basso fino a Operazioni nel pannello del server e fare clic sul riquadro **Database eliminati**.
5. Fare clic sul database eliminato che si desidera ripristinare.
5. Specificare un nuovo **Nome database** e fare clic su **Crea**.
6. Viene avviato il processo di ripristino del database che sarà possibile monitorare tramite **NOTIFICHE**.


## Passaggi successivi
Per altre informazioni, vedere [Continuità aziendale del database SQL di Azure][] e [Panoramica sulla gestione][].

<!--Image references-->

<!--Article references-->
[Continuità aziendale del database SQL di Azure]: sql-database-business-continuity.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md
[Panoramica sulla gestione]: sql-data-warehouse-overview-manage.md

<!--MSDN references-->

<!--Blog references-->
[come visualizzare e aumentare la quota DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Portale di Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0608_2016-->