---
title: Introduzione all'inoltro WCF di Inoltro di Azure in .NET | Documentazione Microsoft
description: Informazioni sull'uso degli inoltri WCF di Inoltro di Azure per connettere due applicazioni ospitate in posizioni diverse.
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 5493281a-c2e5-49f2-87ee-9d3ffb782c75
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: 1af1ac78398d65e6a87f0d24d6198f3dfbc82ffd
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="how-to-use-azure-relay-wcf-relays-with-net"></a>Come usare gli inoltri WCF di Inoltro di Azure con .NET
Questo articolo descrive come usare il servizio di inoltro di Azure. Negli esempi, scritti in C#, viene usata l'API di Windows Communication Foundation (WCF) con le estensioni contenute nell'assembly del bus di servizio. Per altre informazioni, vedere la [panoramica del servizio di inoltro di Azure](relay-what-is-it.md).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-wcf-relay"></a>Informazioni sull'inoltro WCF

Il servizio di [*inoltro WCF*](relay-what-is-it.md) di Azure consente di creare applicazioni ibride eseguite sia in un data center di Azure che nell'ambiente aziendale locale. Il servizio di inoltro facilita questo compito consentendo di esporre in modo sicuro nel cloud pubblico i servizi WCF (Windows Communication Foundation) che risiedono in una rete aziendale senza dover aprire una connessione firewall o richiedere modifiche di notevole impatto a un'infrastruttura di rete aziendale.

