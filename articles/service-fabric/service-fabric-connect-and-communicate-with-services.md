<properties
   pageTitle="Connettersi e comunicare con i servizi in Azure Service Fabric | Microsoft Azure"
   description="Informazioni su come risolvere, connettersi e comunicare con i servizi in Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="msfussell"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/05/2016"
   ms.author="vturecek"/>

# Connettersi e comunicare con i servizi in Service Fabric
In Service Fabric un servizio viene eseguito in una posizione nel cluster di Service Fabric, in genere distribuito su più macchine virtuali. Può essere spostato da una posizione all'altra, dal proprietario del servizio o automaticamente da Service Fabric. I servizi non sono statisticamente associati a un computer o a un indirizzo specifico.
 
Un'applicazione di Service Fabric è in genere costituita da molti servizi diversi, ognuno dei quali esegue un'attività specializzata. Questi servizi possono comunicare tra loro per formare una funzione completa, ad esempio il rendering di diverse parti di un'applicazione Web. Sono inoltre presenti applicazioni client che si connettono ai servizi e comunicano con essi. Questo documento illustra come configurare la comunicazione con e tra i servizi in Service Fabric.

## Usare un protocollo personalizzato
Service Fabric semplifica la gestione del ciclo di vita dei servizi ma non prende alcuna decisione sulle operazioni che devono essere eseguite dai servizi, incluse le comunicazioni. Quando il servizio viene aperto da Service Fabric, può configurare un endpoint per le richieste in ingresso, usando qualsiasi protocollo o stack di comunicazione. Il servizio rimarrà in ascolto su un indirizzo **IP:porta** normale usando qualsiasi schema di indirizzamento, ad esempio un URI. È possibile che più istanze o repliche di un servizio condividano un processo host. In questo caso, dovranno usare porte diverse o un meccanismo di condivisione di porte, ad esempio il driver del kernel http.sys in Windows. In entrambi i casi, ogni istanza o replica del servizio in un processo host deve essere indirizzabile in modo univoco.

![Endpoint del servizio][1]

## Individuazione e risoluzione del servizio
In un sistema distribuito è possibile che i servizi si spostino da un computer a un altro nel tempo per vari motivi, incluso il bilanciamento delle risorse, aggiornamenti, failover o aumento del numero di istanze. Gli indirizzi dell'endpoint di servizio subiscono quindi modifiche quando il servizio si sposta in nodi con indirizzi IP diversi ed è possibile che si aprano su porte diverse se il servizio usa una porta selezionata in modo dinamico.

![Distribuzione dei servizi][7]

Service Fabric offre un servizio di individuazione e risoluzione denominato servizio Naming. Il servizio Naming gestisce una tabella che esegue il mapping delle istanze del servizio denominato agli indirizzi dell'endpoint su cui rimangono in ascolto. Tutte le istanze del servizio denominato in Service Fabric hanno nomi univoci rappresentati come URI, ad esempio `"fabric:/MyApplication/MyService"`. Il nome del servizio non cambia per tutta la durata del servizio. Solo gli indirizzi dell'endpoint possono essere modificati quando i servizi si spostano. analogamente ai siti Web che hanno URL costanti, ma il cui indirizzo IP può cambiare. Analogamente a DNS sul Web, che risolve URL di siti Web in indirizzi IP, Service Fabric ha un registrar che esegue il mapping dei nomi di servizio ai rispettivi indirizzi di endpoint.

![Endpoint del servizio][2]

La risoluzione e la connessione ai servizi prevedono l'esecuzione in ciclo dei passaggi seguenti:

* **Risoluzione**: ottenere dal servizio Naming l'endpoint pubblicato da un servizio.

* **Connessione**: connettersi al servizio sul protocollo usato nell'endpoint.

* **Ripetizione dei tentativi**: è possibile che un tentativo di connessione abbia esito negativo per svariati motivi, ad esempio se il servizio si è spostato dopo l'ultima risoluzione dell'indirizzo dell'endpoint. In questo caso, è necessario provare a ripetere i passaggi precedenti di risoluzione e connessione e il ciclo viene ripetuto finché la connessione non riesce.

## Connessioni da client esterni

I servizi che si connettono tra loro in un cluster possono in genere accedere direttamente agli endpoint di altri servizi, perché i nodi in un cluster si trovano in genere nella stessa rete locale. In alcuni ambienti, tuttavia, è possibile che un cluster si trovi dietro un servizio di bilanciamento del carico che indirizza il traffico esterno in ingresso attraverso un set limitato di porte. In questi casi, i servizi possono comunicare comunque tra di loro e risolvere gli indirizzi usando il servizio Naming, ma sono necessari passaggi aggiuntivi per consentire ai client esterni di connettersi ai servizi.

## Service Fabric in Azure

Un cluster di Service Fabric in Azure viene posizionato dietro un servizio di bilanciamento del carico di Azure. Tutto il traffico esterno verso il cluster deve passare attraverso il servizio di bilanciamento del carico. Il servizio di bilanciamento del carico inoltrerà automaticamente il traffico in ingresso su una porta specifica verso un *nodo* casuale che ha la stessa porta aperta. Il servizio di bilanciamento del carico di Azure riconosce solo le porte aperte sui *nodi*, non le porte aperte dai singoli *servizi*.

![Servizio di bilanciamento del carico di Azure e topologia di Service Fabric][3]

