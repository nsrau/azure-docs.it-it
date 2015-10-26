<properties
   pageTitle="Node.js e Reliable Actors | Microsoft Azure"
   description="Indicazioni su come creare un'applicazione express node.js che utilizzi Reliable Actors e venga eseguita nella piattaforma dell’infrastruttura di servizi di Azure."
   services="service-fabric"
   documentationCenter="nodejs"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/17/2015"
   ms.author="claudioc"/>


# Node.js e Reliable Actors: una combinazione vincente
In questo articolo viene fornita una panoramica su come creare un'applicazione che utilizza node.js e Reliable Actors. La soluzione finale è una combinazione di codice javascript utilizzato principalmente per fornire il lato front-end dell'applicazione (API Web/Rest) e C# per i calcoli più complessi. Sfruttando il modello di programmazione dell'infrastruttura di servizi, l'applicazione è subito scalabile e affidabile. Il processo si basa sui seguenti passaggi:

1. creare un nuovo progetto Stateless o Stateful Actor dell’infrastruttura di servizi utilizzando gli strumenti dell’infrastruttura di servizi per Visual Studio
2. creare un progetto node.js utilizzando, ad esempio [Strumenti node.js per Visual Studio](https://github.com/Microsoft/nodejstools/releases/tag/v1.1.RC) 
3. Aggiungere un progetto node.js (un’app per Basic Express, ad esempio) alla soluzione dell’infrastruttura di servizi. È possibile utilizzare semplicemente soluzioni/Aggiungi progetto esistente per includere il progetto node.js. 
4. Creare un pacchetto dell'app node.js in modo che possa essere distribuita utilizzando VS Tools per l'infrastruttura di servizi

## creare il progetto node. js
Dopo aver creato il progetto node. js e aver aggiunto le dipendenze, è necessario modificare la struttura della directory del progetto in modo che segua la struttura richiesta dagli strumenti di infrastruttura di servizi per Visual Studio per poter assemblare e distribuire l'applicazione come qualsiasi altro servizio dell’infrastruttura di servizi. L'obiettivo è apportare le modifiche alla struttura della directory una sola volta, in modo da poter utilizzare il processo di distribuzione in Visual Studio e quindi distribuire l'app node.js assieme ad altri servizi nella soluzione. Le modifiche da effettuare alla struttura della directory sono:

1. Creare una directory PackageRoot
2. Creare una directory Code in PackageRoot
3. Spostare tutti i file e le directory nella directory Code

Al termine, la struttura del progetto in Visual Studio dovrebbe avere il seguente aspetto:

![][8]

Come si può vedere, tutto il codice node.js si trova nella directory PackageRoot/Code. L’infrastruttura di servizi non conosce le applicazioni/librerie installate nel nodo in cui l'applicazione dovrà essere distribuita, quindi è necessario includere tutte le dipendenze. Nel caso di node.js, è necessario includere node.exe in modo che l'infrastruttura di servizi possa eseguire il codice (è possibile trovare node.exe nella directory files\\nodejs del programma).

![][3]

## Aggiungere il file di metadati servicemanifest.xml
Per distribuire l'applicazione node.js, è necessario aggiungere un file di metadati necessario per specificare alcune proprietà che verranno utilizzate dall'infrastruttura di servizi per determinare come distribuire e avviare l'applicazione.

Questo è un esempio dell’aspetto che servicemanifest.xml dovrebbe avere, fare riferimento a [questo articolo](service-fabric-deploy-existing-app.md) per ulteriori informazioni sull'elemento importante che deve essere aggiornato, ma in genere è necessario aggiornare:

* Nome (elemento ServiceManifest)
* ServiceTypeName (elemento StatelessServiceType)
* Argomenti (elemento ExeHost) per specificare il file js che deve essere utilizzato per avviare l'applicazione.


```xml

<?xml version="1.0" encoding="utf-8"?>

<ServiceManifest Name="NodejsFrontendPkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="NodejsFrontendType" UseImplicitHost="true" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>node.exe</Program>
        <Arguments>server.js</Arguments>
        <WorkingFolder>CodeBase</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0.0" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

> [AZURE.NOTE]Si noti che una delle opzioni di servicemanifest.xml potrebbe non funzionare con node.js. In alcuni casi `ConsoleRedirection` non funziona se un modulo node.js (ad es. Express) presuppone che l’fd per stdout e stderr sia 1 e 2.

Dopo che il file `servicemanifest.xml` è stato aggiunto al progetto di node.js, la struttura del progetto dovrebbe avere il seguente aspetto:

![][4]

## Aggiungere i file binari dell’infrastruttura di servizi
Il passaggio successivo consiste nell'aggiungere i file binari dell’infrastruttura di servizi utilizzati per la connessione agli attori in esecuzione nel cluster dell’infrastruttura di servizi. Come si vede nell'esempio Edge.js, esistono alcuni riferimenti agli assembly. Per rendere tali assembly disponibili per edge.js, è necessario copiarli con il codice node.js. Il modo più semplice consiste nel copiare i file binari da un progetto esistente, i file che devono essere inclusi nella directory del codice (e che verranno utilizzati da edge.js) sono i seguenti:

```
Microsoft.ServiceFabric.Actors.dll
Microsoft.ServiceFabric.Collections.dll
Microsoft.ServiceFabric.Data.dll
Microsoft.ServiceFabric.Data.Log.dll
Microsoft.ServiceFabric.ReplicatedStore.dll
Microsoft.ServiceFabric.Replicator.dll
Microsoft.ServiceFabric.Services.dll
ServiceFabricServiceModel.dll
System.Fabric.Common.Internal.dll
System.Fabric.Common.Internal.Strings.resources.dll
System.Fabric.dll
```

Dopo che i file binari sono stati aggiunti al progetto, la struttura del progetto sarà simile alla seguente:

![][5]

## Aggiungere un riferimento al progetto node.js nel file applicationmanifest
Il passaggio successivo consiste nell'aggiungere il servizio node.js al manifesto dell'applicazione in modo che possa essere distribuito utilizzando gli strumenti di Visual Studio per l'infrastruttura di servizi. Ciò è necessario perché non esiste alcuna integrazione negli strumenti dell’infrastruttura di servizi per Visual Studio con il progetto node.js, quindi questa dovrà essere aggiunta manualmente.

Nel file `applicationmanifest.xml` è necessario aggiungere i seguenti elementi:

* `ServiceManifestImport`
* `Service`

> [AZURE.NOTE]Assicurarsi di utilizzare gli stessi nomi utilizzati in `servicemanifest.xml` per specificare `ServiceName` e `ServiceTypeName`. Il file `applicationmanifest.xml` dovrebbe avere il seguente aspetto:

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeServiceFabricSampleApplication" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeServiceFabricSamplePkg" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="VisualObjectsNodejsWebServicePkg" ServiceManifestVersion="1.0.0.0" />
       </ServiceManifestImport>
   <DefaultServices>
      <Service Name="NodeServiceFabricSampleActorService">
         <StatelessService ServiceTypeName="NodeServiceFabricSampleActorServiceType">
            <UniformInt64Partition PartitionCount="9" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatelessService>
      </Service>
     <Service Name="NodejsFrontendPkg">
       <StatelessService ServiceTypeName="NodejsFrontendType">
         <SingletonPartition />
       </StatelessService>
     </Service>
   </DefaultServices>
</ApplicationManifest>

```

## Utilizzo di Reliable Actors nell’app node.js
Una volta impostata la struttura del progetto, è possibile focalizzare l’attenzione sul codice che consente all'applicazione node.js la connessione a Reliable Actors nel cluster come avviene per un'applicazione .NET. Lo scenario che si desidera abilitare consiste nella compilazione di un'applicazione node.js che opera come gateway/front-end per l'applicazione client e nell’esporre un'applicazione basata su Web o un set di API REST che un'applicazione client può utilizzare. Node.js fornisce il front-end dell'applicazione mentre i Reliable Actors offre il livello 'server delle applicazioni' scalabile e affidabile dell'applicazione. Nell'infrastruttura di servizi, Reliable Actors può essere richiamato utilizzando la [classe ActorProxy](service-fabric-reliable-actors-introduction.md#actor-communication). Fortunatamente esiste un modulo node.js eccezionale che può essere utilizzato per richiamare il codice .NET: [Edge.js](https://github.com/tjanczuk/edge). È possibile utilizzare le istruzioni nell'archivio github per scoprire come installare edge nel computer in uso.

 
![][2]

Dopo l'installazione di edge utilizzando NPM o l’interfaccia utente NPM in Visual Studio, è necessario spostare il nuovo modulo installato nella directory node\_modules all’interno della sottodirectory Code (abbiamo modificato la struttura del progetto di node.js e spostato tutto il codice nella directory PackageRoot/Code). È possibile trovare esempi nell'archivio di edge.js su Github per informazioni sull’utilizzo di edge per richiamare il codice .NET; di seguito si trova un semplice esempio dell’aspetto del codice quando è necessario richiamare Reliable Actors utilizzando la classe ActorProxy.

```javascript

var getActorStatus = edge.func(function () {

/*

    #r "Microsoft.ServiceFabric.Actors.dll"
    #r "System.Globalization.dll"
    #r "System.dll"
    #r  "visualobjects.interfaces.dll"
    
    using Microsoft.ServiceFabric.Actors;
    using System.Globalization;
    using VisualObjects.Interfaces;
    using System.Threading.Tasks;
    using System; 


public class Startup
    {
         public async Task<object> Invoke(dynamic input)
        {

            var objectId = (string) input.actorId;
            var serviceUri = (string) input.serviceUri;
            var actId = new ActorId(objectId);
            var serId = new Uri(serviceUri);
            var actorProxy = ActorProxy.Create<IVisualObjectActor>(actId, serId);
            var buffer = await actorProxy.GetStateAsJsonAsync();
            if (!string.IsNullOrEmpty(buffer))
                return buffer;
                else 
                return "null";
         }
  }
 
 */ 

});

```

> [AZURE.NOTE]ActorProxy utilizza l'interfaccia Actor per sapere a quale attore connettersi. Affinché il codice gestito eseguito nel processo node.js sia in grado di creare un'istanza di tale classe, l'assembly dell'interfaccia Actor deve essere copiato nella directory del codice come le altre DLL dell’infrastruttura di servizi. Nota: è necessario copiare la DLL (o impostare un'azione post-compilazione in Visual Studio) ogni volta che si apportano modifiche a metodi e i parametri nell'interfaccia.

![][6]

## Distribuzione
A questo punto il progetto può essere distribuito utilizzando gli strumenti dell’infrastruttura di servizi per Visual Studio. L'ultimo passaggio del processo consiste nel fare riferimento al progetto nel progetto dell’applicazione dell’infrastruttura di servizi in modo che possa essere incluso nel pacchetto dell'applicazione e distribuito nel cluster.

![][9]
 
È possibile distribuire l'applicazione (che include l'applicazione node.js) utilizzando, ad esempio, il menu di scelta rapida della soluzione.

![][10]

## Passaggi successivi
* Ulteriori informazioni su [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* Ulteriori informazioni sul [ciclo di vita dell’applicazione](service-fabric-application-lifecycle.md) dell’infrastruttura di servizi.
* Ulteriori informazioni sull'aggiornamento all’[applicazione dell’infrastruttura di servizi](service-fabric-application-upgrade.md) 

<!-- images -->
[1]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure.PNG
[2]: ./media/service-fabric-node-and-reliable-actors-app/edge-js.PNG
[3]: ./media/service-fabric-node-and-reliable-actors-app/node-exe.PNG
[4]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-manifest.PNG
[5]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-dlls.PNG
[6]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-interface-dll.PNG
[7]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-config.PNG
[8]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure1.PNG
[9]: ./media/service-fabric-node-and-reliable-actors-app/application-project-reference.PNG
[10]: ./media/service-fabric-node-and-reliable-actors-app/solution-deploy.PNG

<!---HONumber=Oct15_HO3-->