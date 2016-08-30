<properties
	pageTitle="Applicazione .NET multilivello | Microsoft Azure"
	description="Un'esercitazione .NET che consente di sviluppare un'applicazione multilivello in Azure che usa le code di Bus di servizio per la comunicazione tra livelli."
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="05/27/2016"
	ms.author="sethm"/>

# Applicazione .NET multilivello che usa code del bus di servizio

## Introduzione

Microsoft Azure consente di sviluppare applicazioni con la stessa semplicità offerta da Visual Studio e Azure SDK gratuito per .NET. Questa esercitazione illustra i passaggi per creare un'applicazione che usa più risorse di Azure in esecuzione nell'ambiente locale. In questa procedura si presuppone che l'utente non abbia mai usato Azure.

Verranno illustrate le operazioni seguenti:

-   Abilitare il computer in uso per lo sviluppo per Azure tramite un singolo download e una singola installazione.
-   Utilizzare Visual Studio per lo sviluppo per Azure.
-   Creare un'applicazione multilivello in Azure utilizzando ruoli Web e ruoli di lavoro.
-   Consentire le comunicazioni tra i livelli usando le code del bus di servizio.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

In questa esercitazione verrà creata ed eseguita un'applicazione multilivello in un servizio cloud di Azure. Il front-end sarà un ruolo Web ASP.NET MVC e il back-end sarà un ruolo di lavoro che usa una coda del bus di servizio. È possibile creare la stessa applicazione multilivello con il front-end come progetto Web distribuito in un sito Web di Azure anziché in un servizio cloud. Per le istruzioni sulle procedure diverse per un front-end di sito Web di Azure, vedere la sezione [Passaggi successivi](#nextsteps). È anche possibile vedere l'esercitazione che descrive come compilare [applicazioni ibride cloud/locali .NET](service-bus-dotnet-hybrid-app-using-service-bus-relay.md).

Nella schermata seguente è illustrata l'applicazione completata.

![][0]

## Informazioni generali sullo scenario: comunicazione tra ruoli

Per inviare un ordine per l'elaborazione, è necessario che il componente dell'interfaccia utente front-end, in esecuzione nel ruolo Web, interagisca con la logica di livello intermedio in esecuzione nel ruolo di lavoro. Per consentire le comunicazioni tra i livelli, in questo esempio viene usata la messaggistica negoziata del bus di servizio.

L'uso della messaggistica negoziata tra il livello Web e il livello intermedio consente di disaccoppiare i due componenti. Invece di usare la messaggistica diretta, ovvero TCP o HTTP, il livello Web non si connette direttamente al livello intermedio, ma inserisce unità di lavoro, ad esempio messaggi, nel bus di servizio, che li conserva in modo affidabile fino a quando il livello intermedio non sarà pronto per usarli ed elaborarli.

Il bus di servizio offre due entità per il supporto della messaggistica negoziata, ovvero le code e gli argomenti. Se si usano le code, ogni messaggio inviato alla coda viene usato da un ricevitore singolo. Gli argomenti supportano il modello pubblicazione/sottoscrizione, in cui ogni messaggio pubblicato viene reso disponibile a una sottoscrizione registrata per l'argomento. Ogni sottoscrizione mantiene in modo logico la propria coda di messaggi. È anche possibile configurare le sottoscrizioni specificando regole di filtro per limitare i set di messaggi passati alla coda della sottoscrizione ai soli messaggi corrispondenti al filtro. Nel seguente esempio vengono usate le code del bus di servizio.

![][1]

Questo meccanismo di comunicazione presenta alcuni vantaggi rispetto alla messaggistica diretta:

-   **Disaccoppiamento temporaneo.** Grazie al modello di messaggistica asincrono, non è necessario che produttori e consumer siano online nello stesso momento. I messaggi vengono archiviati in modo affidabile nel bus di servizio fino a quando il consumer non sarà pronto per riceverli. Questo consente la disconnessione volontaria (ad esempio, per attività di manutenzione) o involontaria (a seguito di un guasto) dei componenti dell'applicazione distribuita senza ripercussioni sull'intero sistema. È inoltre possibile che l'applicazione consumer debba essere online solo in determinati orari.

-   **Livellamento del carico.** In molte applicazioni il carico del sistema varia in base al momento, mentre il tempo di elaborazione richiesto per ogni unità di lavoro è in genere costante. L'interposizione di una coda tra producer e consumer di messaggi implica che è necessario solo eseguire il provisioning dell'applicazione consumer (il ruolo di lavoro) per consentire a quest'ultima di gestire un carico medio anziché il carico massimo. In base alla variazione del carico in ingresso, si verificherà un incremento o una riduzione della profondità della coda, con un risparmio diretto in termini economici rispetto alle risorse infrastrutturali richieste per gestire il carico dell'applicazione.

