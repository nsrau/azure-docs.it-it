<properties 
    pageTitle="Configurare la replica geografica per il database SQL di Azure con PowerShell | Microsoft Azure" 
    description="Replica geografica per il database SQL di Azure con PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="12/01/2015"
    ms.author="sstein"/>

# Configurare la replica geografica per il database SQL di Azure



> [AZURE.SELECTOR]
- [Azure portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


Questo articolo illustra come configurare la replica geografica per il database SQL con PowerShell.

La replica geografica consente di creare un massimo di 4 database di replica (secondari) in località, o aree geografiche, di data center diverse. I database secondari sono disponibili in caso di interruzione di un data center o dell'impossibilità di connettersi al database primario.

La replica geografica è disponibile solo per i database Standard e Premium.

I database Standard possono avere un solo database secondario non accessibile in lettura e devono usare l'area consigliata. I database Premium possono avere un massimo di quattro database secondari accessibili in lettura in una qualsiasi della aree disponibili.

Per configurare la replica geografica, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Un database SQL di Azure logico: il database primario che si vuole replicare in una area geografica diversa.
- Azure PowerShell 1.0 o versione successiva È possibile scaricare e installare i moduli di Azure PowerShell dalla pagina [Come installare e configurare Azure PowerShell](powershell-install-configure.md).




## Configurare le credenziali e selezionare la sottoscrizione

È innanzitutto necessario stabilire l'accesso all'account Azure, poi avviare PowerShell ed eseguire il cmdlet seguente. Nella schermata di accesso utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.


	Login-AzureRmAccount

Dopo aver effettuato l'accesso, sullo schermo verranno visualizzate informazioni tra cui l'ID usato per l'accesso con le sottoscrizioni di Azure per le quali si dispone dell'accesso.


### Selezionare la sottoscrizione ad Azure

Per selezionare la sottoscrizione, è necessario il relativo ID. Questo può essere copiato dal passaggio precedente oppure, se si dispone di più sottoscrizioni e sono necessarie altre informazioni, è possibile eseguire il cmdlet **Get-AzureRmSubscription** e copiare le informazioni di sottoscrizione desiderate dal set di risultati. Il cmdlet seguente usa l'ID sottoscrizione per impostare la sottoscrizione corrente:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Dopo aver eseguito correttamente il cmdlet **Select-AzureRmSubscription**, si ritornerà al prompt dei comandi di PowerShell.



## Aggiungere un database secondario


La procedura seguente crea un nuovo database secondario in una relazione di replica geografica.
  
Per abilitare un database secondario è necessario essere il proprietario o il comproprietario della sottoscrizione.

È possibile usare il cmdlet **New-AzureRmSqlDatabaseSecondary** per aggiungere un database secondario in un server partner a un database locale nel server a cui si è connessi, ovvero il database primario.

Il cmdlet sostituisce **Start-AzureSqlDatabaseCopy** con il parametro **–IsContinuous**. Genera un oggetto **AzureRmSqlDatabaseSecondary** che può essere usato da altri cmdlet per identificare chiaramente un collegamento di replica specifico. Il risultato di questo cmdlet verrà restituito dopo aver creato il database secondario e averne effettuato correttamente il seeding. A seconda delle dimensioni del database, l'operazione può richiedere da pochi minuti a diverse ore.

Il database replicato nel server secondario avrà lo stesso nome del database nel server primario e, per impostazione predefinita, lo stesso livello di servizio. Il database secondario può essere accessibile o non accessibile in lettura e può essere un database singolo o un database elastico. Per altre informazioni, vedere [New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) e [Livelli di servizio del database SQL](sql-database-service-tiers.md). Dopo aver creato ed effettuato il seeding del database secondario, inizierà la replica dei dati dal database primario al nuovo database secondario. I passaggi seguenti descrivono come eseguire questa attività con PowerShell per creare database secondari accessibili e non accessibili in lettura, sia con un database singolo che con un database elastico.

Se il database partner esiste già, ad esempio come risultato della terminazione di una precedente relazione di replica geografica, il comando non riuscirà.



### Aggiungere un database secondario non accessibile in lettura (database singolo)

Il comando seguente crea un database secondario non accessibile in lettura del database "mydb" nel server "srv2" nel gruppo di risorse "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "None"



### Aggiungere un database secondario accessibile in lettura (database singolo)

Il comando seguente crea un database secondario accessibile in lettura del database "mydb" nel server "srv2" nel gruppo di risorse "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### Aggiungere un database secondario non accessibile in lettura (database elastico)

Il comando seguente crea un database secondario non accessibile in lettura del database "mydb" nel pool di database elastici denominato "ElasticPool1" del server "srv2" nel gruppo di risorse "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "None"


### Aggiungere un database secondario accessibile in lettura (database elastico)

Il comando seguente crea un database secondario accessibile in lettura del database "mydb" nel pool di database elastici denominato "ElasticPool1" del server "srv2" nel gruppo di risorse "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## Rimuovere un database secondario

È possibile usare il cmdlet **Remove-AzureRmSqlDatabaseSecondary** per terminare definitivamente la relazione di replica tra un database secondario e il relativo database primario. Dopo la terminazione della relazione, il database secondario diventa un database accessibile in lettura e scrittura. Se la connettività al database secondario viene interrotta, il comando riesce ma il database diventerà di nuovo accessibile in lettura e scrittura al ripristino della connettività. Per altre informazioni, vedere [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) e [Livelli di servizio del database SQL](sql-database-service-tiers.md).

Questo cmdlet sostituisce Stop-AzureSqlDatabaseCopy per la replica.

Questa rimozione equivale a una terminazione forzata che rimuove il collegamento di replica. Il database secondario diventa quindi un database autonomo che non viene completamente replicato prima della terminazione. Tutti i dati relativi al collegamento vengono puliti sia sui database primario e secondario precedenti, se e quando sono disponibili. Il risultato di questo cmdlet verrà restituito quando viene rimosso il collegamento di replica.


Per rimuovere il database secondario gli utenti devono aver accesso in scrittura sia database primario e secondario, secondo il controllo degli accessi in base al ruolo. Per altre informazioni, vedere Controllo degli accessi in base al ruolo.

Il codice seguente rimuove il collegamento di replica del database denominato "mydb" nel server "srv2" del gruppo di risorse "rg2".

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 




## Avviare un failover pianificato

Usare il cmdlet **Set-AzureRmSqlDatabaseSecondary** con il parametro **-Failover** per alzare di livello un database secondario a nuovo database primario, abbassando di livello il database primario esistente a database secondario. Questa funzionalità è progettata per il failover pianificato, ad esempio durante le esercitazioni sul ripristino di emergenza, e richiede che il database primario sia disponibile.

Il comando esegue il flusso di lavoro seguente:

1. Passa temporaneamente alla modalità di replica sincrona, in modo che tutte le transazioni in sospeso siano scaricate nel server secondario.

2. Scambia i ruoli dei due database nella relazione di replica geografica.

Questa sequenza assicura che non si verifichino perdite di dati. Per un breve periodo, da 0 a 25 secondi, entrambi i database non sono disponibili mentre vengono scambiati i ruoli. Il completamento dell'intera operazione dovrebbe richiedere meno di un minuto in circostanze normali. Per altre informazioni, vedere [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).


> [AZURE.NOTE]Se il database primario non è disponibile quando si esegue il comando, questo non riuscirà e verrà visualizzato un messaggio di errore in cui è indicato che il server primario non è disponibile. In rari casi, è possibile che l'operazione non possa essere completata e appaia bloccata. In questo caso l'utente può eseguire il comando di failover forzato, ovvero un failover non pianificato, e accettare la perdita dei dati.



Il risultato di questo cmdlet verrà restituito quando il processo di scambio da database secondario a primario sarà completato.

Il comando seguente cambia in primario i ruoli del database denominato "mydb” nel server "srv2” del gruppo di risorse "rg2”. Il database primario originale a cui è stato connesso "db2” diventerà secondario dopo la sincronizzazione completa dei due database.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover



## Avviare un failover non pianificato dal database primario al database secondario


È possibile usare il cmdlet **Set-AzureRmSqlDatabaseSecondary** con i parametri **–Failover** e **-AllowDataLoss** per alzare di livello un database secondario a nuovo database primario in maniera non pianificata, forzando l'abbassamento di livello del database primario esistente a database secondario in un momento in cui il database primario non è più disponibile.

Questa funzionalità è progettata per il ripristino di emergenza quando il ripristino della disponibilità del database è critica ed è accettabile una perdita di dati. Quando si richiama il failover forzato, il database secondario specificato diventa immediatamente il database primario e inizia ad accettare le transazioni di scrittura. Non appena il database primario originale può riconnettersi a questo nuovo database primario dopo l'operazione di failover forzato, viene eseguito un backup incrementale nel database primario originale e il database primario precedente viene convertito in un database secondario per il nuovo database primario. In seguito si tratterà semplicemente di una replica del nuovo database.

Poiché tuttavia il ripristino temporizzato non è supportato nei database secondari, se l'utente vuole recuperare i dati di cui è stato eseguito il commit nel database primario precedente e che non sono stati replicati nel nuovo database primario, dovrà rivolgersi al supporto tecnico per il ripristino di un database al backup del log noto.

> [AZURE.NOTE]Se il comando viene eseguito quando i database primario e secondario sono online, il database primario precedente diventerà il nuovo database secondario, ma non verrà eseguita alcuna sincronizzazione dei dati con la conseguente possibile perdita di dati.


Se il database primario ha più database secondari, il comando riuscirà in parte. Il database secondario su cui è stato eseguito il comando diventerà il database primario. Il database primario precedente rimarrà tuttavia primario, ovvero i due database primari rimarranno in uno stato non coerente e connessi da un collegamento di replica sospeso. L'utente dovrà ripristinare manualmente questa configurazione usando un'API di "rimozione del database secondario" in uno di questi database primari.


Il comando seguente cambia i ruoli del database denominato "mydb” in primario quando il database primario non è disponibile. Il database primario originale a cui è stato connesso "mydb” diventerà secondario dopo essere ritornato online. A questo punto la sincronizzazione potrebbe causare una perdita di dati.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss



## Monitorare la configurazione di replica geografica e l'integrità

Le attività di monitoraggio includono il controllo della configurazione della replica geografica e dell'integrità della replica dei dati.

È possibile usare [Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) per recuperare le informazioni sui collegamenti di replica visibili nella vista del catalogo sys.geo\_replication\_links.

Il comando seguente recupera lo stato del collegamento di replica tra il database primario "mydb” e il database secondario nel server "srv2” del gruppo di risorse "rg2”.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb”
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”



   

## Passaggi successivi

- [Esercitazioni di ripristino di emergenza](sql-database-disaster-recovery-drills.md)




## Risorse aggiuntive

- [Nuove funzionalità di replica geografica in evidenza](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication)
- [Progettazione di applicazioni cloud per la continuità aziendale mediante la replica geografica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1210_2015-->