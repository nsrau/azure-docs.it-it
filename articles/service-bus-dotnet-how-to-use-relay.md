<properties urlDisplayName="Service Bus Relay" pageTitle="Come usare Inoltro del bus di servizio (.NET) - Azure" metaKeywords="get started azure Service Bus Relay C# " description="Informazioni su come usare il servizio di inoltro del bus di servizio di Azure per connettere due applicazioni ospitate in località diverse." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use the Service Bus Relay Service" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />







# Come usare il servizio Inoltro del bus di servizio

Questa guida illustra come usare il servizio Inoltro del bus di servizio.
Negli esempi, scritti in C#, viene usata l'API Windows Communication Foundation con le estensioni contenute nell'assembly del bus di servizio incluso in Azure SDK per .NET. Per altre informazioni sul servizio Inoltro del bus di servizio, vedere la sezione "Passaggi successivi".

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

<h2>Informazioni su Inoltro del bus di servizio</h2>

Il servizio **Inoltro** del bus di servizio consente di creare **applicazioni ibride** che vengono eseguite sia in un data center di Azure che nell'ambiente aziendale locale. A tale scopo, consente di esporre in modo sicuro nel cloud pubblico i servizi WCF (Windows Communication Foundation) che risiedono in una rete aziendale, senza dover aprire una connessione firewall o richiedere modifiche di notevole impatto a un'infrastruttura di rete aziendale.

![Relay Concepts](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Il servizio Inoltro del bus di servizio consente di ospitare servizi WCF nell'ambiente aziendale esistente. È quindi possibile delegare al servizio del bus di servizio in esecuzione in Azure l'ascolto delle sessioni in ingresso e delle richieste a questi servizi WCF. In questo modo è possibile esporre tali servizi al codice dell'applicazione in esecuzione in Azure oppure ad ambienti destinati a personale che accede da dispositivi mobili o a partner che accedono tramite Extranet. Il bus di servizio consente di controllare in modo sicuro ed estremamente dettagliato gli utenti autorizzati ad accedere ai servizi. È uno strumento efficace e sicuro per esporre dati e funzionalità dell'applicazione dalle soluzioni aziendali esistenti e di sfruttarle dal cloud.

In questa guida viene illustrato come usare il servizio Inoltro del bus di servizio per creare un servizio Web WCF, esposto tramite un'associazione di canale TCP che implementa una conversazione protetta tra due parti.

##Creare uno spazio dei nomi servizio

Per iniziare a usare il servizio Inoltro del bus di servizio in Azure, è innanzitutto necessario creare uno spazio dei nomi servizio. Uno spazio dei nomi fornisce un contenitore di ambito per fare riferimento alle risorse del bus di servizio all'interno dell'applicazione.

Per creare uno spazio dei nomi servizio:

1.  Accedere al [portale di gestione di Azure][].

2.  Nel pannello di navigazione sinistro del portale di gestione fare clic su **Bus di servizio**.

3.  Nel riquadro inferiore del portale di gestione fare clic su **Crea**.   

	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)

4.  Nella finestra di dialogo **Aggiungi un nuovo spazio dei nomi** immettere un nome per lo spazio dei nomi.
    Verrà effettuato immediatamente un controllo sulla disponibilità del nome.   

	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-04.png)


5.  Dopo avere verificato che lo spazio dei nomi è disponibile, scegliere il paese o l'area in cui dovrà essere ospitato. Assicurarsi di usare lo stesso paese/area in cui verranno distribuite le risorse di calcolo.

	IMPORTANTE: selezionare la **stessa area** che si intende scegliere per la distribuzione dell'applicazione. In questo modo sarà possibile ottenere prestazioni ottimali.

6.	Fare clic sul segno di spunta. A questo punto, lo spazio dei nomi servizio verrà creato e abilitato nel sistema. Potrebbero essere necessari alcuni minuti per consentire al sistema di effettuare il provisioning delle risorse per lo spazio dei nomi creato.

	![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-27.png)

	Lo spazio dei nomi creato verrà quindi visualizzato nel portale di gestione e sarà necessario attendere qualche istante affinché venga attivato. Prima di continuare, attendere che lo stato sia **Attivo**.

