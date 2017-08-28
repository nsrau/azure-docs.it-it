---
title: Anteprima di Docker Compose di Azure Service Fabric | Microsoft Docs
description: "Azure Service Fabric accetta il formato Docker Compose per semplificare l'orchestrazione di contenitori esistenti usando Service Fabric. Questo supporto è attualmente disponibile in versione di anteprima."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: b12ef95add6347621f7d4865fac46568f91a1e12
ms.contentlocale: it-it
ms.lasthandoff: 08/16/2017

---

# <a name="specifying-volume-plugins-and-logging-drivers-for-your-container"></a>Specifica di plug-in di volume e driver di registrazione per il contenitore

Service Fabric supporta la specifica di [plug-in di volume Docker](https://docs.docker.com/engine/extend/plugins_volume/) e i [driver di registrazione Docker](https://docs.docker.com/engine/admin/logging/overview/) per il servizio di contenitore. I plug-in sono specificati nel manifesto dell'applicazione, come illustrato nel manifesto seguente:


```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="d:\myfolder" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myexternalvolume" Destination="c:\testmountlocation3" Driver="sf" IsReadOnly="true"></Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

Nell'esempio precedente, il tag `Source` per `Volume` fa riferimento alla cartella di origine. La cartella di origine potrebbe essere una cartella nella macchina virtuale che ospita i contenitori o un archivio remoto persistente. Il tag `Destination` è il percorso in cui viene eseguito il mapping di `Source` all'interno del contenitore in esecuzione. 

Quando si specifica un plug-in di un volume, Service Fabric crea automaticamente il volume usando i parametri specificati. Il tag `Source` è il nome del volume e il tag `Driver` specifica il plug-in del driver del volume. Le opzioni possono essere specificate usando il tag `DriverOption`, come illustrato nel frammento di codice seguente:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azurefile" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Se viene specificato un driver di registro Docker, è necessario distribuire gli agenti o i contenitori per gestire i registri nel cluster.  Il tag `DriverOption` può essere usato anche per specificare le opzioni del driver di log.

Vedere gli articoli seguenti per distribuire i contenitori a un cluster Service Fabric:


[Distribuire un contenitore in Service Fabric](service-fabric-deploy-container.md)


