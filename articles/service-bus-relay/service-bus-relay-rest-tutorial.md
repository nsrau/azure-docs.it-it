---
title: 'Esercitazione: Esercitazione su REST con Inoltro di Azure'
description: "Esercitazione: Compilare un'applicazione host di inoltro del bus di servizio di Azure che espone un'interfaccia basata su REST."
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 229ed2b00582f2c73ce68c47406d68325abda736
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718826"
---
# <a name="tutorial-azure-wcf-relay-rest-tutorial"></a>Esercitazione: Esercitazione su REST per l'inoltro WCF di Azure

Questa esercitazione descrive come creare un'applicazione host Inoltro di Azure che espone un'interfaccia basata su REST. REST consente ai client Web, ad esempio un Web browser, di accedere all'API del bus di servizio tramite richieste HTTP.

In questa esercitazione viene usato il modello di programmazione REST di Windows Communication Foundation (WCF) per creare un servizio REST nel servizio di inoltro di Azure. Per altre informazioni, vedere [Modello di programmazione REST WCF](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) e [Progettazione e implementazione di servizi](/dotnet/framework/wcf/designing-and-implementing-services).

In questa esercitazione si eseguiranno le attività seguenti:

> [!div class="checklist"]
>
> * Installare i prerequisiti per questa esercitazione.
> * Creare uno spazio dei nomi di inoltro.
> * Definire un contratto di servizio WCF basato su REST.
> * Implementare il contratto WCF basato su REST.
> * Ospitare ed eseguire il servizio WCF basato su REST.
> * Eseguire e testare il servizio.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Visual Studio 2015 o versione successiva](https://www.visualstudio.com). Negli esempi di questa esercitazione viene usato Visual Studio 2019.
* Azure SDK per .NET. Installare l'SDK dalla [pagina Download per gli SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Creare uno spazio dei nomi di inoltro

Per usare le funzionalità del servizio d'inoltro di Azure, è prima necessario creare uno spazio dei nomi del servizio. Uno spazio dei nomi funge da contenitore di ambito in cui indirizzare le risorse di Azure nell'applicazione. Seguire le [istruzioni qui](relay-create-namespace-portal.md) per creare uno spazio dei nomi di inoltro.

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Definire un contratto di servizio WCF basato su REST da usare con Inoltro di Azure

Quando si crea un servizio basato su REST WCF, è necessario definire il contratto. Il contratto specifica quali operazioni sono supportate dall'host. Un'operazione del servizio è simile a un metodo del servizio Web. Definire un contratto con un'interfaccia C++, C# o Visual Basic. Ogni metodo dell'interfaccia corrisponde a un'operazione di servizio specifico. Applicare l'attributo [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) a ogni interfaccia e applicare l'attributo [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) a ogni operazione. 

> [!TIP]
> Se un metodo di un'interfaccia che ha [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) non ha [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), il metodo non viene esposto. Il codice usato per eseguire queste attività è illustrato nell'esempio che segue la procedura.

La differenza principale tra un contratto di WCF e un contratto di tipo REST è costituita dall'aggiunta di una proprietà all'attributo [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Questa proprietà consente di eseguire il mapping di un metodo dell'interfaccia a un metodo su altro lato dell'interfaccia. Questo esempio usa l'attributo [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) per collegare un metodo a `HTTP GET`. Questo approccio consente al bus di servizio di recuperare e interpretare in modo accurato i comandi inviati all'interfaccia.

### <a name="to-create-a-contract-with-an-interface"></a>Per creare un contratto con un'interfaccia

1. Avviare Microsoft Visual Studio come amministratore. A tale scopo, fare clic con il tasto destro del mouse sull'icona del programma Visual Studio e selezionare **Esegui come amministratore**.
1. In Visual Studio selezionare **Crea un nuovo progetto**.
1. In **Crea un nuovo progetto** scegliere **App console (.NET Framework)** per C# e selezionare **Avanti**.
1. Denominare il progetto *ImageListener*. Usare la **Posizione** predefinita e quindi selezionare **Crea**.

   Per un progetto C#, Visual Studio crea un file *Program.cs*. Questa classe contiene un metodo `Main()` vuoto, necessario per una corretta compilazione del progetto di applicazione console.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **ImageListener** e quindi selezionare **Gestisci pacchetti NuGet**.
1. Selezionare **Sfoglia**, quindi cercare e scegliere **WindowsAzure.ServiceBus**. Selezionare **Installa** e accettare le condizioni per l'utilizzo.

    Questo passaggio aggiunge riferimenti al bus di servizio e a *System.ServiceModel.dll*. Questo pacchetto aggiunge automaticamente riferimenti alle librerie del bus di servizio e a `System.ServiceModel` di WCF.

1. Aggiungere in modo esplicito un riferimento a `System.ServiceModel.Web.dll` al progetto. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Riferimenti** nella cartella di progetto e selezionare **Aggiungi riferimento**.
1. In **Aggiungi riferimento** selezionare **Framework** e immettere *System.ServiceModel.Web* in **Cerca**. Selezionare la casella di controllo **System.ServiceModel.Web** e quindi fare clic su **OK**.

Quindi apportare le modifiche di codice seguenti al progetto:

1. Aggiungere le istruzioni `using` seguenti all'inizio del file *Program.cs*.

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel) è lo spazio dei nomi che consente l'accesso a livello di codice alle funzionalità di base di WCF. Inoltro WCF usa molti oggetti e attributi di WCF per definire i contratti di servizio. Questo spazio dei nomi viene usato nella maggior parte delle applicazioni di inoltro.
    * [System.ServiceModel.Channels](/dotnet/api/system.servicemodel.channels) consente di definire il canale, ovvero l'oggetto usato per comunicare con il servizio Inoltro di Azure e il Web browser client.
    * [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) contiene i tipi che consentono di creare applicazioni basate sul Web.

1. Rinominare lo spazio dei nomi `ImageListener` in `Microsoft.ServiceBus.Samples`.

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Subito dopo la parentesi graffa aperta nella dichiarazione dello spazio dei nomi, definire una nuova interfaccia denominata `IImageContract` e applicare l'attributo `ServiceContractAttribute` all'interfaccia con il valore `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1`. 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    Il valore dello spazio dei nomi è diverso dallo spazio dei nomi usato nell'ambito del codice. Il valore dello spazio dei nomi è un identificatore univoco per questo contratto e deve includere informazioni sulla versione. Per altre informazioni, vedere [Controllo delle versioni del servizio](/dotnet/framework/wcf/service-versioning). Specificando lo spazio dei nomi in modo esplicito si impedisce che il valore predefinito dello spazio dei nomi venga aggiunto al nome del contratto.

1. Nell'interfaccia `IImageContract` dichiarare un metodo per la singola operazione esposta dal contratto `IImageContract` nell'interfaccia e applicare l'attributo `OperationContract` al metodo da esporre come parte del contratto pubblico del bus di servizio.

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. Nell'attributo `OperationContract` aggiungere il valore `WebGet`.

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   L'aggiunta del valore `WebGet` consente al servizio di inoltro di instradare le richieste HTTP GET a `GetImage` e di convertire i valori restituiti di `GetImage` in una risposta `HTTP GETRESPONSE`. Più avanti nell'esercitazione verrà usato un Web browser per accedere a questo metodo e per visualizzare l'immagine nel browser.

1. Subito dopo la definizione di `IImageContract`, dichiarare un canale che eredita dalle interfacce `IImageContract` e `IClientChannel`.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   Un canale è l'oggetto WCF tramite il quale il servizio e il client si scambiano le informazioni. In seguito verrà creato il canale nell'applicazione host. Il servizio Inoltro di Azure usa quindi questo canale per passare le richieste HTTP GET dal browser all'implementazione di `GetImage`. L'inoltro usa il canale anche per richiedere il valore restituito `GetImage` e convertirlo in `HTTP GETRESPONSE` per il browser client.

1. Selezionare **Compila** > **Compila soluzione** per verificare la correttezza del lavoro svolto.

### <a name="example-that-defines-a-wcf-relay-contract"></a>Esempio che definisce un contratto di Inoltro WCF

L'esempio di codice seguente illustra un'interfaccia di base che definisce un contratto dell'inoltro WCF.

```csharp
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

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

## <a name="implement-the-rest-based-wcf-service-contract"></a>Implementare il contratto di servizio WCF basato su REST

Per creare un servizio Inoltro WCF di tipo REST, creare innanzitutto il contratto usando un'interfaccia. Il passaggio successivo consiste nell'implementazione dell'interfaccia. Questa procedura richiede la creazione di una classe denominata `ImageService` che implementa l'interfaccia `IImageContract` definita dall'utente. Dopo aver implementato il contratto, è quindi necessario configurare l'interfaccia usando un file *App.config*. Il file di configurazione contiene le informazioni necessarie per l'applicazione. Le informazioni includono il nome del servizio, il nome del contratto e il tipo di protocollo usato per comunicare con il servizio di inoltro. Il codice usato per eseguire queste attività è illustrato nell'esempio che segue la procedura.

Come nella procedura precedente, è impercettibile la differenza tra l'implementazione di un contratto di tipo REST e quella di un contratto di Inoltro WCF.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Per implementare un contratto del bus di servizio di tipo REST

1. Creare una nuova classe denominata `ImageService` direttamente dopo la definizione dell’interfaccia `IImageContract`. La classe `ImageService` implementa l'interfaccia `IImageContract`.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    Analogamente ad altre implementazioni di interfaccia, è possibile implementare la definizione in un altro file. Tuttavia, per questa esercitazione, l'implementazione è presente nello stesso file di definizione dell'interfaccia e del metodo `Main()`.

1. Applicare l'attributo [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) alla classe `IImageService` per indicare che la classe è un'implementazione di un contratto WCF.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Come accennato in precedenza, questo spazio dei nomi non è uno spazio dei nomi tradizionale. È parte dell'architettura WCF che identifica il contratto. Per altre informazioni, vedere [Nomi di contratto di dati](/dotnet/framework/wcf/feature-details/data-contract-names/).

1. Aggiungere un'immagine con estensione *jpg* al progetto. Il file è un'immagine che viene visualizzata nel browser di ricezione dal servizio.

   1. Fare clic con il pulsante destro del mouse sul progetto e selezionare **Aggiungi**.
   1. Quindi selezionare **Elemento esistente**.
   1. Usare **Aggiungi elemento esistente** per cercare un'immagine con estensione jpg appropriata e quindi selezionare **Aggiungi**. Quando si aggiunge il file, selezionare **Tutti i file** dall'elenco a discesa accanto a **Nome file**.

   Nella parte restante di questa esercitazione si presuppone che il nome dell'immagine sia *image.jpg*. Se si dispone di un file diverso, è necessario rinominare l'immagine o modificare il codice per compensare.

1. Per assicurarsi che il servizio in esecuzione riesca a trovare il file di immagine, in **Esplora soluzioni** fare clic con il pulsante destro del mouse sul file di immagine e scegliere **Proprietà**. In **Proprietà** impostare **Copia in directory di output** su **Copia se più recente**.

1. Usare la procedura descritta in [Per creare un contratto con un'interfaccia](#to-create-a-contract-with-an-interface) per aggiungere un riferimento all'assembly *System.Drawing.dll* al progetto.

1. Aggiungere le istruzioni `using` associate seguenti:

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. Nella classe `ImageService` aggiungere il costruttore seguente che carica la bitmap e la prepara per l'invio al browser client:

    ```csharp
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

1. Immediatamente dopo il codice precedente, aggiungere il metodo `GetImage` seguente nella classe `ImageService` per restituire un messaggio HTTP che contiene l'immagine.

    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```

    Questa implementazione usa `MemoryStream` per recuperare l'immagine e prepararla per la trasmissione al browser. Avvia la posizione del flusso da zero, dichiara il contenuto di flusso come *jpg* e trasmette le informazioni.

1. Selezionare **Compila** > **Compila soluzione**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Per definire la configurazione per l'esecuzione del servizio Web sul bus di servizio

1. In **Esplora soluzioni** fare doppio clic su **App.config** per aprire il file nell'editor di Visual Studio.

    Il file *App.config* include il nome del servizio, l'endpoint e l'associazione. L'endpoint è il percorso che Inoltro di Azure espone per la comunicazione tra client e host. L'associazione è il tipo di protocollo usato per comunicare. La differenza principale è che l'endpoint di servizio configurato fa riferimento a un'associazione [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding).

1. L'elemento XML `<system.serviceModel>` è un elemento WCF che definisce uno o più servizi. In questo caso viene usato per definire il nome del servizio e l'endpoint. Nella parte inferiore dell'elemento `<system.serviceModel>`, sempre all'interno di `<system.serviceModel>`, aggiungere un elemento `<bindings>` che include il contenuto seguente:

    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Questo contenuto definisce le associazioni usate nell'applicazione. Sebbene sia possibile definire più associazioni, in questa esercitazione viene definita una sola associazione.

    Il codice precedente definisce un'associazione [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) di Inoltro WCF con `relayClientAuthenticationType` impostato su `None`. Questa impostazione indica che un endpoint che usa questa associazione non richiede le credenziali client.

1. Dopo l'elemento `<bindings>` aggiungere un elemento `<services>`. Come per le associazioni, è possibile definire più servizi in un solo file di configurazione. Tuttavia, per questa esercitazione, se ne definisce solo uno.

    ```xml
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

    Questo contenuto configura un servizio che usa il valore predefinito `webHttpRelayBinding` definito in precedenza. Usa anche il valore predefinito `sbTokenProvider`, definito nel passaggio successivo.

1. Dopo l'elemento `<services>` creare un elemento `<behaviors>` con il contenuto seguente, sostituendo `SAS_KEY` con la chiave di firma di accesso condiviso. Per ottenere una chiave di firma di accesso condiviso dal [portale di Azure][Azure portal], vedere [Ottenere le credenziali di gestione](service-bus-relay-tutorial.md#get-management-credentials).

    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
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

1. Nell'elemento `<appSettings>`, sempre nel file *App.config*, sostituire il valore dell'intera stringa di connessione con la stringa di connessione ottenuta in precedenza dal portale.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. Selezionare **Compila** > **Compila soluzione** per compilare l'intera soluzione.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>Esempio che implementa il contratto di servizio WCF basato su REST

Il codice seguente illustra l'implementazione del contratto e del servizio per un servizio basato su REST eseguito nel bus di servizio usando l'associazione `WebHttpRelayBinding`.

```csharp
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


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

Nell'esempio seguente è illustrato il file *App.config* associato al servizio.

```xml
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
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
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
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>Ospitare il servizio WCF basato su REST per usare Inoltro di Azure

Questa sezione descrive come eseguire un servizio Web usando un'applicazione console con Inoltro WCF. Un elenco completo del codice scritto in questa sezione viene fornito nell’esempio che segue la procedura.

### <a name="to-create-a-base-address-for-the-service"></a>Per creare un indirizzo di base per il servizio

1. Nella dichiarazione della funzione `Main()` creare una variabile per archiviare lo spazio dei nomi del progetto. Assicurarsi di sostituire `yourNamespace` con il nome dello spazio dei nomi di inoltro creato prima.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    Il bus di servizio utilizza il nome dello spazio dei nomi per creare un URI univoco.

1. Creare un'istanza di `Uri` per l'indirizzo di base del servizio basato sullo spazio dei nomi.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Per creare e configurare l'host del servizio Web

Sempre in `Main()` creare l'host del servizio Web usando l'indirizzo URI creato precedentemente in questa sezione.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

L'host del servizio è l'oggetto WCF che crea un'istanza dell'applicazione host. In questo esempio viene passato il tipo di host da creare, ovvero `ImageService`, nonché l'indirizzo in cui si vuole esporre l'applicazione host.

### <a name="to-run-the-web-service-host"></a>Per eseguire l'host del servizio Web

1. Sempre in `Main()` aggiungere la riga seguente per aprire il servizio.

    ```csharp
    host.Open();
    ```

    Il servizio è in esecuzione.

1. Visualizzare un messaggio che indica che il servizio è in esecuzione e come arrestare il servizio.

    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Al termine, chiudere l'host del servizio.

    ```csharp
    host.Close();
    ```

### <a name="example-of-the-service-contract-and-implementation"></a>Esempio di contratto di servizio e di implementazione

Nell'esempio seguente vengono inclusi il contratto di servizio e l'implementazione dei passaggi precedenti dell'esercitazione e viene ospitato il servizio in un'applicazione console. Compilare il codice seguente in un eseguibile denominato *ImageListener.exe*.

```csharp
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

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

## <a name="run-and-test-the-service"></a>Eseguire e testare il servizio

Dopo aver compilato la soluzione, effettuare le operazioni seguenti per eseguire l'applicazione:

1. Premere F5 o passare al percorso del file eseguibile *ImageListener\bin\Debug\ImageListener.exe* per eseguire il servizio. Mantenere l'app in esecuzione poiché verrà usata nel passaggio successivo.
1. Copiare e incollare l'indirizzo dal prompt dei comandi in un browser per visualizzare l'immagine.
1. Al termine, premere INVIO nella finestra del prompt dei comandi per chiudere l'app.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo avere creato un'applicazione che usa il servizio di inoltro di Azure, vedere gli articoli seguenti per altre informazioni:

* [Che cos'è il servizio di inoltro di Azure?](relay-what-is-it.md)
* [Esporre un servizio REST WCF locale a client esterni tramite Inoltro WCF di Azure](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
