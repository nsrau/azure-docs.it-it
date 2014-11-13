<properties title="Analisi dei dati dei sensori con Storm e HDInsight" pageTitle="Analisi dei dati dei sensori con Apache Storm e Microsoft Azure HDInsight (Hadoop)" description="Informazioni su come usare Apache Storm per elaborare i dati dei sensori in tempo reale con HDInsight (Hadoop)" metaKeywords="Azure hdinsight storm, Azure hdinsight realtime, azure hadoop storm, azure hadoop realtime, azure hadoop real-time, azure hdinsight real-time" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="larryfr" />

# Analisi dei dati dei sensori con Storm e HBase in HDInsight (Hadoop)

Informazioni su come compilare una soluzione che usa un cluster Storm in HDInsight per elaborare i dati dei sensori dall'hub eventi di Azure. Durante l'elaborazione, la topologia Storm archivierà i dati in ingresso in un cluster HBase. La topologia userà anche SignalR per fornire informazioni quasi in tempo reale tramite un dashboard basato sul Web ospitato in Siti Web di Azure.

> [AZURE.NOTE] Una versione completa di questo progetto è disponibile all'indirizzo <https://github.com/Blackmist/hdinsight-eventhub-example>.

## Prerequisiti

-   Una sottoscrizione di Azure

-   Visual Studio con [Microsoft Azure SDK per .NET][Microsoft Azure SDK per .NET]

-   [Java e JDK][Java e JDK]

-   [Maven][Maven]

-   [Git][Git]

> [AZURE.NOTE] Java, JDK, Maven e Git sono anche disponibili nel sito dello strumento di gestione dei pacchetti [Chocolatey NuGet][Chocolatey NuGet].

## Creare il dashboard

Nel dashboard vengono visualizzate le informazioni sul sensore quasi in tempo reale. In questo caso, il dashboard è un'applicazione ASP.NET ospitata in un sito Web di Azure. Lo scopo principale dell'applicazione è fungere da hub di [SignalR][SignalR] che riceve informazioni dalla topologia Storm man mano che questa elabora i messaggi.

Il sito Web contiene anche un file index.html statico, che si connette anch'esso a SignalR e usa D3.js per creare un grafico dei dati trasmessi dalla topologia Storm.

> [WACOM.NOTE] È possibile usare WebSocket non elaborati al posto di SignalR, ma i WebSocket non forniscono un meccanismo di scalabilità incorporato se occorre scalare orizzontalmente il sito Web. SignalR può essere scalato con il bus di servizio di Azure (<http://www.asp.net/signalr/overview/performance/scaleout-with-windows-azure-service-bus>).
>
> Per un esempio di uso della topologia Storm per comunicare con un sito Web Python tramite WebSocket non elaborati, vedere il progetto [StormTweetsSentimentD3Viz][StormTweetsSentimentD3Viz].

1.  In Visual Studio creare una nuova applicazione C# tramite il modello di progetto **Applicazione Web ASP.NET**. Denominare la nuova applicazione **Dashboard**.

2.  Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il modello di applicazione **Vuoto**. Nella sezione **Azure** selezionare **Ospita nel cloud** e **Sito Web**. Infine, fare clic su **OK**.

    > [AZURE.NOTE] Se richiesto, accedere alla sottoscrizione di Azure.

3.  Nella finestra di dialogo **Configura sito di Microsoft Azure** specificare **Nome sito** e **Area** relativi al sito Web, quindi fare clic su **OK**. Verrà creato un sito Web di Azure che ospiterà il dashboard.

4.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi selezionare **Aggiungi | SignalR Hub Class (v2)**. Denominare la classe **DashHub.cs** e aggiungerla al progetto. La classe conterrà l'hub SignalR usato per trasmettere i dati tra HDInsight e la pagina Web del dashboard.

    > [AZURE.NOTE] Se di usa Visual Studio 2012, il modello **SignalR Hub Class (v2)** non è disponibile. In alternativa, è possibile aggiungere una **classe** normale denominata DashHub. È anche necessario installare manualmente il pacchetto SignalR aprendo **Strumenti | Library Package Manager | Package Manager Console** ed eseguendo il comando seguente:
    >
    > `install-package Microsoft.AspNet.SignalR`

5.  Sostituire il codice in **DashHub.cs** con il seguente.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using Microsoft.AspNet.SignalR;

        namespace dashboard
        {
            public class DashHub : Hub
            {
                public void Send(string message)
                {
                    // Call the broadcastMessage method to update clients.
                    Clients.All.broadcastMessage(message);
                }
            }
        }

6.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Aggiungi | OWIN Startup Class**. Denominare la nuova classe **Startup.cs**.

    > [AZURE.NOTE] Se si usa Visual Studio 2012, il modello **OWIN Startup Class** non è disponibile. È invece possibile creare una **classe** denominata Startup.

7.  Sostituire il contenuto di **Startup.cs** con il codice seguente.

        using System;
        using System.Threading.Tasks;
        using Microsoft.Owin;
        using Owin;

        [assembly: OwinStartup(typeof(dashboard.Startup))]

        namespace dashboard
        {
            public class Startup
            {
                public void Configuration(IAppBuilder app)
                {
                    // For more information on how to configure your application, visit http://go.microsoft.com/fwlink/?LinkID=316888
                    app.MapSignalR();
                }
            }
        }

8.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e quindi scegliere **Aggiungi | Pagina HTML**. Denominare la nuova pagina **index.html**. Questa pagina conterrà il dashboard in tempo reale per il progetto. Riceverà informazioni da DashHub e visualizzerà un grafico usando D3.js.

9.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **index.html** e selezionare **Imposta come pagina iniziale**.

10. Sostituire il codice nel file **index.html** con il seguente.

        <!DOCTYPE html>
        <html xmlns="http://www.w3.org/1999/xhtml">
        <head>
            <title>Dashboard</title>
            <style>

                .x.axis line {
                    shape-rendering: auto;
                }

                .line {
                    fill: none;
                    stroke-width: 1.5px;
                }

            </style>
            <!--Script references. -->
            <!--Reference the jQuery library. -->
            <script src="Scripts/jquery-1.10.2.min.js"></script>
            <!--Reference the SignalR library. -->
            <script src="Scripts/jquery.signalR-2.0.2.min.js"></script>
            <!--Reference the autogenerated SignalR hub script. -->
            <script src="signalr/hubs"></script>
            <!--Reference d3.js.-->
            <script src="http://d3js.org/d3.v3.min.js"></script>
        </head>
        <body>
            <script>
                $(function () {
                    //Huge thanks to Mike Bostok for his Path Transitions article - http://bost.ocks.org/mike/path/
                    var n = 243,                                 //number of x coordinates in the graph
                    duration = 750,                          //duration for transitions
                    deviceValue=[0,0,0,0,0,0,0,0,0,0],       //temp holding for each device value
                    now = new Date(Date.now() - duration),   //Now
                    //fill an array of arrays with dummy data to start the chart
                    //each item in the top-level array is a line
                    //each item in the line arrays represents the X coordinate across a graph
                    //The 'value' within each line array represents the Y coordinate for that point
                    data = [                                 
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; })
                    ];

                    //Color scale for 10 items
                    var color = d3.scale.category10();
                    //The domain for color (the device IDs)
                    var devices = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9"];
                    //This will auto-generate colors for this range of IDs
                    color.domain(devices);

                    //set margins and figure out width/height
                    var margin = {top: 6, right: 0, bottom: 20, left: 40},
                        width = 960 - margin.right,
                        height = 240 - margin.top - margin.bottom;

                    //the time scale for the X axis
                    var x = d3.time.scale()
                        .domain([now - (n - 2) * duration, now - duration])
                        .range([0, width]);

                    //the numerical scale for the Y axis
                    var y = d3.scale.linear()
                        .domain([100, 0])
                        .range([0, height]);

                    //The line, which is really just a
                    //couple functions that we can pass data to
                    //in order to get back x/y coords.
                    var line = d3.svg.line()
                        .interpolate("basis")
                        .x(function (d, i) { return x(now - (n - 1 - i) * duration); })
                        .y(function (d, i) { return y(d.value); });

                    //Find the HTML body element and add a child SVG element
                    var svg = d3.select("body").append("svg")
                        .attr("width", width + margin.left + margin.right)
                        .attr("height", height + margin.top + margin.bottom)
                      .append("g")
                        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

                    //Define a clipping path, because we need to clip
                    //the graph to render only the bits we want to see
                    //as it moves
                    svg.append("defs").append("clipPath")
                        .attr("id", "clip")
                      .append("rect")
                        .attr("width", width)
                        .attr("height", height);

                    //Append the x axis
                    var axis = svg.append("g")
                        .attr("class", "x axis")
                        .attr("transform", "translate(0," + height + ")")
                        .call(x.axis = d3.svg.axis().scale(x).orient("bottom"));

                    //append the y axis
                    var yaxis = svg.append("g")
                        .attr("class", "y axis")
                        .call(y.axis = d3.svg.axis().scale(y).orient("left").ticks(5));

                    //append the clipping path
                    var linegroup = svg.append("g")
                      .attr("clip-path", "url(#clip)");

                    //magic. Select all paths with a class of .line
                    //if they don't exist, make them.
                    //use the points in the line object to define
                    //the paths
                    //set the color to the cooresponding auto-generated coclor
                    var path = linegroup.selectAll(".line")
                      .data(data)
                      .enter().append("path")
                      .attr("class", "line")
                      .attr("d", line)
                      .style("stroke", function (d, i) { return color(i); });

                    //We need to transition the graph after all
                    //lines have been updated. There's no
                    //built-in for this, so this function
                    //does reference counting on end events
                    //for each line, then applies whatever
                    //callback when all are finished.
                    function endall(transition, callback) {
                        var n = 0;
                        transition
                            .each(function () { ++n; })
                            .each("end", function () { if (!--n) callback.apply(this, arguments); });
                    }

                    //wire up the SignalR client and listen for messages
                    var chat = $.connection.dashHub;
                    chat.client.broadcastMessage = function (message) {
                        //parse the JSON data
                        var incomingData = JSON.parse(message);
                        //stuff it in the global array slot for the device ID
                        deviceValue[incomingData.device] = incomingData.temperature;

                    };
                    //start listening
                    $.connection.hub.start();
                    //tick for D3 graphics
                    tick();

                    function tick() {
                        // update the domains
                        now = new Date();
                        x.domain([now - (n - 2) * duration, now - duration]);

                        //push the (presumably) fresh data deviceValue array onto
                        //the arrays that define the lines.
                        for (i = 0; i < 10; i++) {
                            data[i].push({ value: deviceValue[i] });
                            //data[1].push({ value: maxValue });
                        }
                        //slide the x-axis left
                        axis.transition()
                            .duration(duration)
                            .ease("linear")
                            .call(x.axis);

                        //Update the paths based on the updated line data
                        //and slide left
                        path
                            .attr("d", line)
                            .attr("transform", null)
                        .transition()
                            .duration(duration)
                            .ease("linear")
                            .attr("transform", "translate(" + x(now - (n - 1) * duration) + ",0)")
                            .call(endall, tick);

                        // pop the old data point off the front
                        // of the arrays
                        for (var i = 0; i < data.length; i++) {
                            data[i].shift();
                        };
                    };
                 })()
                </script>
            </body>
        </html>

    > [AZURE.NOTE] Lo strumento di gestione dei pacchetti potrebbe installare una versione più recente degli script SignalR. Verificare che i riferimenti agli script riportati di seguito corrispondano alle versioni dei file di script del progetto (saranno diverse se SignalR è stato aggiunto tramite NuGet invece di aggiungere un hub).

11. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e quindi scegliere **Aggiungi | Pagina HTML**. Denominare la nuova pagina **test.html**. Questa pagina può essere usata per testare DashHub e il dashboard inviando e ricevendo messaggi.

12. Sostituire il codice nel file **test.html** con il seguente.

        <!DOCTYPE html>
        <html>
        <head>
            <title>Test</title>
            <style type="text/css">
                .container {
                    background-color: #99CCFF;
                    border: thick solid #808080;
                    padding: 20px;
                    margin: 20px;
                }
            </style>
        </head>
        <body>
            <div class="container">
                <input type="text" id="message" />
                <input type="button" id="sendmessage" value="Send" />
                <input type="hidden" id="displayname" />
                <ul id="discussion"></ul>
            </div>
            <!--Script references. -->
            <!--Reference the jQuery library. -->
            <script src="Scripts/jquery-1.10.2.min.js"></script>
            <!--Reference the SignalR library. -->
            <script src="Scripts/jquery.signalR-2.0.2.min.js"></script>
            <!--Reference the autogenerated SignalR hub script. -->
            <script src="signalr/hubs"></script>
            <!--Add script to update the page and send messages.-->
            <script type="text/javascript">
                $(function () {
                    // Declare a proxy to reference the hub.
                    var chat = $.connection.dashHub;
                    // Create a function that the hub can call to broadcast messages.
                    chat.client.broadcastMessage = function (message) {
                        // Html encode display the message.
                        var encodedMsg = $('<div />').text(message).html();
                        // Add the message to the page.
                        $('#discussion').append('<li>' + encodedMsg + '</li>');
                    };
                    // Set initial focus to message input box.
                    $('#message').focus();
                    // Start the connection.
                    $.connection.hub.start().done(function () {
                        $('#sendmessage').click(function () {
                            // Call the Send method on the hub.
                            chat.server.send($('#message').val());
                            // Clear text box and reset focus for next comment.
                            $('#message').val('').focus();
                        });
                    });
                });
            </script>
        </body>
        </html>

13. Scegliere **Salva tutto** per il progetto.

14. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **Dashboard** e scegliere **Pubblica**. Selezionare il sito Web creato per questo progetto e quindi fare clic su **Pubblica**.

15. Una volta pubblicato il sito, dovrebbe venire visualizzata una pagina Web con una sequenza temporale in movimento.

### Testare il dashboard

1.  Per verificare che SignalR funzioni e che nel dashboard vengano visualizzate le linee del grafico per i dati inviati a SignalR, aprire una nuova finestra del browser con la pagina **test.html** nel sito Web, ad esempio **http://mydashboard.azurewebsites.net/test.html**.

