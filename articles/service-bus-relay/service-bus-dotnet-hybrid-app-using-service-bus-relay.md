---
title: Azure Windows Communication Foundation (WCF) inoltro di applicazioni ibride locali/cloud (.NET) | Microsoft Docs
description: Informazioni su come esporre un servizio WCF locale a un'applicazione Web nel cloud usando Inoltro di Azure
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: spelluru
ms.openlocfilehash: b86d535e4cbc275b3ee777d7c70146f7711c502c
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212930"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Esporre un servizio WCF locale a un'applicazione Web nel cloud usando Inoltro di Azure

Questo articolo illustra come compilare un'applicazione cloud ibrida con Microsoft Azure e Visual Studio. Si crea un'applicazione che usa più risorse di Azure nel cloud. Questa esercitazione consente di imparare:

* Creare o adattare un servizio Web esistente utilizzabile in una soluzione Web.
* Come usare il servizio di inoltro di Azure Windows Communication Foundation (WCF) per condividere dati tra un'applicazione Azure e un servizio Web ospitato altrove.

In questa esercitazione vengono eseguite le attività seguenti:

> [!div class="checklist"]
>
> * Installare i prerequisiti per questa esercitazione.
> * Esaminare lo scenario
> * Creare uno spazio dei nomi
> * Creare un server locale.
> * Creare un'applicazione ASP .NET.
> * Eseguire l'app in locale.
> * Distribuire l'app Web in Azure.
> * Eseguire l'app in Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Visual Studio 2015 o versione successiva](https://www.visualstudio.com). Gli esempi in questa esercitazione usano Visual Studio 2019.
* Azure SDK per .NET. Installare l'SDK dalla [pagina Download per gli SDK](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Vantaggi del servizio d'inoltro di Azure con soluzioni ibride

Le soluzioni aziendali sono in genere costituite da una combinazione di codice personalizzato e funzionalità esistente. Il codice personalizzato affronta i requisiti aziendali nuovi e univoci. Le soluzioni e i sistemi già disponibili forniscono funzionalità esistenti.

Da qualche tempo gli architetti di soluzioni hanno iniziato a usare il cloud per semplificare la gestione dei requisiti di scalabilità e ridurre i costi operativi. In questo modo, scoprono che gli asset di servizio esistenti da usare come blocchi predefiniti per le proprie soluzioni si trovano all'interno del firewall aziendale e sono facilmente raggiungibili dalla soluzione cloud. Molti servizi interni non sono compilati o ospitati in modo che possano essere facilmente esposti al perimetro della rete aziendale.

