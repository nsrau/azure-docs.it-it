<properties urlDisplayName="Multi-Tier Application" pageTitle="Applicazione .NET multilivello - Esercitazione di Azure" metaKeywords="Azure Service Bus queue tutorial, Azure queue tutorial, Azure worker role tutorial, Azure .NET queue tutorial, Azure C# queue tutorial, Azure C# worker role tutorial" description="A tutorial that helps you develop a multi-tier app in Azure that uses Service Bus queues to communicate between tiers. Samples in .NET." metaCanonical="" services="cloud-services,service-bus" documentationCenter=".NET" title=".NET Multi-Tier Application Using Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/15/2014" ms.author="sethm" />





# Applicazione .NET multilivello che usa le code del bus di servizio

Azure consente di sviluppare applicazioni con la stessa semplicità offerta da Visual Studio 2013 e
Azure SDK gratuito per .NET. Se non si dispone già di Visual Studio 2013, l'SDK installerà
automaticamente Visual Studio Express 2013, per consentire di iniziare a sviluppare applicazioni
per Azure gratuitamente. In questa guida si presuppone che l'utente non abbia mai usato
Windows Azure. Dopo aver completato questa guida, si avrà a disposizione un'applicazione che usa
più risorse di Azure in esecuzione nell'ambiente locale e
che illustra il funzionamento di un'applicazione multilivello.

Si apprenderà come:

-   Abilitare il computer in uso per lo sviluppo per Azure tramite un singolo download e una
    singola installazione.
-   Usare Visual Studio per lo sviluppo per Azure.
-   Creare un'applicazione multilivello in Azure usando ruoli Web
    e ruoli di lavoro.
-   Consentire le comunicazioni tra i livelli usando le code del bus di servizio.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

In questa esercitazione verrà creata ed eseguita un'applicazione multilivello in un servizio cloud di Azure. Il front-end sarà un ruolo Web ASP.NET MVC e il back-end sarò un ruolo di lavoro. È possibile creare la stessa applicazione multilivello con il front-end come progetto Web da distribuire in un sito Web di Azure anziché in un servizio cloud. Per istruzioni sulle procedure diverse per creare un front-end per un sito Web di Azure, vedere la sezione [Passaggi successivi](#nextsteps).

Di seguito è riportata una schermata dell'applicazione completata:

![][0]

**Nota** In Azure è inoltre disponibile la funzionalità della coda di archiviazione. Per altre informazioni sulle code di archiviazione di Azure e sulle code del bus di servizio, vedere [Code di Azure e bus di servizio di Azure: confronto e contrapposizioni][sbqueuecomparison].  

<h2>Panoramica dello scenario: comunicazioni tra ruoli</h2>

Per inviare un ordine per l'elaborazione, è necessario che il componente dell'interfaccia utente front-end, in esecuzione
nel ruolo Web, interagisca con la logica di livello intermedio in esecuzione nel
ruolo di lavoro. Per consentire le
comunicazioni tra i livelli, in questo esempio viene usata la messaggistica negoziata del bus di servizio.

L'uso della messaggistica negoziata tra il livello Web e il livello intermedio consente di disaccoppiare i
due componenti. Anziché usare la messaggistica diretta, ovvero TCP o HTTP,
il livello Web non si connette direttamente al livello intermedio, ma
inserisce unità di lavoro, ad esempio messaggi, nel bus di servizio, che li conserva in modo affidabile
finché il livello intermedio non sarà pronto per usarli ed elaborarli.

Il bus di servizio offre due entità per il supporto della messaggistica negoziata,
ovvero le code e gli argomenti. Se si usano le code, ogni messaggio inviato alla coda
viene usato da un ricevitore singolo. Gli argomenti supportano il modello di
pubblicazione/sottoscrizione, in cui ogni messaggio pubblicato viene reso
disponibile a ogni sottoscrizione registrata per l'argomento. Ogni sottoscrizione gestisce
in modo logico la propria coda di messaggi. È inoltre possibile configurare
le sottoscrizioni specificando regole per i filtri che consentono di limitare i set di
messaggi passati alla coda della sottoscrizione ai soli messaggi corrispondenti al filtro. In questo esempio vengono usate le
code del bus di servizio.

![][1]

Questo meccanismo di comunicazione presenta alcuni vantaggi rispetto alla messaggistica
diretta, inclusi i seguenti:

-   **Disaccoppiamento temporaneo.** Grazie al modello di messaggistica asincrono,
    non è necessario che produttori e consumer siano online nello stesso momento. I messaggi
    vengono archiviati in modo affidabile nel bus di servizio finché il consumer
    non sarà pronto per riceverli. Questo consente la disconnessione volontaria
    (ad esempio, per attività di manutenzione) o involontaria (a seguito di un guasto)
    dei componenti dell'applicazione distribuita senza ripercussioni
    sull'intero sistema. È inoltre possibile che l'applicazione consumer
    debba essere online solo in determinati orari.

