---
title: Creare un'applicazione .NET per Service Fabric | Microsoft Docs
description: Informazioni su come creare un'applicazione con un front-end ASP.NET Core e un servizio Reliable con stato back-end e distribuire l'applicazione in un cluster.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi, mikhegn
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 71d910bc0e459528805521ba991e5291396a3b8d
ms.contentlocale: it-it
ms.lasthandoff: 08/10/2017

---

# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Creare e distribuire un'applicazione con un servizio front-end API Web ASP.NET Core e un servizio back-end con stato
Questa esercitazione è la prima parte di una serie e illustra come creare un'applicazione di Azure Service Fabric con un front-end API Web ASP.NET Core e un servizio back-end con stato per archiviare i dati. 

![Diagramma dell'applicazione](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Nella prima parte della serie si apprenderà come:

> [!div class="checklist"]
> * Creare un servizio API Web ASP.NET Core come servizio Reliable
> * Creare un servizio Reliable con stato
> * Implementare la comunicazione remota del servizio e l'uso di un proxy del servizio

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * Creare un'applicazione di Service Fabric .NET
> * [Distribuire l'applicazione in un cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Configurare l'integrazione continua e la distribuzione continua usando Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Installare Visual Studio 2017](https://www.visualstudio.com/) e installare i carichi di lavoro **Sviluppo di Azure** e **Sviluppo ASP.NET e Web**.
- [Installare Service Fabric SDK](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Creare un servizio API Web ASP.NET come servizio Reliable
ASP.NET Core è un framework di sviluppo Web multipiattaforma leggero, che consente di creare un'interfaccia utente Web e API Web moderne. Per comprendere a fondo la modalità di integrazione di ASP.NET Core con Service Fabric, si consiglia di leggere l'articolo [ASP.NET Core in Reliable Services di Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md). Per il momento, è possibile seguire questa esercitazione per essere subito operativi. Per altre informazioni su ASP.NET Core, vedere la [documentazione di ASP.NET Core](https://docs.microsoft.com/aspnet/core/).

> [!NOTE]
> L'esercitazione si basa sugli strumenti di [ASP.NET Core per Visual Studio 2017](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/start-mvc). Gli strumenti di .NET Core per Visual Studio 2015 non saranno più oggetto di aggiornamenti.

1. Avviare Visual Studio come **amministratore**.

2. Creare un progetto da **File**->**Nuovo**->**Progetto**

3. Nella finestra di dialogo **Nuovo progetto** scegliere **Cloud > Applicazione di Service Fabric**.

4. Assegnare all'applicazione il nome **MyApplication** e fare clic su **OK**.

   ![Finestra di dialogo Nuovo progetto in Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. Nella pagina **Nuovo servizio Service Fabric** scegliere **ASP.NET Core senza stato** e assegnare al servizio il nome **MyWebAPIFrontEnd**.
   
   ![Scelta di un servizio Web ASP.NET nella finestra di dialogo del nuovo servizio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. Nella pagina successiva è disponibile un set di modelli di progetto ASP.NET Core. Per questa esercitazione si sceglierà **API Web**, ma è possibile applicare gli stessi concetti anche alla compilazione di un'applicazione Web completa.
   
   ![Scelta di un tipo di progetto ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio crea un'applicazione e un progetto di servizio e li visualizza in Esplora soluzioni.

   ![Esplora soluzioni dopo la creazione dell'applicazione con il servizio API Web ASP.NET Core]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="deploy-and-debug-the-application-locally"></a>Distribuire l'applicazione ed eseguirne il debug in locale
A questo punto è possibile eseguire il debug dell'applicazione e analizzarne il comportamento predefinito fornito dal modello API Web ASP.NET Core.

In Visual Studio premere `F5` per distribuire l'applicazione per il debug. `F5` ha esito negativo se in precedenza Visual Studio non è stato aperto come **amministratore**.

> [!NOTE]
> La prima volta che si esegue e si distribuisce l'applicazione in locale, Visual Studio crea un cluster locale per il debug e questa operazione può richiedere tempo. Lo stato della creazione del cluster verrà visualizzato nella finestra di output di Visual Studio.

Quando il cluster è pronto, si riceverà una notifica dall'applicazione di gestione cluster locale di Service Fabric, nell'area di notifica.

1. Per eseguire il debug dell'applicazione, premere F5 in Visual Studio.
2. Al termine della distribuzione, Visual Studio avvia il browser nella radice del servizio API Web ASP.NET. Il modello API Web ASP.NET Core non prevede un comportamento predefinito per la radice, quindi nel browser verrà visualizzato un errore.
3. Aggiungere `/api/values` all'URL nel browser. Questa richiesta richiama il metodo `Get` su ValuesController nel modello API Web. Restituisce la risposta predefinita trasmessa dal modello, ovvero una matrice JSON contenente due stringhe:
   
![Valori predefiniti restituiti dal modello API Web ASP.NET Core](./media/service-fabric-tutorial-create-dotnet-app/browser-aspnet-template-values.png)

> [!NOTE]
> Per modificare il comportamento predefinito di Visual Studio 2017 durante il debug di un'applicazione, è possibile modificare le proprietà del progetto di applicazione di Service Fabric **MyApplication**.
> Per questa esercitazione, impostare Modalità di debug applicazione su **Aggiorna l'applicazione** e aggiungere **'/api/values'** alla proprietà dell'URL dell'applicazione per ottimizzare l'esperienza di debug.
> 

Per arrestare il debug dell'applicazione, tornare a Visual Studio e premere **MAIUSC+F5**.

### <a name="understanding-the-service-fabric-application-and-service"></a>Informazioni sul servizio e sull'applicazione di Service Fabric
La soluzione di Visual Studio contiene ora due progetti.
1. Il progetto di applicazione di Service Fabric, **MyApplication**
    - Questo progetto non contiene codice direttamente. Fa invece riferimento a un set di progetti di servizio. Include inoltre altri tipi di contenuto per specificare come è composta e distribuita l'applicazione.
2. Il progetto di servizio, **MyWebAPIFrontEnd**
    - Questo progetto è il progetto API Web ASP.NET Core, che contiene il codice e la configurazione per il servizio. Esaminando il file di codice ValuesController.cs nella cartella Controllers, è possibile notare che si tratta di un normale controller API Web ASP.NET Core. Non ci sono requisiti specifici per il codice scritto come parte dei controller quando si esegue un'API Web ASP.NET Core come servizio Reliable in Service Fabric.

Per altre informazioni sul modello di applicazione in Service Fabric, vedere [Modellare un'applicazione in Service Fabric](service-fabric-application-model.md).

Per altre informazioni sui contenuti del progetto di servizio, vedere [Introduzione a Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Aggiungere un servizio back-end con stato a un'applicazione
Ora che c'è un servizio API Web ASP.NET in esecuzione nell'applicazione, è possibile procedere e aggiungere un servizio Reliable con stato per archiviare alcuni dati nell'applicazione.

Service Fabric consente di archiviare in modo coerente e affidabile i dati all'interno del servizio usando raccolte Reliable Collections. Le raccolte Reliable Collections sono un semplice set di classi di raccolte con un livello elevato di disponibilità e affidabilità che risulterà familiare a chiunque abbia usato raccolte C#.

In questa esercitazione si crea un servizio, che archivia un valore del contatore in una raccolta Reliable Collections.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Servizi** nel progetto dell'applicazione e scegliere **Aggiungi > Nuovo servizio Service Fabric**.
   
    ![Aggiunta di un nuovo servizio a un'applicazione esistente](./media/service-fabric-tutorial-create-dotnet-app/vs-add-new-service.png)

2. Nella finestra di dialogo **Nuovo servizio Service Fabric** scegliere **Servizio con stato**, assegnare il nome **MyStatefulService** al servizio e fare clic su **OK**.

    ![Finestra di dialogo Nuovo servizio in Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    Una volta creato il progetto di servizio, l'applicazione includerà due servizi. Man mano che si compila l'applicazione, è possibile aggiungere altri servizi nello stesso modo. Per ogni servizio, sarà possibile eseguire in modo indipendente il controllo della versione e l'aggiornamento.

### <a name="deploy-and-debug-the-application-locally"></a>Distribuire l'applicazione ed eseguirne il debug in locale
Con il nuovo servizio aggiunto all'applicazione, eseguire il debug dell'applicazione completa e osservare il comportamento predefinito del nuovo servizio.

Per eseguire il debug dell'applicazione, premere F5 in Visual Studio.

> [!NOTE]
> Se si sceglie di usare **Aggiorna l'applicazione** come modalità di debug dell'applicazione, viene chiesto di concedere al cluster di Service Fabric locale l'accesso alla cartella di output di compilazione dell'applicazione.
> 

Entrambi i servizi dell'applicazione vengono compilati, distribuiti e dichiarati nel cluster di Service Fabric locale. All'avvio dei servizi, Visual Studio avvia comunque il browser, ma apre automaticamente anche il **visualizzatore eventi di diagnostica**, in cui viene visualizzato l'output di traccia dei servizi.
   
![Visualizzatore eventi di diagnostica](./media/service-fabric-tutorial-create-dotnet-app/diagnostic-events-viewer.png)

Il visualizzatore eventi di diagnostica mostra i messaggi di traccia di tutti i servizi che fanno parte della soluzione Visual Studio di cui viene eseguito il debug. Mettendo in pausa il visualizzatore eventi di diagnostica, è possibile espandere uno dei messaggi di servizio, per controllare le relative proprietà. In questo modo, è possibile vedere il servizio nel cluster che ha generato il messaggio, il nodo in cui è attualmente in esecuzione l'istanza del servizio e altre informazioni.

![Visualizzatore eventi di diagnostica](./media/service-fabric-tutorial-create-dotnet-app/expanded-diagnostics-viewer.png)

Verranno visualizzati i messaggi di servizio provenienti dal servizio con stato creato, in quanto **serviceTypeName** è **MyStatefulServiceType**. È anche possibile vedere che vengono inviati messaggi con un testo che si riferisce al contatore corrente. Questo messaggio viene restituito dalla riga di codice evidenziata nel metodo `RunAsync` di **MyStatefulService.cs**, come illustrato nello screenshot seguente.

![Coda di messaggi del servizio](./media/service-fabric-tutorial-create-dotnet-app/service-message-code.png)

Per altre informazioni sulla restituzione di informazioni di diagnostica da applicazioni e servizi, vedere [Monitoraggio e diagnostica in Azure Service Fabric](service-fabric-diagnostics-overview.md).

Per arrestare il debug dell'applicazione, tornare a Visual Studio e premere **MAIUSC+F5**.

### <a name="understanding-the-mystatefulservice-code"></a>Informazioni sul codice di MyStatefulService
Per comprendere come viene usato un dizionario Reliable per archiviare i dati nel cluster, esaminare il codice nel servizio **MyStatefulService**.

Nel servizio ci sono cinque righe di codice correlate a creazione, aggiornamento e lettura dal dizionario Reliable.

![Codice del dizionario Reliable](./media/service-fabric-tutorial-create-dotnet-app/reliable-dictionary-code.png)

1. Ogni volta che viene eseguito il metodo `RunAsync` del servizio (ovvero all'avvio del servizio), questa riga di codice ottiene o aggiunge al servizio un dizionario Reliable con il nome `myDictionary`.
2. Tutte le interazioni con i valori in un dizionario Reliable richiedono una transazione, che viene creata dall'istruzione using immessa in questa riga di codice.
3. Questa riga di codice ottiene il valore associato alla chiave specificata nella chiamata al metodo, ad esempio `Counter`.
4. Questa riga di codice aggiorna il valore associato alla chiave `Counter` incrementandolo.
5. Questa chiamata al metodo esegue il commit della transazione e restituisce un risultato quando il valore aggiornato viene archiviato in un quorum di nodi del cluster.

Per informazioni più dettagliate su dizionari Reliable e raccolte Reliable Collections, vedere [Introduzione alle Reliable Collections nei servizi con stato di Service Fabric](service-fabric-reliable-services-reliable-collections.md).

## <a name="connect-the-services"></a>Connettere i servizi
Nel passaggio successivo i due servizi vengono connessi e si fa in modo che l'API Web front-end restituisca il valore corrente dal dizionario Reliable dei servizi back-end.

L'infrastruttura di servizi offre la massima flessibilità nella comunicazione con Reliable Services. All'interno di una singola applicazione possono esserci servizi accessibili tramite TCP. Altri servizi potrebbero essere accessibili tramite un'API REST HTTP e altri ancora tramite Web Socket. Per informazioni sulle opzioni disponibili e sui compromessi necessari, vedere [Comunicazione con i servizi](service-fabric-connect-and-communicate-with-services.md).

In questa esercitazione si usa la [comunicazione remota del servizio con Reliable Services](service-fabric-reliable-services-communication-remoting.md).

Nell'approccio di comunicazione remota del servizio (basato sulle chiamate Remote Procedure Call o RPC) viene definita un'interfaccia da usare come contratto pubblico per il servizio. Quindi si usa tale interfaccia per generare una classe proxy per l'interazione con il servizio.

### <a name="create-the-remoting-interface"></a>Creare l'interfaccia di comunicazione remota
Prima di tutto, è necessario creare l'interfaccia da usare come contratto tra i due servizi. L'interfaccia viene creata in un progetto di libreria di classi separato, perché tutti i servizi che la usano devono farvi riferimento.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Aggiungi** > **Nuovo progetto**.
2. Scegliere la voce **Visual C#** nel riquadro di spostamento sinistro e quindi selezionare il modello **Libreria di classi (.NET Framework)**. Verificare che la versione di .NET Framework sia impostata su **4.5.2** o successiva.
   
    ![Creazione di un progetto interfaccia per il servizio con stato](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-project.png)

3. Assegnare alla libreria di classi il nome **MyStatefulService.Interface** e fare clic su **OK**.

4. Fare clic con il pulsante destro del mouse sulla soluzione in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet per la soluzione**.

5. Scegliere **Sfoglia** e cercare **Microsoft.ServiceFabric.Services.Remoting**. Scegliere di eseguire l'installazione per i tre progetti di servizio nella soluzione: 
   
    ![Aggiunta del pacchetto NuGet dei servizi](./media/service-fabric-tutorial-create-dotnet-app/add-nuget.png)

6. Nella libreria di classi creare un'interfaccia con un solo metodo, `GetCountAsync`, ed estendere l'interfaccia da `Microsoft.ServiceFabric.Services.Remoting.IService`. L'interfaccia di comunicazione remota deve derivare da questa interfaccia, per indicare che si tratta di un'interfaccia di comunicazione remota del servizio. 
   
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
7. Fare clic con il pulsante destro del mouse sul progetto **MyStatefulService.Interface** in Esplora soluzioni e scegliere **Proprietà**. Selezionare la scheda **Compila** e quindi il valore **x64** nel menu a discesa **Destinazione piattaforma**. 

8. Salvare tutte le modifiche.

### <a name="implement-the-interface-in-your-stateful-service"></a>Implementare l'interfaccia nel servizio con stato
Dopo aver definito l'interfaccia, è ora necessario implementarla nel servizio con stato.

1. Aggiungere un riferimento al progetto di libreria di classi contenente l'interfaccia dal progetto **MyStatefulService**.
   
    ![Aggiunta di un riferimento al progetto libreria di classi nel servizio con stato](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-reference.png)

    ![Aggiunta di un riferimento al progetto libreria di classi nel servizio con stato](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-reference-2.png)

2. Aprire il file `MyStatefulService.cs` ed estenderlo per implementare l'interfaccia `ICounter` creata.
   
    ```c#
    using MyStatefulService.Interface;
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
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

    - Questo metodo restituisce il valore archiviato della chiave `Counter` nel dizionario Reliable denominato `myDictionary`. 

### <a name="expose-the-stateful-service-using-service-remoting"></a>Esporre il servizio con stato usando la comunicazione remota del servizio
Dopo aver implementato l'interfaccia `ICounter`, il passaggio finale consiste nell'aprire l'endpoint di comunicazione remota del servizio. Per i servizi con stato, Service Fabric offre un metodo sostituibile denominato `CreateServiceReplicaListeners`, in cui è possibile specificare uno o più listener di comunicazione in base al tipo di comunicazione che si vuole abilitare per il servizio.

In questo caso si sostituisce il metodo `CreateServiceReplicaListeners` esistente e si specifica un'istanza dell'oggetto `ServiceRemotingListener`, che crea un endpoint RPC chiamabile dai client tramite `ServiceProxy`.  

Modificare il metodo **CreateServiceReplicaListeners** nel file **MyStatefulService.cs** e aggiungere un'istruzione using allo spazio dei nomi `Microsoft.ServiceFabric.Services.Remoting.Runtime`.

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

Il servizio con stato è ora pronto per ricevere traffico da altri servizi tramite RPC usando la comunicazione remota del servizio.

### <a name="call-the-stateful-back-end-service-from-the-front-end-service"></a>Chiamare il servizio back-end con stato dal servizio front-end
Ora che il servizio back-end ha esposto un'interfaccia, non resta che aggiungere il codice per comunicare con essa dal servizio API Web ASP.NET. Per comunicare usando la comunicazione remota del servizio, si usa un proxy del servizio di ValuesController.

1. In Esplora soluzioni espandere **MyWebAPIFrontEnd**, fare clic con il pulsante destro del mouse su **Dipendenze** e scegliere **Aggiungi riferimento**.  Selezionare **MyStatefulService.Interface** e fare clic su **OK**.
   
2. Nella cartella **Controller** aprire il file `ValuesController.cs`. Aggiungere le istruzioni using seguenti al file:

    ```c#
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
    ```
    
3. Il metodo `Get` attualmente restituisce solo una matrice di stringhe hardcoded con "value1" e "value2", che corrisponde a quanto visto in precedenza nel browser. Sostituire l'implementazione con il codice seguente:
   
    ```
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
 
    La prima riga di codice in questo metodo crea l'oggetto ServiceProxy nel servizio con stato tramite l'interfaccia ICounter. Quando si crea un oggetto ServiceProxy, è necessario fornire due informazioni: un ID di partizione e il nome del servizio.
   
    I servizi con stato possono essere partizionati per essere ridimensionati suddividendone lo stato in diversi bucket in base a una chiave, ad esempio l'ID cliente o il CAP. In questa semplice applicazione la chiave non è importante, perché il servizio con stato ha una sola partizione. Qualsiasi chiave specificata rimanda alla stessa partizione. Per altre informazioni sul partizionamento dei servizi, vedere [Partizionare Reliable Services di Service Fabric](service-fabric-concepts-partitioning.md).
   
    Il nome del servizio è un URI in formato fabric:/&lt;nome_applicazione&gt;/&lt;nome_servizio&gt;.
   
    Con queste due informazioni, Service Fabric può identificare in modo univoco il computer a cui inviare le richieste. La classe `ServiceProxy` gestirà facilmente anche i casi in cui il computer che ospita la partizione del servizio con stato presenta un errore e un altro computer deve essere alzato di livello per sostituirlo. Questa astrazione semplifica la scrittura di codice client per gestire altri servizi.
   
    Una volta che il proxy è disponibile, è sufficiente richiamare il metodo `GetCountAsync` e restituirne il risultato.

4. Premere di nuovo F5 per eseguire l'applicazione modificata. Come in precedenza, Visual Studio avvia automaticamente il browser nella radice del progetto Web. Aggiungere il percorso "api/values" per visualizzare il valore del contatore attualmente restituito.
   
    ![Valore del contatore con stato visualizzato nel browser](./media/service-fabric-tutorial-create-dotnet-app/browser-aspnet-counter-value.png)
   
    Aggiornare regolarmente il browser per visualizzare l'aggiornamento del valore del contatore.

Per arrestare il debug dell'applicazione, tornare a Visual Studio e premere **MAIUSC+F5**.

## <a name="next-steps"></a>Passaggi successivi
In questa parte dell'esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un servizio API Web ASP.NET Core come servizio Reliable
> * Creare un servizio Reliable con stato
> * Implementare la comunicazione remota del servizio e l'uso di un proxy del servizio

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Distribuire l'applicazione in Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
