<properties
   pageTitle="Esercitazione sull'interfaccia REST del bus di servizio usando la messaggistica inoltrata | Microsoft Azure"
   description="Compilare una semplice applicazione host di Inoltro del bus di servizio che espone un'interfaccia basata su REST."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/14/2015"
   ms.author="sethm" />

# Esercitazione sull'interfaccia REST del bus di servizio

In questa esercitazione viene descritto come creare una semplice applicazione host del bus di servizio che espone un'interfaccia basata su REST. REST consente ai client Web, ad esempio un Web browser, di accedere all'API del bus di servizio tramite richieste HTTP.

In questa esercitazione viene utilizzato il modello di programmazione REST di Windows Communication Foundation (WCF) per creare un servizio REST nel bus di servizio. Per ulteriori informazioni, vedere [Modello di programmazione REST di WCF](https://msdn.microsoft.com/library/bb412169.aspx) e [Progettazione e implementazione di servizi](https://msdn.microsoft.com/library/ms729746.aspx) nella documentazione di WCF.

## Passaggio 1: iscriversi a un account Azure

Il primo passaggio consiste nel creare uno spazio dei nomi del servizio e nell'ottenere una chiave di firma di accesso condiviso. Uno spazio dei nomi fornisce un limite per ogni applicazione esposta tramite il bus di servizio. Una chiave di firma di accesso condiviso viene generata dal sistema quando viene creato uno spazio dei nomi del servizio. La combinazione di spazio dei nomi servizio e chiave di firma di accesso condiviso fornisce le credenziali che consentono al bus di servizio di autenticare l'accesso a un'applicazione.

### Per creare uno spazio dei nomi del servizio e ottenere una chiave di firma di accesso condiviso

1. Per creare uno spazio dei nomi del servizio, visitare il [portale di Azure classico][]. Fare clic su **Bus di servizio** sul lato sinistro, quindi fare clic su **Crea**. Digitare un nome per lo spazio dei nomi, quindi fare clic sul segno di spunta.

2. Nella finestra principale del portale fare clic sul nome dello spazio dei nomi del servizio creato nel passaggio precedente.

3. Fare clic su **Configura** per visualizzare i criteri di accesso condivisi per lo spazio dei nomi.

4. Prendere nota della chiave primaria per i criteri **RootManageSharedAccessKey** oppure copiarla negli Appunti. Questo valore verrà usato più avanti nell'esercitazione.

## Passaggio 2: definire un contratto di servizio WCF basato su REST da utilizzare con il bus di servizio

Come con altri servizi del bus di servizio, quando si crea un servizio basato su REST, è necessario definire il contratto. Il contratto specifica quali operazioni sono supportate dall'host. Un'operazione di servizio può essere considerata come un metodo del servizio Web. I contratti vengono creati definendo un'interfaccia C++, C# o Visual Basic. Ogni metodo dell'interfaccia corrisponde a un'operazione di servizio specifico. L'attributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) deve essere applicato a ogni interfaccia e l'attributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) deve essere applicato a ogni operazione. Se un metodo di un'interfaccia che dispone di [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) non dispone di [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), il metodo non viene esposto. Il codice usato per eseguire queste attività viene illustrato nell'esempio che segue la procedura.

La differenza principale tra un contratto del bus di servizio di base e un contratto di tipo REST è costituita dall'aggiunta di una proprietà per [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Questa proprietà consente di eseguire il mapping di un metodo dell'interfaccia a un metodo su altro lato dell'interfaccia. In tal caso, si userà [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) per collegare un metodo a HTTP GET. In questo modo il bus di servizio può recuperare e interpretare in modo accurato i comandi inviati all'interfaccia.

### Per creare un contratto di bus di servizio con un'interfaccia

1. Aprire Visual Studio come amministratore: fare clic con il pulsante destro del mouse sul programma nel menu **Start** e quindi fare clic su **Esegui come amministratore**.

2. Creare un nuovo progetto di applicazione console. Scegliere il menu **File**, selezionare **Nuovo** e quindi **Progetto**. Nella finestra di dialogo **Nuovo progetto** fare clic su **Visual C#** (se **Visual C#** non è visibile, cercare in **Altri linguaggi**), selezionare il modello **Applicazione console** e denominarlo **ImageListener**. Usare il valore predefinito del campo **Posizione**. Fare clic su **OK** per creare il progetto.

