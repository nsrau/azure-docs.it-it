---
title: Esercitazione sull'inoltro WCF del bus di servizio di Azure | Microsoft Docs
description: Compilare un'applicazione client e un'applicazione di servizio con l'inoltro WCF.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: sethm
ms.openlocfilehash: a0b06c32cf5f154cf5eb01842d9b917dcb35f7b3
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="azure-wcf-relay-tutorial"></a>Esercitazione sull'inoltro WCF di Azure

Questa esercitazione descrive come compilare una semplice applicazione e servizio client di inoltro WCF usando Inoltro di Azure. Per un'esercitazione simile che usa la [messaggistica del bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md#brokered-messaging), vedere [Introduzione alle code del bus di servizio](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

L'esecuzione di questa esercitazione consente di acquisire una comprensione dei passaggi necessari per creare un'applicazione client e di servizio di inoltro WCF. Come per le rispettive controparti WCF originali, un servizio è un costrutto che espone uno o più endpoint, ognuno dei quali espone a sua volta una o più operazioni del servizio. L'endpoint di un servizio specifica un indirizzo in cui è disponibile il servizio, un binding che contiene le informazioni che un client deve comunicare al servizio e un contratto che definisce la funzionalità fornita dal servizio ai propri client. La differenza principale tra WCF e l'inoltro WCF riguarda l'esposizione dell'endpoint nel cloud invece che localmente nel computer.

Dopo aver eseguito la sequenza di argomenti in questa esercitazione, saranno disponibili un servizio in esecuzione e un client che potrà richiamare le operazioni del servizio. Il primo argomento descrive come configurare un account. I passaggi successivi descrivono come definire un servizio che usa un contratto, come implementare il servizio e come configurarlo nel codice. Descrivono anche come ospitare ed eseguire il servizio. Il servizio creato è self-hosted e il client e il servizio vengono eseguiti nello stesso computer. È possibile configurare il servizio usando il codice o un file di configurazione.

I tre passaggi finali descrivono come creare un'applicazione client, configurare l'applicazione client e creare e usare un client in grado di accedere alla funzionalità dell'host.

## <a name="prerequisites"></a>prerequisiti

Per completare l'esercitazione sono necessari gli elementi seguenti:

* [Microsoft Visual Studio 2015 o versione successiva](http://visualstudio.com). Questa esercitazione usa Visual Studio 2017.
* Un account Azure attivo. Se non si ha un account, è possibile crearne uno gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).

## <a name="create-a-service-namespace"></a>Creare uno spazio dei nomi del servizio

Il primo passaggio consiste nel creare uno spazio dei nomi e nell'ottenere una chiave di [firma di accesso condiviso](../service-bus-messaging/service-bus-sas.md). Uno spazio dei nomi fornisce un limite per ogni applicazione esposta tramite il servizio di inoltro. Una chiave di firma di accesso condiviso viene generata dal sistema quando viene creato uno spazio dei nomi del servizio. La combinazione di spazio dei nomi servizio e chiave di firma di accesso condiviso fornisce le credenziali che consentono ad Azure di autenticare l'accesso a un'applicazione. Seguire [queste istruzioni](relay-create-namespace-portal.md) per creare uno spazio dei nomi di inoltro.

## <a name="define-a-wcf-service-contract"></a>Definire un contratto del servizio WCF

Il contratto di servizio specifica le operazioni (terminologia dei servizi Web per indicare metodi o funzioni) supportate dal servizio. I contratti vengono creati definendo un'interfaccia C++, C# o Visual Basic. Ogni metodo dell'interfaccia corrisponde a un'operazione di servizio specifico. A ogni interfaccia deve essere applicato l'attributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) e a ogni operazione deve essere applicato l'attributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx). Se un metodo di un'interfaccia con l'attributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) non ha l'attributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), il metodo non viene esposto. Il codice per eseguire queste attività viene fornito nell'esempio che segue la procedura. Per una descrizione più ampia dei contratti e dei servizi, vedere [Progettazione e implementazione di servizi](https://msdn.microsoft.com/library/ms729746.aspx) nella documentazione di WCF.

### <a name="create-a-relay-contract-with-an-interface"></a>Creare un contratto di inoltro con un'interfaccia

