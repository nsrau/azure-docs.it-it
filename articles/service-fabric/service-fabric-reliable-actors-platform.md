<properties
   pageTitle="Reliable Actors nell'infrastruttura di servizi | Microsoft Azure"
   description="Descrive come i Reliable Actors usano le funzionalità della piattaforma dell'infrastruttura dei servizi trattando concetti dal punto di vista degli sviluppatori attori."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="abhisram"/>

# Modalità d'uso della piattaforma Service Fabric da parte di Reliable Actors

Gli attori usano il modello di applicazione di Service Fabric per gestire il ciclo di vita dell'applicazione. Ogni tipo di attore viene mappato a un [tipo di servizio](service-fabric-application-model.md#describe-a-service) di infrastruttura di servizi. Il codice attore viene [incluso in un pacchetto](service-fabric-application-model.md#package-an-application) come applicazione di Service Fabric e [distribuito](service-fabric-deploy-remove-applications.md#deploy-an-application) sul cluster.

## Concetto di modello dell'applicazione di esempio per gli attori

Per illustrare alcuni dei concetti precedenti, verrà esaminato l'esempio di un progetto attore [creato tramite Visual Studio](service-fabric-reliable-actors-get-started.md).

Il manifesto dell'applicazione, il manifesto del servizio e il file di configurazione Settings.xml sono inclusi nel progetto per il servizio attore durante la creazione della soluzione in Visual Studio, come illustrato nella schermata seguente.

![][1]

È possibile individuare il tipo e la versione dell'applicazione in cui l'attore è contenuto esaminando il manifesto dell'applicazione incluso nel progetto per il servizio attore. Il frammento di codice di un manifesto dell'applicazione riportato di seguito ne è un esempio.

~~~
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                     ApplicationTypeName="VoiceMailBoxApplication"
                     ApplicationTypeVersion="1.0.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric">
~~~

È possibile individuare il tipo di servizio a cui il tipo di attore è mappato esaminando il manifesto del servizio incluso nel progetto per il servizio attore. Il frammento di codice di un manifesto del servizio riportato di seguito ne è un esempio.

~~~
<StatefulServiceType ServiceTypeName="VoiceMailBoxActorServiceType" HasPersistedState="true">
~~~

Quando il pacchetto dell'applicazione viene creato usando Visual Studio, i log nella finestra dell'output di compilazione indicano la posizione del pacchetto dell'applicazione. Ad esempio:

    -------- Package started: Project: VoiceMailBoxApplication, Configuration: Debug x64 ------
    VoiceMailBoxApplication -> C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug

Di seguito è riportato un elenco parziale del percorso precedente (l'elenco completo non è stato riportato per brevità):

    C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug>tree /f
    Folder PATH listing
    Volume serial number is 303F-6F91
    C:.
    │   ApplicationManifest.xml
    │
    ├───VoiceMailBoxActorServicePkg
    │   │   ServiceManifest.xml
    │   │
    │   ├───Code
    │   │   │   Microsoft.ServiceFabric.Actors.dll
    │   │   │       :
    │   │   │   Microsoft.ServiceFabric.Services.dll
    │   │   │   ServiceFabricServiceModel.dll
    │   │   │   System.Fabric.Common.Internal.dll
    │   │   │   System.Fabric.Common.Internal.Strings.dll
    │   │   │   VoiceMailBox.exe
    │   │   │   VoiceMailBox.exe.config
    │   │   │   VoiceMailBox.Interfaces.dll
    │   │   │
    │   │   └───it-IT
    │   │           System.Fabric.Common.Internal.Strings.resources.dll
    │   │
    │   └───Config
    │           Settings.xml
    │
    └───VoicemailBoxWebServicePkg
        │   ServiceManifest.xml
        │
        └───Code
            │   Microsoft.Owin.dll
            │       :
            │   Microsoft.ServiceFabric.Services.dll
            │       :
            │   System.Fabric.Common.Internal.dll
            │   System.Fabric.Common.Internal.Strings.dll
            │       :
            │   VoiceMailBox.Interfaces.dll
            │   VoicemailBoxWebService.exe
            │   VoicemailBoxWebService.exe.config
            │
            └───it-IT
                    System.Fabric.Common.Internal.Strings.resources.dll

L'elenco precedente mostra gli assembly che implementano l'inclusione dell'attore VoicemailBox nel pacchetto di codice all'interno del pacchetto del servizio nel pacchetto dell'applicazione.

La soluzione Visual Studio include gli script di PowerShell che consentono di distribuire l'applicazione nel cluster e rimuoverla dal cluster. Gli script sono cerchiati nella schermata seguente.

![][2]

Anche la successiva gestione dell'applicazione, ad esempio gli aggiornamenti e l'eventuale eliminazione, viene eseguita tramite meccanismi di gestione dell'applicazione di Service Fabric. Per altre informazioni, vedere gli argomenti relativi al [modello di applicazione](service-fabric-application-model.md), alla [distribuzione e rimozione dell'applicazione](service-fabric-deploy-remove-applications.md) e all'[aggiornamento dell'applicazione](service-fabric-application-upgrade.md).

