<properties
	pageTitle="Applicazione ibrida cloud/locale (.NET) - Azure"
	description="Informazioni su come creare un'applicazione .NET ibrida locale e nel cloud usando l'inoltro di Bus di servizio di Azure."
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
	ms.topic="get-started-article" 
	ms.date="06/02/2015"
	ms.author="sethm"/>

#Uso di Inoltro del bus di servizio per creare applicazioni ibride cloud/locali .NET

##Introduzione

Lo sviluppo di applicazioni cloud ibride con Microsoft Azure è semplice con Visual Studio 2013 e Azure SDK for .NET gratuito. In questa guida si presuppone che l'utente non abbia mai usato Azure. In meno di 30 minuti si otterrà un'applicazione in esecuzione nel cloud e che usa più risorse di Azure.

Si acquisiranno le nozioni seguenti:

-   Creare o adattare un servizio Web esistente utilizzabile in una soluzione Web.
-   Usare Inoltro del bus di servizio per condividere dati tra un'applicazione Azure e un servizio Web ospitato altrove.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

##Vantaggi derivanti dall'uso di inoltro del bus di servizio con le soluzioni ibride

Le soluzioni aziendali sono in genere costituite da una combinazione di codice personalizzato appositamente scritto in risposta a specifici requisiti aziendali e da funzionalità esistenti fornite da soluzioni e sistemi già implementati.

Da qualche tempo gli architetti di soluzioni hanno iniziato a usare il cloud per semplificare la gestione dei requisiti di scalabilità e ridurre i costi operativi. Nell'adottare un tale approccio hanno quindi scoperto che le risorse di servizi esistenti che desiderano sfruttare come blocchi predefiniti delle soluzioni si trovano all'interno del firewall aziendale e quindi non sono facilmente raggiungibili dalla soluzione cloud. Molti servizi interni non sono creati né vengono ospitati in modo tale da consentirne una agevole esposizione al perimetro della rete aziendale.

