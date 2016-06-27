<properties
   pageTitle="Ripristinare un'istanza di Azure SQL Data Warehouse (portale) | Microsoft Azure"
   description="Attività del portale di Azure per il ripristino di un'istanza di Azure SQL Data Warehouse."
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
   ms.date="06/11/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Ripristinare un'istanza di Azure SQL Data Warehouse (portale)

> [AZURE.SELECTOR]
- [Panoramica][]
- [Portale][]
- [PowerShell][]
- [REST][]

Questo articolo illustra come ripristinare un'istanza di Azure SQL Data Warehouse tramite il portale di Azure.

## Prima di iniziare

**Verificare la capacità in DTU.** Ogni istanza di SQL Data Warehouse è ospitata da un server logico SQL, il cui limite di capacità è misurato in DTU. Prima di ripristinare un'istanza di SQL Data Warehouse, è importante assicurarsi che il server logico SQL che ospita il database abbia una capacità in DTU sufficiente per il database da ripristinare. Per altre informazioni su [come visualizzare e aumentare la quota DTU][], vedere il relativo post di blog.


## Ripristinare un database attivo o sospeso

Per ripristinare un database:

1. Accedere al [Portale di Azure][].
2. Sul lato sinistro della schermata fare clic su **SFOGLIA**, quindi su **Database SQL**.
3. Individuare e selezionare il database.
4. Nella parte superiore del pannello del database fare clic su **Ripristina**.
5. Specificare un nuovo **Nome database**, selezionare un **Punto di ripristino** e quindi fare clic su **Crea**.
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
Per altre informazioni sulle funzionalità di continuità aziendale delle edizioni del database SQL di Azure, vedere [Panoramica: Continuità aziendale del cloud e ripristino di emergenza del database con database SQL][].

<!--Image references-->

<!--Article references-->
[Panoramica: Continuità aziendale del cloud e ripristino di emergenza del database con database SQL]: ./sql-database-business-continuity.md
[Panoramica]: ./sql-data-warehouse-restore-database-overview.md
[Portale]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->

<!--Blog references-->
[come visualizzare e aumentare la quota DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Portale di Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0615_2016-->