![Concetti sull'inoltro con WCF](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Il servizio di inoltro di Azure consente di ospitare servizi WCF nell'ambiente aziendale esistente. È quindi possibile delegare l'ascolto delle sessioni e delle richieste in ingresso per questi servizi WCF al servizio di inoltro in esecuzione in Azure. In questo modo è possibile esporre tali servizi al codice dell'applicazione in esecuzione in Azure oppure ad ambienti destinati a personale che accede da dispositivi mobili o a partner che accedono tramite Extranet. Il servizio di inoltro consente di controllare in modo sicuro ed estremamente dettagliato quali utenti possono accedere ai servizi. È uno strumento efficace e sicuro per esporre dati e funzionalità dell'applicazione dalle soluzioni aziendali esistenti e di sfruttarle dal cloud.

Questo articolo illustra come usare il servizio di inoltro di Azure per creare un servizio Web WCF, esposto con un'associazione di canale TCP, che implementa una conversazione sicura tra due parti.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Ottenere il pacchetto NuGet del bus di servizio
Il [pacchetto NuGet del bus di servizio](https://www.nuget.org/packages/WindowsAzure.ServiceBus) è il modo più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con tutte le dipendenze di tale servizio. Per installare il pacchetto NuGet nel progetto, eseguire queste operazioni:

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti** e scegliere **Gestisci pacchetti NuGet**.
2. Cercare "Bus di servizio" e selezionare la voce **Bus di servizio di Microsoft Azure** . Fare clic su **Installa** per completare l'installazione e alla fine chiudere la finestra di dialogo successiva:
   
   ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="expose-and-consume-a-soap-web-service-with-tcp"></a>Esporre e utilizzare un servizio Web SOAP con TCP
Per esporre un servizio Web SOAP WCF esistente per l'utilizzo esterno, è necessario apportare modifiche alle associazioni e agli indirizzi del servizio. A seconda di come sono stati installati e configurati i servizi WCF, potrebbe essere necessario modificare il file di configurazione o il codice. Si noti che WCF consente di usare più endpoint di rete con lo stesso servizio, quindi è possibile mantenere gli endpoint interni esistenti aggiungendo al tempo stesso endpoint di inoltro per l'accesso esterno.

In questa attività si crea un semplice servizio WCF e si aggiunge un listener di inoltro a tale servizio. Per questo esercizio si presuppone una certa conoscenza di Visual Studio, pertanto non è inclusa una spiegazione dettagliata della procedura di creazione di un progetto, ma l'attenzione è rivolta principalmente al codice.

Prima di iniziare, completare questa procedura per configurare l'ambiente:

1. In Visual Studio creare un'applicazione console contenente due progetti, "Client" e "Service", all'interno della soluzione.
2. Aggiungere il pacchetto NuGet del bus di servizio a entrambi i progetti. Questo pacchetto aggiunge ai progetti tutti i riferimenti ad assembly necessari.

### <a name="how-to-create-the-service"></a>Come creare il servizio
Creare innanzitutto il servizio stesso. Tutti i servizi WCF sono costituiti da almeno tre parti distinte:

* Definizione di un contratto che descrive i messaggi da scambiare e le operazioni da richiamare.
* Implementazione di tale contratto.
* Host che ospita il servizio WCF ed espone diversi endpoint.

Negli esempi di codice di questa sezione vengono trattati singolarmente tutti questi componenti.

Il contratto consente di definire una singola operazione, `AddNumbers`, che somma due numeri e restituisce il risultato. L'interfaccia `IProblemSolverChannel` consente al client di gestire più facilmente la durata del proxy. La creazione di tale interfaccia rientra tra le procedure consigliate. È opportuno inserire questa definizione del contratto in un file separato in modo da potervi fare riferimento da entrambi i progetti "Client" e "Service", ma è comunque possibile copiare il codice in entrambi i progetti.

```csharp
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Dopo che il contratto è stato definito, l'implementazione è la seguente:

```csharp
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Configurare un host del servizio a livello di codice
Una volta definiti il contratto e l'implementazione, è ora possibile ospitare il servizio. L'hosting viene eseguito all'interno di un oggetto [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/system.servicemodel.servicehost.aspx), che si occupa della gestione delle istanze del servizio e ospita gli endpoint che sono in ascolto dei messaggi. Il codice seguente configura il servizio sia con un normale endpoint locale che con un endpoint di inoltro per illustrare come si presentano gli endpoint interni ed esterni affiancati. Sostituire la stringa *namespace* con il nome dello spazio dei nomi e *yourKey* con la chiave SAS ottenuta nel passaggio di impostazione precedente.

```csharp
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

Nell'esempio vengono creati due endpoint inclusi nella stessa implementazione del contratto: Uno è locale e uno viene proiettato tramite il servizio di inoltro. Le differenze principali tra di essi sono costituite dalle associazioni: [NetTcpBinding](https://msdn.microsoft.com/library/system.servicemodel.nettcpbinding.aspx) per l'endpoint locale e [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding#microsoft_servicebus_nettcprelaybinding) per l'endpoint e gli indirizzi di inoltro. L'endpoint locale è dotato di un indirizzo di rete locale con una porta distinta. L'endpoint di inoltro ha un indirizzo costituito dalla stringa `sb`, dal nome dello spazio dei nomi e dal percorso "solver". Si ottiene così l'URI `sb://[serviceNamespace].servicebus.windows.net/solver`, che identifica l'endpoint del servizio come endpoint TCP (di inoltro) del bus di servizio con nome DNS esterno completo. Se si inserisce il codice sostituendo i segnaposto nella funzione `Main` dell'applicazione **Service**, si otterrà un servizio funzionante. Se si vuole che il servizio sia in ascolto esclusivamente sull'inoltro, rimuovere la dichiarazione dell'endpoint locale.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Come configurare un host del servizio nel file App.config
È anche possibile configurare l'host usando il file App.config. L'esempio seguente in questo caso visualizza il codice del servizio di hosting.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Le definizioni dell'endpoint vengono spostate nel file App.config. Il pacchetto NuGet ha già aggiunto al file App.config una serie di definizioni, che sono le estensioni di configurazione necessarie per il servizio di inoltro. L'esempio seguente, che è l'esatto equivalente di quello riportato in precedenza, deve essere inserito direttamente sotto l'elemento **system.serviceModel**. In questo esempio di codice si presuppone che il nome dello spazio dei nomi del progetto C# sia **Service**.
Sostituire i segnaposto con la chiave di firma di accesso condiviso e il nome dello spazio dei nomi dell'inoltro.

```xml
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://<namespaceName>.servicebus.windows.net/solver"
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

### <a name="create-the-client"></a>Creare il client
#### <a name="configure-a-client-programmatically"></a>Configurare un client a livello di codice
Per usare il servizio, è possibile costruire un client WCF tramite un oggetto [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx). Il bus di servizio usa un modello di sicurezza basato sui token implementato tramite la firma di accesso condiviso. La classe [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) rappresenta un provider di token di sicurezza con metodi factory incorporati che restituiscono alcuni provider di token noti. L'esempio seguente usa il metodo [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_) per gestire l'acquisizione del token SAS appropriato. Il nome e la chiave sono quelli ottenuti dal portale come descritto nella sezione precedente.

In primo luogo, fare riferimento o copiare nel progetto client il codice del contratto `IProblemSolver` del servizio.

Sostituire quindi il codice nel metodo `Main` del client, anche in questo caso sostituendo il testo segnaposto con la chiave di firma di accesso condiviso e lo spazio dei nomi dell'inoltro.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "<namespaceName>", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

È ora possibile compilare il client e il servizio ed eseguirli (con il servizio per primo), in modo che il client chiami il servizio e visualizzi **9**. È possibile eseguire il client e il server in computer diversi, persino in reti diverse, senza riscontrare problemi di comunicazione. Il codice client può inoltre essere eseguito nel cloud o in locale.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Configurare un client nel file App.config
Il codice seguente illustra come configurare il client usando il file App.config.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Le definizioni dell'endpoint vengono spostate nel file App.config. L'esempio seguente, che è identico al codice riportato in precedenza, deve essere inserito direttamente sotto l'elemento `<system.serviceModel>`. Anche in questo caso, come in precedenza, è necessario sostituire i segnaposto con la chiave di firma di accesso condiviso e lo spazio dei nomi dell'inoltro.

```xml
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://<namespaceName>.servicebus.windows.net/solver"
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

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base del servizio di inoltro di Azure, selezionare i collegamenti seguenti per altre informazioni.

* [Che cos'è il servizio di inoltro di Azure?](relay-what-is-it.md)
* [Panoramica dell'architettura del bus di servizio di Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* Scaricare esempi del bus di servizio da [Esempi di Azure][Azure samples] o vedere la [panoramica degli esempi del bus di servizio][overview of Service Bus samples].

[Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
[overview of Service Bus samples]: ../service-bus-messaging/service-bus-samples.md