-   **Bilanciamento del carico.** Con l'aumento del carico, è possibile aggiungere altri processi di lavoro per la lettura della coda. Ciascun messaggio viene elaborato da un solo processo di lavoro. Inoltre, il bilanciamento del carico di tipo pull consente un uso ottimale delle macchine di lavoro anche quando queste presentano una potenza di elaborazione diversa. Ogni macchina effettuerà infatti il pull dei messaggi alla propria velocità massima. Questo modello viene spesso definito modello del *consumer concorrente*.

    ![][2]

Il codice che consente di implementare questa architettura viene illustrato nelle sezioni seguenti.

## Configurare l'ambiente di sviluppo

Prima di iniziare a sviluppare applicazioni Azure, è necessario ottenere gli strumenti e configurare l'ambiente di sviluppo.

1.  Installare Azure SDK per .NET in [Ottieni strumenti ed SDK][].

2. 	Fare clic su **Installa l’SDK** per la versione di Visual Studio in uso. Nelle procedure di questa esercitazione viene usato Visual Studio 2015.

4.  Quando viene richiesto se eseguire o salvare il file di installazione, fare clic su **Esegui**.

5.  Nell'**Installazione guidata piattaforma Web** fare clic su **Installa** e procedere con l'installazione.

6.  Al termine dell'installazione, saranno disponibili tutti gli strumenti necessari per avviare lo sviluppo dell’app. Nell'SDK sono disponibili gli strumenti che consentono di sviluppare con facilità applicazioni per Azure in Visual Studio. Se Visual Studio non è ancora installato, l'SDK installerà anche la versione gratuita di Visual Studio Express.

## Creare uno spazio dei nomi

Il passaggio successivo consiste nel creare uno spazio dei nomi del servizio e nell'ottenere una chiave di firma di accesso condiviso. Uno spazio dei nomi fornisce un limite per ogni applicazione esposta tramite il bus di servizio. Una chiave di firma di accesso condiviso viene generata dal sistema quando viene creato uno spazio dei nomi. La combinazione di spazio dei nomi e chiave di firma di accesso condiviso fornisce le credenziali che consentono al bus di servizio di autenticare l'accesso a un'applicazione.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## Creare un ruolo web

Creare in questa sezione il front-end dell'applicazione. Creare prima di tutto le pagine visualizzate dall'applicazione. Aggiungere quindi il codice per inviare elementi a una coda del bus di servizio e visualizzare informazioni relative allo stato della coda.

### Creare il progetto

1.  Usando privilegi di amministratore, avviare Microsoft Visual. Per avviare Visual Studio con privilegi di amministratore, fare clic con il pulsante destro del mouse sull'icona del programma **Visual Studio** e quindi scegliere **Esegui come amministratore**. Per l'emulatore di calcolo di Azure, illustrato più avanti in questo articolo, è necessario che Visual Studio sia avviato con privilegi di amministratore.

    In Visual Studio, nel menu **File** scegliere **Nuovo**, quindi fare clic su **Progetto**.

2.  Da **Modelli installati**, in **Visual C#**, fare clic su **Cloud** e quindi su **Servizio cloud Azure**. Assegnare al progetto il nome **MultiTierApp**. Fare quindi clic su **OK**.

    ![][9]  

3.  Dai ruoli **.NET Framework 4.5** fare doppio clic su **Ruolo Web ASP.NET**.

    ![][10]  

4.  Passare il puntatore su **RuoloWeb1** in **Soluzione Servizio cloud Azure**, quindi fare clic sull'icona a forma di matita e rinominare il ruolo Web in **FrontendWebRole**. Fare quindi clic su **OK**. Assicurarsi di immettere "Frontend" con una 'e' minuscola, non "FrontEnd".

    ![][11]  

5.  Nella finestra di dialogo **Nuovo progetto ASP.NET** fare clic su **MVC** nell'elenco **Selezionare un modello**.

    ![][12]  

6. Sempre nella finestra di dialogo **Nuovo progetto ASP.NET** fare clic sul pulsante **Modifica autenticazione**. Nella finestra di dialogo **Modifica autenticazione** fare clic su **Nessuna autenticazione** e quindi fare clic su **OK**. Per questa esercitazione si distribuisce un'applicazione che non richiede l'accesso utente.

	![][16]  

7. Nella finestra di dialogo **Nuovo progetto ASP.NET** fare clic su **OK** per creare il progetto.

