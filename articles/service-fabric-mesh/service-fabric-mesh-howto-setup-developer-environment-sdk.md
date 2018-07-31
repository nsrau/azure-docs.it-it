---
title: Configurare un ambiente di sviluppo Windows per app Service Fabric Mesh | Microsoft Docs
description: È possibile configurare l'ambiente di sviluppo Windows per creare un'applicazione Service Fabric Mesh e distribuirla in Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/20/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 589bef1894a3bee1e6974a0ea2516200fae2891f
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185544"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-applications"></a>Configurare l'ambiente di sviluppo Windows per creare applicazioni Service Fabric

Per compilare ed eseguire app di Azure Service Fabric nel computer di sviluppo Windows, installare il runtime di Service Fabric, l'SDK e gli strumenti.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Versioni del sistema operativo supportate

Per lo sviluppo, sono supportati i sistemi operativi seguenti:

* Windows 10 (Enterprise, Professional o Education)
* Windows Server 2016

## <a name="visual-studio"></a>Visual Studio

Per distribuire app di Service Fabric, è richiesto Visual Studio 2017. [Installare la versione 15.6.0][download-visual-studio] o successiva e abilitare i carichi di lavoro seguenti:

- Sviluppo Web e ASP.NET
- Sviluppo di Azure
 
## <a name="windows-10---install-docker"></a>Windows 10 - Installare Docker

Scaricare e installare la versione più recente di [Docker Community Edition per Windows][download-docker] per supportare le app di Service Fabric in contenitori usate da Service Fabric Mesh.

Durante l'installazione, selezionare **Use Windows containers instead of Linux containers** (Usa contenitori Windows invece di contenitori Linux) quando viene richiesto. Se Hyper-V non è abilitato nel computer, l'installazione di Docker offrirà di abilitarlo. Fare clic su **OK** per farlo se viene richiesto.

## <a name="windows-server-2016---install-hyper-v-and-docker"></a>Windows Server 2016 - Installare Hyper-V e Docker

**Installare Hyper-V**

Prima di tutto, aprire PowerShell come amministratore ed eseguire il comando seguente per installare Hyper-V e riavviare il computer. Per altre informazioni, vedere [Docker Enterprise Edition for Windows Server][download-docker-server] (Docker Enterprise Edition per Windows Server).

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

**Installare Docker**

Aprire PowerShell come amministratore ed eseguire il comando seguente per installare Docker:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Riavviare il computer.

## <a name="sdk-and-tools"></a>SDK e strumenti

Installare il runtime di Service Fabric Mesh, l'SDK e gli strumenti nell'ordine seguente.

1. Installare [Service Fabric Mesh SDK][download-sdkmesh] usando Installazione guidata piattaforma Web. Verranno installati anche Microsoft Azure Service Fabric SDK e il runtime.
2. Installare l'[estensione Strumenti di Service Fabric per Visual Studio (anteprima)][download-tools] da Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Creare un cluster

Se si usa Visual Studio, è possibile ignorare questa sezione perché Visual Studio creerà un cluster locale se non è disponibile.

Per ottimizzare le prestazioni del debug quando si creano e si eseguono app Service Fabric, è consigliabile creare un cluster di sviluppo locale a nodo singolo. Questo cluster deve essere in esecuzione ogni volta che si distribuisce o si esegue il debug di un progetto Service Fabric Mesh.

Prima di poter creare un cluster, Docker **deve** essere in esecuzione. Per testare se Docker è in esecuzione, aprire una finestra del terminale ed eseguire `docker ps` per vedere se si verifica un errore. Se la risposta non indica un errore, Docker è in esecuzione ed è possibile creare un cluster.

Dopo aver installato il runtime, l'SDK e gli strumenti di Visual Studio, creare un cluster di sviluppo.

1. Chiudere la finestra di PowerShell.
2. Aprire una nuova finestra di PowerShell con privilegi elevati come amministratore. Questo passaggio è necessario per caricare i moduli di Service Fabric installati di recente.
3. Eseguire il comando di PowerShell seguente per creare un cluster di sviluppo:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster -UseMachineName
    ```

4. Per avviare lo strumento di gestione cluster locale, eseguire il comando seguente di PowerShell:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```

È ora possibile creare applicazioni Service Fabric Mesh.

## <a name="next-steps"></a>Passaggi successivi

Leggere l'esercitazione sulla [creazione di un'app Azure Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.ServiceFabricMesh
[download-visual-studio]: https://www.visualstudio.com/downloads/