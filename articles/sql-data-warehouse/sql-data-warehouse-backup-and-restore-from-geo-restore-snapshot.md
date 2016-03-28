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
   ms.date="03/09/2016"
   ms.author="sahajs;barbkess"/>

# Ripristinare un database dopo un'interruzione del servizio in SQL Data Warehouse

Il ripristino geografico consente di ripristinare un database da un backup con ridondanza geografica per crearne uno nuovo. Il database può essere creato su qualunque server in qualsiasi area di Azure. Poiché usa un backup con ridondanza geografica come origine, è possibile usarlo per ripristinare un database anche se il database è inaccessibile a causa di un'interruzione del servizio. Oltre al ripristino dopo un'interruzione, il ripristino geografico può anche essere usato per altri scenari come ad esempio la migrazione o la copia del database in un server o un'area differenti.


## Quando avviare il ripristino
L'operazione di ripristino richiede la modifica della stringa di connessione SQL al momento del ripristino e può comportare una perdita di dati permanente. Pertanto, è necessario eseguirla solo quando è probabile che l'interruzione del servizio duri più a lungo dell'obiettivo del tempo di ripristino dell'applicazione. Usare i punti dati seguenti per determinare se il ripristino è possibile:

- Errore di connettività permanente per il database.
- Il portale di Azure visualizza un avviso relativo a un evento imprevisto con un vasto impatto nell'area.


## Ripristino tramite il ripristino geografico
Il ripristino di un database crea un nuovo database dall'ultimo backup con ridondanza geografica. È importante assicurarsi che il server in cui si esegue il ripristino abbia una capacità sufficiente DTU per il nuovo database. È possibile richiedere un aumento della quota da [contattare il supporto][].


### Portale di Azure
1. Accedere al [portale di Azure][].
2. Sul lato sinistro della schermata fare clic su **+NUOVO**, quindi selezionare **Dati e archiviazione** e poi ancora **SQL Data Warehouse**
3. Selezionare **BACKUP** come origine, quindi selezionare il backup con ridondanza geografica da ripristinare
4. Specificare il resto delle proprietà del database, quindi fare clic su **Crea**
5. Il processo di ripristino del database inizierà e potrà essere monitorato tramite **NOTIFICHE**


### PowerShell
Usare Azure PowerShell per eseguire il ripristino del database a livello di codice. Per scaricare il modulo di Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). È possibile controllare la versione in uso eseguendo Get-Module -ListAvailable -Name Azure. Questo articolo si basa su Microsoft Azure PowerShell versione 1.0.4.

Per ripristinare un database, usare il cmdlet [Start-AzureSqlDatabaseRecovery][].

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Selezionare la sottoscrizione che contiene il database da ripristinare.
4. Selezionare il database che si desidera ripristinare.
5. Creare la richiesta di ripristino per il database.
6. Monitorare lo stato del ripristino.

```

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$Database = Get-AzureRmSqlRecoverableDatabase -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>"

# Recover database
$RecoveryRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourSourceServerName>" -SourceDatabase $Database -TargetDatabaseName "<NewDatabaseName>" -TargetServerName "<YourTargetServerName>"

# Monitor progress of recovery operation
Get-AzureSqlDatabaseOperation -ServerName "<YourTargetServerName>" –OperationGuid $RecoveryRequest.RequestID

```

Se il server è foo.database.windows.net, utilizzare "foo" come NomeServer nei cmdlet sopraindicati di powershell.

### API REST
Usare REST per eseguire il ripristino del database a livello di codice.

1. Ottenere l'elenco dei database recuperabili mediante l'operazione [List Recoverable Databases][].
2. Ottenere il database che si desidera ripristinare mediante l'operazione [Get Recoverable Database][].
3. Creare la richiesta di ripristino mediante l'operazione [Create Database Recovery Request][].
4. Tenere traccia dello stato di ripristino mediante l'operazione [Database Operation Status][].



## Configurare il database dopo il ripristino
Si tratta di un elenco di controllo che può essere utilizzato per avere pronta la produzione di database ripristinati.

1. **Aggiornare le stringhe di connessione**: accertarsi che le stringhe di connessione degli strumenti client facciano riferimento al database appena ripristinato.
2. **Modificare le regole del firewall**: verificare le regole del firewall a livello di server di destinazione, quindi assicurarsi che le connessioni tra i computer client o Azure con il server e il database appena ripristinato siano abilitate.
3. **Verificare gli account di accesso al server e gli utenti del database**: verificare che tutti gli account di accesso usati dall'applicazione siano presenti sul server che ospita il database ripristinato. Ricreare gli account di accesso mancanti e concedere loro le autorizzazioni appropriate per il database ripristinato. 
4. **Abilitare il controllo**: se è necessario il controllo di accesso al database, occorre attivare il controllo dopo il ripristino del database.

Il database ripristinato sarà abilitato TDE se il database di origine è abilitato per questa tecnologia.


## Passaggi successivi
Per ulteriori informazioni sulle funzionalità di continuità aziendale di altre versioni del database SQL di Azure, leggere la [Panoramica sulla continuità aziendale per database SQL Azure][].


<!--Image references-->

<!--Article references-->
[Panoramica sulla continuità aziendale per database SQL Azure]: sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Start-AzureSqlDatabaseRecovery]: https://msdn.microsoft.com/library/azure/dn720224.aspx
[List Recoverable Databases]: http://msdn.microsoft.com/library/azure/dn800984.aspx
[Get Recoverable Database]: http://msdn.microsoft.com/library/azure/dn800985.aspx
[Create Database Recovery Request]: http://msdn.microsoft.com/library/azure/dn800986.aspx
[Database Operation Status]: http://msdn.microsoft.com/library/azure/dn720371.aspx

<!--Other Web references-->
[portale di Azure]: https://portal.azure.com/
[contattare il supporto]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!---HONumber=AcomDC_0316_2016-->