-   **Livellamento del carico**. In molte applicazioni il carico del sistema varia
    in base al momento, mentre il tempo di elaborazione richiesto per ogni
    unità di lavoro è in genere costante. L'interposizione di una coda tra producer e consumer
    di messaggi implica che è sufficiente eseguire il provisioning dell'applicazione consumer
    (il ruolo di lavoro) per consentire a quest'ultima di gestire un carico medio anziché il
    carico massimo. In base alla variazione del carico in ingresso, si verificherà un incremento o
    una riduzione della profondità della coda, con un risparmio diretto in termini economici rispetto
    alle risorse infrastrutturali richieste per gestire il carico dell'applicazione.

-   **Bilanciamento del carico.** Con l'aumento del carico, è possibile aggiungere altri
    processi di lavoro per la lettura della coda. Ciascun messaggio viene elaborato da un
    solo processo di lavoro. Inoltre, il bilanciamento del carico di tipo pull
    consente un utilizzo ottimale delle macchine di lavoro anche quando
    queste presentano una potenza di elaborazione diversa. Ogni macchina
    effettuerà infatti il pull dei messaggi alla propria velocità massima. Questo modello viene spesso
    definito modello del consumer concorrente.

    ![][2]

Il codice che consente di implementare questa
architettura viene illustrato nelle sezioni seguenti.

<h2>Configurare l'ambiente di sviluppo</h2>

Prima di iniziare a usare l'applicazione Azure, è necessario
ottenere gli strumenti idonei e configurare l'ambiente di sviluppo.

1.  Per installare Azure SDK per .NET, fare clic sul pulsante seguente:

    [Get Tools and SDK][]

2. 	Fare clic su **Installare l'SDK**.

3. 	Scegliere il collegamento relativo alla versione di Visual Studio in uso. Nelle procedure incluse in questa esercitazione viene usato Visual Studio 2013.

	![][32]

4. 	Quando viene richiesto se eseguire o salvare il file di installazione, fare clic su
    **Esegui**:

    ![][3]

5.  Nell'Installazione guidata piattaforma Web fare clic su **Installa** e procedere con l'installazione.

    ![][33]

6.  Al termine dell'installazione, saranno disponibili tutti gli strumenti
    necessari per avviare lo sviluppo. Nell'SDK sono disponibili gli strumenti che consentono di
    sviluppare con facilità applicazioni per Azure in Visual Studio. Se
    Visual Studio non è ancora installato, verrà installata anche la versione gratuita di
    Visual Studio Express per il Web.

<h2>Configurare lo spazio dei nomi del bus di servizio</h2>

Il passaggio successivo consiste nel creare uno spazio dei nomi servizio e nell'ottenere una
chiave privata condivisa. Uno spazio dei nomi servizio fornisce un limite per
ogni applicazione esposta tramite il bus di servizio. Alla creazione di uno spazio
dei nomi del servizio, viene generata automaticamente una
chiave privata condivisa. La combinazione di spazio dei nomi servizio e chiave privata condivisa
fornisce le credenziali che consentono al bus di servizio di autenticare l'accesso a un'
applicazione.

È inoltre possibile gestire gli spazi dei nomi e le entità di messaggistica del bus di servizio usando Esplora server di Visual Studio, ma è possibile creare spazi dei nomi nuovi solo all'interno del portale. 

###Configurare lo spazio dei nomi tramite il portale di gestione

1.  Accedere al [portale di gestione di Azure][].

2.  Nel pannello di navigazione sinistro del portale di gestione fare clic su 
    **Bus di servizio**.

3.  Nel riquadro inferiore del portale di gestione fare clic su **Crea**.

    ![][6]

4.  Nella finestra di dialogo **Aggiungi un nuovo spazio dei nomi** immettere un nome per lo spazio dei nomi.
    Verrà effettuato immediatamente un controllo sulla disponibilità del nome.   
    ![][7]

5.  Dopo avere verificato che lo spazio dei nomi sia disponibile, scegliere il
    paese o l'area in cui dovrà essere ospitato. Assicurarsi
    di usare lo stesso paese/area in cui verranno distribuite le
    risorse di calcolo.

    IMPORTANTE: selezionare la **stessa area** che si intende scegliere per la
    distribuzione dell'applicazione. In questo modo sarà possibile ottenere prestazioni ottimali.

6.  Fare clic sul segno di spunta. A questo punto, lo spazio dei nomi servizio
    verrà creato e abilitato nel sistema. Potrebbero essere necessari alcuni minuti per consentire al sistema di
    effettuare il provisioning delle risorse per lo spazio dei nomi creato.

	![][27]

7.  Fare clic sullo spazio dei nomi servizio nella finestra principale.

	![][30]