Il servizio *Inoltro del bus di servizio* è progettato per i casi d'uso in cui servizi Web WCF (Windows Communication Foundation) vengono resi accessibili in modo sicuro a soluzioni che risiedono all'esterno del perimetro aziendale senza richiedere modifiche di notevole impatto all'infrastruttura di rete aziendale. Tali servizi di Inoltro del bus di servizio sono comunque ospitati all'interno dell'ambiente esistente, ma delegano l'ascolto delle sessioni e delle richieste in ingresso al bus di servizio ospitato nel cloud. Bus di servizio protegge questi servizi dall'accesso non autorizzato tramite l'autenticazione con [firma di accesso condiviso](https://msdn.microsoft.com/library/dn170478.aspx).

##Scenario della soluzione

In questa esercitazione si creerà un sito Web ASP.NET MVC che consentirà di visualizzare un elenco di prodotti nella pagina dell'inventario dei prodotti.

![][0]

Ai fini dell'esercitazione si presuppone che le informazioni sui prodotti siano già disponibili in un sistema locale esistente e che per accedere a tale sistema venga usato Inoltro del bus di servizio. Tale operazione viene simulata da un servizio Web eseguito in una semplice applicazione console ed è supportata da un insieme di prodotti in memoria. Sarà quindi possibile eseguire questa applicazione console nel computer in uso e distribuire il ruolo Web in Azure. In tal modo sarà possibile osservare che il ruolo Web in esecuzione nel data center di Azure verrà effettivamente chiamato nel computer in uso, anche se quest'ultimo sarà quasi certamente protetto da almeno un firewall e un livello NAT (Network Address Translation).

Di seguito è riportata una schermata relativa alla pagina iniziale dell'applicazione Web completata.

![][1]

##Configurare l'ambiente di sviluppo

Prima di iniziare a sviluppare l'applicazione Azure, è necessario ottenere gli strumenti idonei e configurare l'ambiente di sviluppo:

1.  Per installare Azure SDK per .NET, fare clic sul collegamento seguente.

    [Get Tools and SDK][]

2. 	Fare clic su **Install the SDK** per la versione di Visual Studio in uso. Nelle procedure incluse in questa esercitazione viene usato Visual Studio 2013.

	![][42]

4.  Quando viene richiesto se eseguire o salvare il file di installazione, fare clic su **Esegui**:

    ![][2]

5.  Nell'**Installazione guidata piattaforma Web** fare clic su **Installa** e procedere con l'installazione:

    ![][3]

6.  Al termine dell'installazione, saranno disponibili tutti gli strumenti necessari per avviare lo sviluppo. Nell'SDK sono disponibili gli strumenti che consentono di sviluppare con facilità applicazioni per Azure in Visual Studio. Se Visual Studio non è ancora installato, l'SDK installerà anche la versione gratuita di Visual Studio Express.

## Creare uno spazio dei nomi del servizio

Per iniziare a usare le funzionalità del bus di servizio in Azure, è innanzitutto necessario creare uno spazio dei nomi del servizio Uno spazio dei nomi fornisce un contenitore di ambito per fare riferimento alle risorse del bus di servizio all'interno dell'applicazione.

È possibile gestire gli spazi dei nomi e le entità di messaggistica del bus di servizio usando il [portale di gestione di Azure][] o Esplora server di Visual Studio, ma è possibile creare spazi dei nomi nuovi solo all'interno del portale.

### Per creare uno spazio dei nomi del servizio tramite il portale:

1.  Accedere al [portale di gestione di Azure][].

2.  Nel pannello di navigazione sinistro del portale di gestione fare clic su **Bus di servizio**.

3.  Nel riquadro inferiore del portale di gestione fare clic su **Crea**. ![][5]

4.  Nella finestra di dialogo **Add a new namespace** immettere un nome per lo spazio dei nomi. Verrà effettuato immediatamente un controllo sulla disponibilità del nome.![][6]

5.  Dopo avere verificato che lo spazio dei nomi è disponibile, scegliere il paese o l'area in cui dovrà essere ospitato. Assicurarsi di usare lo stesso paese/area in cui verranno distribuite le risorse di calcolo.

    IMPORTANTE: selezionare la **stessa area** che si intende scegliere per la distribuzione dell'applicazione. In questo modo sarà possibile ottenere prestazioni ottimali.

6.	Non modificare i valori predefiniti negli altri campi della finestra di dialogo (**Messaggistica** e **Livello Standard**), quindi fare clic sul segno di spunta. A questo punto, lo spazio dei nomi verrà creato e abilitato nel sistema. Potrebbero essere necessari alcuni minuti per consentire al sistema di effettuare il provisioning delle risorse per lo spazio dei nomi creato.

	![][38]

Lo spazio dei nomi creato verrà quindi visualizzato nel portale di gestione e sarà necessario attendere qualche istante affinché venga attivato. Prima di continuare, attendere che lo stato sia **Active**.

## Recuperare le credenziali di gestione predefinite per lo spazio dei nomi

Per eseguire operazioni di gestione nel nuovo spazio dei nomi, ad esempio creare entità di messaggistica, è necessario ottenere le credenziali per lo spazio dei nomi.

1.  Fare clic sullo spazio dei nomi del servizio nella finestra principale.

	![][39]


2.  Fare clic su **Connection Information**.

	![][40]


3.  Nel riquadro **Accedi a informazioni di connessione** individuare la stringa di connessione che contiene la chiave della firma di accesso condiviso e il nome della chiave.

	![][45]


4.  Prendere nota di queste credenziali oppure copiarle negli Appunti.

## Creare un server locale

In primo luogo, si creerà un sistema di catalogo prodotti locale fittizio. Si tratta di un sistema locale abbastanza semplice che intende rappresentare un catalogo prodotti effettivo che include una superficie completa di servizi da integrare.

Questo progetto si avvia come applicazione console di Visual Studio. Usa il pacchetto NuGet del bus di servizio per includere le librerie e le impostazioni di configurazione del bus di servizio. L'estensione NuGet di Visual Studio semplifica l'installazione e l'aggiornamento di librerie e strumenti in Visual Studio e in Visual Studio Express. Il pacchetto NuGet del bus di servizio è il modo più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con tutte le dipendenze di tale servizio. Per informazioni dettagliate su NuGet e sul pacchetto del bus di servizio, vedere la pagina relativa all'[uso del pacchetto NuGet del bus di servizio][].

### Creare il progetto

1.  Usando privilegi di amministratore, avviare Microsoft Visual Studio 2013 o Microsoft Visual Studio Express. Per avviare Visual Studio con privilegi di amministratore, fare clic con il pulsante destro del mouse su **Microsoft Visual Studio 2013 (o Microsoft Visual Studio Express)**, quindi scegliere **Esegui come amministratore**.

2.  In Visual Studio, nel menu **File** scegliere **Nuovo**, quindi fare clic su **Progetto**.

    ![][10]

3.  Da **Modelli installati**, in **Visual C#**, fare clic su **Applicazione console**. Nella casella **Nome** digitare il nome **ProductsServer**:

    ![][11]

4.  Fare clic su **OK** per creare il progetto **ProductsServer**.

5.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **ProductsServer**, quindi scegliere **Proprietà**.

6.  Fare clic sulla scheda **Applicazione** a sinistra, quindi verificare che **.NET Framework 4** o **.NET Framework 4.5** venga visualizzato nell'elenco a discesa **Framework di destinazione:**. In caso contrario, selezionarlo dall'elenco a discesa e fare clic su **Sì** quando viene richiesto di ricaricare il progetto.

    ![][12]

7.  Se Gestione pacchetti NuGet per Visual Studio è già stato installato, continuare con il passaggio successivo. In caso contrario, visitare [NuGet][] e fare clic sull'opzione per [installare NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Seguire le istruzioni visualizzate per installare Gestione pacchetti NuGet, quindi riavviare Visual Studio.

7.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Riferimenti**, quindi fare clic su **Manage NuGet Packages**.

8.  Nella colonna di sinistra della finestra di dialogo di NuGet fare clic su **Online**.

9. 	Nella colonna di destra fare clic sulla casella **Ricerca**, digitare "**Bus di servizio**" e selezionare l'elemento **Bus di servizio di Microsoft Azure**. Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.

    ![][13]

    I riferimenti agli assembly client necessari sono ora disponibili.

9.  Aggiungere una nuova classe per il contratto dei prodotti. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **ProductsServer**, scegliere **Aggiungi** e quindi fare clic su **Classe**.

    ![][14]

10. Nella casella **Nome** digitare il nome **ProductsContracts.cs**. Fare quindi clic su **Aggiungi**.

11. In **ProductsContract.cs** sostituire la definizione dello spazio dei nomi con il codice seguente, che consente di definire il contratto per il servizio:

        namespace ProductsServer
        {
            using System.Collections.Generic;
            using System.Runtime.Serialization;
            using System.ServiceModel;

            // Define the data contract for the service
            [DataContract]
            // Declare the serializable properties
            public class ProductData
            {
                [DataMember]
                public string Id { get; set; }
                [DataMember]
                public string Name { get; set; }
                [DataMember]
                public string Quantity { get; set; }
            }

            // Define the service contract.
            [ServiceContract]
            interface IProducts
            {
                [OperationContract]
                IList<ProductData> GetProducts();

            }

            interface IProductsChannel : IProducts, IClientChannel
            {
            }
        }

12. In Program.cs sostituire la definizione dello spazio dei nomi con il codice seguente, che consente di aggiungere il servizio profili e l'host relativo:

        namespace ProductsServer
        {
            using System;
            using System.Linq;
            using System.Collections.Generic;
            using System.ServiceModel;

            // Implement the IProducts interface
            class ProductsService : IProducts
            {

                // Populate array of products for display on Website
                ProductData[] products =
                    new []
                        {
                            new ProductData{ Id = "1", Name = "Rock",
                                             Quantity = "1"},
                            new ProductData{ Id = "2", Name = "Paper",
                                             Quantity = "3"},
                            new ProductData{ Id = "3", Name = "Scissors",
                                             Quantity = "5"},
                            new ProductData{ Id = "4", Name = "Well",
                                             Quantity = "2500"},
                        };

                // Display a message in the service console application
                // when the list of products is retrieved
                public IList<ProductData> GetProducts()
                {
                    Console.WriteLine("GetProducts called.");
                    return products;
                }

            }

            class Program
            {
                // Define the Main() function in the service application
                static void Main(string[] args)
                {
                    var sh = new ServiceHost(typeof(ProductsService));
                    sh.Open();

                    Console.WriteLine("Press ENTER to close");
                    Console.ReadLine();

                    sh.Close();
                }
            }
        }

13. In **Esplora soluzioni** fare doppio clic sul file **App.config** per aprirlo nell'editor di **Visual Studio**. Sostituire il contenuto di **system.ServiceModel** con il codice XML seguente. Assicurarsi di sostituire *yourServiceNamespace* con il nome dello spazio dei nomi del servizio e *yourKey* con la chiave di firma di accesso condiviso recuperata in precedenza dal portale di gestione di Azure:

        <system.serviceModel>
          <extensions>
             <behaviorExtensions>
                <add name="transportClientEndpointBehavior" type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Version=2.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </behaviorExtensions>
              <bindingExtensions>
                 <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </bindingExtensions>
          </extensions>
          <services>
             <service name="ProductsServer.ProductsService">
               <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts"
        behaviorConfiguration="products"/>
             </service>
          </services>
          <behaviors>
             <endpointBehaviors>
               <behavior name="products">
                 <transportClientEndpointBehavior>
                    <tokenProvider>
                       <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                    </tokenProvider>
                 </transportClientEndpointBehavior>
               </behavior>
             </endpointBehaviors>
          </behaviors>
        </system.serviceModel>

14. Premere **F6** oppure nel menu **Compila** scegliere **Compila soluzione** per compilare l'applicazione e verificare l'accuratezza di quanto creato finora.

## Creare un'applicazione ASP.NET MVC

In questa sezione si creerà una semplice applicazione ASP.NET per visualizzare i dati recuperati dal servizio dei prodotti.

### Creare il progetto

1.  Assicurarsi che Visual Studio sia in esecuzione con privilegi di amministratore. In caso contrario, per avviare Visual Studio con privilegi di amministratore, fare clic con il pulsante destro del mouse su **Microsoft Visual Studio 2013 (o Microsoft Visual Studio Express)**, quindi scegliere **Esegui come amministratore**. Per l'emulatore di calcolo di Microsoft Azure, illustrato più avanti in questa guida, è necessario che Visual Studio sia avviato con privilegi di amministratore.

2.  In Visual Studio, nel menu **File** scegliere **Nuovo**, quindi fare clic su **Progetto**.

3.  Da **Modelli installati**, in **Visual C#**, fare clic su **Applicazione Web ASP.NET**. Assegnare al progetto il nome **ProductsPortal**. Fare quindi clic su **OK**.

    ![][15]

4.  Nell'elenco **Select a template** fare clic su **MVC**, quindi su **OK**.

    ![][16]

5.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Modelli**, quindi scegliere **Aggiungi** e infine **Classe**. Nella casella **Nome** digitare il nome **Product.cs**. Fare quindi clic su **Aggiungi**.

    ![][17]

### Modificare l'applicazione web

1.  Nel file Product.cs in Visual Studio sostituire la definizione dello spazio dei nomi esistente con il codice seguente:

        // Declare properties for the products inventory
        namespace ProductsWeb.Models
        {
            public class Product
            {
                public string Id { get; set; }
                public string Name { get; set; }
                public string Quantity { get; set; }
            }
        }

2.  Nel file HomeController.cs in Visual Studio sostituire la definizione dello spazio dei nomi esistente con il codice seguente:

        namespace ProductsWeb.Controllers
        {
            using System.Collections.Generic;
            using System.Web.Mvc;
            using Models;

            public class HomeController : Controller
            {
                // Return a view of the products inventory
                public ActionResult Index(string Identifier, string ProductName)
                {
                    var products = new List<Product>
                        {new Product {Id = Identifier, Name = ProductName}};
                    return View(products);
                }

            }
        }

3.  In **Esplora soluzioni** espandere Views\\Shared:

    ![][18]

4.  Fare quindi doppio clic su \_Layout.cshtml per aprirlo nell'editor di Visual Studio.

5.  Modificare tutte le occorrenze di **My ASP.NET Application** in **LITWARE's Products**.

6. Rimuovere i collegamenti **Home**, **About** e **Contact**. Eliminare il codice evidenziato:

	![][41]

7.  In **Esplora soluzioni** espandere Views\\Home:

    ![][20]

8.  Fare doppio clic su Index.cshtml per aprirlo nell'editor di Visual Studio. Sostituire l'intero contenuto del file con il codice seguente:

		@model IEnumerable<ProductsWeb.Models.Product>

		@{
    		ViewBag.Title = "Index";
		}

		<h2>Prod Inventory</h2>

		<table>
    		<tr>
        		<th>
            		@Html.DisplayNameFor(model => model.Name)
        		</th>
                <th></th>
        		<th>
            		@Html.DisplayNameFor(model => model.Quantity)
        		</th>
    		</tr>

		@foreach (var item in Model) {
    		<tr>
        		<td>
            		@Html.DisplayFor(modelItem => item.Name)
        		</td>
        		<td>
            		@Html.DisplayFor(modelItem => item.Quantity)
        		</td>
    		</tr>
		}

		</table>


9.  Per verificare l'accuratezza di quanto creato finora, premere **F6** o **CTRL+MAIUSC+B** per compilare il progetto.


### Eseguire l'applicazione in locale

Eseguire l'applicazione per verificarne il funzionamento.

1.  Assicurarsi che il progetto attivo sia **ProductsPortal**. Fare clic con il pulsante destro del mouse sul nome del progetto in **Esplora soluzioni**, quindi scegliere **Imposta come progetto di avvio**.
2.  In **Visual Studio** premere **F5**.
3.  L'applicazione dovrebbe risultare in esecuzione in un browser:

    ![][21]

## Preparazione dell'applicazione per la distribuzione in Azure

È possibile distribuire l'applicazione in un servizio cloud o un sito Web di Azure. Per altre informazioni sulle differenze tra siti Web e servizi cloud, vedere [Modelli di esecuzione di Azure][executionmodels]. Per informazioni su come distribuire l'applicazione in un sito Web di Azure, vedere [Distribuzione di un'applicazione Web ASP.NET in un sito Web di Azure](http://azure.microsoft.com/develop/net/tutorials/get-started/). In questa sezione è illustrata la procedura dettagliata per distribuire l'applicazione in un servizio cloud di Azure.

Per distribuire l'applicazione a un servizio cloud, è necessario aggiungere alla soluzione un progetto di distribuzione del progetto servizio cloud. Il progetto di distribuzione contiene le informazioni di configurazione necessarie per la corretta esecuzione dell'applicazione nel cloud.

1.  Per rendere l'applicazione distribuibile nel cloud, fare clic con il pulsante destro del mouse sul progetto **ProductsPortal** in **Esplora soluzioni**, fare clic su **Converti** e quindi su **Converti nel progetto di servizio cloud di Microsoft Azure**.

    ![][22]

2.  Per testare l'applicazione, premere **F5**.

3.  Verrà avviato l'emulatore di calcolo di Azure. Tale emulatore usa il computer locale per emulare l'applicazione in esecuzione in Azure. Per verificare se l'emulatore è stato avviato, osservare la barra delle applicazioni:

       ![][23]

4.  L'applicazione in esecuzione in locale sarà ancora visibile in un browser e l'aspetto e il funzionamento saranno analoghi a quelli di quando è stata eseguita come una normale applicazione ASP.NET MVC 4.

## Combinare i diversi componenti

Il passaggio successivo consiste nel collegare il server dei prodotti locale con l'applicazione ASP.NET MVC.

1.  Se non è già aperto, in Visual Studio riaprire il progetto **ProductsPortal** creato nella sezione "Creazione di un'applicazione ASP.NET MVC".

2.  Analogamente a quanto descritto nella sezione "Creazione di un server locale", aggiungere il pacchetto NuGet al progetto Riferimenti. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti**, quindi fare clic su **Manage NuGet Packages**.

3.  Cercare "Bus di servizio" e selezionare l'elemento **Bus di servizio di Microsoft Azure**. Completare quindi l'installazione e chiudere la finestra di dialogo.

4.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProductsPortal**, quindi scegliere **Aggiungi** e infine **Elemento esistente**.

5.  Individuare il file **ProductsContract.cs** nel progetto console **ProductsServer**. Fare clic per evidenziare ProductsContract.cs. Fare clic sulla freccia rivolta verso il basso accanto ad **Aggiungi**, quindi fare clic su **Aggiungi come collegamento**.

	![][24]

6.  Aprire il file **HomeController.cs** nell'editor di Visual Studio e sostituire la definizione dello spazio dei nomi con il codice seguente. Assicurarsi di sostituire *yourServiceNamespace* con il nome dello spazio dei nomi del servizio e *yourKey* con la chiave di firma di accesso condiviso. In tal modo il client potrà chiamare il servizio locale e restituire il risultato della chiamata.

            namespace ProductsWeb.Controllers
            {
                using System.Linq;
                using System.ServiceModel;
                using System.Web.Mvc;
                using Microsoft.ServiceBus;
                using Models;
                using ProductsServer;

                public class HomeController : Controller
                {
                    // Declare the channel factory
                    static ChannelFactory<IProductsChannel> channelFactory;

                    static HomeController()
                    {
                        // Create shared secret token credentials for authentication
                        channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                            "sb://yourServiceNamespace.servicebus.windows.net/products");
                        channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                            TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                                "RootManageSharedAccessKey", "yourKey") });
                    }

                    public ActionResult Index()
                    {
                        using (IProductsChannel channel = channelFactory.CreateChannel())
                        {
                            // Return a view of the products inventory
                            return this.View(from prod in channel.GetProducts()
                                             select
                                                 new Product { Id = prod.Id, Name = prod.Name,
                                                     Quantity = prod.Quantity });
                        }
                    }
                }
            }
