---
title: Applicazione ibrida cloud/locale (.NET) | Microsoft Docs
description: Informazioni su come creare un&quot;applicazione ibrida cloud/locale (.NET) usando il servizio d&quot;inoltro WCF di Azure.
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/16/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 385eb87ec32f5f605b28cc8c76b1c89c7e90bfec
ms.openlocfilehash: 0288b0dda9139c28da28fedfe39c4e9156c6c938


---
# <a name="net-on-premisescloud-hybrid-application-using-azure-wcf-relay"></a>Applicazione ibrida cloud/locale (.NET) usando il servizio d'inoltro WCF di Azure
## <a name="introduction"></a>Introduzione
Questo articolo descrive come compilare un'applicazione cloud ibrida con Microsoft Azure e Visual Studio. Nell'esercitazione si presuppone che l'utente non abbia mai usato Azure. In meno di 30 minuti si otterrà un'applicazione in esecuzione nel cloud e che usa più risorse di Azure.

Si acquisiranno le nozioni seguenti:

* Creare o adattare un servizio Web esistente utilizzabile in una soluzione Web.
* Usare il servizio d'inoltro WCF di Azure per condividere dati tra un'applicazione Azure e un servizio Web ospitato altrove.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Vantaggi del servizio d'inoltro di Azure con soluzioni ibride
Le soluzioni aziendali sono in genere costituite da una combinazione di codice personalizzato appositamente scritto in risposta a specifici requisiti aziendali e da funzionalità esistenti fornite da soluzioni e sistemi già implementati.

Da qualche tempo gli architetti di soluzioni hanno iniziato a usare il cloud per semplificare la gestione dei requisiti di scalabilità e ridurre i costi operativi. Nell'adottare un tale approccio hanno quindi scoperto che le risorse di servizi esistenti che desiderano sfruttare come blocchi predefiniti delle soluzioni si trovano all'interno del firewall aziendale e quindi non sono facilmente raggiungibili dalla soluzione cloud. Molti servizi interni non sono creati né vengono ospitati in modo tale da consentirne una agevole esposizione al perimetro della rete aziendale.

Il servizio d'inoltro di Azure è progettato per i casi d'uso in cui i servizi Web WCF (Windows Communication Foundation) vengono resi accessibili in modo protetto a soluzioni che risiedono all'esterno del perimetro aziendale senza richiedere modifiche di notevole impatto all'infrastruttura di rete aziendale. Tali servizi d'inoltro sono comunque ospitati all'interno dell'ambiente esistente, ma delegano l'ascolto delle sessioni e delle richieste in ingresso al servizio d'inoltro ospitato nel cloud. Il servizio d'inoltro di Azure protegge anche quei servizi dall'accesso non autorizzato tramite l'autenticazione con [firma di accesso condiviso](../service-bus-messaging/service-bus-sas-overview.md).

## <a name="solution-scenario"></a>Scenario della soluzione
In questa esercitazione si creerà un sito Web ASP.NET che consente di visualizzare un elenco di prodotti nella pagina relativa all'inventario dei prodotti.

![][0]

In questa esercitazione si presuppone che le informazioni sui prodotti siano già disponibili in un sistema locale esistente e che per accedere a tale sistema venga usato il servizio d'inoltro di Azure. Tale operazione viene simulata da un servizio Web eseguito in una semplice applicazione console ed è supportata da un insieme di prodotti in memoria. Sarà quindi possibile eseguire questa applicazione console nel computer in uso e distribuire il ruolo Web in Azure. In tal modo sarà possibile osservare che il ruolo Web in esecuzione nel data center di Azure verrà effettivamente chiamato nel computer in uso, anche se quest'ultimo sarà quasi certamente protetto da almeno un firewall e un livello NAT (Network Address Translation).

Di seguito è riportata una schermata relativa alla pagina iniziale dell'applicazione Web completata.

![][1]

## <a name="set-up-the-development-environment"></a>Configurare l'ambiente di sviluppo
Prima di iniziare a sviluppare applicazioni Azure, è necessario ottenere gli strumenti e configurare l'ambiente di sviluppo.

1. Installare Azure SDK per .NET dalla pagina [Ottenere strumenti e SDK][Get Tools and SDK].
2. Fare clic su **Installare l'SDK** per la versione di Visual Studio in uso. Nelle procedure di questa esercitazione viene usato Visual Studio 2015.
3. Quando viene richiesto se eseguire o salvare il file di installazione, fare clic su **Esegui**.
4. Nell'**Installazione guidata piattaforma Web** fare clic su **Installa** e procedere con l'installazione.
5. Al termine dell'installazione, saranno disponibili tutti gli strumenti necessari per avviare lo sviluppo dell’app. Nell'SDK sono disponibili gli strumenti che consentono di sviluppare con facilità applicazioni per Azure in Visual Studio. Se Visual Studio non è ancora installato, l'SDK installerà anche la versione gratuita di Visual Studio Express.

