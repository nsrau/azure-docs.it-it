<properties
   pageTitle="Ripristinare un database dopo un'interruzione del servizio in SQL Data Warehouse | Microsoft Azure"
   description="Procedura per ripristinare un database dopo un'interruzione del servizio in SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/20/2016"
   ms.author="sahajs;barbkess"/>

# Ripristinare un database dopo un'interruzione del servizio in SQL Data Warehouse

Il ripristino geografico consente di ripristinare un database da un backup con ridondanza geografica. Il database ripristinato può essere creato su qualunque server in qualsiasi area di Azure. Il ripristino geografico è in grado di recuperare anche un database inaccessibile a causa di un guasto, poiché usa come origine un backup con ridondanza geografica. Oltre al ripristino dopo un'interruzione, il ripristino geografico può anche essere usato per altri scenari come ad esempio la migrazione o la copia del database in un server o un'area differenti.

## Quando avviare il ripristino
L'operazione di ripristino richiede la modifica della stringa di connessione SQL al momento del ripristino e può comportare una perdita di dati permanente. Pertanto, è necessario eseguirla solo quando è probabile che l'interruzione del servizio duri più a lungo dell'obiettivo del tempo di ripristino dell'applicazione. Usare i punti dati seguenti per determinare se il ripristino è possibile:

- Errore di connettività permanente per il database.
- Il portale di Azure visualizza un avviso relativo a un evento imprevisto con un vasto impatto nell'area.

## Ripristino tramite il ripristino geografico
Il ripristino di un database crea un nuovo database dall'ultimo backup con ridondanza geografica. È importante assicurarsi che il server in cui si esegue il ripristino abbia una capacità sufficiente DTU per il nuovo database. Vedere questo post di blog per altre informazioni su [come visualizzare e aumentare la quota DTU][].

### Portale di Azure
1. Accedere al [portale di Azure][].
2. Sul lato sinistro della schermata fare clic su **+NUOVO**, quindi selezionare **Dati e archiviazione** e poi ancora **SQL Data Warehouse**
3. Selezionare **BACKUP** come origine, quindi selezionare il backup con ridondanza geografica da ripristinare
4. Specificare il resto delle proprietà del database, quindi fare clic su **Crea**
5. Il processo di ripristino del database inizierà e potrà essere monitorato tramite **NOTIFICHE**

### PowerShell

Per ripristinare un database, usare il cmdlet [Restore-AzureRmSqlDatabase][].

> [AZURE.NOTE]  Per usare Azure PowerShell con SQL Data Warehouse, è necessario installare Azure PowerShell versione 1.0.3 o successiva. È possibile controllare la versione in uso eseguendo **Get-Module -ListAvailable -Name Azure**. È possibile installare la versione più recente usando [Installazione guidata piattaforma Web Microsoft][]. Per altre informazioni sull'installazione della versione più recente, vedere [Come installare e configurare Azure PowerShell][].

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Selezionare la sottoscrizione che contiene il database da ripristinare.
4. Selezionare il database che si desidera ripristinare.
5. Creare la richiesta di ripristino per il database.
6. Verificare lo stato del database recuperato con il ripristino geografico.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status

```

>[AZURE.NOTE] Per il server foo.database.windows.net, usare "foo" come -ServerName nei cmdlet di PowerShell sopraindicati.

### API REST
Usare REST per eseguire il ripristino del database a livello di codice.

1. Ottenere l'elenco dei database recuperabili mediante l'operazione [List Recoverable Databases][].
2. Ottenere il database che si desidera ripristinare mediante l'operazione [Get Recoverable Database][].
3. Creare la richiesta di ripristino mediante l'operazione [Create Database Recovery Request][].
4. Tenere traccia dello stato di ripristino mediante l'operazione [Database Operation Status][].

## Configurare il database dopo il ripristino
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
[Come installare e configurare Azure PowerShell]: ../powershell/powershell-install-configure.md
[panoramica sulla continuità aziendale per database SQL di Azure]: ../sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[List Recoverable Databases]: https://msdn.microsoft.com/library/azure/dn800984.aspx
[Get Recoverable Database]: https://msdn.microsoft.com/library/azure/dn800985.aspx
[Create Database Recovery Request]: https://msdn.microsoft.com/library/azure/dn800986.aspx
[Database Operation Status]: https://msdn.microsoft.com/library/azure/dn720371.aspx

<!--Blog references-->
[come visualizzare e aumentare la quota DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[portale di Azure]: https://portal.azure.com/
[Installazione guidata piattaforma Web Microsoft]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0518_2016-->