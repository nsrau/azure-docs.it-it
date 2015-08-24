<properties 
	pageTitle="Come utilizzare il database SQL (NET) | Microsoft Azure" 
	description="Introduzione al database SQL. Informazioni su come creare un'istanza di database SQL e connettersi ad essa usando ADO.NET, ODBC e il provider EntityClient." 
	services="sql-database" 
	documentationCenter=".net" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="jeffreyg"/>


# Come usare il database SQL di Azure in applicazioni .NET

Questa guida illustra come creare un server logico e un'istanza di database sul database SQL di Azure e come effettuare la connessione al database mediante le seguenti tecnologie del provider di dati di .NET Framework: ADO.NET, ODBC e EntityClient Provider.


## Informazioni sul database SQL

Il database SQL offre un sistema di gestione di database relazionali per Azure e si basa sulla tecnologia SQL Server. Con un'istanza di database SQL, è possibile eseguire facilmente il provisioning e la distribuzione nel cloud di soluzioni di database relazionali e usufruire di un data center distribuito che offre disponibilità, scalabilità e sicurezza di livello aziendale, con i vantaggi delle funzionalità predefinite di riparazione automatica e protezione dati.



## Accedere ad Azure

Database SQL offre servizi di archiviazione di dati relazionali, di accesso e di gestione in Azure. Per usarlo, è necessario disporre di una sottoscrizione di Azure.

1. Aprire un Web browser e passare al sito [http://azure.microsoft.com/](http://azure.microsoft.com). Per iniziare usando un account gratuito, fare clic su Versione di valutazione gratuita nell'angolo superiore destro e seguire le istruzioni visualizzate.

2. Verrà creato l'account e sarà possibile iniziare.


## Creare e configurare il database SQL

Sarà quindi possibile creare e configurare un database e un server. Nel portale di gestione di Azure i flussi di lavoro revisionati consentono di creare innanzitutto un database e quindi di eseguire il provisioning del server.

**Creare un documento di database e un logical server. **

1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com).

2. Fare clic su **NUOVO** nella parte inferiore della pagina.

3. Fare clic su **Data Services**.

4. Fare clic su **SQL Database**.

5. Fare clic su **Custom Create**.

6. In Name immettere un nome di database.

7. Scegliere un'edizione, una dimensione massima e le regole di confronto. Ai fini di questa guida, è possibile usare i valori predefiniti.

	Il database SQL offre tre edizioni: Base, Standard e Premium.

	Il valore MAXSIZE viene specificato alla prima creazione del database e può essere modificato in seguito tramite ALTER DATABASE. MAXSIZE consente di limitare le dimensioni del database.

	Per ogni database SQL creato su Azure sono effettivamente disponibili tre repliche di tale database. Ciò consente di assicurare una disponibilità elevata. Il failover è trasparente e incluso nel servizio.

8. In Server selezionare **New SQL Database Server**.

9. Fare clic sulla freccia per passare alla pagina successiva.

10. In Server Settings immettere un nome di account di accesso con autenticazione di SQL Server.

	Nel database SQL viene utilizzata l'autenticazione SQL tramite una connessione crittografata. Con il nome specificato verrà creato un nuovo account di accesso con autenticazione di SQL Server assegnato al ruolo predefinito del server sysadmin.

	L'account di accesso non può essere un indirizzo e-mail, un account utente Windows o un Windows Live ID. In un database SQL non sono supportate né l'autenticazione basata sulle attestazioni né l'autenticazione di Windows.

11. Impostare una password complessa composta da più di otto caratteri, usando una combinazione di caratteri in minuscolo e maiuscolo e da un numero o un simbolo.

12. Scegliere un'area, che determina la posizione geografica del server. Poiché le aree non possono essere cambiate facilmente, sceglierne una significativa per questo server. Selezionare la posizione più vicina. Per ridurre i costi relativi alla larghezza di banda in uscita e la latenza dei dati, è consigliabile mantenere il database e l'applicazione Azure nella stessa area.

13. Assicurarsi che la casella di controllo **Allow Azure Services to access the server** sia selezionata in modo da consentire la connessione al database tramite il portale di gestione per il database SQL, i servizi di archiviazione e altri servizi di Azure.

