<properties
   pageTitle="Eseguire il backup e il ripristino in Azure SQL Data Warehouse (portale) | Microsoft Azure"
   description="Attività del portale di Azure per il ripristino di un database attivo, eliminato o inaccessibile in Azure SQL Data Warehouse."
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
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Eseguire il backup e il ripristino di un database in Azure SQL Data Warehouse (portale)

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-overview-manage-backup-and-restore.md)
- [Portale](sql-data-warehouse-manage-backup-and-restore-tasks-portal.md)
- [PowerShell](sql-data-warehouse-manage-backup-and-restore-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-backup-and-restore-tasks-rest-api.md)

Come eseguire il backup e il ripristino di un database in SQL Data Warehouse tramite il portale di Azure.

Attività contenute in questo argomento:

- Ripristino di un database attivo
- Ripristino di un database eliminato
- Ripristinare un database non accessibile da un'area geografica di Azure diversa

[AZURE.INCLUDE [Criteri di conservazione dei backup di SQL Data Warehouse](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## Prima di iniziare

Verificare la capacità DTU del database SQL. SQL Data Warehouse si ripristina su un nuovo database nel server logico di SQL. Per questo motivo, è importante verificare che il server SQL di cui si esegue il ripristino abbia una capacità DTU sufficiente per il nuovo database SQL Server. Vedere questo post di blog per altre informazioni su [come visualizzare e aumentare la quota DTU][].

## Ripristino di un database attivo

Per ripristinare un database da uno snapshot:

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


## Ripristino geografico di un data warehouse da un backup con ridondanza geografica

Per eseguire un ripristino geografico:

1. Accedere al [portale di Azure][].
2. Sul lato sinistro della schermata fare clic su **+NEW** (+NUOVO), selezionare **Data and Storage** (Dati e archiviazione) e quindi **SQL Data Warehouse**.
3. Selezionare **BACKUP** come origine e quindi selezionare il backup con ridondanza geografica dal quale eseguire il ripristino.
4. Specificare il resto delle proprietà del database, quindi fare clic su **Crea**
5. Viene avviato il processo di ripristino del database che sarà possibile monitorare tramite **NOTIFICHE**.

## Passaggi successivi
Per altre informazioni sulle funzionalità di continuità aziendale delle edizioni del database SQL di Azure, leggere la [panoramica sulla continuità aziendale per database SQL di Azure][].

<!--Image references-->

<!--Article references-->
[panoramica sulla continuità aziendale per database SQL di Azure]: sql-database-business-continuity.md
[Finalize a recovered database]: sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->

<!--Blog references-->
[come visualizzare e aumentare la quota DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Portale di Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0518_2016-->