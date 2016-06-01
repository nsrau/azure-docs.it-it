<properties
   pageTitle="Ripristino del database in Azure SQL Data Warehouse (portale di Azure) | Microsoft Azure"
   description="Attività delle API REST per il ripristino di un database attivo, eliminato o inaccessibile in Azure SQL Data Warehouse."
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

# Eseguire il backup e il ripristino di un database in Azure SQL Data Warehouse (API REST)

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-overview-manage-database-restore.md)
- [Portale](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST](sql-data-warehouse-manage-database-restore-rest-api.md)

Attività dell'API REST per il ripristino di un database in SQL Data Warehouse.

Attività contenute in questo argomento:

- Ripristino di un database attivo
- Ripristino di un database eliminato
- Ripristinare un database non accessibile da un'area geografica di Azure diversa

## Prima di iniziare

**Verificare la capacità DTU del database SQL.** SQL Data Warehouse si ripristina su un nuovo database nel server logico di SQL. Per questo motivo, è importante verificare che il server SQL di cui si esegue il ripristino abbia una capacità DTU sufficiente per il nuovo database SQL Server. Vedere questo post di blog per altre informazioni su [come visualizzare e aumentare la quota DTU][].

## Ripristino di un database attivo

Per ripristinare un database:

1. Ottenere l'elenco dei punti di ripristino del database utilizzando l'operazione Get Database Restore Points.
2. Iniziare il ripristino utilizzando l'operazione [Create database restore request][]
3. Monitorare lo stato del ripristino mediante l'operazione [Database Operation Status][].

>[AZURE.NOTE] Dopo aver completato il ripristino, sarà possibile configurare il database ripristinato seguendo la guida sulla [finalizzazione di un database ripristinato][].

## Ripristino di un database eliminato

Per ripristinare un database eliminato

1.	Elencare tutti i database eliminati ripristinabili mediante l'operazione [List Restorable Dropped Databases][].
2.	Ottenere i dettagli del database eliminato che si desidera ripristinare mediante l'operazione [Get Restorable Dropped Database][].
3.	Iniziare il ripristino utilizzando l'operazione [Create database restore request][].
4.	Monitorare lo stato del ripristino mediante l'operazione [Database Operation Status][].

>[AZURE.NOTE] Dopo aver completato il ripristino, sarà possibile configurare il database ripristinato seguendo la guida sulla [finalizzazione di un database ripristinato][].

## Eseguire il ripristino da un'area geografica di Azure

Per eseguire un ripristino geografico:

1. Ottenere l'elenco dei database recuperabili mediante l'operazione [List Recoverable Databases][].
2. Ottenere il database che si desidera ripristinare mediante l'operazione [Get Recoverable Database][].
3. Creare la richiesta di ripristino mediante l'operazione [Create Database Recovery Request][].
4. Tenere traccia dello stato di ripristino mediante l'operazione [Database Operation Status][].

### Configurare il database dopo l'esecuzione di un ripristino geografico
Si tratta di un elenco di controllo che può essere utilizzato per avere pronta la produzione di database ripristinati.

1. **Aggiornare le stringhe di connessione**: verificare che le stringhe di connessione degli strumenti client facciano riferimento al database appena ripristinato.
2. **Modificare le regole del firewall**: verificare le regole del firewall sul server di destinazione, quindi assicurarsi che le connessioni tra i computer client o Azure e il server e il database appena ripristinato siano abilitate.
3. **Verificare gli account di accesso al server e gli utenti del database**: controllare che tutti gli account di accesso usati dall'applicazione siano presenti sul server che ospita il database ripristinato. Ricreare gli account di accesso mancanti e concedere loro le autorizzazioni appropriate per il database ripristinato. 
4. **Abilitare il controllo**: se è necessario il controllo di accesso al database, attivarlo dopo il ripristino.

Il database ripristinato sarà abilitato TDE se il database di origine è abilitato per questa tecnologia.


## Passaggi successivi
Per altre informazioni sulle funzionalità di continuità aziendale delle edizioni del database SQL di Azure, leggere la [panoramica sulla continuità aziendale per database SQL di Azure][].

<!--Image references-->

<!--Article references-->
[panoramica sulla continuità aziendale per database SQL di Azure]: sql-database-business-continuity.md
[finalizzazione di un database ripristinato]: sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[come visualizzare e aumentare la quota DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0518_2016-->