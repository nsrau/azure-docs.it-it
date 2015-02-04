<properties urlDisplayName="Hybrid Application" pageTitle="Applicazione ibrida cloud/locale (.NET) - Azure" metaKeywords="Azure Service Bus tutorial,hybrid .NET" description="Informazioni su come creare un'applicazione .NET ibrida locale e nel cloud usando l'inoltro di Bus di servizio di Azure." metaCanonical="" services="service-bus" documentationCenter=".NET" title=".NET On-Premises/Cloud Hybrid Application Using Service Bus Relay" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/15/2014" ms.author="sethm" />






# Utilizzo di Inoltro del bus di servizio per creare applicazioni ibride cloud/locali .NET

<h2>INTRODUZIONE</h2>

Azure consente di sviluppare applicazioni cloud ibride con la stessa semplicità offerta da
Visual Studio 2013 e Azure SDK gratuito per .NET. In questa guida
si presuppone che l'utente non abbia mai usato Azure. In meno di
30 minuti si otterrà un'applicazione in esecuzione nel cloud e che usa più risorse di Windowss
Azure.

Si apprenderà come:

-   Creare o adattare un servizio Web esistente utilizzabile in una
    soluzione Web.
-   Usare Inoltro del bus di servizio di Microsoft Azure per condividere dati tra
    un'applicazione Azure e un servizio Web ospitato altrove.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

### VANTAGGI DERIVANTI DALL'UTILIZZO DI INOLTRO DEL BUS DI SERVIZIO CON LE SOLUZIONI IBRIDE

Le soluzioni aziendali sono in genere costituite da una combinazione di codice
personalizzato appositamente scritto in risposta a specifici requisiti aziendali e da funzionalità
esistenti fornite da soluzioni e sistemi già
implementati.

Da qualche tempo gli architetti di soluzioni hanno iniziato a usare il cloud per semplificare la gestione dei
requisiti di scalabilità e ridurre i costi operativi. Nell'adottare un tale approccio hanno quindi
scoperto che le risorse di servizi esistenti che desiderano sfruttare come blocchi predefiniti
delle soluzioni si trovano all'interno del firewall aziendale e quindi non sono facilmente
raggiungibili dalla soluzione cloud. Molti servizi interni non sono
creati né vengono ospitati in modo tale da consentirne una agevole esposizione al
perimetro della rete aziendale.

Il servizio *Inoltro del bus di servizio* è progettato per i casi di utilizzo in cui
servizi Web WCF (Windows Communication Foundation) vengono resi accessibili
in modo sicuro a soluzioni che risiedono all'esterno del
perimetro aziendale senza richiedere modifiche di notevole impatto
all'infrastruttura di rete aziendale. Tali servizi di Inoltro del bus di servizio sono comunque ospitati
all'interno dell'ambiente esistente, ma delegano l'ascolto delle
sessioni e delle richieste in ingresso al bus di servizio ospitato nel cloud. Il
bus di servizio usa inoltre Azure AD Access Control
per proteggere tali servizi da accessi non autorizzati.

### SCENARIO DELLA SOLUZIONE

In questa esercitazione si creerà un sito Web ASP.NET MVC 4 che
consentirà di visualizzare un elenco di prodotti nella pagina dell'inventario dei prodotti.

![][0]

Ai fini dell'esercitazione si presuppone che le informazioni sui prodotti siano già disponibili in un
sistema locale esistente e che per accedere a tale sistema venga usato
Inoltro del bus di servizio. Tale operazione viene simulata da un servizio Web in esecuzione in una semplice
applicazione console ed è supportata da un insieme di prodotti in memoria. Sarà
quindi possibile eseguire questa applicazione console nel computer in uso e
distribuire il ruolo Web in Azure. In tal modo sarà possibile osservare che il
ruolo Web in esecuzione nel data center di Azure verrà effettivamente chiamato
nel computer in uso, anche se quest'ultimo sarà quasi certamente
protetto da almeno un firewall e un livello
NAT (Network Address Translation).

Di seguito è riportata una schermata relativa alla pagina iniziale dell'applicazione Web completata.

![][1]

<h2>CONFIGURARE L'AMBIENTE DI SVILUPPO</h2>

Prima di iniziare a usare l'applicazione Azure, è necessario
ottenere gli strumenti idonei e configurare l'ambiente di sviluppo.

1.  Per installare Azure SDK per .NET, fare clic sul pulsante seguente:

    [Get Tools and SDK][]

2. 	Fare clic su **Installare l'SDK**.