Il servizio di [inoltro di Azure](https://azure.microsoft.com/services/service-bus/) accetta i servizi Web WCF esistenti e rende tali servizi accessibili in modo sicuro alle soluzioni esterne al perimetro aziendale senza richiedere modifiche intrusive all'infrastruttura di rete aziendale. Tali servizi d'inoltro sono comunque ospitati all'interno dell'ambiente esistente, ma delegano l'ascolto delle sessioni e delle richieste in ingresso al servizio d'inoltro ospitato nel cloud. Il servizio d'inoltro di Azure protegge anche quei servizi dall'accesso non autorizzato tramite l'autenticazione con [firma di accesso condiviso](../service-bus-messaging/service-bus-sas.md).

## <a name="review-the-scenario"></a>Esaminare lo scenario

In questa esercitazione viene creato un sito Web ASP.NET che consente di visualizzare un elenco di prodotti nella pagina relativa all'inventario dei prodotti.

![Scenario][0]

In questa esercitazione si presuppone che le informazioni sui prodotti siano già disponibili in un sistema locale esistente e che per accedere a tale sistema venga usato il servizio d'inoltro di Azure. Un servizio Web che viene eseguito in una semplice applicazione console simula questa situazione. Contiene un set di prodotti in memoria. È possibile eseguire questa applicazione console nel computer in uso e distribuire il ruolo Web in Azure. In questo modo, si vedrà come il ruolo Web in esecuzione nel Data Center di Azure chiama il computer. Questa chiamata si verifica anche se il computer è quasi certamente dietro almeno un firewall e un livello Network Address Translation (NAT).

## <a name="set-up-the-development-environment"></a>Configurare l'ambiente di sviluppo

Prima di iniziare a sviluppare applicazioni Azure, è necessario scaricare gli strumenti e configurare l'ambiente di sviluppo:

1. Installare Azure SDK per .NET dalla [pagina di download](https://azure.microsoft.com/downloads/) di SDK.
1. Nella colonna **.NET** scegliere la versione di [Visual Studio](https://www.visualstudio.com) che si sta usando. Questa esercitazione usa Visual Studio 2019.
1. Quando viene richiesto di eseguire o salvare il programma di installazione, selezionare **Esegui**.
1. Nella finestra di dialogo **installazione guidata piattaforma Web** selezionare **Installa** e continua con l'installazione.

Al termine dell'installazione, si avranno tutti gli elementi necessari per iniziare a sviluppare l'app. Nell'SDK sono disponibili gli strumenti che consentono di sviluppare con facilità applicazioni per Azure in Visual Studio.

## <a name="create-a-namespace"></a>Crea uno spazio dei nomi

Il primo passaggio consiste nel creare uno spazio dei nomi e nell'ottenere una chiave di [firma di accesso condiviso](../service-bus-messaging/service-bus-sas.md). Uno spazio dei nomi fornisce un limite per ogni applicazione esposta tramite il servizio di inoltro. Una chiave SAS viene generata automaticamente dal sistema quando viene creato uno spazio dei nomi del servizio. La combinazione di spazio dei nomi servizio e chiave di firma di accesso condiviso fornisce le credenziali che consentono ad Azure di autenticare l'accesso a un'applicazione.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Creare un server locale

In primo luogo, viene creato un sistema di catalogo prodotti locale fittizio.  Il progetto è un'applicazione console di Visual Studio e usa il [pacchetto NuGet del bus di servizio di Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) per includere le librerie e le impostazioni di configurazione del bus di servizio. <a name="create-the-project"></a>

1. Avviare Microsoft Visual Studio come amministratore. A tale scopo, fare clic con il pulsante destro del mouse sull'icona del programma Visual Studio e scegliere **Esegui come amministratore**.
1. In Visual Studio selezionare **Crea un nuovo progetto**.
1. In **Crea un nuovo progetto**selezionare **App Console (.NET Framework)** per e C# fare clic su **Avanti**.
1. Assegnare al progetto il nome *ProductsServer* e selezionare **Crea**.

   ![Configurare il nuovo progetto][11]

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **ProductsServer** , quindi scegliere **Gestisci pacchetti NuGet**.
1. Selezionare **Sfoglia**, quindi cercare e scegliere **WindowsAzure. ServiceBus**. Selezionare **Installa**e accettare le condizioni per l'utilizzo.

   ![Selezionare il pacchetto NuGet][13]

   I riferimenti agli assembly client necessari sono ora disponibili.

1. Aggiungere una nuova classe per il contratto dei prodotti. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **ProductsServer** e scegliere **Aggiungi** > **classe**.
1. In **nome**immettere il nome *ProductsContract.cs* e selezionare **Aggiungi**.

Apportare le seguenti modifiche al codice alla soluzione:

1. In *ProductsContract.cs* sostituire la definizione dello spazio dei nomi con il codice seguente, che consente di definire il contratto per il servizio.

    ```csharp
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

1. In *Program.cs*sostituire la definizione dello spazio dei nomi con il codice seguente, che aggiunge il servizio profili e l'host.

    ```csharp
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

1. In **Esplora soluzioni**fare doppio clic su **app. config** per aprire il file nell'editor di Visual Studio. Nella parte inferiore dell' `<system.ServiceModel>` elemento, ma sempre all'interno `<system.ServiceModel>`di, aggiungere il codice XML seguente. Assicurarsi di sostituire `yourServiceNamespace` con il nome dello spazio dei nomi e `yourKey` con la chiave SAS recuperata in precedenza dal portale:

    ```xml
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

    > [!NOTE]
    > L'errore causato da `transportClientEndpointBehavior` è solo un avviso e non è un problema di blocco per questo esempio.

1. Sempre in *app. config*, nell' `<appSettings>` elemento, sostituire il valore della stringa di connessione con la stringa di connessione ottenuta in precedenza dal portale.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Premere CTRL + MAIUSC + B oppure selezionare **Compila** > **Compila soluzione** per compilare l'applicazione e verificare l'accuratezza del lavoro fino a questo punto.

## <a name="create-an-aspnet-application"></a>Creare un'applicazione ASP.NET

In questa sezione viene creata una semplice applicazione ASP.NET per visualizzare i dati recuperati dal servizio dei prodotti.

### <a name="create-the-project"></a>Creare il progetto

1. Assicurarsi che Visual Studio sia in esecuzione come amministratore.
1. In Visual Studio selezionare **Crea un nuovo progetto**.
1. In **Crea un nuovo progetto**selezionare **applicazione Web ASP.NET (.NET Framework)** per e C# fare clic su **Avanti**.
1. Assegnare al progetto il nome *ProductsPortal* e selezionare **Crea**.
1. In **creare una nuova applicazione Web ASP.NET**scegliere **MVC** e selezionare **modifica** in **autenticazione**.

   ![Selezionare l'applicazione Web ASP.NET][16]

1. In **Modifica autenticazione**scegliere **Nessuna autenticazione** e quindi fare clic su **OK**. Per questa esercitazione si distribuisce un'app che non richiede l'accesso dell'utente.

    ![Specificare l'autenticazione][18]

1. Tornare a **creare una nuova applicazione Web ASP.NET**e selezionare **Crea** per creare l'app MVC.
1. Configurare le risorse di Azure per una nuova app Web. Seguire i passaggi in [pubblicare l'app Web](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard). Tornare quindi a questa esercitazione e continuare con il passaggio successivo.
1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse su **modelli** , quindi scegliere **Aggiungi** > **classe**.
1. Assegnare alla classe il nome *Product.cs*, quindi selezionare **Aggiungi**.

    ![Creare il modello Product][17]

### <a name="modify-the-web-application"></a>Modificare l'applicazione web

1. Nel file *Product.cs* in Visual Studio sostituire la definizione dello spazio dei nomi esistente con il codice seguente:

   ```csharp
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

1. In **Esplora soluzioni**espandere **controller**, quindi fare doppio clic su **HomeController.cs** per aprire il file in Visual Studio.
1. In *HomeController.cs* sostituire la definizione dello spazio dei nomi esistente con il codice seguente:

    ```csharp
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

1. In **Esplora soluzioni**espandere **viste** > **condivise**, quindi fare doppio clic su **file. cshtml** per aprire il file nell'editor di Visual Studio.
1. Modificare tutte le occorrenze di `My ASP.NET Application` in *Northwind Traders Products*.
1. Rimuovere i `Home`collegamenti `About`, e `Contact` . Nell'esempio seguente, eliminare il codice evidenziato.

    ![Eliminare gli elementi elenco generati][41]

1. In **Esplora soluzioni**espandere **views** > **Home**, quindi fare doppio clic su **index. cshtml** per aprire il file nell'editor di Visual Studio. Sostituire l'intero contenuto del file con il codice seguente:

   ```html
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

1. Per verificare l'accuratezza del lavoro fino a questo momento, è possibile premere CTRL + MAIUSC + B per compilare il progetto.

### <a name="run-the-app-locally"></a>Eseguire l'app in locale

Eseguire l'applicazione per verificarne il funzionamento.

1. Assicurarsi che il progetto attivo sia **ProductsPortal**. Fare clic con il pulsante destro del mouse sul nome del progetto in **Esplora soluzioni** e selezionare **Imposta come progetto di avvio**.
1. In Visual Studio selezionare F5.

L'applicazione dovrebbe risultare in esecuzione in un browser.

![Applicazione Web][21]

## <a name="put-the-pieces-together"></a>Combinare i diversi componenti

Nel passaggio successivo si collegherà il server dei prodotti locale all'applicazione ASP.NET.

1. Se non è già aperto, in Visual Studio aprire il progetto **ProductsPortal** creato nella sezione [creare un'applicazione ASP.NET](#create-an-aspnet-application) .
1. Analogamente al passaggio nella sezione [creare un server locale](#create-an-on-premises-server) , aggiungere il pacchetto NuGet ai riferimenti del progetto. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **ProductsPortal** , quindi scegliere **Gestisci pacchetti NuGet**.
1. Cercare *WindowsAzure.ServiceBus* e selezionare l'elemento **WindowsAzure.ServiceBus**. Completare quindi l'installazione e chiudere la finestra di dialogo.
1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **ProductsPortal** , quindi scegliere **Aggiungi** > **elemento esistente**.
1. Individuare il file *ProductsContract.cs* nel progetto console **ProductsServer**. Evidenziare *ProductsContract.cs*. Selezionare la freccia rivolta verso il basso accanto a **Aggiungi**, quindi scegliere **Aggiungi come collegamento**.

   ![Aggiungi come collegamento][24]

1. Aprire il file *HomeController.cs* nell'editor di Visual Studio e sostituire la definizione dello spazio dei nomi con il codice seguente. Assicurarsi di sostituire `yourServiceNamespace` con il nome dello spazio dei nomi del servizio e `yourKey` con la chiave SAS. Questo codice consente al client di chiamare il servizio locale, restituendo il risultato della chiamata.

   ```csharp
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

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sulla soluzione **ProductsPortal** . Assicurarsi di fare clic con il pulsante destro del mouse sulla soluzione, non sul progetto. Selezionare **Aggiungi** > **progetto esistente**.
1. Individuare il progetto **ProductsServer**, quindi fare doppio clic sulla soluzione *ProductsServer.csproj* per aggiungerla.
1. **ProductsServer** deve essere in esecuzione per visualizzare i dati in **ProductsPortal**. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sulla soluzione **ProductsPortal** e selezionare **Proprietà** per visualizzare le **pagine delle proprietà**.
1. Selezionare**progetto di avvio** **Proprietà** > comuni e scegliere **progetti di avvio multipli**. Verificare che **ProductsServer** e **ProductsPortal** siano visualizzati nell'ordine specificato e che l' **azione** per entrambi sia **Avvia**.

      ![Progetti di avvio multipli][25]

1. Selezionare **Proprietà** > comuni**Dipendenze progetto** sul lato sinistro.
1. Per i **progetti**, scegliere **ProductsPortal**. Assicurarsi che **ProductsServer** sia selezionato.

    ![Dipendenze progetto][26]

1. Per i **progetti**, scegliere **ProductsServer**. Verificare che **ProductsPortal** non sia selezionato e quindi fare clic su **OK** per salvare le modifiche.

## <a name="run-the-project-locally"></a>Eseguire il progetto in locale

Per testare l'applicazione in locale, in Visual Studio selezionare F5. Il server locale, **ProductsServer**, dovrebbe iniziare per primo, quindi l'applicazione **ProductsPortal** dovrebbe avviarsi in una finestra del browser. Questa volta è possibile notare che nell'inventario dei prodotti sono elencati i dati recuperati dal sistema locale del servizio dei prodotti.

![Applicazione Web][10]

Selezionare **Aggiorna** nella pagina **ProductsPortal** . Ogni volta che si aggiorna la pagina, nell'app server viene visualizzato un messaggio quando si chiama `GetProducts()` da **ProductsServer**.

Chiudere entrambe le applicazioni prima di procedere alla sezione successiva.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Distribuire il progetto ProductsPortal in un'app Web di Azure

Il passaggio successivo consiste nel ripubblicare il front-end **ProductsPortal** dell'app Web di Azure:

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **ProductsPortal** e scegliere **pubblica**. Nella pagina **pubblica** selezionare **pubblica**.

   > [!NOTE]
   > È possibile che nella finestra del browser venga visualizzato un messaggio di errore quando il progetto Web **ProductsPortal** viene avviato automaticamente dopo la distribuzione. Si tratta di un comportamento previsto che si verifica perché l'applicazione **ProductsServer** non è ancora in esecuzione.
   >

1. Copiare l'URL dell'app Web distribuita. L'URL sarà necessario in un secondo momento. È anche possibile ottenere questo URL dalla finestra **attività del servizio app Azure** in Visual Studio:

   ![URL dell'app distribuita][9]

1. Chiudere la finestra del browser per arrestare l'applicazione in esecuzione.

<a name="set-productsportal-as-web-app"></a>Prima di eseguire l'applicazione nel cloud, è necessario assicurarsi che **ProductsPortal** venga avviato dall'interno di Visual Studio come app Web.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **ProductsPortal** e scegliere **Proprietà**.
1. Selezionare **Web**. In **azione di avvio**scegliere **Avvia URL**. Immettere l'URL per l'app Web distribuita in precedenza, in questo `https://productsportal20190906122808.azurewebsites.net/`esempio.

    ![URL di avvio][27]

1. Selezionare **file** > **Salva tutto**.
1. Selezionare **Compila compilazione** > **soluzione**.

## <a name="run-the-application"></a>Eseguire l'applicazione

Selezionare F5 per compilare ed eseguire l'applicazione. Il server locale, che è l'applicazione console **ProductsServer** , dovrebbe iniziare per primo, quindi l'applicazione **ProductsPortal** dovrebbe avviarsi in una finestra del browser, come illustrato di seguito:

   ![Eseguire l'app Web in Azure][1]

Nell'inventario dei prodotti sono elencati i dati recuperati dal sistema locale del servizio prodotto e vengono visualizzati i dati nell'app Web. Verificare l'URL per assicurarsi che **ProductsPortal** sia in esecuzione nel cloud come app Web di Azure.

   > [!IMPORTANT]
   > L'applicazione console **ProductsServer** deve essere in esecuzione e in grado di passare dati all'applicazione **ProductsPortal**. Se il browser visualizza un errore, attendere alcuni secondi per il caricamento di **ProductsServer** e visualizzare il messaggio seguente, quindi aggiornare il browser.
   >

Nel browser aggiornare la pagina **ProductsPortal** . Ogni volta che si aggiorna la pagina, nell'app server viene visualizzato un messaggio quando si chiama `GetProducts()` da **ProductsServer**.

![Output aggiornato][38]

## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione seguente:

> [!div class="nextstepaction"]
>[Esporre un servizio WCF locale a un client WCF esterno alla rete](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