14. Al termine, fare clic sul segno di spunta nella parte inferiore della pagina.

Si noti che non è stato specificato un nome di server. Il database SQL genera automaticamente il nome del server per garantire che non vi siano voci DNS duplicate. Il nome del server è una stringa alfanumerica di dieci caratteri. Il nome del server di database SQL non è modificabile.

Dopo la creazione del database, farvi clic per aprire il dashboard corrispondente. Nel dashboard sono disponibili le stringhe di connessione che possono essere copiate e utilizzate nel codice dell'applicazione. Il dashboard include inoltre l'URL di gestione che deve essere specificato se ci si connette al database da Management Studio o da un altro strumento di amministrazione.


![Il dashboard di Database SQL](./media/sql-database-dotnet-how-to-use/SQLDbDashboard.PNG)


Nel passaggio successivo si configurerà il firewall in modo da consentire l'accesso alle connessioni da applicazioni in esecuzione nella rete.

**Configurare il firewall per il server logico**

1. Fare clic su **SQL Databases**, su **Servers** nella parte superiore della pagina e quindi sul server appena creato.

	![Configurare di un firewall](./media/sql-database-dotnet-how-to-use/SQLDBFirewall.PNG)

2. Fare clic su **Configure**.

3. Copiare l'indirizzo IP del client corrente, ovvero l'indirizzo IP su cui è in ascolto il router o il server proxy, se ci si connette da una rete. Il database SQL rileva l'indirizzo IP utilizzato dalla connessione corrente, pertanto è possibile creare una regola firewall per accettare le richieste di connessione da questo dispositivo.

4. Incollare l'indirizzo IP in START IP ADDRESS e in END IP ADDRESS per definire l'intervallo di indirizzi autorizzati ad accedere al server. In seguito, se si verificano errori di connessione in cui si informa che l'intervallo è troppo ristretto, è possibile modificare la regola per ampliarlo.

	Se i computer client utilizzano indirizzi IP assegnati dinamicamente, è necessario specificare un intervallo sufficientemente ampio da includere gli indirizzi IP assegnati ai computer della rete. Iniziare con un intervallo ristretto, quindi ampliarlo solo se è necessario.

5. Immettere un nome per la regola firewall, ad esempio il nome del computer o della società.

6. Fare clic sul segno di spunta accanto alla regola per salvarla.

	![Intervallo IP per le impostazioni del firewall](./media/sql-database-dotnet-how-to-use/SQLDBIPRange.PNG)

7. Per completare il passaggio, fare clic su **Save** nella parte inferiore della pagina. Se il pulsante **Save** non è visualizzato, aggiornare la pagina del browser.

A questo punto sono stati configurati un'istanza di database, un server logico, una regola del firewall che consente le connessioni in ingresso dall'indirizzo IP e un account di accesso dell'amministratore. È ora possibile connettersi al database a livello di codice.


## Connessione al database SQL

In questa sezione viene illustrato come effettuare la connessione a un'istanza del database SQL utilizzando diversi provider di dati .NET Framework. Per consigli importanti sulla connessione a un server di database SQL e a un database, vedere:


- [Connessione al database SQL: indicazioni importanti](../sql-database-connect-central-recommendations.md).


Se si decide di usare Visual Studio e la configurazione non include alcuna applicazione Web di Azure come front-end, non è necessario installare altri strumenti o SDK nel computer di sviluppo e si potrà iniziare subito a sviluppare l'applicazione.

Per lavorare con un database SQL, è possibile utilizzare in Visual Studio gli stessi strumenti di progettazione disponibili con SQL Server. Esplora server consente di visualizzare, ma non di modificare, gli oggetti di database. Sono disponibili tutte le funzionalità di Progettazione Entity Data Model di Visual Studio, che è possibile usare per creare modelli basati al database SQL per l'uso con Entity Framework.

## Uso del provider di dati .NET Framework per SQL Server

Lo spazio dei nomi **System.Data.SqlClient** corrisponde al provider di dati .NET Framework per SQL Server.

La stringa di connessione standard ha un aspetto analogo al seguente:

    Server=tcp:.database.windows.net;
    Database=;
    User ID=@;
    Password=;
    Trusted_Connection=False;
    Encrypt=True;

