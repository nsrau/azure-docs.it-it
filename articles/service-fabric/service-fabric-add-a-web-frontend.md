<properties
   pageTitle="Creare un front-end Web per l'applicazione | Microsoft Azure"
   description="Esporre l'applicazione dell'infrastruttura di servizi al Web usando un progetto API Web ASP.NET 5 e la comunicazione tra servizi con ServiceProxy."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/21/2015"
   ms.author="seanmck"/>


# Compilare un front-end di servizio Web per l'applicazione

Per impostazione predefinita, i servizi di infrastruttura di servizi non forniscono un'interfaccia pubblica per il Web. Per esporre la funzionalità dell'applicazione ai client HTTP, sarà necessario creare un progetto Web da usare come punto di ingresso da cui comunicare con i singoli servizi.

In questa esercitazione viene illustrata in dettaglio l'aggiunta di un front-end di API Web ASP.NET 5 a un'applicazione che include già un servizio Reliable Service basato sul modello di un progetto di servizio con stato. Prima di iniziare questa esercitazione, vedere [Creare la prima applicazione in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md), se questa operazione non è già stata eseguita.


## Aggiungere un servizio ASP.NET 5 all'applicazione

ASP.NET 5 è un framework di sviluppo Web leggero multipiattaforma, che consente di creare un'interfaccia utente Web e API Web moderne. Ora verrà aggiunto un progetto API Web ASP.NET all'applicazione esistente.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Servizi** nel progetto dell'applicazione e scegliere **Aggiungi servizio di infrastruttura**.

	![Aggiunta di un nuovo servizio a un'applicazione esistente][vs-add-new-service]

2. Nella finestra di dialogo del nuovo servizio, scegliere **ASP.NET 5** e assegnargli un nome.

	![Scelta di un servizio Web ASP.NET nella finestra di dialogo del nuovo servizio][vs-new-service-dialog]

3. Nella finestra di dialogo successiva è disponibile un set di modelli di progetto ASP.NET 5. Si noti che i modelli sono gli stessi che verrebbero visualizzati se si creasse un progetto ASP.NET 5 al di fuori di un'applicazione dell'infrastruttura di servizi. Per questa esercitazione si usa **Web API**, ma è possibile applicare gli stessi concetti alla compilazione di un'applicazione Web completa.

	![Scelta di un tipo di progetto ASP.NET][vs-new-aspnet-project-dialog]

    Una volta creato il progetto API Web, l'applicazione includerà due servizi. Man mano che si compila l'applicazione, si aggiungeranno altri servizi esattamente nello stesso modo ed è possibile eseguire in modo indipendente il controllo della versione e l'aggiornamento di ogni servizio.


## Eseguire l'applicazione

Per avere un'idea di quanto è stato fatto, ora verrà distribuita la nuova applicazione e verrà esaminato il comportamento predefinito del modello API Web ASP.NET 5.

1. Premere F5 in Visual Studio per eseguire il debug dell'app.

2. Al termine della distribuzione, Visual Studio avvierà il browser nella radice del servizio API Web ASP.NET, ad esempio http://localhost:33003. Il numero della porta viene assegnato casualmente e potrebbe essere diverso su ciascun computer. Poiché il modello API Web ASP.NET 5 non prevede un comportamento predefinito per la radice, verrà visualizzato un errore nel browser.

3. Aggiungere `/api/values` al percorso nel browser. Questo richiamerà il metodo `Get` in ValuesController nel modello API Web e restituirà la risposta predefinita fornita dal modello, una matrice JSON contenente due stringhe:

    ![Valori predefiniti restituiti dal modello API Web ASP.NET 5][browser-aspnet-template-values]

    Al termine di questa esercitazione, questi valori predefiniti risulteranno sostituiti con il valore del contatore più recente del servizio con stato.


## Connettere i servizi

L'infrastruttura di servizi offre la massima flessibilità nella comunicazione con Reliable Services. In una singola applicazione possono esserci servizi accessibili tramite TCP, altri tramite un'API REST HTTP e altri ancora tramite Web Socket. Per informazioni sulle opzioni disponibili e sui compromessi richiesti, vedere [Comunicazione con i servizi](service-fabric-connect-and-communicate-with-services.md). In questa esercitazione verrà seguito uno degli approcci più semplici e verranno usate le classi `ServiceProxy`/`ServiceCommunicationListener` fornite nell'SDK.

Nell'approccio `ServiceProxy` (basato sulle chiamate Remote Procedure Call o RPC) si definisce un'interfaccia da usare come contratto pubblico per il servizio e quindi si usa tale interfaccia per generare una classe proxy per interagire con il servizio.


### Creare l'interfaccia

Si inizierà creando l'interfaccia da usare come contratto tra il servizio con stato e i client, incluso il progetto ASP.NET 5.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Aggiungi > Nuovo progetto**.

2. Scegliere la voce Visual C# nel riquadro di spostamento sinistro e quindi selezionare il modello **Libreria di classi**. Verificare che la versione di .NET Framework sia impostata su 4.5.1.

    ![Creazione di un progetto interfaccia per il servizio con stato][vs-add-class-library-project]

3. Un'interfaccia, per poter essere usata da `ServiceProxy`, deve derivare dall'interfaccia IService, inclusa in uno dei pacchetti NuGet di Service Fabric. Per aggiungere il pacchetto, fare clic con il pulsante destro del mouse sul progetto della nuova libreria di classi e scegliere **Gestisci pacchetti NuGet**.

4. Verificare che la casella di controllo **Includi versione preliminare** sia selezionata, quindi cercare il pacchetto **Microsoft.ServiceFabric.Services** e installarlo.

    ![Aggiunta del pacchetto NuGet dei servizi][vs-services-nuget-package]

5. Nella libreria di classi creare un'interfaccia con un solo metodo, `GetCountAsync`, ed estendere l'interfaccia da IService.

    ```c#
    namespace MyStatefulService.Interfaces
    {
        using Microsoft.ServiceFabric.Services.Remoting;

        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```


### Implementare l'interfaccia nel servizio con stato

Ora che si è definita l'interfaccia, è necessario implementarla nel servizio con stato.

1. Nel servizio con stato aggiungere un riferimento al progetto della libreria di classi contenente l'interfaccia.

    ![Aggiunta di un riferimento al progetto libreria di classi nel servizio con stato][vs-add-class-library-reference]

2. Individuare la classe che eredita da `StatefulService`, ad esempio `MyStatefulService`, ed estenderla per implementare l'interfaccia `ICounter`.

    ```c#
    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. Implementare ora il singolo metodo che ha definito l'interfaccia `ICounter`: `GetCountAsync`.

    ```c#
    public async Task<long> GetCountAsync()
    {
      var myDictionary =
        await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```


### Esporre il servizio con stato usando ServiceCommunicationListener

Con l'interfaccia `ICounter` implementata, il passaggio finale per consentire al servizio con stato di essere chiamato da altri servizi è l'apertura di un canale di comunicazione. Per i servizi con stato, Service Fabric fornisce un metodo sostituibile denominato `CreateServiceReplicaListeners`, in cui è possibile specificare uno o più listener di comunicazione in base al tipo di comunicazione che si desidera abilitare per il servizio.

>[AZURE.NOTE]Il metodo equivalente per aprire un canale di comunicazione con i servizi senza stato è denominato `CreateServiceInstanceListeners`.

In questo caso verrà fornito un oggetto `ServiceCommunicationListener`, che crea un endpoint RPC chiamabile dai client tramite `ServiceProxy`.

```c#
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (initParams) =>
                new ServiceCommunicationListener<ICounter>(initParams, this))
    };
}
```


### Usare ServiceProxy per interagire con il servizio

Il servizio con stato ora è pronto per ricevere il traffico da altri servizi, quindi non resta che aggiungere il codice per comunicare dal servizio Web ASP.NET.

1. Nel progetto ASP.NET aggiungere un riferimento alla libreria di classi contenente l'interfaccia `ICounter`.

2. Aggiungere il pacchetto Microsoft.ServiceFabric.Services al progetto ASP.NET, come è stato fatto prima per il progetto della libreria di classi. Si otterrà così la classe `ServiceProxy`.

3. Nella cartella dei controller aprire la classe `ValuesController`. Attualmente, il metodo `Get` restituisce solo una matrice di stringhe hardcoded con "value1" e "value2", che corrisponde a quanto si è visto prima nel browser. Sostituire l'implementazione con il codice seguente:

    ```c#
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(0, new Uri("fabric:/MyApp/MyStatefulService"));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    La prima riga del codice è quella più importante. Per creare il proxy ICounter per il servizio con stato, è necessario fornire due informazioni: un ID partizione e il nome del servizio.

    Il partizionamento consente di ridimensionare i servizi con stato suddividendone lo stato in bucket diversi in base a una chiave definita, ad esempio l'ID cliente o il CAP. In questa semplice applicazione la chiave non è importante, perché il servizio con stato ha una sola partizione e qualsiasi chiave specificata restituirà la stessa partizione. Per altre informazioni sul partizionamento dei servizi, consultare l'articolo relativo al [partizionamento dei servizi di Service Fabric](service-fabric-concepts-partitioning).

    Il nome del servizio è un URI in formato fabric:/&lt;application\_name&gt;/&lt;service\_name&gt;.

    Con queste due informazioni, l'infrastruttura di servizi può identificare in modo univoco il computer a cui inviare le richieste. `ServiceProxy` gestirà facilmente anche i casi in cui il computer che ospita la partizione del servizio con stato presenta un guasto e un altro computer deve essere alzato di livello per sostituirlo. Questa astrazione semplifica notevolmente la scrittura di codice client per gestire gli altri servizi.

    Una volta che il proxy è disponibile, è sufficiente richiamare il metodo `GetCountAsync` e restituirne il risultato.

