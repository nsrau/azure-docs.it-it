---
title: Esercitazione sull'interfaccia REST del bus di servizio usando la messaggistica inoltrata | Microsoft Docs
description: Compilare una semplice applicazione host di Inoltro del bus di servizio che espone un'interfaccia basata su REST.
services: service-bus
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2016
ms.author: sethm

---
# <a name="service-bus-relay-rest-tutorial"></a>Esercitazione su REST di Inoltro del bus di servizio
In questa esercitazione viene descritto come creare una semplice applicazione host del bus di servizio che espone un'interfaccia basata su REST. REST consente ai client Web, ad esempio un Web browser, di accedere all'API del bus di servizio tramite richieste HTTP.

In questa esercitazione viene utilizzato il modello di programmazione REST di Windows Communication Foundation (WCF) per creare un servizio REST nel bus di servizio. Per altre informazioni, vedere [Modello di programmazione HTTP Web WCF](https://msdn.microsoft.com/library/bb412169.aspx) e [Progettazione e implementazione di servizi](https://msdn.microsoft.com/library/ms729746.aspx) nella documentazione di WCF.

## <a name="step-1:-create-a-service-namespace"></a>Passaggio 1: Creare uno spazio dei nomi del servizio
Il primo passaggio consiste nel creare uno spazio dei nomi e nell'ottenere una chiave di firma di accesso condiviso. Uno spazio dei nomi fornisce un limite per ogni applicazione esposta tramite il bus di servizio. Una chiave di firma di accesso condiviso viene generata dal sistema quando viene creato uno spazio dei nomi del servizio. La combinazione di spazio dei nomi servizio e chiave di firma di accesso condiviso fornisce le credenziali che consentono al bus di servizio di autenticare l'accesso a un'applicazione.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="step-2:-define-a-rest-based-wcf-service-contract-to-use-with-service-bus"></a>Passaggio 2: definire un contratto di servizio WCF basato su REST da utilizzare con il bus di servizio
Come con altri servizi del bus di servizio, quando si crea un servizio basato su REST, è necessario definire il contratto. Il contratto specifica quali operazioni sono supportate dall'host. Un'operazione di servizio può essere considerata come un metodo del servizio Web. I contratti vengono creati definendo un'interfaccia C++, C# o Visual Basic. Ogni metodo dell'interfaccia corrisponde a un'operazione di servizio specifico. L'attributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) deve essere applicato a ogni interfaccia e l'attributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) deve essere applicato a ogni operazione. Se un metodo di un'interfaccia che ha [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) non ha [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), non viene esposto. Il codice usato per eseguire queste attività viene illustrato nell'esempio che segue la procedura.

La differenza principale tra un contratto del bus di servizio di base e un contratto di tipo REST è costituita dall'aggiunta di una proprietà all’attributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Questa proprietà consente di eseguire il mapping di un metodo dell'interfaccia a un metodo su altro lato dell'interfaccia. In tal caso, si userà [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) per collegare un metodo a HTTP GET. In questo modo il bus di servizio può recuperare e interpretare in modo accurato i comandi inviati all'interfaccia.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Per creare un contratto di bus di servizio con un'interfaccia
1. Aprire Visual Studio come amministratore: fare clic con il pulsante destro del mouse sul programma nel menu **Start** e quindi fare clic su **Esegui come amministratore**.
2. Creare un nuovo progetto di applicazione console. Scegliere il menu **File**, selezionare **Nuovo** e quindi **Progetto**. Nella finestra di dialogo **Nuovo progetto** fare clic su **Visual C#**, selezionare il modello **Applicazione console** e denominarlo **ImageListener**. Usare il valore predefinito **Percorso**. Fare clic su **OK** per creare il progetto.
3. Per un progetto C#, Visual Studio crea un file `Program.cs`. Questa classe contiene un metodo `Main()` vuoto, necessario per una corretta compilazione del progetto di applicazione console.
4. Aggiungere al progetto i riferimenti al bus di servizio e a **System.ServiceModel.dll** installando il pacchetto NuGet del bus di servizio. Questo pacchetto aggiunge automaticamente i riferimenti alle librerie del bus di servizio, oltre all'oggetto **System.ServiceModel** di WCF. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ImageListener** e quindi scegliere **Gestisci pacchetti NuGet**. Fare clic sulla scheda **Sfoglia** e cercare `Microsoft Azure Service Bus`. Fare clic su **Installa**e accettare le condizioni per l'utilizzo.
5. È necessario aggiungere in modo esplicito un riferimento a **System.ServiceModel.Web.dll** nel progetto:
   
    a. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella **Riferimenti** nella cartella di progetto e quindi fare clic su **Aggiungi riferimento**.
   
    b. Nella finestra di dialogo **Aggiungi riferimento** fare clic sulla scheda **Framework** sul lato sinistro e nella casella **Cerca** digitare **System.ServiceModel.Web**. Selezionare la casella di controllo **System.ServiceModel.Web** e quindi fare clic su **OK**.