8. Fare clic su **Informazioni di connessione**.

	![][31]

9.  Nel riquadro **Accesso alle informazioni di connessione** individuare i valori **Autorità di certificazione predefinita** e **Chiave predefinita**.

10.  Prendere nota del valore della chiave oppure copiarlo negli Appunti.

###Gestire gli spazi dei nomi e le entità di messaggistica tramite Esplora server di Visual Studio

Per gestire uno spazio dei nomi e ottenere le informazioni di connessione tramite Visual Studio anziché dal portale di gestione, attenersi alla procedura descritta [qui](http://http://msdn.microsoft.com/it-it/library/windowsazure/ff687127.aspx), nella sezione **Per connettersi ad Azure da Visual Studio**. Quando si accede ad Azure, il nodo **Service Bus** nell'albero **Microsoft Azure** in Esplora server viene automaticamente popolato con gli eventuali spazi dei nomi creati. Fare clic con il pulsante destro del mouse su uno spazio dei nomi, quindi scegliere **Proprietà** per visualizzare la stringa di connessione e altri metadati associati allo spazio dei nomi nel riquadro **Proprietà** di Visual Studio. 

Prendere nota del valore **SharedAccessKey** oppure copiarlo negli Appunti:

![][34]

<h2>Creare un ruolo web</h2>

In questa sezione verrà creato il front-end dell'applicazione. Verranno
innanzitutto create le diverse pagine visualizzate dall'applicazione.
Verrà quindi aggiunto il codice per l'invio di elementi a una coda del bus di
servizio e la visualizzazione di informazioni relative allo stato della coda.

### Creare il progetto

1.  Usando i privilegi di amministratore, avviare Microsoft Visual Studio
    2013 o Microsoft Visual Studio Express. Per avviare Visual Studio
    con privilegi di amministratore, fare clic con il pulsante destro del mouse su **Microsoft Visual
    Studio 2013 (o Microsoft Visual Studio Express)**,
    quindi scegliere **Esegui come amministratore**. Per l'emulatore di calcolo di Azure,
    illustrato più avanti in questa guida, è necessario che Visual Studio
    sia avviato con privilegi di amministratore.

    In Visual Studio, nel menu **File**, scegliere **Nuovo**, quindi
    fare clic su **Progetto**.

    ![][8]


2.  Da **Modelli installati**, in **Visual C#**, fare clic su **Cloud**, 
    quindi su **Servizio cloud**. Assegnare al progetto il nome 
    **MultiTierApp**. Fare quindi clic su **OK**.

    ![][9]

3.  Dai ruoli **.NET Framework 4.5** fare doppio clic su **Ruolo Web
    ASP.NET**.

    ![][10]

4.  Passare il puntatore su **RuoloWeb1** in **Soluzione Servizio cloud Azure**, quindi fare clic
    sull'icona a forma di matita e rinominare il ruolo Web in **FrontendWebRole**. Fare quindi clic su **OK**. Assicurarsi di immettere "Frontend" con una "e" minuscola, non "FrontEnd".

    ![][11]

5.  Nella finestra di dialogo **Nuovo progetto ASP.NET**, nell'elenco **Selezionare un modello**, fare clic su **MVC**,
    , quindi su **OK**.

    ![][12]

6.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Riferimenti**, quindi scegliere 
    **Gestisci pacchetti NuGet...** oppure **Aggiungi riferimento a pacchetto di librerie**.

7.  Selezionare **Online** nella parte sinistra della finestra di dialogo. Cercare
    "**WindowsAzure**" e selezionare l'elemento **Bus di servizio
    Azure**. Completare quindi l'installazione e chiudere la finestra di dialogo.

    ![][13]

8.  Sono ora disponibili riferimenti agli assembly client necessari e sono stati
    aggiunti nuovi file di codice.

9.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Modelli**, quindi scegliere **Aggiungi** e
     infine **Classe**. Nella casella Nome digitare il nome 
    **OnlineOrder.cs**. Fare quindi clic su **Aggiungi**.

### Scrivere il codice per il ruolo Web

In questa sezione verranno innanzitutto create le diverse pagine
visualizzate dall'applicazione.

1.  Nel file **OnlineOrder.cs** in Visual Studio sostituire la definizione
    dello spazio dei nomi esistente con il codice seguente:

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  In **Esplora soluzioni** fare doppio clic su 
    **Controllers\HomeController.cs**. Aggiungere le istruzioni**using**
    seguenti nella parte iniziale del file per includere gli spazi dei nomi per il
    modello appena creato, oltre al bus di servizio:

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  Nel file **HomeController.cs** in Visual Studio sostituire la
    definizione dello spazio dei nomi esistente con il codice seguente. Il codice
    include metodi per la gestione dell'invio di elementi alla coda:

        namespace FrontendWebRole.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    // Simply redirect to Submit, since Submit will serve as the
                    // front page of this application
                    return RedirectToAction("Submit");
                }

                public ActionResult About()
                {
                    return View();
                }

                // GET: /Home/Submit
                // Controller method for a view you will create for the submission
                // form
                public ActionResult Submit()
                {
                    // Will put code for displaying queue message count here.

                    return View();
                }

                // POST: /Home/Submit
                // Controller method for handling submissions from the submission
                // form 
                [HttpPost]
				// Attribute to help prevent cross-site scripting attacks and 
				// cross-site request forgery  
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