7.  In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione **ProductsPortal**, quindi scegliere **Aggiungi** e infine **Progetto esistente**.

8.  Individuare il progetto **ProductsServer**, quindi fare doppio clic sulla soluzione **ProductsServer.csproj** per aggiungerla.

9.  In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione **ProductsPortal** e scegliere **Proprietà**.

10. Sul lato sinistro fare clic su **Progetto di avvio**. Sul lato destro fare clic su **Progetti di avvio multipli**. Assicurarsi che **ProductsServer**, **ProductsPortal.Azure** e **ProductsPortal** siano visualizzati in quest'ordine e che per **ProductsServer** e **ProductsPortal.Azure** sia impostata l'azione **Avvia**, mentre per **ProductsPortal** sia impostata l'azione **Nessuna**. Ad esempio:

      ![][25]

11. Sempre nella finestra Proprietà fare clic su **ProjectDependencies** sul lato sinistro.

12. Nell'elenco a discesa **Projects** fare clic su **ProductsServer**. Assicurarsi che **ProductsPortal** sia deselezionato e che **ProductsPortal.Azure** sia selezionato. Fare quindi clic su **OK**:

    ![][26]

## Eseguire l'applicazione

1.  Nel menu **File** di Visual Studio fare clic su **Salva tutto**.