2.  Il dashboard richiede dati con formattazione JSON, con un valore **device id** e **temperature**, ad esempio **{"device":0, "temperature":80}**. Immettere alcuni valori di test nella pagina **test.html** usando ID di dispositivo da 0 a 9 mentre il dashboard è aperto in un'altra pagina. Si noti che le linee per ogni ID di dispositivo vengono tracciate con un colore diverso.

## Configurare l'hub eventi

L'hub eventi viene usato per ricevere i messaggi (eventi) dai sensori. Per creare un nuovo hub eventi, eseguire la procedura seguente.

1.  Nel [portale di Azure][portale di Azure] selezionare **NUOVO | Bus di servizio | Hub eventi | Creazione personalizzata**.

2.  Nella finestra di dialogo **Crea un nuovo hub eventi** immettere un nome in **Nome hub eventi**, selezionare l'area in cui creare l'hub in **Area geografica** e creare un nuovo spazio dei nomi o selezionarne uno esistente. Infine fare clic sulla freccia.

3.  Nella finestra di dialogo **Configura hub eventi** immettere i valori per **Conteggio partizioni** e **Conservazione messaggi**. Per questo esempio usare un numero di partizioni pari a 10 e un valore di conservazione dei messaggi pari a 1.

4.  Dopo la creazione dell'hub eventi, selezionare lo spazio dei nomi e quindi **Hub eventi**. Infine, selezionare l'hub eventi creato precedentemente.

5.  Selezionare **Configura**, quindi creare due nuovi criteri di accesso con le informazioni seguenti.

    | Nome    | Autorizzazioni |
    |---------|----------------|
    | devices | Invio          |
    | storm   | Attesa         |

    Dopo avere creato le autorizzazioni, selezionare l'icona **Salva** in fondo alla pagina. Verranno creati i criteri di accesso condivisi che verranno usati per inviare messaggi all'hub e per leggere i messaggi provenienti dall'hub.

6.  Dopo avere salvato i criteri, usare **Generatore di chiavi di accesso condivise** in fondo alla pagina per recuperare la chiave per i criteri **devices** e **storm**. Salvare queste informazioni perché verranno usate in seguito.

### Inviare messaggi all'hub eventi

Poiché non è disponibile un set di sensori standard e facile da usare per tutti, viene usata un'applicazione .NET per generare numeri casuali. L'applicazione .NET creata con la procedura che segue genererà gli eventi per 10 dispositivi ogni secondo, fino a quando non verrà arrestata premendo un tasto.

1.  In Visual Studio creare un nuovo progetto **Desktop di Windows** e selezionare il modello di progetto **Applicazione console**. Denominare il progetto **SendEvents** e quindi fare clic su **OK**.

2.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **SendEvents** e quindi scegliere **Gestisci pacchetti NuGet**.

3.  In **Gestisci pacchetti NuGet** cercare e installare i pacchetti seguenti.

    -   **Bus di servizio di Microsoft Azure**
    -   **JSON.Net**

    Dopo avere installato i pacchetti, fare clic su **Chiudi** per chiudere Gestione pacchetti.

4.  Sostituire il contenuto del file **Program.cs** con il codice seguente.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using Newtonsoft.Json;
        using Microsoft.ServiceBus;
        using System.Threading;

        namespace SendEvents
        {
            class Program
            {

                static int numberOfDevices = 10;
                static string eventHubName = "temperature";
                static string eventHubNamespace = "namespace";
                static string sharedAccessPolicyName = "devices";
                static string sharedAccessPolicyKey = "key for devices policy";

                static void Main(string[] args)
                {
                    var settings = new MessagingFactorySettings()
                    {
                        TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(sharedAccessPolicyName, sharedAccessPolicyKey),
                        TransportType = TransportType.Amqp
                    };
                    var factory = MessagingFactory.Create(
                         ServiceBusEnvironment.CreateServiceUri("sb", eventHubNamespace, ""), settings);

                    EventHubClient client = factory.CreateEventHubClient(eventHubName);

                    try
                    {

                        List<Task> tasks = new List<Task>();
                        // Send messages to Event Hub
                        Console.WriteLine("Sending messages to Event Hub {0}", client.Path);
                        Random random = new Random();
                        //for (int i = 0; i < numberOfMessages; ++i)
                        while(!Console.KeyAvailable)
                        {
                            // One event per device
                            for(int devices = 0; devices < numberOfDevices; devices++)
                            {
                                // Create the device/temperature metric
                                Event info = new Event() { 
                                    TimeStamp = DateTime.UtcNow,
                                    DeviceId = random.Next(numberOfDevices),
                                    Temperature = random.Next(100)
                                };
                                // Serialize to JSON
                                var serializedString = JsonConvert.SerializeObject(info);
                                Console.WriteLine(serializedString);
                                EventData data = new EventData(Encoding.UTF8.GetBytes(serializedString))
                                {
                                    PartitionKey = info.DeviceId.ToString()
                                };

                                // Send the metric to Event Hub
                                tasks.Add(client.SendAsync(data));
                            }
                            // Sleep a second
                            Thread.Sleep(1000);
                        };

                        Task.WaitAll(tasks.ToArray());
                    }
                    catch (Exception exp)
                    {
                        Console.WriteLine("Error on send: " + exp.Message);
                    }

                }
            }
        }

    Per il momento si riceverà un avviso sulle righe che fanno riferimento alla classe Event. Ignorare questi avvisi per ora.

5.  Nel file **Program.cs** impostare le variabili seguenti all'inizio del file con i valori corrispondenti recuperati dall'hub eventi nel portale di gestione di Azure.

    | Impostare la variabile | Su                                                              |
    |------------------------|-----------------------------------------------------------------|
    | eventHubName           | Nome dell'hub eventi, ad esempio **temperature**.               |
    | eventHubNamespace      | Spazio dei nomi dell'hub eventi, ad esempio **sensors-ns**.     |
    | sharedAccessPolicyName | Criteri creati con l'accesso per invio, ad esempio **devices**. |
    | sharedAccessPolicyKey  | Chiave per i criteri con accesso per invio.                     |

6.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **SendEvents** e scegliere **Aggiungi | Classe**. Denominare la nuova classe **Event.cs**. Questa classe descrive il messaggio inviato all'hub eventi.

7.  Sostituire il contenuto del file **Event.cs** con il codice seguente.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Runtime.Serialization;
        using System.Text;
        using System.Threading.Tasks;

        namespace SendEvents
        {
            [DataContract]
            public class Event
            {
                [DataMember]
                public DateTime TimeStamp { get; set; }
                [DataMember]
                public int DeviceId { get; set; }
                [DataMember]
                public int Temperature { get; set; }
            }
        }

    Questa classe descrive i dati inviati: i valori di TimeStamp, DeviceID e Temperature.

8.  Scegliere **Salva tutto**, quindi eseguire l'applicazione per popolare l'hub eventi con messaggi.

## Creare una rete virtuale di Azure