3. 	Scegliere il collegamento relativo alla versione di Visual Studio in uso. Nelle procedure incluse in questa esercitazione viene usato Visual Studio 2013.

	![][42]

4.  Quando viene richiesto se eseguire o salvare **WindowsAzureSDKForNet.exe**, fare clic su 
    **Esegui**:

    ![][2]

5.  Nell'Installazione guidata piattaforma Web fare clic su **Installa** e procedere con l'installazione.

    ![][3]

6.  Al termine dell'installazione, saranno disponibili tutti gli strumenti
    necessari per avviare lo sviluppo. Nell'SDK sono disponibili gli strumenti che consentono di
    sviluppare con facilità applicazioni per Azure in Visual Studio. Se
    Visual Studio non è ancora installato, verrà installata anche la versione gratuita di
    Visual Studio Express.

<h2>CREARE UNO SPAZIO DEI NOMI SERVIZIO</h2>

Per iniziare a usare le funzionalità del bus di servizio in Azure, è prima di tutto necessario
creare uno spazio dei nomi servizio che fornisce un contenitore
di ambito per fare riferimento alle risorse del bus di servizio all'interno dell'applicazione. 

È possibile gestire gli spazi dei nomi e le entità di messaggistica del bus di servizio usando il [portale di gestione di Azure][] o Esplora server di Visual Studio, ma è possibile creare spazi dei nomi nuovi solo all'interno del portale.

###Per creare uno spazio dei nomi servizio tramite il portale:

1.  Accedere al [portale di gestione di Azure][].

2.  Nel pannello di navigazione sinistro del portale di gestione fare clic su 
    **Bus di servizio**.

3.  Nel riquadro inferiore del portale di gestione fare clic su **Crea**.   
    ![][5]

4.  Nella finestra di dialogo **Aggiungi un nuovo spazio dei nomi** immettere un nome per lo spazio dei nomi.
    Verrà effettuato immediatamente un controllo sulla disponibilità del nome.   
    ![][6]

5.  Dopo avere verificato che lo spazio dei nomi sia disponibile, scegliere il
    paese o l'area in cui dovrà essere ospitato. Assicurarsi
    di usare lo stesso paese/area in cui verranno distribuite le
    risorse di calcolo.

    IMPORTANTE: selezionare la **stessa area** che si intende scegliere per la
    distribuzione dell'applicazione. In questo modo sarà possibile ottenere prestazioni ottimali.

6.	Fare clic sul segno di spunta. A questo punto, lo spazio dei nomi servizio
    verrà creato e abilitato nel sistema. Potrebbero essere necessari alcuni minuti per consentire al sistema di
    effettuare il provisioning delle risorse per lo spazio dei nomi creato.

	![][38]

Lo spazio dei nomi creato verrà quindi visualizzato nel portale di gestione e sarà necessario
attendere qualche istante affinché venga attivato. Attendere che lo stato sia **Attivo** prima
di continuare.

<h2>RECUPERARE LE CREDENZIALI DI GESTIONE PREDEFINITE PER LO SPAZIO DEI NOMI</h2>

Per poter eseguire le operazioni di gestione, ad esempio creare una coda, nel
nuovo spazio dei nomi, è necessario ottenere le credenziali di gestione per lo
spazio dei nomi.

1.  Fare clic sullo spazio dei nomi servizio nella finestra principale.   

	![][39]

2.  Fare clic su **Informazioni di connessione**.   

	![][40]

3.  Nel riquadro **Accedi a informazioni di connessione** individuare le voci **Autorità di certificazione predefinita** e **Chiave predefinita**.   

4.  Prendere nota del valore della chiave oppure copiarlo negli Appunti.

###Gestire uno spazio dei nomi servizio tramite Esplora server di Visual Studio

