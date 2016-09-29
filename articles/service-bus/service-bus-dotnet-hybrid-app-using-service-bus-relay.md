<properties
	pageTitle="Applicazione ibrida cloud/locale (.NET) | Microsoft Azure"
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
	ms.date="09/16/2016"
	ms.author="sethm"/>

# Uso di Inoltro del bus di servizio di Azure per creare applicazioni ibride cloud/locali .NET

## Introduzione

Questo articolo descrive come compilare un'applicazione cloud ibrida con Microsoft Azure e Visual Studio. Nell'esercitazione si presuppone che l'utente non abbia mai usato Azure. In meno di 30 minuti si otterrà un'applicazione in esecuzione nel cloud e che usa più risorse di Azure.

Si acquisiranno le nozioni seguenti:

-   Creare o adattare un servizio Web esistente utilizzabile in una soluzione Web.
-   Usare il servizio Inoltro del bus di servizio di Azure per condividere dati tra un'applicazione Azure e un servizio Web ospitato altrove.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Vantaggi derivanti dall'uso di inoltro del bus di servizio con le soluzioni ibride

Le soluzioni aziendali sono in genere costituite da una combinazione di codice personalizzato appositamente scritto in risposta a specifici requisiti aziendali e da funzionalità esistenti fornite da soluzioni e sistemi già implementati.

Da qualche tempo gli architetti di soluzioni hanno iniziato a usare il cloud per semplificare la gestione dei requisiti di scalabilità e ridurre i costi operativi. Nell'adottare un tale approccio hanno quindi scoperto che le risorse di servizi esistenti che desiderano sfruttare come blocchi predefiniti delle soluzioni si trovano all'interno del firewall aziendale e quindi non sono facilmente raggiungibili dalla soluzione cloud. Molti servizi interni non sono creati né vengono ospitati in modo tale da consentirne una agevole esposizione al perimetro della rete aziendale.

Il servizio Inoltro del bus di servizio è progettato per i casi d'uso in cui servizi Web WCF (Windows Communication Foundation) vengono resi accessibili in modo sicuro a soluzioni che risiedono all'esterno del perimetro aziendale senza richiedere modifiche di notevole impatto all'infrastruttura di rete aziendale. Tali servizi di Inoltro del bus di servizio sono comunque ospitati all'interno dell'ambiente esistente, ma delegano l'ascolto delle sessioni e delle richieste in ingresso al bus di servizio ospitato nel cloud. Bus di servizio protegge questi servizi dall'accesso non autorizzato tramite l'autenticazione con [firma di accesso condiviso](service-bus-sas-overview.md).

## Scenario della soluzione

In questa esercitazione si creerà un sito Web ASP.NET che consente di visualizzare un elenco di prodotti nella pagina relativa all'inventario dei prodotti.

![][0]

Ai fini dell'esercitazione si presuppone che le informazioni sui prodotti siano già disponibili in un sistema locale esistente e che per accedere a tale sistema venga usato Inoltro del bus di servizio. Tale operazione viene simulata da un servizio Web eseguito in una semplice applicazione console ed è supportata da un insieme di prodotti in memoria. Sarà quindi possibile eseguire questa applicazione console nel computer in uso e distribuire il ruolo Web in Azure. In tal modo sarà possibile osservare che il ruolo Web in esecuzione nel data center di Azure verrà effettivamente chiamato nel computer in uso, anche se quest'ultimo sarà quasi certamente protetto da almeno un firewall e un livello NAT (Network Address Translation).

Di seguito è riportata una schermata relativa alla pagina iniziale dell'applicazione Web completata.

![][1]

## Configurare l'ambiente di sviluppo

Prima di iniziare a sviluppare applicazioni Azure, è necessario ottenere gli strumenti e configurare l'ambiente di sviluppo.

1.  Installare Azure SDK per .NET dalla pagina [Documentazione di Azure .NET][].

2. 	Fare clic su **Installa l’SDK** per la versione di Visual Studio in uso. Nelle procedure di questa esercitazione viene usato Visual Studio 2015.

4.  Quando viene richiesto se eseguire o salvare il file di installazione, fare clic su **Esegui**.

5.  Nell'**Installazione guidata piattaforma Web** fare clic su **Installa** e procedere con l'installazione.