È possibile utilizzare la classe **SQLConnectionStringBuilder** per creare una stringa di connessione come mostrato nell'esempio di codice seguente:

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder();
    csBuilder.DataSource = xxxxxxxxxx.database.windows.net;
    csBuilder.InitialCatalog = testDB;
    csBuilder.Encrypt = true;
    csBuilder.TrustServerCertificate = false;
    csBuilder.UserID = MyAdmin;
    csBuilder.Password = pass@word1;

Se gli elementi di una stringa di connessione sono noti in anticipo, è possibile archiviarli in un file di configurazione e recuperarli in fase di esecuzione per creare una stringa di connessione. Di seguito viene riportata una stringa di connessione di esempio in un file di configurazione:

    <connectionStrings>
      <add name="ConnectionString" 
           connectionString ="Server=tcp:xxxxxxxxxx.database.windows.net;Database=testDB;User ID=MyAdmin@xxxxxxxxxx;Password=pass@word1;Trusted_Connection=False;Encrypt=True;" />
    </connectionStrings>

Per recuperare la stringa di connessione in un file di configurazione, utilizzare la classe **ConfigurationManager**:

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder(ConfigurationManager.ConnectionStrings["ConnectionString"].ConnectionString);
    After you have built your connection string, you can use the SQLConnection class to connect the SQL Database server:
    SqlConnection conn = new SqlConnection(csBuilder.ToString());
    conn.Open();

## Uso del provider di dati .NET Framework per ODBC

Lo spazio dei nomi **System.Data.Odbc** corrisponde al provider di dati .NET Framework per ODBC. Di seguito viene riportata una stringa di connessione di esempio per ODBC:

    Driver={SQL Server Native Client 10.0};
    Server=tcp:.database.windows.net;
    Database=;
    Uid=@;
    Pwd=;
    Encrypt=yes;

La classe **OdbcConnection** rappresenta una connessione aperta a un'origine dati. Di seguito è riportato un esempio di codice relativo all'apertura di una connessione:

    string cs = "Driver={SQL Server Native Client 10.0};" +
                "Server=tcp:xxxxxxxxxx.database.windows.net;" +
                "Database=testDB;"+
                "Uid=MyAdmin@xxxxxxxxxx;" +
                "Pwd=pass@word1;"+
                "Encrypt=yes;";

    OdbcConnection conn = new OdbcConnection(cs);
    conn.Open();

Se si desidera creare la stringa di connessione in fase di esecuzione, utilizzare la classe **OdbcConnectionStringBuilder**.

## Uso del provider EntityClient

Lo spazio dei nomi **System.Data.EntityClient** corrisponde al provider di dati .NET Framework per Entity Framework.

Entity Framework consente agli sviluppatori di creare applicazioni di accesso ai dati mediante la programmazione rispetto a un modello di applicazione concettuale invece che mediante la programmazione diretta rispetto a uno schema di archiviazione relazionale. Entity Framework si basa su provider di dati ADO.NET specifici per il tipo di archiviazione, fornendo un oggetto **EntityConnection** a un provider di dati e a un database relazionale sottostanti.

Per creare un oggetto **EntityConnection**, è necessario fare riferimento a un insieme di metadati che include i modelli e il mapping necessari, oltre a un nome di provider di dati specifico per il tipo di archiviazione e una stringa di connessione. Dopo la creazione dell'oggetto **EntityConnection**, sarà possibile accedere alle entità tramite le classi generate dal modello concettuale.

Di seguito è riportato un esempio di stringa di connessione:

    metadata=res://*/SchoolModel.csdl|res://*/SchoolModel.ssdl|res://*/SchoolModel.msl;provider=System.Data.SqlClient;provider connection string="Data Source=xxxxxxxxxx.database.windows.net;Initial Catalog=School;Persist Security Info=True;User ID=MyAdmin;Password=***********"

Per ulteriori informazioni, vedere [Provider EntityClient per Entity Framework](http://msdn.microsoft.com/library/bb738561.aspx).

## Passaggi successivi

Ora che sono state apprese le nozioni di base della connessione al Database SQL, vedere[Sviluppo: procedure (Database SQL)](http://msdn.microsoft.com/library/windowsazure/ee621787.aspx)
 

<!---HONumber=August15_HO7-->