Per fare in modo che la topologia in esecuzione nel cluster Storm comunichi direttamente con HBase, è necessario eseguire il provisioning di entrambi i server in una rete virtuale di Azure.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Nella parte inferiore della pagina fare clic su **+NUOVO**, quindi su **Servizi di rete**, **Rete virtuale** e infine su **Creazione rapida**.

3.  Digitare o selezionare i valori seguenti:

    -   **Nome**: nome della rete virtuale.
    -   **Spazio di indirizzi**: scegliere uno spazio di indirizzi per la rete virtuali con dimensioni sufficienti per fornire indirizzi per tutti i nodi del cluster. In caso contrario, il provisioning avrà esito negativo.
    -   **Numero massimo VM**: scegliere uno dei valori disponibili per il numero massimo di VM.
    -   **Percorso**: il percorso deve essere uguale a quello del cluster HBase che sarà creato.
    -   **Server DNS**: questo articolo usa un server DNS interno fornito da Azure. È quindi possibile scegliere **Nessuno**. Sono supportate anche configurazioni di rete più avanzate con server DNS personalizzati. Per indicazioni dettagliate, vedere <http://msdn.microsoft.com/library/azure/jj156088.aspx>.

4.  Fare clic su **Crea rete virtuale**. Il nome della rete virtuale sarà visualizzato nell'elenco. Attendere l'impostazione del valore **Creata** nella colonna Stato.

5.  Nel riquadro principale fare clic sulla rete virtuale appena creata.

6.  Nella parte superiore della pagina fare clic su **DASHBOARD**.

7.  In **riepilogo rapido** annotare l'**ID RETE VIRTUALE**. Questa informazione sarà necessaria per il provisioning dei cluster Storm e HBase.

8.  Nella parte superiore della pagina fare clic su **CONFIGURA**.

9.  Nella parte inferiore della pagina il nome predefinito per la subnet è **Subnet-1**. Usare il pulsante **aggiungi subnet** per aggiungere **Subnet-2**. Queste subnet includeranno i cluster Storm e HBase.

    > [WACOM.NOTE] In questo articolo si usano cluster con un solo nodo. Se si creano cluster con più nodi, verificare il valore di **CIDR (CONTEGGIO INDIRIZZI)** per la subnet che sarà usata per il cluster. Il conteggio di indirizzi deve essere superiore al numero di nodi di lavoro più sette (Gateway: 2, Nodo head: 2, Zookeeper: 3). Ad esempio, se è necessario un cluster HBase da 10 nodi, il conteggio di indirizzi per la subnet dovrà essere maggiore di 17 (10+7). In caso contrario, la distribuzione avrà esito negativo.
    >
    > È consigliabile designare una singola subnet per un cluster.

10. Fare clic su **Salva** nella parte inferiore della pagina.

## Creare il cluster Storm in HDInsight

1.  Accedere al [portale di gestione di Azure][portale di azure]

2.  Fare clic su **HDInsight** a sinistra e quindi su **+Nuovo** nell'angolo inferiore sinistro della pagina.

3.  Fare clic sull'icona di HDInsight nella seconda colonna e quindi selezionare **Personalizzato**.

4.  Nella pagina **Dettagli cluster** immettere il nome del nuovo cluster e selezionare **Storm** in **Tipo di cluster**. Selezionare la freccia per continuare.

5.  Immettere 1 in **Nodi dati** per indicare il numero di nodi per il cluster. In **Area/Rete virtuale** selezionare la rete virtuale di Azure creata in precedenza. Per **Subnet della rete virtuale** selezionare **Subnet-2**.

    > [WACOM.NOTE] Per ridurre al minimo il costo del cluster usato in questo articolo, ridurre il valore di **Dimensione del cluster** a 1 ed eliminare il cluster al termine della procedura.

6.  Immettere **Nome utente** e **Password** dell'amministratore, quindi selezionare la freccia per continuare.

7.  In **Account di archiviazione** selezionare **Crea nuova archiviazione** o selezionare un account di archiviazione esistente. Selezionare o immettere il **Nome account** e il **Contenitore predefinito** da usare. Fare clic sull'icona del segno di spunta in basso a sinistra per creare il cluster Storm.

## Creare il cluster HBase in HDInsight

1.  Accedere al [portale di gestione di Azure][portale di azure]

2.  Fare clic su **HDInsight** a sinistra e quindi su **+Nuovo** nell'angolo inferiore sinistro della pagina.

3.  Fare clic sull'icona di HDInsight nella seconda colonna e quindi selezionare **Personalizzato**.

4.  Nella pagina **Dettagli cluster** immettere il nome del nuovo cluster e selezionare **HBase** in **Tipo di cluster**. Selezionare la freccia per continuare.

5.  Immettere 1 in **Nodi dati** per indicare il numero di nodi per il cluster. In **Area/Rete virtuale** selezionare la rete virtuale di Azure creata in precedenza. Per **Subnet della rete virtuale** selezionare **Subnet-1**.

    > [WACOM.NOTE] Per ridurre al minimo il costo del cluster usato in questo articolo, ridurre il valore di **Dimensione del cluster** a 1 ed eliminare il cluster al termine della procedura.

6.  Immettere **Nome utente** e **Password** dell'amministratore, quindi selezionare la freccia per continuare.

7.  In **Account di archiviazione** selezionare **Crea nuova archiviazione** o selezionare un account di archiviazione esistente. Selezionare o immettere il **Nome account** e il **Contenitore predefinito** da usare. Fare clic sull'icona del segno di spunta in basso a sinistra per creare il cluster Storm.

    > [WACOM.NOTE] È consigliabile usare un contenitore diverso da quello usato per il cluster Storm.

### Abilitare Desktop remoto

In questa esercitazione è necessario usare Desktop remoto per accedere ai cluster Storm e HBase. Usare questa procedura per abilitare Desktop remoto su entrambi i cluster.

1.  Accedere al [portale di gestione di Azure][portale di azure].

2.  A sinistra selezionare **HDInsight**, quindi selezionare il cluster Storm nell'elenco. Infine selezionare **Configura** nella parte superiore della pagina.

3.  Nella parte inferiore della pagina selezionare **Abilita modalità remota**. Quando richiesto, immettere un nome utente, una password e una data di scadenza per l'accesso tramite Desktop remoto. Fare clic sul segno di spunta per abilitare Desktop remoto.

Dopo avere abilitato Desktop remoto, è possibile selezionare **Connetti** in fondo alla pagina. Seguire i prompt per connettersi al cluster.

### Individuare il suffisso DNS di HBase

Per scrivere in HBase dal cluster Storm, è necessario usare il nome di dominio completo del cluster HBase. Usare la procedura seguente per individuare queste informazioni.

1.  Connettersi al cluster HBase tramite Desktop remoto.

2.  Dopo essersi connessi al cluster, aprire la riga di comando di Hadoop ed eseguire il comando **ipconfig** per ottenere il suffisso DNS. Il campo **Suffisso DNS specifico della connessione** conterrà il suffisso, ad esempio **mycluster.b4.internal.cloudapp.net**. Salvare queste informazioni.

## Sviluppare la topologia Storm

> [WACOM.NOTE] La procedura descritta in questa sezione deve essere eseguita nell'ambiente di sviluppo locale.