3. Per un progetto C#, Visual Studio crea un file `Program.cs`. Questa classe contiene un metodo `Main()` vuoto, necessario per una corretta compilazione del progetto di applicazione console.

4. Aggiungere un riferimento a **System.ServiceModel.dll** nel progetto:

	a. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella **Riferimenti** nella cartella di progetto e quindi fare clic su **Aggiungi riferimento**.

	b. Fare clic sulla scheda **.NET** nella finestra di dialogo **Aggiungi riferimento** e scorrere verso il basso fino a visualizzare **System.ServiceModel**. Selezionarlo, quindi fare clic su **OK**.

5. Ripetere il passaggio precedente per aggiungere un riferimento all’assembly **System.ServiceModel.Web.dll**.

6. Aggiungere le istruzioni `using` per gli spazi dei nomi **System.ServiceModel**, **System.ServiceModel.Channels**, **System.ServiceModel.Web** e **System.IO**.

	```c
  	using System.ServiceModel;
  	using System.ServiceModel.Channels;
  	using System.ServiceModel.Web;
  	using System.IO;
	```

	[System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) è lo spazio dei nomi che consente l'accesso a livello di codice alle funzionalità di base di WCF. Il bus di servizio utilizza molti degli oggetti e degli attributi di WCF per definire i contratti di servizio. Questo spazio dei nomi viene utilizzato nella maggior parte delle applicazioni di inoltro del bus di servizio. Analogamente, [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) consente di definire il canale, ossia l'oggetto usato per comunicare con il bus di servizio e il Web browser client. Infine, [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) contiene i tipi che consentono di creare applicazioni basate sul Web.

7. Rinominare lo spazio dei nomi per il programma dal valore predefinito di Visual Studio in **Microsoft.ServiceBus.Samples**.

 	```
	namespace Microsoft.ServiceBus.Samples
	{
		...
	```

8. Subito dopo la dichiarazione dello spazio dei nomi, definire una nuova interfaccia denominata **IImageContract** e applicare l'attributo **ServiceContractAttribute** all'interfaccia con un valore `http://samples.microsoft.com/ServiceModel/Relay/`. Il valore dello spazio dei nomi è diverso dallo spazio dei nomi usato nell'ambito del codice. Il valore dello spazio dei nomi viene utilizzato come identificatore univoco per questo contratto e deve disporre delle informazioni sulla versione. Per altre informazioni, vedere [Controllo delle versioni del servizio](http://go.microsoft.com/fwlink/?LinkID=180498). Specificando lo spazio dei nomi in modo esplicito si impedisce che il valore predefinito dello spazio dei nomi venga aggiunto al nome del contratto.

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

10. Accanto all'attributo **OperationContract** applicare l'attributo **WebGet**.

	```
	public interface IImageContract
	{
		[OperationContract, WebGet]
		Stream GetImage();
	}
	```

	Ciò consente al bus di servizio di indirizzare le richieste HTTP GET a **GetImage** e di convertire i valori restituiti di **GetImage** in una risposta HTTP GETRESPONSE. Più avanti nell'esercitazione si utilizzerà un Web browser per accedere a questo metodo e per visualizzare l'immagine nel browser.

11. Subito dopo la definizione di `IImageContract`, dichiarare un canale che eredita dalle interfacce `IImageContract` e `IClientChannel`.

	```
	[ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	public interface IImageContract
	{
		[OperationContract, WebGet]
		Stream GetImage();
	}

	public interface IImageChannel : IImageContract, IClientChannel { }
	```

	Un canale è l'oggetto WCF tramite il quale il servizio e il client si scambiano le informazioni. In seguito verrà creato il canale nell'applicazione host. Il bus di servizio utilizza questo canale per passare le richieste HTTP GET dal browser per all’implementazione di **GetImage**. Il bus di servizio usa il canale anche per richiedere il valore restituito **GetImage** e convertirlo in una risposta HTTP GETRESPONSE per il browser client.