## <a name="create-a-namespace"></a>Creare uno spazio dei nomi
Per usare le funzionalità del servizio d'inoltro di Azure, è prima necessario creare uno spazio dei nomi del servizio. Uno spazio dei nomi funge da contenitore di ambito in cui indirizzare le risorse di Azure nell'applicazione.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Creare un server locale
In primo luogo, si creerà un sistema di catalogo prodotti locale fittizio. Si tratta di un sistema locale abbastanza semplice che intende rappresentare un catalogo prodotti effettivo che include una superficie completa di servizi da integrare.

Il progetto è un'applicazione console di Visual Studio e usa il [pacchetto NuGet del bus di servizio di Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) per includere le librerie e le impostazioni di configurazione del bus di servizio.

### <a name="create-the-project"></a>Creare il progetto
1. Usando privilegi di amministratore, avviare Microsoft Visual. Per avviare Visual Studio con privilegi di amministratore, fare clic con il pulsante destro del mouse sull'icona del programma **Visual Studio** e quindi scegliere **Esegui come amministratore**.
2. In Visual Studio scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**.
3. Da **Modelli installati** in **Visual C#** fare clic su **Applicazione console**. Nella casella **Nome** digitare il nome **ProductsServer**:

   ![][11]
4. Fare clic su **OK** per creare il progetto **ProductsServer**.
5. Se Gestione pacchetti NuGet per Visual Studio è già stato installato, continuare con il passaggio successivo. In caso contrario, visitare il sito [NuGet][NuGet] e fare clic su [Install NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) (Installa NuGet). Seguire le istruzioni visualizzate per installare Gestione pacchetti NuGet, quindi riavviare Visual Studio.
6. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProductsServer** e quindi scegliere **Gestisci pacchetti NuGet**.
7. Fare clic sulla scheda **Sfoglia** e quindi cercare `Microsoft Azure Service Bus`. Fare clic su **Installa**e accettare le condizioni per l'utilizzo.

   ![][13]

   I riferimenti agli assembly client necessari sono ora disponibili.
8. Aggiungere una nuova classe per il contratto dei prodotti. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProductsServer**, scegliere **Aggiungi** e quindi fare clic su **Classe**.
9. Nella casella **Nome** digitare il nome **ProductsContract.cs**. Fare quindi clic su **Aggiungi**.
10. In **ProductsContract.cs** sostituire la definizione dello spazio dei nomi con il codice seguente, che consente di definire il contratto per il servizio.

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
11. In Program.cs sostituire la definizione dello spazio dei nomi con il codice seguente, che consente di aggiungere il servizio profili e l'host relativo.

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
12. In Esplora soluzioni fare doppio clic sul file **App.config** per aprirlo nell'editor di Visual Studio. Nella parte inferiore dell'elemento **&lt;system.ServiceModel&gt;**, ma sempre all'interno di &lt;system.ServiceModel&gt;, aggiungere il codice XML seguente. Assicurarsi di sostituire *yourServiceNamespace* con il nome del proprio spazio dei nomi e *yourKey* con la chiave di firma di accesso condiviso recuperata in precedenza dal portale:

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
13. Nell'elemento **&lt;appSettings&gt;**sempre nel file App.config, sostituire il valore della stringa di connessione con la stringa di connessione ottenuta in precedenza dal portale.

    ```
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```
14. Premere **CTRL+MAIUSC+B** oppure scegliere **Compila soluzione** dal menu **Compila** per compilare l'applicazione e verificare la correttezza del lavoro svolto finora.

## <a name="create-an-aspnet-application"></a>Creare un'applicazione ASP.NET
In questa sezione si creerà una semplice applicazione ASP.NET per visualizzare i dati recuperati dal servizio dei prodotti.

### <a name="create-the-project"></a>Creare il progetto
1. Assicurarsi che Visual Studio sia in esecuzione con privilegi di amministratore.
2. In Visual Studio scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**.
3. Da **Modelli installati** in **Visual C#** fare clic su **Applicazione Web ASP.NET**. Assegnare al progetto il nome **ProductsPortal**. Fare quindi clic su **OK**.

   ![][15]
4. Nell'elenco **Seleziona modello** fare clic su **MVC**.
5. Selezionare la casella **Ospita nel cloud**.

   ![][16]
6. Fare clic sul pulsante **Modifica autenticazione**. Nella finestra di dialogo **Modifica autenticazione** fare clic su **Nessuna autenticazione**, quindi fare clic su **OK**. Per questa esercitazione si distribuisce un'applicazione che non richiede l'accesso utente.

    ![][18]