### Scaricare e compilare le dipendenze esterne

Molte delle dipendenze usate in questo progetto devono essere scaricate e compilate singolarmente, quindi installate nel repository Maven locale nell'ambiente di sviluppo. In questa sezione si eseguirà il download e l'installazione degli elementi seguenti:

-   Spout dell'hub eventi che legge i messaggi provenienti dall'hub eventi.

-   SDK del client Java SignalR

#### Scaricare e compilare lo Spout dell'hub eventi

Per ricevere dati dall'hub eventi, si userà **eventhubs-storm-spout**.

1.  Connettersi al cluster Storm tramite Desktop remoto, quindi copiare il file **%STORM\_HOME%\\examples\\eventhubspout\\eventhubs-storm-spout-0.9-jar-with-dependencies.jar** nell'ambiente di sviluppo locale, che contiene **events-storm-spout**.

2.  Usare il comando seguente per installare il pacchetto nell'archivio Maven locale. In questo modo sarà facile aggiungerlo come riferimento nel progetto Storm in un secondo momento.

        mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

#### Scaricare e compilare il client SignalR

Per inviare messaggi al dashboard ASP.NET, usare l'[SDK del client SignalR per Java][SDK del client SignalR per Java].

1.  Aprire un prompt dei comandi.

2.  Passare alla directory in cui si vuole scaricare e archiviare il progetto di SDK del client SignalR.

3.  Usare il comando seguente per scaricare il progetto da GitHub.

    git clone https://github.com/SignalR/java-client

4.  Passare alla directory **java-client\\signalr-client-sdk** e compilare il progetto in un file JAR tramite i comandi seguenti.

        cd java-client\signalr-client-sdk
        mvn package

    > [WACOM.NOTE] Se si riceve un errore che indica che la dipendenza **gson** non può essere scaricata, rimuovere le righe seguenti dal file **java-client\\signalr-client-sdk\\pom.xml**.
    >
    > ``` <repositories>
    > <repository>
    > <id>central</id>
    > <name>Central</name>
    > <url>http://maven.eclipse.org/build</url>
    > </repository>
    > </repositories>
    > ```
    >
    > Con la rimozione di queste righe Maven effettuerà il pull del file dal repository centrale (comportamento predefinito). Per fare in modo che Maven riprovi a cercare nel repository, usare il comando `-U`. ad esempio, `mvn package -U`.

5.  Usare il comando seguente per installare il pacchetto nell'archivio Maven locale. In questo modo sarà facile aggiungerlo come riferimento nel progetto Storm in un secondo momento.

        mvn install:install-file -Dfile=target\signalr-client-sdk-1.0.jar -DgroupId=microsoft.aspnet.signalr -DartifactId=signalr-client-sdk -Dversion=1.0 -Dpackaging=jar

### Eseguire lo scaffolding del progetto di topologia Storm

Dopo avere installato lo Spout dell'hub eventi e il client SignalR nel repository locale, usare Maven per creare lo scaffolding per il progetto di topologia Storm.

1.  Aprire un prompt dei comandi, una sessione Bash o Terminal oppure lo strumento usato normalmente per digitare i comandi nel sistema.

2.  Passare al percorso in cui si vuole creare il progetto, ad esempio una directory in cui si archiviano tutti i progetti di codice.

3.  Usare il seguente comando Maven per creare uno scaffolding di base per l'applicazione.

        mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.examples -DartifactId=TemperatureMonitor -DinteractiveMode=false

    Il comando eseguirà le operazioni seguenti:

    -   Creazione di una nuova directory con il valore *artifactId* specificato. In questo caso è **Temperature**.
    -   Creazione di un file **pom.xml** contenente informazioni Maven per il progettto.
    -   Creazione di una struttura di directory **src** contenente codice e test di base.

### Aggiungere dipendenze e plug-in

A questo punto modificare **pom.xml** in modo che faccia riferimento alle dipendenze del progetto e ai plug-in Maven da usare per la compilazione e la creazione dei pacchetti.

1.  Aprire il file **pom.xml** in un editor di testo e aggiungere il codice seguente alla sezione **\<dependencies\>**. È possibile aggiungerlo alla fine della sezione, dopo la dipendenza per junit.

        <dependency>
          <groupId>org.apache.storm</groupId>
          <artifactId>storm-core</artifactId>
          <version>0.9.2-incubating</version>
          <!-- keep storm out of the jar-with-dependencies -->
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>microsoft.aspnet.signalr</groupId>
          <artifactId>signalr-client-sdk</artifactId>
          <version>1.0</version>
        </dependency>
        <dependency>
          <groupId>com.microsoft.eventhubs</groupId>
          <artifactId>eventhubs-storm-spout</artifactId>
          <version>0.9</version>
        </dependency>
        <dependency>
          <groupId>com.google.code.gson</groupId>
          <artifactId>gson</artifactId>
          <version>2.2.2</version>
        </dependency>
        <dependency>
          <groupId>com.github.ptgoetz</groupId>
          <artifactId>storm-hbase</artifactId>
          <version>0.1.2</version>
        </dependency>
        <dependency>
          <groupId>com.netflix.curator</groupId>
          <artifactId>curator-framework</artifactId>
          <version>1.3.3</version>
          <exclusions>
            <exclusion>
              <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
              </exclusion>
            <exclusion>
              <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
          </exclusions>
          <scope>provided</scope>
        </dependency>

    In questo modo vengono aggiunte le dipendenze per:

    -   lo spout dell'hub eventi eventhubs-storm-spout
    -   il client SignalR signalr-client-sdk
    -   la dipendenza gson del client SignalR, che verrà usata anche per creare JSON durante la scrittura in SignalR
    -   storm-core, che fornisce le funzionalità principali per le applicazioni Storm
    -   slf4j, che fornisce funzionalità di registrazione e viene usato da eventhubs-storm-spout
    -   curator-framework, usato da eventhubs-storm-spout
    -   le classi principali storm-core di Storm
    -   le classi storm-hbase che consentono la scrittura in HBase

    > [WACOM.NOTE] Si noti che alcune dipendenze sono contrassegnate con l'ambito **provided** per indicare che devono essere scaricate dal repository Maven e usate per compilare e testare l'applicazione in locale, ma che saranno anche disponibili nell'ambiente di runtime e che non sarà necessario compilarle e includerle nel file JAR creato dal progetto.