4.  Dal menu **Compila** scegliere **Compila soluzione**.

5.  Verrà ora creata la visualizzazione per il metodo **Submit()** 
    creato in precedenza. Fare clic con il pulsante destro del mouse nel metodo Submit(), quindi scegliere 
    **Aggiungi visualizzazione**

    ![][14]

6.  Verrà aperta una finestra di dialogo per la creazione di una visualizzazione. Selezionare la classe 
    **OnlineOrder** nel menu a discesa **Classe modello**, quindi scegliere 
    **Crea** nel menu a discesa **Modello**.

    ![][15]

7.  Fare clic su **Aggiungi**.

8.  Verrà ora modificato il nome visualizzato dell'applicazione. In 
    **Esplora soluzioni**, fare doppio clic su
    **Views\Shared\\_Layout.cshtml** per aprirlo nell'editor di Visual
    Studio.

9.  Sostituire tutte le occorrenze di **My ASP.NET MVC Application** con
    **LITWARE'S Awesome Products**.

10.	Sostituire **"your logo here"** con **LITWARE'S Awesome Products**:

	![][16]

11. Rimuovere i collegamenti **Home**, **About** e **Contact**. Eliminare il codice evidenziato:

	![][28]
  

12. Modificare infine la pagina di invio in modo da includere informazioni
    sulla coda. In **Esplora soluzioni** fare doppio clic sul file 
    **Views\Home\Submit.cshtml** per aprirlo nell'editor di
    Visual Studio. Aggiungere la riga seguente dopo **<h2>Submit</h2>**. Per il momento
    il valore di **ViewBag.MessageCount** non è specificato. Il valore verrà inserito successivamente.

        <p>Current Number of Orders in Queue Waiting to be Processed: @ViewBag.MessageCount</p>
             

13. L'interfaccia utente è stata implementata. È possibile premere **F5** per eseguire
    l'applicazione e confermare che abbia l'aspetto previsto.

    ![][17]

### Scrivere codice per l'invio di elementi a una coda del bus di servizio

Verrà ora aggiunto il codice per l'invio di elementi a una coda. Verrà creata
innanzitutto una classe contenente le informazioni di connessione
della coda del bus di servizio. Verrà quindi inizializzata la connessione da
**Global.aspx.cs**. Verrà infine aggiornato il codice di invio
creato in precedenza nel file **HomeController.cs** in modo da inviare effettivamente elementi a una
coda del bus di servizio.

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse su **FrontendWebRole**. È necessario fare clic con il pulsante destro del mouse sul progetto, non sul ruolo. Fare clic su **Aggiungi**, quindi su **Classe**.

2.  Assegnare alla classe il nome **QueueConnector.cs**. Fare clic su **Add** per creare la classe.

