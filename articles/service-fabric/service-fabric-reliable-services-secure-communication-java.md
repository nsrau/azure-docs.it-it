---
title: Proteggere le comunicazioni remote per i servizi in Azure Service Fabric con Java | Microsoft Docs
description: Informazioni su come proteggere le comunicazioni remote per Reliable Services Java in esecuzione in un cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: b465ab602a14285f8cf40b24ce1dfa9c763fecb8
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666997"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Proteggere le comunicazioni remote per Reliable Services in un servizio Java
> [!div class="op_single_selector"]
> * [C# su Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java su Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

La sicurezza è uno degli aspetti essenziali della comunicazione. Il framework di applicazioni di Reliable Services offre alcuni stack e strumenti predefiniti che è possibile usare per migliorare la sicurezza. Questo articolo discute come migliorare la sicurezza quando si usa la comunicazione remota in un servizio Java. Verrà usato un [esempio](service-fabric-reliable-services-communication-remoting-java.md) esistente che spiega come configurare la comunicazione remota per Reliable Services in Java. 

Per proteggere un servizio quando si usa la comunicazione remota con i servizi Java, seguire questa procedura:

1. Creare un'interfaccia, `HelloWorldStateless`, che definisce i metodi che saranno disponibili per la Remote Procedure Call del servizio. Il servizio userà il metodo `FabricTransportServiceRemotingListener`, dichiarato nel pacchetto `microsoft.serviceFabric.services.remoting.fabricTransport.runtime`. Si tratta di un’implementazione `CommunicationListener` che fornisce funzionalità di accesso remoto.

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. Aggiungere le impostazioni del listener e le credenziali di sicurezza.

    Assicurarsi che il certificato da usare per proteggere le comunicazioni dei servizi sia installato in tutti i nodi del cluster. Per i servizi in esecuzione su Linux, il certificato deve essere disponibile come file formattato PEM; un file `.pem` che contiene il certificato e la chiave privata o un file `.crt` che contiene il certificato e un file `.key` che contiene la chiave privata. Per altre informazioni, vedere [Percorso e formato dei certificati X.509 nei nodi Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Esistono due modi per specificare le impostazioni del listener e le credenziali di sicurezza:

   1. Specificarle tramite un [pacchetto di configurazione](service-fabric-application-and-service-manifests.md):

       Aggiungere una sezione denominata `TransportSettings` nel file settings.xml.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       In questo caso il metodo `createServiceInstanceListeners` avrà un aspetto analogo al seguente:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Se si aggiunge una sezione `TransportSettings` al file settings.xml senza alcun prefisso, `FabricTransportListenerSettings` caricherà tutte le impostazioni da questa sezione per impostazione predefinita.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        In questo caso il metodo `CreateServiceInstanceListeners` avrà un aspetto analogo al seguente:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Quando si chiamano metodi in un servizio protetto tramite lo stack di comunicazione remota, anziché usare la classe `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` per creare un proxy del servizio, usare `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Se il codice client viene eseguito come parte del servizio, è possibile caricare `FabricTransportSettings` dal file settings.xml. Creare una sezione TransportSettings simile al codice del servizio, come illustrato in precedenza. Apportare le modifiche seguenti al codice client:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