6.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Riferimenti** nel progetto **FrontendWebRole** e quindi scegliere **Gestisci pacchetti NuGet**.

7.  Fare clic sulla scheda **Sfoglia** e quindi cercare `Microsoft Azure Service Bus`. Fare clic su **Installa** e accettare le condizioni per l'utilizzo.

    ![][13]

	Sono ora disponibili riferimenti agli assembly client necessari e sono stati aggiunti nuovi file di codice.

9.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Modelli**, quindi scegliere **Aggiungi** e infine **Classe**. Nella casella **Nome** digitare il nome **OnlineOrder.cs**. Fare quindi clic su **Aggiungi**.

### Scrivere il codice per il ruolo Web

Creare prima di tutto in questa sezione le diverse pagine visualizzate dall'applicazione.

1.  In Visual Studio, nel file OnlineOrder.cs sostituire la definizione dello spazio dei nomi esistente con il codice seguente:

	```
	namespace FrontendWebRole.Models
	{
	    public class OnlineOrder
	    {
	        public string Customer { get; set; }
	        public string Product { get; set; }
	    }
	}
	```

2.  In **Esplora soluzioni** fare doppio clic su **Controllers\\HomeController.cs**. Aggiungere le istruzioni **using** seguenti nella parte iniziale del file per includere gli spazi dei nomi per il modello appena creato, oltre al bus di servizio.

	```
	using FrontendWebRole.Models;
	using Microsoft.ServiceBus.Messaging;
	using Microsoft.ServiceBus;
	```

3.  Anche nel file HomeController.cs sostituire la definizione dello spazio dei nomi esistente con il seguente codice. Tale codice include metodi per la gestione dell'invio di elementi alla coda.

	```
	namespace FrontendWebRole.Controllers
	{
	    public class HomeController : Controller
	    {
	        public ActionResult Index()
	        {
	            // Simply redirect to Submit, since Submit will serve as the
	            // front page of this application.
	            return RedirectToAction("Submit");
	        }
	
	        public ActionResult About()
	        {
	            return View();
	        }
	
	        // GET: /Home/Submit.
	        // Controller method for a view you will create for the submission
	        // form.
	        public ActionResult Submit()
	        {
	            // Will put code for displaying queue message count here.
	
	            return View();
	        }
	
	        // POST: /Home/Submit.
	        // Controller method for handling submissions from the submission
	        // form.
	        [HttpPost]
			// Attribute to help prevent cross-site scripting attacks and
			// cross-site request forgery.  
			[ValidateAntiForgeryToken]
	        public ActionResult Submit(OnlineOrder order)
	        {
	            if (ModelState.IsValid)
	            {
	                // Will put code for submitting to queue here.
	
	                return RedirectToAction("Submit");
	            }
	            else
	            {
	                return View(order);
	            }
	        }
	    }
	}
	```

4.  Dal menu **Compila** fare clic su **Compila soluzione** per verificare la correttezza del lavoro svolto finora.

5.  Creare quindi la visualizzazione per il metodo `Submit()` creato in precedenza. Fare clic con il pulsante destro del mouse all'interno del metodo `Submit()`, ovvero l'overload del metodo `Submit()` che non accetta parametri, e quindi scegliere **Aggiungi visualizzazione**.

    ![][14]  

6.  Viene visualizzata una finestra di dialogo per la creazione della visualizzazione. Nell'elenco **Modello** scegliere **Crea**. Nell'elenco **Classe modello** scegliere la classe **OnlineOrder**.

    ![][15]  

7.  Fare clic su **Aggiungi**.

8.  Modificare ora il nome visualizzato dell'applicazione. In **Esplora soluzioni** fare doppio clic sul file **Views\\Shared\\\\_Layout.cshtml** per aprirlo nell'editor di Visual Studio.

9.  Sostituire tutte le occorrenze di **My ASP.NET Application** con **LITWARE'S Products**.

10. Rimuovere i collegamenti **Home**, **About** e **Contact**. Eliminare il codice evidenziato:

	![][28]  

11. Modificare infine la pagina di invio in modo da includere informazioni sulla coda. In **Esplora soluzioni** fare doppio clic sul file **Views\\Home\\Submit.cshtml** per aprirlo nell'editor di Visual Studio. Aggiungere la riga seguente dopo `<h2>Submit</h2>`. Per ora `ViewBag.MessageCount` non contiene valori. Il valore verrà inserito successivamente.

	```
	<p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
	```

12. L'interfaccia utente è stata implementata. È possibile premere **F5** per eseguire l'applicazione e confermare che abbia l'aspetto previsto.

    ![][17]  

