<properties
	pageTitle="Come usare il servizio Inoltro del bus di servizio con .NET | Microsoft Azure"
	description="Informazioni sull'uso del servizio di inoltro del bus di servizio di Azure per connettere due applicazioni ospitate in posizioni diverse."
	services="service-bus-relay"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus-relay"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="sethm"/>


# Come usare il servizio Inoltro del bus di servizio di Azure

Questo articolo descrive come usare il servizio Inoltro del bus di servizio. Negli esempi, scritti in C#, viene usata l'API di Windows Communication Foundation (WCF) con le estensioni contenute nell'assembly del bus di servizio. Per altre informazioni sul servizio Inoltro del bus di servizio, vedere la panoramica in [Messaggistica inoltrata del bus di servizio](service-bus-relay-overview.md).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Informazioni sul servizio Inoltro del bus di servizio

Il [servizio *Inoltro* del bus di servizio](service-bus-relay-overview.md) consente di creare applicazioni ibride che vengono eseguite sia in un data center di Azure che nell'ambiente aziendale locale. A tale scopo, consente di esporre in modo sicuro nel cloud servizi WCF (Windows Communication Foundation) che risiedono in una rete aziendale sul cloud pubblico, senza dover aprire una connessione firewall o richiedere modifiche di notevole impatto a un'infrastruttura di rete aziendale.

