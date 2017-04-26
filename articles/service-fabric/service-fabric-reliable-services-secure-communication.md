---
title: Proteggere le comunicazioni per i servizi in Azure Service Fabric | Microsoft Docs
description: Panoramica relativa a come proteggere le comunicazioni per servizi Reliable Services in esecuzione in un cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 01/05/2017
ms.author: suchia
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 89eca322062f5e5c51142b2cc9e758004583cb3f
ms.lasthandoff: 04/03/2017


---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Proteggere le comunicazioni per i servizi in Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# su Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java su Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

La sicurezza è uno degli aspetti essenziali delle comunicazioni. Il framework di applicazioni di Reliable Services offre alcuni stack e strumenti predefiniti che è possibile usare per migliorare la sicurezza. In questo articolo viene illustrato come migliorare la sicurezza quando si usa la comunicazione remota dei servizi e lo stack di comunicazione di Windows Communication Foundation (WCF).

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Proteggere un servizio quando si usa la comunicazione remota dei servizi
Verrà usato un [esempio](service-fabric-reliable-services-communication-remoting.md) esistente che spiega come configurare la comunicazione remota per Reliable Services. Per proteggere un servizio quando si usa la comunicazione remota, seguire questa procedura:

1. Creare un'interfaccia, `IHelloWorldStateful`, che definisce i metodi che saranno disponibili per la Remote Procedure Call del servizio. Il servizio userà il metodo `FabricTransportServiceRemotingListener`, dichiarato nello spazio dei nomi `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime`. Si tratta di un’implementazione `ICommunicationListener` che fornisce funzionalità di accesso remoto.

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
2. Aggiungere le impostazioni del listener e le credenziali di sicurezza.

    Assicurarsi che il certificato da usare per proteggere le comunicazioni dei servizi sia installato in tutti i nodi del cluster. Esistono due modi per specificare le impostazioni del listener e le credenziali di sicurezza:

   1. Specificarle direttamente nel codice del servizio:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportListenerSettings listenerSettings = new FabricTransportListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
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
   2. Specificarle tramite un [pacchetto di configurazione](service-fabric-application-model.md):

       Aggiungere una sezione `TransportSettings` nel file settings.xml.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateful".-->
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

       In questo caso il metodo `CreateServiceReplicaListeners` avrà un aspetto analogo al seguente:

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

        Se si aggiunge una sezione `TransportSettings` al file settings.xml senza alcun prefisso, `FabricTransportListenerSettings` caricherà tutte le impostazioni da questa sezione per impostazione predefinita.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        In questo caso il metodo `CreateServiceReplicaListeners` avrà un aspetto analogo al seguente:

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
3. Quando si chiamano metodi in un servizio protetto tramite lo stack di comunicazione remota, anziché usare la classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` per creare un proxy del servizio, usare `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Specificare `FabricTransportSettings`, che contiene `SecurityCredentials`.

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

    Se il codice client viene eseguito come parte del servizio, è possibile caricare `FabricTransportSettings` dal file settings.xml. Creare una sezione TransportSettings simile al codice del servizio, come illustrato in precedenza. Apportare le modifiche seguenti al codice client:

    ```csharp

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportSettings.LoadFrom("TransportSettingsPrefix")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se il client non è in esecuzione come parte di un servizio, è possibile creare un file client_name.settings.xml nello stesso percorso del file client_name.exe. Creare quindi una sezione TransportSettings in tale file.

    Analogamente al servizio, se nel file client settings.xml/client_name.settings.xml si aggiunge una sezione `TransportSettings` senza alcun prefisso, `FabricTransportSettings` caricherà tutte le impostazioni da questa sezione per impostazione predefinita.

    In questo caso il codice precedente risulta ancora più semplice:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## <a name="help-secure-a-service-when-youre-using-a-wcf-based-communication-stack"></a>Proteggere un servizio quando si usa uno stack di comunicazione basato su WCF
Verrà usato un [esempio](service-fabric-reliable-services-communication-wcf.md) esistente che spiega come configurare uno stack di comunicazione basato su WCF per Reliable Services. Per proteggere un sevizio quando si usa uno stack di comunicazione basato su WCF, seguire questa procedura:

1. Per il servizio, è necessario proteggere il listener di comunicazione WCF (`WcfCommunicationListener`) che viene creato. A tale scopo, modificare il metodo `CreateServiceReplicaListeners` .

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
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
2. Nel client la classe `WcfCommunicationClient` creata nell' [esempio](service-fabric-reliable-services-communication-wcf.md) precedente rimane invariata. È però necessario eseguire l'override del metodo `CreateClientAsync` di `WcfCommunicationClientFactory`:

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
            // These credentials will be used by the clients created by
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

    Usare il metodo `SecureWcfCommunicationClientFactory` per creare un client di comunicazione WCF (`WcfCommunicationClient`). Usare il client per richiamare i metodi del servizio.

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

## <a name="next-steps"></a>Passaggi successivi
* [Web API con OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)

