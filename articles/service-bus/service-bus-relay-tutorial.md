<properties 
    pageTitle="Esercitazione sulla messaggistica inoltrata del bus di servizio | Microsoft Azure"
    description="Creare un'applicazione client e un servizio del bus di servizio tramite la messaggistica inoltrata del bus di servizio."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="05/17/2016"
    ms.author="sethm" />

# Esercitazione sulla messaggistica inoltrata del bus di servizio

Questa esercitazione descrive come compilare una semplice applicazione client e di servizio del bus di servizio usando le relative funzionalità di "inoltro". Per un'esercitazione simile che usa la [messaggistica negoziata](service-bus-messaging-overview.md#Brokered-messaging) del bus di servizio, vedere [Esercitazione sulla messaggistica negoziata del bus di servizio .NET](service-bus-brokered-tutorial-dotnet.md).

L'esecuzione di questa esercitazione consente di acquisire una comprensione dei passaggi necessari per creare un'applicazione client e di servizio del bus di servizio. Come per le rispettive controparti WCF, un servizio è un costrutto che espone uno o più endpoint, ognuno dei quali espone a sua volta una o più operazioni del servizio. L'endpoint di un servizio specifica un indirizzo in cui è disponibile il servizio, un binding che contiene le informazioni che un client deve comunicare al servizio e un contratto che definisce la funzionalità fornita dal servizio ai propri client. La differenza principale tra un servizio WCF e un servizio del bus di servizio riguarda l'esposizione dell'endpoint nel cloud invece che localmente nel computer.

Dopo aver eseguito la sequenza di argomenti in questa esercitazione, si avrà un servizio in esecuzione e di un client in grado di richiamare le operazioni del servizio. Il primo argomento descrive come configurare un account. I passaggi successivi descrivono come definire un servizio che usa un contratto, come implementare il servizio e come configurarlo nel codice. Descrivono anche come ospitare ed eseguire il servizio. Il servizio creato è self-hosted e il client e il servizio vengono eseguiti nello stesso computer. È possibile configurare il servizio usando il codice o un file di configurazione.

I tre passaggi finali descrivono come creare un'applicazione client, configurare l'applicazione client e creare e usare un client in grado di accedere alla funzionalità dell'host..

Tutti gli argomenti inclusi in questa sezione presuppongono l'uso di Visual Studio come ambiente di sviluppo.

## Iscriversi per ottenere un account

Il primo passaggio consiste nel creare uno spazio dei nomi del servizio per il bus di servizio e nell'ottenere una chiave di firma di accesso condiviso. Uno spazio dei nomi fornisce un limite per ogni applicazione esposta tramite il bus di servizio. La combinazione di spazio dei nomi e chiave di firma di accesso condiviso fornisce le credenziali che consentono al bus di servizio di autenticare l'accesso a un'applicazione.

1. Per creare uno spazio dei nomi, visitare il [portale di Azure classico][]. Fare clic su **Bus di servizio** sul lato sinistro, quindi fare clic su **Crea**. Digitare un nome per lo spazio dei nomi, accettare le impostazioni predefinite per tutti gli altri valori e quindi fare clic sul segno di spunta OK.

	>[AZURE.NOTE] Non è necessario usare lo stesso spazio dei nomi per le applicazioni client e per le applicazioni del servizio.

	![][4]

1. Nella finestra principale del portale fare clic sul nome dello spazio dei nomi creato nel passaggio precedente.

2. Fare clic sulla scheda **Configura** per visualizzare le chiavi e i criteri di accesso condiviso predefiniti per lo spazio dei nomi.

	![][1]

3. Prendere nota della chiave primaria per i criteri **RootManageSharedAccessKey** oppure copiarla negli Appunti. Questo valore verrà usato più avanti nell'esercitazione.

## Definire un contratto di servizio WCF basato su REST da usare con il bus di servizio

Il contratto di servizio specifica le operazioni (terminologia dei servizi Web per indicare metodi o funzioni) supportate dal servizio. I contratti vengono creati definendo un'interfaccia C++, C# o Visual Basic. Ogni metodo dell'interfaccia corrisponde a un'operazione di servizio specifico. A ogni interfaccia deve essere applicato l'attributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) e a ogni operazione deve essere applicato l'attributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx). Se un metodo di un'interfaccia con l'attributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) non ha l'attributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), il metodo non viene esposto. Il codice per eseguire queste attività viene fornito nell'esempio che segue la procedura. Per una descrizione più ampia dei contratti e dei servizi, vedere [Progettazione e implementazione di servizi](https://msdn.microsoft.com/library/ms729746.aspx) nella documentazione di WCF.