6.  Al termine dell'installazione, saranno disponibili tutti gli strumenti necessari per avviare lo sviluppo dell’app. Nell'SDK sono disponibili gli strumenti che consentono di sviluppare con facilità applicazioni per Azure in Visual Studio. Se Visual Studio non è ancora installato, l'SDK installerà anche la versione gratuita di Visual Studio Express.

## Creare uno spazio dei nomi

Per iniziare a usare le funzionalità del bus di servizio in Azure, è innanzitutto necessario creare uno spazio dei nomi del servizio Uno spazio dei nomi fornisce un contenitore di ambito per fare riferimento alle risorse del bus di servizio all'interno dell'applicazione.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## Creare un server locale

In primo luogo, si creerà un sistema di catalogo prodotti locale fittizio. Si tratta di un sistema locale abbastanza semplice che intende rappresentare un catalogo prodotti effettivo che include una superficie completa di servizi da integrare.

Il progetto è un'applicazione console di Visual Studio e usa il [pacchetto NuGet del bus di servizio di Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) per includere le librerie e le impostazioni di configurazione del bus di servizio.

### Creare il progetto

1.  Usando privilegi di amministratore, avviare Microsoft Visual. Per avviare Visual Studio con privilegi di amministratore, fare clic con il pulsante destro del mouse sull'icona del programma **Visual Studio** e quindi scegliere **Esegui come amministratore**.

2.  In Visual Studio, nel menu **File** scegliere **Nuovo**, quindi fare clic su **Progetto**.

3.  Da **Modelli installati**, in **Visual C#**, fare clic su **Applicazione console**. Nella casella **Nome** digitare il nome **ProductsServer**:

    ![][11]

4.  Fare clic su **OK** per creare il progetto **ProductsServer**.

7.  Se Gestione pacchetti NuGet per Visual Studio è già stato installato, continuare con il passaggio successivo. In caso contrario, visitare [NuGet][] e fare clic sull'opzione per [installare NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Seguire le istruzioni visualizzate per installare Gestione pacchetti NuGet, quindi riavviare Visual Studio.

7.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProductsServer** e quindi scegliere **Gestisci pacchetti NuGet**.

8.  Fare clic sulla scheda **Sfoglia** e quindi cercare `Microsoft Azure Service Bus`. Fare clic su **Installa** e accettare le condizioni per l'utilizzo.

    ![][13]

    I riferimenti agli assembly client necessari sono ora disponibili.

9.  Aggiungere una nuova classe per il contratto dei prodotti. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProductsServer**, scegliere **Aggiungi** e quindi fare clic su **Classe**.

10. Nella casella **Nome** digitare il nome **ProductsContracts.cs**. Fare quindi clic su **Aggiungi**.

11. In **ProductsContract.cs** sostituire la definizione dello spazio dei nomi con il codice seguente, che consente di definire il contratto per il servizio.

	```
	namespace ProductsServer
	{
	    using System.Collections.Generic;
	    using System.Runtime.Serialization;
	    using System.ServiceModel;
	
	    // Define the data contract for the service
	    [DataContract]
	    // Declare the serializable properties.
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
	```

12. In Program.cs sostituire la definizione dello spazio dei nomi con il codice seguente, che consente di aggiungere il servizio profili e l'host relativo.

	```
	namespace ProductsServer
	{
	    using System;
	    using System.Linq;
	    using System.Collections.Generic;
	    using System.ServiceModel;
	
	    // Implement the IProducts interface.
	    class ProductsService : IProducts
	    {
	
	        // Populate array of products for display on website
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
	        // when the list of products is retrieved.
	        public IList<ProductData> GetProducts()
	        {
	            Console.WriteLine("GetProducts called.");
	            return products;
	        }
	
	    }
	
	    class Program
	    {
	        // Define the Main() function in the service application.
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
	```

13. In Esplora soluzioni fare doppio clic sul file **App.config** per aprirlo nell'editor di Visual Studio. Nella parte inferiore dell'elemento **&lt;system.ServiceModel&gt;**, ma sempre all'interno di &lt;system.ServiceModel&gt;, aggiungere il codice XML seguente. Assicurarsi di sostituire *yourServiceNamespace* con il nome del proprio spazio dei nomi e *yourKey* con la chiave di firma di accesso condiviso recuperata in precedenza dal portale:

    ```
    <system.serviceModel>
	...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
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
    ```
