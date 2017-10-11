---
title: Creare un front-end Web per l'app Azure Service Fabric tramite ASP.NET Core | Microsoft Docs
description: Esporre l'applicazione di Service Fabric al Web usando un progetto ASP.NET Core e la comunicazione tra servizi tramite servizio remoto.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.openlocfilehash: b19aaa652f2c15573ded632ca1348e1a6752f080
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>Compilare un front-end di servizio Web per l'applicazione tramite ASP.NET Core
Per impostazione predefinita, i servizi di Azure Service Fabric non forniscono un'interfaccia pubblica per il Web. Per esporre la funzionalità dell'applicazione ai client HTTP è necessario creare un progetto Web da usare come punto di ingresso per comunicare con i singoli servizi.

Questa esercitazione è la prosecuzione dell'esercitazione [Creare la prima applicazione in Visual studio](service-fabric-create-your-first-application-in-visual-studio.md). Verrà aggiunto un servizio Web ASP.NET Core per il servizio dei contatori con stato. Se non è già stato fatto, è necessario tornare indietro ed eseguire prima quella esercitazione.

## <a name="set-up-your-environment-for-aspnet-core"></a>Configurare l'ambiente per ASP.NET Core

Per completare questa esercitazione è necessario Visual Studio 2017. Qualsiasi edizione è adeguata. 

 - [Installare Visual Studio 2017](https://www.visualstudio.com/)

Per sviluppare applicazioni Service Fabric ASP.NET Core è necessario avere installato i carichi di lavoro seguenti:
 - **Sviluppo di Azure** (in *Web e cloud*)
 - **Sviluppo ASP.NET e Web** (in *Web e cloud*)
 - **Sviluppo multipiattaforma .NET Core** (in *Altri set di strumenti*)

>[!Note] 
>Gli strumenti .NET Core per Visual Studio 2015 non vengono più aggiornati. Se si usa Visual Studio 2015 è necessario avere installato [.NET Core VS 2015 Tooling Preview 2](https://www.microsoft.com/net/download/core).

## <a name="add-an-aspnet-core-service-to-your-application"></a>Aggiungere un servizio ASP.NET Core all'applicazione
ASP.NET Core è un framework di sviluppo Web multipiattaforma leggero, che consente di creare un'interfaccia utente Web e API Web moderne. 

Ora verrà aggiunto un progetto API Web ASP.NET all'applicazione esistente.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Servizi** nel progetto dell'applicazione e scegliere **Aggiungi > Nuovo servizio Service Fabric**.
   
    ![Aggiunta di un nuovo servizio a un'applicazione esistente][vs-add-new-service]
2. Nella pagina **Crea servizio** scegliere **ASP.NET Core** e assegnare un nome.
   
    ![Scelta di un servizio Web ASP.NET nella finestra di dialogo del nuovo servizio][vs-new-service-dialog]

3. Nella pagina successiva è disponibile un set di modelli di progetto ASP.NET Core. Si tratta degli stessi modelli che verrebbero visualizzati se si creasse un progetto ASP.NET Core all'esterno di un'applicazione di Service Fabric, con una piccola quantità di codice aggiuntivo per registrare il servizio con il runtime di Service Fabric. Per questa esercitazione si sceglierà **API Web**, ma è possibile applicare gli stessi concetti anche alla compilazione di un'applicazione Web completa.
   
    ![Scelta di un tipo di progetto ASP.NET][vs-new-aspnet-project-dialog]
   
    Dopo la creazione del progetto API Web l'applicazione includerà due servizi. Man mano che si compila l'applicazione, si aggiungeranno altri servizi seguendo esattamente la stessa procedura e, per ogni servizio, sarà possibile eseguire in modo indipendente il controllo della versione e l'aggiornamento.

## <a name="run-the-application"></a>Eseguire l'applicazione
Per avere un'idea di quanto è stato fatto, verrà ora distribuita la nuova applicazione ed esaminato il comportamento predefinito del modello API Web ASP.NET Core.

1. Premere F5 in Visual Studio per eseguire il debug dell'app.
2. Al termine della distribuzione Visual Studio avvia un browser nella radice del servizio API Web ASP.NET. Il modello API Web ASP.NET Core non prevede un comportamento predefinito per la radice, quindi nel browser viene visualizzato un errore 404.
3. Aggiungere `/api/values` al percorso nel browser. Questa operazione chiama il metodo `Get` su ValuesController nel modello API Web e restituisce la risposta predefinita trasmessa dal modello, una matrice JSON contenente due stringhe:
   
    ![Valori predefiniti restituiti dal modello API Web ASP.NET Core][browser-aspnet-template-values]
   
    Al termine dell'esercitazione questa pagina visualizza il valore del contatore più recente del servizio con stato anziché le stringhe predefinite.

## <a name="connect-the-services"></a>Connettere i servizi
L'infrastruttura di servizi offre la massima flessibilità nella comunicazione con Reliable Services. In una singola applicazione possono coesistere servizi accessibili tramite TCP, altri tramite un'API REST HTTP e altri ancora tramite Web Socket. Per informazioni sulle opzioni disponibili e sui compromessi necessari, vedere [Comunicazione con i servizi](service-fabric-connect-and-communicate-with-services.md). In questa esercitazione si usa il [servizio remoto di Service Fabric](service-fabric-reliable-services-communication-remoting.md) disponibile nel SDK.

Nell'approccio con servizio remoto, basato sulle chiamate Remote Procedure Call o RPC, viene definita un'interfaccia da usare come contratto pubblico per il servizio e quindi si usa tale interfaccia per generare una classe proxy per l'interazione con il servizio.

### <a name="create-the-remoting-interface"></a>Creare l'interfaccia di comunicazione remota
Per iniziare si crea l'interfaccia da usare come contratto tra il servizio con stato e gli altri servizi, in questo caso il progetto Web ASP.NET Core. Questa interfaccia deve essere condivisa da tutti i servizi che la usano per le chiamate RPC, pertanto va creata in un progetto Libreria di classi specifico.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Aggiungere** > **Nuovo progetto**.

2. Scegliere la voce **Visual C#** nel riquadro di spostamento sinistro e quindi selezionare il modello **Libreria di classi**. Verificare che la versione di .NET Framework sia impostata su **4.5.2**.
   
    ![Creazione di un progetto interfaccia per il servizio con stato][vs-add-class-library-project]

3. Installare il pacchetto NuGet **Microsoft.ServiceFabric.Services.Remoting**. Cercare **Microsoft.ServiceFabric.Services.Remoting** in Gestione pacchetti NuGet e installarlo per tutti i progetti della soluzione che usano il servizio remoto, tra cui:
   - Il progetto Libreria di classi che contiene l'interfaccia del servizio
   - Il progetto di servizio con stato
   - Il progetto di servizio Web ASP.NET Core
   
    ![Aggiunta del pacchetto NuGet dei servizi][vs-services-nuget-package]

4. Nella libreria di classi creare un'interfaccia con un solo metodo, `GetCountAsync`, ed estendere l'interfaccia da `Microsoft.ServiceFabric.Services.Remoting.IService`. L'interfaccia di comunicazione remota deve derivare da questa interfaccia, per indicare che si tratta di un'interfaccia di comunicazione remota del servizio.
   
    ```c#
    using Microsoft.ServiceFabric.Services.Remoting;
    using System.Threading.Tasks;
        
    ...

    namespace MyStatefulService.Interface
    {
        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```

### <a name="implement-the-interface-in-your-stateful-service"></a>Implementare l'interfaccia nel servizio con stato
Dopo aver definito l'interfaccia, è ora necessario implementarla nel servizio con stato.

1. Nel servizio con stato aggiungere un riferimento al progetto della libreria di classi contenente l'interfaccia.
   
    ![Aggiunta di un riferimento al progetto libreria di classi nel servizio con stato][vs-add-class-library-reference]
2. Trovare la classe che eredita da `StatefulService`, ad esempio `MyStatefulService`, ed estenderla per implementare l'interfaccia `ICounter`.
   
    ```c#
    using MyStatefulService.Interface;
   
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
         ...
    }
    ```
3. Implementare ora il singolo metodo definito nell'interfaccia `ICounter`: `GetCountAsync`.
   
    ```c#
    public async Task<long> GetCountAsync()
    {
        var myDictionary = 
            await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
   
        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```

### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>Esporre il servizio con stato usando un listener di comunicazione remota
Con l'interfaccia `ICounter` implementata, il passaggio finale è l'apertura del canale di comunicazione remota del servizio. Per i servizi con stato, Service Fabric offre un metodo sostituibile denominato `CreateServiceReplicaListeners`, in cui è possibile specificare uno o più listener di comunicazione in base al tipo di comunicazione che si vuole abilitare per il servizio.

> [!NOTE]
> Il metodo equivalente per aprire un canale di comunicazione con i servizi senza stato è denominato `CreateServiceInstanceListeners`.

In questo caso si sostituisce il metodo `CreateServiceReplicaListeners` esistente e si specifica un'istanza dell'oggetto `ServiceRemotingListener`, che crea un endpoint RPC chiamabile dai client tramite `ServiceProxy`.  

Il metodo di estensione `CreateServiceRemotingListener` sull'interfaccia `IService` consente di creare facilmente un `ServiceRemotingListener` con tutte le impostazioni predefinite. Per usare questo metodo di estensione verificare che sia stato importato lo spazio dei nomi `Microsoft.ServiceFabric.Services.Remoting.Runtime`. 

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>Usare la classe ServiceProxy per interagire con il servizio
Il servizio con stato è ora pronto per ricevere traffico da altri servizi tramite RPC. e quindi non resta che aggiungere il codice per comunicare con esso dal servizio Web ASP.NET.

1. Nel progetto ASP.NET aggiungere un riferimento alla libreria di classi contenente l'interfaccia `ICounter` .

2. In precedenza è stato aggiunto al progetto ASP.NET il pacchetto NuGet **Microsoft.ServiceFabric.Services.Remoting**. Questo pacchetto offre la classe `ServiceProxy` che consente di eseguire chiamate RPC al servizio con stato. Verificare che questo pacchetto NuGet sia installato nel progetto di servizio Web ASP.NET Core.

4. Nella cartella **Controller** aprire la classe `ValuesController`. Si noti che il metodo `Get` restituisce attualmente solo una matrice di stringhe hardcoded con "value1" e "value2", che corrisponde a quanto visto in precedenza nel browser. Sostituire l'implementazione con il codice seguente:
   
    ```c#
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
   
    ...

    [HttpGet]
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
   
    La prima riga del codice è quella più importante. Per creare il proxy ICounter per il servizio con stato, è necessario fornire due informazioni: un ID partizione e il nome del servizio.
   
    Il partizionamento consente di ridimensionare i servizi con stato suddividendone lo stato in diversi bucket in base a una chiave definita, ad esempio l'ID cliente o il CAP. In questa semplice applicazione la chiave non è importante, perché il servizio con stato ha una sola partizione e qualsiasi chiave specificata restituirà la stessa partizione. Per altre informazioni sul partizionamento dei servizi, vedere [Partizionare Reliable Services di Service Fabric](service-fabric-concepts-partitioning.md).
   
    Il nome del servizio è un URI in formato fabric:/&lt;nome_applicazione&gt;/&lt;nome_servizio&gt;.
   
    Con queste due informazioni, Service Fabric può identificare in modo univoco il computer a cui inviare le richieste. La classe `ServiceProxy` gestirà facilmente anche i casi in cui il computer che ospita la partizione del servizio con stato presenta un errore e un altro computer deve essere alzato di livello per sostituirlo. Questa astrazione semplifica notevolmente la scrittura di codice client per gestire gli altri servizi.
   
    Una volta che il proxy è disponibile, è sufficiente richiamare il metodo `GetCountAsync` e restituirne il risultato.

5. Premere di nuovo F5 per eseguire l'applicazione modificata. Come in precedenza, Visual Studio avvia automaticamente il browser nella radice del progetto Web. Aggiungere il percorso "api/values" per visualizzare il valore del contatore attualmente restituito.
   
    ![Valore del contatore con stato visualizzato nel browser][browser-aspnet-counter-value]
   
    Aggiornare regolarmente il browser per visualizzare l'aggiornamento del valore del contatore.

## <a name="kestrel-and-weblistener"></a>Kestrel e WebListener

Il server Web ASP.NET Core predefinito, noto come Kestrel, [non è attualmente supportato per la gestione diretta del traffico Internet](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel). Il modello del servizio senza stato ASP.NET Core per Service Fabric usa di conseguenza [WebListener](https://docs.microsoft.com/aspnet/core/fundamentals/servers/weblistener) per impostazione predefinita. 

Per altre informazioni su Kestrel e WebListener nei servizi Service Fabric, fare riferimento all'articolo [ASP.NET Core in Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md) (ASP.NET Core in Reliable Services di Service Fabric).

## <a name="connecting-to-a-reliable-actor-service"></a>Connessione a un servizio Reliable Actor
Questa esercitazione ha illustrato la procedura per aggiungere un front-end Web in grado di comunicare con un servizio con stato. Per comunicare con gli attori è possibile seguire un modello molto simile. Quando si crea un progetto Reliable Actor, Visual Studio genera automaticamente un progetto interfaccia. È possibile usare tale interfaccia per generare un proxy attore nel progetto Web per comunicare con l'attore. Poiché il canale di comunicazione viene fornito automaticamente, non è necessario eseguire operazioni, ad esempio stabilire un oggetto `ServiceRemotingListener`, come per il servizio con stato.

## <a name="how-web-services-work-on-your-local-cluster"></a>Funzionamento dei servizi Web in un cluster locale
In generale è possibile distribuire esattamente la stessa applicazione Service Fabric anche in un cluster con più computer distribuito nel cluster locale con la certezza che funzionerà come previsto, perché il cluster locale è semplicemente una configurazione a cinque nodi compressa in un solo computer.

Quando si tratta di servizi Web, tuttavia, c'è una sola possibilità. Quando il cluster si trova dietro un servizio di bilanciamento del carico, come in Azure, è necessario assicurarsi che i servizi Web vengano distribuiti in ogni computer, perché il servizio di bilanciamento del carico si limita a eseguire il round robin del traffico tra i computer. A tale scopo, è possibile impostare `InstanceCount` per il servizio sul valore speciale "-1".

Quando invece si esegue il servizio Web in locale, è necessario assicurarsi che solo un'istanza del servizio sia in esecuzione. In caso contrario si verificano conflitti tra più processi in ascolto sullo stesso percorso e sulla stessa porta. Per le distribuzioni locali, quindi, è opportuno impostare il numero delle istanze del servizio Web su 1.

Per informazioni su come configurare valori diversi a seconda dell'ambiente, vedere [Gestione dei parametri dell'applicazione per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="next-steps"></a>Passaggi successivi
Dopo l'impostazione del front-end Web per l'applicazione con ASP.NET Core, vedere altre informazioni su [ASP.NET Core in Reliable Services di Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) per un esame dettagliato dell'interazione tra ASP.NET Core e Service Fabric.

[Altre informazioni sulla comunicazione tra servizi](service-fabric-connect-and-communicate-with-services.md) in generale sono disponibili per completare il quadro del funzionamento della comunicazione tra servizi in Service Fabric.

Dopo avere acquisito una buona comprensione del funzionamento della comunicazione tra servizi, [creare un cluster in Azure e distribuire la propria applicazione nel cloud](service-fabric-cluster-creation-via-portal.md).

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