6. Aggiungere le istruzioni `using` seguenti all'inizio del file Program.cs.
   
    ```
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```
   
    [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) è lo spazio dei nomi che consente l'accesso a livello di codice alle funzionalità di base di WCF. Il bus di servizio utilizza molti degli oggetti e degli attributi di WCF per definire i contratti di servizio. Questo spazio dei nomi viene utilizzato nella maggior parte delle applicazioni di inoltro del bus di servizio. Analogamente, [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) consente di definire il canale, ossia l'oggetto usato per comunicare con il bus di servizio e il Web browser client. Infine, [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) contiene i tipi che consentono di creare applicazioni basate sul Web.
7. Rinominare lo spazio dei nomi `ImageListener` in **Microsoft.ServiceBus.Samples**.
   
    ```
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```
8. Subito dopo la parentesi graffa aperta nella dichiarazione dello spazio dei nomi, definire una nuova interfaccia denominata **IImageContract** e applicare l'attributo **ServiceContractAttribute** all'interfaccia con il valore `http://samples.microsoft.com/ServiceModel/Relay/`. Il valore dello spazio dei nomi è diverso dallo spazio dei nomi usato nell'ambito del codice. Il valore dello spazio dei nomi viene utilizzato come identificatore univoco per questo contratto e deve disporre delle informazioni sulla versione. Per altre informazioni, vedere [Controllo delle versioni del servizio](http://go.microsoft.com/fwlink/?LinkID=180498). Specificando lo spazio dei nomi in modo esplicito si impedisce che il valore predefinito dello spazio dei nomi venga aggiunto al nome del contratto.
   
    ```
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```
9. Nell'interfaccia `IImageContract` dichiarare un metodo per la singola operazione esposta dal contratto `IImageContract` nell'interfaccia e applicare l'attributo `OperationContractAttribute` al metodo da esporre come parte del contratto pubblico del bus di servizio.
   
    ```
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```
10. Nell'attributo **OperationContract** aggiungere il valore **WebGet**.
    
    ```
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```
    
    Ciò consente al bus di servizio di instradare le richieste HTTP GET a `GetImage` e di convertire i valori restituiti di `GetImage` in una risposta HTTP GETRESPONSE. Più avanti nell'esercitazione si utilizzerà un Web browser per accedere a questo metodo e per visualizzare l'immagine nel browser.
11. Subito dopo la definizione di `IImageContract`, dichiarare un canale che eredita dalle interfacce `IImageContract` e `IClientChannel`.
    
    ```
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```
    
    Un canale è l'oggetto WCF tramite il quale il servizio e il client si scambiano le informazioni. In seguito verrà creato il canale nell'applicazione host. A questo punto, il bus di servizio usa questo canale per passare le richieste HTTP GET dal browser all’implementazione di **GetImage**. Il bus di servizio usa il canale anche per richiedere il valore restituito **GetImage** e convertirlo in una risposta HTTP GETRESPONSE per il browser client.
12. Dal menu **Compila**, fare clic su **Compila soluzione** per verificare la correttezza del lavoro svolto finora.

### <a name="example"></a>Esempio
L'esempio di codice seguente illustra un'interfaccia di base che definisce un contratto del bus di servizio.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="step-3:-implement-a-rest-based-wcf-service-contract-to-use-service-bus"></a>Passaggio 3: implementare un contratto di servizio WCF basato su REST per utilizzare il bus di servizio
La creazione di un servizio basato su REST del bus di servizio, è necessario creare innanzitutto il contratto, il quale viene definito utilizzando un'interfaccia. Il passaggio successivo consiste nell'implementazione dell'interfaccia. Ciò comporta la creazione di una classe denominata **ImageService** che implementa l’interfaccia **IImageContract** definita dall'utente. Dopo aver implementato il contratto, è quindi necessario configurare l'interfaccia utilizzando un file App.config. Il file di configurazione contiene le informazioni necessarie per l'applicazione, ad esempio il nome del servizio, il nome del contratto e il tipo di protocollo utilizzato per comunicare con il bus di servizio. Il codice usato per eseguire queste attività viene fornito nell'esempio che segue la procedura.

