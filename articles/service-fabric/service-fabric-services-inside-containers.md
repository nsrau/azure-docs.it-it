---
title: Distribuire in un contenitore i servizi di Azure Service Fabric in Windows
description: Informazioni su come distribuire in un contenitore i servizi Reliable Services di Service Fabric e Reliable Actors in Windows.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: anmolah
editor: roroutra
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/23/2018
ms.author: aljo, anmola
ms.openlocfilehash: caed77234646654d151b64d2c80b7231342f6d8c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050468"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Distribuire in un contenitore Reliable Services di Service Fabric e Reliable Actors in Windows

Service Fabric supporta la distribuzione in un contenitore di microservizi di Service Fabric (servizi di base di Reliable Services e Reliable Actors). Per altre informazioni, vedere [Service Fabric e contenitori](service-fabric-containers-overview.md).

Questo documento fornisce linee guida per eseguire il servizio all'interno di un contenitore di Windows.

> [!NOTE]
> Attualmente questa funzionalità funziona solo per Windows. Per eseguire contenitori, il cluster deve essere in esecuzione in Windows Server 2016 with Containers.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Procedura per distribuire in un contenitore l'applicazione Service Fabric

1. Aprire l'applicazione di Service Fabric in Visual Studio.

2. Aggiungere la classe [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) al progetto. Il codice in questa classe è un metodo di supporto per caricare correttamente i file binari di runtime di Service Fabric all'interno dell'applicazione quando è in esecuzione all'interno di un contenitore.

3. Per ogni pacchetto di codice che si desidera distribuire in un contenitore, inizializzare il caricatore nel punto di ingresso del programma. Aggiungere il costruttore statico illustrato nel frammento di codice seguente al file del punto di ingresso del programma.

   ```csharp
   namespace MyApplication
   {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
   ```

4. Compilare e inserire in un [pacchetto](service-fabric-package-apps.md#Package-App) il progetto. Per costruire e creare un pacchetto, fare clic con il pulsante destro sul progetto dell'applicazione in Esplora soluzioni e scegliere il comando **Pacchetto**.

5. Per ogni pacchetto di codice che è necessario distribuire in un contenitore, eseguire lo script di PowerShell [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). L'utilizzo è il seguente:

    .NET completo
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $applicationExeName = 'Name of the Code package executable.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
      ```
    .NET Core
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $dotnetCoreDllName = 'Name of the Code package dotnet Core Dll.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -DotnetCoreDllName $dotnetCoreDllName
      ```
      Lo script crea una cartella con gli elementi di Docker in $dockerPackageOutputDirectoryPath. Modificare il Dockerfile generato per `expose` qualsiasi porta, eseguire gli script di installazione e così via. in base alle esigenze.

6. È quindi necessario [compilare](service-fabric-get-started-containers.md#Build-Containers) ed [eseguire il push](service-fabric-get-started-containers.md#Push-Containers) del pacchetto del contenitore Docker nel repository.

7. Modificare ApplicationManifest.xml e ServiceManifest.xml per aggiungere l'immagine del contenitore, le informazioni sul repository, l'autenticazione del registro di sistema e il mapping tra porta e host. Per modificare i manifesti, vedere [Creare la prima applicazione contenitore di Service Fabric in Windows](service-fabric-get-started-containers.md). La definizione del pacchetto di codice nel manifesto del servizio deve essere sostituita con l'immagine del contenitore corrispondente. Verificare di modificare il punto di ingresso in un tipo ContainerHost.

   ```xml
   <!-- Code package is your service executable. -->
   <CodePackage Name="Code" Version="1.0.0">
   <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
   </EntryPoint>
   <!-- Pass environment variables to your container: -->
   </CodePackage>
   ```

8. Aggiungere il mapping da porta a host per la replica e un endpoint di servizio. Poiché entrambe queste porte vengono assegnate in fase di esecuzione di Service Fabric, il ContainerPort è impostato su zero per usare la porta assegnata per il mapping.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Per configurare la modalità di isolamento del contenitore, consultare [Configurare modalità di isolamento]( https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode). Windows supporta due modalità di isolamento per i contenitori: la modalità processo e la modalità Hyper-V. I frammenti seguenti indicano come viene specificata la modalità di isolamento nel file manifesto dell'applicazione.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```
   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```

> [!NOTE] 
> Per impostazione predefinita, le applicazioni di Service Fabric hanno accesso al runtime di Service Fabric, sotto forma di un endpoint che accetta le richieste specifiche dell'applicazione. Provare a disabilitare l'accesso quando l'applicazione include codice non attendibile. Per altre informazioni, vedi [le procedure consigliate in Service Fabric](service-fabric-best-practices-security.md#platform-isolation). Per disabilitare l'accesso al runtime di Service Fabric, aggiungere l'impostazione seguente nella sezione dei criteri del manifesto dell'applicazione corrispondente al manifesto del servizio importato, come indicato di seguito:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Per testare questa applicazione, è necessario distribuirla in un cluster che esegue la versione 5.7 o versioni successive. Per le versioni runtime 6.1 o precedenti, è necessario modificare e aggiornare le impostazioni del cluster per abilitare questa funzionalità in anteprima. Seguire la procedura in questo [articolo](service-fabric-cluster-fabric-settings.md) per aggiungere l'impostazione illustrata di seguito.
    ```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
    ```

11. Successivamente, [distribuire](service-fabric-deploy-remove-applications.md) il pacchetto di applicazioni modificato in questo cluster.

Ora si dovrebbe avere un'applicazione di Service Fabric distribuita nei contenitori che esegue il cluster.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sull'esecuzione di [contenitori in Service Fabric](service-fabric-get-started-containers.md).
* Altre informazioni sul [ciclo di vita dell'applicazione](service-fabric-application-lifecycle.md) di Service Fabric.
