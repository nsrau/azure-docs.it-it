---
title: Docker Compose di Azure Service Fabric (anteprima) | Microsoft Docs
description: "Azure Service Fabric accetta il formato Docker Compose per orchestrare più facilmente i contenitori esistenti. Il supporto per Docker Compose è attualmente in anteprima."
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
ms.openlocfilehash: cbe7e338ac7da9dc7e8d03cb1bb07a69af70cb17
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="use-docker-volume-plug-ins-and-logging-drivers-in-your-container"></a>Usare il plug-in di volume e driver di registrazione Docker per il contenitore
Azure Service Fabric supporta la specifica di [plug-in di volume Docker](https://docs.docker.com/engine/extend/plugins_volume/) e i [driver di registrazione Docker](https://docs.docker.com/engine/admin/logging/overview/) per il servizio di contenitore. È possibile rendere persistenti i dati in [File di Azure](https://azure.microsoft.com/services/storage/files/) quando il contenitore viene spostato o riavviato in un host diverso.

Attualmente sono supportati solo driver di volume per contenitori Linux. Se si usano contenitori di Windows, è possibile mappare un volume a una [condivisione SMB3](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/) File di Azure senza un driver di volume. Per questo mapping è necessario aggiornare le macchine virtuali nel cluster alla versione 1709 di Windows Server.


## <a name="install-the-docker-volumelogging-driver"></a>Installare i driver di volume/registrazione Docker

Il driver di volume/registrazione Docker non è installato nel computer, è possibile installarlo manualmente usando i protocolli RDP/SSH. È possibile eseguire l'installazione con questi protocolli tramite un [script di avvio del set di scalabilità di macchine virtuali](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) o uno [script SetupEntryPoint](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

Un esempio di script per installare il [driver di volume Docker per Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) è riportato di seguito:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

> [!NOTE]
> Windows Server 2016 Datacenter non supporta il mapping dei montaggi SMB nei contenitori ([supportati solo in Windows Server versione 1709](https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/container-storage)). Questa limitazione impedisce il mapping del volume della rete e l'uso dei driver di volume di File di Azure precedenti alla versione 1709. 
>   


## <a name="specify-the-plug-in-or-driver-in-the-manifest"></a>Specificare il plug-in o il driver nel manifesto
I plug-in sono specificati nel manifesto dell'applicazione, come illustrato di seguito:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
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
        <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
        </Volume>
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

Il tag **Source** per l'elemento **Volume** fa riferimento alla cartella di origine. La cartella di origine può essere una cartella nella macchina virtuale che ospita i contenitori o un archivio remoto persistente. Il tag **Destination** è il percorso in cui viene eseguito il mapping di **Source** all'interno del contenitore in esecuzione. La destinazione non può quindi essere un percorso già esistente all'interno del contenitore.

I parametri dell'applicazione sono supportati per i volumi, come illustrato nel frammento di manifesto precedente (cercare `MyStoreVar` per un esempio d'uso).

Quando si specifica un plug-in di volume, Service Fabric crea automaticamente il volume usando i parametri specificati. Il tag **Source** è il nome del volume e il tag **Driver** specifica il plug-in del driver del volume. Le opzioni possono essere specificate tramite il tag **DriverOption** come indicato di seguito:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
Se viene specificato un driver di log Docker, è necessario distribuire gli agenti o i contenitori per gestire i registri nel cluster. Il tag **DriverOption** può essere usato per specificare le opzioni per il driver di log.

## <a name="next-steps"></a>Passaggi successivi
Per distribuire i contenitori in un cluster di Service Fabric, fare riferimento all'articolo [Creare la prima applicazione contenitore di Service Fabric in Windows](service-fabric-deploy-container.md).
