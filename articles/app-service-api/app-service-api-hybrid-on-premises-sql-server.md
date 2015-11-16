<properties 
	pageTitle="Connettersi a un'istanza di SQL Server locale da un'app per le API nel servizio app di Azure mediante le connessioni ibride" 
	description="Creare un'app per le API in Microsoft Azure e connetterla a un database SQL Server locale"
	services="app-service\api" 
	documentationCenter="" 
	authors="TomArcher" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="tdykstra"/>

# Connettersi a un'istanza di SQL Server locale da un'app per le API nel servizio app di Azure mediante le connessioni ibride

Le connessioni ibride possono connettere le app per le API del [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) a risorse locali che usano una porta TCP statica. Le risorse supportate includono Microsoft SQL Server, MySQL, HTTP API Web, servizi mobili e la maggior parte dei servizi Web personalizzati.

In questa esercitazione si apprenderà come creare un'app per le API del servizio app nel [portale di anteprima di Azure](http://go.microsoft.com/fwlink/?LinkId=529715) che si connette a un database locale di SQL Server mediante la funzionalità Connessione ibrida. Questa esercitazione presuppone che l'utente non abbia mai usato Azure o SQL Server.

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Prerequisiti

Per completare l'esercitazione, sono necessari i prodotti seguenti. Sono tutti disponibili in versioni gratuite, quindi è possibile avviare le attività di sviluppo per soluzioni di Azure in modo completamente gratuito.

- **Sottoscrizione di Azure**: per una sottoscrizione gratuita, vedere [Versione di valutazione gratuita di Azure](/pricing/free-trial/). 

- **Visual Studio**: per scaricare una versione di valutazione gratuita di Visual Studio 2013 o Visual Studio 2015, vedere [Download di Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). Installare una di queste versioni prima di continuare. Le schermate di questa esercitazione sono state create in Visual Studio 2013.

