---
title: Configurare un Host Docker con VirtualBox | Documentazione Microsoft
description: Istruzioni passo a passo per configurare un&quot;istanza di Docker predefinita usando Docker Machine e VirtualBox
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 0b1335a2-7720-42a8-8260-4e06fc00c9f6
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: e9465afb560a73d74f853c19094b3ee75b8c470c


---
# <a name="configure-a-docker-host-with-virtualbox"></a>Configurare un Host Docker con VirtualBox
## <a name="overview"></a>Panoramica
Questo articolo consente di configurare un'istanza di Docker predefinita usando Docker Machine e VirtualBox. Se si utilizza il [Docker per Windows beta](http://beta.docker.com/), questa configurazione non è necessaria.

## <a name="prerequisites"></a>Prerequisiti
È necessario installare gli strumenti seguenti.

* [Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Configurare il client Docker con Windows PowerShell
Per configurare un client Docker, è sufficiente aprire Windows PowerShell ed eseguire la procedura seguente:

1. Creare un'istanza di host docker predefinita.
   
    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
2. Verificare che l'istanza predefinita sia configurata e in esecuzione. Verrà visualizzata un'istanza denominata `default' in esecuzione.
   
    ```PowerShell
    docker-machine ls 
    ```
   
    ![docker-machine ls output][0]
3. Impostare l'host corrente come predefinito e configurare la shell.
   
    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```
4. Visualizzare i contenitori del Docker attivi. L'elenco deve essere vuoto.
   
    ```PowerShell
    docker ps
    ```
   
    ![docker ps output][1]

> [!NOTE]
> Ogni volta che si riavvia il computer di sviluppo, sarà necessario riavviare l'host Docker locale.
> A questo scopo, al prompt dei comandi eseguire questo comando: `docker-machine start default`.
> 
> 

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png



<!--HONumber=Feb17_HO3-->