12. Dal menu **Compila** menu, fare clic su **Compila soluzione** per verificare la correttezza del lavoro svolto finora.

### Esempio

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

## Passaggio 3: implementare un contratto di servizio WCF basato su REST per utilizzare il bus di servizio

La creazione di un servizio basato su REST del bus di servizio, è necessario creare innanzitutto il contratto, il quale viene definito utilizzando un'interfaccia. Il passaggio successivo consiste nell'implementazione dell'interfaccia. Ciò comporta la creazione di una classe denominata **ImageService** che implementa l’interfaccia **IImageContract** definita dall'utente. Dopo aver implementato il contratto, è quindi necessario configurare l'interfaccia utilizzando un file App.config. Il file di configurazione contiene le informazioni necessarie per l'applicazione, ad esempio il nome del servizio, il nome del contratto e il tipo di protocollo utilizzato per comunicare con il bus di servizio. Il codice usato per eseguire queste attività viene fornito nell'esempio che segue la procedura.

Come per i passaggi precedenti, è impercettibile la differenza tra l'implementazione di un contratto di tipo REST e di un contratto di base del bus di servizio.

### Per implementare un contratto del bus di servizio di tipo REST

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

	Come accennato in precedenza, questo spazio dei nomi non è uno spazio dei nomi tradizionale. Infatti, fa parte dell'architettura WCF che identifica il contratto. Per ulteriori informazioni, vedere l’argomento [Nomi di contratto dati](https://msdn.microsoft.com/library/ms731045.aspx) nella documentazione di WCF.

3. Aggiungere un'immagine con estensione jpg al progetto.

	Si tratta di un'immagine che viene visualizzata nel browser di ricezione dal servizio. Fare clic con il pulsante destro del mouse sul progetto e quindi scegliere **Aggiungi**. Fare quindi clic su **Elemento esistente**. Usare la finestra di dialogo **Aggiungi elemento esistente** per trovare un'immagine con estensione jpg appropriata e quindi fare clic su **Aggiungi**.

	Quando si aggiunge il file, assicurarsi che **Tutti i file** sia selezionato nell'elenco a discesa accanto al campo **Nome file**. Nella parte restante di questa esercitazione si presuppone che il nome dell'immagine sia "image.jpg". Se si dispone di un file diverso, è necessario rinominare l'immagine o modificare il codice per compensare.

4. Per assicurarsi che il servizio in esecuzione trovi il file di immagine, in **Esplora soluzioni** fare clic con il pulsante destro del mouse sul file di immagine. Nel riquadro **Proprietà**, impostare il valore di **Copia in directory di output** su **Copia se più recente**.

5. Aggiungere al progetto i riferimenti agli assembly **System.Drawing.dll**, **Serialization** e **Microsoft.ServiceBus.dll** e aggiungere anche le seguenti istruzioni `using` associate.

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

7. Direttamente dopo il codice precedente aggiungere il metodo **GetImage** seguente nella classe **ImageService** per restituire un messaggio HTTP che contiene l'immagine.

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

	Questa implementazione utilizza **MemoryStream** per recuperare l'immagine e prepararla per la trasmissione in streaming al browser. Avvia la posizione del flusso da zero, dichiara il contenuto di flusso come jpeg e trasmette le informazioni.

8. Dal menu **Compila** scegliere **Compila soluzione**.

### Per definire la configurazione per l'esecuzione del servizio Web sul bus di servizio

1. Fare clic con il pulsante destro del mouse sul progetto **ImageListener**. Fare clic su **Aggiungi**, quindi su **Nuovo elemento**.

2. In **Esplora soluzioni** fare doppio clic su **App.config**, che attualmente contiene gli elementi XML seguenti.

	```
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	</configuration>
	```

	Il file di configurazione è simile a un file di configurazione WCF e include il nome del servizio, l’endpoint (ovvero il percorso esposto dal bus di servizio per le comunicazioni tra client e host) e l’associazione (il tipo di protocollo utilizzato per la comunicazione). La differenza principale è che l'endpoint di servizio configurato fa riferimento a un'associazione [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) che non fa parte di .NET Framework. Per altre informazioni su come configurare un'applicazione del bus di servizio, vedere [Configurazione di un servizio WCF da registrare con il bus di servizio](https://msdn.microsoft.com/library/ee173579.aspx).