Per gestire uno spazio dei nomi e ottenere le informazioni di connessione tramite Visual Studio anziché dal portale di gestione, attenersi alla procedura descritta [qui](http://http://msdn.microsoft.com/it-it/library/windowsazure/ff687127.aspx), nella sezione **Per connettersi ad Azure da Visual Studio**. Quando si accede ad Azure, il nodo **Service Bus** nell'albero **Microsoft Azure** in Esplora server viene automaticamente popolato con gli eventuali spazi dei nomi creati. Fare clic con il pulsante destro del mouse su uno spazio dei nomi, quindi scegliere **Proprietà** per visualizzare la stringa di connessione e altri metadati associati allo spazio dei nomi nel riquadro **Proprietà** di Visual Studio. 

![][44]

Prendere nota del valore **SharedAccessKey** oppure copiarlo negli Appunti.


<h2>CREARE UN SERVER LOCALE</h2>

In primo luogo, si creerà un sistema di catalogo prodotti locale fittizio. Si
tratta di un sistema locale abbastanza semplice che intende rappresentare
un catalogo prodotti effettivo che include una superficie completa di servizi
da integrare.

Questo progetto, avviato come applicazione console di Visual Studio, usa
il pacchetto NuGet del bus di servizio per includere le librerie e le impostazioni di configurazione
del bus di servizio. L'estensione NuGet di Visual Studio
semplifica l'installazione e l'aggiornamento di librerie e strumenti in Visual Studio e
in Visual Studio Express. Il pacchetto NuGet del bus di servizio è il modo
più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con
tutte le dipendenze di tale servizio. Per informazioni dettagliate su NuGet e
sul pacchetto del bus di servizio, vedere la pagina relativa all'[uso del pacchetto NuGet del bus di servizio][].

### CREARE IL PROGETTO

1.  Usando privilegi di amministratore, avviare Microsoft Visual
    Studio 2013 o Microsoft Visual Studio Express. Per
    avviare Visual Studio con privilegi di amministratore, fare clic con il pulsante destro del mouse su 
    **Microsoft Visual Studio 2013 (o Microsoft Visual Studio Express)**, quindi scegliere **Esegui come amministratore**.
2.  In Visual Studio, nel menu **File**, scegliere **Nuovo**, quindi
    fare clic su **Progetto**.

    ![][10]

3.  Da **Modelli installati**, in **Visual C#**, fare clic su **Applicazione
    console**. Nella casella **Nome** digitare il nome
    **ProductsServer**:

    ![][11]

4.  Fare clic su **OK** per creare il progetto **ProductsServer**.

5.  In **Esplora soluzioni**, fare clic con il pulsante destro del mouse su **ProductsServer**, quindi
    fare clic su **Proprietà**.
6.  Fare clic sulla scheda **Applicazione** sulla sinistra, quindi assicurarsi che nell'elenco a discesa 
    Framework di destinazione** sia presente **.NET**Framework 4** o **.NET Framework 4.5** . In caso contrario, selezionarlo dall'elenco a discesa e fare clic su **Sì**
     quando viene richiesto di ricaricare il progetto.

    ![][12]

7.  Se Gestione pacchetti NuGet per Visual Studio è già stato installato, continuare con il passaggio successivo. In caso contrario, visitare [NuGet][] e fare clic sull'opzione per [installare NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Seguire le istruzioni visualizzate per installare Gestione pacchetti NuGet, quindi riavviare Visual Studio.

7.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Riferiment**i, quindi fare clic su 
    **Gestisci pacchetti NuGet**...
8.  Nella colonna di sinistra della finestra di dialogo di NuGet fare clic su **Online**.

9. 	Nella colonna di destra fare clic sulla casella di **ricerca**, digitare "**WindowsAzure**" e selezionare l'elemento **Windows
    Azure Service Bus**. Fare clic su **Installa** per completare
    l'installazione, quindi chiudere questa finestra di dialogo.

    ![][13]

    I riferimenti agli assembly client necessari sono ora disponibili.

9.  Aggiungere una nuova classe per il contratto dei prodotti. In **Esplora soluzioni**,
    fare clic con il pulsante destro del mouse sul progetto **ProductsServer**, scegliere **Aggiungi** e quindi fare clic su
    **Classe**.

    ![][14]

10. Nella casella **Nome** digitare il nome **ProductsContract.cs**. Quindi 
    fare clic su **Aggiungi**.
11. In **ProductsContract.cs** sostituire la definizione dello spazio dei nomi con il
    codice seguente, che consente di definire il contratto per il servizio:

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

12. In Program.cs sostituire la definizione dello spazio dei nomi con il codice
    seguente, che consente di aggiungere il servizio profili e l'host relativo:

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

13. In **Esplora soluzioni** fare doppio clic sul file **app.config** per
    aprirlo nell'**editor di Visual Studio**. Sostituire il contenuto di
    **<system.ServiceModel>** con il codice XML seguente. Assicurarsi di
    sostituire *yourServiceNamespace* con il nome dello spazio
    dei nomi servizio e *yourIssuerSecret* con la chiave recuperata
    in precedenza dal portale di gestione di Azure:

        <system.serviceModel>
          <extensions>
             <behaviorExtensions>
                <add name="transportClientEndpointBehavior" type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </behaviorExtensions>
              <bindingExtensions>
                 <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
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
                       <sharedSecret issuerName="owner" issuerSecret="yourIssuerSecret" />
                    </tokenProvider>
                 </transportClientEndpointBehavior>
               </behavior>
             </endpointBehaviors>
          </behaviors>
        </system.serviceModel>

14. Premere **F6** oppure nel menu **Compila** scegliere **Compila soluzione** per compilare l'applicazione e verificare l'accuratezza di quanto creato finora.

<h2>CREARE UN'APPLICAZIONE ASP.NET MVC</h2>

In questa sezione si creerà una semplice applicazione ASP.NET per
visualizzare i dati recuperati dal servizio dei prodotti.

### CREARE IL PROGETTO

1.  Assicurarsi che Microsoft Visual Studio 2013 sia in esecuzione con privilegi di amministratore. In caso contrario, per
    avviare Visual Studio con privilegi di amministratore, fare clic con il pulsante destro del mouse su 
    **Microsoft Visual Studio 2013 (o Microsoft Visual Studio Express)**, quindi scegliere **Esegui come amministratore**. Per l'emulatore di calcolo di
    Azure, illustrato più avanti in questa guida, è necessario che
    Visual Studio sia avviato con privilegi di amministratore.

2.  In Visual Studio, nel menu **File**, scegliere **Nuovo**, quindi
    fare clic su **Progetto**.

3.  Da **Modelli installati**, in **Visual C#**, fare clic su **Applicazione Web ASP.NET**. Assegnare al progetto il nome **ProductsPortal**. Quindi 
    fare clic su **OK**.

    ![][15]

4.  Nell'elenco **Selezionare un modello** fare clic su **MVC**,
    quindi su **OK**.

    ![][16]

5.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Modelli**, quindi scegliere **Aggiungi** e
     infine **Classe**. Nella casella **Nome** digitare il nome 
    **Product.cs**. Fare quindi clic su **Aggiungi**.

    ![][17]

### MODIFICARE L'APPLICAZIONE WEB

1.  Nel file Product.cs in Visual Studio sostituire la definizione dello
    spazio dei nomi esistente con il codice seguente:

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

2.  Nel file HomeController.cs in Visual Studio sostituire la definizione dello
    spazio dei nomi esistente con il codice seguente:

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

3.  In **Esplora soluzioni**, espandere Views\Shared:

    ![][18]

4.  Fare quindi doppio clic su _Layout.cshtml per aprirlo nell'editor di Visual Studio.

5.  Modificare tutte le occorrenze di **My ASP.NET Application** in **LITWARE's Products**.

6. Rimuovere i collegamenti **Home**, **About** e **Contact**. Eliminare il codice evidenziato:

	![][41]

7.  In **Esplora soluzioni**, espandere Views\Home:

    ![][20]

8.  Fare doppio clic su Index.cshtml per aprirlo nell'editor di Visual Studio.
    Sostituire l'intero contenuto del file con il codice seguente:
	
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


9.  Per verificare l'accuratezza di quanto creato finora, premere **F6** o
    **Ctrl+MAIUSC+B** per compilare il progetto.


### ESEGUIRE L'APPLICAZIONE IN LOCALE

Eseguire l'applicazione per verificarne il funzionamento.

1.  Assicurarsi che il progetto attivo sia **ProductsPortal**. Fare clic con il pulsante destro del mouse
    sul nome del progetto in **Esplora soluzioni**, quindi scegliere **Imposta come
    progetto di avvio**
2.  In **Visual Studio**, premere **F5**.
3.  L'applicazione dovrebbe risultare in esecuzione in un browser:

    ![][21]

    <h2>PREPARAZIONE DELL'APPLICAZIONE PER LA DISTRIBUZIONE IN AZURE</h2>

    È possibile distribuire l'applicazione in un servizio cloud o un sito Web di Azure. Per altre informazioni sulle differenze tra siti Web e servizi cloud, vedere [Modelli di esecuzione di Azure][executionmodels]. Per informazioni su come distribuire l'applicazione in un sito Web di Azure, vedere [Distribuzione di un'applicazione Web ASP.NET in un sito Web di Azure](http://www.windowsazure.com/it-it/develop/net/tutorials/get-started/). In questa sezione è illustrata la procedura dettagliata per distribuire l'applicazione in un servizio cloud di Azure.

    Per distribuire l'applicazione a un servizio cloud, è necessario aggiungere alla soluzione un progetto di distribuzione del progetto servizio cloud.
    Il progetto di distribuzione contiene le informazioni
    di configurazione necessarie per la corretta esecuzione dell'applicazione nel 
    cloud.

    1.  Per rendere l'applicazione distribuibile nel cloud, fare clic con il pulsante destro del mouse sul progetto 
        ProductsPortal** in **Esplora soluzioni**, fare**clic su 
        Converti** e quindi su **Converti nel progetto di servizio cloud di Azure**.**

        ![][22]

    2.  Per testare l'applicazione, premere **F5**.
    3.  Verrà avviato l'emulatore di calcolo di Azure. L'emulatore
        usa il computer locale per emulare l'applicazione
        in esecuzione in Azure. Per verificare se l'emulatore è
        stato avviato, osservare la barra delle applicazioni:

        ![][23]

    4.  L'applicazione in esecuzione in locale sarà ancora visibile in un browser
        e l'aspetto e il funzionamento saranno analoghi a quelli di quando è stata
        eseguita come una normale applicazione ASP.NET MVC 4.

    <h2>COMBINAZIONE DEI VARI COMPONENTI</h2>

    Il passaggio successivo consiste nel collegare il server dei prodotti locale con
    l'applicazione ASP.NET MVC.

    1.  Se non è già aperto, in Visual Studio riaprire il progetto
        **ProductsPortal** creato nella sezione "Creazione di
        un'applicazione ASP.NET MVC".

    2.  Analogamente a quanto descritto nella sezione
        "Creazione di un server locale", aggiungere il pacchetto NuGet al progetto Riferimenti. In
        Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti**, quindi scegliere 
        **Gestisci pacchetti NuGet**.

    3.  Cercare "WindowsAzure.ServiceBus" e selezionare l'elemento **Windows
        Azure Service Bus**. Completare quindi l'installazione e
        chiudere la finestra di dialogo.

    4.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProductsPortal**
        , quindi scegliere **Aggiungi** e infine **Elemento esistente**.

    5.  Individuare il file **ProductsContract.cs** nel progetto console
        **ProductsServer**. Fare clic per evidenziare
        ProductsContract.cs, quindi fare clic sulla freccia giù accanto ad **Aggiungi** e infine
        scegliere **Aggiungi come collegamento**.

        ![][24]

    6.  Aprire il file **HomeController.cs** nell'editor di Visual Studio
        e sostituire la definizione dello spazio dei nomi con il
        codice seguente. Assicurarsi di sostituire *yourServiceNamespace* con il nome dello spazio
        dei nomi servizio e *yourIssuerSecret* con la chiave in uso.
        In tal modo il client potrà chiamare il servizio locale e 
        restituire il risultato della chiamata.

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
                            TokenProvider = TokenProvider.CreateSharedSecretTokenProvider(
                                "owner", "yourIssuerSecret") });
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

    7.  In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione **ProductsPortal**
        , quindi scegliere **Aggiungi** e infine **Progetto esistente**.

    8.  Individuare il progetto **ProductsServer**, quindi fare doppio clic
        sul file soluzione **ProductsServer.csproj** per aggiungerlo.

    9.  In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione **ProductsPortal**
         e scegliere **Proprietà**.

    10. Sul lato sinistro fare clic su **Progetto di avvio**. Sul
        lato destro fare clic su **Progetti di avvio multipli**. Assicurarsi che 
        **ProductsServer**, **ProductsPortal.Azure** e
        **ProductsPortal** siano visualizzati in quest'ordine e che l'azione **Avvia** sia impostata 
        per **ProductsServer** e **ProductsPortal.Azure**,
        e l'azione **Nessuna** sia impostata per **ProductsPortal**. Ad
        esempio:

        ![][25]

    11. Sempre nella finestra Proprietà fare clic su **Dipendenze progetto** sul
        lato sinistro.

    12. Nell'elenco a discesa **Progetti** fare clic su 
        **ProductsServer**. Assicurarsi che **ProductsPortal** sia deselezionato e
        che **ProductsPortal.Azure** sia selezionato. Quindi, fare clic su **OK**:

        ![][26]

    <h2>ESECUZIONE DELL'APPLICAZIONE</h2>

    1.  Nel menu **File** di Visual Studio fare clic su **Salva tutto**.

    2.  Premere **F5** per compilare ed eseguire l'applicazione. Il server
        locale, ovvero l'applicazione console **ProductsServer**, dovrebbe essere avviato
        per primo. Verrà quindi avviata l'applicazione **ProductsWeb** in una finestra
        del browser, come illustrato nella schermata seguente. Questa volta si
        noterà che nell'inventario dei prodotti sono elencati i dati recuperati
        dal sistema locale del servizio dei prodotti.

        ![][1]

    <h2>DISTRIBUIRE L'APPLICAZIONE IN AZURE</h2>

    1.  Fare clic con il pulsante destro del mouse sul progetto **ProductsPortal** in **Esplora
        soluzioni**, quindi scegliere **Pubblica in Azure**.

    2.  Per visualizzare tutte le sottoscrizioni, potrebbe essere necessario effettuare l'accesso.

        Fare clic su **Sign in to see more subscriptions**:

        ![][27]

    3.  Effettuare l'accesso con l'account Microsoft.


    8.  Fare clic su **Avanti**. Se la sottoscrizione non contiene già servizi
        ospitati, verrà chiesto di crearne uno. Il servizio ospitato
        funge da contenitore per l'applicazione all'interno della sottoscrizione di Microsoft
        Azure. Immettere un nome che identifichi
        l'applicazione e selezionare l'area per la quale ottimizzare
        l'applicazione. I tempi di caricamento saranno presumibilmente più rapidi per gli
        utenti che accedono dall'area specificata.

        ![][32]

    9.  Selezionare il servizio ospitato in cui pubblicare
        l'applicazione. Mantenere i valori predefiniti illustrati di seguito per le
        rimanenti impostazioni. Fare clic su **Avanti**:

        ![][33]

    10. Nell'ultima pagina fare clic su **Pubblica** per avviare il processo di
        distribuzione:

        ![][34]

        L'operazione richiederà all'incirca 5-7 minuti. Poiché si tratta della
        prima pubblicazione, in Azure verrà eseguito il provisioning di una
        macchina virtuale, verrà applicata la protezione avanzata, verrà creato un ruolo
        Web nella macchina virtuale in cui ospitare l'applicazione, verrà distribuito il codice a tale
        ruolo Web e infine verranno configurati il bilanciamento del carico e la
        rete per rendere l'applicazione disponibile al pubblico.

    11. Durante la pubblicazione è possibile monitorare le
        attività nella finestra **Log attività di Azure**, in genere
        ancorata alla parte inferiore della finestra di Visual Studio o di Visual Web
        Developer:

        ![][35]

    12. Al termine della distribuzione, per visualizzare il sito Web
        fare clic sul collegamento **URL sito Web** nella finestra di monitoraggio.

        ![][36]

        Il sito Web dipende dal server locale, pertanto, per il
        corretto funzionamento del sito Web è necessario eseguire l'applicazione **ProductsServer** in
        locale. Durante l'esecuzione di richieste sul sito
        Web del cloud è possibile notare la presenza di richieste in arrivo nell'applicazione
        console locale sono, come indicato dal messaggio "GetProducts called" visualizzato
        nella schermata seguente.

        ![][37]

