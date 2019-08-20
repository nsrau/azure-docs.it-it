---
title: Distribuire un'applicazione in Kubernetes tramite Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Distribuire un microservizio nel servizio Azure Kubernetes con Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 39fb7658140a2eda948cd0dc0e58d71b0b9a053b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67710674"
---
# <a name="quickstart-develop-an-application-on-kubernetes-using-azure-dev-spaces"></a>Guida introduttiva: Sviluppare un'applicazione in Kubernetes tramite Azure Dev Spaces
In questa guida si apprenderà come:

- Configurare Azure Dev Spaces con un cluster Kubernetes gestito in Azure.
- Sviluppare ed eseguire codice nei contenitori tramite la riga di comando.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
- [L'interfaccia della riga di comando di Azure installata](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Creare un cluster del servizio Azure Kubernetes

È necessario creare un cluster del servizio Azure Kubernetes in un'[area supportata][supported-regions]. I comandi seguenti creano un gruppo di risorse denominato *MyResourceGroup* e un cluster del servizio Azure Kubernetes denominato *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Abilitare Azure Dev Spaces nel cluster del servizio Azure Kubernetes

Usare il comando `use-dev-spaces` per abilitare Dev Spaces nel cluster del servizio Azure Kubernetes e seguire i prompt. Il comando seguente abilita Dev Spaces nel cluster *MyAKS* all'interno del gruppo *MyResourceGroup* e crea uno spazio *predefinito*.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Ottenere il codice dell'applicazione di esempio

In questo articolo verrà usata l'[applicazione di esempio Azure Dev Spaces](https://github.com/Azure/dev-spaces) per dimostrare l'uso di Azure Dev Spaces.

Clonare l'applicazione da GitHub e passare alla directory *dev-spaces/samples/nodejs/getting-started/webfrontend*:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Preparare l'applicazione

Per eseguire l'applicazione in Azure Dev Spaces, è necessario avere un Dockerfile e un grafico Helm. Per alcuni linguaggi, come [Java][java-quickstart], [.NET Core][netcore-quickstart] e [Node.js][nodejs-quickstart], gli strumenti client di Azure Dev Spaces possono generare tutti gli asset necessari. Per molti altri linguaggi, come Go, PHP e Python, gli strumenti client consentono di generare il grafico Helm purché venga specificato un Dockerfile valido.

Generare le risorse grafico Docker e Helm per l'esecuzione dell'applicazione in Kubernetes usando il comando `azds prep`:

```cmd
azds prep --public
```

Per la corretta generazione delle risorse grafico Docker e Helm, è necessario eseguire il comando `prep` dalla directory *dev-spaces/samples/nodejs/getting-started/webfrontend*.

## <a name="build-and-run-code-in-kubernetes"></a>Compilare ed eseguire codice in Kubernetes

Compilare ed eseguire il codice nel servizio Azure Kubernetes usando il comando `azds up`:

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
Step 1/8 : FROM node
Step 2/8 : ENV PORT 80
Step 3/8 : EXPOSE 80
Step 4/8 : WORKDIR /app
Step 5/8 : COPY package.json .
Step 6/8 : RUN npm install
Step 7/8 : COPY . .
Step 8/8 : CMD ["npm", "start"]
Built container image in 6m 17s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

Per vedere il servizio in esecuzione, aprire l'URL pubblico, visualizzato nell'output del comando `azds up`. In questo esempio l'URL pubblico è *http://webfrontend.1234567890abcdef1234.eus.azds.io/* .

Se si arresta il comando `azds up` premendo *CTRL+C*, il servizio continuerà a essere eseguito nel servizio Azure Kubernetes e l'URL pubblico rimarrà disponibile.

## <a name="update-code"></a>Aggiornare il codice

Per distribuire una versione aggiornata del servizio, è possibile aggiornare qualsiasi file del progetto ed eseguire di nuovo il comando `azds up`. Ad esempio:

1. Se `azds up` è ancora in esecuzione, premere *CTRL+C*.
1. Aggiornare la [riga 13 di `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) in:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Salvare le modifiche.
1. Eseguire di nuovo il comando `azds up`:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Passare al servizio in esecuzione e osservare le modifiche.
1. Premere *CTRL+C* per arrestare il comando `azds up`.

## <a name="clean-up-your-azure-resources"></a>Pulire le risorse di Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come Azure Dev Spaces consente di sviluppare applicazioni più complesse in più contenitori e su come è possibile semplificare lo sviluppo collaborativo usando versioni o rami diversi del codice in spazi diversi.

> [!div class="nextstepaction"]
> [Sviluppo in team in Azure Dev Spaces][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: about.md#supported-regions-and-configurations