- **SQL Server 2014 Express with Tools**: scaricare Microsoft SQL Server Express gratuitamente dalla [pagina del database della piattaforma Web Microsoft](https://www.microsoft.com/it-IT/download/details.aspx?id=42299). Più avanti nell'esercitazione verrà illustrato come [installare SQL Server](#InstallSQLDB) per assicurare che sia configurato correttamente.

- **SQL Server Management Studio Express**: incluso con il download di SQL Server 2014 Express with Tools citato sopra; tuttavia, per installarlo separatamente è possibile scaricarlo e installarlo dalla [pagina di download di SQL Server Express](https://www.microsoft.com/it-IT/download/details.aspx?id=42299).

In questa esercitazione si presuppone che l'utente disponga di una sottoscrizione di Azure, che abbia installato Visual Studio 2013 e che abbia installato o abilitato .NET Framework 3.5. L'esercitazione illustra come installare SQL Server 2014 Express in una configurazione adatta alla funzionalità Connessioni ibride di Azure (un'istanza predefinita con la porta TCP statica). Prima di iniziare l'esercitazione, scaricare, ma non installare, SQL Server 2014 Express with Tools dalla posizione indicata sopra, se SQL Server non è installato.

### Note
Per usare un'istanza di SQL Server locale o un database SQL Server Express con una connessione ibrida, TCP/IP deve essere abilitato su una porta statica. A differenza delle istanze denominate, le istanze predefinite di SQL Server usano la porta statica 1433.

Il computer in cui si installa l'agente Hybrid Connection Manager locale:

- Deve avere una connettività in uscita ad Azure usando:

> <table border="1">
    <tr>
       <th><strong>Porta</strong></th>
        <th>Motivo</th>
    </tr>
    <tr>
        <td>80</td>
        <td><strong>Obbligatorio</strong> per la porta HTTP per la convalida del certificato e, facoltativamente, per la connettività dati.</td>
    </tr>
    <tr>
        <td>443</td>
        <td><strong>Facoltativo</strong> per la connettività dei dati. Se la connettività in uscita alla porta 443 non è disponibile, viene usata la porta TCP 80.</td>
    </tr>
	<tr>
        <td>5671 e 9352</td>
        <td><strong>Consigliato</strong> ma facoltativo per la connettività dei dati. Notare che questa modalità normalmente genera una maggiore velocità effettiva. Se la connettività in uscita a questa porta non è disponibile, viene usata la porta TCP 443.</td>
	</tr>
</table>

- Deve essere in grado di raggiungere il *nome host*:* numero di porta* della risorsa locale. 

I passaggi indicati in questo articolo presuppongono che l'utente usi il browser dal computer che ospiterà l'agente di connessione ibrida locale.

Se SQL Server è già installato in una configurazione e in un ambiente che soddisfano le condizioni descritte sopra, è possibile ignorare questo passaggio e iniziare dal passaggio [Creare un database SQL Server locale](#CreateSQLDB).

<a name="InstallSQL"></a>
## Installare SQL Server Express, abilitare TCP/IP e creare un database SQL Server locale

Questa sezione illustra come installare SQL Server Express, abilitare TCP/IP e creare un database in modo che l'app per le API funzioni con il [portale di anteprima di Azure](https://portal.azure.com).

<a name="InstallSQLDB"></a>
### Installare SQL Server Express

1. Per installare SQL Server Express, scaricare il file **SQLEXPRWT\_x64\_ENU.exe** (versione a 64 bit) o **SQLEXPR\_x86\_ENU.exe** (versione a 32 bit) ed estrarlo nella cartella desiderata. 

2. Dopo l'estrazione dei file di installazione di SQL Server Express, eseguire **setup.exe**.

3. Quando viene visualizzato il **Centro installazione SQL Server**, scegliere **Nuova installazione autonoma di SQL Server o aggiunta di funzionalità a un'installazione esistente**.

	![Centro installazione SQL Server](./media/app-service-api-hybrid-on-premises-sql-server/sql-server-installation-center.png)

4. Seguire le istruzioni, accettando le scelte e le impostazioni predefinite, fino a visualizzare la pagina **Configurazione istanza**.
	
5. Nella pagina **Configurazione istanza** scegliere **Istanza predefinita**, quindi fare clic su **Avanti**.
	
	![Configurazione istanza](./media/app-service-api-hybrid-on-premises-sql-server/instance-configuration.png)
	
	L'istanza predefinita di SQL Server è in ascolto delle richieste provenienti dai client SQL Server sulla porta statica 1433, che è la porta richiesta dalla funzionalità Connessioni ibride. Le istanze denominate usano le porte dinamiche e UDP, che non sono supportate da Connessioni ibride.
	
6. Accettare i valori predefiniti nella pagina **Configurazione server**, quindi fare clic su **Avanti**.
	
7. Nella pagina **Configurazione del motore di database**, in **Modalità di autenticazione**, scegliere **Modalità mista (autenticazione di SQL Server e autenticazione di Windows)** e specificare una password.
	
	![Configurazione del motore di database](./media/app-service-api-hybrid-on-premises-sql-server/database-engine-configuration.png)
	
	In questa esercitazione l'utente userà l'autenticazione SQL Server. Prendere nota della password specificata, perché sarà necessaria in seguito.
	
8. Eseguire gli altri passaggi della procedura guidata, accettando le impostazioni predefinite, per completare l'installazione.

9. Chiudere la finestra di dialogo **Centro installazione SQL Server**.

### Abilitare TCP/IP
Per abilitare TCP/IP, si userà Gestione configurazione SQL Server, installato al momento dell'installazione di SQL Server Express. Prima di continuare, seguire la procedura riportata in [Abilitare un protocollo di rete TCP/IP per SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx).

<a name="CreateSQLDB"></a>
### Creare un database SQL Server locale

1. In **SQL Server Management Studio** connettersi all'istanza di SQL Server appena installata. In **Tipo server** scegliere **Motore di database**. In **Nome server** è possibile usare **localhost** o il nome del computer. Scegliere **Autenticazione di SQL Server**, quindi accedere con il nome utente e la password `sa` creati in precedenza.

	![Connetti al server](./media/app-service-api-hybrid-on-premises-sql-server/connect-to-server.png)
	
	Se la finestra di dialogo **Connetti al server** non viene visualizzata automaticamente, passare a **Esplora oggetti** nel riquadro a sinistra, fare clic su **Connetti** e quindi su **Motore di database**.
	
2. Per creare un nuovo database usando SQL Server Management Studio, fare clic con il pulsante destro del mouse su **Database** in Esplora oggetti, quindi fare clic su **Nuovo database**.
	
	![Creare un nuovo menu di database](./media/app-service-api-hybrid-on-premises-sql-server/new-database-menu.png)
	
3. Nella finestra di dialogo **Nuovo database** immettere `LocalDatabase` come nome del database, quindi fare clic su **OK**.
	
	![Creazione di un nuovo database](./media/app-service-api-hybrid-on-premises-sql-server/new-database.png)
	
### Creare e popolare una tabella di SQL Server

1. In **Esplora oggetti** di **SQL Server Management Studio** espandere la voce `LocalDatabase`.

	![Database espanso](./media/app-service-api-hybrid-on-premises-sql-server/local-database-expanded.png)

2. Fare clic con il pulsante destro del mouse su **Tabelle**, quindi scegliere l'opzione **Tabella** dal menu di scelta rapida.

	![Menu Nuova tabella](./media/app-service-api-hybrid-on-premises-sql-server/new-table-menu.png)

3. Quando viene visualizzata la griglia di Progettazione tabelle, immettere le informazioni sulle colonne, come illustrato nella figura seguente.

	![Nuove Colonne tabella](./media/app-service-api-hybrid-on-premises-sql-server/table-def.png)

4. Premere **&lt;CTRL>S** per salvare la nuova definizione di tabella. Sarà necessario immettere un nome di tabella. Immettere `Speakers` e premere **OK**.

	![Salvare la nuova tabella](./media/app-service-api-hybrid-on-premises-sql-server/save-new-table.png)

5. In **Esplora oggetti** fare clic con il pulsante destro del mouse sulla tabella `Speakers` appena creata, quindi selezionare **Modifica le prime 200 righe** dal menu di scelta rapida.

	![Modifica le prime 200 righe](./media/app-service-api-hybrid-on-premises-sql-server/edit-top-200-rows.png)

6. Quando viene visualizzata la griglia per l'immissione/modifica dei dati della tabella, immettere alcuni dati di prova, come illustrato nella figura seguente.

	![Dati di test](./media/app-service-api-hybrid-on-premises-sql-server/speakers-data.png)

## Creare e distribuire la demo di app per le API in Azure 

Questa sezione illustra in modo dettagliato la creazione della demo di app per le API.

1. Aprire Visual Studio 2013 e selezionare **File > Nuovo progetto**. 

2. Selezionare il modello **Visual C# > Web > Applicazione Web ASP.NET**, deselezionare l'opzione **Aggiungi Application Insights al progetto**, assegnare il nome *SpeakersList* al progetto, quindi fare clic su **OK**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project.png)

3. Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il modello di progetto **App per le API di Azure**, quindi fare clic su **OK**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project-api-app.png)

4. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Modelli**, quindi scegliere l'opzione **Aggiungi > Classe...** dal menu contestuale.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-menu.png)

5. Assegnare al nuovo file il nome *Speaker.cs* e quindi fare clic su **Aggiungi**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-class.png)

6. Sostituire l'intero contenuto del file `Speaker.cs` con il codice seguente.

		namespace SpeakersList.Models
		{
			public class Speaker
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

7. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Controller**, quindi scegliere l'opzione **Aggiungi > Controller...** dal menu contestuale.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-controller.png)

8. Nella finestra di dialogo **Aggiungi scaffolding** selezionare l'opzione **Controller API Web 2 - Vuoto** e fare clic su **Aggiungi**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-scaffold.png)

9. Assegnare al controller il nome **SpeakersController** e quindi fare clic su **Aggiungi**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-controller-name.png)

10. Sostituire il codice nel file `SpeakersController.cs` con il codice seguente. Assicurarsi di specificare valori personalizzati per i segnaposto &lt;serverName> e &lt;password> in `connectionString`. Il valore &lt;serverName> corrisponde al nome del computer in cui si trova SQL Server e il valore &lt;password> corrisponde al valore impostato durante l'installazione e la configurazione di SQL Server.

	> [AZURE.NOTE]Il frammento di codice seguente include le informazioni sulla password. Ciò permette di mantenere semplice la demo. In un ambiente di produzione reale, è consigliabile non archiviare le credenziali nel codice. Vedere invece le [Procedure consigliate per la distribuzione di password e altri dati sensibili in ASP.NET e Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		using SpeakersList.Models;
		using System.Data.Sql;
		using System.Data.SqlClient;
		
		namespace SpeakersList.Controllers
		{
		    public class SpeakersController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Speaker> Get()
		        {
					// Instantiate List object that will be populated and returned
					// from this method.
		            List<Speaker> speakers = new List<Speaker>();
	
					// Build database connection string.
		            string connectionString = "Server=<serverName>;" +
		                                      "Initial Catalog=LocalDatabase;" +
		                                      "User Id=sa;Password=<password>;" +
		                                      "Asynchronous Processing=true;";
		
					// Build query string to select all three columns from the Speakers table.
		            string queryString = "SELECT Id, EmailAddress, Name FROM dbo.Speakers;";

					// Instantiate the SqlConnection object using the connection string.
		            using (SqlConnection connection = new SqlConnection(connectionString))
		            {
						// Instantiate the SqlCommand object using the connection and query strings.
		                SqlCommand command = new SqlCommand(queryString, connection);

						// Open the connection to the database.
		                connection.Open();

						// Execute the command and return a SqlDataReader object
						// that can be iterated.
		                SqlDataReader reader = command.ExecuteReader();
		                try
		                {
							// Read a row from the SqlDataReader object
		                    while (reader.Read())
		                    {
								// For each row, use the returned data to instantiate
								// and add to the List a new Speaker object.
		                        speakers.Add(new Speaker 
		                            { 
		                                Id = Convert.ToInt32(reader[0]), 
		                                EmailAddress = reader[1].ToString(), 
		                                Name = reader[2].ToString() 
		                            }
		                        );
		                    }
		                }
		                finally
		                {
							// Close the SqlDataReader object.
		                    reader.Close();
		                }
		            }
					// Return the List of Speaker objects.
		            return speakers;
		        }
		    }
		}

### Abilitare l'interfaccia utente di Swagger

L'abilitazione dell'interfaccia utente di Swagger consentirà di testare con facilità l'app per le API, senza dovere scrivere codice client per chiamarla.

1. Aprire il file *App\_Start/SwaggerConfig.cs* e cercare **EnableSwaggerUI**:

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-disabled.png)

2. Rimuovere quindi i simboli di commento dalle righe di codice seguenti:

	        })
	    .EnableSwaggerUi(c =>
	        {

3. Al termine, verificare che il file abbia un aspetto analogo a quello della figura seguente.

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-enabled.png)

### Testare l'app per le API

1. Per visualizzare la pagina di test dell'API, eseguire l'applicazione localmente tramite **&lt;CTRL>F5**. L'errore dovrebbe essere simile alla figura seguente.

	![](./media/app-service-api-hybrid-on-premises-sql-server/error-forbidden.png)

2. Nella barra degli indirizzi del browser aggiungere `/swagger` alla fine dell'URL, quindi premere **&lt;INVIO>**. Verrà visualizzata l'interfaccia utente di Swagger abilitata nella sezione precedente.

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui.png)