2.  Alla fine del file **pom.xml**, appena prima della voce **\</project\>**, aggiungere il codice seguente.

          <build>
            <plugins>
              <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.3.2</version>
                <configuration>
                  <source>1.7</source>
                  <target>1.7</target>
                </configuration>
              </plugin>
              <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                  <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                  </transformers>
                </configuration>
                <executions>
                  <execution>
                    <phase>package</phase>
                    <goals>
                      <goal>shade</goal>
                    </goals>
                  </execution>
                </executions>
              </plugin>
              <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>1.2.1</version>
                <executions>
                  <execution>
                  <goals>
                    <goal>exec</goal>
                  </goals>
                  </execution>
                </executions>
                <configuration>
                  <executable>java</executable>
                  <includeProjectDependencies>true</includeProjectDependencies>
                  <includePluginDependencies>false</includePluginDependencies>
                  <classpathScope>compile</classpathScope>
                  <mainClass>${storm.topology}</mainClass>
                </configuration>
              </plugin>
            </plugins>
            <resources>
              <resource>
                <directory>${basedir}/conf</directory>
                <filtering>false</filtering>
                <includes>
                  <include>Config.properties</include>
                  <include>hbase-site.xml</include>
                </includes>
              </resource>
            </resources>
          </build>

    Questo codice indica a Maven di eseguire queste operazioni durante la compilazione del progetto:

    -   Includere il file di risorse **/conf/Config.properties**. Questo file verrà creato più avanti, ma conterrà informazioni di configurazione per la connessione all'hub eventi di Azure.
    -   Includere il file di risorse **/conf/hbase-site.xml**. Questo file verrà creato più avanti, ma conterrà informazioni sulla connessione a HBase
    -   Usare **maven-compiler-plugin** per compilare l'applicazione.
    -   Usare **maven-shade-plugin** per compilare un file uberjar o fatjar contenente il progetto e tutte le dipendenze necessarie.
    -   Usare **exec-maven-plugin**, che consente di eseguire l'applicazione in locale senza un cluster Hadoop.

### Aggiungere i file di configurazione

**eventhubs-storm-spout** legge le informazioni di configurazione da un file **Config.properties** in cui è indicato l'hub eventi a cui connettersi. È possibile specificare un file di configurazione quando si avvia la topologia su un cluster, ma l'inclusione del file nel progetto offre una configurazione predefinita nota.

1.  Nella directory **Temperature** creare una nuova directory denominata **conf**.

2.  Nella directory **conf** creare due nuovi file:

    -   **Config.properties** contenente le impostazioni per l'hub eventi
    -   **hbase-site.xml** contenente le impostazioni per la connessione a HBase

3.  Usare il codice seguente come contenuto del file **Config.properties**.

        eventhubspout.username = storm

        eventhubspout.password = <the key of the 'storm' policy>

        eventhubspout.namespace = <the event hub namespace>

        eventhubspout.entitypath = temperature

        eventhubspout.partitions.count = <the number of partitions for the event hub>

        # if not provided, will use storm's zookeeper settings
        # zookeeper.connectionstring=localhost:2181

        eventhubspout.checkpoint.interval = 10

        eventhub.receiver.credits = 1024

    Sostituire il valore **password** con la chiave dei criteri **storm** creata in precedenza nell'hub eventi. Sostituire **namespace** con lo spazio dei nomi dell'hub eventi.

4.  Usare il codice seguente come contenuto del file **hbase-site.xml**.

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
         * Copyright 2010 The Apache Software Foundation
         *
         * Licensed to the Apache Software Foundation (ASF) under one
         * or more contributor license agreements.  See the NOTICE file
         * distributed with this work for additional information
         * regarding copyright ownership.  The ASF licenses this file
         * to you under the Apache License, Version 2.0 (the
         * "License"); you may not use this file except in compliance
         * with the License.  You may obtain a copy of the License at
         *
         *     http://www.apache.org/licenses/LICENSE-2.0
         *
         * Unless required by applicable law or agreed to in writing, software
         * distributed under the License is distributed on an "AS IS" BASIS,
         * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
         * See the License for the specific language governing permissions and
         * limitations under the License.
         */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0.suffix,zookeeper1.suffix,zookeeper2.suffix</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

5.  Nel file **hbase-site.xml** sostituire il valore **suffix** per le voci zookeeper con il suffisso DNS recuperato in precedenza per HBase. Ad esempio, **zookeeper0.mycluster.b4.internal.cloudapp.net, zookeeper1.mycluster.b4.internal.cloudapp.net, zookeeper2.mycluster.b4.internal.cloudapp.net**.

6.  Salvare i file.

### Aggiungere helper

Per supportare la serializzazione a e da JSON, sono necessarie alcune classi helper che definiscono la struttura degli oggetti.

1.  Nella directory **\\temperaturemonitor\\src\\main\\java\\com\\microsoft\\examples** creare una nuova directory denominata **helpers**.

2.  Nella directory **helpers** creare due nuovi file:

    -   **EventHubMessage.java** per definire il formato dei messaggi dell'hub eventi

    -   **SignalRMessage.java** per definire il formato del messaggio inviato a SignalR

3.  Usare il codice seguente come contenuto del file **EventHubMessage.java**.

        package com.microsoft.examples;

        public class EventHubMessage {
          String TimeStamp;
          int DeviceId;
          int Temperature;
        }

4.  Usare il codice seguente come contenuto del file **SignalRMessage.java**.

        package com.microsoft.examples;

        public class SignalRMessage {
          int device;
          int temperature;
        }

5.  Salvare e chiudere i file.

### Aggiungere Bolt

Un Bolt esegue la parte principale dell'elaborazione in una topologia. Per questa tipologia sono presenti tre Bolt, sebbene uno sia hbase-bolt, che sarà scaricato automaticamente durante la compilazione del progetto.

1.  Nella directory **\\temperaturemonitor\\src\\main\\java\\com\\microsoft\\examples** creare una nuova directory denominata **bolts**.

2.  Nella directory **bolts** creare due nuovi file:

    -   **ParserBolt.java** per analizzare il messaggio in ingresso dall'hub eventi nei singoli campi e creare due flussi
    -   **DashboardBolt.java** per registrare informazioni nel dashboard Web tramite SignalR

3.  Usare il codice seguente come contenuto del file **ParserBolt.java**.

        package com.microsoft.examples;

        import backtype.storm.topology.base.BaseBasicBolt;
        import backtype.storm.topology.BasicOutputCollector;
        import backtype.storm.topology.OutputFieldsDeclarer;
        import backtype.storm.tuple.Tuple;
        import backtype.storm.tuple.Fields;
        import backtype.storm.tuple.Values;

        import com.google.gson.Gson;
        import com.google.gson.GsonBuilder;

        public class ParserBolt extends BaseBasicBolt {

          //Declare output fields & streams
          //hbasestream is all fields, and goes to hbase
          //dashstream is just the device and temperature, and goes to the dashboard
          @Override
          public void declareOutputFields(OutputFieldsDeclarer declarer) {
            declarer.declareStream("hbasestream", new Fields("timestamp", "deviceid", "temperature"));
            declarer.declareStream("dashstream", new Fields("deviceid", "temperature"));
          }

          //Process tuples
          @Override
          public void execute(Tuple tuple, BasicOutputCollector collector) {
            Gson gson = new Gson();
            //Should only be one tuple, which is the JSON message from the spout
            String value = tuple.getString(0);

            //Convert it from JSON to an object
            EventHubMessage evMessage = gson.fromJson(value, EventHubMessage.class);

            //Pull out the values and emit as a stream
            String timestamp = evMessage.TimeStamp;
            int deviceid = evMessage.DeviceId;
            int temperature = evMessage.Temperature;
            collector.emit("hbasestream", new Values(timestamp, deviceid, temperature));
            collector.emit("dashstream", new Values(deviceid, temperature));
          }
        }

