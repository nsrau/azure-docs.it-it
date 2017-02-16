---
title: Panoramica della configurazione di FabricTransport per Reliable Actors di Azure Service Fabric | Documentazione Microsoft
description: Informazioni sulla configurazione delle impostazioni di comunicazione di Azure Service Fabric Actors.
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
ms.sourcegitcommit: a395d0653a72883b1901824ba25d40f673276d3f
ms.openlocfilehash: 0b43ba67ea30772054c513b47c31642baaa8b9e7


---
# <a name="configuring-fabrictransport-settings-for-reliable-actors"></a>Configurazione delle impostazioni di FabricTransport per Reliable Actors

Di seguito sono elencate le impostazioni configurabili dall'utente in [FabrictTansportSettings](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.fabrictransport.common.fabrictransportsettings)

È possibile modificare la configurazione predefinita di FabricTransport nei modi seguenti.

1.  Uso dell'attributo dell'assembly [FabricTransportActorRemotingProvider](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute?redirectedfrom=MSDN#microsoft_servicefabric_actors_remoting_fabrictransport_fabrictransportactorremotingproviderattribute).

  Questo attributo deve essere applicato nel client Actor e nell'assembly del servizio Actor.
  L'esempio seguente illustra come modificare il valore delle impostazioni OperationTimeout di FabricTransport.

  ```csharp
     using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

   Il secondo esempio modifica i valori di MaxMessageSize e OperationTimeoutInSeconds di FabricTransport.

    ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
    ```

2. Uso di un [pacchetto di configurazione](service-fabric-application-model.md):

  * Configurazione delle impostazioni di FabricTransport per il servizio Actor

    Aggiungere una sezione TransportSettings nel file settings.xml.

    * Nome sezione: per impostazione predefinita, il codice Actor cerca il nome sezione "&lt;NomeActor&gt;TransportSettings". Se non viene trovato, cerca il nome sezione "TransportSettings".

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

  * Configurazione delle impostazioni di FabricTransport per l'assembly del client Actor

    Se il client non è in esecuzione come parte di un servizio, è possibile creare un file XML "&lt;Nome EXE client&gt;.settings.xml" nello stesso percorso in cui si trova il file EXE client. Aggiungere quindi una sezione TransportSettings in tale file. Il nome sezione deve essere "TransportSettings".

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



<!--HONumber=Nov16_HO4-->


