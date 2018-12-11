---
title: Configurare un ambiente di sviluppo Windows per compilare app Service Fabric Mesh | Microsoft Docs
description: È possibile configurare l'ambiente di sviluppo Windows per creare un'applicazione Service Fabric Mesh e distribuirla in Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 11/29/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: bec0b9a7e34f1577f80a99f5380795c479c04bc8
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890467"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Configurare l'ambiente di sviluppo Windows per compilare app Service Fabric Mesh

Per compilare ed eseguire applicazioni Azure Service Fabric Mesh nel computer di sviluppo Windows, installare il runtime di Service Fabric Mesh, l'SDK e gli strumenti.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Versioni del sistema operativo supportate

Per lo sviluppo, sono supportati i sistemi operativi seguenti:

* Windows 10 (Enterprise, Professional o Education)
* Windows Server 2016

## <a name="visual-studio"></a>Visual Studio

Per distribuire applicazioni Service Fabric Mesh, è richiesto Visual Studio 2017. [Installare la versione 15.6.0][download-visual-studio] o successiva e abilitare i carichi di lavoro seguenti:

* Sviluppo Web e ASP.NET
* Sviluppo di Azure

## <a name="install-docker"></a>Installare Docker

#### <a name="windows-10"></a>Windows 10

Scaricare e installare la versione più recente di [Docker Community Edition per Windows][download-docker] per supportare le app di Service Fabric in contenitori usate da Service Fabric Mesh.

Durante l'installazione, selezionare **Use Windows containers instead of Linux containers** (Usa contenitori Windows invece di contenitori Linux) quando viene richiesto.

Se Hyper-V non è abilitato nel computer, l'installazione di Docker offrirà di abilitarlo. Fare clic su **OK** per farlo se viene richiesto.

#### <a name="windows-server-2016"></a>Windows Server 2016

Se il ruolo Hyper-V non è abilitato, aprire PowerShell come amministratore ed eseguire il comando seguente per abilitare Hyper-V, quindi riavviare il computer. Per altre informazioni, vedere [Docker Enterprise Edition for Windows Server][download-docker-server] (Docker Enterprise Edition per Windows Server).

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Riavviare il computer.

Aprire PowerShell come amministratore ed eseguire il comando seguente per installare Docker:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK e strumenti

Installare il runtime di Service Fabric Mesh, l'SDK e gli strumenti nell'ordine seguente.

1. Installare [Service Fabric Mesh SDK][download-sdkmesh] usando Installazione guidata piattaforma Web. Verranno installati anche Microsoft Azure Service Fabric SDK e il runtime.
2. Installare l'[estensione Strumenti di Service Fabric Mesh per Visual Studio (anteprima)][download-tools] da Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Creare un cluster

> [!IMPORTANT]
> Prima di poter creare un cluster, Docker **deve** essere in esecuzione.
> Per testare se Docker è in esecuzione, aprire una finestra del terminale ed eseguire `docker ps` per vedere se si verifica un errore. Se la risposta non indica un errore, Docker è in esecuzione ed è possibile creare un cluster.

Se si usa Visual Studio, è possibile ignorare questa sezione perché Visual Studio creerà un cluster locale se non è disponibile.

Per ottimizzare le prestazioni del debug quando si creano e si eseguono app Service Fabric, è consigliabile creare un cluster di sviluppo locale a nodo singolo. Questo cluster deve essere in esecuzione ogni volta che si distribuisce o si esegue il debug di un progetto Service Fabric Mesh.

Dopo aver installato il runtime, gli SDK e gli strumenti di Visual Studio e aver avviato il Docker, creare un cluster di sviluppo.

1. Chiudere la finestra di PowerShell.
2. Aprire una nuova finestra di PowerShell con privilegi elevati come amministratore. Questo passaggio è necessario per caricare i moduli di Service Fabric installati di recente.
3. Eseguire il comando di PowerShell seguente per creare un cluster di sviluppo:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. Per avviare lo strumento di gestione cluster locale, eseguire il comando seguente di PowerShell:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. Una volta che lo strumento di gestione cluster del servizio è in esecuzione (compare nella barra delle applicazioni), fare clic su di esso con il pulsante destro del mouse e scegliere **Start Local Cluster** (Avvia cluster locale).

![Figura 1 - Avvio del cluster locale](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

È ora possibile creare applicazioni Service Fabric Mesh.

## <a name="next-steps"></a>Passaggi successivi

Leggere l'esercitazione sulla [creazione di un'app Azure Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md).

Risposte a [domande comuni](service-fabric-mesh-faq.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/