##Recuperare le credenziali di gestione predefinite per lo spazio dei nomi

Per poter eseguire le operazioni di gestione, ad esempio creare una connessione di inoltro, nel nuovo spazio dei nomi, è necessario configurare la regola di autorizzazione con firma di accesso condiviso per lo spazio dei nomi. Per altre informazioni sulle firma di accesso condiviso, vedere [Autenticazione della firma di accesso condiviso con il bus di servizio][].

1.  Nel pannello di navigazione sinistro fare clic sul nodo **Bus di servizio** per visualizzare l'elenco degli spazi dei nomi disponibili:   
	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)


2.  Fare doppio clic sullo spazio dei nomi appena creato nell'elenco visualizzato:   
	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-09.png)


3.  Fare clic sulla scheda **Configura** nella parte superiore della pagina.   
 
4.  Quando viene eseguito il provisioning di uno spazio dei nomi del bus di servizio, per impostazione predefinita viene creata una regola **SharedAccessAuthorizationRule**, con **KeyName** impostato su **RootManageSharedAccessKey**. In questa pagina viene visualizzata questa chiave, nonché la chiave primaria e quella secondaria per la regola predefinita. 

##Ottenere il pacchetto NuGet del bus di servizio##

Il pacchetto **NuGet** del bus di servizio è il modo più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con tutte le dipendenze di tale servizio. L'estensione NuGet di Visual Studio semplifica l'installazione e l'aggiornamento di librerie e strumenti in Visual Studio e in Visual Studio Express 2012 per il Web. Il pacchetto NuGet del bus di servizio è il modo più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con tutte le dipendenze di tale servizio.

Per installare il pacchetto NuGet nell'applicazione, eseguire le operazioni seguenti:

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti**, quindi scegliere **Gestisci pacchetti NuGet**.
2.  Cercare "WindowsAzure" e selezionare l'elemento **Bus di servizio di Azure**. Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.

	![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)
  

##Come esporre e usare un servizio Web SOAP con TCP tramite il bus di servizio

Per esporre un servizio Web SOAP WCF esistente per l'utilizzo esterno, è necessario apportare modifiche alle associazioni e agli indirizzi del servizio. A seconda di come sono stati installati e configurati i servizi WCF, potrebbe essere necessario modificare il file di configurazione o il codice. Si noti che WCF consente di usare più endpoint di rete con lo stesso servizio, pertanto è possibile continuare a mantenere gli endpoint interni esistenti pur aggiungendo endpoint del bus di servizio per l'accesso esterno.

In questa attività si creerà un semplice servizio WCF al quale verrà aggiunto un listener del bus di servizio. Per questo esercizio si presuppone una certa conoscenza di Visual Studio, pertanto non è inclusa una spiegazione dettagliata della procedura di creazione di un progetto, ma l'attenzione è rivolta principalmente al codice.

Prima di iniziare, completare la procedura seguente per configurare l'ambiente:

1.  In Visual Studio creare un'applicazione console contenente due progetti, "Client" e "Service", all'interno della soluzione.
2.  Aggiungere il pacchetto **NuGet del bus di servizio di Azure** a entrambi i progetti.
    In questo modo si aggiungeranno ai progetti tutti i riferimenti di assembly necessari.

### Come creare il servizio

Creare innanzitutto il servizio stesso. Tutti i servizi WCF sono costituiti da almeno tre parti distinte:

-   Definizione di un contratto che descrive i messaggi da scambiare e le operazioni da richiamare. 
-   Implementazione del suddetto contratto.
-   Host che ospita il servizio ed espone un numero di endpoint.

Negli esempi di codice di questa sezione vengono trattati singolarmente tutti questi componenti.

