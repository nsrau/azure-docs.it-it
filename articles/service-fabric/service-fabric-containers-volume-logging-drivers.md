---
title: Driver di volume per File di Azure di Service Fabric (anteprima) | Microsoft Docs
description: Service Fabric supporta l'uso di File di Azure per eseguire il backup di volumi dal contenitore. È attualmente in fase di anteprima.
services: service-fabric
documentationcenter: other
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/30/2018
ms.author: subramar
ms.openlocfilehash: d4751182cac9b5b952ef9a9dd125408267c1f8d0
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Driver di volume per File di Azure di Service Fabric (anteprima)
Il plug-in di volume di File di Azure è un [plug-in di volume Docker](https://docs.docker.com/engine/extend/plugins_volume/) che fornisce volumi basati su [File di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) per contenitori Docker. Questo plug-in di volume Docker viene offerto come pacchetto di applicazione di Service Fabric distribuibile nei cluster di Service Fabric, con lo scopo di fornire volumi basati su File di Azure per altre applicazioni contenitore di Service Fabric distribuite nel cluster.

> [!NOTE]
> La versione 6.255.389.9494 del plug-in di volume di File di Azure è una versione di anteprima disponibile con questo documento. In quanto versione di anteprima, l'uso in ambienti di produzione **non** è supportato.
>

## <a name="prerequisites"></a>Prerequisiti
* La versione Windows del plug-in di volume di File di Azure può essere usata solo in [Windows Server versione 1709](https://docs.microsoft.com/en-us/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 versione 1709](https://docs.microsoft.com/en-us/windows/whats-new/whats-new-windows-10-version-1709) o sistemi operativi successivi. La versione Linux del plug-in di volume di File di Azure può essere usata in tutte le versioni del sistema operativo supportate da Service Fabric.

* Per creare una condivisione file per l'applicazione contenitore di Service Fabric da usare come volume, seguire le istruzioni riportate nella [documentazione di File di Azure](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-create-file-share).

* È necessario che sia installato [Powershell con il modulo Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started) o [SFCTL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cli).

## <a name="deploy-the-service-fabric-azure-files-application"></a>Distribuire l'applicazione File di Azure di Service Fabric

L'applicazione di Service Fabric che fornisce i volumi per i contenitori può essere scaricata dal [collegamento](https://aka.ms/sfvolume) seguente. L'applicazione può essere distribuita nel cluster tramite [PowerShell](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications), l'[interfaccia della riga di comando](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-lifecycle-sfctl) o le [API di FabricClient](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient).

1. Usando la riga di comando, passare alla directory radice del pacchetto dell'applicazione scaricato. 

    ```powershell 
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Copiare il pacchetto dell'applicazione nell'archivio immagini. Eseguire questo comando con i valori appropriati per [ApplicationPackagePath] e [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Registrare il tipo di applicazione

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Creare l'applicazione. Nel comando per la creazione dell'applicazione riportato di seguito, si noti il parametro **ListenPort** dell'applicazione. Il valore specificato per questo parametro dell'applicazione è la porta su cui il plug-in di volume di File di Azure resta in ascolto delle richieste dal daemon Docker. È importante assicurarsi che la porta specificata per l'applicazione non sia in conflitto con altre porte usate dal cluster o dalle applicazioni.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]

> Windows Server 2016 Datacenter non supporta il mapping dei montaggi SMB nei contenitori ([supportati solo in Windows Server versione 1709](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Questa limitazione impedisce il mapping del volume della rete e l'uso dei driver di volume di File di Azure precedenti alla versione 1709. 
>   

### <a name="deploy-the-application-on-a-local-development-cluster"></a>Distribuire l'applicazione in un cluster di sviluppo locale
Il numero predefinito di istanze del servizio per l'applicazione del plug-in di volume di File di Azure è -1, corrispondente alla distribuzione di un'istanza del servizio in ogni nodo del cluster. Quando si distribuisce l'applicazione del plug-in di volume di File di Azure in un cluster di sviluppo locale, tuttavia, il numero di istanze del servizio specificato dovrà essere 1. A questo scopo è possibile usare il parametro **InstanceCount** dell'applicazione. Il comando per distribuire l'applicazione del plug-in di volume di File di Azure in un cluster di sviluppo locale è quindi il seguente:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>Configurare le applicazioni per l'uso del volume
Il frammento seguente illustra come è possibile specificare un volume basato su File di Azure nel manifesto dell'applicazione. L'elemento di specifico interesse è il tag **Volume**:

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
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
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

Il nome del driver per il plug-in di volume di File di Azure è **sfazurefile**. Questo valore viene impostato per l'attributo **Driver** dell'elemento **Volume** nel manifesto dell'applicazione.

Nell'elemento **Volume** nel frammento precedente, il plug-in di volume di File di Azure richiede i tag seguenti. 
- **Source**: questo tag fa riferimento alla cartella di origine, che può essere una cartella nella VM che ospita i contenitori o un archivio remoto permanente.
- **Destination**: questo tag è la posizione a cui viene eseguito il mapping di **Source** nel contenitore in esecuzione. La destinazione non può quindi essere una posizione già esistente nel contenitore.

Come illustrato negli elementi **DriverOption** nel frammento precedente, il plug-in di volume di File di Azure supporta le opzioni del driver seguenti.

- **shareName**: nome della condivisione file di File di Azure che fornisce il volume per il contenitore
- **storageAccountName**: nome dell'account di archiviazione di Azure contenente la condivisione file di File di Azure
- **storageAccountKey**: chiave di accesso dell'account di archiviazione di Azure contenente la condivisione file di File di Azure

Tutte le opzioni del driver riportate sopra sono obbligatorie. 

## <a name="using-your-own-volume-or-logging-driver"></a>Uso di un driver di volume o di registrazione personalizzato
Service Fabric consente anche di usare driver di volume o di registrazione personalizzati. Se il driver di volume/registrazione Docker non è installato nel cluster, è possibile installarlo manualmente usando i protocolli RDP/SSH. È possibile eseguire l'installazione con questi protocolli tramite un [script di avvio del set di scalabilità di macchine virtuali](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) o uno [script SetupEntryPoint](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

Un esempio di script per installare il [driver di volume Docker per Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) è riportato di seguito:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Nelle applicazioni, per usare il driver di volume o di registrazione installato sarà necessario specificare i valori appropriati negli elementi **Volume** e **LogConfig** inclusi in **ContainerHostPolicies** nel manifesto dell'applicazione. 

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare esempi dei contenitori con il driver di volume, vedere gli [esempi di contenitori di Service Fabric](https://github.com/Azure-Samples/service-fabric-containers)
* Per distribuire i contenitori in un cluster di Service Fabric, vedere l'articolo su come [distribuire un contenitore in Service Fabric](service-fabric-deploy-container.md)


