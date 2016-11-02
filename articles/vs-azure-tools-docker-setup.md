<properties
   pageTitle="Configurare un Host Docker con VirtualBox | Microsoft Azure"
   description="Istruzioni passo a passo per configurare un'istanza di Docker predefinita usando Docker Machine e VirtualBox"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" /> 
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" /> 

# Configurare un Host Docker con VirtualBox

## Overview
Questo articolo consente di configurare un'istanza di Docker predefinita usando Docker Machine e VirtualBox. Se si utilizza il [Docker per Windows beta](http://beta.docker.com/), questa configurazione non è necessaria.

## Prerequisiti
È necessario installare gli strumenti seguenti.

- [Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox)

## Configurare il client Docker con Windows PowerShell

Per configurare un client Docker, è sufficiente aprire Windows PowerShell ed eseguire la procedura seguente:

1. Creare un'istanza di host docker predefinita.

    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
 
1. Verificare che l'istanza predefinita sia configurata e in esecuzione. Verrà visualizzata un'istanza denominata `default' in esecuzione.

    ```PowerShell
	docker-machine ls 
    ```
		
	![docker-machine ls output][0]
 
1. Impostare l'host corrente come predefinito e configurare la shell.

    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```

1. Visualizzare i contenitori del Docker attivi. L'elenco deve essere vuoto.

    ```PowerShell
	docker ps
    ```

	![docker ps output][1]
 
> [AZURE.NOTE] Ogni volta che si riavvia il computer di sviluppo, sarà necessario riavviare l'host Docker locale. A questo scopo, al prompt dei comandi eseguire questo comando: `docker-machine start default`.

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png

<!---HONumber=AcomDC_0921_2016-->