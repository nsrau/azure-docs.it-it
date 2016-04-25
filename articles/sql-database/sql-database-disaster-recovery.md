<properties 
   pageTitle="Ripristino di emergenza del database SQL" 
   description="Informazioni su come ripristinare un database da un errore o da un'interruzione del servizio del data center dell'area con le funzionalità di replica geografica attiva, di replica geografica standard e di ripristino geografico del database SQL." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="02/09/2016"
   ms.author="elfish"/>

# Ripristinare un database SQL di Azure in seguito a un'interruzione del servizio

Il database SQL Azure offre le seguenti funzionalità per il ripristino da un'interruzione del servizio:

- Replica geografica attiva [(blog)](http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/)
- Replica geografica standard [(blog)](http://azure.microsoft.com/blog/2014/09/03/azure-sql-database-standard-geo-replication/)
- Ripristino geografico [(blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)
- Nuove funzionalità della replica geografica [(blog)](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)

Per informazioni su come prepararsi per possibili emergenze e su quando ripristinare il database, vedere la pagina [Progettazione per la continuità aziendale](sql-database-business-continuity-design.md).

##Quando avviare il ripristino 

L'operazione di ripristino ha un impatto sull'applicazione. Richiede la modifica della stringa di connessione SQL e può comportare una perdita di dati permanente. Pertanto, è necessario eseguirla solo quando è probabile che l'interruzione del servizio duri più a lungo dell'obiettivo del tempo di ripristino dell'applicazione. Quando l'applicazione viene distribuita nell'ambiente di produzione, è consigliabile eseguire il monitoraggio regolare dell'integrità dell'applicazione e usare i punti dati seguenti per determinare se il ripristino è possibile:

1. Si è verificato un errore di connettività permanente dal livello applicazione al database.
2. Il portale di Azure visualizza un avviso relativo a un evento imprevisto con un vasto impatto nell'area.

> [AZURE.NOTE] Dopo avere ripristinato il database è possibile configurarlo per utilizzarlo seguendo la guida [Configurare il database dopo il ripristino](#postrecovery).

## Failover al database secondario con replica geografica
> [AZURE.NOTE] È necessario eseguire la configurazione per disporre di un database secondario da utilizzare per il failover. La funzionalità di replica geografica è disponibile solo per i database Standard e Premium. Informazioni su [come configurare la replica geografica](sql-database-business-continuity-design.md)

###Portale di Azure
Utilizzare il portale di Azure per terminare la relazione di copia continua con il database secondario con replica geografica.

1. Accedere al [portale di Azure](https://portal.Azure.com).
2. Sul lato sinistro della schermata fare clic su **SFOGLIA** e quindi selezionare **Database SQL**.
3. Individuare e selezionare il database. 
4. Nella parte inferiore del pannello del database selezionare la **mappa della replica geografica**.
4. In **Secondari** fare clic con il pulsante destro del mouse sulla riga con il nome del database che si desidera ripristinare e scegliere **Failover**.

###PowerShell
Utilizzare PowerShell per avviare il failover al database secondario con replica geografica usando il cmdlet [Set AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).
		
		$database = Get-AzureRMSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
		$database | Set-AzureRMSqlDatabaseSecondary –Failover -AllowDataLoss

###API REST 
Utilizzare REST per iniziare il failover a un database secondario a livello di programmazione.

1. Ottenere un collegamento di replica a un secondario specifico mediante l’operazione [Ottenere collegamento di replica](https://msdn.microsoft.com/library/mt600778.aspx).
2. Failover al secondario mediante [Impostare database secondario come primario](https://msdn.microsoft.com/library/mt582027.aspx) con la perdita di dati consentita. 

## Ripristino tramite il ripristino geografico

In caso di interruzione del servizio di un database, è possibile ripristinare il database dalla più recente copia di backup con ridondanza geografica usando la funzionalità di ripristino geografico.

> [AZURE.NOTE] Quando si ripristina un database viene creato un nuovo database. È importante assicurarsi che il server in cui si esegue il ripristino abbia una capacità sufficiente DTU per il nuovo database. È possibile richiedere un aumento della quota da [contattare il supporto](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/).

###Portale di Azure (ripristino in un database autonomo)
Per ripristinare un database SQL tramite il Ripristino geografico nel portale di Azure, osservare la seguente procedura.

1. Accedere al [Portale di Azure](https://portal.Azure.com).
2. Sul lato sinistro della schermata fare clic su **NUOVO**, selezionare **Dati e archiviazione** e quindi **Database SQL**.
2. Selezionare **BACKUP** come origine e quindi selezionare la copia di backup con ridondanza geografica che si desidera ripristinare.
3. Specificare il resto delle proprietà del database e quindi fare clic su **Crea**.
4. Il processo di ripristino del database inizierà e potrà essere monitorato tramite **NOTIFICHE** sul lato sinistro della schermata.

###Portale di Azure (ripristino in un pool di database elastici)
Per ripristinare un database SQL mediante ripristino geografico in un pool di database elastici tramite il portale, seguire queste istruzioni.

1. Accedere al [portale di Azure](https://portal.Azure.com).
2. Sul lato sinistro della schermata selezionare **Sfoglia** e quindi selezionare **Pool elastici SQL**.
3. Selezionare il pool in cui si vuole eseguire il ripristino geografico del database.
4. Nella parte superiore del pannello del pool elastico, selezionare **Crea database**.
5. Selezionare **BACKUP** come origine e quindi selezionare la copia di backup con ridondanza geografica che si desidera ripristinare.
6. Specificare il resto delle proprietà del database e quindi fare clic su **Crea**.
7. Il processo di ripristino del database inizierà e potrà essere monitorato tramite **NOTIFICHE** sul lato sinistro della schermata.

###PowerShell 
> [AZURE.NOTE] Il ripristino geografico con PowerShell supporta attualmente solo il ripristino in un database autonomo. Per il ripristino geografico in un pool di database elastici usare il [portale di Azure](https://portal.Azure.com).

Per ripristinare un database SQL tramite ripristino geografico con PowerShell, avviare una richiesta di ripristino geografico con il cmdlet [start-AzureSqlDatabaseRecovery](https://msdn.microsoft.com/library/azure/dn720224.aspx).

		$Database = Get-AzureSqlRecoverableDatabase -ServerName "ServerName" –DatabaseName “DatabaseToBeRecovered"
		$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –TargetServerName “TargetServerName”
		Get-AzureSqlDatabaseOperation –ServerName "TargetServerName" –OperationGuid $RecoveryRequest.RequestID

###API REST 

Usare REST per eseguire il ripristino del database a livello di codice.

1.	Ottenere l'elenco dei database recuperabili mediante l'operazione [List Recoverable Databases](http://msdn.microsoft.com/library/azure/dn800984.aspx).
	
2.	Ottenere il database che si desidera ripristinare mediante l'operazione [Get Recoverable Database](http://msdn.microsoft.com/library/azure/dn800985.aspx).
	
3.	Creare la richiesta di ripristino mediante l'operazione [Create Database Recovery Request](http://msdn.microsoft.com/library/azure/dn800986.aspx).
	
4.	Tenere traccia dello stato di ripristino mediante l'operazione [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx).
 
## Configurare il database dopo il ripristino<a name="postrecovery"></a>

Si tratta di un elenco di controllo delle attività che può essere utilizzato per avere pronta la produzione di database ripristinati.

### Aggiornare le stringhe di connessione

Accertarsi che le stringhe di connessione dell'applicazione facciano riferimento al database appena ripristinato. Aggiornare le stringhe di connessione, se si verifica una delle situazioni seguenti:

  + Nel database ripristinato viene utilizzato un nome diverso rispetto al nome del database di origine
  + Il database ripristinato si trova in un server diverso rispetto al server di origine

Per ulteriori informazioni sulla modifica delle stringhe di connessione, vedere [Connessioni al database SQL di Azure: indicazioni principali ](sql-database-connect-central-recommendations.md).
 
### Modificare le regole del firewall
Verificare le regole del firewall a livello di server e a livello di database, quindi assicurarsi che le connessioni tra i computer client o Azure con il server e il database appena ripristinato siano abilitate Per altre informazioni, vedere [Procedura: Configurare le impostazioni del firewall (Database SQL di Azure)](sql-database-configure-firewall-settings.md).

### Verificare gli account di accesso al server e gli utenti del database

Verificare che tutti gli account di accesso utilizzati dall'applicazione siano presenti sul server che ospita il database ripristinato. Ricreare gli account di accesso mancanti e concedere loro le autorizzazioni appropriate per il database ripristinato. Per ulteriori informazioni, vedere [Gestione di database e account di accesso in database SQL di Azure](sql-database-manage-logins.md).

Verificare che ciascun utente del database ripristinato sia associato a un account di accesso a un server valido. Utilizzare l'istruzione ALTER USER per eseguire il mapping dell'utente all'account di accesso a un server valido. Per ulteriori informazioni, vedere [ALTER USER](http://go.microsoft.com/fwlink/?LinkId=397486).


### Avvisi di telemetria di configurazione

Verificare che le impostazioni della regola di avviso esistenti facciano riferimento al database ripristinato. Aggiornare la configurazione se si verifica una delle situazioni seguenti:

  + Nel database ripristinato viene utilizzato un nome diverso rispetto al nome del database di origine
  + Il database ripristinato si trova in un server diverso rispetto al server di origine

Per altre informazioni sulle regole di avviso per il database, vedere [Ricevere notifiche di avviso](../azure-portal/insights-receive-alert-notifications.md) e [Tracciare l’integrità del servizio](../azure-portal/insights-service-health.md).


### Attivare il controllo

Se è necessario il controllo di accesso al database, occorre attivare il controllo dopo il ripristino del database. Un indicatore efficace del fatto che è necessario un controllo è che le applicazioni client utilizzano stringhe di connessione protette in base a un modello di *.database.secure.windows.net. Per altre informazioni, vedere l'[Introduzione al controllo del database SQL](sql-database-auditing-get-started.md).

<!---HONumber=AcomDC_0413_2016-->