Per altre informazioni sulle differenze tra siti Web e servizi cloud, vedere [Modelli di esecuzione di Azure][executionmodels].

<h2>ARRESTARE ED ELIMINARE L'APPLICAZIONE</h2>

Dopo aver distribuito l'applicazione, se necessario è possibile disabilitarla per
creare e distribuire altre applicazioni entro il termine delle 750
ore mensili (31 giorni al mese) gratuite di tempo del server.

Azure addebita le istanze del ruolo Web al consumo, in base all'utilizzo
di tempo del server su base oraria. Un'applicazione distribuita usa tempo del server
anche se le istanze non sono in esecuzione e sono in stato arrestato.
Un account gratuito include 750 ore mensili (31 giorni al mese) di tempo del server dedicato
delle macchine virtuali usate per l'hosting di queste istanze del ruolo Web.

Nella procedura seguente viene illustrato come arrestare ed eliminare
l'applicazione.

1.  Accedere al [portale di gestione di Azure],
        fare clic su Servizi cloud e quindi sul nome del servizio.

2.  Fare clic sulla scheda **Dashboard** e quindi su **Stop** per sospendere temporaneamente l'applicazione. Per
        riavviarla, è sufficiente fare clic su Avvia. Fare clic su **Elimina** per rimuovere completamente l'applicazione da Azure
        senza poterla più ripristinare.

	![][43]

