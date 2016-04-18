<properties
   pageTitle="Comunicazioni sicure per i servizi in Service Fabric | Microsoft Azure"
   description="Panoramica relativa a come proteggere le comunicazioni per servizi Reliable Services in esecuzione in un cluster di Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="punewa"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="03/22/2016"
   ms.author="punewa"/>

# Comunicazioni sicure per i servizi in Service Fabric
La sicurezza è uno degli aspetti essenziali delle comunicazioni. Il framework applicazione dei servizi Reliable Services fornisce alcuni stack e strumenti predefiniti che è possibile usare per le comunicazioni. Questo articolo illustra come configurare la sicurezza usando le comunicazioni remote del servizio e lo stack di comunicazione WCF.

## Protezione del servizio in caso di uso delle comunicazioni remote
Per configurare la protezione delle comunicazioni remote per un servizio, seguire questa procedura:

1. Usare un [esempio](service-fabric-reliable-services-communication-remoting.md) precedente, che illustra come configurare comunicazioni remote affidabili per i servizi. Iniziare con un'interfaccia `IHelloWorldStateful` che definisce i metodi che saranno disponibili per una chiamata di procedura remota nel servizio. Il servizio userà un valore `FabricTransportServiceRemotingListener`, dichiarato nello spazio dei nomi `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime`. Si tratta di un’implementazione`ICommunicationListener` che fornisce funzionalità di accesso remoto.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```

2. Aggiunta di impostazioni del listener e delle credenziali di sicurezza.

    Il certificato da usare per proteggere le comunicazioni dei servizi deve essere installato in tutti i nodi del cluster. È possibile fornire le impostazioni del listener e le credenziali di sicurezza in due modi.

    1. Direttamente nel codice del servizio.

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            FabricTransportListenerSettings listnerSettings = new FabricTransportListenerSettings
            {
                MaxMessageSize = 10000000,
                SecurityCredentials = GetSecurityCredentials()
            };
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(context,this,listnerSettings))
            };
        }

        private static SecurityCredentials GetSecurityCredentials()
        {
            // Provide certificate details.
            var x509Credentials = new X509Credentials
            {
                FindType = X509FindType.FindByThumbprint,
                FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
                StoreLocation = StoreLocation.LocalMachine,
                StoreName = "My",
                ProtectionLevel = ProtectionLevel.EncryptAndSign
            };
            x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
            return x509Credentials;
        }
        ```
    2. Con un [pacchetto di configurazione](service-fabric-application-model.md)

        Aggiungere una sezione `TransportSettings` nel file settings.xml.

        ```xml
        <!--Section name should always end with "TransportSettings"-->
        <!--Here we are using a prefix "HelloWorldStateful"-->
        <Section Name="HelloWorldStatefulTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509" />
            <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
            <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
            <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
            <Parameter Name="CertificateStoreName" Value="My" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
            <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
        </Section>
        ```

        In questo caso il metodo `CreateServiceReplicaListeners` avrà un aspetto analogo al seguente.

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(
                        context,this,FabricTransportListenerSettings.LoadFrom("HelloWorldStateful")))
            };
        }
        ```

         Se si aggiunge una sezione `TransportSettings` al file settings.xml senza alcun prefisso, `FabricTransportListenerSettings` caricherà per impostazione predefinita tutte le impostazioni da questa sezione.

         ```xml
         <!--"TransportSettings" section without any prefix-->
         <Section Name="TransportSettings">
             ...
         </Section>
         ```
         In questo caso il metodo `CreateServiceReplicaListeners` avrà un aspetto analogo al seguente.

         ```csharp
         protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
         {
             return new[]
             {
                 return new[]{
                         new ServiceReplicaListener(
                             (context) => new FabricTransportServiceRemotingListener(context,this))};
             };
         }
         ```

3. Quando si chiamano metodi su un servizio sicuro usando lo stack remoto, invece di usare la classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` per creare un proxy del servizio, usare `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory` e passare il valore `FabricTransportSettings` che include `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");

    FabricTransportSettings transportSettings = new FabricTransportSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se il codice del client è in esecuzione come parte di un servizio, è possibile caricare il valore `FabricTransportSettings` dal file settings.xml. Creare una sezione TransportSettings simile al codice del servizio, come illustrato in precedenza. Apportare le modifiche seguenti al codice del client.

    ```csharp

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportSettings.LoadFrom("TransportSettingsPrefix")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se il client non è in esecuzione come parte di un servizio, è possibile creare un file client\_name.settings.xml nello stesso percorso del file client\_name.exe e quindi creare una sezione TransportSettings in tale file.

    Analogamente al servizio, se nel file settings.xml/client\_name.settings.xml si aggiunge una sezione `TransportSettings` senza *prefisso*, `FabricTransportSettings` caricherà per impostazione predefinita tutte le impostazioni da questa sezione.

    In questo caso il codice precedente risulta ancora più semplice.

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## Protezione del servizio in caso di uso dello stack di comunicazione basato su WCF

Verrà usato un [esempio](service-fabric-reliable-services-communication-wcf.md) precedente, che illustra come configurare comunicazioni basate su WCF per servizi Reliable Services e come estenderle per renderle sicure.

1. Per il servizio è necessario creare un `WcfCommunicationListener`, che è sicuro. Sarà quindi necessario modificare il metodo `CreateServiceReplicaListeners`.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListner = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example we will be using NetTcpBinding
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the servicehost credentials.
        wcfCommunicationListner.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListner;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
    
2. Nel client la classe `WcfCommunicationClient` creata nell'[esempio](service-fabric-reliable-services-communication-wcf.md) precedente rimane invariata. È però necessario eseguire l'override del metodo `CreateClientAsync` di `WcfCommunicationClientFactory`.

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by the
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Usare `SecureWcfCommunicationClientFactory` per creare un `WcfCommunicationClient`. Usare il client per richiamare i metodi del servizio.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## Passaggi successivi

* [Web API con OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)

<!---HONumber=AcomDC_0330_2016-->