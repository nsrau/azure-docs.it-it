---
title: Proteggere le comunicazioni remote per i servizi in Azure Service Fabric con C# | Microsoft Docs
description: Informazioni su come proteggere le comunicazioni remote per Reliable Services C# in esecuzione in un cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: chackdan
ms.openlocfilehash: 193df34a092d9feea3e0cf370fe38543395dad92
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871721"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Proteggere le comunicazioni remote in un servizio C#
> [!div class="op_single_selector"]
> * [C# su Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java su Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

La sicurezza è uno degli aspetti essenziali della comunicazione. Il framework di applicazioni di Reliable Services offre alcuni stack e strumenti predefiniti che è possibile usare per migliorare la sicurezza. Questo articolo discute come migliorare la sicurezza quando si usa la comunicazione remota in un servizio C#. Verrà usato un [esempio](service-fabric-reliable-services-communication-remoting.md) esistente che spiega come configurare la comunicazione remota per Reliable Services in C#. 

Per proteggere un servizio quando si usa la comunicazione remota con i servizi C#, seguire questa procedura:

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

    Assicurarsi che il certificato da usare per proteggere le comunicazioni dei servizi sia installato in tutti i nodi del cluster. 
    
    > [!NOTE]
    > Sui nodi Linux, il certificato deve essere presente come file formattati PEM nella directory */var/lib/sfcerts*. Per altre informazioni, vedere [Percorso e formato dei certificati X.509 nei nodi Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Esistono due modi per specificare le impostazioni del listener e le credenziali di sicurezza:

   1. Specificarle direttamente nel codice del servizio:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
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
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. Specificarle tramite un [pacchetto di configurazione](service-fabric-application-and-service-manifests.md):

       Aggiungere una sezione denominata `TransportSettings` nel file settings.xml.

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
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
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        Se si aggiunge una sezione `TransportSettings` al file settings.xml, `FabricTransportRemotingListenerSettings` caricherà tutte le impostazioni da questa sezione per impostazione predefinita.

        ```xml
        <!--"TransportSettings" section .-->
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
3. Quando si chiamano metodi in un servizio protetto tramite lo stack di comunicazione remota, anziché usare la classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` per creare un proxy del servizio, usare `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Specificare `FabricTransportRemotingSettings`, che contiene `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se il codice client viene eseguito come parte del servizio, è possibile caricare `FabricTransportRemotingSettings` dal file settings.xml. Creare una sezione HelloWorldClientTransportSettings simile al codice del servizio, come illustrato in precedenza. Apportare le modifiche seguenti al codice client:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se il client non è in esecuzione come parte di un servizio, è possibile creare un file client_name.settings.xml nello stesso percorso del file client_name.exe. Creare quindi una sezione TransportSettings in tale file.

    Come con il servizio, se si aggiunge una sezione `TransportSettings` nel file settings.xml/client_name.settings.xml del client, `FabricTransportRemotingSettings` carica tutte le impostazioni da questa sezione per impostazione predefinita.

    In questo caso il codice precedente risulta ancora più semplice:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


Come passaggio successivo, vedere [API Web con OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md).
