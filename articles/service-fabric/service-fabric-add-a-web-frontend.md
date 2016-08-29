<properties
   pageTitle="Creare un front-end di servizio Web per l'applicazione tramite ASP.NET Core | Microsoft Azure"
   description="Esporre l'applicazione di Service Fabric al Web usando un progetto API Web ASP.NET Core e la comunicazione tra servizi con ServiceProxy."
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
   ms.date="08/11/2016"
   ms.author="seanmck"/>


# Compilare un front-end di servizio Web per l'applicazione tramite ASP.NET Core

Per impostazione predefinita, i servizi di Azure Service Fabric non forniscono un'interfaccia pubblica per il Web. Per esporre la funzionalità dell'applicazione ai client HTTP, sarà necessario creare un progetto Web da usare come punto di ingresso da cui comunicare con i singoli servizi.

Questa esercitazione è la prosecuzione dell'esercitazione [Creating your first application in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) (Creare la prima applicazione in Visual Studio). Verrà aggiunto un servizio Web davanti al servizio dei contatori con stato. Se non è già stato fatto, è necessario tornare indietro ed eseguire prima quella esercitazione.

## Aggiungere un servizio ASP.NET Core all'applicazione

ASP.NET Core è un framework di sviluppo Web multipiattaforma leggero, che consente di creare un'interfaccia utente Web e API Web moderne. Ora verrà aggiunto un progetto API Web ASP.NET all'applicazione esistente.