1. Aprire Visual Studio con ruolo di amministratore. Fare clic con il pulsante destro del mouse sul programma nel menu **Start** e scegliere **Esegui come amministratore**.
2. Creare un nuovo progetto di applicazione console. Fare clic sul menu**File**, selezionare **Nuovo** e fare clic su **Progetto**. Nella finestra di dialogo **Nuovo progetto** fare clic su **Visual C#**. Se **Visual C#** non è visualizzato, vedere in **Altri linguaggi**. Fare clic sul modello **App console (.NET Framework)** e denominarlo **EchoService**. Fare clic su **OK** per creare il progetto.

    ![][2]

3. Installare il pacchetto NuGet del bus di servizio. Questo pacchetto aggiunge automaticamente i riferimenti alle librerie del bus di servizio, oltre all'oggetto **System.ServiceModel** di WCF. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) è lo spazio dei nomi che consente l'accesso a livello di codice alle funzionalità di base di WCF. Il bus di servizio utilizza molti degli oggetti e degli attributi di WCF per definire i contratti di servizio.

    In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e quindi scegliere **Gestisci pacchetti NuGet...**. Fare clic sulla scheda **Sfoglia** e quindi cercare **WindowsAzure.ServiceBus**. Assicurarsi che il nome del progetto sia selezionato nella casella **Versione**. Fare clic su **Installa**e accettare le condizioni per l'utilizzo.

    ![][3]
4. In Esplora soluzioni fare doppio clic sul file Program.cs per aprirlo nell'editor, se non è già aperto.
5. Aggiungere le istruzioni using seguenti all'inizio del file:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```
6. Modificare il nome predefinito dello spazio dei nomi da **EchoService** a **Microsoft.ServiceBus.Samples**.

   > [!IMPORTANT]
   > Questa esercitazione usa lo spazio dei nomi C# **Microsoft.ServiceBus.Samples**, ovvero lo spazio dei nomi del tipo gestito in base al contratto usato nel file di configurazione nel passaggio [Configurare il client WCF](#configure-the-wcf-client). È possibile specificare qualsiasi spazio dei nomi quando si crea questo esempio. L'esercitazione tuttavia non funzionerà a meno che gli spazi dei nomi del contratto e del servizio non vengano modificati di conseguenza nel file di configurazione dell'applicazione. Lo spazio dei nomi specificato nel file App.config deve essere lo stesso specificato nei file C#.
   >
   >