### Per creare un contratto di bus di servizio con un'interfaccia

1. Aprire Visual Studio come amministratore. Fare clic con il pulsante destro del mouse sul programma nel menu **Start** e quindi scegliere **Esegui come amministratore**.

2. Creare un nuovo progetto di applicazione console. Fare clic sul menu**File**, selezionare **Nuovo**, quindi fare clic su **Progetto**. Nella finestra di dialogo **Nuovo progetto** fare clic su **Visual C#** (se **Visual C#** non è visualizzato, vedere in **Altri linguaggi**). Fare clic sul modello **Applicazione console** e denominarlo **EchoService**. Fare clic su **OK** per creare il progetto.

	![][2]

3. Installare il pacchetto NuGet del bus di servizio. Questo pacchetto aggiunge automaticamente riferimenti alle librerie del bus di servizio, oltre all'oggetto **System.ServiceModel** di WCF. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) è lo spazio dei nomi che consente l'accesso a livello di codice alle funzionalità di base di WCF. Il bus di servizio usa molti degli oggetti e degli attributi di WCF per definire i contratti di servizio.

	In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet per la soluzione**. Fare clic sulla scheda **Sfoglia** e quindi cercare `Microsoft Azure Service Bus`. Assicurarsi che il nome del progetto sia selezionato nella casella **Versione/i**. Fare clic su **Installa** e accettare le condizioni per l'utilizzo.

	![][3]

3. In Esplora soluzioni fare doppio clic sul file Program.cs per aprirlo nell'editor, se non è già aperto.

4. Aggiungere le istruzioni using seguenti all'inizio del file:

	```
	using System.ServiceModel;
	using Microsoft.ServiceBus;
	```

1. Modificare il nome predefinito dello spazio dei nomi **EchoService** in **Microsoft.ServiceBus.Samples**.

	>[AZURE.IMPORTANT] Questa esercitazione usa lo spazio dei nomi C# **Microsoft.ServiceBus.Samples**, ovvero lo spazio dei nomi del tipo gestito tramite contratto usato nel file di configurazione nel passaggio [Configurare il client WCF](#configure-the-wcf-client). È possibile specificare qualsiasi spazio dei nomi quando si crea questo esempio. L'esercitazione tuttavia non funzionerà a meno che gli spazi dei nomi del contratto e del servizio non vengano modificati di conseguenza nel file di configurazione dell'applicazione. Lo spazio dei nomi specificato nel file App.config deve essere lo stesso specificato nei file C#.