Come per i passaggi precedenti, è impercettibile la differenza tra l'implementazione di un contratto di tipo REST e di un contratto di base del bus di servizio.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Per implementare un contratto del bus di servizio di tipo REST
1. Creare una nuova classe denominata **ImageService** direttamente dopo la definizione dell'interfaccia **IImageContract**. La classe **ImageService** implementa l'interfaccia **IImageContract**.
   
    ```
    class ImageService : IImageContract
    {
    }
    ```
    Analogamente ad altre implementazioni di interfaccia, è possibile implementare la definizione in un altro file. Tuttavia, per questa esercitazione, l'implementazione è presente nello stesso file di definizione dell'interfaccia e del metodo `Main()`.
2. Applicare l'attributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) alla classe **IImageService** per indicare che la classe è un'implementazione di un contratto WCF.
   
    ```
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```
   
    Come accennato in precedenza, questo spazio dei nomi non è uno spazio dei nomi tradizionale. Infatti, fa parte dell'architettura WCF che identifica il contratto. Per altre informazioni, vedere l'argomento [Nomi di contratto dati](https://msdn.microsoft.com/library/ms731045.aspx) nella documentazione di WCF.
3. Aggiungere un'immagine con estensione jpg al progetto.  
   
    Si tratta di un'immagine che viene visualizzata nel browser di ricezione dal servizio. Fare clic con il pulsante destro del mouse sul progetto e quindi scegliere **Aggiungi**. Fare clic su **Elemento esistente**. Usare la finestra di dialogo **Aggiungi elemento esistente** per trovare un'immagine con estensione jpg appropriata e quindi fare clic su **Aggiungi**.
   
    Quando si aggiunge il file, assicurarsi che **Tutti i file** sia selezionato nell'elenco a discesa accanto al campo **Nome file:**. Nella parte restante di questa esercitazione si presuppone che il nome dell'immagine sia "image.jpg". Se si dispone di un file diverso, è necessario rinominare l'immagine o modificare il codice per compensare.
4. Per assicurarsi che il servizio in esecuzione riesca a trovare il file di immagine, in **Esplora soluzioni** fare clic con il pulsante destro del mouse sul file di immagine e scegliere **Proprietà**. Nel riquadro **Proprietà** impostare il valore di **Copia in directory di output** su **Copia se più recente**.
5. Aggiungere al progetto un riferimento all'assembly **System.Drawing.dll`using` e anche le istruzioni ** associate riportate qui sotto.  
   
    ```
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```
6. Nella classe **ImageService** aggiungere il costruttore seguente che consente di caricare la bitmap e prepararla per l'invio al browser client.
   
    ```
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```
7. Direttamente dopo il codice precedente, aggiungere il metodo **GetImage** seguente nella classe **ImageService** per restituire un messaggio HTTP che contiene l'immagine.
   
    ```
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```
   
    Questa implementazione usa **MemoryStream** per recuperare l'immagine e prepararla per la trasmissione in streaming al browser. Avvia la posizione del flusso da zero, dichiara il contenuto di flusso come jpeg e trasmette le informazioni.
8. Dal menu **Compila** scegliere **Compila soluzione**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Per definire la configurazione per l'esecuzione del servizio Web sul bus di servizio
1. In **Esplora soluzioni** fare doppio clic sul file **App.config** per aprirlo nell'editor di Visual Studio.
   
    Il file **App.config** è simile a un file di configurazione WCF e include il nome del servizio, l'endpoint, ovvero il percorso esposto dal bus di servizio per le comunicazioni tra client e host, e l’associazione, ovvero il tipo di protocollo usato per la comunicazione. La differenza principale è che l'endpoint di servizio configurato fa riferimento a un’associazione [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) che non fa parte di .NET Framework.
2. L'elemento XML `<system.serviceModel>` è un elemento WCF che definisce uno o più servizi. In questo caso, viene utilizzato per definire il nome del servizio e l'endpoint. Nella parte inferiore dell'elemento `<system.serviceModel>`, ma sempre in `<system.serviceModel>`, aggiungere un elemento `<bindings>` che include il contenuto seguente. In tal modo, vengono definite le associazioni utilizzate nell'applicazione. È possibile definire più associazioni, ma per questa esercitazione se ne definisce una sola.
   
    ```
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```
   
    Questo passaggio definisce un'associazione [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) del bus di servizio con **relayClientAuthenticationType** impostato su **None**. Questa impostazione indica che un endpoint che utilizza questa associazione non richiede le credenziali client.
3. Dopo l'elemento `<bindings>` aggiungere un elemento `<services>`. Come per le associazioni, è possibile definire più servizi in un solo file di configurazione. Tuttavia, per questa esercitazione, se ne definisce solo uno.
   
    ```
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```
   
    Questo passaggio consente di configurare un servizio che usa il valore predefinito **webHttpRelayBinding** definito in precedenza. Usa anche il valore predefinito **sbTokenProvider**, definito nel passaggio successivo.
4. Dopo l'elemento `<services>`, creare un elemento `<behaviors>` con il contenuto seguente, sostituendo "SAS_KEY" con la chiave di *firma di accesso condiviso* ottenuta in precedenza dal [portale di Azure][portale di Azure].
   
    ```
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```
5. Nell'elemento `<appSettings>`, sempre nel file App.config, sostituire il valore dell'intera stringa di connessione con la stringa di connessione ottenuta prima dal portale. 
   
    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```
6. Scegliere **Compila soluzione** dal menu **Compila** per compilare l'intera soluzione.

### <a name="example"></a>Esempio
Il codice seguente illustra l'implementazione del contratto e del servizio per un servizio basato su REST eseguito nel bus di servizio usando l'associazione **WebHttpRelayBinding**.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Nell'esempio seguente viene illustrato il file App.config associato al servizio.

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="[SAS_KEY]" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
</configuration>
```

## <a name="step-4:-host-the-rest-based-wcf-service-to-use-service-bus"></a>Passaggio 4: ospitare il servizio WCF basato su REST per usare il bus di servizio
In questo passaggio viene descritto come eseguire un servizio Web utilizzando un'applicazione console nel bus di servizio. Un elenco completo del codice scritto in questo passaggio viene fornito nell’esempio che segue la procedura.

### <a name="to-create-a-base-address-for-the-service"></a>Per creare un indirizzo di base per il servizio
1. Nella dichiarazione della funzione `Main()` creare una variabile per archiviare lo spazio dei nomi del progetto del bus di servizio. Assicurarsi di sostituire `yourNamespace` con il nome dello spazio dei nomi del servizio creato prima.
   
    ```
    string serviceNamespace = "yourNamespace";
    ```
    Il bus di servizio utilizza il nome dello spazio dei nomi per creare un URI univoco.
2. Creare un'istanza di `Uri` per l'indirizzo di base del servizio basato sullo spazio dei nomi.
   
    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Per creare e configurare l'host del servizio Web
* Creare l'host del servizio Web utilizzando l'indirizzo URI creato precedentemente in questa sezione.
  
    ```
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    L'host del servizio è l'oggetto WCF che crea un'istanza dell'applicazione host. In questo esempio viene passato il tipo di host da creare, ovvero **ImageService**, nonché l'indirizzo al quale si vuole esporre l'applicazione host.

### <a name="to-run-the-web-service-host"></a>Per eseguire l'host del servizio Web
1. Aprire il servizio.
   
    ```
    host.Open();
    ```
    Il servizio è in esecuzione.
2. Visualizzare un messaggio che indica che il servizio è in esecuzione e come arrestare il servizio.
   
    ```
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Al termine, chiudere l'host del servizio.
   
    ```
    host.Close();
    ```

## <a name="example"></a>Esempio
Nell'esempio seguente vengono inclusi il contratto di servizio e l'implementazione dei passaggi precedenti dell'esercitazione e viene ospitato il servizio in un'applicazione console. Compilare il codice seguente in un eseguibile denominato ImageListener.exe.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### <a name="compiling-the-code"></a>Compilazione del codice
Dopo aver compilato la soluzione, effettuare le operazioni seguenti per eseguire l'applicazione:

1. Premere **F5** o passare al percorso del file eseguibile ImageListener\bin\Debug\ImageListener.exe per eseguire il servizio. Mantenere l'app in esecuzione perché verrà usata per eseguire il passaggio successivo.
2. Copiare e incollare l'indirizzo dal prompt dei comandi in un browser per visualizzare l'immagine.
3. Al termine, premere **INVIO** nella finestra del prompt dei comandi per chiudere l'app.

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver creato un'applicazione che utilizza il servizio di inoltro del bus di servizio, vedere gli articoli seguenti per ulteriori informazioni sulla messaggistica inoltrata:

* [Panoramica dell'architettura del bus di servizio di Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#relays)
* [Come usare il servizio Inoltro del bus di servizio di Azure](service-bus-dotnet-how-to-use-relay.md)

[portale di Azure]: https://portal.azure.com


<!--HONumber=Oct16_HO2-->