3. Fare clic sulla sezione **Speakers** per espanderla.

4. Fare clic su **GET /api/speakers** per espandere la sezione.

5. Fare clic su **Try it out!** per visualizzare i dati immessi in precedenza nel database.

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out.png)

### Distribuzione dell'app per le API

Dopo il test locale dell'app, è possibile distribuire l'app in Azure.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto (non sulla soluzione), quindi scegliere **Pubblica**. 

	![Opzione di menu per la pubblicazione del progetto](./media/app-service-api-hybrid-on-premises-sql-server/publish-menu.png)

2. Scegliere la scheda **Profilo** e fare clic su **App per le API di Microsoft Azure (anteprima)**.

	![Opzione di menu per la pubblicazione del progetto](./media/app-service-api-hybrid-on-premises-sql-server/publish-web.png)

3. Fare clic su **Nuovo** per effettuare il provisioning di una nuova app per le API nella sottoscrizione di Azure.

	![Finestra di dialogo per la selezione di servizi API esistenti](./media/app-service-api-hybrid-on-premises-sql-server/publish-to-existing-api-app.png)

4. Nella finestra di dialogo **Crea un'app per le API** immettere quanto segue:

	- In **Nome app per le API**, immettere un nome per l'app. 
	- Se si dispone di più sottoscrizioni di Azure, selezionare quella da usare.
	- In **Piano di servizio app** selezionare uno dei piani di servizio app esistenti oppure selezionare **Crea nuovo piano di servizio app** e immettere il nome di un nuovo piano. 
	- In **Gruppo di risorse** selezionare i gruppi di risorse esistenti oppure selezionare **Crea nuovo gruppo di risorse** e immettere un nome. Il nome deve essere univoco; provare a usare il nome dell'app come un prefisso e ad aggiungere alcune informazioni personali, ad esempio l'ID Microsoft (senza il simbolo @).  
	- In **Livello di accesso** selezionare **Disponibile** per chiunque. Questa opzione renderà l'API pubblica completamente, il che è corretto per questa esercitazione. È possibile in un secondo momento limitare l'accesso tramite il [portale di anteprima di Azure](https://portal.azure.com).
	- Scegliere un'area,

	Fare clic su **OK** per creare l'app per le API nella sottoscrizione.

	![Finestra di dialogo per la configurazione di app Web di Microsoft Azure](./media/app-service-api-hybrid-on-premises-sql-server/publish-new-api-app.png)

5. Il processo può richiedere alcuni minuti per consentire a Visual Studio di mostrare una finestra di dialogo che segnala all'utente l'avvio del processo. Fare clic su **OK** nella finestra di conferma.

	![Messaggio di conferma dell'inizio della creazione del servizio API](./media/app-service-api-hybrid-on-premises-sql-server/create-api-app-confirmation.png)

6. Mentre il processo di provisioning crea il gruppo di risorse e l'app per le API nella sottoscrizione di Azure, Visual Studio mostra l'avanzamento nella finestra **Attività del servizio app di Azure**.

	![Notifica sullo stato tramite la finestra Attività del servizio app di Azure](./media/app-service-api-hybrid-on-premises-sql-server/app-service-activity.png)

7. Dopo avere effettuato il provisioning dell'app per le API, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** per aprire nuovamente la finestra di dialogo di pubblicazione. Il profilo di pubblicazione creato nel passaggio precedente deve essere già selezionato. Fare clic su **Pubblica** per avviare il processo di distribuzione.

	![Distribuzione dell'app per le API](./media/app-service-api-hybrid-on-premises-sql-server/publish2.png)

La finestra **Attività del servizio app di Azure** mostra l'avanzamento della distribuzione e segnalerà il completamento del processo di pubblicazione.

## Creare una connessione ibrida e un servizio BizTalk ##

1. Nel browser passare al [portale di anteprima di Azure](https://portal.azure.com). 

2. Fare clic sull'opzione **Esplora tutto** a sinistra.

3. Nel pannello **Sfoglia** selezionare **App per le API**.

4. Nel pannello **App per le API** individuare l'app per le API e farvi clic.

5. Nel pannello dell'app per le API fare clic sul valore disponibile in **Host app per le API**.
 
	![Pannello App per le API](./media/app-service-api-hybrid-on-premises-sql-server/api-app-blade-api-app-host.png)

6. Quando viene visualizzato il pannello **Host app per le API**, scorrere verso il basso fino alla sezione **Rete**, quindi fare clic su **Connessioni ibride**.
	
	![Connessioni ibride](./media/app-service-api-hybrid-on-premises-sql-server/api-app-host-blade-hybrid-connections.png)
	
7. Nel pannello **Connessioni ibride** fare clic su **Aggiungi** > **Nuova connessione ibrida**.
	
8. Nel **pannello Crea connessione ibrida**:
	- In **Nome** specificare un nome per la connessione.
	- In **Nome host** immettere il nome computer del computer host SQL Server.
	- In **Porta** immettere `1433` (la porta predefinita per SQL Server).
	- Fare clic su **servizio parlare Biz** e immettere un nome per il servizio BizTalk.
	
	![Creare una connessione ibrida](./media/app-service-api-hybrid-on-premises-sql-server/create-biztalk-service.png)
		
9. Fare clic su **OK** due volte.

	Al termine del processo, nell'area **Notifiche** verrà visualizzata una luce di colore verde per **OPERAZIONE RIUSCITA** e il pannello **Connessioni ibride** mostrerà la nuova connessione ibrida con stato **Non connesso**.
	
	![Connessione ibrida creata](./media/app-service-api-hybrid-on-premises-sql-server/hybrid-not-connected-yet.png)
	
A questo punto è stata completata una parte importante dell'infrastruttura della connessione ibrida cloud. Nel passaggio successivo verrà creato un elemento locale corrispondente.

<a name="InstallHCM"></a>
## Installare l'istanza locale di Hybrid Connection Manager per completare la connessione

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

Dopo avere completato l'infrastruttura della connessione ibrida, sarà possibile testare l'applicazione.

<a name="CreateASPNET"></a>
## Testare l'app per le API completata in Azure

1. Nel portale di anteprima di Azure tornare al pannello Host app per le API, quindi fare clic sul valore disponibile in **URL**.
	
2. Quando viene visualizzata la pagina dell'host dell'app per le API nel browser, aggiungere `/swagger` alla fine dell'URL nella barra degli indirizzi del browser, quindi premere **&lt;INVIO>**.
	
3. Fare clic sulla sezione **Speakers** per espanderla.

4. Fare clic su **GET /api/speakers** per espandere la sezione.

5. Fare clic su **Try it out!** per visualizzare i dati immessi in precedenza nel database.

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out-azure.png)
	
**Congratulazioni** È stata creata un'app per le API che viene eseguita su Azure e usa una connessione ibrida per connettersi a un database SQL Server locale.

## Vedere anche
[Panoramica delle connessioni ibride](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist presenta le connessioni ibride (video Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Panoramica delle connessioni ibride](/services/biztalk-services/)

[Servizi BizTalk: schede Dashboard, Monitor, Scala, Configura e Connessione ibrida](../biztalk-dashboard-monitor-scale-tabs/)

[Creazione di un cloud ibrido reale con portabilità continua delle applicazioni (video Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Connettersi a un'istanza di SQL Server locale da un servizio mobile di Azure mediante Connessioni ibride](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)

[Connettersi a un'istanza di SQL Server locale da Servizi mobili di Azure mediante Connessioni ibride (video Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[Panoramica dell'identità ASP.NET](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

<!---HONumber=Nov15_HO2-->