2.  Premere **F5** per compilare ed eseguire l'applicazione. Il server locale, ovvero l'applicazione console **ProductsServer**, dovrebbe essere avviato per primo. Verrà quindi avviata l'applicazione **ProductsWeb** in una finestra del browser, come illustrato nella schermata seguente. Questa volta si noterà che nell'inventario dei prodotti sono elencati i dati recuperati dal sistema locale del servizio dei prodotti.

    ![][1]

## Distribuzione dell'applicazione in Azure

1.  Fare clic con il pulsante destro del mouse sul progetto **ProductsPortal** in **Esplora soluzioni**, quindi scegliere **Pubblica in Microsoft Azure**.

2.  Per visualizzare tutte le sottoscrizioni, potrebbe essere necessario effettuare l'accesso.

    Fare clic su **Sign in to see more subscriptions**:

    ![][27]

3.  Accedere usando l'account Microsoft.


8.  Fare clic su **Avanti**. Se la sottoscrizione non contiene già servizi ospitati, verrà chiesto di crearne uno. Il servizio ospitato funge da contenitore per l'applicazione all'interno della sottoscrizione di Azure. Immettere un nome che identifichi l'applicazione e selezionare l'area per la quale ottimizzare l'applicazione. I tempi di caricamento saranno presumibilmente più rapidi per gli utenti che accedono dall'area specificata.