<h2><a name="nextsteps"></a>Passaggi successivi</h2>  

Per altre informazioni sul bus di servizio, vedere le risorse seguenti:  
  
* [Service Bus di Azure][sbmsdn]  
* [Procedure relative al bus di servizio][sbwacom]  
* [Come usare le code del bus di servizio][sbwacomqhowto]  


  [0]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Get Tools and SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png
  
  
  [Portale di gestione di Azure]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png
  
  
  
  [Uso del pacchetto NuGet per Service Bus]: http://go.microsoft.com/fwlink/?LinkId=234589
  [10]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [11]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [12]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [13]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [15]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [18]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg
  
  [20]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [21]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [22]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [23]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [24]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png
  
  
  [30]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-36.png
  [31]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-37.png
  [32]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [33]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [34]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [35]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [36]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [39]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [40]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [41]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [42]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [43]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [44]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/VSProperties.png

  [sbmsdn]: http://msdn.microsoft.com/it-it/library/windowsazure/ee732537.aspx  
  [sbwacom]: /it-it/documentation/services/service-bus/  
  [sbwacomqhowto]: /it-it/develop/net/how-to-guides/service-bus-queues/
  [executionmodels]: http://www.windowsazure.com/it-it/develop/net/fundamentals/compute/

1.  Ensure that **ProductsPortal** is the active project. Right-click
    the project name in **Solution Explorer** and select **Set As
    Startup Project**