14. Nell'elemento **&lt;appSettings&gt;**, sempre nel file App.config, sostituire il valore della stringa di connessione con la stringa di connessione ottenuta in precedenza dal portale.

	```
	<appSettings>
   	<!-- Service Bus specific app settings for messaging connections -->
   	<add key="Microsoft.ServiceBus.ConnectionString"
	       value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
	</appSettings>
	```

14. Premere **CTRL+MAIUSC+B** oppure scegliere **Compila soluzione** dal menu **Compila** per compilare l'applicazione e verificare la correttezza del lavoro svolto finora.

## Creare un'applicazione ASP.NET

In questa sezione si creerà una semplice applicazione ASP.NET per visualizzare i dati recuperati dal servizio dei prodotti.

### Creare il progetto

1.  Assicurarsi che Visual Studio sia in esecuzione con privilegi di amministratore.

2.  In Visual Studio, nel menu **File** scegliere **Nuovo**, quindi fare clic su **Progetto**.

3.  Da **Modelli installati**, in **Visual C#**, fare clic su **Applicazione Web ASP.NET**. Assegnare al progetto il nome **ProductsPortal**. Fare quindi clic su **OK**.

    ![][15]

4.  Nell'elenco **Seleziona modello** fare clic su **MVC**.

6.  Selezionare la casella **Ospita nel cloud**.

    ![][16]

5. Fare clic sul pulsante **Modifica autenticazione**. Nella finestra di dialogo **Modifica autenticazione** fare clic su **Nessuna autenticazione** e quindi fare clic su **OK**. Per questa esercitazione si distribuisce un'applicazione che non richiede l'accesso utente.

	![][18]

6. 	Nella sezione **Microsoft Azure** della finestra di dialogo **Nuovo progetto ASP.NET** assicurarsi che sia selezionata la casella **Ospita nel cloud** e che nell'elenco a discesa sia selezionato **Servizio app**.

	![][19]

7. Fare clic su **OK**.

