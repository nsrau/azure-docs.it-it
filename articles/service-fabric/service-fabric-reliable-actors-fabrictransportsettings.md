---
title: Modificare le impostazioni di FabricTransport nei microservizi di Azure | Documentazione Microsoft
description: Informazioni sulla configurazione delle impostazioni di comunicazione degli attori di Service Fabric di Azure.
services: Service-Fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: 
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: suchia
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 4cbca6e496135a312bf4704dd0989f45dcccfc00
ms.lasthandoff: 04/14/2017


---
# <a name="configure-fabrictransport-settings-for-reliable-actors"></a>Configurare le impostazioni di FabricTransport per Reliable Actors

Ecco le impostazioni che possono essere configurate:

- C#: [FabricTansportSettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.fabrictransport.common.fabrictransportsettings)
- Java: [FabricTransportRemotingSettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.fabrictransport._fabric_transport_remoting_settings)

È possibile modificare la configurazione predefinita di FabricTransport nei modi seguenti.

## <a name="assembly-attribute"></a>Attributo assembly

L'attributo [FabricTransportActorRemotingProvider](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute?redirectedfrom=MSDN#microsoft_servicefabric_actors_remoting_fabrictransport_fabrictransportactorremotingproviderattribute) deve essere applicato negli assembly del client attore e del servizio attore.

L'esempio seguente illustra come modificare il valore predefinito delle impostazioni OperationTimeout di FabricTransport:

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

L'esempio seguente illustra come modificare i valori predefiniti delle impostazioni MaxMessageSize e OperationTimeoutInSeconds di FabricTransport:

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
   ```

## <a name="config-package"></a>Pacchetto di configurazione

È possibile usare un [pacchetto di configurazione](service-fabric-application-model.md) per modificare la configurazione predefinita.

### <a name="configure-fabrictransport-settings-for-the-actor-service"></a>Configurare le impostazioni di FabricTransport per il servizio attore

Aggiungere una sezione TransportSettings nel file settings.xml.

Per impostazione predefinita, il codice attore cerca il nome sezione "&lt;ActorName&gt;TransportSettings". Se non viene trovato, cerca il SectionName "TransportSettings".

  ```xml
  <Section Name="MyActorServiceTransportSettings">
       <Parameter Name="MaxMessageSize" Value="10000000" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
       <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
       <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
       <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
       <Parameter Name="CertificateStoreName" Value="My" />
       <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
       <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
   </Section>
  ```

### <a name="configure-fabrictransport-settings-for-the-actor-client-assembly"></a>Configurare le impostazioni di FabricTransport per l'assembly del client attore

Se il client non è in esecuzione come parte di un servizio, è possibile creare un file XML "&lt;Nome EXE client&gt;.settings.xml" nello stesso percorso in cui si trova il file client .exe. Aggiungere quindi una sezione TransportSettings in tale file. SectionName deve essere "TransportSettings".

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <Section Name="TransportSettings">
      <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
      <Parameter Name="CertificateFindValue" Value="78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
      <Parameter Name="CertificateStoreName" Value="My" />
      <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
    </Section>
  </Settings>
   ```