1. Subito dopo la dichiarazione dello spazio dei nomi `Microsoft.ServiceBus.Samples`, ma sempre all'interno dello spazio dei nomi, definire una nuova interfaccia denominata `IEchoContract` e applicare l'attributo `ServiceContractAttribute` all'interfaccia con un valore dello spazio dei nomi **http://samples.microsoft.com/ServiceModel/Relay/**. Il valore dello spazio dei nomi è diverso dallo spazio dei nomi usato nell'ambito del codice. Il valore dello spazio dei nomi viene invece usato come identificatore univoco per questo contratto. Specificando lo spazio dei nomi in modo esplicito si impedisce che il valore predefinito dello spazio dei nomi venga aggiunto al nome del contratto.

	```
	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	public interface IEchoContract
	{
	}
	```

	>[AZURE.NOTE] Lo spazio dei nomi del contratto del servizio include in genere uno schema di denominazione che contiene informazioni sulla versione. Se si includono le informazioni sulla versione nello spazio dei nomi del contratto di servizio, si consente ai servizi di isolare le modifiche principali definendo un nuovo contratto di servizio con un nuovo spazio dei nomi ed esponendolo in un nuovo endpoint. In questo modo i client possono continuare a usare il contratto del servizio precedente senza doverli aggiornare. Le informazioni sulla versione possono essere costituite da una data o da un numero di build. Per altre informazioni, vedere [Controllo delle versioni del servizio](http://go.microsoft.com/fwlink/?LinkID=180498). Ai fini di questa esercitazione, lo schema di denominazione dello spazio dei nomi del contratto del servizio non include informazioni sulla versione.

1. Nell'interfaccia `IEchoContract` dichiarare un metodo per la singola operazione esposta dal contratto `IEchoContract` nell'interfaccia e applicare l'attributo `OperationContractAttribute` al metodo da esporre come parte del contratto pubblico del bus di servizio.

	```
	[OperationContract]
	string Echo(string text);
	```

1. Direttamente dopo la definizione dell'interfaccia `IEchoContract` dichiarare un canale che eredita dalle interfacce `IEchoContract` e `IClientChannel`, come illustrato di seguito:

	```
    public interface IEchoChannel : IEchoContract, IClientChannel { }
	```

	Un canale è l'oggetto WCF attraverso il quale l'host e il client si scambiano informazioni. Successivamente si scriverà il codice per il canale per ripetere le informazioni tra le due applicazioni.

1. Scegliere **Compila soluzione** dal menu **Compila** o premere **CTRL+MAIUSC+B** per confermare la correttezza di quanto fatto finora.

### Esempio

L'esempio di codice seguente illustra un'interfaccia di base che definisce un contratto del bus di servizio.

```
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

## Implementare il contratto WCF per l'uso del bus di servizio

La creazione di un inoltro del bus di servizio deve essere preceduta dalla creazione del contratto, che viene definito tramite un'interfaccia. Per altre informazioni sulla creazione dell'interfaccia, vedere il passaggio precedente. Il passaggio successivo consiste nell'implementazione dell'interfaccia. Questa operazione richiede la creazione di una classe denominata `EchoService` t che implementa l'interfaccia `IEchoContract` definita dall'utente. Dopo l'implementazione dell'interfaccia, si procede alla sua configurazione usando un file di configurazione App.config. Il file di configurazione contiene le informazioni necessarie per l'applicazione, ad esempio il nome del servizio, il nome del contratto e il tipo di protocollo utilizzato per comunicare con il bus di servizio. Il codice usato per eseguire queste attività viene fornito nell'esempio che segue la procedura. Per una descrizione di carattere generale dell'implementazione di un contratto di servizio, vedere [Implementazione dei contratti di servizio](https://msdn.microsoft.com/library/ms733764.aspx) nella documentazione di WCF.

1. Creare una nuova classe denominata `EchoService` direttamente dopo la definizione dell’interfaccia `IEchoContract`. La classe `EchoService` implementa l'interfaccia `IEchoContract`. 

	```
	class EchoService : IEchoContract
	{
	}
	```
	
	Analogamente ad altre implementazioni di interfaccia, è possibile implementare la definizione in un altro file. Tuttavia, per questa esercitazione l'implementazione si trova nello stesso file che contiene la definizione di interfaccia e il metodo `Main`.

1. Applicare l'attributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) all'interfaccia `IEchoContract`. L'attributo specifica lo spazio dei nomi e il nome del servizio. Al termine dell'operazione, la classe `EchoService` ha un aspetto simile al seguente:

	```
	[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	class EchoService : IEchoContract
	{
	}
	```

1. Implementare il metodo `Echo` definito nell'interfaccia `IEchoContract` nella classe `EchoService`.

	```
	public string Echo(string text)
	{
    	Console.WriteLine("Echoing: {0}", text);
    	return text;
	}
	```

1. Scegliere **Compila soluzione** dal menu **Compila** per verificare la correttezza del lavoro.

### Per definire la configurazione dell'host del servizio

1. Il file di configurazione è molto simile a un file di configurazione WCF. Include il nome del servizio, l'endpoint (ovvero il percorso esposto dal bus di servizio per le comunicazioni tra client e host) e il binding (ossia il tipo di protocollo usato per la comunicazione). La differenza principale consiste nel fatto che l'endpoint di servizio configurato fa riferimento a un'associazione [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) che non fa parte di .NET Framework. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) è uno dei binding definiti dal bus di servizio.

1. In Esplora soluzioni fare doppio clic sul file **App.config** per aprirlo nell'editor di Visual Studio.

2. Nell'elemento `<appSettings>` sostituire i segnaposto con il nome dello spazio dei nomi del servizio e la chiave di firma di accesso condiviso copiata nel passaggio precedente.

1. All'interno dei tag `<system.serviceModel>` aggiungere un elemento `<services>`. È possibile definire più applicazioni del bus di servizio in un singolo file di configurazione. In questa esercitazione ne viene tuttavia definita solo una.
 
	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	    <services>

	    </services>
	  </system.serviceModel>
	</configuration>
	```

1. Nell'elemento `<services>` aggiungere un elemento `<service>` per definire il nome del servizio.

	```
	<service name="Microsoft.ServiceBus.Samples.EchoService">
	</service>
	```

1. Nell'elemento `<service>` definire la posizione del contratto, nonché il tipo di binding per l'endpoint.

	```
	<endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
	```

	L'endpoint definisce la posizione in cui il client cercherà l'applicazione host. Successivamente nell'esercitazione si userà questo passaggio per creare un URI che espone completamente l'host tramite il bus di servizio. L'associazione dichiara che come protocollo per le comunicazioni con il bus di servizio si sta usando TCP.

1. Scegliere **Compila soluzione** dal menu **Compila** per verificare la correttezza del lavoro.

### Esempio

Il codice seguente illustra l'implementazione del contratto di servizio.

```
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

```
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

## Ospitare ed eseguire un servizio Web di base per la registrazione con il bus di servizio

Questo passaggio descrive come eseguire un servizio di base del bus di servizio.

### Per creare le credenziali del bus di servizio

1. In `Main()` creare due variabili in cui archiviare lo spazio dei nomi e la chiave di firma di accesso condiviso letti dalla finestra della console.

	```
	Console.Write("Your Service Namespace: ");
	string serviceNamespace = Console.ReadLine();
	Console.Write("Your SAS key: ");
	string sasKey = Console.ReadLine();
	```

	La chiave di firma di accesso condiviso verrà usata in seguito per accedere al progetto del bus di servizio. Lo spazio dei nomi viene passato come parametro a `CreateServiceUri` per creare un URI del servizio.

4. Tramite un oggetto [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx) dichiarare che come tipo di credenziali si userà una chiave di firma di accesso condiviso. Aggiungere il codice seguente direttamente dopo il codice aggiunto nel passaggio precedente.

	```
	TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
	sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
	```

### Per creare un indirizzo di base per il servizio

1. Dopo il codice aggiunto nel passaggio precedente, creare un'istanza di `Uri` per l'indirizzo di base del servizio. L'URI specifica lo schema del bus di servizio, lo spazio dei nomi e il percorso dell'interfaccia del servizio.

	```
	Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
	```

	"sb" è l'abbreviazione dello schema del bus di servizio e indica che si usa il protocollo TCP. Il protocollo è stato indicato anche in precedenza nel file di configurazione, quando come binding si è specificato [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx).
	
	Per questa esercitazione l'URI è `sb://putServiceNamespaceHere.windows.net/EchoService`.

### Per creare e configurare l'host del servizio

1. Impostare la modalità di connessione su `AutoDetect`.

	```
	ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
	```

	La modalità di connessione descrive il protocollo usato dal servizio per comunicare con il bus di servizio, ovvero HTTP o TCP. Quando si usa l'impostazione predefinita `AutoDetect`, il servizio prova a connettersi al bus di servizio tramite TCP, se disponibile, e tramite HTTP se TCP non è disponibile. Si noti che questo protocollo è diverso da quello specificato dal servizio per la comunicazione client. Il protocollo dipende dal binding usato. Ad esempio, un servizio può usare il binding [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) che indica che il relativo endpoint (esposto nel bus di servizio) comunica con i client tramite HTTP. Lo stesso servizio può specificare **ConnectivityMode.AutoDetect** per indicare che il servizio comunica con il bus di servizio tramite TCP.

1. Creare l'host del servizio usando l'URI creato precedentemente in questa sezione.

	```
	ServiceHost host = new ServiceHost(typeof(EchoService), address);
	```

	L'host del servizio è l'oggetto WCF che crea un'istanza del servizio. In questo caso, viene passato il tipo di servizio che si vuole creare (`EchoService`), insieme all'indirizzo in cui si vuole esporre il servizio.

1. All'inizio del file Program.cs aggiungere riferimenti a [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) e [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx).

	```
	using System.ServiceModel.Description;
	using Microsoft.ServiceBus.Description;
	```

1. Tornando a `Main()`, configurare l'endpoint per abilitare l'accesso pubblico.

	```
	IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
	```

	Questo passaggio segnala al bus di servizio che l'applicazione può essere trovata pubblicamente esaminando il feed ATOM del bus di servizio per il progetto. Se si imposta **DiscoveryType** su **private**, un client sarà comunque in grado di accedere al servizio. Quest'ultimo non sarà tuttavia visibile durante la ricerca nello spazio dei nomi del bus di servizio. Il client dovrà invece conoscere in anticipo il percorso dell'endpoint.

1. Applicare le credenziali del servizio agli endpoint di servizio definiti nel file App.config:

	```
	foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
	{
	    endpoint.Behaviors.Add(serviceRegistrySettings);
	    endpoint.Behaviors.Add(sasCredential);
	}
	```

	Come spiegato nel passaggio precedente, nel file di configurazione potrebbero essere stati dichiarati più servizi e più endpoint. In tal caso, questo codice scorrerebbe il file di configurazione per cercare tutti gli endpoint a cui applicare le credenziali. Per questa esercitazione, nel file di configurazione è tuttavia contenuto un solo endpoint.

### Per aprire l'host del servizio

1. Aprire il servizio.

	```
	host.Open();
	```

1. Informare l'utente che il servizio è in esecuzione e spiegargli come arrestarlo.

	```
	Console.WriteLine("Service address: " + address);
	Console.WriteLine("Press [Enter] to exit");
	Console.ReadLine();
	```

1. Al termine, chiudere l'host del servizio.

	```
	host.Close();
	```

1. Premere **CTRL+MAIUSC+B** per compilare il progetto.

### Esempio

L'esempio seguente include il contratto di servizio e l'implementazione dei passaggi precedenti dell'esercitazione e ospita il servizio in un'applicazione console. Compilare il seguente codice in un eseguibile denominato EchoService.exe.

```
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

            // Add the Service Bus credentials to all endpoints specified in configuration.
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

## Creare un client WCF per il contratto di servizio

Il passaggio successivo consiste nel creare un'applicazione console del bus di servizio di base e nel definire il contratto di servizio che sarà implementato nei passaggi successivi. Si noti che molti di questi passaggi sono simili ai passaggi usati per la creazione di un servizio: ovvero definizione di un contratto, modifica di un file App.config, uso delle credenziali per la connessione al bus di servizio e così via. Il codice usato per eseguire queste attività viene fornito nell'esempio che segue la procedura.

1. Creare un nuovo progetto nella soluzione Visual Studio attuale per il client eseguendo le operazioni seguenti:
	1. Nella stessa soluzione che include il servizio in Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione attuale (non sul progetto) e scegliere **Aggiungi**. Fare clic su **Nuovo progetto**.
	2. Nella finestra di dialogo **Aggiungi nuovo progetto**, fare clic su **Visual C#** (se **Visual C#** non è visibile, cercare in **Altri linguaggi**), selezionare il modello **Applicazione console** e denominarlo **EchoClient**.
	3. Fare clic su **OK**. <br />

1. In Esplora soluzioni fare doppio clic sul file Program.cs nel progetto **EchoClient** per aprirlo nell'editor, se non è già aperto.

1. Modificare il nome dello spazio dei nomi da nome predefinito di `EchoClient` a `Microsoft.ServiceBus.Samples`.

1. Installare il [pacchetto NuGet del bus di servizio](https://www.nuget.org/packages/WindowsAzure.ServiceBus). In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **EchoClient** e quindi scegliere **Gestisci pacchetti NuGet**. Fare clic sulla scheda **Sfoglia** e quindi cercare `Microsoft Azure Service Bus`. Fare clic su **Installa** e accettare le condizioni per l'utilizzo.

	![][3]

1. Aggiungere un'istruzione `using` per lo spazio dei nomi [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) nel file Program.cs.

	```
	using System.ServiceModel;
	```

1. Aggiungere la definizione del contratto di servizio allo spazio dei nomi, come mostrato nell'esempio seguente. Si noti che questa definizione è identica alla definizione usata nel progetto **Service**. È necessario aggiungere il codice alla parte iniziale dello spazio dei nomi `Microsoft.ServiceBus.Samples`.

	```
	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	public interface IEchoContract
	{
	    [OperationContract]
	    string Echo(string text);
	}

	public interface IEchoChannel : IEchoContract, IClientChannel { }
	```

1. Premere **CTRL+MAIUSC+B** per compilare il client.

### Esempio

Il codice seguente mostra lo stato attuale del file Program.cs nel progetto EchoClient.

```
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

## Configurare il client WCF

In questo passaggio si crea un file App.config per un'applicazione client di base che accede al servizio creato in precedenza in questa esercitazione. Il file App.config definisce il contratto, il binding e il nome dell'endpoint. Il codice usato per eseguire queste attività viene fornito nell'esempio che segue la procedura.

1. In Esplora soluzioni fare doppio clic su **App.config** nel progetto **EchoClient** per aprire il file nell'editor di Visual Studio.

2. Nell'elemento `<appSettings>` sostituire i segnaposto con il nome dello spazio dei nomi del servizio e la chiave di firma di accesso condiviso copiata nel passaggio precedente.

1. Nell'elemento system.serviceModel aggiungere un elemento `<client>`.

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	    <client>
	    </client>
	  </system.serviceModel>
	</configuration>
	```

	Questo passaggio dichiara che si sta definendo un'applicazione client di tipo WCF.

1. Nell'elemento `client` definire il nome, il contratto e il tipo di binding per l'endpoint.

	```
	<endpoint name="RelayEndpoint"
					contract="Microsoft.ServiceBus.Samples.IEchoContract"
					binding="netTcpRelayBinding"/>
	```

	Questo passaggio specifica il nome dell'endpoint, il contratto definito nel servizio e l'uso del protocollo TCP da parte dell'applicazione per comunicare con il bus di servizio. Il nome dell'endpoint viene usato nel passaggio successivo per collegare questa configurazione dell'endpoint con l'URI del servizio.

1. Fare clic su **File** quindi su **Salva tutto**.

## Esempio

Il codice seguente mostra il file App.config per il client Echo.

```
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

## Implementare il client WCF per chiamare il bus di servizio

In questo passaggio si implementa un'applicazione client di base che accede al servizio creato in precedenza in questa esercitazione. Analogamente al servizio, il client esegue molte delle stesse operazioni per accedere al bus di servizio:

1. Imposta la modalità di connessione.
1. Crea l'URI che individua il servizio host.
1. Definisce le credenziali di sicurezza.
1. Applica le credenziali alla connessione.
1. Apre la connessione.
1. Esegue attività specifiche dell'applicazione.
1. Chiude la connessione.

Una delle differenze principali, tuttavia, consiste nel fatto che l'applicazione client usa un canale per connettersi al bus di servizio, mentre il servizio usa una chiamata a **ServiceHost**. Il codice usato per eseguire queste attività viene fornito nell'esempio che segue la procedura.

### Per implementare un'applicazione client

1. Impostare la modalità di connessione su **AutoDetect**. Aggiungere il codice seguente nel metodo `Main()` dell'applicazione **EchoClient**.

	```
	ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
	```

1. Definire le variabile per includere i valori per lo spazio dei nomi del servizio e la chiave di firma di accesso condiviso letti dalla console.

	```
	Console.Write("Your Service Namespace: ");
	string serviceNamespace = Console.ReadLine();
	Console.Write("Your SAS Key: ");
	string sasKey = Console.ReadLine();
	```

1. Creare l'URI che definisce il percorso dell'host nel progetto del bus di servizio.

	```
	Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
	```

1. Creare l'oggetto credential per l'endpoint dello spazio dei nomi del servizio.

	```
	TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
	sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
	```

1. Creare la channel factory che carica la configurazione descritta nel file App.config.

	```
	ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
	```

	Una channel factory è un oggetto WCF che crea un canale attraverso il quale comunicano le applicazioni di servizio e client.

1. Applicare le credenziali del bus di servizio.

	```
	channelFactory.Endpoint.Behaviors.Add(sasCredential);
	```

1. Creare e aprire il canale per il servizio.

	```
	IEchoChannel channel = channelFactory.CreateChannel();
	channel.Open();
	```

1. Scrivere l'interfaccia utente di base e la funzionalità per echo.

	```
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

1. Chiudere il canale, quindi chiudere la factory.

	```
	channel.Close();
	channelFactory.Close();
	```

## Per eseguire le applicazioni

1. Premere **CTRL+MAIUSC+B** per compilare la soluzione. Verranno compilati sia il progetto client che il progetto di servizio creati nei passaggi precedenti.

2. Prima di eseguire l'applicazione client, è necessario assicurarsi che l'applicazione di servizio sia in esecuzione. In Esplora soluzioni in Visual Studio fare clic sulla soluzione **EchoService** e quindi su **Proprietà**.

3. Nella finestra di dialogo delle proprietà della soluzione fare clic su **Progetto di avvio** e quindi scegliere il pulsante **Progetti di avvio multipli**. Assicurarsi che **EchoService** sia visualizzato per primo nell'elenco.

4. Impostare su **Avvio** la casella **Azione** per entrambi i progetti **EchoService** ed **EchoClient**.

	![][5]

5. Fare clic su **Dipendenze progetto**. Nella casella **Progetti** selezionare **EchoClient**. Nella casella **Dipendente da** assicurarsi che sia selezionato **EchoService**.

	![][6]

6. Fare clic su **OK** per chiudere la finestra di dialogo **Proprietà**.

7. Premere **F5** per eseguire entrambi i progetti.

8. Verranno visualizzate entrambe le finestre della console e verrà richiesto il nome dello spazio dei nomi. Il servizio deve essere eseguito per primo. Nella finestra della console **EchoService** immettere quindi lo spazio dei nomi e premere **INVIO**.

9. Verrà quindi chiesto di specificare la chiave di firma di accesso condiviso. Immettere la chiave di firma di accesso condiviso e premere INVIO.

	Ecco un esempio dell'output dalla finestra della console. Si noti che i valori specificati hanno semplicemente scopo esemplificativo.

	`Your Service Namespace: myNamespace` `Your SAS Key: <SAS key value>`

	L'applicazione di servizio stampa l'indirizzo su cui è in ascolto nella finestra della console, come illustrato nell'esempio seguente.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/` `Press [Enter] to exit`
    
10. Nella finestra della console **EchoClient** immettere le stesse informazioni immesse in precedenza per l'applicazione di servizio. Eseguire la procedura precedente per immettere gli stessi valori di spazio dei nomi del servizio e chiave di firma di accesso condiviso per l'applicazione client.

11. Dopo l'immissione di questi valori, il client aprirà un canale per il servizio e richiederà l'immissione dello stesso testo, come mostrato nell'esempio di output della console seguente.

	`Enter text to echo (or [Enter] to exit):`

	Immettere il testo da inviare all'applicazione del servizio, quindi premere INVIO. Il testo viene inviato al servizio tramite l'operazione del servizio Echo e viene visualizzato nella finestra della console del servizio, come illustrato nell'output di esempio seguente.

	`Echoing: My sample text`

	L'applicazione client riceve il valore restituito dell'operazione `Echo`, ovvero il testo originale, quindi lo stampa nella finestra della console. Di seguito è riportato un output di esempio dalla finestra della console client.

	`Server echoed: My sample text`

12. È possibile continuare a inviare messaggi di testo dal client al servizio in questo modo. Al termine, premere INVIO nelle finestre del client e della console per terminare entrambe le applicazioni.

## Esempio

L'esempio seguente illustra come creare un'applicazione client, come chiamare le operazioni del servizio e come chiudere il client al termine della chiamata dell'operazione.

```
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

## Passaggi successivi

Questa esercitazione ha illustrato come compilare un'applicazione client e di servizio del bus di servizio usando le relative funzionalità di "inoltro". Per un'esercitazione simile che usa la [messaggistica negoziata](service-bus-messaging-overview.md#Brokered-messaging) del bus di servizio, vedere [Esercitazione sulla messaggistica negoziata del bus di servizio .NET](service-bus-brokered-tutorial-dotnet.md).

Per altre informazioni sul bus di servizio, vedere gli argomenti seguenti.

- [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
- [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
- [Architettura del bus di servizio](service-bus-architecture.md)

[portale di Azure classico]: http://manage.windowsazure.com

[1]: ./media/service-bus-relay-tutorial/service-bus-policies.png
[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[4]: ./media/service-bus-relay-tutorial/create-ns.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png

<!---HONumber=AcomDC_0601_2016-->