8. A questo punto è necessario configurare le risorse di Azure per una nuova app Web. Seguire tutti i passaggi nella sezione [Configurare le risorse di Azure per una nuova app Web](../app-service-web/web-sites-dotnet-get-started.md#configure-azure-resources-for-a-new-web-app). Tornare quindi a questa esercitazione e procedere al passaggio successivo.

5.  In Esplora soluzioni fare clic con il pulsante destro del mouse su **Modelli**, scegliere **Aggiungi** e infine fare clic su **Classe**. Nella casella **Nome** digitare il nome **Product.cs**. Fare quindi clic su **Aggiungi**.

    ![][17]

### Modificare l'applicazione web

1.  Nel file Product.cs in Visual Studio sostituire la definizione dello spazio dei nomi esistente con il codice seguente.

	```
	// Declare properties for the products inventory.
 	namespace ProductsWeb.Models
	{
    	public class Product
    	{
    	    public string Id { get; set; }
    	    public string Name { get; set; }
    	    public string Quantity { get; set; }
    	}
	}
	```

2.  In Esplora soluzioni espandere la cartella **Controller** e quindi fare doppio clic sul file **HomeController.cs** per aprirlo in Visual Studio.

3. In **HomeController.cs** sostituire la definizione dello spazio dei nomi esistente con il codice seguente.

	```
	namespace ProductsWeb.Controllers
	{
	    using System.Collections.Generic;
	    using System.Web.Mvc;
	    using Models;
	
	    public class HomeController : Controller
	    {
	        // Return a view of the products inventory.
	        public ActionResult Index(string Identifier, string ProductName)
	        {
	            var products = new List<Product>
	                {new Product {Id = Identifier, Name = ProductName}};
	            return View(products);
	        }
	     }
	}
	```

3.  In Esplora soluzioni espandere la cartella Views\\Shared e quindi fare doppio clic su **\_Layout.cshtml** per aprirlo nell'editor di Visual Studio.

5.  Modificare tutte le occorrenze di **My ASP.NET Application** in **LITWARE's Products**.

6. Rimuovere i collegamenti **Home**, **About** e **Contact**. Nell'esempio seguente, eliminare il codice evidenziato.

	![][41]

7.  In Esplora soluzioni espandere la cartella Views\\Home e quindi fare doppio clic su **Index.cshtml** per aprirlo nell'editor di Visual Studio. Sostituire l'intero contenuto del file con il codice seguente.

	```
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
	```

9.  Per verificare la correttezza del lavoro svolto finora, premere **CTRL+MAIUSC+B** per compilare il progetto.


### Eseguire l'app in locale

Eseguire l'applicazione per verificarne il funzionamento.

1.  Assicurarsi che il progetto attivo sia **ProductsPortal**. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, quindi scegliere **Imposta come progetto di avvio**.
2.  In Visual Studio premere F5.
3.  L'applicazione dovrebbe risultare in esecuzione in un browser.

    ![][21]

## Combinare i diversi componenti

Nel passaggio successivo si collegherà il server dei prodotti locale all'applicazione ASP.NET.

1.  Se non è già aperto, in Visual Studio riaprire il progetto **ProductsPortal** creato nella sezione [Creare un'applicazione ASP.NET](#create-an-aspnet-application).

2.  Analogamente a quanto descritto nella sezione "Creare un server locale", aggiungere il pacchetto NuGet ai riferimenti del progetto. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProductsPortal** e quindi scegliere **Gestisci pacchetti NuGet**.

3.  Cercare "Bus di servizio" e selezionare la voce **Bus di servizio di Microsoft Azure**. Completare quindi l'installazione e chiudere la finestra di dialogo.

4.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProductsPortal**, quindi scegliere **Aggiungi** e infine **Elemento esistente**.

5.  Individuare il file **ProductsContract.cs** nel progetto console **ProductsServer**. Fare clic per evidenziare ProductsContract.cs. Fare clic sulla freccia rivolta verso il basso accanto ad **Aggiungi**, quindi fare clic su **Aggiungi come collegamento**.

	![][24]

6.  Aprire il file **HomeController.cs** nell'editor di Visual Studio e sostituire la definizione dello spazio dei nomi con il codice seguente. Assicurarsi di sostituire *yourServiceNamespace* con il nome dello spazio dei nomi servizio e *yourKey* con la chiave SAS. In tal modo il client potrà chiamare il servizio locale e restituire il risultato della chiamata.

	```
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
	        // Declare the channel factory.
	        static ChannelFactory<IProductsChannel> channelFactory;
	
	        static HomeController()
	        {
	            // Create shared access signature token credentials for authentication.
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
	                // Return a view of the products inventory.
	                return this.View(from prod in channel.GetProducts()
	                                 select
	                                     new Product { Id = prod.Id, Name = prod.Name,
	                                         Quantity = prod.Quantity });
	            }
	        }
	    }
	}
	```

7.  In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione **ProductsPortal**. Verificare di fare clic con il pulsante destro del mouse sulla soluzione, non sul progetto. Scegliere **Aggiungi**, quindi fare clic su **Progetto esistente**.

8.  Individuare il progetto **ProductsServer**, quindi fare doppio clic sulla soluzione **ProductsServer.csproj** per aggiungerla.

9.  **ProductsServer** deve essere in esecuzione per visualizzare i dati in **ProductsPortal**. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione **ProductsPortal** e scegliere **Proprietà**. Verrà visualizzata la finestra di dialogo **Pagine delle proprietà**.

10. Sul lato sinistro fare clic su **Progetto di avvio**. Sul lato destro fare clic su **Progetti di avvio multipli**. Assicurarsi che **ProductsServer** e **ProductsPortal** siano visualizzati in questo ordine e che l'azione **Avvia** sia impostata per entrambi.

      ![][25]

11. Sempre nella finestra di dialogo **Proprietà** fare clic su **Dipendenze progetto** sul lato sinistro.

12. Nell'elenco **Projects** fare clic su **ProductsServer**. Verificare che **ProductsPortal** **non** sia selezionato.

14. Nell'elenco **Progetti** fare clic su **ProductsPortal**. Assicurarsi che **ProductsServer** sia selezionato.

    ![][26]

15. Fare clic su **OK** nella finestra di dialogo **Pagine delle proprietà**.

## Eseguire il progetto in locale

Per testare l'applicazione in locale, in Visual Studio premere **F5**. Il server locale, **ProductsServer**, verrà avviato per primo, quindi verrà avviata l'applicazione **ProductsPortal** in una finestra del browser. Questa volta si noterà che nell'inventario dei prodotti sono elencati i dati recuperati dal sistema locale del servizio dei prodotti.

![][10]

Fare clic su **Aggiorna** nella pagina **ProductsPortal**. Ogni volta che si aggiorna la pagina, nell'app server viene visualizzato un messaggio quando si chiama `GetProducts()` da **ProductsServer**.

Chiudere entrambe le applicazioni prima di procedere al passaggio successivo.

## Distribuire il progetto ProductsPortal in un'app Web di Azure

Nel passaggio successivo si convertirà il front-end **ProductsPortal** in un'app Web di Azure. Distribuire prima il progetto **ProductsPortal** seguendo tutti i passaggi nella sezione [Distribuire il progetto Web nell'app Web di Azure](../app-service-web/web-sites-dotnet-get-started.md#deploy-the-web-project-to-the-azure-web-app). Una volta completata la distribuzione, tornare a questa esercitazione e procedere al passaggio successivo.

> [AZURE.NOTE] È possibile che nella finestra del browser venga visualizzato un messaggio di errore quando il progetto Web **ProductsPortal** viene avviato automaticamente dopo la distribuzione. Si tratta di un comportamento previsto che si verifica perché l'applicazione **ProductsServer** non è ancora in esecuzione.

Copiare l'URL dell'app Web distribuita, perché sarà necessario nel passaggio successivo. È anche possibile ottenere l'URL dalla finestra Attività del servizio app di Azure in Visual Studio:

![][9]

### Impostare ProductsPortal come app Web

Prima di eseguire l'applicazione nel cloud è necessario assicurarsi che **ProductsPortal** venga avviato dall'interno di Visual Studio come app Web.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **ProjectsPortal** e quindi scegliere **Proprietà**.

3. Nella colonna sinistra fare clic su **Web**.

5. Nella sezione **Azione di avvio** fare clic sul pulsante **Avvia URL** e nella casella di testo immettere l'URL dell'app Web distribuita in precedenza, ad esempio `http://productsportal1234567890.azurewebsites.net/`.

	![][27]

6. Nel menu **File** di Visual Studio fare clic su **Salva tutto**.

7. In Visual Studio scegliere **Ricompila soluzione** dal menu Compila.

## Eseguire l'applicazione

2.  Premere F5 per compilare ed eseguire l'applicazione. Il server locale, ovvero l'applicazione console **ProductsServer**, verrà avviato per primo, quindi verrà avviata l'applicazione **ProductsPortal** in una finestra del browser, come illustrato nella schermata seguente. Si noterà di nuovo che nell'inventario dei prodotti sono elencati i dati recuperati dal sistema locale del servizio dei prodotti, che vengono visualizzati nell'app Web. Verificare l'URL per assicurarsi che **ProductsPortal** sia in esecuzione nel cloud come app Web di Azure.

    ![][1]

	> [AZURE.IMPORTANT] L'applicazione console **ProductsServer** deve essere in esecuzione e in grado di passare dati all'applicazione **ProductsPortal**. Se nel browser viene visualizzato un errore, attendere alcuni secondi che **ProductsServer** venga caricato e visualizzi il messaggio seguente. Fare quindi clic su **Aggiorna** nel browser.

	![][37]

3. Nel browser fare clic su **Aggiorna** nella pagina **ProductsPortal**. Ogni volta che si aggiorna la pagina, nell'app server viene visualizzato un messaggio quando si chiama `GetProducts()` da **ProductsServer**.

	![][38]

## Passaggi successivi  

Per ulteriori informazioni sul bus di servizio, vedere le risorse seguenti:

* [Bus di servizio di Azure][sbwacom]
* [Come usare le code del bus di servizio][sbwacomqhowto]


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Documentazione di Azure .NET]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
  [19]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-6.png
  [9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png
  
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png


  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: service-bus-dotnet-get-started-with-queues.md

<!---HONumber=AcomDC_0921_2016-->