4. Premere ancora F5 per eseguire l'applicazione modificata. Come prima, Visual Studio avvierà automaticamente il browser nella radice del progetto Web. Aggiungere il percorso "api/values" per visualizzate il valore del contatore corrente restituito.

    ![Valore del contatore con stato visualizzato nel browser][browser-aspnet-counter-value]

    Aggiornare regolarmente il browser per visualizzare l'aggiornamento del valore del contatore.


## Come funzionano gli attori?

Questa esercitazione ha illustrato l'aggiunta di un front-end Web che comunica con un servizio con stato, ma è possibile seguire un modello molto simile per comunicare con gli attori. Infatti è un po' più semplice.

Quando si crea un progetto attore, Visual Studio genera automaticamente un progetto interfaccia. È possibile usare tale interfaccia per generare un proxy attore nel progetto Web per comunicare con l'attore. Poiché il canale di comunicazione viene fornito automaticamente, non è necessario eseguire operazioni quali stabilire un oggetto `ServiceCommunicationListener`, come per il servizio con stato.

## Esecuzione di servizi Web in un cluster locale

In generale, è possibile distribuire esattamente la stessa applicazione dell'infrastruttura di servizi in un cluster con più computer distribuito nel cluster locale, avendo la certezza che funzionerà come previsto, perché il cluster locale è semplicemente una configurazione a cinque nodi compressa in un solo computer.