3.  Verrà ora incollato il codice che include le informazioni di connessione
    e i metodi per l'inizializzazione della connessione a una coda del
    bus di servizio. In QueueConnector.cs incollare il codice seguente, quindi immettere
    i valori per **Namespace**, **IssuerName** e **IssuerKey**. È possibile
    ottenere tali valori dal [portale di gestione][Portale di gestione di Azure] oppure da Esplora server di Visual Studio nel nodo **Service Bus**.

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

                // Obtain these values from the Management Portal
                public const string Namespace = "your service bus namespace";
                public const string IssuerName = "issuer name";
                public const string IssuerKey = "issuer key";

                // The name of your queue
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Create the namespace manager which gives you access to
                    // management operations
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedSecretTokenProvider(
                        IssuerName, IssuerKey);
                    return new NamespaceManager(uri, tP);
                }

                public static void Initialize()
                {
                    // Using Http to be friendly with outbound firewalls
                    ServiceBusEnvironment.SystemConnectivity.Mode = 
                        ConnectivityMode.Http;

                    // Create the namespace manager which gives you access to 
                    // management operations
                    var namespaceManager = CreateNamespaceManager();

                    // Create the queue if it does not exist already
                    if (!namespaceManager.QueueExists(QueueName))
                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    // Get a client to the queue
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address, 
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

4.  Si farà ora in modo di assicurare che il metodo **Initialize** venga chiamato. In **Esplora soluzioni**, fare doppio clic su **Global.asax\Global.asax.cs**.

5.  Aggiungere la riga seguente alla fine del metodo **Application_Start**
    :

        FrontendWebRole.QueueConnector.Initialize();

6.  Verrà infine aggiornato il codice Web creato in precedenza, in modo da
    inviare elementi alla coda. In **Esplora soluzioni**,
    fare doppio clic su **Controllers\HomeController.cs** creato
    in precedenza.

7.  Aggiornare il metodo **Submit()** come indicato di seguito, in modo da ottenere il conteggio dei messaggi
    per la coda:

        public ActionResult Submit()
        {            
            // Get a NamespaceManager which allows you to perform management and
            // diagnostic operations on your Service Bus Queues.
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // Get the queue, and obtain the message count.
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  Aggiornare il metodo **Submit(OnlineOrder order)** come indicato di seguito per inviare le
    informazioni sull'ordine alla coda:

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // Create a message from the order
                var message = new BrokeredMessage(order);
                
                // Submit the order
                QueueConnector.OrdersQueueClient.Send(message);
                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }

9.  È ora possibile eseguire di nuovo l'applicazione. Ogni volta che si invia un
    ordine, il conteggio dei messaggi aumenta.

    ![][18]

<h2>Gestione configurazione del cloud</h2>

Azure supporta un nuovo set di API gestite che fornisce un sistema coerente per la creazione di nuove istanze dei client di servizio Azure, ad esempio il bus di servizio, in tutti i servizi cloud Microsoft. Le API consentono di creare istanze di tali client (ad esempio, **CloudBlobClient**, **QueueClient**, **TopicClient**) indipendentemente dalla posizione in cui è ospitata l'applicazione, ovvero in locale, in un servizio cloud Microsoft, in siti Web o in un ruolo VM persistente. È inoltre possibile usare queste API per recuperare le informazioni di configurazione necessarie per creare istanze dei client e per modificare la configurazione senza dover ridistribuire l'applicazione chiamante. Le API si trovano nella classe [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][]. Sono inoltre disponibili API sul lato client.

### Stringa di connessione

Per creare un'istanza di un client, ad esempio un **QueueClient** del bus di servizio, è possibile rappresentare le informazioni di configurazione come una stringa di connessione. Sul lato client è disponibile un metodo **CreateFromConnectionString()** che consente di creare istanze del tipo di client mediante la relativa stringa di connessione. Ad esempio, con la seguente sezione di configurazione:

	<ConfigurationSettings>
    ...
    	<Setting name="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
	</ConfigurationSettings>

Il seguente codice recupera la stringa di connessione, crea una coda e inizializza la connessione alla coda:

	QueueClient Client; 

	string connectionString =
     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
	
    var namespaceManager =
     NamespaceManager.CreateFromConnectionString(connectionString); 

	if (!namespaceManager.QueueExists(QueueName))
    {
        namespaceManager.CreateQueue(QueueName);
    }

	// Initialize the connection to Service Bus Queue
	Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

Il codice riportato nella sezione seguente usa tali API di gestione della configurazione.

<h2>Creare il ruolo di lavoro</h2>

Verrà ora creato il ruolo di lavoro che elabora l'invio
dell'ordine. In questo esempio viene usato il modello di progetto **Worker Role with Service Bus Queue** di Visual Studio. Verrà innanzitutto usato Esplora server in Visual Studio per ottenere le credenziali necessarie.

1. Se Visual Studio è già stato connesso all'account Azure, come illustrato nella sezione **Configurazione dello spazio dei nomi mediante Esplora server di Visual Studio**, passare al passaggio 5. 

3. Dalla barra dei menu in Visual Studio scegliere **Visualizza**, quindi fare clic su **Esplora server**. Un nodo **Service Bus** verrà visualizzato sotto **Azure** nella gerarchia di Esplora server, come mostrato nella figura seguente.

	![][21]

2. In Esplora server espandere **Azure**, quindi fare clic con il pulsante destro del mouse su **Service Bus** e infine scegliere **Aggiungi nuova connessione**.

3. Nella finestra di dialogo **Aggiungi connessione** immettere il nome dello spazio dei nomi servizio, quindi il nome e la chiave dell'autorità emittente. Fare quindi clic su **OK** per connettersi.

	![][22]

4.  In **Esplora soluzioni** di Visual Studio, fare clic con il pulsante destro del mouse sulla cartella 
    **Roles** nel progetto **MultiTierApp**.

5.  Fare clic su **Aggiungi**, quindi fare clic su **Nuovo progetto di ruolo di lavoro**. Verrà visualizzata la finestra di dialogo **Aggiungi nuovo progetto di ruolo**.

	![][26]

6.  Nella finestra di dialogo **Aggiungi nuovo progetto di ruolo** fare clic sul progetto relativo al ruolo **Worker Role with Service Bus Queue**, come illustrato nella seguente figura:

	![][23]

7.  Nella casella **Name** assegnare il nome **OrderProcessingRole**al progetto. Fare quindi clic su **Aggiungi**.

8.  In Esplora server fare clic con il pulsante destro del mouse sul nome del proprio spazio dei nomi servizio, quindi scegliere **Proprietà**. Nel riquadro **Proprietà** di Visual Studio la prima voce contiene una stringa di connessione popolata con l'endpoint dello spazio dei nomi servizio che contiene le credenziali di autorizzazione necessarie. Vedere, ad esempio, la seguente figura: Fare doppio clic su **ConnectionString**, quindi premere **Ctrl+C** per copiare questa stringa negli Appunti.

	![][24]

9.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul ruolo **OrderProcessingRole** creato nel passaggio 7. Assicurarsi di fare clic con il pulsante destro del mouse su **OrderProcessingRole** in **Roles**, non sulla classe. Fare quindi clic su **Proprietà**.

10.  Nella scheda **Impostazioni** della finestra di dialogo **Proprietà** posizionare il cursore all'interno della casella **Valore** per **Microsoft.ServiceBus.ConnectionString**, quindi incollare il valore dell'endpoint copiato al passaggio 8.

	![][25]

11.  Creare una classe **OnlineOrder** che rappresenti gli ordini elaborati dalla coda. È possibile riutilizzare una classe creata in precedenza. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **OrderProcessingRole**. È necessario fare clic con il pulsante destro del mouse sul progetto, non sul ruolo. Fare clic su **Aggiungi**, quindi fare clic su **Elemento esistente**.

12. Selezionare la sottocartella per **FrontendWebRole\Models** e fare doppio clic su **OnlineOrder.cs** per aggiungerlo al progetto corrente.

13. In WorkerRole.cs modificare il valore della variabile **QueueName** in **WorkerRole.cs** da `"ProcessingQueue"` a `"OrdersQueue"` come nel seguente codice:

		// The name of your queue
		const string QueueName = "OrdersQueue";

14. Aggiungere l'istruzione using seguente all'inizio del file WorkerRole.cs:

		using FrontendWebRole.Models;

15. Nella funzione `Run()`, all'interno della chiamata `OnMessage`, aggiungere il codice seguente nella clausola `try`:

		Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
		// View the message as an OnlineOrder
		OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
		Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
		receivedMessage.Complete();

16.  L'applicazione è stata completata. È possibile testare
    l'applicazione completa, come già fatto in precedenza, premendo F5. Il numero totale dei messaggi non aumenta perché il ruolo di lavoro elabora gli elementi dalla coda e li contrassegna come completati. È possibile verificare l'output di traccia del ruolo di lavoro
    visualizzando l'interfaccia utente dell'emulatore di calcolo di Azure. Per 
    eseguire questa operazione, fare clic con il pulsante destro del mouse sull'icona dell'emulatore nell'area
    di notifica della barra delle applicazioni, quindi scegliere **Show Compute Emulator UI**.

    ![][19]

    ![][20]

<h2><a name="nextsteps"></a>Passaggi successivi</h2>  

Per altre informazioni sul bus di servizio, vedere le risorse seguenti:  
  
* [Service Bus di Azure][sbmsdn]  
* [Procedure relative al bus di servizio][sbwacom]  
* [Come usare le code del bus di servizio][sbwacomqhowto]  

Per altre informazioni sugli scenari multilivello o per informazioni su come distribuire un'applicazione in un servizio cloud, vedere:  

* [Applicazione .NET multilivello con tabelle, code e BLOB di archiviazione di Azure][multitierstorage]  

È consigliabile implementare il front-end di un'applicazione multilivello in un sito Web di Azure anziché in un servizio cloud di Azure. Per altre informazioni sulle differenze tra siti Web e servizi cloud, vedere [Modelli di esecuzione di Azure][executionmodels].

Per implementare l'applicazione creata in questa esercitazione come progetto Web standard invece che come ruolo Web di un servizio cloud, attenersi alla procedura riportata in questa esercitazione, apportando le modifiche seguenti:

1. Quando si crea il progetto, scegliere il modello di progetto **Applicazione Web ASP.NET MVC 4** nella categoria **Web** invece del modello **Servizio cloud** nella categoria **Cloud**. Seguire quindi le istruzioni disponibili per la creazione dell'applicazione MVC, fino alla sezione **Gestione configurazione del cloud**.

2. Quando si crea un ruolo di lavoro, crearlo in una nuova soluzione distinta, seguendo le istruzioni analoghe originali per la creazione del ruolo Web, ma creando solo il ruolo di lavoro nel progetto servizio cloud. Seguire le stesse indicazioni per la creazione del ruolo di lavoro.

3. È possibile verificare separatamente il front-end e il back-end oppure eseguirli simultaneamente in istanze distinte di Visual Studio.

Per informazioni su come distribuire il front-end in un sito Web di Azure, vedere [Distribuzione di un'applicazione Web ASP.NET in un sito Web di Azure](http://www.windowsazure.com/it-it/develop/net/tutorials/get-started/). Per informazioni su come distribuire il back-end in un servizio cloud di Azure, vedere Applicazione [.NET multilivello con tabelle, code e BLOB di archiviazione di Azure][multitierstorage].


  [0]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: http://msdn.microsoft.com/it-it/library/windowsazure/hh767287.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Get Tools and SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png
  
  
  
  [Portale di gestione di Azure]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.jpg
  [10]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [17]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png
  
  [19]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [21]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorer.png
  [22]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerAddConnect.png
  [23]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [24]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [25]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [28]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [30]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [31]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [32]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [33]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png
  [34]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/VSProperties.png
  [sbmsdn]: http://msdn.microsoft.com/it-it/library/windowsazure/ee732537.aspx  
  [sbwacom]: /it-it/documentation/services/service-bus/  
  [sbwacomqhowto]: /it-it/develop/net/how-to-guides/service-bus-queues/  
  [mutitierstorage]: /it-it/develop/net/tutorials/multi-tier-web-site/1-overview/ 
  [executionmodels]: http://www.windowsazure.com/it-it/develop/net/fundamentals/compute/

                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    // Get a client to the queue
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address, 
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

4.  Now, you will ensure your **Initialize** method gets called. In **Solution Explorer**, double-click **Global.asax\Global.asax.cs**.

5.  Add the following line to the bottom of the **Application_Start**
    method:

        FrontendWebRole.QueueConnector.Initialize();

6.  Finally, you will update your web code you created earlier, to
    submit items to the queue. In **Solution Explorer**,
    double-click **Controllers\HomeController.cs** that you created
    earlier.

7.  Update the **Submit()** method as follows to get the message count
    for the queue:

        public ActionResult Submit()
        {            
            // Get a NamespaceManager which allows you to perform management and
            // diagnostic operations on your Service Bus Queues.
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // Get the queue, and obtain the message count.
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  Update the **Submit(OnlineOrder order)** method as follows to submit
    order information to the queue:

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // Create a message from the order
                var message = new BrokeredMessage(order);
                
                // Submit the order
                QueueConnector.OrdersQueueClient.Send(message);
                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }

9.  You can now run your application again. Each time you submit an
    order, the message count increases.

    ![][18]

<h2>Cloud configuration manager</h2>

Azure supports a set of managed APIs that provides a consistent way to create new instances of Azure service clients (such as the Service Bus) across Microsoft cloud services. These APIs enable you to instantiate these clients (for example, **CloudBlobClient**, **QueueClient**, **TopicClient**) regardless of where the application is hosted -- on-premises, in a Microsoft cloud service, in websites, or in a persistent VM Role. You can also use these APIs to retrieve the configuration information necessary for instantiating these clients, and to change the configuration without having to redeploy the calling application. The APIs are located in the [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][] class. There are also APIs on the client side.

### Connection string

To instantiate a client (for example, a Service Bus **QueueClient**), you can represent the configuration information as a connection string. On the client side, there is a **CreateFromConnectionString()** method that instantiates that client type by using that connection string. For example, given the following configuration section:

	<ConfigurationSettings>
    ...
    	<Setting name="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
	</ConfigurationSettings>

The following code retrieves the connection string, creates a queue, and initializes the connection to the queue:

	QueueClient Client; 

	string connectionString =
     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
	
    var namespaceManager =
     NamespaceManager.CreateFromConnectionString(connectionString); 

	if (!namespaceManager.QueueExists(QueueName))
    {
        namespaceManager.CreateQueue(QueueName);
    }

	// Initialize the connection to Service Bus Queue
	Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

The code in the following section uses these configuration management APIs.

<h2>Create the worker role</h2>

You will now create the worker role that processes the order
submissions. This example uses the **Worker Role with Service Bus Queue** Visual Studio project template. First, you will use Server Explorer in Visual Studio to obtain the required credentials.

1. If you've already connected Visual Studio to your Azure account, as described in the section **Set up the namespace using the Visual Studio Server Explorer,** skip ahead to step 5. 

3. From the menu bar in Visual Studio, choose **View**, and then click **Server Explorer**. A **Service Bus** node appears under **Azure** within the Server Explorer hierarchy, as in the following figure.

	![][21]

2. In Server Explorer, expand **Azure**, then right-click **Service Bus**, then click **Add New Connection**.

3. In the **Add Connection** dialog, type the name of the service namespace, the issuer name, and the issuer key. Then click **OK** to connect.

	![][22]

4.  In Visual Studio, in **Solution Explorer** right-click the
    **Roles** folder under the **MultiTierApp** project.

5.  Click **Add**, and then click **New Worker Role Project**. The **Add New Role Project** dialog appears.

	![][26]

6.  In the **Add New Role Project dialog**, click **Worker Role with Service Bus Queue**, as in the following figure:

	![][23]

7.  In the **Name** box, name the project **OrderProcessingRole**. Then click **Add**.

8.  In Server Explorer, right-click the name of your service namespace, then click **Properties**. In the Visual Studio **Properties** pane, the first entry contains a connection string that is populated with the service namespace endpoint containing the required authorization credentials. For example, see the following figure. Double-click **ConnectionString**, and then press **Ctrl+C** to copy this string to the clipboard.

	![][24]

9.  In Solution Explorer, right-click the **OrderProcessingRole** you created in step 7 (make sure that you right-click **OrderProcessingRole** under **Roles**, and not the class). Then click **Properties**.

10.  In the **Settings** tab of the **Properties** dialog, click inside the **Value** box for **Microsoft.ServiceBus.ConnectionString**, and then paste the endpoint value you copied in step 8.

	![][25]

11.  Create an **OnlineOrder** class to represent the orders as you process them from the queue. You can reuse a class you have already created. In Solution Explorer, right-click the **OrderProcessingRole** project (right-click the project, not the role). Click **Add**, then click **Existing Item**.

12. Browse to the subfolder for **FrontendWebRole\Models**, and double-click **OnlineOrder.cs** to add it to this project.

13. In WorkerRole.cs, replace the value of the **QueueName** variable in **WorkerRole.cs** from `"ProcessingQueue"` to `"OrdersQueue"` as in the following code:

		// The name of your queue
		const string QueueName = "OrdersQueue";

14. Add the following using statement at the top of the WorkerRole.cs file:

		using FrontendWebRole.Models;

15. In the `Run()` function, inside the `OnMessage` call, add the following code inside the `try` clause:

		Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
		// View the message as an OnlineOrder
		OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
		Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
		receivedMessage.Complete();

16.  You have completed the application. You can test the full
    application as you did earlier, by pressing F5. Note that the message count does not increment, because the worker role processes items from the queue and marks them as complete. You can see the trace output of your
    worker role by viewing the Azure Compute Emulator UI. You
    can do this by right-clicking the emulator icon in the notification
    area of your taskbar and selecting **Show Compute Emulator UI**.

    ![][19]

    ![][20]

<h2><a name="nextsteps"></a>Next steps</h2>  

To learn more about Service Bus, see the following resources:  
  
* [Azure Service Bus][sbmsdn]  
* [Service Bus How To's][sbwacom]  
* [How to Use Service Bus Queues][sbwacomqhowto]  

To learn more about multi-tier scenarios, or to learn how to deploy an application to a cloud service, see:  

* [.NET Multi-Tier Application Using Storage Tables, Queues, and Blobs][mutitierstorage]  

You might want to implement the front-end of a multi-tier application in an Azure Website instead of an Azure Cloud Service. To learn more about the difference between websites and cloud services, see [Azure Execution Models][executionmodels].

To implement the application you create in this tutorial as a standard web project instead of as a cloud service web role, follow the steps in this tutorial with the following differences:

1. When you create the project, choose the **ASP.NET MVC 4 Web Application** project template in the **Web** category instead of the **Cloud Service** template in the **Cloud** category. Then follow the same directions for creating the MVC application, until you get to the **Cloud configuration manager** section.

2. When you create the worker role, create it in a new, separate solution, similar to the original instructions for the web role. Now however, you're creating just the worker role in the cloud service project. Then follow the same directions for creating the worker role.

3. You can test the front-end and back-end separately, or you can run both simultaneously in separate Visual Studio instances.

To learn how to deploy the front end to an Azure Website, see [Deploying an ASP.NET Web Application to an Azure Website](http://www.windowsazure.com/it-it/develop/net/tutorials/get-started/). To learn how to deploy the back end to an Azure Cloud Service, see [.NET Multi-Tier Application Using Storage Tables, Queues, and Blobs][mutitierstorage].


  [0]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: http://msdn.microsoft.com/it-it/library/windowsazure/hh767287.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Get Tools and SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png
  
  
  
  [Azure Management Portal]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.jpg
  [10]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [17]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png
  
  [19]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [21]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorer.png
  [22]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerAddConnect.png
  [23]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [24]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [25]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [28]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [30]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [31]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [32]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [33]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png
  [34]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/VSProperties.png
  [sbmsdn]: http://msdn.microsoft.com/it-it/library/windowsazure/ee732537.aspx  
  [sbwacom]: /it-it/documentation/services/service-bus/  
  [sbwacomqhowto]: /it-it/develop/net/how-to-guides/service-bus-queues/  
  [mutitierstorage]: /it-it/develop/net/tutorials/multi-tier-web-site/1-overview/ 
  [executionmodels]: http://www.windowsazure.com/it-it/develop/net/fundamentals/compute/