7. Nella sezione **Microsoft Azure** della finestra di dialogo **Nuovo progetto ASP.NET** assicurarsi che sia selezionata la casella **Ospita nel cloud** e che nell'elenco a discesa sia selezionato **Servizio app**.

   ![][19]
8. Fare clic su **OK**.
9. A questo punto è necessario configurare le risorse di Azure per una nuova app Web. Seguire tutti i passaggi in [Creare un'applicazione Web](../app-service-web/web-sites-dotnet-get-started.md#create-a-web-application) e [Creare le risorse di Azure](../app-service-web/web-sites-dotnet-get-started.md#create-the-azure-resources). Tornare quindi a questa esercitazione e procedere al passaggio successivo.
10. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Modelli**, scegliere **Aggiungi** e infine fare clic su **Classe**. Nella casella **Nome** digitare il nome **Product.cs**. Fare quindi clic su **Aggiungi**.

    ![][17]

### <a name="modify-the-web-application"></a>Modificare l'applicazione web
1. Nel file Product.cs in Visual Studio sostituire la definizione dello spazio dei nomi esistente con il codice seguente.

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
2. In Esplora soluzioni espandere la cartella **Controller** e quindi fare doppio clic sul file **HomeController.cs** per aprirlo in Visual Studio.
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
4. In Esplora soluzioni espandere la cartella Views\Shared e quindi fare doppio clic su **_Layout.cshtml** per aprirlo nell'editor di Visual Studio.
5. Modificare tutte le occorrenze di **My ASP.NET Application** in **LITWARE's Products**.
6. Rimuovere i collegamenti **Home**, **About** e **Contact**. Nell'esempio seguente, eliminare il codice evidenziato.

    ![][41]
7. In Esplora soluzioni espandere la cartella Views\Home e quindi fare doppio clic su **Index.cshtml** per aprirlo nell'editor di Visual Studio.
   Sostituire l'intero contenuto del file con il codice seguente.

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
8. Per verificare la correttezza del lavoro svolto finora, premere **CTRL+MAIUSC+B** per compilare il progetto.

### <a name="run-the-app-locally"></a>Eseguire l'app in locale
Eseguire l'applicazione per verificarne il funzionamento.

1. Assicurarsi che il progetto attivo sia **ProductsPortal**. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, quindi scegliere **Imposta come progetto di avvio**.
2. In Visual Studio premere F5.
3. L'applicazione dovrebbe risultare in esecuzione in un browser.

   ![][21]

## <a name="put-the-pieces-together"></a>Combinare i diversi componenti
Nel passaggio successivo si collegherà il server dei prodotti locale all'applicazione ASP.NET.

1. Se non è già aperto, in Visual Studio riaprire il progetto **ProductsPortal** creato nella sezione [Creare un'applicazione ASP.NET](#create-an-aspnet-application).
2. Analogamente a quanto descritto nella sezione "Creare un server locale", aggiungere il pacchetto NuGet ai riferimenti del progetto. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProductsPortal** e quindi scegliere **Gestisci pacchetti NuGet**.
3. Cercare "Bus di servizio" e selezionare la voce **Bus di servizio di Microsoft Azure** . Completare quindi l'installazione e chiudere la finestra di dialogo.
4. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProductsPortal**, quindi scegliere **Aggiungi** e infine **Elemento esistente**.
5. Individuare il file **ProductsContract.cs** nel progetto console **ProductsServer**. Fare clic per evidenziare ProductsContract.cs. Fare clic sulla freccia rivolta verso il basso accanto ad **Aggiungi**, quindi fare clic su **Aggiungi come collegamento**.

   ![][24]
6. Aprire il file **HomeController.cs** nell'editor di Visual Studio e sostituire la definizione dello spazio dei nomi con il codice seguente. Assicurarsi di sostituire *yourServiceNamespace* con il nome dello spazio dei nomi servizio e *yourKey* con la chiave di firma di accesso condiviso. In tal modo il client potrà chiamare il servizio locale e restituire il risultato della chiamata.

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
7. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione **ProductsPortal**. Verificare di fare clic con il pulsante destro del mouse sulla soluzione, non sul progetto. Scegliere **Aggiungi**, quindi fare clic su **Progetto esistente**.
8. Individuare il progetto **ProductsServer**, quindi fare doppio clic sulla soluzione **ProductsServer.csproj** per aggiungerla.
9. **ProductsServer** deve essere in esecuzione per visualizzare i dati in **ProductsPortal**. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione **ProductsPortal** e scegliere **Proprietà**. Viene visualizzata la finestra di dialogo **Pagine delle proprietà**.
10. Sul lato sinistro fare clic su **Progetto di avvio**. Sul lato destro fare clic su **Progetti di avvio multipli**. Assicurarsi che **ProductsServer** e **ProductsPortal** siano visualizzati in questo ordine e che l'azione **Avvia** sia impostata per entrambi.

      ![][25]
11. Sempre nella finestra di dialogo **Proprietà** fare clic su **Dipendenze progetto** sul lato sinistro.
12. Nell'elenco **Progetti** fare clic su **ProductsServer**. Verificare che **ProductsPortal** **non** sia selezionato.
13. Nell'elenco **Progetti** fare clic su **ProductsPortal**. Assicurarsi che **ProductsServer** sia selezionato.

    ![][26]
14. Fare clic su **OK** nella finestra di dialogo **Pagine delle proprietà**.

## <a name="run-the-project-locally"></a>Eseguire il progetto in locale
Per testare l'applicazione in locale, in Visual Studio premere **F5**. Il server locale, **ProductsServer**, verrà avviato per primo, quindi verrà avviata l'applicazione **ProductsPortal** in una finestra del browser. Questa volta si noterà che nell'inventario dei prodotti sono elencati i dati recuperati dal sistema locale del servizio dei prodotti.

![][10]

Fare clic su **Aggiorna** nella pagina **ProductsPortal**. Ogni volta che si aggiorna la pagina, nell'app server viene visualizzato un messaggio quando si chiama `GetProducts()` da **ProductsServer**.

Chiudere entrambe le applicazioni prima di procedere al passaggio successivo.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Distribuire il progetto ProductsPortal in un'app Web di Azure
Nel passaggio successivo si convertirà il front-end **ProductsPortal** in un'app Web di Azure. Distribuire prima di tutto il progetto **ProductsPortal** seguendo tutti i passaggi nella sezione [Distribuire il progetto Web in Azure](../app-service-web/web-sites-dotnet-get-started.md#deploy-the-web-project-to-azure). Una volta completata la distribuzione, tornare a questa esercitazione e procedere al passaggio successivo.

> [!NOTE]
> È possibile che nella finestra del browser venga visualizzato un messaggio di errore quando il progetto Web **ProductsPortal** viene avviato automaticamente dopo la distribuzione. Si tratta di un comportamento previsto che si verifica perché l'applicazione **ProductsServer** non è ancora in esecuzione.
>
>

Copiare l'URL dell'app Web distribuita, perché sarà necessario nel passaggio successivo. È anche possibile ottenere l'URL dalla finestra Attività del servizio app di Azure in Visual Studio:

![][9]

### <a name="set-productsportal-as-web-app"></a>Impostare ProductsPortal come app Web
Prima di eseguire l'applicazione nel cloud è necessario assicurarsi che **ProductsPortal** venga avviato dall'interno di Visual Studio come app Web.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **ProjectsPortal** e quindi scegliere **Proprietà**.
2. Nella colonna sinistra fare clic su **Web**.
3. Nella sezione **Azione di avvio** fare clic sul pulsante **URL di avvio** e nella casella di testo immettere l'URL dell'app Web distribuita in precedenza, ad esempio, `http://productsportal1234567890.azurewebsites.net/`.

    ![][27]
4. Scegliere **Salva tutto** dal menu **File** in Visual Studio.
5. In Visual Studio scegliere **Ricompila soluzione** dal menu Compila.

## <a name="run-the-application"></a>Eseguire l'applicazione
1. Premere F5 per compilare ed eseguire l'applicazione. Il server locale, ovvero l'applicazione console **ProductsServer**, verrà avviato per primo, quindi verrà avviata l'applicazione **ProductsPortal** in una finestra del browser, come illustrato nella schermata seguente. Si noterà di nuovo che nell'inventario dei prodotti sono elencati i dati recuperati dal sistema locale del servizio dei prodotti, che vengono visualizzati nell'app Web. Verificare l'URL per assicurarsi che **ProductsPortal** sia in esecuzione nel cloud come app Web di Azure.

   ![][1]

   > [!IMPORTANT]
   > L'applicazione console **ProductsServer** deve essere in esecuzione e in grado di passare dati all'applicazione **ProductsPortal**. Se nel browser viene visualizzato un errore, attendere alcuni secondi che **ProductsServer** venga caricato e visualizzi il messaggio seguente. Fare quindi clic su **Aggiorna** nel browser.
   >
   >

   ![][37]
2. Nel browser fare clic su **Aggiorna** nella pagina **ProductsPortal**. Ogni volta che si aggiorna la pagina, nell'app server viene visualizzato un messaggio quando si chiama `GetProducts()` da **ProductsServer**.

    ![][38]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul servizio d'inoltro di Azure, vedere le risorse seguenti:  

* [Che cos'è il servizio di inoltro di Azure?](relay-what-is-it.md)  
* [Come usare l'inoltro](service-bus-dotnet-how-to-use-relay.md)  

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[Get Tools and SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
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



<!--HONumber=Jan17_HO1-->