9.  Selezionare il servizio ospitato in cui pubblicare l'applicazione. Mantenere i valori predefiniti illustrati di seguito per le rimanenti impostazioni. Fare clic su **Avanti**:

    ![][33]

10. Nell'ultima pagina fare clic su **Pubblica** per avviare il processo di distribuzione:

    ![][34]

L'operazione richiederà all'incirca 5-7 minuti. Poiché si tratta della prima pubblicazione, in Azure verrà eseguito il provisioning di una macchina virtuale, verrà applicata la protezione avanzata, verrà creato un ruolo Web nella macchina virtuale in cui ospitare l'applicazione, verrà distribuito il codice a tale ruolo Web e infine verranno configurati il bilanciamento del carico e la rete per rendere l'applicazione disponibile al pubblico.

11. Durante la pubblicazione è possibile monitorare le attività nella finestra **Azure Activity Log**, in genere ancorata alla parte inferiore della finestra di Visual Studio o di Visual Web Developer:

    ![][35]

12. Al termine della distribuzione, per visualizzare il sito Web fare clic sul collegamento **URL sito Web** nella finestra di monitoraggio.

    ![][36]

    Il sito Web dipende dal server locale, pertanto perché funzioni correttamente è necessario eseguire l'applicazione **ProductsServer** in locale. Durante l'esecuzione di richieste sul sito Web del cloud è possibile notare la presenza di richieste in arrivo nell'applicazione console locale sono, come indicato dal messaggio "GetProducts called" visualizzato nella schermata seguente.

    ![][37]