## Scalabilità per i servizi attore
Gli amministratori del cluster possono creare uno o più servizi attore per ogni tipo di servizio nel cluster. Ciascuno di questi servizi attore può disporre di una o più partizioni, come qualsiasi altro servizio di Service Fabric. La capacità di creare più servizi di un tipo di servizio mappato a un tipo di attore e più partizioni per un servizio consente all'applicazione attore di applicare la scalabilità. Per altre informazioni, vedere l'articolo relativo alla [scalabilità](service-fabric-concepts-scalability.md).

> [AZURE.NOTE]Per i servizi attore senza stato il numero di [istanze](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services) deve essere pari a 1. Non è supportata la presenza di più istanze del servizio attore senza stato in una partizione. Di conseguenza, i servizi attore senza stato non dispongono dell'opzione per incrementare il numero di istanze per ottenere la scalabilità. Devono usare le opzioni di scalabilità descritte nell'[articolo relativo alla scalabilità](service-fabric-concepts-scalability.md).

## Concetti relativi alla partizione di Service Fabric per gli attori
L'ID attore di un attore viene mappato a una partizione di un servizio attore. L'attore viene creato all'interno della partizione a cui è mappato il relativo ID attore. Quando viene creato un attore, il runtime di Actors scrive un [evento EventSource](service-fabric-reliable-actors-diagnostics.md#eventsource-events) che indica in quale partizione è stato creato l'attore. Di seguito è riportato un esempio di questo evento, che indica che un attore con ID `-5349766044453424161` è stato creato all'interno della partizione `0583c745-1bed-43b2-9545-29d7e3448156` del servizio `fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService` nell'applicazione `fabric:/VoicemailBoxAdvancedApplication`.

    {
      "Timestamp": "2015-04-26T10:12:20.2485941-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -5349766044453424161, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: 0583c745-1bed-43b2-9545-29d7e3448156.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-5349766044453424161",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "0583c745-1bed-43b2-9545-29d7e3448156",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

Un altro attore con ID `-4952641569324299627` è stato creato all'interno di una partizione `c146fe53-16d7-4d96-bac6-ef54613808ff` diversa dello stesso servizio, come indicato nell'evento seguente.

    {
      "Timestamp": "2015-04-26T15:06:56.93882-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -4952641569324299627, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: c146fe53-16d7-4d96-bac6-ef54613808ff.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-4952641569324299627",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "c146fe53-16d7-4d96-bac6-ef54613808ff",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

*Nota:* alcuni campi degli eventi precedenti sono stati omessi per brevità.

È possibile usare l'ID partizione per ottenere altre informazioni sulla partizione. È ad esempio possibile usare lo strumento [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) per visualizzare informazioni relative alla partizione, nonché al servizio e all'applicazione a cui appartiene la partizione. La schermata seguente mostra le informazioni relative alla partizione `c146fe53-16d7-4d96-bac6-ef54613808ff`, che includeva l'attore con ID `-4952641569324299627` nell'esempio precedente.

![][3]

Gli attori possono ottenere l'ID partizione, il nome del servizio, il nome dell'applicazione e altre informazioni specifiche sulla piattaforma Service Fabric a livello di codice tramite l'oggetto `Host.ActivationContext` e l'oggetto `Host.StatelessServiceInitialization` o tramite i membri `Host.StatefulServiceInitializationParameters` della classe base da cui deriva il tipo di attore, come illustrato dal frammento di codice seguente:

```csharp
public void ActorMessage<TState>(Actor<TState> actor, string message, params object[] args)
{
    string finalMessage = string.Format(message, args);
    this.ActorMessage(
        actor.GetType().ToString(),
        actor.Id.ToString(),
        actor.Host.ActivationContext.ApplicationTypeName,
        actor.Host.ActivationContext.ApplicationName,
        actor.Host.StatefulServiceInitializationParameters.ServiceTypeName,
        actor.Host.StatefulServiceInitializationParameters.ServiceName.ToString(),
        actor.Host.StatefulServiceInitializationParameters.PartitionId,
        actor.Host.StatefulServiceInitializationParameters.ReplicaId,
        FabricRuntime.GetNodeContext().NodeName,
        finalMessage);
}
```

### Concetti relativi alla partizione di Service Fabric per gli attori senza stato
Gli attori senza stato vengono creati all'interno di una partizione di un servizio di Service Fabric senza stato. L'ID attore determina in quale partizione viene creato l'attore. Il numero di [istanze](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services) per un servizio attore senza stato deve essere 1. La modifica del numero di istanze su qualsiasi altro valore non è supportata. Pertanto, l'attore viene creato all'interno dell'unica istanza di servizio presente all'interno della partizione.

> [AZURE.TIP]Il runtime di Fabric Actors emette alcuni [eventi relativi alle istanze di attori senza stato](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateless-actor-instances), che sono utili per la diagnostica e il monitoraggio delle prestazioni.

Quando viene creato un attore senza stato, il runtime di Actors scrive un [evento EventSource](service-fabric-reliable-actors-diagnostics.md#eventsource-events) che indica in quale partizione è stato creato l'attore. Di seguito è riportato un esempio di questo evento, che indica che un attore con ID `abc` è stato creato all'interno dell'istanza `130745709600495974` della partizione `8c828833-ccf1-4e21-b99d-03b14d4face3` del servizio `fabric:/HelloWorldApplication/HelloWorldActorService` nell'applicazione `fabric:/HelloWorldApplication`.

    {
      "Timestamp": "2015-04-26T18:17:46.1453113-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: HelloWorld.HelloWorld, actor ID: abc, stateful: False, replica/instance ID: 130,745,709,600,495,974, partition ID: 8c828833-ccf1-4e21-b99d-03b14d4face3.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "HelloWorld.HelloWorld",
        "actorId": "abc",
        "isStateful": "False",
        "replicaOrInstanceId": "130745709600495974",
        "partitionId": "8c828833-ccf1-4e21-b99d-03b14d4face3",
        "serviceName": "fabric:/HelloWorldApplication/HelloWorldActorService",
        "applicationName": "fabric:/HelloWorldApplication",
      }
    }

*Nota:* alcuni campi dell'evento precedente sono stati omessi per brevità.

### Concetti relativi alla partizione di Service Fabric per gli attori con stato
Gli attori con stato vengono creati all'interno di una partizione del servizio di Service Fabric con stato. L'ID attore determina in quale partizione viene creato l'attore. Ogni partizione del servizio può disporre di una o più [repliche](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services) posizionate in diversi nodi del cluster. La presenza di più repliche garantisce affidabilità per lo stato dell'attore. Il gestore delle risorse consente di ottimizzare il posizionamento in base ai domini di errore e di aggiornamento disponibili nel cluster. Due repliche della stessa partizione non vengono mai posizionate nello stesso nodo. Gli attori vengono sempre creati nella replica primaria della partizione a cui è mappato l'ID attore.

> [AZURE.TIP]Il runtime di Fabric Actors emette alcuni [eventi relativi alle repliche di attori con stato](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateful-actor-replicas), che sono utili per la diagnostica e il monitoraggio delle prestazioni.

Tenere presente che nell'[esempio di VoiceMailBoxActor riportato in precedenza](#service-fabric-partition-concepts-for-actors) l'attore con ID `-4952641569324299627` è stato creato all'interno della partizione `c146fe53-16d7-4d96-bac6-ef54613808ff`. L'evento EventSource di quell'esempio indica anche che l'attore è stato creato nella replica `130745418574851853` di tale partizione. Questa era la replica primaria della partizione al momento della creazione dell'attore, come confermato dalla schermata seguente di Service Fabric Explorer.

![][4]

## Scelta del provider di stato degli attori
Nel runtime di Actors sono inclusi alcuni provider di stato degli attori predefiniti. Per scegliere un provider di stato appropriato per un servizio attore, è necessario comprendere in che modo i provider di stato usano le funzionalità della piattaforma Service Fabric sottostante per rendere lo stato dell'attore estremamente disponibile.

Per impostazione predefinita, un attore con stato usa il provider di stato basato sull'archivio chiave-valore distribuito che viene fornito dalla piattaforma Service Fabric. Lo stato viene salvato in modo permanente sul disco locale del nodo che ospita la [replica](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services) primaria, nonché replicato e salvato in modo permanente sui dischi locali dei nodi che ospitano le repliche secondarie. Il salvataggio dello stato è completato solo quando un quorum di repliche ha eseguito il commit dello stato nei dischi locali. L'archivio chiave-valore dispone di funzionalità avanzate che consentono di rilevare le incoerenze, ad esempio uno stato False, e di correggerle automaticamente.

Il runtime di Actors include anche un oggetto `VolatileActorStateProvider`. Questo provider di stato replica lo stato in repliche, ma lo stato rimane in memoria nella replica. Se una replica si arresta e poi torna disponibile, il relativo stato viene ricompilato dall'altra replica. Tuttavia, se tutte le repliche (le copie dello stato) si arrestano contemporaneamente, i dati relativi allo stato andranno persi. Questo provider di stato è quindi adatto alle applicazioni in cui i dati non andranno persi anche in caso di errore di alcune repliche e in caso di failover pianificati come gli aggiornamenti. In caso di perdita di tutte le repliche (copie) è necessario ricreare i dati tramite meccanismi esterni a Service Fabric. È possibile configurare l'attore con stato per l'uso di un provider di stato volatile aggiungendo l'attributo `VolatileActorStateProvider` alla classe attore o un attributo a livello di assembly.

Il frammento di codice seguente illustra come modificare tutti gli attori nell'assembly che non dispone di un attributo del provider di stato esplicito per l'uso dell'oggetto `VolatileActorStateProvider`.

```csharp
[assembly:Microsoft.ServiceFabric.Actors.VolatileActorStateProvider]
```

Il frammento di codice seguente illustra come modificare il provider di stato per un tipo di attore specifico, in questo caso `VoicemailBox`, impostandolo su `VolatileActorStateProvider`.

```csharp
[VolatileActorStateProvider]
public class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

Si noti che per modificare il provider di stato è necessario ricreare il servizio attore. Non è possibile modificare i provider di stato durante l'aggiornamento dell'applicazione.

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/manifests-in-vs-solution.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png

<!---HONumber=Nov15_HO2-->