4.  Usare il codice seguente come contenuto del file **DashboardBolt.java**.

        package com.microsoft.examples;

        import backtype.storm.topology.BasicOutputCollector;
        import backtype.storm.topology.OutputFieldsDeclarer;
        import backtype.storm.topology.base.BaseBasicBolt;
        import backtype.storm.tuple.Tuple;
        import backtype.storm.task.TopologyContext;
        import backtype.storm.Config;
        import backtype.storm.Constants;

        import microsoft.aspnet.signalr.client.Action;
        import microsoft.aspnet.signalr.client.ErrorCallback;
        import microsoft.aspnet.signalr.client.LogLevel;
        import microsoft.aspnet.signalr.client.Logger;
        import microsoft.aspnet.signalr.client.MessageReceivedHandler;
        import microsoft.aspnet.signalr.client.hubs.HubConnection;
        import microsoft.aspnet.signalr.client.hubs.HubProxy;

        import com.google.gson.Gson;
        import com.google.gson.GsonBuilder;

        import java.util.Map;

        public class DashboardBolt extends BaseBasicBolt {
          //Connection and proxy for SignalR hub
          private HubConnection conn;
          private HubProxy proxy;

          //Declare output fields
          @Override
          public void declareOutputFields(OutputFieldsDeclarer declarer) {
            //no stream output - we talk directly to SignalR
          }

          @Override
          public void prepare(Map config, TopologyContext context) {

            // Connect to the DashHub SignalR server
            conn = new HubConnection("http://dashboard.azurewebsites.net/");
            // Create the hub proxy
            proxy = conn.createHubProxy("DashHub");
            // Subscribe to the error event
            conn.error(new ErrorCallback() {
              @Override
              public void onError(Throwable error) {
                error.printStackTrace();
              }
            });
            // Subscribe to the connected event
            conn.connected(new Runnable() {
              @Override
              public void run() {
                System.out.println("CONNECTED");
              }
            });
            // Subscribe to the closed event
            conn.closed(new Runnable() {
              @Override
              public void run() {
                System.out.println("DISCONNECTED");
              }
            });
            // Start the connection
            conn.start()
              .done(new Action<Void>() {
                @Override
                public void run(Void obj) throws Exception {
                  System.out.println("Done Connecting!");
                }
            });
          }

          //Process tuples
          @Override
          public void execute(Tuple tuple, BasicOutputCollector collector) {
            Gson gson = new Gson();
            try {
              //Get the deviceid and temperature by field name
              int deviceid = tuple.getIntegerByField("deviceid");
              int temperature = tuple.getIntegerByField("temperature");
              //Construct the SignalR message
              SignalRMessage srMessage = new SignalRMessage();
              srMessage.device = deviceid;
              srMessage.temperature = temperature;
              // send it as JSON
              proxy.invoke("send", gson.toJson(srMessage));
            } catch (Exception e) {
               // LOG.error("Bolt execute error: {}", e);
               collector.reportError(e);
            }
          }
        }

    Sostituire `http://yourwebsiteaddress` con l'indirizzo del sito Web di Azure in cui è stato precedentemente pubblicato il dashboard, ad esempio http://mydashboard.azurewebsites.net.

5.  Salvare e chiudere i file.

### Definire la topologia

La topologia descrive il flusso dei dati tra gli Spout e i Bolt in una topologia e il grado di parallelismo per la topologia e i relativi componenti.

1.  Nella directory **\\temperaturemonitor\\src\\main\\java\\com\\microsoft\\examples** creare un nuovo file denominato **Temperature.java**.

2.  Aprire il file **Temperature.java** e inserire il codice seguente come contenuto del file.

        package com.microsoft.examples;

        import backtype.storm.Config;
        import backtype.storm.LocalCluster;
        import backtype.storm.StormSubmitter;
        import backtype.storm.generated.StormTopology;
        import backtype.storm.topology.TopologyBuilder;
        import backtype.storm.tuple.Fields;
        import com.microsoft.eventhubs.spout.EventHubSpout;
        import com.microsoft.eventhubs.spout.EventHubSpoutConfig;

        import java.io.FileReader;
        import java.util.Properties;

        //hbase
        import org.apache.storm.hbase.bolt.mapper.SimpleHBaseMapper;
        import org.apache.storm.hbase.bolt.HBaseBolt;
        import java.util.Map;
        import java.util.HashMap;
        import backtype.storm.tuple.Fields;

        public class Temperature
        {
          protected EventHubSpoutConfig spoutConfig;
          protected int numWorkers;

          // Reads the configuration information for the Event Hub spout
          protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if(args.length > 1) {
              properties.load(new FileReader(args[1]));
            }
            else {
              properties.load(Temperature.class.getClassLoader().getResourceAsStream(
                "Config.properties"));
            }

            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties.getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties.getProperty("zookeeper.connectionstring");
            int partitionCount = Integer.parseInt(properties.getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties.getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties.getProperty("eventhub.receiver.credits"));
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: " + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
            spoutConfig = new EventHubSpoutConfig(username, password,
              namespaceName, entityPath, partitionCount, zkEndpointAddress,
              checkpointIntervalInSeconds, receiverCredits);

            //set the number of workers to be the same as partition number.
            //the idea is to have a spout and a partial count bolt co-exist in one
            //worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();

            if(args.length > 0) {
              //set topology name so that sample Trident topology can use it as stream name.
              spoutConfig.setTopologyName(args[0]);
            }
          }

          // Create the spout using the configuration
          protected EventHubSpout createEventHubSpout() {
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            return eventHubSpout;
          }

          // Build the topology
          protected StormTopology buildTopology(EventHubSpout eventHubSpout, SimpleHBaseMapper mapper) {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
            // Name the spout 'EventHubsSpout', and set it to create
            // as many as we have partition counts in the config file
            topologyBuilder.setSpout("EventHub", eventHubSpout, spoutConfig.getPartitionCount())
              .setNumTasks(spoutConfig.getPartitionCount());
            // Create the parser bolt, which subscribes to the stream from EventHub
            topologyBuilder.setBolt("Parser", new ParserBolt(), spoutConfig.getPartitionCount())
              .localOrShuffleGrouping("EventHub").setNumTasks(spoutConfig.getPartitionCount());
            // Create the dashboard bolt, which subscribes to the stream from Parser
            topologyBuilder.setBolt("Dashboard", new DashboardBolt(), spoutConfig.getPartitionCount())
              .fieldsGrouping("Parser", "dashstream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());
            // Create the HBase bolt, which subscribes to the stream from Parser
            // WARNING - uncomment the following two lines when deploying
            // leave commented when testing locally
            // topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
            //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
          }

          protected void submitTopology(String[] args, StormTopology topology, Config config) throws Exception {
            // Config config = new Config();
            config.setDebug(false);

            //Enable metrics
            config.registerMetricsConsumer(backtype.storm.metric.LoggingMetricsConsumer.class, 1);

            // Is this running locally, or on an HDInsight cluster?
            if (args != null && args.length > 0) {
              config.setNumWorkers(numWorkers);
              StormSubmitter.submitTopology(args[0], config, topology);
            } else {
              config.setMaxTaskParallelism(2);

              LocalCluster localCluster = new LocalCluster();
              localCluster.submitTopology("test", config, topology);

              Thread.sleep(5000000);

              localCluster.shutdown();
            }
          }

          // Loads the configuration, creates the spout, builds the topology,
          // and then submits it
          protected void runScenario(String[] args) throws Exception{
            readEHConfig(args);
            Config config = new Config();

            //hbase configuration
            Map<String, Object> hbConf = new HashMap<String, Object>();
            if(args.length > 0) {
              hbConf.put("hbase.rootdir", args[0]);
            }
            config.put("hbase.conf", hbConf);
            SimpleHBaseMapper mapper = new SimpleHBaseMapper()
                  .withRowKeyField("deviceid")
                  .withColumnFields(new Fields("timestamp", "temperature"))
                  .withColumnFamily("cf");

            EventHubSpout eventHubSpout = createEventHubSpout();
            StormTopology topology = buildTopology(eventHubSpout, mapper);
            submitTopology(args, topology, config);
          }

          public static void main(String[] args) throws Exception {
            Temperature scenario = new Temperature();
            scenario.runScenario(args);
          }
        }

    > [AZURE.NOTE] Si noti che le righe relative a **HBaseBolt** contengono commenti, in quanto il passaggio successivo consiste nell'eseguire la topologia a livello locale. Poiché HBaseBolt comunica direttamente con HBase, restituirà degli errori se è abilitato, a meno che non sia stata configurata una rete virtuale con un server DNS e la macchina virtuale non sia stata aggiunta alla rete.