Il contratto consente di definire una singola operazione, **AddNumbers**, che somma due numeri e restituisce il risultato. L'interfaccia **IProblemSolverChannel**  consente al client di gestire più facilmente la durata del proxy. La creazione di tale interfaccia rientra tra le procedure consigliate. È opportuno inserire questa definizione del contratto in un file separato in modo da potervi fare riferimento da entrambi i progetti "Client" e "Service", ma è comunque possibile copiare il codice in entrambi i progetti:

        using System.ServiceModel;
     
        [ServiceContract(Namespace = "urn:ps")]
        interface IProblemSolver
        {
            [OperationContract]
            int AddNumbers(int a, int b);
        }
     
        interface IProblemSolverChannel : IProblemSolver, IClientChannel {}

Una volta definito il contratto, l'implementazione è davvero semplice:

        class ProblemSolver : IProblemSolver
        {
            public int AddNumbers(int a, int b)
            {
                return a + b;
            }
        }

####Come configurare un host del servizio a livello di codice####

Una volta definiti il contratto e l'implementazione, è ora possibile ospitare il servizio. Per l'hosting viene usato un oggetto **System.ServiceModel.ServiceHost**, che si occupa di gestire le istanze del servizio e ospita gli endpoint che sono in ascolto dei messaggi. Nel codice seguente il servizio viene configurato sia con un endpoint locale normale che con un endpoint del bus di servizio per illustrare l'aspetto di endpoint interni ed esterni affiancati. Sostituire la stringa "\*\*namespace\*\*" con il nome dello spazio dei nomi e "\*\*key\*\*" con la chiave dell'autorità emittente ottenuta nel passaggio di configurazione precedente. 

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

    sh.AddServiceEndpoint(
       typeof (IProblemSolver), new NetTcpBinding(), 
       "net.tcp://localhost:9358/solver");

    sh.AddServiceEndpoint(
       typeof(IProblemSolver), new NetTcpRelayBinding(), 
       ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver"))
        .Behaviors.Add(new TransportClientEndpointBehavior {
              TokenProvider = TokenProvider.CreateSharedSecretTokenProvider( "owner", "**key**")});

    sh.Open();

    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();

    sh.Close();

Nell'esempio vengono creati due endpoint inclusi nella stessa implementazione del contratto: uno è locale e l'altro è proiettato tramite il bus di servizio. Le principali differenze tra loro sono costituite dalle associazioni. Per quello locale viene usato **NetTcpBinding**, mentre per l'endpoint del bus di servizio e per gli indirizzi viene usato **NetTcpRelayBinding**. L'endpoint locale è dotato di un indirizzo di rete locale con una porta distinta. L'endpoint del bus di servizio include invece un indirizzo composto dalla stringa "sb", dal nome dello spazio dei nomi e dal percorso "solver". Si ottiene in tal modo l'URI "sb://[SpazioNomiServizio].servicebus.windows.net/solver", che identifica l'endpoint di servizio come endpoint TCP del bus di servizio con un nome DNS esterno completo. Se si inserisce il codice sostituendo i segnaposto come spiegato in precedenza nella funzione **Main** dell'applicazione "Service", si otterrà un servizio funzionante. Se si vuole che il servizio sia in ascolto esclusivamente sul bus di servizio, rimuovere la dichiarazione dell'endpoint locale.

####Come configurare un host del servizio nel file App.config####

È anche possibile configurare l'host usando il file App.config. Il codice per l'hosting del servizio in questo caso è il seguente:

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
    sh.Open();
    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();
    sh.Close();

Le definizioni dell'endpoint vengono spostate nel file App.config. Si noti che il pacchetto **NuGet** ha già aggiunto al file App.config una serie di definizioni, che sono le estensioni di configurazione necessarie per il bus di servizio. Il frammento di codice seguente, che è l'esatto equivalente del codice riportato in precedenza, deve essere inserito direttamente sotto l'elemento **system.serviceModel**. In questo frammento di codice si presuppone che il nome dello spazio dei nomi del progetto C\# sia "Service".
Sostituire i segnaposto con lo spazio dei nomi e la chiave del servizio del bus di servizio.

    <services>
        <service name="Service.ProblemSolver">
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpBinding"
                      address="net.tcp://localhost:9358/solver"/>
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpRelayBinding"
                      address="sb://**namespace**.servicebus.windows.net/solver"
                      behaviorConfiguration="sbTokenProvider"/>
        </service>
    </services>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

Dopo aver apportato queste modifiche, il servizio viene avviato come in precedenza, ma con due endpoint dinamici, uno locale e l'altro in ascolto nel cloud.

### Come creare il client

####Come configurare un client a livello di codice####

Per usare il servizio, è possibile costruire un client WCF usando un oggetto **ChannelFactory**. Il bus di servizio usa un modello di sicurezza basato sulle attestazioni implementato tramite il Servizio di controllo di accesso. La classe **TokenProvider** rappresenta un provider di token di sicurezza con metodi factory incorporati che restituiscono alcuni provider di token noti. Nell'esempio seguente viene usato **SharedSecretTokenProvider** per contenere le credenziali del segreto condiviso e per gestire l'acquisizione dei token appropriati dal Servizio di controllo di accesso. Il nome e la chiave sono quelli ottenuti dal portale come descritto nella sezione precedente.

In primo luogo, fare riferimento o copiare nel progetto client il codice del contratto **IProblemSolver** del servizio.

Sostituire quindi il codice nel metodo **Main** del client, anche in questo caso sostituendo il testo del segnaposto con lo spazio dei nomi e la chiave del servizio del bus di servizio:

    var cf = new ChannelFactory<IProblemSolverChannel>(
        new NetTcpRelayBinding(), 
        new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver")));

    cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
                { TokenProvider = TokenProvider.CreateSharedSecretTokenProvider("owner","**key**") });
     
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

È ora possibile compilare il client e il servizio ed eseguirli (con il servizio per primo), in modo che il client chiami il servizio e visualizzi "9". È possibile eseguire il client e il server in computer diversi, persino in reti diverse, senza riscontrare problemi di comunicazione. Il codice client può inoltre essere eseguito nel cloud o in locale.

####Come configurare un client nel file App.config####

È anche possibile configurare il client usando il file App.config. In questo caso il codice del client è il seguente:

    var cf = new ChannelFactory<IProblemSolverChannel>("solver");
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

Le definizioni dell'endpoint vengono spostate nel file App.config. Il frammento di codice seguente, che è identico al codice riportato in precedenza, deve essere inserito direttamente sotto l'elemento **system.serviceModel**. Anche in questo caso, come in precedenza, è necessario sostituire i segnaposto con lo spazio dei nomi e la chiave del servizio del bus di servizio.

    <client>
        <endpoint name="solver" contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://**namespace**.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </client>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

#Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base del servizio **Inoltro** del bus di servizio, usare i collegamenti seguenti per altre informazioni.

-   Creazione di un servizio: [Creazione di un servizio per il bus di servizio][].
-   Creazione del client: [Creazione di un'applicazione client del bus di servizio][].
-   Esempi del bus di servizio: scaricarli dagli [esempi di Azure][].

  [Creare uno spazio dei nomi servizio]: #create_namespace
  [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi]: #obtain_credentials
  [Ottenere il pacchetto NuGet del bus di servizio]: #get_nuget_package
  [Procedura: Esporre e usare un servizio Web SOAP con TCP tramite il bus di servizio]: #how_soap
  [Portale di gestione di Azure]: http://manage.windowsazure.com
  [Autenticazione della firma di accesso condiviso con il bus di servizio]: http://msdn.microsoft.com/it-it/library/dn170477.aspx
  [Creazione di un servizio per il bus di servizio]: http://msdn.microsoft.com/it-it/library/windowsazure/ee173564.aspx
  [Creazione di un'applicazione client del bus di servizio]: http://msdn.microsoft.com/it-it/library/windowsazure/ee173543.aspx
  [Esempi di Azure]: http://code.msdn.microsoft.com/windowsazure

<!--HONumber=35.2-->
