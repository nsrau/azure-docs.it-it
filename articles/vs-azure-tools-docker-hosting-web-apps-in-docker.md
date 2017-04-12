---
title: Distribuire un contenitore Docker ASP.NET Core Linux in un host Docker remoto | Documentazione Microsoft
description: Informazioni su come usare Visual Studio Tools per Docker per distribuire un&quot;app Web ASP.NET Core in un contenitore Docker in esecuzione in una macchina virtuale Linux host Docker di Azure
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 7169b6f2d9738abd9651120be96bb1cf209ea85d
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>Distribuire un contenitore ASP.NET in un host Docker remoto
## <a name="overview"></a>Panoramica
Docker è un motore contenitore leggero, simile in qualche modo a una macchina virtuale, che è possibile usare per ospitare applicazioni e servizi.
In questa esercitazione viene usata l'estensione [Visual Studio 2015 Tools per Docker](http://aka.ms/DockerToolsForVS) per distribuire un'app ASP.NET Core in un host Docker in Azure con PowerShell.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione sarà necessario svolgere le operazioni seguenti:

* Creare una VM host Docker in Azure, come descritto in [Come usare Docker Machine in Azure](virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Installare [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129)
* [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)
* Installare [Visual Studio 2015 Tools per Docker - Preview](http://aka.ms/DockerToolsForVS)

## <a name="1-create-an-aspnet-core-web-app"></a>1. Creare un'app Web ASP.NET Core
La procedura seguente illustra la creazione di un'app ASP.NET Core di base che verrà usata in questa esercitazione.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Aggiungere il supporto di Docker
[!INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3. Usare lo script di PowerShell DockerTask.ps1
1. Aprire un prompt dei comandi di PowerShell nella directory radice del progetto. 
   
   ```
   PS C:\Src\WebApplication1>
   ```
2. La convalida dell'host remoto è in esecuzione. Dovrebbe essere visualizzato lo stato = Running 
   
   ```
   docker-machine ls
   NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
   MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
   ```
   
   > [!NOTE]
   > Se si usa la versione beta di Docker, l'host non viene elencato qui.
   > 
   > 
3. Compilare l'app con il parametro -Build
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
   ```  
   
   > [!NOTE]
   > Se si usa la versione beta di Docker, omettere l'argomento -Machine
   > 
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
   > ```  
   > 
   > 
4. Eseguire l'app usando il parametro -Run
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
   ```
   
   > [!NOTE]
   > Se si usa la versione beta di Docker, omettere l'argomento -Machine
   > 
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
   > ```
   > 
   > 
   
   Una volta completata l'esecuzione di Docker, i risultati visualizzati dovrebbero essere simili ai seguenti:
   
   ![Visualizzare l'app][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png