### Testare la topologia in locale

Per compilare e testare il file nel computer di sviluppo, eseguire la procedura seguente.

1.  Avviare l'applicazione **SendEvent** .NET per iniziare a inviare eventi, in modo che ci sia qualcosa da leggere dall'hub eventi.

2.  Aprire un Web browser con il dashboard Web distribuito in precedenza in un sito Web di Azure. Sarà così possibile vedere la rappresentazione dei valori nel grafico man mano che questi attraversano la topologia

3.  Avviare la topologia in locale con il comando seguente

    mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

    La topologia verrà avviata, i file verranno letti dall'hub eventi e quindi verranno inviati al dashboard in esecuzione In Siti Web di Azure. Dovrebbe essere possibile vedere le linee comparire nel dashboard Web.

4.  Dopo avere verificato il funzionamento di questa caratteristica, arrestare la topologia premendo Ctrl+C. Per arrestare l'app SendEvent, selezionare la finestra e premere un tasto.

### Abilitare HBaseBolt e preparare HBase

1.  Aprire il file **Temperature.java** e rimuovere il commento (//) dalle righe seguenti:

        //topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
        //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

    Questa operazione consente di abilitare il Bolt HBase.

2.  Salvare **Temperature.java**.

3.  Connettersi al cluster HBase tramite Desktop remoto.

4.  Dal desktop avviare le riga di comando di HDInsight e immettere i comandi seguenti.

        cd %hbase_home%
        bin\hbase shell

5.  Dalla shell HBase immettere il comando seguente per creare una tabella in cui verranno archiviati i dati dei sensori.

        create 'SensorData', 'cf'

6.  Verificare che la tabella non contenga dati immettendo il comando seguente.

        scan 'SensorData'

Per il momento lasciare aperto il prompt nella shell HBase.

## Creare il pacchetto della topologia e distribuirlo in HDInsight

Nell'ambiente di sviluppo seguire la procedura seguente per eseguire la topologia Temperature nel cluster Storm di HDInsight.

1.  Usare il comando seguente per creare un pacchetto JAR dal progetto.

        mvn package

    Verrà creato un file denominato **TemperatureMonitor-1.0-SNAPSHOT.jar** nella directory **target** del progetto.

2.  Nel computer di sviluppo locale avviare l'applicazione .NET **SendEvents** in modo che siano presenti eventi da leggere.

3.  Connettersi al cluster Storm di HDInsight tramite Desktop remoto e copiare il file **TemperatureMonitor-1.0-SNAPSHOT.jar** nella directory **c:\\apps\\dist\\storm\<version number\>**.

4.  Usare l'icona della **riga di comando di HDInsight** nel desktop del cluster per aprire un nuovo prompt dei comandi e usare i comandi seguenti per eseguire la topologia.

        cd %storm_home%
        bin\storm jar TemperatureMonitor-1.0-SNAPSHOT.jar com.microsoft.examples.Temperature Temperature

5.  Una volta avviata la topologia, potrebbero essere necessari alcuni secondi prima che gli elementi compaiano nel dashboard Web.

6.  Quando nel dashboard compaiono alcuni elementi, passare alla sessione di Desktop remoto nel cluster HBase.

7.  Dalla shell HBase immettere il comando seguente.

        scan 'SensorData'

    Si noti che vengono restituite alcune righe di dati scritte dalla topologia Storm.

8.  Per arrestare la topologia, passare alla sessione di Desktop remoto con il cluster Storm e immettere il comando seguente nella riga di comando di HDInsight.

        bin\storm kill Temperature

    Dopo alcuni secondi la topologia si arresterà.

## Riepilogo

In questo articolo è stato illustrato come usare Storm per leggere dati dall'hub eventi, archiviare dati in HBase e visualizzare informazioni da Storm su un dashboard esterno tramite SignalR e D3.js.

-   Per altre informazioni su Apache Storm, vedere <https://storm.incubator.apache.org/>

-   Per altre informazioni su HBase con HDInsight, vedere la [panoramica su HBase con HDInsight][panoramica su HBase con HDInsight]

-   Per altre informazioni su SignalR, vedere [ASP.NET SignalR][ASP.NET SignalR]

-   Per altre informazioni su D3.js, vedere [D3.js - Data Driven Documents][D3.js - Data Driven Documents]

-   Per altre informazioni sulla creazione di topologie in .NET, vedere [Sviluppare applicazioni di elaborazione di flussi di dati con SCP.NET e C# tramite Storm in HDInsight][Sviluppare applicazioni di elaborazione di flussi di dati con SCP.NET e C# tramite Storm in HDInsight].

  [Microsoft Azure SDK per .NET]: http://azure.microsoft.com/it-it/downloads/archive-net-downloads/
  [Java e JDK]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  [Maven]: http://maven.apache.org/what-is-maven.html
  [Git]: http://git-scm.com/
  [Chocolatey NuGet]: http://chocolatey.org/
  [SignalR]: http://www.asp.net/signalr/overview/getting-started/introduction-to-signalr
  [StormTweetsSentimentD3Viz]: https://github.com/P7h/StormTweetsSentimentD3Viz
  [portale di Azure]: https://manage.windowsazure.com
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [SDK del client SignalR per Java]: https://github.com/SignalR/java-client
  [panoramica su HBase con HDInsight]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-hbase-overview/
  [ASP.NET SignalR]: http://signalr.net/
  [D3.js - Data Driven Documents]: http://d3js.org/
  [Sviluppare applicazioni di elaborazione di flussi di dati con SCP.NET e C# tramite Storm in HDInsight]: /it-it/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/