7. Subito dopo la dichiarazione dello spazio dei nomi `Microsoft.ServiceBus.Samples`, ma sempre nello spazio dei nomi, definire una nuova interfaccia denominata `IEchoContract` e applicare l'attributo `ServiceContractAttribute` all'interfaccia con un valore dello spazio dei nomi `http://samples.microsoft.com/ServiceModel/Relay/`. Il valore dello spazio dei nomi è diverso dallo spazio dei nomi usato nell'ambito del codice. Il valore dello spazio dei nomi viene invece usato come identificatore univoco per questo contratto. Specificando lo spazio dei nomi in modo esplicito si impedisce che il valore predefinito dello spazio dei nomi venga aggiunto al nome del contratto. Incollare il codice seguente dopo la dichiarazione dello spazio dei nomi:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

   > [!NOTE]
   > Lo spazio dei nomi del contratto del servizio include in genere uno schema di denominazione che contiene informazioni sulla versione. Se si includono le informazioni sulla versione nello spazio dei nomi del contratto di servizio, si consente ai servizi di isolare le modifiche principali definendo un nuovo contratto di servizio con un nuovo spazio dei nomi ed esponendolo in un nuovo endpoint. In questo modo i client possono continuare a usare il contratto del servizio precedente senza dover essere aggiornati. Le informazioni sulla versione possono essere costituite da una data o da un numero di build. Per altre informazioni, vedere [Controllo delle versioni del servizio](http://go.microsoft.com/fwlink/?LinkID=180498). Ai fini di questa esercitazione, lo schema di denominazione dello spazio dei nomi del contratto del servizio non include informazioni sulla versione.
   >
   >
8. Nell'interfaccia `IEchoContract` dichiarare un metodo per la singola operazione esposta dal contratto `IEchoContract` nell'interfaccia e applicare l'attributo `OperationContractAttribute` al metodo da esporre come parte del contratto pubblico di inoltro WCF, come segue:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```
9. Direttamente dopo la definizione dell'interfaccia `IEchoContract` dichiarare un canale che eredita dalle interfacce `IEchoContract` e `IClientChannel`, come illustrato di seguito:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Un canale è l'oggetto WCF attraverso il quale l'host e il client si scambiano informazioni. Successivamente si scriverà il codice per il canale per ripetere le informazioni tra le due applicazioni.
10. Scegliere **Compila soluzione** dal menu **Compila** o premere **CTRL+MAIUSC+B** per confermare la correttezza di quanto fatto finora.

### <a name="example"></a>Esempio

L'esempio di codice seguente illustra un'interfaccia di base che definisce un contratto dell'inoltro WCF.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
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

La creazione di un inoltro di Azure deve essere preceduta dalla creazione del contratto, che viene definito usando un'interfaccia. Per altre informazioni sulla creazione dell'interfaccia, vedere il passaggio precedente. Il passaggio successivo consiste nell'implementazione dell'interfaccia. Questa operazione richiede la creazione di una classe denominata `EchoService` t che implementa l'interfaccia `IEchoContract` definita dall'utente. Dopo l'implementazione dell'interfaccia, si procede alla sua configurazione usando un file di configurazione App.config. Il file di configurazione contiene le informazioni necessarie per l'applicazione, ad esempio il nome del servizio, il nome del contratto e il tipo di protocollo usato per comunicare con il servizio di inoltro. Il codice usato per eseguire queste attività viene fornito nell'esempio che segue la procedura. Per una descrizione di carattere generale sull'implementazione di un contratto di servizio, vedere [Implementazione dei contratti di servizio](https://msdn.microsoft.com/library/ms733764.aspx) nella documentazione di WCF.

1. Creare una nuova classe denominata `EchoService` direttamente dopo la definizione dell’interfaccia `IEchoContract`. La classe `EchoService` implementa l'interfaccia `IEchoContract`.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Analogamente ad altre implementazioni di interfaccia, è possibile implementare la definizione in un altro file. Tuttavia, per questa esercitazione l'implementazione si trova nello stesso file che contiene la definizione di interfaccia e il metodo `Main`.
2. Applicare l'attributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) all'interfaccia `IEchoContract`. L'attributo specifica lo spazio dei nomi e il nome del servizio. Al termine dell'operazione, la classe `EchoService` ha un aspetto simile al seguente:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```
3. Implementare il metodo `Echo` definito nell'interfaccia `IEchoContract` nella classe `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```
4. Scegliere **Compila soluzione** dal menu **Compila** per verificare la correttezza del lavoro.

### <a name="define-the-configuration-for-the-service-host"></a>Definire la configurazione dell'host del servizio

1. Il file di configurazione è molto simile a un file di configurazione WCF. Include il nome del servizio, l'endpoint (ovvero il percorso esposto dall'inoltro di Azure per le comunicazioni tra client e host) e l'associazione (ossia il tipo di protocollo usato per la comunicazione). La differenza principale consiste nel fatto che l'endpoint di servizio configurato fa riferimento a un’associazione [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) che non fa parte di .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) è una delle associazioni definite dal servizio.
2. In **Esplora soluzioni** fare doppio clic sul file App.config per aprirlo nell'editor di Visual Studio.
3. Nell'elemento `<appSettings>` sostituire i segnaposto con il nome dello spazio dei nomi del servizio e la chiave di firma di accesso condiviso copiata nel passaggio precedente.
4. All'interno dei tag `<system.serviceModel>` aggiungere un elemento `<services>`. È possibile definire più applicazioni di inoltro in un singolo file di configurazione. In questa esercitazione ne viene tuttavia definita solo una.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```
5. Nell'elemento `<services>` aggiungere un elemento `<service>` per definire il nome del servizio.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```
6. Nell'elemento `<service>` definire la posizione del contratto, nonché il tipo di binding per l'endpoint.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    L'endpoint definisce la posizione in cui il client cercherà l'applicazione host. Successivamente nell'esercitazione si userà questo passaggio per creare un URI che espone completamente l'host tramite l'inoltro di Azure. L'associazione dichiara che come protocollo per le comunicazioni con il servizio di inoltro viene usato TCP.
7. Scegliere **Compila soluzione** dal menu **Compila** per verificare la correttezza del lavoro.

### <a name="example"></a>Esempio

Il codice seguente illustra l'implementazione del contratto di servizio.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

Il codice seguente illustra il formato di base del file App.config associato all'host del servizio.

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

## <a name="host-and-run-a-basic-web-service-to-register-with-the-relay-service"></a>Ospitare ed eseguire un servizio Web di base per la registrazione con il servizio di inoltro

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
2. Tramite un oggetto [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) dichiarare che come tipo di credenziali si userà una chiave di firma di accesso condiviso. Aggiungere il codice seguente direttamente dopo il codice aggiunto nel passaggio precedente.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Creare un indirizzo di base per il servizio

Dopo il codice aggiunto nel passaggio precedente, creare un'istanza di `Uri` per l'indirizzo di base del servizio. L'URI specifica lo schema del bus di servizio, lo spazio dei nomi e il percorso dell'interfaccia del servizio.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

"sb" è l'abbreviazione dello schema del bus di servizio e indica che si usa il protocollo TCP. Il protocollo è stato indicato anche in precedenza nel file di configurazione, quando come associazione si è specificato [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx).

Per questa esercitazione l'URI è `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>Creare e configurare l'host del servizio

1. Impostare la modalità di connessione su `AutoDetect`.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    La modalità di connessione descrive il protocollo usato dal servizio per comunicare con il servizio di inoltro, ovvero HTTP o TCP. Quando si usa l'impostazione predefinita `AutoDetect`, il servizio prova a connettersi all'inoltro di Azure tramite TCP, se disponibile, e tramite HTTP se TCP non è disponibile. Si noti che questo protocollo è diverso da quello specificato dal servizio per la comunicazione client. Il protocollo dipende dal binding usato. Ad esempio, un servizio può usare l'associazione [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) che specifica che l'endpoint comunica con i client tramite HTTP. Lo stesso servizio può specificare **ConnectivityMode.AutoDetect** per indicare che il servizio comunica con l'inoltro di Azure tramite TCP.
2. Creare l'host del servizio usando l'URI creato precedentemente in questa sezione.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    L'host del servizio è l'oggetto WCF che crea un'istanza del servizio. In questo caso, viene passato il tipo di servizio che si vuole creare (`EchoService`), insieme all'indirizzo in cui si vuole esporre il servizio.
3. All'inizio del file Program.cs aggiungere riferimenti a [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) e [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```
4. Tornando a `Main()`, configurare l'endpoint per abilitare l'accesso pubblico.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Questo passaggio segnala al servizio di inoltro che l'applicazione può essere trovata pubblicamente esaminando il feed ATOM per il progetto. Se si imposta **DiscoveryType** su **private**, un client sarà comunque in grado di accedere al servizio. Quest'ultimo non sarà tuttavia visibile durante la ricerca nello spazio dei nomi Relay. Il client dovrà invece conoscere in anticipo il percorso dell'endpoint.
5. Applicare le credenziali del servizio agli endpoint di servizio definiti nel file App.config:

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Come spiegato nel passaggio precedente, nel file di configurazione potrebbero essere stati dichiarati più servizi e più endpoint. In tal caso, questo codice scorrerebbe il file di configurazione per cercare tutti gli endpoint a cui applicare le credenziali. Per questa esercitazione, nel file di configurazione è tuttavia contenuto un solo endpoint.

### <a name="open-the-service-host"></a>Aprire l'host del servizio

1. Aprire il servizio.

    ```csharp
    host.Open();
    ```
2. Informare l'utente che il servizio è in esecuzione e spiegargli come arrestarlo.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Al termine, chiudere l'host del servizio.

    ```csharp
    host.Close();
    ```
4. Premere **CTRL+MAIUSC+B** per compilare il progetto.

### <a name="example"></a>Esempio

Il codice del servizio completato comparirà come indicato di seguito. Il codice include il contratto di servizio e l'implementazione dei passaggi precedenti nell'esercitazione e ospita il servizio in un'applicazione console.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
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

Il passaggio successivo consiste nel creare un'applicazione client e nel definire il contratto di servizio che sarà implementato nei passaggi successivi. Si noti che molti di questi passaggi sono simili ai passaggi usati per la creazione di un servizio: ovvero definizione di un contratto, modifica di un file App.config, uso delle credenziali per la connessione al servizio di inoltro e così via. Il codice usato per eseguire queste attività viene fornito nell'esempio che segue la procedura.

1. Creare un nuovo progetto nella soluzione Visual Studio attuale per il client eseguendo le operazioni seguenti:

   1. Nella stessa soluzione che include il servizio in Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione attuale, non sul progetto, e scegliere **Aggiungi**. Fare clic su **Nuovo progetto**.
   2. Nella finestra di dialogo **Aggiungi nuovo progetto** fare clic su **Visual C#**. Se **Visual C#** non è visibile, cercare in **Other Languages** (Altri linguaggi), selezionare il modello **App console (.NET Framework)** e denominarlo **EchoClient**.
   3. Fare clic su **OK**.
      <br />
2. In Esplora soluzioni fare doppio clic sul file Program.cs nel progetto **EchoClient** per aprirlo nell'editor, se non è già aperto.
3. Modificare il nome dello spazio dei nomi da nome predefinito di `EchoClient` a `Microsoft.ServiceBus.Samples`.
4. Installare il [pacchetto NuGet del bus di servizio](https://www.nuget.org/packages/WindowsAzure.ServiceBus): in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **EchoClient** e scegliere **Gestisci pacchetti NuGet**. Fare clic sulla scheda **Sfoglia** e quindi cercare `Microsoft Azure Service Bus`. Fare clic su **Installa**e accettare le condizioni per l'utilizzo.

    ![][3]
5. Aggiungere un'istruzione `using` per lo spazio dei nomi [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) nel file Program.cs.

    ```csharp
    using System.ServiceModel;
    ```
6. Aggiungere la definizione del contratto di servizio allo spazio dei nomi, come mostrato nell'esempio seguente. Si noti che questa definizione è identica alla definizione usata nel progetto **Service**. È necessario aggiungere il codice alla parte iniziale dello spazio dei nomi `Microsoft.ServiceBus.Samples`.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```
7. Premere **CTRL+MAIUSC+B** per compilare il client.

### <a name="example"></a>Esempio

Il codice seguente illustra lo stato corrente del file Program.cs nel progetto **EchoClient**.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
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

In questo passaggio si crea un file App.config per un'applicazione client di base che accede al servizio creato in precedenza in questa esercitazione. Il file App.config definisce il contratto, il binding e il nome dell'endpoint. Il codice usato per eseguire queste attività viene fornito nell'esempio che segue la procedura.

1. In Esplora soluzioni fare doppio clic su **App.config** nel progetto **EchoClient** per aprire il file nell'editor di Visual Studio.
2. Nell'elemento `<appSettings>` sostituire i segnaposto con il nome dello spazio dei nomi del servizio e la chiave di firma di accesso condiviso copiata nel passaggio precedente.
3. Nell'elemento system.serviceModel aggiungere un elemento `<client>`.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Questo passaggio dichiara che si sta definendo un'applicazione client di tipo WCF.
4. Nell'elemento `client` definire il nome, il contratto e il tipo di binding per l'endpoint.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Questo passaggio specifica il nome dell'endpoint, il contratto definito nel servizio e l'uso del protocollo TCP da parte dell'applicazione per comunicare con l'inoltro di Azure. Il nome dell'endpoint viene usato nel passaggio successivo per collegare questa configurazione dell'endpoint con l'URI del servizio.
5. Fare clic su **File** quindi su **Salva tutto**.

## <a name="example"></a>Esempio

Il codice seguente mostra il file App.config per il client Echo.

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
In questo passaggio si implementa un'applicazione client di base che accede al servizio creato in precedenza in questa esercitazione. Analogamente al servizio, il client esegue molte delle stesse operazioni per accedere all'inoltro di Azure:

1. Imposta la modalità di connessione.
2. Crea l'URI che individua il servizio host.
3. Definisce le credenziali di sicurezza.
4. Applica le credenziali alla connessione.
5. Apre la connessione.
6. Esegue attività specifiche dell'applicazione.
7. Chiude la connessione.

Una delle differenze principali, tuttavia, consiste nel fatto che l'applicazione client usa un canale per connettersi al servizio di inoltro, mentre il servizio usa una chiamata a **ServiceHost**. Il codice usato per eseguire queste attività viene fornito nell'esempio che segue la procedura.

### <a name="implement-a-client-application"></a>Implementare un'applicazione client
1. Impostare la modalità di connessione su **AutoDetect**. Aggiungere il codice seguente nel metodo `Main()` dell'applicazione **EchoClient**.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```
2. Definire le variabile per includere i valori per lo spazio dei nomi del servizio e la chiave di firma di accesso condiviso letti dalla console.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```
3. Creare l'URI che definisce il percorso dell'host nel progetto di inoltro.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```
4. Creare l'oggetto credential per l'endpoint dello spazio dei nomi del servizio.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```
5. Creare la channel factory che carica la configurazione descritta nel file App.config.

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Una channel factory è un oggetto WCF che crea un canale attraverso il quale comunicano le applicazioni di servizio e client.
6. Applicare le credenziali.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```
7. Creare e aprire il canale per il servizio.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```
8. Scrivere l'interfaccia utente di base e la funzionalità per echo.

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

    Si noti che il codice usa l'istanza dell'oggetto canale come proxy per il servizio.
9. Chiudere il canale, quindi chiudere la factory.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

## <a name="example"></a>Esempio

Il codice completo dovrebbe apparire come mostrato di seguito e illustra come creare un'applicazione client, come chiamare le operazioni del servizio e come chiudere il client al termine della chiamata dell'operazione.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
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

1. Premere **CTRL+MAIUSC+B** per compilare la soluzione. Verranno compilati sia il progetto client che il progetto di servizio creati nei passaggi precedenti.
2. Prima di eseguire l'applicazione client, è necessario assicurarsi che l'applicazione di servizio sia in esecuzione. In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sulla soluzione **EchoService** e scegliere **Proprietà**.
3. Nella finestra di dialogo delle proprietà della soluzione fare clic su **Progetto di avvio** e quindi sul pulsante **Progetti di avvio multipli**. Verificare che **EchoService** sia visualizzato per primo nell'elenco.
4. Impostare su **Avvio** la casella **Azione** per entrambi i progetti **EchoService** ed **EchoClient**.

    ![][5]
5. Fare clic su **Dipendenze progetto**. Nella casella **Progetti** selezionare **EchoClient**. Nella casella **Dipendente da** verificare che sia selezionato **EchoService**.

    ![][6]
6. Fare clic su **OK** per chiudere la finestra di dialogo **Proprietà**.
7. Premere **F5** per eseguire entrambi i progetti.
8. Verranno visualizzate entrambe le finestre della console e verrà richiesto il nome dello spazio dei nomi. Il servizio deve essere eseguito per primo. Nella finestra della console **EchoService** immettere lo spazio dei nomi e quindi premere **INVIO**.
9. Verrà quindi chiesto di specificare la chiave di firma di accesso condiviso. Immettere la chiave di firma di accesso condiviso e premere INVIO.

    Ecco un esempio dell'output dalla finestra della console. Si noti che i valori specificati hanno semplicemente scopo esemplificativo.

    `Your Service Namespace: myNamespace``Your SAS Key: <SAS key value>`

    L'applicazione di servizio stampa l'indirizzo su cui è in ascolto nella finestra della console, come illustrato nell'esempio seguente.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/``Press [Enter] to exit`
10. Nella finestra della console **EchoClient** immettere le stesse informazioni immesse precedentemente per l'applicazione di servizio. Eseguire la procedura precedente per immettere gli stessi valori di spazio dei nomi del servizio e chiave di firma di accesso condiviso per l'applicazione client.
11. Dopo l'immissione di questi valori, il client aprirà un canale per il servizio e richiederà l'immissione dello stesso testo, come mostrato nell'esempio di output della console seguente.

    `Enter text to echo (or [Enter] to exit):`

    Immettere il testo da inviare all'applicazione del servizio, quindi premere INVIO. Il testo viene inviato al servizio tramite l'operazione del servizio Echo e viene visualizzato nella finestra della console del servizio, come illustrato nell'output di esempio seguente.

    `Echoing: My sample text`

    L'applicazione client riceve il valore restituito dell'operazione `Echo`, ovvero il testo originale, quindi lo stampa nella finestra della console. Di seguito è riportato un output di esempio dalla finestra della console client.

    `Server echoed: My sample text`
12. È possibile continuare a inviare messaggi di testo dal client al servizio in questo modo. Al termine, premere INVIO nelle finestre del client e della console per terminare entrambe le applicazioni.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come compilare un'applicazione client e di servizio di inoltro di Azure usando le funzionalità di inoltro WCF del bus di servizio. Per un'esercitazione simile che usa la [messaggistica](../service-bus-messaging/service-bus-messaging-overview.md#brokered-messaging) del bus di servizio, vedere [Introduzione alle code del bus di servizio](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Per altre informazioni sul servizio d'inoltro di Azure, vedere gli argomenti seguenti.

* [Panoramica dell'architettura del bus di servizio di Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#relays)
* [Panoramica del servizio di inoltro di Azure](relay-what-is-it.md)
* [Come usare il servizio di inoltro WCF con .NET](relay-wcf-dotnet-get-started.md)

[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