>[AZURE.NOTE] Per completare questa esercitazione è necessario [installare .NET Core 1.0][dotnetcore-install].

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Servizi** nel progetto dell'applicazione e scegliere **Aggiungi > Nuovo servizio Service Fabric**.

	![Aggiunta di un nuovo servizio a un'applicazione esistente][vs-add-new-service]

2. Nella pagina **Nuovo servizio Service Fabric** scegliere **ASP.NET Core** e assegnare un nome.

	![Scelta di un servizio Web ASP.NET nella finestra di dialogo del nuovo servizio][vs-new-service-dialog]

3. Nella pagina successiva è disponibile un set di modelli di progetto ASP.NET Core. Si tratta degli stessi modelli che verrebbero visualizzati se si creasse un progetto ASP.NET Core all'esterno di un'applicazione di Service Fabric. Per questa esercitazione si sceglierà **API Web**, ma è possibile applicare gli stessi concetti anche alla compilazione di un'applicazione Web completa.

	![Scelta di un tipo di progetto ASP.NET][vs-new-aspnet-project-dialog]

    Una volta creato il progetto API Web, l'applicazione includerà due servizi. Man mano che si compila l'applicazione, si aggiungeranno altri servizi seguendo esattamente la stessa procedura e, per ogni servizio, sarà possibile eseguire in modo indipendente il controllo della versione e l'aggiornamento.

>[AZURE.TIP] Per altre informazioni sulla compilazione di servizi ASP.NET Core, vedere [ASP.NET Core Documentation](https://docs.asp.net) (Documentazione di ASP.NET Core).

## Eseguire l'applicazione

Per avere un'idea di quanto è stato fatto, verrà ora distribuita la nuova applicazione ed esaminato il comportamento predefinito del modello API Web ASP.NET Core.

1. Premere F5 in Visual Studio per eseguire il debug dell'app.

2. Al termine della distribuzione, Visual Studio avvierà il browser nella radice del servizio API Web ASP.NET, ad esempio http://localhost:33003. Il numero della porta viene assegnato casualmente e può essere diverso su ciascun computer. Il modello API Web ASP.NET Core non prevede un comportamento predefinito per la radice, quindi verrà visualizzato un errore nel browser.

3. Aggiungere `/api/values` al percorso nel browser. Questa operazione richiamerà il metodo `Get` in ValuesController del modello API Web. e restituirà la risposta predefinita fornita dal modello, una matrice JSON contenente due stringhe:

    ![Valori predefiniti restituiti dal modello API Web ASP.NET Core][browser-aspnet-template-values]

    Al termine di questa esercitazione, questi valori predefiniti risulteranno sostituiti con il valore del contatore più recente del servizio con stato.


## Connettere i servizi

L'infrastruttura di servizi offre la massima flessibilità nella comunicazione con Reliable Services. In una singola applicazione possono coesistere servizi accessibili tramite TCP, altri tramite un'API REST HTTP e altri ancora tramite Web Socket. Per informazioni sulle opzioni disponibili e sui compromessi necessari, vedere [Comunicazione con i servizi](service-fabric-connect-and-communicate-with-services.md). In questa esercitazione si seguirà uno degli approcci più semplici e si useranno le classi `ServiceProxy`/`ServiceRemotingListener` messe a disposizione nell'SDK.

Nell'approccio `ServiceProxy`, basato sulle chiamate Remote Procedure Call o RPC, viene definita un'interfaccia da usare come contratto pubblico per il servizio e quindi si usa tale interfaccia per generare una classe proxy per l'interazione con il servizio.


### Creare l'interfaccia

Si inizierà creando l'interfaccia da usare come contratto tra il servizio con stato e i client, incluso il progetto ASP.NET Core.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Aggiungi** > **Nuovo progetto**.

2. Scegliere la voce **Visual C#** nel riquadro di spostamento sinistro e quindi selezionare il modello **Libreria di classi**. Verificare che la versione di .NET Framework sia impostata su **4.5.2**.

    ![Creazione di un progetto interfaccia per il servizio con stato][vs-add-class-library-project]

3. Per poter essere usata da `ServiceProxy`, un'interfaccia deve derivare dall'interfaccia IService, inclusa in uno dei pacchetti NuGet di Service Fabric. Per aggiungere il pacchetto, fare clic con il pulsante destro del mouse sul progetto della nuova libreria di classi e scegliere **Gestisci pacchetti NuGet**.

4. Cercare il pacchetto **Microsoft.ServiceFabric.Services** e installarlo.

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

Dopo aver definito l'interfaccia, è ora necessario implementarla nel servizio con stato.

1. Nel servizio con stato aggiungere un riferimento al progetto della libreria di classi contenente l'interfaccia.

    ![Aggiunta di un riferimento al progetto libreria di classi nel servizio con stato][vs-add-class-library-reference]

2. Trovare la classe che eredita da `StatefulService`, ad esempio `MyStatefulService`, ed estenderla per implementare l'interfaccia `ICounter`.

    ```c#
    using MyStatefulService.Interfaces;

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


### Esporre il servizio con stato usando un listener di comunicazione remota

Con l'interfaccia `ICounter` implementata, il passaggio finale per consentire al servizio con stato di essere chiamato da altri servizi è l'apertura di un canale di comunicazione. Per i servizi con stato, Service Fabric offre un metodo sostituibile denominato `CreateServiceReplicaListeners`, in cui è possibile specificare uno o più listener di comunicazione in base al tipo di comunicazione che si vuole abilitare per il servizio.

>[AZURE.NOTE] Il metodo equivalente per aprire un canale di comunicazione con i servizi senza stato è denominato `CreateServiceInstanceListeners`.

In questo caso si sostituirà il metodo `CreateServiceReplicaListeners` esistente e si specificherà un'istanza dell'oggetto `ServiceRemotingListener`, che crea un endpoint RPC chiamabile dai client tramite `ServiceProxy`.

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


### Usare la classe ServiceProxy per interagire con il servizio

Il servizio con stato è ora pronto per ricevere traffico da altri servizi e quindi non resta che aggiungere il codice per comunicare con esso dal servizio Web ASP.NET.

1. Nel progetto ASP.NET aggiungere un riferimento alla libreria di classi contenente l'interfaccia `ICounter`.

2. Scegliere **Gestione configurazione** dal menu **Compila**. Verrà visualizzata una schermata analoga alla seguente:

    ![Gestione configurazione che mostra la libreria di classi per AnyCPU][vs-configuration-manager]  

    Si noti che il progetto di libreria di classi, **MyStatefulService.Interface**, è configurato in modo da poter essere compilato per qualsiasi CPU. Per il corretto funzionamento di Service Fabric, deve essere esplicitamente destinato alle piattaforme a 64 bit (x64). Fare clic sul menu a discesa Piattaforma, scegliere **Nuovo** e quindi creare la configurazione della piattaforma a 64 bit (x64).

    ![Creazione della nuova piattaforma per la libreria di classi][vs-create-platform]  

3. Aggiungere il pacchetto Microsoft.ServiceFabric.Services al progetto ASP.NET, come è stato fatto prima per il progetto della libreria di classi. Si otterrà così la classe `ServiceProxy`.

4. Nella cartella **Controller** aprire la classe `ValuesController`. Si noti che il metodo `Get` restituisce attualmente solo una matrice di stringhe hardcoded con "value1" e "value2", che corrisponde a quanto visto in precedenza nel browser. Sostituire l'implementazione con il codice seguente:

    ```c#
    using MyStatefulService.Interfaces;
    using Microsoft.ServiceFabric.Services.Remoting.Client;

    ...

    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    La prima riga del codice è quella più importante. Per creare il proxy ICounter per il servizio con stato, è necessario fornire due informazioni: un ID partizione e il nome del servizio.

    Il partizionamento consente di ridimensionare i servizi con stato suddividendone lo stato in diversi bucket in base a una chiave definita, ad esempio l'ID cliente o il CAP. In questa semplice applicazione la chiave non è importante, perché il servizio con stato ha una sola partizione e qualsiasi chiave specificata restituirà la stessa partizione. Per altre informazioni sul partizionamento dei servizi, consultare l'articolo relativo al [partizionamento dei servizi di Service Fabric](service-fabric-concepts-partitioning.md).

    Il nome del servizio è un URI in formato fabric:/&lt;application\_name&gt;/&lt;service\_name&gt;.

    Con queste due informazioni, Service Fabric può identificare in modo univoco il computer a cui inviare le richieste. La classe `ServiceProxy` gestirà facilmente anche i casi in cui il computer che ospita la partizione del servizio con stato presenta un errore e un altro computer deve essere alzato di livello per sostituirlo. Questa astrazione semplifica notevolmente la scrittura di codice client per gestire gli altri servizi.

    Una volta che il proxy è disponibile, è sufficiente richiamare il metodo `GetCountAsync` e restituirne il risultato.

5. Premere di nuovo F5 per eseguire l'applicazione modificata. Come prima, Visual Studio avvierà automaticamente il browser nella radice del progetto Web. Aggiungere il percorso "api/values" per visualizzare il valore del contatore attualmente restituito.

    ![Valore del contatore con stato visualizzato nel browser][browser-aspnet-counter-value]  

    Aggiornare regolarmente il browser per visualizzare l'aggiornamento del valore del contatore.


>[AZURE.WARNING] Il server Web ASP.NET Core fornito nel modello, noto come Kestrel, [non è attualmente supportato per la gestione diretta del traffico Internet](https://docs.asp.net/en/latest/fundamentals/servers.html#kestrel). Per gli scenari di produzione, è consigliabile ospitare gli endpoint ASP.NET Core in [Gestione API][api-management-landing-page] o un altro gateway con connessione Internet. Si noti che la distribuzione di Service Fabric non è supportata in IIS.


## Come funzionano gli attori?

Questa esercitazione ha illustrato la procedura per aggiungere un front-end Web in grado di comunicare con un servizio con stato. Per comunicare con gli attori è possibile seguire un modello molto simile. Infatti è un po' più semplice.

Quando si crea un progetto attore, Visual Studio genera automaticamente un progetto interfaccia. È possibile usare tale interfaccia per generare un proxy attore nel progetto Web per comunicare con l'attore. Poiché il canale di comunicazione viene fornito automaticamente, non è necessario eseguire operazioni, ad esempio stabilire un oggetto `ServiceRemotingListener`, come per il servizio con stato.

## Funzionamento dei servizi Web in un cluster locale

In generale, è possibile distribuire esattamente la stessa applicazione di Service Fabric anche in un cluster con più computer distribuito nel cluster locale, avendo la certezza che funzionerà come previsto, perché il cluster locale è semplicemente una configurazione a cinque nodi compressa in un solo computer.

Quando si tratta di servizi Web, tuttavia, c'è una sola possibilità. Quando il cluster si trova dietro un servizio di bilanciamento del carico, come in Azure, è necessario assicurarsi che i servizi Web vengano distribuiti in ogni computer perché il servizio di bilanciamento del carico si limiterà a eseguire il round robin del traffico tra i computer. A tale scopo, è possibile impostare `InstanceCount` per il servizio sul valore speciale "-1".

Quando invece si esegue il servizio Web in locale, è necessario assicurarsi che solo un'istanza del servizio sia in esecuzione. In caso contrario, si verificheranno dei conflitti tra più processi in ascolto sullo stesso percorso e sulla stessa porta. Per le distribuzioni locali, quindi, è opportuno impostare il numero delle istanze del servizio Web su 1.

Per informazioni su come configurare valori diversi a seconda dell'ambiente, vedere [Gestione dei parametri dell'applicazione per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md).

## Passaggi successivi

- [Creare un cluster in Azure per la distribuzione dell'applicazione nel cloud](service-fabric-cluster-creation-via-portal.md)
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
[vs-configuration-manager]: ./media/service-fabric-add-a-web-frontend/vs-configuration-manager.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
[api-management-landing-page]: https://azure.microsoft.com/it-IT/services/api-management/

<!---HONumber=AcomDC_0817_2016-->