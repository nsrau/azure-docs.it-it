---
title: Creare un'applicazione Java per Service Fabric | Microsoft Docs
description: Questa esercitazione illustra come creare un'applicazione Java Reliable Services con un front-end, creare un back-end Reliable Services con stato e distribuire l'applicazione in un cluster.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: dc67de00abb2eac2eeb6e2b6bf3798e3aa210152
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-create-and-deploy-an-application-with-a-java-web-api-front-end-service-and-a-stateful-back-end-service"></a>Esercitazione: creare e distribuire un'applicazione con un servizio front-end API Web Java e un servizio back-end con stato
Questa è la prima di una serie di esercitazioni. Al termine sarà disponibile un'applicazione Voting con un front-end Web Java che salva i risultati delle votazioni in un servizio back-end con stato nel cluster. Questa serie di esercitazioni presuppone un computer di sviluppo con Mac OSX o Linux. Se non si vuole creare manualmente l'applicazione di voto, è possibile [scaricare il codice sorgente per l'applicazione completa](https://github.com/Azure-Samples/service-fabric-java-quickstart) e passare direttamente a [Descrizione dettagliata dell'applicazione di voto di esempio](service-fabric-tutorial-create-java-app.md#walk-through-the-voting-sample-application).

![App Voting locale](./media/service-fabric-tutorial-create-java-app/votingjavalocal.png)

Nella prima parte della serie si apprenderà come:

> [!div class="checklist"]
> * Creare un servizio Reliable Java con stato
> * Creare un servizio di applicazione Web Java senza stato
> * Usare la comunicazione remota per comunicare con il servizio con stato
> * Distribuire l'applicazione in un cluster di Service Fabric locale

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> *  Compilare un'applicazione Reliable Services di Service Fabric per Java
> * [Distribuire l'applicazione ed eseguirne il debug in un cluster locale](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Distribuire l'applicazione in un cluster di Azure](service-fabric-tutorial-java-deploy-azure.md)
> * [Configurare il monitoraggio e la diagnostica per l'applicazione](service-fabric-tutorial-java-elk.md)
> * [Configurare CI/CD](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Configurare l'ambiente di sviluppo per [Mac](service-fabric-get-started-mac.md) o [Linux](service-fabric-get-started-linux.md). Seguire le istruzioni per installare il plug-in Eclipse, Gradle, Service Fabric SDK e l'interfaccia della riga di comando di Service Fabric (sfctl).

## <a name="create-the-front-end-java-stateless-service"></a>Creare il servizio front-end Java senza stato
Per prima cosa, creare il front-end Web dell'applicazione Voting. Il servizio Java senza stato configura un server HTTP lightweight che ospita un'interfaccia utente Web basata su AngularJS. Le richieste provenienti da un utente vengono elaborate da questo servizio senza stato e inviate come chiamata RPC al servizio con stato per archiviare i voti. 

1. Avviare Eclipse

2. Creare un progetto con **File**->**New (Nuovo)**->**Other (Altro)**->**Service Fabric**->**Service Fabric Project (Progetto di Service Fabric)**

    ![Finestra di dialogo del nuovo progetto in Eclipse](./media/service-fabric-tutorial-create-java-app/create-sf-proj-wizard.png)

3. Nella finestra di dialogo **ServiceFabric Project Wizard** (Procedura guidata progetto ServiceFabric), denominare il progetto **Voting** e fare clic su **Next** (Avanti)

    ![Scelta di un servizio Java senza stato nella finestra di dialogo del nuovo servizio](./media/service-fabric-tutorial-create-java-app/name-sf-proj-wizard.png) 

4. Nella pagina **Add Service** (Aggiungi servizio) scegliere **Stateless Service** (Servizio senza stato) e assegnare il nome **VotingWeb** al servizio. Fare clic su **Finish** (Fine) per creare il progetto.
   
    ![Creare il servizio senza stato]( ./media/service-fabric-tutorial-create-java-app/createvotingweb.png)

    Eclipse crea un'applicazione e un progetto di servizio e li visualizza in Package Explorer (Esplora pacchetti).

    ![Package Explorer (Esplora pacchetti) di Eclipse dopo la creazione dell'applicazione]( ./media/service-fabric-tutorial-create-java-app/eclipse-package-explorer.png)

La tabella fornisce una breve descrizione di ogni elemento di Package Explorer (Esplora pacchetti) presente nello screenshot precedente. 
| **Elemento di Package Explorer (Esplora pacchetti)** | **Descrizione** |
| --- | --- |
| PublishProfiles | Contiene i file JSON che descrivono i dettagli del profilo dei cluster locali e di Azure Service Fabric. Il contenuto di questi file viene usato dal plug-in quando si distribuisce l'applicazione. |
| Script | Contiene script helper che possono essere usati dalla riga di comando per gestire rapidamente l'applicazione con un cluster. |
| VotingApplication | Contiene l'applicazione di Service Fabric della quale viene eseguito il push nel cluster di Service Fabric. |
| VotingWeb | Contiene i file di origine del servizio senza stato front-end insieme al file di compilazione Gradle correlato. |
| build.gradle | File Gradle usato per gestire il progetto. |
| settings.gradle | Contiene i nomi dei progetti Gradle presenti in questa cartella. |

### <a name="add-html-and-javascript-to-the-votingweb-service"></a>Aggiungere HTML e Javascript al servizio VotingWeb
Per aggiungere un'interfaccia utente che può essere eseguita dal servizio senza stato, aggiungere un file HTML in *VotingApplication/VotingWebPkg/Code*. Il rendering di questo file HTML viene quindi eseguito dal server HTTP lightweight integrato nel servizio Java senza stato. 

1. Espandere la directory *VotingApplication* per raggiungere la directory *VotingApplication/VotingWebPkg/Code*. 

2. Fare clic con il pulsante destro del mouse sulla directory *Code* e scegliere **New (Nuovo)**->**Other (Altro)**

3. Creare una cartella denominata *wwwroot* e fare clic su **Finish** (Fine)

    ![Creare la cartella wwwroot in Eclipse](./media/service-fabric-tutorial-create-java-app/create-wwwroot-folder.png)

4. Aggiungere un file denominato **index.html** alla cartella **wwwroot** e incollare il contenuto seguente in questo file.

```html
<!DOCTYPE html>
<html>
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
<script src="http://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/0.13.4/ui-bootstrap-tpls.min.js"></script>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
<body>

<script>
var app = angular.module('VotingApp', ['ui.bootstrap']); 
app.controller("VotingAppController", ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {
    $scope.votes = [];
    
    $scope.refresh = function () {
        $http.get('getStatelessList')
            .then(function successCallback(response) {
        $scope.votes = Object.assign(
          {},
          ...Object.keys(response.data) .
            map(key => ({[decodeURI(key)]: response.data[key]}))
        )
        },
        function errorCallback(response) {
            alert(response);
        });
    };

    $scope.remove = function (item) {            
       $http.get("removeItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };

    $scope.add = function (item) {
        if (!item) {return;}        
        $http.get("addItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });        
    };
}]);
</script>

<div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-offset-2">
                <form style="width:50% ! important;" class="center-block">
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
                <div class="row top-buffer" ng-repeat="(key, value)  in votes">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(key)">
                            <span class="pull-left">
                                {{key}}
                            </span>
                            <span class="badge pull-right">
                                {{value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

</body>
</html>
```

### <a name="update-the-votingwebservicejava-file"></a>Aggiornare il file VotingWebService.java
Nel sottoprogetto **VotingWeb** aprire il file *VotingWeb/src/statelessservice/VotingWebService.java*. **VotingWebService** è il gateway nel servizio senza stato ed è responsabile della configurazione del listener di comunicazione per l'API front-end. 

Sostituire il contenuto del metodo **createServiceInstanceListeners** nel file con il contenuto seguente e salvare le modifiche.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {

    EndpointResourceDescription endpoint = this.getServiceContext().getCodePackageActivationContext().getEndpoint(webEndpointName);
    int port = endpoint.getPort();
    
    List<ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
    listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationListener(context, port)));
    return listeners;
}
```

### <a name="add-the-httpcommunicationlistenerjava-file"></a>Aggiungere il file HTTPCommunicationListener.java
Il listener di comunicazione HTTP funge da controller che configura il server HTTP ed espone le API che definiscono le azioni di voto. Fare clic con il pulsante destro del mouse sul pacchetto *statelessservice* nella cartella *VotingWeb/src/statelessservice*, quindi scegliere **New (Nuovo) ->Other (Altro)...->General (Generale) ->File** e fare clic su **Next** (Avanti).  Denominare il file *HttpCommunicationListener.java* e fare clic su **Finish** (Fine).  

Sostituire il contenuto del file con il codice seguente e quindi salvare le modifiche.  Nella sezione [Aggiornare il file HttpCommunicationListener.java](#updatelistener_anchor) disponibile più avanti, questo file viene modificato per eseguire il rendering, leggere e scrivere i dati di voto dal servizio back-end.  Per il momento, il listener restituisce semplicemente il codice HTML statico per l'app Voting.

```java
// ------------------------------------------------------------
//  Copyright (c) Microsoft Corporation.  All rights reserved.
//  Licensed under the MIT License (MIT). See License.txt in the repo root for license information.
// ------------------------------------------------------------

package statelessservice;

import com.google.gson.Gson;
import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.Headers;

import java.io.File;
import java.io.OutputStream;
import java.io.FileInputStream;

import java.net.InetSocketAddress;
import java.net.URI;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.CompletableFuture;
import java.util.logging.Level;
import java.util.logging.Logger;

import microsoft.servicefabric.services.communication.runtime.CommunicationListener;
import microsoft.servicefabric.services.runtime.StatelessServiceContext;
import microsoft.servicefabric.services.client.ServicePartitionKey;
import microsoft.servicefabric.services.remoting.client.ServiceProxyBase;
import microsoft.servicefabric.services.communication.client.TargetReplicaSelector;
import system.fabric.CancellationToken;

public class HttpCommunicationListener implements CommunicationListener {

    private static final Logger logger = Logger.getLogger(HttpCommunicationListener.class.getName());
    
    private static final String HEADER_CONTENT_TYPE = "Content-Type";
    private static final int STATUS_OK = 200;
    private static final int STATUS_NOT_FOUND = 404; 
    private static final int STATUS_ERROR = 500;
    private static final String RESPONSE_NOT_FOUND = "404 (Not Found) \n";
    private static final String MIME = "text/html";  
    private static final String ENCODING = "UTF-8";
    
    private static final String ROOT = "wwwroot/";
    private static final String FILE_NAME = "index.html";
    private StatelessServiceContext context;
    private com.sun.net.httpserver.HttpServer server;
    private ServicePartitionKey partitionKey;
    private final int port;

    public HttpCommunicationListener(StatelessServiceContext context, int port) {
        this.partitionKey = new ServicePartitionKey(0); 
        this.context = context;
        this.port = port;
    }

    // Called by openAsync when the class is instantiated 
    public void start() {
        try {
            logger.log(Level.INFO, "Starting Server");
            server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(this.port), 0);
        } catch (Exception ex) {
            logger.log(Level.SEVERE, null, ex);
            throw new RuntimeException(ex);
        }
        
        // Responsible for rendering the HTML layout described in the previous step
        server.createContext("/", new HttpHandler() {
            @Override
            public void handle(HttpExchange t) {
                try {
                    File file = new File(ROOT + FILE_NAME).getCanonicalFile();
    
                    if (!file.isFile()) {
                      // Object does not exist or is not a file: reject with 404 error.
                      t.sendResponseHeaders(STATUS_NOT_FOUND, RESPONSE_NOT_FOUND.length());
                      OutputStream os = t.getResponseBody();
                      os.write(RESPONSE_NOT_FOUND.getBytes());
                      os.close();
                    } else {    
                      Headers h = t.getResponseHeaders();
                      h.set(HEADER_CONTENT_TYPE, MIME);
                      t.sendResponseHeaders(STATUS_OK, 0);              
    
                      OutputStream os = t.getResponseBody();
                      FileInputStream fs = new FileInputStream(file);
                      final byte[] buffer = new byte[0x10000];
                      int count = 0;
                      while ((count = fs.read(buffer)) >= 0) {
                        os.write(buffer,0,count);
                      }
                      
                      fs.close();
                      os.close();
                    }  
                } catch (Exception e) {
                    logger.log(Level.WARNING, null, e);
                }
            }
        });

        /*
        [Replace this entire comment block in the 'Connect the services' section]
        */
        
        server.setExecutor(null);
        server.start();
    }
    
    //Helper method to parse raw HTTP requests
    private Map<String, String> queryToMap(String query){
        Map<String, String> result = new HashMap<String, String>();
        for (String param : query.split("&")) {
            String pair[] = param.split("=");
            if (pair.length>1) {
                result.put(pair[0], pair[1]);
            }else{
                result.put(pair[0], "");
            }
        }
        return result;
    }

    //Called closeAsync when the service is shut down
    private void stop() {
        if (null != server)
            server.stop(0);
    }

    //Called by the Service Fabric runtime when this service is created on a node
    @Override
    public CompletableFuture<String> openAsync(CancellationToken cancellationToken) {
        this.start();
                    logger.log(Level.INFO, "Opened Server");
        String publishUri = String.format("http://%s:%d/", this.context.getNodeContext().getIpAddressOrFQDN(), port);
        return CompletableFuture.completedFuture(publishUri);
    }

    //Called by the Service Fabric runtime when the service is shut down
    @Override
    public CompletableFuture<?> closeAsync(CancellationToken cancellationToken) {
        this.stop();
        return CompletableFuture.completedFuture(true);
    }

    //Called by the Service Fabric runtime to forcibly shut this listener down
    @Override
    public void abort() {
        this.stop();
    }
}
```

### <a name="configure-the-listening-port"></a>Configurare la porta di ascolto
Quando viene creato il servizio front-end VotingWebService, Service Fabric seleziona una porta su cui il servizio resterà in ascolto.  Poiché il servizio VotingWebService agisce come front-end per l'applicazione e accetta traffico esterno, è opportuno associare il servizio a una porta fissa e conosciuta. In Package Explorer (Esplora pacchetti) aprire *VotingWebService/VotingWebServicePkg/ServiceManifest.xml*.  Trovare la risorsa **Endpoint** nella sezione **Resources** (Risorse) e modificare il valore di **Port** (Porta) in 8080 o in un'altra porta. Per distribuire ed eseguire l'applicazione in locale, la porta dell'applicazione in ascolto deve essere aperta e disponibile nel computer. Incollare il frammento di codice seguente sotto il tag **ServiceManifest**.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
        <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
    </Endpoints>
  </Resources>
```  

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Aggiungere un servizio back-end con stato a un'applicazione
Ora che la struttura del servizio API Web Java è stata completata, completare il servizio back-end con stato.

Service Fabric consente di archiviare in modo coerente e affidabile i dati all'interno del servizio usando raccolte Reliable Collections. Reliable Collections è un set di classi di raccolte con elevati livelli di disponibilità e affidabilità. L'uso di queste classi è noto a chiunque abbia usato le raccolte di Java.

1. In Package Explorer (Esplora pacchetti) fare clic con il pulsante destro del mouse su **Voting** nel progetto dell'applicazione e scegliere **Service Fabric > Add Service Fabric Service** (Aggiungi servizio di Service Fabric).
   
2. Nella finestra di dialogo **Add Service** (Aggiungi servizio) scegliere **Stateful Service** (Servizio con stato), assegnare il nome **VotingData** al servizio e fare clic su **Add Service** (Aggiungi servizio). 

    ![Aggiunta di un nuovo servizio a un'applicazione esistente](./media/service-fabric-tutorial-create-java-app/addstatefuljava.png)

    Una volta creato il progetto di servizio, l'applicazione includerà due servizi. Man mano che si compila l'applicazione, è possibile aggiungere altri servizi nello stesso modo. Per ogni servizio, sarà possibile eseguire in modo indipendente il controllo della versione e l'aggiornamento.

3. Eclipse crea un progetto di servizio e lo visualizza in Package Explorer (Esplora pacchetti).

    ![Controllers\HomeController.cs](./media/service-fabric-tutorial-create-java-app/packageexplorercompletejava.png)

### <a name="add-the-votingdataservicejava-file"></a>Aggiungere il file VotingDataService.java

Il file *VotingDataService.java* contiene i metodi con logica per recuperare, aggiungere e rimuovere voti da Reliable Collections. Aggiungere i metodi seguenti alla classe **VotingDataService** nel file *VotingDataService/src/statefulservice/VotingDataService.java* creato.

```java
package statefulservice;

import java.util.HashMap;
import java.util.ArrayList;
import java.util.List; 
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.atomic.AtomicInteger;

import microsoft.servicefabric.services.communication.runtime.ServiceReplicaListener;

import microsoft.servicefabric.services.runtime.StatefulService;

import microsoft.servicefabric.services.remoting.fabrictransport.runtime.FabricTransportServiceRemotingListener;

import microsoft.servicefabric.data.ReliableStateManager;
import microsoft.servicefabric.data.Transaction;
import microsoft.servicefabric.data.collections.ReliableHashMap;
import microsoft.servicefabric.data.utilities.AsyncEnumeration;
import microsoft.servicefabric.data.utilities.KeyValuePair;

import system.fabric.StatefulServiceContext; 

import rpcmethods.VotingRPC; 

class VotingDataService extends StatefulService implements VotingRPC {
    private static final String MAP_NAME = "votesMap";
    private ReliableStateManager stateManager;
    
    protected VotingDataService (StatefulServiceContext statefulServiceContext) {
        super (statefulServiceContext);
    }

    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        this.stateManager = this.getReliableStateManager();
        ArrayList<ServiceReplicaListener> listeners = new ArrayList<>();
        
        listeners.add(new ServiceReplicaListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        
        return listeners;
    }
    
    // Method that will be invoked via RPC from the front end to retrieve the complete set of votes in the map
    public CompletableFuture<HashMap<String,String>> getList() {
        HashMap<String, String> tempMap = new HashMap<String, String>();

        try {                    

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();
                        
            Transaction tx = stateManager.createTransaction();
            AsyncEnumeration<KeyValuePair<String, String>> kv = votesMap.keyValuesAsync(tx).get();
            while (kv.hasMoreElementsAsync().get()) {
                KeyValuePair<String, String> k = kv.nextElementAsync().get();
                tempMap.put(k.getKey(), k.getValue()); 
            }
            
            tx.close();                    
            

        } catch (Exception e) {
            e.printStackTrace();
        }  
        
        return CompletableFuture.completedFuture(tempMap);
    }
    
    // Method that will be invoked via RPC from the front end to add an item to the votes list or to increase the
    // vote count for a particular item
    public CompletableFuture<Integer> addItem(String itemToAdd) {
        AtomicInteger status = new AtomicInteger(-1); 

        try {
            
            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();                    
            
            Transaction tx = stateManager.createTransaction();
            votesMap.computeAsync(tx, itemToAdd, (k, v) -> {
                if (v == null) {
                    return "1";
                }
                else {
                    int numVotes = Integer.parseInt(v);
                    numVotes = numVotes + 1;                            
                    return Integer.toString(numVotes);
                }
            }).get(); 
            
            tx.commitAsync().get();
            tx.close(); 

            status.set(1);                            
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }
    
    // Method that will be invoked via RPC from the front end to remove an item
    public CompletableFuture<Integer> removeItem(String itemToRemove) {
        AtomicInteger status = new AtomicInteger(-1); 
        try {
            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();
            
            Transaction tx = stateManager.createTransaction();
            votesMap.removeAsync(tx, itemToRemove).get();
            tx.commitAsync().get();
            tx.close();                    
            
            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }
        
        return CompletableFuture.completedFuture(new Integer(status.get()));
    }
    
}
```

## <a name="create-the-communication-interface-to-your-application"></a>Creare l'interfaccia di comunicazione con l'applicazione 
La struttura del servizio front-end senza stato e del servizio back-end è stata così creata. Il passaggio successivo consiste nel connettere i due servizi. I servizi back-end e front-end usano un'interfaccia denominata VotingRPC che definisce le operazioni dell'applicazione Voting. Questa interfaccia è implementata da entrambi i servizi, front-end e back-end, per abilitare le chiamate di procedura remota (RPC) tra i due servizi. Poiché Eclipse non supporta l'aggiunta di sottoprogetti Gradle, è necessario aggiungere manualmente il pacchetto che contiene questa interfaccia. 

1. Fare clic con il pulsante destro del mouse sul progetto **Voting** in Package Explorer (Esplora pacchetti) e scegliere **New (Nuovo) -> Other (Altro)**.

2. Nella procedura guidata fare clic su **General (Generale) -> Folder (Cartella)** e assegnare il nome **VotingRPC/src/rpcmethods** alla cartella 

    ![Creare il pacchetto VotingRPC](./media/service-fabric-tutorial-create-java-app/createvotingrpcpackage.png)

3. In *Voting/VotingRPC/src/rpcmethods* creare un file denominato *VotingRPC.java* e incollare il contenuto seguente nel file **VotingRPC.java**. 

    ```java
    package rpcmethods; 
    
    import java.util.ArrayList;
    import java.util.concurrent.CompletableFuture;
    import java.util.List;
    import java.util.HashMap;
    
    import microsoft.servicefabric.services.remoting.Service;
    
    public interface VotingRPC extends Service {
        CompletableFuture<HashMap<String, String>> getList();
    
        CompletableFuture<Integer> addItem(String itemToAdd);
    
        CompletableFuture<Integer> removeItem(String itemToRemove);
    }
    ``` 

4. Creare un file denominato *build.gradle* nella directory *Voting/VotingRPC* e incollarvi il contenuto seguente. Questo file Gradle viene usato per compilare e creare il file JAR importato da altri servizi. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/out"
        delete "${projectDir}/VotingRPC.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
        
        manifest {
                attributes(
                'Main-Class': 'rpcmethods.VotingRPC')
            baseName "VotingRPC"
            destinationDir = file('./')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA' 
    }
    
    defaultTasks 'clean', 'jar'
    ```

5. Nel file *Voting/settings.gradle* aggiungere una riga per includere il progetto appena creato nella compilazione. 

    ```gradle 
    include ':VotingRPC'
    ```

6. Nel file *Voting/VotingWebService/src/statelessservice/HttpCommunicationListener.java* sostituire il blocco di commento con il contenuto seguente.  

    ```java
    server.createContext("/getStatelessList", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {                    
                t.sendResponseHeaders(STATUS_OK,0);
                OutputStream os = t.getResponseBody();
                
                HashMap<String,String> list = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").getList().get();
                String json = new Gson().toJson(list);
                os.write(json.getBytes(ENCODING));                   
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    
    server.createContext("/removeItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                OutputStream os = t.getResponseBody();
                URI r = t.getRequestURI();     
    
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToRemove = params.get("item");                    
                
                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").removeItem(itemToRemove).get();
                
                if (num != 1) 
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
    
        }
    });
    
    server.createContext("/addItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                URI r = t.getRequestURI();
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToAdd = params.get("item");
                
                OutputStream os = t.getResponseBody();
                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").addItem(itemToAdd).get();
                if (num != 1) 
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    ```
7. Aggiungere l'istruzione di importazione appropriata nella parte superiore del file *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java*. 

    ```java
    import rpcmethods.VotingRPC; 
    ```

A questo punto, la funzionalità per le interfacce RPC, front-end e back-end è completa. La fase successiva consiste nel configurare gli script di Gradle in modo appropriato prima della distribuzione in un cluster di Service Fabric. 

## <a name="walk-through-the-voting-sample-application"></a>Descrizione dettagliata dell'applicazione di voto di esempio
L'applicazione di voto è costituita da due servizi:
- Il servizio front-end Web (VotingWeb): servizio front-end Web Java che gestisce la pagina Web ed espone le API per la comunicazione con il servizio back-end.
- Servizio back-end (VotingDataService): servizio Web Java che definisce i metodi richiamati tramite chiamate RPC per rendere persistenti i voti.

![Diagramma dell'applicazione](./media/service-fabric-tutorial-create-java-app/walkthroughjavavoting.png)

Quando si esegue un'azione nell'applicazione (aggiunta di un elemento, voto, rimozione di un elemento) si verificano gli eventi seguenti:
1. JavaScript invia la richiesta appropriata all'API Web nel servizio front-end Web come richiesta HTTP.

2. Il servizio front-end Web usa la funzionalità integrata di comunicazione remota con i servizi di Service Fabric per individuare e inoltrare la richiesta al servizio back-end. 

3. Il servizio back-end definisce i metodi che aggiornano il risultato in un dizionario affidabile. Il contenuto di questo dizionario affidabile viene replicato in più nodi nel cluster e reso persistente nel disco. Tutti i dati dell'applicazione sono archiviati nel cluster. 

## <a name="configure-gradle-scripts"></a>Configurare gli script Gradle 

In questa sezione vengono configurati gli script Gradle per il progetto. 

1. Sostituire il contenuto del file *Voting/build.gradle* con il seguente.

    ```gradle 
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    subprojects {
        apply plugin: 'java'
    } 
        
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

2. Sostituire il contenuto del file *Voting/VotingWeb/build.gradle*.

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/VotingWeb.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
        
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingWebPkg/Code/lib")
            include('lib*.so')
        }
    }
    
    compileJava.dependsOn(explodeDeps)
    
    jar {
        from configurations.compile.collect {(it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
        
        manifest {
            attributes(
                'Main-Class': 'statelessservice.VotingWebServiceHost')
            baseName "VotingWeb"
            destinationDir = file('../VotingApplication/VotingWebPkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA' 
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ``` 

3. Sostituire il contenuto del file *Voting/VotingData/build.gradle*. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/VotingDataService.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
        
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    compileJava.dependsOn(explodeDeps)
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingDataServicePkg/Code/lib")
            include('lib*.so')
        }
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
        
        manifest {
            attributes('Main-Class': 'statefulservice.VotingDataServiceHost')
    
            baseName "VotingDataService"
            destinationDir = file('../VotingApplication/VotingDataServicePkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA' 
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

## <a name="deploy-application-to-local-cluster"></a>Distribuire l'applicazione nel cluster locale
A questo punto, l'applicazione è pronta per essere distribuita in un cluster di Service Fabric locale.

1. Fare clic con il pulsante destro del mouse sul progetto **Voting** in Package Explorer (Esplora pacchetti) e scegliere **Service Fabric -> Build Application** (Compila applicazione).

2. Eseguire il cluster locale di Service Fabric. Questo passaggio dipende dall'ambiente di sviluppo (Mac o Linux).

    Se si usa un computer Mac, eseguire il cluster locale con il comando seguente. Sostituire il comando passato nel parametro **-v** con il percorso della propria area di lavoro.

    ```bash
    docker run -itd -p 19080:19080 -p 8080:8080 -p --name sfonebox servicefabricoss/service-fabric-onebox
    ``` 

    Se si usa un computer Linux, avviare il cluster locale con il comando seguente: 

    ```bash 
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

4. In Package Explorer (Esplora pacchetti) per Eclipse fare clic con il pulsante destro del mouse sul progetto **Voting** e scegliere **Service Fabric -> Publish Application** (Pubblica applicazione) 
5. Nella finestra **Publish Application** (Pubblica applicazione) selezionare **Local.json** dall'elenco a discesa e fare clic su **Publish** (Pubblica).
6. Passare al Web browser e accedere a **http://localhost:8080/** per visualizzare l'applicazione in esecuzione nel cluster locale di Service Fabric. 

## <a name="next-steps"></a>Passaggi successivi
In questa parte dell'esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un servizio Java come servizio Reliable con stato
> * Creare un servizio Java come servizio Web senza stato
> * Aggiungere un'interfaccia Java per gestire le chiamate RPC tra i servizi
> * Configurare gli script Gradle
> * Compilare e distribuire l'applicazione nel cluster di Service Fabric locale

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Eseguire il debug e indirizzare i log delle applicazioni nel cluster locale](service-fabric-tutorial-debug-log-local-cluster.md)