Quando si tratta di servizi Web, tuttavia, c'è una sola possibilità. Quando il cluster si trova dietro un servizio di bilanciamento del carico, come in Azure, è necessario assicurarsi che i servizi Web vengano distribuiti in ogni computer perché il servizio di bilanciamento del carico si limiterà a eseguire il round robin del traffico tra i computer. A tale scopo, è possibile impostare `InstanceCount` per il servizio sul valore speciale -1. Invece, quando l'esecuzione è locale, è necessario assicurarsi che solo un'istanza del servizio sia in esecuzione. In caso contrario, si verificheranno conflitti tra più processi in ascolto sullo stesso percorso e sulla stessa porta. Di conseguenza, è opportuno impostare il conteggio delle istanze del servizio Web su 1 per le distribuzioni locali.

Per informazioni su come configurare valori diversi a seconda dell'ambiente, vedere [Gestione dei parametri dell'applicazione per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md).

## Passaggi successivi

- [Creare un cluster nella distribuzione dell'applicazione nel cloud con Azure](service-fabric-cluster-creation-via-portal.md)
- [Altre informazioni sulla comunicazione con i servizi](service-fabric-connect-and-communicate-with-services.md)
- [Altre informazioni sul partizionamento dei servizi con stato](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png

<!---HONumber=AcomDC_1125_2015-->