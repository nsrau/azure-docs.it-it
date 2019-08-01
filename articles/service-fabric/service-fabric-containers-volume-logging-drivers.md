---
title: Driver di volume per File di Azure di Service Fabric (anteprima) | Microsoft Docs
description: Service Fabric supporta l'uso di File di Azure per eseguire il backup di volumi dal contenitore. È attualmente in fase di anteprima.
services: service-fabric
author: athinanthny
manager: chackdan
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.topic: conceptual
ms.date: 6/10/2018
ms.author: atsenthi
ms.openlocfilehash: 36a3bb385064009f25a585ea299558d54714fc27
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599696"
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Driver di volume per File di Azure di Service Fabric (anteprima)
Il plug-in di volume di File di Azure è un [plug-in di volume Docker](https://docs.docker.com/engine/extend/plugins_volume/) che fornisce volumi basati su [File di Azure](https:///azure/storage/files/storage-files-introduction) per contenitori Docker. Questo plug-in di volume Docker viene offerto come pacchetto di applicazione di Service Fabric distribuibile nei cluster di Service Fabric, Lo scopo è fornire volumi basati su File di Azure per altre applicazioni contenitore Service Fabric distribuite nel cluster.

> [!NOTE]
> La versione 6.5.516.9494 del plug-in del volume File di Azure è una versione di anteprima disponibile in questo documento. In quanto versione di anteprima, l'uso in ambienti di produzione **non** è supportato.
>

## <a name="prerequisites"></a>Prerequisiti
* La versione Windows del plug-in di volume di File di Azure può essere usata solo in [Windows Server versione 1709](https:///windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 versione 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) o sistemi operativi successivi.

* La versione Linux del plug-in di volume di File di Azure può essere usata in tutte le versioni del sistema operativo supportate da Service Fabric.

* Il plug-in di volume di File di Azure funziona solo con Service Fabric 6.2 e versioni successive.

* Per creare una condivisione file per l'applicazione contenitore di Service Fabric da usare come volume, seguire le istruzioni riportate nella [documentazione di File di Azure](https:///azure/storage/files/storage-how-to-create-file-share).

* È necessario che sia installato [Powershell con il modulo Service Fabric](https:///azure/service-fabric/service-fabric-get-started) o [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli).

* Se si usano contenitori di Hyper-V, è necessario aggiungere i frammenti di codice seguenti nella sezione ClusterManifest (cluster locale) o fabricSettings nel modello di Azure Resource Manager (cluster di Azure) o ClusterConfig. JSON (cluster autonomo).

In ClusterManifest è necessario aggiungere il codice seguente nella sezione Hosting. In questo esempio il nome del volume è **sfazurefile** e la porta su cui è in ascolto nel cluster è **19100**. Sostituirli con i valori corretti per il cluster.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

Nella sezione fabricSettings del modello di Azure Resource Manager (per le distribuzioni di Azure) o ClusterConfig. JSON (per le distribuzioni autonome) è necessario aggiungere il frammento di codice seguente. Anche in questo caso, sostituire i valori di nome e porta del volume con quelli personalizzati.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```


## <a name="deploy-the-service-fabric-azure-files-application"></a>Distribuire l'applicazione File di Azure di Service Fabric

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Uso di Azure Resource Manager tramite lo script di PowerShell specificato (scelta consigliata)

Se il cluster è basato su Azure, si consiglia di distribuire le applicazioni usando il modello di risorse dell'applicazione Azure Resource Manager per facilitarne l'uso e di spostarsi verso il modello di gestione dell'infrastruttura come codice. Questo approccio elimina la necessità di tenere traccia della versione dell'app per il driver del volume File di Azure. Consente inoltre di gestire modelli di Azure Resource Manager distinti per ogni sistema operativo supportato. Lo script presuppone che venga distribuita la versione più recente dell'applicazione File di Azure e accetta i parametri per tipo di sistema operativo, ID sottoscrizione cluster e gruppo di risorse. È possibile scaricare lo script dal [sito di download Service Fabric](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip). Si noti che in questo modo viene impostato automaticamente il ListenPort, ovvero la porta su cui il plug-in File di Azure volume è in ascolto delle richieste provenienti dal daemon Docker, fino a 19100. È possibile modificarlo aggiungendo il parametro denominato "listenPort". Verificare che la porta non sia in conflitto con altre porte utilizzate dal cluster o dalle applicazioni.
 

Azure Resource Manager comando di distribuzione per Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Comando Azure Resource Manager distribuzione per Linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Una volta eseguito correttamente lo script, è possibile passare alla [sezione configurazione dell'applicazione.](https:////azure/service-fabric/service-fabric-containers-volume-logging-drivers#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Distribuzione manuale per i cluster autonomi

L'applicazione Service Fabric che fornisce i volumi per i contenitori può essere scaricata dal [sito di download di Service Fabric](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.516.9494.zip). L'applicazione può essere distribuita nel cluster tramite [PowerShell](https:///azure/service-fabric/service-fabric-deploy-remove-applications), l'[interfaccia della riga di comando](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle-sfctl) o le [API di FabricClient](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient).

1. Utilizzando la riga di comando, passare alla directory radice del pacchetto dell'applicazione scaricato.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Copiare quindi il pacchetto dell'applicazione nell'archivio immagini con i valori appropriati per [ApplicationPackagePath] e [ImageStoreConnectionString]:

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

4. Creare l'applicazione, prestando attenzione al valore del parametro dell'applicazione **listenport** . Questo valore è la porta su cui il plug-in del volume File di Azure è in ascolto delle richieste provenienti dal daemon docker. Verificare che la porta fornita all'applicazione corrisponda a quella di VolumePluginPorts in ClusterManifest e non sia in conflitto con altre porte utilizzate dal cluster o dalle applicazioni.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.516.9494  -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.516.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> Windows Server 2016 Datacenter non supporta il mapping dei montaggi SMB nei contenitori ([supportati solo in Windows Server versione 1709](https:///virtualization/windowscontainers/manage-containers/container-storage)). Questa limitazione impedisce il mapping del volume della rete e l'uso dei driver di volume di File di Azure precedenti alla versione 1709.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>Distribuire l'applicazione in un cluster di sviluppo locale
Seguire i passaggi 1-3 della versione [precedente.](https:////azure/service-fabric/service-fabric-containers-volume-logging-drivers#manual-deployment-for-standalone-clusters)

 Il numero predefinito di istanze del servizio per l'applicazione del plug-in di volume di File di Azure è -1, corrispondente alla distribuzione di un'istanza del servizio in ogni nodo del cluster. Quando si distribuisce l'applicazione del plug-in di volume di File di Azure in un cluster di sviluppo locale, tuttavia, il numero di istanze del servizio specificato dovrà essere 1. A questo scopo è possibile usare il parametro **InstanceCount** dell'applicazione. Pertanto, il comando per la creazione dell'applicazione del plug-in del volume File di Azure in un cluster di sviluppo locale è:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.516.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.516.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Configurare le applicazioni per l'uso del volume
Il frammento di codice seguente mostra come è possibile specificare un volume basato su File di Azure nel file manifesto dell'applicazione. L'elemento di specifico interesse è il tag **Volume**:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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
                <DriverOption Name="storageAccountFQDN" Value="" />
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

Il nome del driver per il plug-in di volume di File di Azure è **sfazurefile**. Questo valore è impostato per l'attributo **driver** dell'elemento tag del **volume** nel manifesto dell'applicazione.

Nel tag **volume** del frammento di codice precedente, il plug-in del volume file di Azure richiede gli attributi seguenti:
- **Source**: corrisponde al nome del volume. L'utente può scegliere qualsiasi nome per il volume.
- **Destination (destinazione** ): questo attributo è il percorso a cui è stato eseguito il mapping del volume all'interno del contenitore in esecuzione. La destinazione non può quindi essere una posizione già esistente nel contenitore.

Come illustrato negli elementi **DriverOption** nel frammento precedente, il plug-in di volume di File di Azure supporta le opzioni del driver seguenti.
- **shareName**: nome della condivisione file di File di Azure che rende disponibile il volume per il contenitore.
- **storageAccountName**: nome dell'account di archiviazione di Azure contenente la condivisione file di File di Azure.
- **storageAccountKey**: chiave di accesso dell'account di archiviazione di Azure contenente la condivisione file di File di Azure.
- **storageAccountFQDN**: nome di dominio associato all'account di archiviazione. Se storageAccountFQDN non è specificato, il nome di dominio è costituito dal suffisso predefinito (.file.core.windows.net) con il valore di storageAccountName.  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>Uso di un driver di volume o di registrazione personalizzato
Service Fabric consente anche di usare driver di [volume](https://docs.docker.com/engine/extend/plugins_volume/) o di [registrazione](https://docs.docker.com/engine/admin/logging/overview/) personalizzati. Se il driver di volume/registrazione Docker non è installato nel cluster, è possibile installarlo manualmente usando i protocolli RDP/SSH. È possibile eseguire l'installazione con questi protocolli tramite un [script di avvio del set di scalabilità di macchine virtuali](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) o uno [script SetupEntryPoint](https:///azure/service-fabric/service-fabric-application-model).

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

Quando si specifica un plug-in di volume, Service Fabric crea automaticamente il volume usando i parametri specificati. Il tag **Source** per l'elemento **Volume** è il nome del volume e il tag **Driver** specifica il plug-in del driver di volume. Il tag **Destination** è il percorso in cui viene eseguito il mapping di **Source** all'interno del contenitore in esecuzione. La destinazione non può quindi essere un percorso già esistente all'interno del contenitore. Le opzioni possono essere specificate tramite il tag **DriverOption** come indicato di seguito:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

I parametri dell'applicazione sono supportati per i volumi, come illustrato nel frammento di manifesto precedente (cercare `MyStorageVar` per un esempio d'uso).

Se viene specificato un driver di log Docker, è necessario distribuire gli agenti o i contenitori per gestire i registri nel cluster. Il tag **DriverOption** può essere usato per specificare le opzioni per il driver di log.

## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare esempi dei contenitori con il driver di volume, vedere gli [esempi di contenitori di Service Fabric](https://github.com/Azure-Samples/service-fabric-containers)
* Per distribuire i contenitori in un cluster di Service Fabric, vedere l'articolo su come [distribuire un contenitore in Service Fabric](service-fabric-deploy-container.md)
