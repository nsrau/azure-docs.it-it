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
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: ef50adf3af19bce494c3256308b443c8eaccdcea
ms.contentlocale: it-it
ms.lasthandoff: 08/17/2017

---

# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Creare e distribuire un'applicazione con un servizio front-end API Web ASP.NET Core e un servizio back-end con stato
Questa è la prima di una serie di esercitazioni.  Illustra come creare un'applicazione di Azure Service Fabric con un front-end API Web ASP.NET Core e un servizio back-end con stato per archiviare i dati. Al termine, sarà disponibile un'applicazione di voto con un front-end Web ASP.NET Core che salva i risultati delle votazioni in un servizio back-end con stato nel cluster. Se non si vuole creare manualmente l'applicazione di voto, è possibile [scaricare il codice sorgente](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) per l'applicazione completata e passare direttamente alla [Descrizione dettagliata dell'applicazione di voto di esempio](#walkthrough_anchor).

![Diagramma dell'applicazione](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Nella prima parte della serie si apprenderà come:

> [!div class="checklist"]
> * Creare un servizio API Web ASP.NET Core come servizio Reliable con stato
> * Creare un servizio applicazione Web ASP.NET Core come servizio Web senza stato
> * Usare il proxy inverso per comunicare con il servizio con stato

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
Per prima cosa, creare il front-end Web dell'applicazione di voto usando ASP.NET Core. ASP.NET Core è un framework di sviluppo Web multipiattaforma leggero, che consente di creare un'interfaccia utente Web e API Web moderne. Per comprendere a fondo la modalità di integrazione di ASP.NET Core con Service Fabric, si consiglia di leggere l'articolo [ASP.NET Core in Reliable Services di Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md). Per il momento, è possibile seguire questa esercitazione per essere subito operativi. Per altre informazioni su ASP.NET Core, vedere la [documentazione di ASP.NET Core](https://docs.microsoft.com/aspnet/core/).

> [!NOTE]
> L'esercitazione si basa sugli strumenti di [ASP.NET Core per Visual Studio 2017](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/start-mvc). Gli strumenti di .NET Core per Visual Studio 2015 non saranno più oggetto di aggiornamenti.

1. Avviare Visual Studio come **amministratore**.

2. Creare un progetto da **File**->**Nuovo**->**Progetto**

3. Nella finestra di dialogo **Nuovo progetto** scegliere **Cloud > Applicazione di Service Fabric**.

4. Assegnare all'applicazione il nome **Voting** e fare clic su **OK**.

   ![Finestra di dialogo Nuovo progetto in Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. Nella pagina **Nuovo servizio Service Fabric** scegliere **ASP.NET Core senza stato** e assegnare al servizio il nome **VotingWeb**.
   
   ![Scelta di un servizio Web ASP.NET nella finestra di dialogo del nuovo servizio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. Nella pagina successiva è disponibile un set di modelli di progetto ASP.NET Core. Per questa esercitazione scegliere **Applicazione Web**. 
   
   ![Scegliere un tipo di progetto ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio crea un'applicazione e un progetto di servizio e li visualizza in Esplora soluzioni.

   ![Esplora soluzioni dopo la creazione dell'applicazione con il servizio API Web ASP.NET Core]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-angularjs-to-the-votingweb-service"></a>Aggiungere AngularJS al servizio VotingWeb
Aggiungere [AngularJS](http://angularjs.org/) al servizio usando il [supporto per Bower](/aspnet/core/client-side/bower) predefinito. Aprire *bower.json* e aggiungere le voci appropriate per Angular e Angular-Bootstrap e quindi salvare le modifiche.

```json
{
  "name": "asp.net",
  "private": true,
  "dependencies": {
    "bootstrap": "3.3.7",
    "jquery": "2.2.0",
    "jquery-validation": "1.14.0",
    "jquery-validation-unobtrusive": "3.2.6",
    "angular": "v1.6.5",
    "angular-bootstrap": "v1.1.0"
  }
}
```
Durante il salvataggio del file *bower.json*, Angular viene installato nella cartella *wwwroot/lib* del progetto. Viene anche elencato nella cartella *Dependencies/Bower*.

### <a name="update-the-sitejs-file"></a>Aggiornare il file site.js
Aprire il file *wwwroot/js/site.js*.  Sostituirne il contenuto con il codice JavaScript usato dalle visualizzazioni Home:

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Aggiornare il file Index.cshtml
Aprire il file *Views/Home/Index.cshtml*, ovvero la visualizzazione specifica del controller Home.  Sostituirne il contenuto con il codice seguente e quindi salvare le modifiche.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-layoutcshtml-file"></a>Aggiornare il file _Layout.cshtml
Aprire il file *Views/Shared/_Layout.cshtml*, ovvero il layout predefinito per l'app ASP.NET.  Sostituirne il contenuto con il codice seguente e quindi salvare le modifiche.

```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="http://angularjs.org">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="~/css/site.css" rel="stylesheet" />

</head>
<body>
    <div class="container body-content">
        @RenderBody()
    </div>

    <script src="~/lib/jquery/dist/jquery.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
    <script src="~/lib/angular/angular.js"></script>
    <script src="~/lib/angular-bootstrap/ui-bootstrap-tpls.js"></script>
    <script src="~/js/site.js"></script>

    @RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>Aggiornare il file VotingWeb.cs
Aprire il file *VotingWeb.cs*, che crea il WebHost ASP.NET Core all'interno del servizio senza stato tramite il server Web WebListener.  Aggiungere la direttiva `using System.Net.Http;` all'inizio del file.  Sostituire la funzione `CreateServiceInstanceListeners()` con il codice seguente e quindi salvare le modifiche.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
            {
                ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting WebListener on {url}");

                return new WebHostBuilder().UseWebListener()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<StatelessServiceContext>(serviceContext)
                                    .AddSingleton<HttpClient>())
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseApplicationInsights()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
            }))
    };
}
```

### <a name="add-the-votescontrollercs-file"></a>Aggiungere il file VotesController.cs
Aggiungere un controller che definisce le azioni di voto. Fare clic con il pulsante destro del mouse sulla cartella **Controller** e quindi selezionare **Aggiungi->Nuovo elemento->Classe**.  Assegnare al file il nome "VotesController.cs" e fare clic su **Aggiungi**.  Sostituire il contenuto del file con il codice seguente e quindi salvare le modifiche.  Nella sezione [Aggiornare il file VotesController.cs](#updatevotecontroller_anchor) disponibile più avanti, questo file verrà modificato per poter leggere e scrivere i dati di voto dal servizio back-end.  Per il momento, il controller restituisce nella visualizzazione i dati di una stringa statica.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using Newtonsoft.Json;
using System.Text;
using System.Net.Http;
using System.Net.Http.Headers;

namespace VotingWeb.Controllers
{
    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```



### <a name="deploy-and-run-the-application-locally"></a>Distribuire ed eseguire l'applicazione in locale
È ora possibile andare avanti con l'esercitazione ed eseguire l'applicazione. In Visual Studio premere `F5` per distribuire l'applicazione per il debug. `F5` ha esito negativo se in precedenza Visual Studio non è stato aperto come **amministratore**.

> [!NOTE]
> La prima volta che si esegue e si distribuisce l'applicazione in locale, Visual Studio crea un cluster locale per il debug.  La creazione del cluster può richiedere del tempo. Lo stato della creazione del cluster verrà visualizzato nella finestra di output di Visual Studio.

L'app Web, a questo punto, dovrebbe aver l'aspetto seguente:

![Front-end ASP.NET Core](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Per arrestare il debug dell'applicazione, tornare a Visual Studio e premere **MAIUSC+F5**.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Aggiungere un servizio back-end con stato a un'applicazione
Ora che c'è un servizio API Web ASP.NET in esecuzione nell'applicazione, è possibile procedere e aggiungere un servizio Reliable con stato per archiviare alcuni dati nell'applicazione.

Service Fabric consente di archiviare in modo coerente e affidabile i dati all'interno del servizio usando raccolte Reliable Collections. Le Reliable Collection sono un set di classi di raccolte con elevati livelli di disponibilità e affidabilità che risultano familiari a chiunque abbia usato raccolte C#.

In questa esercitazione si creerà un servizio che archivia un valore del contatore in una Reliable Collection.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Servizi** nel progetto dell'applicazione e scegliere **Aggiungi > Nuovo servizio Service Fabric**.
   
    ![Aggiunta di un nuovo servizio a un'applicazione esistente](./media/service-fabric-tutorial-create-dotnet-app/vs-add-new-service.png)

2. Nella finestra di dialogo **Nuovo servizio Service Fabric** scegliere **ASP.NET Core con stato**, assegnare al servizio il nome **VotingData** e fare clic su **OK**.

    ![Finestra di dialogo Nuovo servizio in Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    Una volta creato il progetto di servizio, l'applicazione includerà due servizi. Man mano che si compila l'applicazione, è possibile aggiungere altri servizi nello stesso modo. Per ogni servizio, sarà possibile eseguire in modo indipendente il controllo della versione e l'aggiornamento.

3. Nella pagina successiva è disponibile un set di modelli di progetto ASP.NET Core. Per questa esercitazione si sceglierà **API Web**,

    ![Scegliere un tipo di progetto ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog2.png)

    Visual Studio crea un progetto di servizio e lo visualizza in Esplora soluzioni.

    ![Esplora soluzioni](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>Aggiungere il file VoteDataController.cs

Nel progetto **VotingData** fare clic con il pulsante destro del mouse sulla cartella **Controller** e quindi selezionare **Aggiungi->Nuovo elemento->Classe**. Assegnare al file il nome "VoteDataController.cs" e fare clic su **Aggiungi**. Sostituire il contenuto del file con il codice seguente e quindi salvare le modifiche.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.ServiceFabric.Data;
using System.Threading;
using Microsoft.ServiceFabric.Data.Collections;

namespace VotingData.Controllers
{
    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            var ct = new CancellationToken();

            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                var list = await votesDictionary.CreateEnumerableAsync(tx);

                var enumerator = list.GetAsyncEnumerator();

                var result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```


## <a name="connect-the-services"></a>Connettere i servizi
Nel passaggio successivo i due servizi verranno connessi e l'applicazione Web front-end otterrà e imposterà informazioni di voto dal servizio back-end.

L'infrastruttura di servizi offre la massima flessibilità nella comunicazione con Reliable Services. All'interno di una singola applicazione possono esserci servizi accessibili tramite TCP. Altri servizi potrebbero essere accessibili tramite un'API REST HTTP e altri ancora tramite Web Socket. Per informazioni sulle opzioni disponibili e sui compromessi necessari, vedere [Comunicazione con i servizi](service-fabric-connect-and-communicate-with-services.md).

In questa esercitazione si userà l'[API Web ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md).

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>Aggiornare il file VotesController.cs
Nel progetto **VotingWeb** aprire il file *Controllers/VotesController.cs*.  Sostituire il contenuto della definizione di classe `VotesController` con il codice seguente e quindi salvare le modifiche.

```csharp
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;
        string serviceProxyUrl = "http://localhost:19081/Voting/VotingData/api/VoteData";
        string partitionKind = "Int64Range";
        string partitionKey = "0";

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            IEnumerable<KeyValuePair<string, int>> votes;

            HttpResponseMessage response = await this.httpClient.GetAsync($"{serviceProxyUrl}?PartitionKind={partitionKind}&PartitionKey={partitionKey}");

            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int)response.StatusCode);
            }

            votes = JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync());

            return Json(votes);
        }

        // PUT: api/Votes/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            string payload = $"{{ 'name' : '{name}' }}";
            StringContent putContent = new StringContent(payload, Encoding.UTF8, "application/json");
            putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

            string proxyUrl = $"{serviceProxyUrl}/{name}?PartitionKind={partitionKind}&PartitionKey={partitionKey}";

            HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent);

            return new ContentResult()
            {
                StatusCode = (int)response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }

        // DELETE: api/Votes/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            HttpResponseMessage response = await this.httpClient.DeleteAsync($"{serviceProxyUrl}/{name}?PartitionKind={partitionKind}&PartitionKey={partitionKey}");

            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int)response.StatusCode);
            }

            return new OkResult();

        }
    }
```
<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>Descrizione dettagliata dell'applicazione di voto di esempio
L'applicazione di voto è costituita da due servizi:
- Il servizio front-end Web (VotingWeb) - Un servizio front-end Web ASP.NET Core che gestisce la pagina Web e che espone le API Web per la comunicazione con il servizio back-end.
- Il servizio back-end (VotingData) - Un servizio Web ASP.NET Core che espone un'API per l'archiviazione dei risultati delle votazioni in un oggetto Reliable Dictionary reso persistente su disco.

![Diagramma dell'applicazione](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Quando l'utente vota nell'applicazione, si verificano gli eventi seguenti:
1. JavaScript invia la richiesta di voto all'API Web nel servizio front-end Web come una richiesta HTTP PUT.

2. Il servizio front-end Web usa un proxy per individuare e inoltrare una richiesta PUT HTTP al servizio back-end.

3. Il servizio back-end accetta la richiesta in ingresso e archivia il risultato aggiornato in un oggetto Reliable Dictionary, che viene replicato in più nodi all'interno del cluster e reso persistente su disco. Tutti i dati dell'applicazione sono archiviati nel cluster, quindi non è necessario alcun database.

## <a name="debug-in-visual-studio"></a>Eseguire il debug in Visual Studio
Durante il debug dell'applicazione in Visual Studio, viene usato un cluster di sviluppo locale di Service Fabric. È possibile modificare l'esperienza di debug in base allo specifico scenario. In questa applicazione, i dati vengono archiviati nel servizio back-end tramite un oggetto Reliable Dictionary. Visual Studio rimuove l'applicazione per impostazione predefinita quando si arresta il debugger. La rimozione dell'applicazione determina la rimozione anche dei dati nel servizio back-end. Per rendere persistenti i dati tra le sessioni di debug, è possibile modificare la proprietà **Modalità di debug applicazione** del progetto **Voting** in Visual Studio.

Per osservare che cosa avviene nel codice, completare la procedura seguente:
1. Aprire il file **VotesController.cs** e impostare un punto di interruzione nel metodo **Put** dell'API Web (riga 47). È possibile eseguire una ricerca nel file usando Esplora soluzioni in Visual Studio.

2. Aprire il file **VoteDataController.cs** e impostare un punto di interruzione nel metodo **Put** dell'API Web (riga 50).

3. Tornare al browser e fare clic su un'opzione di voto oppure aggiungere una nuova opzione di voto. È stato raggiunto il primo punto di interruzione nel controller API del front-end Web.
    
    1. In questa posizione, il codice JavaScript nel browser invia una richiesta al controller API Web nel servizio front-end.
    
    ![Aggiungere il servizio front-end di voto](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

    2. Prima di tutto, costruire l'URL del proxy inverso per il servizio back-end **(1)**.
    3. Inviare quindi la richiesta PUT HTTP al proxy inverso **(2)**.
    4. Infine, restituire la risposta dal servizio back-end al client **(3)**.

4. Premere **F5** per continuare.
    1. Ora ci troviamo al punto di interruzione nel servizio back-end.
    
    ![Aggiungere il servizio back-end di voto](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

    2. Nella prima riga del metodo **(1)** usiamo `StateManager` per ottenere o aggiungere un oggetto Reliable Dictionary denominato `counts`.
    3. Tutte le interazioni con i valori in un oggetto Reliable Dictionary richiedono una transazione, che viene creata dall'istruzione using **(2)**.
    4. Nella transazione, aggiorniamo quindi il valore della chiave pertinente per l'opzione di voto e viene eseguito il commit dell'operazione **(3)**. Dopo la restituzione del metodo Commit, i dati vengono aggiornati nel dizionario e replicati negli altri nodi del cluster. A questo punto, i dati sono archiviati in modo sicuro nel cluster e il servizio back-end può eseguire il failover in altri nodi, rendendo comunque disponibili i dati.
5. Premere **F5** per continuare.

Per interrompere la sessione di debug, premere **MAIUSC+F5**.


## <a name="next-steps"></a>Passaggi successivi
In questa parte dell'esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un servizio API Web ASP.NET Core come servizio Reliable con stato
> * Creare un servizio applicazione Web ASP.NET Core come servizio Web senza stato
> * Usare il proxy inverso per comunicare con il servizio con stato

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Distribuire l'applicazione in Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