### Scrivere codice per l'invio di elementi a una coda del bus di servizio

Aggiungere quindi il codice per l'invio di elementi a una coda. Creare prima di tutto una classe contenente le informazioni di connessione della coda del bus di servizio. Inizializzare quindi la connessione da Global.aspx.cs. Aggiornare infine il codice di invio creato in precedenza in HomeController.cs in modo da inviare effettivamente elementi alla coda del bus di servizio.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **FrontendWebRole**. È necessario fare clic con il pulsante destro del mouse sul progetto, non sul ruolo. Fare clic su **Aggiungi**, quindi su **Classe**.

2.  Assegnare alla classe il nome **QueueConnector.cs**. Fare clic su **Aggiungi** per creare la classe.

3.  Aggiungere ora codice che incapsula le informazioni di connessione e inizializza la connessione a una coda del bus di servizio. Sostituire l'intero contenuto di QueueConnector.cs con il codice seguente e immettere i valori per `your Service Bus namespace`, ovvero il nome dello spazio dei nomi, e `yourKey`, ovvero la **chiave primaria** ottenuta in precedenza dal portale di Azure.

	```
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using Microsoft.ServiceBus.Messaging;
	using Microsoft.ServiceBus;
	
	namespace FrontendWebRole
	{
	    public static class QueueConnector
	    {
	        // Thread-safe. Recommended that you cache rather than recreating it
	        // on every request.
	        public static QueueClient OrdersQueueClient;
	
	        // Obtain these values from the portal.
	        public const string Namespace = "your Service Bus namespace";
	
	        // The name of your queue.
	        public const string QueueName = "OrdersQueue";
	
	        public static NamespaceManager CreateNamespaceManager()
	        {
	            // Create the namespace manager which gives you access to
	            // management operations.
	            var uri = ServiceBusEnvironment.CreateServiceUri(
	                "sb", Namespace, String.Empty);
	            var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
	                "RootManageSharedAccessKey", "yourKey");
	            return new NamespaceManager(uri, tP);
	        }
	
	        public static void Initialize()
	        {
	            // Using Http to be friendly with outbound firewalls.
	            ServiceBusEnvironment.SystemConnectivity.Mode =
	                ConnectivityMode.Http;
	
	            // Create the namespace manager which gives you access to
	            // management operations.
	            var namespaceManager = CreateNamespaceManager();
	
	            // Create the queue if it does not exist already.
	            if (!namespaceManager.QueueExists(QueueName))
	            {
	                namespaceManager.CreateQueue(QueueName);
	            }
	
	            // Get a client to the queue.
	            var messagingFactory = MessagingFactory.Create(
	                namespaceManager.Address,
	                namespaceManager.Settings.TokenProvider);
	            OrdersQueueClient = messagingFactory.CreateQueueClient(
	                "OrdersQueue");
	        }
	    }
	}
	```

4.  Accertarsi che venga chiamato il metodo **Initialize**. In **Esplora soluzioni** fare doppio clic su **Global.asax\\Global.asax.cs**.

5.  Aggiungere la riga di codice seguente alla fine del metodo **Application\_Start**.

	```
	FrontendWebRole.QueueConnector.Initialize();
	```

6.  Verrà infine aggiornato il codice Web creato in precedenza, in modo da inviare elementi alla coda. In **Esplora soluzioni** fare doppio clic su **Controllers\\HomeController.cs**.

7.  Aggiornare il metodo `Submit()`, ovvero l'overload che non accetta parametri, come indicato di seguito per ottenere il numero di messaggi per la coda.

	```
	public ActionResult Submit()
	{
	    // Get a NamespaceManager which allows you to perform management and
	    // diagnostic operations on your Service Bus queues.
	    var namespaceManager = QueueConnector.CreateNamespaceManager();
	
	    // Get the queue, and obtain the message count.
	    var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
	    ViewBag.MessageCount = queue.MessageCount;
	
	    return View();
	}
	```

8.  Aggiornare il metodo `Submit(OnlineOrder order)`, ovvero l'overload che accetta un parametro, come indicato di seguito per inviare alla coda le informazioni relative all'ordine.

	```
	public ActionResult Submit(OnlineOrder order)
	{
	    if (ModelState.IsValid)
	    {
	        // Create a message from the order.
	        var message = new BrokeredMessage(order);
	
	        // Submit the order.
	        QueueConnector.OrdersQueueClient.Send(message);
	        return RedirectToAction("Submit");
	    }
	    else
	    {
	        return View(order);
	    }
	}
	```