2.  Within **Visual Studio**, press **F5**.
3.  Your application should appear running in a browser:

    ![][21]

    <h2>MAKE YOUR APPLICATION READY TO DEPLOY TO AZURE</h2>

    You can deploy your application to an Azure Cloud Service or to an Azure Website. To learn more about the difference between websites and cloud services, see [Azure Execution Models][executionmodels]. To learn how to deploy the application to an Azure Website, see [Deploying an ASP.NET Web Application to an Azure Website](http://www.windowsazure.com/it-it/develop/net/tutorials/get-started/). This section contains detailed steps for deploying the application to an Azure Cloud Service.

    To deploy your application to a cloud service, you'll add a cloud service project deployment project to the solution.
    The deployment project contains configuration
    information that is needed to properly run your application in the
    cloud.

    1.  To make your application deployable to the cloud, right-click
        the **ProductsPortal** project in **Solution Explorer** and
        click **Convert**, then click **Convert to Azure Cloud Service Project**.

        ![][22]

    2.  To test your application, press **F5**.
    3.  This will start the Azure compute emulator. The compute
        emulator uses the local computer to emulate your application
        running in Azure. You can confirm the emulator has
        started by looking at the system tray:

        ![][23]

    4.  A browser will still display your application running locally,
        and it will look and function the same way it did when you ran
        it earlier as a regular ASP.NET MVC 4 application.

    <h2>PUT THE PIECES TOGETHER</h2>

    The next step is to hook up the on-premises products server with the
    ASP.NET MVC application.

    1.  If it is not already open, in Visual Studio re-open the
        **ProductsPortal** project you created in the "Creating an
        ASP.NET MVC Application" section.

    2.  Similar to the step in the "Create an On-Premises Server"
        section, add the NuGet package to the project References. In
        Solution Explorer, right-click **References**, then click
        **Manage NuGet Packages**.

    3.  Search for "WindowsAzure.ServiceBus" and select the **Windows
        Azure Service Bus** item. Then complete the installation and
        close this dialog.

    4.  In Solution Explorer, right-click the **ProductsPortal**
        project, then click **Add**, then **Existing Item**.

    5.  Navigate to the **ProductsContract.cs** file from the
        **ProductsServer** console project. Click to highlight
        ProductsContract.cs. Click the down arrow next to **Add**, then
        click **Add as Link**.

        ![][24]

    6.  Now open the **HomeController.cs** file in the Visual Studio
        editor and replace the namespace definition with the following
        code. Be sure to replace *yourServiceNamespace* with the name of
        your service namespace, and *yourIssuerSecret* with your key.
        This will enable the client to call the on-premises service,
        returning the result of the call.

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
                            TokenProvider = TokenProvider.CreateSharedSecretTokenProvider(
                                "owner", "yourIssuerSecret") });
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

    7.  In Solution Explorer, right-click on the **ProductsPortal**
        solution, click **Add**, then click **Existing Project**.

    8.  Navigate to the **ProductsServer** project, then double-click
        the **ProductsServer.csproj** solution file to add it.

    9.  In Solution Explorer, right-click the **ProductsPortal**
        solution and click **Properties**.

    10. On the left-hand side, click **Startup Project**. On the
        right-hand side, cick **Multiple startup projects**. Ensure that
        **ProductsServer**, **ProductsPortal.Azure**, and
        **ProductsPortal** appear, in that order, with **Start** set as
        the action for **ProductsServer** and **ProductsPortal.Azure**,
        and **None** set as the action for **ProductsPortal**. For
        example:

        ![][25]

    11. Still in the Properties dialog, click **ProjectDependencies** on
        the left-hand side.

    12. In the **Projects** dropdown, click
        **ProductsServer**. Ensure that **ProductsPortal** is unchecked,
        and **ProductsPortal.Azure** is checked. Then click **OK**:

        ![][26]

    <h2>RUN THE APPLICATION</h2>

    1.  From the **File** menu in Visual Studio, click **Save All**.

    2.  Press **F5** to build and run the application. The on-premises
        server (the **ProductsServer** console application) should start
        first, then the **ProductsWeb** application should start in a
        browser window, as shown in the screenshot below. This time, you
        will see that the product inventory lists data retrieved from
        the product service on-premises system.

        ![][1]

    <h2>DEPLOY YOUR APPLICATION TO AZURE</h2>

    1.  Right-click on the **ProductsPortal** project in **Solution
        Explorer** and click **Publish to Azure**.

    2.  You might have to sign in to see all your subscriptions.

        Click **Sign in to see more subscriptions**:

        ![][27]

    3.  Sign-in using your Microsoft Account.


    8.  Click **Next**. If your subscription doesn't already contain any hosted
        services, you will be asked to create one. The hosted service
        acts as a container for your application within your Windows
        Azure subscription. Enter a name that identifies your
        application and choose the region for which the application
        should be optimized. (You can expect faster loading times for
        users accessing it from this region.)

        ![][32]

    9.  Select the hosted service you would like to publish your
        application to. Keep the defaults as shown below for the
        remaining settings. Click **Next**:

        ![][33]

    10. On the last page, click **Publish** to start the deployment
        process:

        ![][34]

        This will take approximately 5-7 minutes. Since this is the
        first time you are publishing, Azure provisions a
        virtual machine (VM), performs security hardening, creates a Web
        role on the VM to host your application, deploys your code to
        that Web role, and finally configures the load balancer and
        networking so your application is available to the public.

    11. While publishing is in progress you will be able to monitor the
        activity in the **Azure Activity Log** window, which is
        typically docked to the bottom of Visual Studio or Visual Web
        Developer:

        ![][35]

    12. When deployment is complete, you can view your Website by
        clicking the **Website URL** link in the monitoring window.

        ![][36]

        Your Website depends on your on-premises server, so you must
        run the **ProductsServer** application locally for the Website
        to function properly. As you perform requests on the cloud Web
        site, you will see requests coming into your on-premises console
        application, as indicated by the "GetProducts called" output
        displayed in the screenshot below.

        ![][37]