3. Aggiungere un elemento XML `<system.serviceModel>` al file App.config. Si tratta di un elemento WCF che definisce uno o più servizi. In questo caso, viene utilizzato per definire il nome del servizio e l'endpoint.

	```
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
		<system.serviceModel>

		</system.serviceModel>

	</configuration>
	```

4. All'interno dell'elemento `system.serviceModel` aggiungere un elemento `<bindings>` che include il contenuto seguente. In tal modo, vengono definite le associazioni utilizzate nell'applicazione. È possibile definire più associazioni, ma per questa esercitazione se ne definisce una sola.

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

5. Dopo l'elemento `<bindings>` aggiungere un elemento `<services>`. Come per le associazioni, è possibile definire più servizi in un solo file di configurazione. Tuttavia, per questa esercitazione, se ne definisce solo uno.

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

	Questo passaggio consente di configurare un servizio che usa il valore predefinito **webHttpRelayBinding** definito in precedenza. Si usa anche il valore predefinito **sbTokenProvider**, definito nel passaggio successivo.

6. Dopo l'elemento `<services>` creare un elemento `<behaviors>` con il contenuto seguente, sostituendo "SAS_KEY" con la chiave di *firma di accesso condiviso* ottenuta dal [portale di Azure classico][] nel passaggio 1.

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

7. Scegliere **Compila soluzione** dal menu **Compila** per compilare l'intera soluzione.

### Esempio

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
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <bindings>
      <!-- Application Binding -->
      <webHttpRelayBinding>
        <binding name="default">
          <!-- Turn off client authentication so that client does not need to present credential through browser or fiddler -->
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
              <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
            </tokenProvider>
          <transportClientEndpointBehavior>
        </behavior>
      </endpointBehaviors>
      <serviceBehaviors>
        <behavior name="default">
          <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
        </behavior>
      </serviceBehaviors>
    </behaviors>

  </system.serviceModel>
</configuration>
```

## Passaggio 4: ospitare il servizio WCF basato su REST per usare il bus di servizio

In questo passaggio viene descritto come eseguire un servizio Web utilizzando un'applicazione console nel bus di servizio. Un elenco completo del codice scritto in questo passaggio viene fornito nell’esempio che segue la procedura.

### Per creare un indirizzo di base per il servizio

1. Nella dichiarazione della funzione `Main()` creare una variabile per archiviare lo spazio dei nomi del progetto del bus di servizio.

	```
	string serviceNamespace = "InsertServiceNamespaceHere";
	```
	Il bus di servizio utilizza il nome dello spazio dei nomi per creare un URI univoco.

2. Creare un'istanza di `Uri` per l'indirizzo di base del servizio basato sullo spazio dei nomi.

	```
	Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
	```

### Per creare e configurare l'host del servizio Web

- Creare l'host del servizio Web utilizzando l'indirizzo URI creato precedentemente in questa sezione.

	```
	WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
	```
	L'host del servizio è l'oggetto WCF che crea un'istanza dell'applicazione host. In questo esempio viene passato il tipo di host da creare (**ImageService**), nonché l'indirizzo al quale si vuole esporre l'applicazione host.

### Per eseguire l'host del servizio Web

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

	```c
	host.Close();
	```

## Esempio

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

### Compilazione del codice

Dopo aver compilato la soluzione, effettuare le operazioni seguenti per eseguire l'applicazione:

1. Dal prompt dei comandi, eseguire il servizio (ImageListener\bin\Debug\ImageListener.exe).

2. Copiare e incollare l'indirizzo dal prompt dei comandi in un browser per visualizzare l'immagine.

## Passaggi successivi

A questo punto, dopo aver creato un'applicazione che utilizza il servizio di inoltro del bus di servizio, vedere gli articoli seguenti per ulteriori informazioni sulla messaggistica inoltrata:

- [Panoramica dell'architettura del bus di servizio di Azure](service-bus-fundamentals-hybrid-solutions.md#relays)

- [Come usare il servizio Inoltro del bus di servizio](service-bus-dotnet-how-to-use-relay.md)

[portale di Azure classico]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0121_2016-->