9.  È ora possibile eseguire di nuovo l'applicazione. Ogni volta che si invia un ordine, il conteggio dei messaggi aumenta.

    ![][18]  

## Creare il ruolo di lavoro

Verrà ora creato il ruolo di lavoro che elabora l'invio dell'ordine. In questo esempio viene usato il modello di progetto **Worker Role with Service Bus Queue** di Visual Studio. Le credenziali necessarie sono già state ottenute dal portale.

1. Assicurarsi che Visual Studio sia connesso all'account Azure.

2.  In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sulla cartella **Roles** nel progetto **MultiTierApp**.

3.  Fare clic su **Aggiungi**, quindi su **Nuovo progetto di ruolo di lavoro**. Viene visualizzata la finestra di dialogo **Aggiungi nuovo progetto di ruolo**.

	![][26]  

4.  Nella finestra di dialogo **Aggiungi nuovo progetto di ruolo** fare clic su **Ruolo di lavoro con coda del bus di servizio**.

	![][23]  

5.  Nella casella **Name** assegnare il nome **OrderProcessingRole** al progetto. Fare quindi clic su **Aggiungi**.

6.  Copiare negli Appunti la stringa di connessione ottenuta nel passaggio 9 della sezione "Creare uno spazio dei nomi del bus di servizio".

7.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul ruolo **OrderProcessingRole** creato nel passaggio 5. Assicurarsi di fare clic con il pulsante destro del mouse su **OrderProcessingRole** in **Ruoli** e non sulla classe. Fare quindi clic su **Proprietà**.

8.  Nella scheda **Impostazioni** della finestra di dialogo **Proprietà** posizionare il cursore all'interno della casella **Valore** per **Microsoft.ServiceBus.ConnectionString**, quindi incollare il valore dell'endpoint copiato al passaggio 6.

	![][25]  

9.  Creare una classe **OnlineOrder** che rappresenti gli ordini elaborati dalla coda. È possibile riutilizzare una classe creata in precedenza. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla classe **OrderProcessingRole**. È necessario fare clic con il pulsante destro del mouse sull'icona della classe, non sul ruolo. Fare clic su **Aggiungi**, quindi su **Elemento esistente**.

10. Selezionare la sottocartella per **FrontendWebRole\\Models** e fare doppio clic su **OnlineOrder.cs** per aggiungerlo al progetto corrente.

11. In **WorkerRole.cs** modificare il valore della variabile **QueueName** da `"ProcessingQueue"` in `"OrdersQueue"`, come illustrato nel codice seguente.

	```
	// The name of your queue.
	const string QueueName = "OrdersQueue";
	```

12. Aggiungere l'istruzione using seguente all'inizio del file WorkerRole.cs.

	```
	using FrontendWebRole.Models;
	```

13. Nella funzione `Run()`, all'interno della chiamata `OnMessage()`, sostituire il contenuto della clausola `try` con il codice seguente.

	```
	Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
	// View the message as an OnlineOrder.
	OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
	Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
	receivedMessage.Complete();
	```

14. L'applicazione è stata completata. È possibile testare l'applicazione completa facendo clic con il pulsante destro del mouse sul progetto MultiTierApp in Esplora soluzioni. Selezionare quindi **Imposta come progetto di avvio** e premere F5. Il numero totale dei messaggi non aumenta perché il ruolo di lavoro elabora gli elementi dalla coda e li contrassegna come completati. È possibile verificare l'output di traccia del ruolo di lavoro visualizzando l'interfaccia utente dell'emulatore di calcolo di Azure. Per eseguire questa operazione, fare clic con il pulsante destro del mouse sull'icona dell'emulatore nell'area di notifica della barra delle applicazioni, quindi scegliere **Show Compute Emulator UI**.

    ![][19]  

    ![][20]  

## Passaggi successivi  

Per ulteriori informazioni sul bus di servizio, vedere le risorse seguenti:

* [Bus di servizio di Azure][sbmsdn]
* [Pagina relativa al bus di servizio][sbwacom]
* [Come usare le code del bus di servizio][sbwacomqhowto]

Per altre informazioni sugli scenari multilivello, vedere:

* [Applicazione .NET multilivello con tabelle, code e BLOB di archiviazione di Azure][mutitierstorage]

  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Ottieni strumenti ed SDK]: http://go.microsoft.com/fwlink/?LinkId=271920


  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx
  [NamespaceMananger]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx

  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx

  [EventHubClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx

  [9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
  [17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx
  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: service-bus-dotnet-get-started-with-queues.md
  [mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  

<!---HONumber=AcomDC_0824_2016-->