![Concetti relativi all'inoltro](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Il servizio Inoltro del bus di servizio consente di ospitare servizi WCF nell'ambiente aziendale esistente. È quindi possibile delegare al servizio del bus di servizio in esecuzione in Azure l'ascolto delle sessioni in ingresso e delle richieste a questi servizi WCF. In questo modo è possibile esporre tali servizi al codice dell'applicazione in esecuzione in Azure oppure ad ambienti destinati a personale che accede da dispositivi mobili o a partner che accedono tramite Extranet. Il bus di servizio consente di controllare in modo sicuro ed estremamente dettagliato gli utenti autorizzati ad accedere ai servizi. È uno strumento efficace e sicuro per esporre dati e funzionalità dell'applicazione dalle soluzioni aziendali esistenti e di sfruttarle dal cloud.

Questo articolo illustra come usare il servizio Inoltro del bus di servizio per creare un servizio Web WCF, esposto tramite un'associazione di canale TCP che implementa una conversazione protetta tra due parti.

## Creare uno spazio dei nomi del servizio

Per iniziare a usare il servizio Inoltro del bus di servizio in Azure, è necessario creare prima di tutto uno spazio dei nomi. Uno spazio dei nomi fornisce un contenitore di ambito per fare riferimento alle risorse del bus di servizio all'interno dell'applicazione.

Per creare uno spazio dei nomi del servizio:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## Ottenere il pacchetto NuGet del bus di servizio

Il [pacchetto NuGet del bus di servizio](https://www.nuget.org/packages/WindowsAzure.ServiceBus) è il modo più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con tutte le dipendenze del bus di servizio. Per installare il pacchetto NuGet nel progetto, eseguire queste operazioni:

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti**, quindi fare clic su **Manage NuGet Packages**.
2.  Cercare "Bus di servizio" e selezionare la voce **Bus di servizio di Microsoft Azure**. Fare clic su **Installa** per completare l'installazione e quindi chiudere la finestra di dialogo successiva:

	![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## Esporre e usare un servizio Web SOAP con TCP tramite il bus di servizio

Per esporre un servizio Web SOAP WCF esistente per l'utilizzo esterno, è necessario apportare modifiche alle associazioni e agli indirizzi del servizio. A seconda di come sono stati installati e configurati i servizi WCF, potrebbe essere necessario modificare il file di configurazione o il codice. Si noti che WCF consente di usare più endpoint di rete con lo stesso servizio, pertanto è possibile continuare a mantenere gli endpoint interni esistenti pur aggiungendo endpoint del bus di servizio per l'accesso esterno.

In questa attività si creerà un semplice servizio WCF al quale verrà aggiunto un listener del bus di servizio. Per questo esercizio si presuppone una certa conoscenza di Visual Studio, pertanto non è inclusa una spiegazione dettagliata della procedura di creazione di un progetto, ma l'attenzione è rivolta principalmente al codice.

Prima di iniziare, completare questa procedura per configurare l'ambiente:

1.  In Visual Studio creare un'applicazione console contenente due progetti, "Client" e "Service", all'interno della soluzione.
2.  Aggiungere il pacchetto NuGet di Bus di servizio di Microsoft Azure a entrambi i progetti. Questo pacchetto aggiunge ai progetti tutti i riferimenti ad assembly necessari.

### Come creare il servizio

Creare innanzitutto il servizio stesso. Tutti i servizi WCF sono costituiti da almeno tre parti distinte:

-   Definizione di un contratto che descrive i messaggi da scambiare e le operazioni da richiamare.
-   Implementazione del suddetto contratto.
-   Host che ospita il servizio WCF ed espone diversi endpoint.

Negli esempi di codice di questa sezione vengono trattati singolarmente tutti questi componenti.

Il contratto consente di definire una singola operazione, `AddNumbers`, che somma due numeri e restituisce il risultato. L'interfaccia `IProblemSolverChannel` consente al client di gestire più facilmente la durata del proxy. La creazione di tale interfaccia rientra tra le procedure consigliate. È opportuno inserire questa definizione del contratto in un file separato in modo da potervi fare riferimento da entrambi i progetti "Client" e "Service", ma è comunque possibile copiare il codice in entrambi i progetti.

```
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Una volta definito il contratto, l'implementazione è davvero semplice.

```
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### Configurare un host del servizio a livello di codice

Una volta definiti il contratto e l'implementazione, è ora possibile ospitare il servizio. Per l'hosting viene utilizzato un oggetto [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/azure/system.servicemodel.servicehost.aspx), che si occupa di gestire le istanze del servizio e ospita gli endpoint che sono in ascolto dei messaggi. Il codice seguente configura il servizio sia con un endpoint locale normale che con un endpoint del bus di servizio per illustrare l'aspetto di endpoint interni ed esterni affiancati. Sostituire la stringa *namespace* con il nome dello spazio dei nomi e *yourKey* con la chiave di firma di accesso condiviso ottenuta nel passaggio di configurazione precedente.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

Nell'esempio vengono creati due endpoint inclusi nella stessa implementazione del contratto: Uno è locale e uno viene proiettato tramite il bus di servizio. Le differenze principali tra i due endpoint sono costituite dalle associazioni, [NetTcpBinding](https://msdn.microsoft.com/library/azure/system.servicemodel.nettcpbinding.aspx) per l'endpoint locale e [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) per l'endpoint e gli indirizzi del bus di servizio. L'endpoint locale è dotato di un indirizzo di rete locale con una porta distinta. L'endpoint del bus di servizio include un indirizzo composto dalla stringa `sb`, dal nome dello spazio dei nomi e dal percorso "solver". Si ottiene in tal modo l'URI `sb://[serviceNamespace].servicebus.windows.net/solver`, che identifica l'endpoint del servizio come endpoint TCP del bus di servizio con un nome DNS esterno completo. Se si inserisce il codice sostituendo i segnaposto nella funzione `Main` dell'applicazione **Service**, si otterrà un servizio funzionante. Se si vuole che il servizio sia in ascolto esclusivamente sul bus di servizio, rimuovere la dichiarazione dell'endpoint locale.

### Come configurare un host del servizio nel file App.config

È anche possibile configurare l'host usando il file App.config. L'esempio seguente in questo caso visualizza il codice del servizio di hosting.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Le definizioni dell'endpoint vengono spostate nel file App.config. Il pacchetto NuGet ha già aggiunto al file App.config una serie di definizioni, che sono le estensioni di configurazione necessarie per il bus di servizio. L'esempio seguente, che è l'esatto equivalente di quello riportato in precedenza, deve essere inserito direttamente sotto l'elemento **system.serviceModel**. In questo esempio di codice si presuppone che il nome dello spazio dei nomi del progetto C# sia **Service**. Sostituire i segnaposto con la chiave di firma di accesso condiviso e lo spazio dei nomi del servizio del bus di servizio.

```
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Dopo aver apportato queste modifiche, il servizio viene avviato come in precedenza, ma con due endpoint dinamici, uno locale e l'altro in ascolto nel cloud.

### Creare il client

#### Configurare un client a livello di codice

Per utilizzare il servizio, è possibile costruire un client WCF mediante un oggetto [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx). Il bus di servizio usa un modello di sicurezza basato sui token implementato tramite la firma di accesso condiviso. La classe [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) rappresenta un provider di token di sicurezza con metodi factory incorporati che restituiscono alcuni provider di token noti. L'esempio seguente usa il metodo [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) per gestire l'acquisizione del token di firma di accesso condiviso appropriato. Il nome e la chiave sono quelli ottenuti dal portale come descritto nella sezione precedente.

In primo luogo, fare riferimento o copiare nel progetto client il codice del contratto `IProblemSolver` del servizio.

Sostituire quindi il codice nel metodo `Main` del client, anche in questo caso sostituendo il testo del segnaposto con la chiave di firma di accesso condiviso e lo spazio dei nomi del bus di servizio.

```
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

È ora possibile compilare il client e il servizio ed eseguirli (con il servizio per primo), in modo che il client chiami il servizio e visualizzi **9**. È possibile eseguire il client e il server in computer diversi, persino in reti diverse, senza riscontrare problemi di comunicazione. Il codice client può inoltre essere eseguito nel cloud o in locale.

#### Configurare un client nel file App.config

Il codice seguente illustra come configurare il client usando il file App.config.

```
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Le definizioni dell'endpoint vengono spostate nel file App.config. L'esempio seguente, che è identico al codice riportato in precedenza, deve essere inserito direttamente sotto l'elemento **system.serviceModel**. Anche in questo caso, come in precedenza, è necessario sostituire i segnaposto con la chiave di firma di accesso condiviso e lo spazio dei nomi del bus di servizio.

```
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base del servizio di inoltro del bus di servizio, usare i collegamenti seguenti per ottenere altre informazioni.

- [Panoramica della messaggistica inoltrata del bus di servizio](service-bus-relay-overview.md)
- [Panoramica dell'architettura del bus di servizio di Azure](../service-bus/service-bus-fundamentals-hybrid-solutions.md)
- Scaricare esempi del bus di servizio da [esempi di Azure][] o vedere la [panoramica degli esempi del bus di servizio][].

  [Shared Access Signature Authentication with Service Bus]: ../service-bus/service-bus-shared-access-signature-authentication.md
  [esempi di Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [panoramica degli esempi del bus di servizio]: ../service-bus/service-bus-samples.md

<!---HONumber=AcomDC_0928_2016-->