Per altre informazioni sulle differenze tra siti Web e servizi cloud, vedere [Modelli di esecuzione di Azure][executionmodels].

## Arrestare ed eliminare l'applicazione

Dopo aver distribuito l'applicazione, se necessario è possibile disabilitarla per creare e distribuire altre applicazioni entro il termine delle 750 ore mensili (31 giorni al mese) gratuite di tempo del server.

Azure addebita le istanze del ruolo Web al consumo, in base all'utilizzo di tempo del server su base oraria. Un'applicazione distribuita utilizza tempo del server anche se le istanze non sono in esecuzione e sono in stato arrestato. Un account gratuito include 750 ore mensili (31 giorni al mese) di tempo del server dedicato delle macchine virtuali utilizzate per l'hosting di queste istanze del ruolo Web.

Nella procedura seguente viene illustrato come arrestare ed eliminare l'applicazione.

1.  Accedere al [portale di gestione di Azure], fare clic su **Servizi cloud**, quindi fare clic sul nome del servizio.

2.  Fare clic sulla scheda **Dashboard** e quindi su **Stop** per sospendere temporaneamente l'applicazione. Per riavviarla, è sufficiente fare clic su Start. Fare clic su **Delete** per rimuovere completamente l'applicazione da Azure senza poterla più ripristinare.

	![][43]

## Passaggi successivi  

Per altre informazioni sul bus di servizio, vedere le risorse seguenti:

* [Bus di servizio di Azure][sbmsdn]  
* [Procedure relative al bus di servizio][sbwacom]  
* [Come usare le code del bus di servizio][sbwacomqhowto]  


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Get Tools and SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png


  [portale di gestione di Azure]: http://manage.windowsazure.com
  [5]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png



  [uso del pacchetto NuGet del bus di servizio]: http://go.microsoft.com/fwlink/?LinkId=234589
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [12]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg

  [20]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [22]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [23]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png


  [30]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-36.png
  [31]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-37.png
  [32]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [33]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [34]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [35]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [39]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [40]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [42]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [45]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-45.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx
  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: /develop/net/how-to-guides/service-bus-queues/
  [executionmodels]: http://azure.microsoft.com/develop/net/fundamentals/compute/
 

<!---HONumber=August15_HO6-->