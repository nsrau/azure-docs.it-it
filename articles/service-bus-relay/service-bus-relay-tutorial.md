---
title: 'Esercitazione: Esporre un servizio REST WCF locale a client esterni tramite Inoltro WCF di Azure'
description: "Esercitazione: Compilare un'applicazione client e un'applicazione di servizio con l'inoltro WCF."
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: e2dd0448dfed55450a6319936f49831e5d6d77f3
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718854"
---
# <a name="tutorial-expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Esercitazione: Esporre un servizio REST WCF locale a client esterni tramite Inoltro WCF di Azure

Questa esercitazione descrive come compilare un'applicazione e un servizio client di Inoltro WCF usando Inoltro di Azure. Per un'esercitazione simile che usa la [messaggistica del bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md), vedere [Introduzione alle code del bus di servizio](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Questa esercitazione consente di acquisire una comprensione dei passaggi necessari per creare un'applicazione client e di servizio di Inoltro WCF. Come per le rispettive controparti WCF originali, un servizio è un costrutto che espone uno o più endpoint. Ogni endpoint espone una o più operazioni del servizio. L'endpoint di un servizio specifica un indirizzo in cui è disponibile il servizio, un binding che contiene le informazioni che un client deve comunicare al servizio e un contratto che definisce la funzionalità fornita dal servizio ai propri client. La differenza principale tra WCF e l'inoltro WCF riguarda l'esposizione dell'endpoint nel cloud invece che localmente nel computer.

Dopo aver eseguito la sequenza di sezioni di questa esercitazione, sarà disponibile un servizio in esecuzione. Sarà disponibile anche un client in grado di richiamare le operazioni del servizio. 

In questa esercitazione si eseguiranno le attività seguenti:

> [!div class="checklist"]
>
> * Installare i prerequisiti per questa esercitazione.
> * Creare uno spazio dei nomi di inoltro.
> * Creare un contratto di servizio WCF.
> * Implementare il contratto WCF.
> * Ospitare ed eseguire il servizio WCF per la registrazione con il servizio di inoltro.
> * Creare un client WCF per il contratto di servizio.
> * Configurare il client WCF.
> * Implementare il client WCF.
> * Eseguire le applicazioni.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Visual Studio 2015 o versione successiva](https://www.visualstudio.com). Negli esempi di questa esercitazione viene usato Visual Studio 2019.
* Azure SDK per .NET. Installare l'SDK dalla [pagina Download per gli SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Creare uno spazio dei nomi di inoltro

Il primo passaggio consiste nel creare uno spazio dei nomi e nell'ottenere una chiave di [firma di accesso condiviso](../service-bus-messaging/service-bus-sas.md). Uno spazio dei nomi fornisce un limite per ogni applicazione esposta tramite il servizio di inoltro. Una chiave di firma di accesso condiviso viene automaticamente generata dal sistema quando viene creato uno spazio dei nomi del servizio. La combinazione di spazio dei nomi servizio e chiave di firma di accesso condiviso fornisce le credenziali che consentono ad Azure di autenticare l'accesso a un'applicazione.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>Definire un contratto del servizio WCF

Il contratto di servizio specifica le operazioni supportate dal servizio. Le operazioni sono metodi o funzioni del servizio Web. I contratti vengono creati definendo un'interfaccia C++, C# o Visual Basic. Ogni metodo dell'interfaccia corrisponde a un'operazione di servizio specifico. A ogni interfaccia deve essere applicato l'attributo [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) e a ogni operazione deve essere applicato l'attributo [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute). Se un metodo di un'interfaccia con l'attributo [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) non ha l'attributo [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), il metodo non viene esposto. Il codice per eseguire queste attività viene fornito nell'esempio che segue la procedura. Per una descrizione più dettagliata dei contratti e dei servizi, vedere [Progettazione e implementazione di servizi](/dotnet/framework/wcf/designing-and-implementing-services).

### <a name="create-a-relay-contract-with-an-interface"></a>Creare un contratto di inoltro con un'interfaccia

1. Avviare Microsoft Visual Studio come amministratore. A tale scopo, fare clic con il tasto destro del mouse sull'icona del programma Visual Studio e selezionare **Esegui come amministratore**.
1. In Visual Studio selezionare **Crea un nuovo progetto**.
1. In **Crea un nuovo progetto** scegliere **App console (.NET Framework)** per C# e selezionare **Avanti**.
1. Denominare il progetto *EchoService* e selezionare **Crea**.

   ![Creare un'app console][2]

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**. In **Gestione pacchetti NuGet** selezionare **Sfoglia**, quindi cercare e scegliere **WindowsAzure.ServiceBus**. Selezionare **Installa** e accettare le condizioni per l'utilizzo.

    ![Pacchetto del bus di servizio][3]

   Questo pacchetto aggiunge automaticamente riferimenti alle librerie del bus di servizio e a `System.ServiceModel` di WCF. [System.ServiceModel](/dotnet/api/system.servicemodel) è lo spazio dei nomi che consente l'accesso a livello di codice alle funzionalità di base di WCF. Il bus di servizio utilizza molti degli oggetti e degli attributi di WCF per definire i contratti di servizio.

1. Aggiungere le istruzioni `using` seguenti all'inizio del file *Program.cs*:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Modificare il nome dello spazio dei nomi da nome predefinito di `EchoService` a `Microsoft.ServiceBus.Samples`.

   > [!IMPORTANT]
   > Questa esercitazione usa lo spazio dei nomi C# `Microsoft.ServiceBus.Samples`, ovvero lo spazio dei nomi del tipo gestito in base al contratto usato nel file di configurazione nella sezione [Configurare il client WCF](#configure-the-wcf-client). È possibile specificare qualsiasi spazio dei nomi quando si compila questo esempio. L'esercitazione tuttavia non funzionerà a meno che gli spazi dei nomi del contratto e del servizio non vengano modificati di conseguenza nel file di configurazione dell'applicazione. Lo spazio dei nomi specificato nel file *App.config* deve essere lo stesso specificato nei file C#.
   >

1. Subito dopo la dichiarazione dello spazio dei nomi `Microsoft.ServiceBus.Samples`, ma sempre nello spazio dei nomi, definire una nuova interfaccia denominata `IEchoContract` e applicare l'attributo `ServiceContractAttribute` all'interfaccia con un valore dello spazio dei nomi `https://samples.microsoft.com/ServiceModel/Relay/`. Incollare il codice seguente dopo la dichiarazione dello spazio dei nomi:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    Il valore dello spazio dei nomi è diverso dallo spazio dei nomi usato nell'ambito del codice. Il valore dello spazio dei nomi viene invece usato come identificatore univoco per questo contratto. Specificando lo spazio dei nomi in modo esplicito si impedisce che il valore predefinito dello spazio dei nomi venga aggiunto al nome del contratto.

   > [!NOTE]
   > Lo spazio dei nomi del contratto del servizio include in genere uno schema di denominazione che contiene informazioni sulla versione. Se si includono le informazioni sulla versione nello spazio dei nomi del contratto di servizio, si consente ai servizi di isolare le modifiche principali definendo un nuovo contratto di servizio con un nuovo spazio dei nomi ed esponendolo in un nuovo endpoint. In questo modo i client possono continuare a usare il contratto del servizio precedente senza dover essere aggiornati. Le informazioni sulla versione possono essere costituite da una data o da un numero di build. Per altre informazioni, vedere [Controllo delle versioni del servizio](/dotnet/framework/wcf/service-versioning). Per questa esercitazione, lo schema di denominazione dello spazio dei nomi del contratto di servizio non include informazioni sulla versione.
   >

1. Nell'interfaccia `IEchoContract` dichiarare un metodo per la singola operazione esposta dal contratto `IEchoContract` nell'interfaccia e applicare l'attributo `OperationContractAttribute` al metodo da esporre come parte del contratto pubblico di inoltro WCF, come segue:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. Direttamente dopo la definizione dell'interfaccia `IEchoContract` dichiarare un canale che eredita dalle interfacce `IEchoContract` e `IClientChannel`, come illustrato di seguito:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Un canale è l'oggetto WCF attraverso il quale l'host e il client si scambiano informazioni. Successivamente si scriverà il codice per il canale per ripetere le informazioni tra le due applicazioni.

1. Selezionare **Compila** > **Compila soluzione** o premere CTRL+MAIUSC+B per verificare la correttezza del lavoro svolto.

### <a name="example-of-a-wcf-contract"></a>Esempio di contratto WCF

L'esempio di codice seguente illustra un'interfaccia di base che definisce un contratto dell'inoltro WCF.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Dopo aver creato l'interfaccia, è possibile implementarla.

## <a name="implement-the-wcf-contract"></a>Implementare il contratto WCF

La creazione di un inoltro di Azure deve essere preceduta dalla creazione del contratto con un'interfaccia. Per altre informazioni sulla creazione dell'interfaccia, vedere la sezione precedente. La procedura successiva implementa l'interfaccia. Questa attività richiede la creazione di una classe denominata `EchoService` che implementa l'interfaccia `IEchoContract` definita dall'utente. Dopo l'implementazione dell'interfaccia, si procede alla sua configurazione usando un file di configurazione *App.config*. Il file di configurazione contiene le informazioni necessarie per l'applicazione. Le informazioni includono il nome del servizio, il nome del contratto e il tipo di protocollo usato per comunicare con il servizio di inoltro. Il codice usato per eseguire queste attività viene fornito nell'esempio che segue la procedura. Per una descrizione di carattere generale sull'implementazione di un contratto di servizio, vedere [Implementazione dei contratti di servizio](/dotnet/framework/wcf/implementing-service-contracts).

1. Creare una nuova classe denominata `EchoService` direttamente dopo la definizione dell’interfaccia `IEchoContract`. La classe `EchoService` implementa l'interfaccia `IEchoContract`.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Analogamente ad altre implementazioni di interfaccia, è possibile implementare la definizione in un altro file. Tuttavia, per questa esercitazione l'implementazione si trova nello stesso file che contiene la definizione di interfaccia e il metodo `Main()`.

1. Applicare l'attributo [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) all'interfaccia `IEchoContract`. L'attributo specifica lo spazio dei nomi e il nome del servizio. Al termine dell'operazione, la classe `EchoService` ha un aspetto simile al seguente:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Implementare il metodo `Echo` definito nell'interfaccia `IEchoContract` nella classe `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Selezionare **Compila** > **Compila soluzione** oppure premere CTRL+MAIUSC+B.

### <a name="define-the-configuration-for-the-service-host"></a>Definire la configurazione dell'host del servizio

Il file di configurazione è simile a un file di configurazione WCF. Il file include il nome del servizio, l'endpoint e l'associazione. L'endpoint è il percorso che Inoltro di Azure espone per la comunicazione tra client e host. L'associazione è il tipo di protocollo usato per comunicare. La differenza principale consiste nel fatto che l'endpoint di servizio configurato fa riferimento a un’associazione [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) che non fa parte di .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) è una delle associazioni definite dal servizio.

1. In **Esplora soluzioni** fare doppio clic su **App.config** per aprire il file nell'editor di Visual Studio.
1. Nell'elemento `<appSettings>` sostituire i segnaposto con il nome dello spazio dei nomi del servizio e la chiave di firma di accesso condiviso copiata nel passaggio precedente.
1. All'interno dei tag `<system.serviceModel>` aggiungere un elemento `<services>`. È possibile definire più applicazioni di inoltro in un singolo file di configurazione. In questa esercitazione ne viene tuttavia definita solo una.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. Nell'elemento `<services>` aggiungere un elemento `<service>` per definire il nome del servizio.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. Nell'elemento `<service>` definire la posizione del contratto, nonché il tipo di binding per l'endpoint.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    L'endpoint definisce la posizione in cui il client cercherà l'applicazione host. Successivamente nell'esercitazione si userà questo passaggio per creare un URI che espone completamente l'host tramite l'inoltro di Azure. L'associazione dichiara che viene usato il protocollo TCP come protocollo per le comunicazioni con il servizio di inoltro.

1. Selezionare **Compila** > **Compila soluzione** o premere CTRL+MAIUSC+B per verificare la correttezza del lavoro svolto.

### <a name="example-of-implementation-of-a-service-contract"></a>Esempio di implementazione di un contratto di servizio

Il codice seguente illustra l'implementazione del contratto di servizio.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

Il codice seguente illustra il formato di base del file *App.config* associato all'host del servizio.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Ospitare ed eseguire il servizio WCF per la registrazione con il servizio di inoltro

Questo passaggio descrive come eseguire un servizio di inoltro di Azure.

### <a name="create-the-relay-credentials"></a>Creare le credenziali di inoltro

1. In `Main()` creare due variabili in cui archiviare lo spazio dei nomi e la chiave di firma di accesso condiviso  letti dalla finestra della console.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    La chiave di firma di accesso condiviso verrà usata in seguito per accedere al progetto. Lo spazio dei nomi viene passato come parametro a `CreateServiceUri` per creare un URI del servizio.

1. Usando un oggetto [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) dichiarare che verrà usata una chiave di firma di accesso condiviso come tipo di credenziali. Aggiungere il codice seguente direttamente dopo il codice aggiunto nel passaggio precedente.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Creare un indirizzo di base per il servizio

Dopo il codice aggiunto nella sezione precedente, creare un'istanza di `Uri` per l'indirizzo di base del servizio. L'URI specifica lo schema del bus di servizio, lo spazio dei nomi e il percorso dell'interfaccia del servizio.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

Il valore "sb" è un'abbreviazione dello schema del bus di servizio. Indica che è in uso il protocollo TCP. Il protocollo è stato indicato anche in precedenza nel file di configurazione, quando è stata specificata l'associazione [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding).

Per questa esercitazione l'URI è `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>Creare e configurare l'host del servizio

1. Sempre in `Main()`, impostare la modalità di connessione su `AutoDetect`.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    La modalità di connessione descrive il protocollo usato dal servizio per comunicare con il servizio di inoltro, ovvero HTTP o TCP. Quando si usa l'impostazione predefinita `AutoDetect`, il servizio prova a connettersi a Inoltro di Azure tramite TCP, se disponibile, e tramite HTTP se TCP non è disponibile. Questo protocollo è diverso da quello specificato dal servizio per la comunicazione client. Il protocollo dipende dal binding usato. Ad esempio, un servizio può usare l'associazione [BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) che specifica che l'endpoint comunica con i client tramite HTTP. Lo stesso servizio può specificare `ConnectivityMode.AutoDetect` in modo che il servizio comunichi con Inoltro di Azure tramite TCP.

1. Creare l'host del servizio usando l'URI creato precedentemente in questa sezione.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    L'host del servizio è l'oggetto WCF che crea un'istanza del servizio. In questo caso, viene passato il tipo di servizio che si vuole creare (`EchoService`), insieme all'indirizzo in cui si vuole esporre il servizio.

1. All'inizio del file *Program.cs* aggiungere riferimenti a [System.ServiceModel.Description](/dotnet/api/system.servicemodel.description) e [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Tornando a `Main()`, configurare l'endpoint per abilitare l'accesso pubblico.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Questo passaggio indica al servizio di inoltro che l'applicazione può essere trovata pubblicamente esaminando il feed ATOM per il progetto. Se si imposta `DiscoveryType` su `private`, un client può comunque accedere al servizio. Il servizio tuttavia non sarà visibile durante la ricerca nello spazio dei nomi `Relay`. Il client dovrà invece conoscere in anticipo il percorso dell'endpoint.

1. Applicare le credenziali del servizio agli endpoint servizio definiti nel file *App.config*:

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Come accennato in precedenza, nel file di configurazione potrebbero essere stati dichiarati più servizi e più endpoint. In tal caso, questo codice scorrerebbe il file di configurazione per cercare tutti gli endpoint a cui applicare le credenziali. Per questa esercitazione, il file di configurazione ha un solo endpoint.

### <a name="open-the-service-host"></a>Aprire l'host del servizio

1. Sempre in `Main()` aggiungere la riga seguente per aprire il servizio.

    ```csharp
    host.Open();
    ```

1. Informare l'utente che il servizio è in esecuzione e spiegargli come arrestarlo.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Al termine, chiudere l'host del servizio.

    ```csharp
    host.Close();
    ```

1. Premere CTRL+MAIUSC+B per compilare il progetto.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>Esempio che ospita un servizio in un'applicazione console

Il codice del servizio completato comparirà come indicato di seguito. Il codice include il contratto di servizio e l'implementazione dei passaggi precedenti nell'esercitazione e ospita il servizio in un'applicazione console.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Creare un client WCF per il contratto di servizio

L'attività successiva consiste nel creare un'applicazione client e nel definire il contratto di servizio che verrà implementato in seguito. Questi passaggi sono simili ai passaggi usati per la creazione di un servizio: definizione di un contratto, modifica di un file *App.config*, uso delle credenziali per la connessione al servizio di inoltro e così via. Il codice usato per eseguire queste attività viene fornito nell'esempio che segue la procedura.

1. Creare un nuovo progetto nella soluzione Visual Studio corrente per il client:

   1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla soluzione corrente, non sul progetto, quindi selezionare **Aggiungi** > **Nuovo progetto**.
   1. In **Aggiungi un nuovo progetto** selezionare **App console (.NET Framework)** per C# e selezionare **Avanti**.
   1. Assegnare al progetto il nome *EchoClient* e selezionare **Crea**.

1. In **Esplora soluzioni** nel progetto **EchoClient** fare doppio clic su **Program.cs** per aprire il file nell'editor, se non è già aperto.
1. Modificare il nome dello spazio dei nomi da nome predefinito di `EchoClient` a `Microsoft.ServiceBus.Samples`.
1. Installare il [pacchetto NuGet del bus di servizio](https://www.nuget.org/packages/WindowsAzure.ServiceBus):

   1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **EchoClient** e quindi selezionare **Gestisci pacchetti NuGet**.
   1. Selezionare **Sfoglia**, quindi cercare e selezionare **WindowsAzure.ServiceBus**. Selezionare **Installa** e accettare le condizioni per l'utilizzo.

      ![Installare il pacchetto del bus di servizio][4]

1. Aggiungere un'istruzione `using` per lo spazio dei nomi [System.ServiceModel](/dotnet/api/system.servicemodel) nel file *Program.cs*.

    ```csharp
    using System.ServiceModel;
    ```

1. Aggiungere la definizione del contratto di servizio allo spazio dei nomi, come mostrato nell'esempio seguente. Questa definizione è identica alla definizione usata nel progetto **Service**. Aggiungere questo codice nella parte iniziale dello spazio dei nomi `Microsoft.ServiceBus.Samples`.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Premere CTRL+MAIUSC+B per compilare il client.

### <a name="example-of-the-echoclient-project"></a>Esempio del progetto EchoClient

Il codice seguente illustra lo stato corrente del file *Program.cs* nel progetto **EchoClient**.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Configurare il client WCF

In questo passaggio si crea un file *App.config* per un'applicazione client di base che accede al servizio creato in precedenza in questa esercitazione. Il file *App.config* definisce il contratto, l'associazione e il nome dell'endpoint. Il codice usato per eseguire queste attività viene fornito nell'esempio che segue la procedura.

1. In **Esplora soluzioni** nel progetto **EchoClient** fare doppio clic su **App.config** per aprire il file nell'editor di Visual Studio.
1. Nell'elemento `<appSettings>` sostituire i segnaposto con il nome dello spazio dei nomi del servizio e la chiave di firma di accesso condiviso copiata nel passaggio precedente.
1. All'interno dell'elemento `system.serviceModel` aggiungere un elemento `<client>`.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Questo codice dichiara che si sta definendo un'applicazione client di tipo WCF.

1. Nell'elemento `client` definire il nome, il contratto e il tipo di binding per l'endpoint.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Questo codice definisce il nome dell'endpoint. Specifica anche il contratto definito nel servizio e l'uso del protocollo TCP da parte dell'applicazione client per comunicare con Inoltro di Azure. Il nome dell'endpoint viene usato nel passaggio successivo per collegare questa configurazione dell'endpoint con l'URI del servizio.

1. Selezionare **File** > **Salva tutto**.

### <a name="example-of-the-appconfig-file"></a>Esempio del file App.config

Il codice seguente mostra il file *App.config* per il client Echo.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>Implementare il client WCF

In questa sezione viene implementata un'applicazione client di base che accede al servizio creato in precedenza in questa esercitazione. Analogamente al servizio, il client esegue molte delle stesse operazioni per accedere a Inoltro di Azure:

* Imposta la modalità di connessione.
* Crea l'URI che individua il servizio host.
* Definisce le credenziali di sicurezza.
* Applica le credenziali alla connessione.
* Apre la connessione.
* Esegue attività specifiche dell'applicazione.
* Chiude la connessione.

Una delle differenze principali, tuttavia, consiste nel fatto che l'applicazione client usa un canale per connettersi al servizio di inoltro. Il servizio usa una chiamata a **ServiceHost**. Il codice usato per eseguire queste attività viene fornito nell'esempio che segue la procedura.

### <a name="implement-a-client-application"></a>Implementare un'applicazione client

1. Impostare la modalità di connessione su `AutoDetect`. Aggiungere il codice seguente nel metodo `Main()` dell'applicazione **EchoClient**.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Definire le variabile per includere i valori per lo spazio dei nomi del servizio e la chiave di firma di accesso condiviso letti dalla console.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Creare l'URI che definisce il percorso dell'host nel progetto di inoltro.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Creare l'oggetto credential per l'endpoint dello spazio dei nomi del servizio.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Creare la factory canale che carica la configurazione descritta nel file *App.config*.

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Una channel factory è un oggetto WCF che crea un canale attraverso il quale comunicano le applicazioni di servizio e client.

1. Applicare le credenziali.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Creare e aprire il canale per il servizio.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Scrivere l'interfaccia utente di base e la funzionalità per echo.

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Il codice usa l'istanza dell'oggetto canale come proxy per il servizio.

1. Chiudere il canale, quindi chiudere la factory.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>Codice di esempio per l'esercitazione

Il codice completato verrà visualizzato come indicato di seguito. Questo codice illustra come creare un'applicazione client, come chiamare le operazioni del servizio e come chiudere il client al termine della chiamata dell'operazione.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>Eseguire le applicazioni

1. Premere CTRL+MAIUSC+B per compilare la soluzione. Verranno compilati sia il progetto client che il progetto di servizio creati nei passaggi precedenti.
1. Prima di eseguire l'applicazione client, è necessario assicurarsi che l'applicazione di servizio sia in esecuzione. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla soluzione **EchoService** e selezionare **Proprietà**.
1. In **Pagine delle proprietà** scegliere **Proprietà comuni** > **Progetto di avvio** e quindi **Progetti di avvio multipli**. Verificare che **EchoService** sia visualizzato per primo nell'elenco.
1. Impostare su **Avvio** la casella **Azione** per entrambi i progetti **EchoService** ed **EchoClient**.

    ![Pagina delle proprietà del progetto][5]

1. Selezionare **Dipendenze progetto**. In **Progetti** selezionare **EchoClient**. Per **Dipendente da** verificare che sia selezionato **EchoService**.

    ![Dipendenze progetto][6]

1. Selezionare **OK** per chiudere **Pagine delle proprietà**.
1. Premere F5 per eseguire entrambi i progetti.
1. Verranno visualizzate entrambe le finestre della console e verrà richiesto il nome dello spazio dei nomi. Poiché il servizio deve essere eseguito per primo, nella finestra della console **EchoService** immettere lo spazio dei nomi e quindi premere INVIO.
1. La console richiede quindi di specificare la chiave di firma di accesso condiviso. Immettere la chiave di firma di accesso condiviso e premere INVIO.

    Ecco un esempio dell'output dalla finestra della console. I valori seguenti sono solo esempi.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    L'applicazione di servizio stampa l'indirizzo su cui è in ascolto nella finestra della console, come illustrato nell'esempio seguente.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. Nella finestra della console **EchoClient** immettere le stesse informazioni immesse precedentemente per l'applicazione di servizio. Immettere gli stessi valori di spazio dei nomi del servizio e chiave di firma di accesso condiviso per l'applicazione client.
1. Dopo l'immissione di questi valori, il client aprirà un canale per il servizio e richiederà l'immissione dello stesso testo, come mostrato nell'esempio di output della console seguente.

    `Enter text to echo (or [Enter] to exit):`

    Immettere il testo da inviare all'applicazione del servizio e premere INVIO. Il testo viene inviato al servizio tramite l'operazione del servizio Echo e viene visualizzato nella finestra della console del servizio, come illustrato nell'output di esempio seguente.

    `Echoing: My sample text`

    L'applicazione client riceve il valore restituito dell'operazione `Echo`, ovvero il testo originale, quindi lo stampa nella finestra della console. Il testo seguente è un output di esempio della finestra della console client.

    `Server echoed: My sample text`

1. È possibile continuare a inviare messaggi di testo dal client al servizio in questo modo. Al termine, premere INVIO nelle finestre della console client e del servizio per terminare entrambe le applicazioni.

## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione seguente:

> [!div class="nextstepaction"]
>[Esporre un servizio REST WCF locale a un client esterno alla rete](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