To learn more about the difference between websites and cloud services, see [Azure Execution Models][executionmodels].

<h2>STOP AND DELETE YOUR APPLICATION</h2>

After deploying your application, you may want to disable it so you
can build and deploy other applications within the free 750
hours/month (31 days/month) of server time.

Azure bills web role instances per hour of server time
consumed. Server time is consumed once your application is deployed,
even if the instances are not running and are in the stopped state.
A free account includes 750 hours/month (31 days/month) of dedicated
virtual machine server time for hosting these web role instances.

The following steps show you how to stop and delete your
application.

1.  Login to the [Azure Management Portal],
        click on Cloud Services, then click the name of your service.

2.  Click the **Dashboard** tab, and then click on **Stop** to temporarily suspend your application. You will
        be able to start it again just by clicking on Start. Click **Delete** to completely remove your application from Azure
        with no ability to restore it.

	![][43]

<h2><a name="nextsteps"></a>Next steps</h2>  

To learn more about Service Bus, see the following resources:  
  
* [Azure Service Bus][sbmsdn]  
* [Service Bus How To's][sbwacom]  
* [How to Use Service Bus Queues][sbwacomqhowto]  


  [0]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Get Tools and SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png
  
  
  [Azure Management Portal]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png
  
  
  
  [Using the NuGet Service Bus Package]: http://go.microsoft.com/fwlink/?LinkId=234589
  [10]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [11]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [12]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [13]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [15]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [18]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg
  
  [20]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [21]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [22]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [23]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [24]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png
  
  
  [30]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-36.png
  [31]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-37.png
  [32]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [33]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [34]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [35]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [36]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [39]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [40]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [41]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [42]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [43]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [44]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/VSProperties.png

  [sbmsdn]: http://msdn.microsoft.com/it-it/library/windowsazure/ee732537.aspx  
  [sbwacom]: /it-it/documentation/services/service-bus/  
  [sbwacomqhowto]: /it-it/develop/net/how-to-guides/service-bus-queues/
  [executionmodels]: http://www.windowsazure.com/it-it/develop/net/fundamentals/compute/