Ad esempio, per accettare il traffico esterno sulla porta **80**, è necessario configurare gli elementi seguenti:

1. Scrivere un servizio che rimane in ascolto sulla porta 80. Configurare la porta 80 nel file ServiceManifest.xml del servizio e aprire un listener nel servizio, ad esempio un server Web self-hosted.
 
    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...
            
            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint = 
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string uriPublished = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

                return Task.FromResult(this.publishUri);
            }
            
            ...
        }
        
        class WebService : StatelessService
        {
            ...
            
            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] {new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }
            
            ...
        }
    ```
  
2. Creare un cluster di Service Fabric in Azure e specificare la porta **80** come porta di endpoint personalizzata per il tipo di nodo che ospiterà il servizio. Se sono presenti più tipi di nodo, è possibile configurare un *vincolo di posizionamento* sul servizio per assicurare che venga eseguito solo sul tipo di nodo con la porta di endpoint personalizzata aperta.

    ![Apertura di una porta su un tipo di nodo][4]

3. Dopo la creazione del cluster, configurare il servizio di bilanciamento del carico di Azure nel gruppo di risorse del cluster per inoltrare il traffico sulla porta 80. Quando si crea un cluster tramite il portale di Azure, il cluster viene configurato automaticamente per ogni porta di endpoint personalizzata configurata.

    ![Inoltro di traffico nel servizio di bilanciamento del carico di Azure][5]

4. Il servizio di bilanciamento del carico di Azure usa un probe per determinare se inviare o meno il traffico a un nodo specifico. Il probe controlla periodicamente un endpoint su ogni nodo per determinare se il nodo risponde o meno. Se il probe non riesce a ricevere una risposta dopo un numero configurato di volte, il servizio di bilanciamento del carico smette di inviare traffico a quel nodo. Quando si crea un cluster tramite il portale di Azure, viene configurato automaticamente un probe per ogni porta di endpoint personalizzata configurata.

    ![Inoltro di traffico nel servizio di bilanciamento del carico di Azure][8]

È importante ricordare che il servizio di bilanciamento del carico di Azure e il probe riconoscono solo i *nodi*, non i *servizi* in esecuzione sui nodi. Il servizio di bilanciamento del carico di Azure invierà sempre il traffico ai nodi che rispondono al probe, quindi occorre assicurarsi che i servizi siano disponibili sui nodi che sono in grado di rispondere al probe.

## Opzioni predefinite per l'API di comunicazione
Il framework Reliable Services include alcune opzioni di comunicazione predefinite. la cui scelta dipende dal modello di programmazione adottato, dal framework di comunicazione e dal linguaggio di programmazione usato per scrivere i servizi.

* **Nessun protocollo specifico:** se non si ha una preferenza particolare per il framework di comunicazione, ma si vuole essere immediatamente operativi, l'opzione ideale è costituita dalle [comunicazioni remote del servizio](service-fabric-reliable-services-communication-remoting.md), che consentono chiamate a procedure remote fortemente tipizzate per Reliable Services e Reliable Actors. Questo è il modo più semplice e veloce per iniziare a comunicare con i servizi. Le comunicazioni remote del servizio gestiscono la risoluzione degli indirizzi del servizio, la connessione, la ripetizione dei tentativi e la gestione degli errori. Si noti che le comunicazioni remote del servizio sono disponibili solo per applicazioni C#.

* **HTTP**: per comunicazioni indipendenti dal linguaggio, HTTP costituisce la scelta standard di settore, con strumenti e server HTTP disponibili in molti linguaggi diversi, tutti supportati da Service Fabric. I servizi possono usare qualsiasi stack HTTP disponibile, inclusa l'[API Web ASP.NET](service-fabric-reliable-services-communication-webapi.md). I client scritti in C# possono sfruttare le [classi `ICommunicationClient` e `ServicePartitionClient`](service-fabric-reliable-services-communication.md) per la risoluzione del servizio, le connessioni HTTP e i cicli di ripetizione dei tentativi.

* **WCF**: se il codice esistente usa WCF come framework di comunicazione, è possibile scegliere `WcfCommunicationListener` per il lato server e le classi `WcfCommunicationClient` e `ServicePartitionClient` per il client. Per altre informazioni, vedere l'articolo [Stack di comunicazione basato su WCF per Reliable Services](service-fabric-reliable-services-communication-wcf.md).

## Uso di protocolli personalizzati e altri framework di comunicazione
I servizi possono usare qualsiasi protocollo o framework per le comunicazioni, ad esempio un protocollo binario personalizzato su socket TCP o eventi di streaming tramite l'[Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) o l'[Hub IoT di Azure](https://azure.microsoft.com/services/iot-hub/). Service Fabric fornisce API di comunicazione a cui è possibile collegare lo stack di comunicazione, mentre tutte le operazioni di individuazione e connessione vengono eseguite in modo indipendente dall'utente. Per altre informazioni, vedere l'articolo [Modello di comunicazione Reliable Service](service-fabric-reliable-services-communication.md).

## Passaggi successivi

Per altre informazioni sui concetti e sulle API disponibili, vedere il [modello di comunicazione di Reliable Services](service-fabric-reliable-services-communication.md), quindi iniziare subito a usare le [del servizio](service-fabric-reliable-services-communication-remoting.md) oppure ottenere informazioni approfondite su come scrivere un listener di comunicazione usando l'[API Web con self-hosting OWIN](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png

<!---HONumber=AcomDC_0713_2016-->