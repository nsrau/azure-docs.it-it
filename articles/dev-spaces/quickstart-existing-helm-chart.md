---
title: Sviluppare un'applicazione con un grafico Helm esistente in Kubernetes
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: Questa guida di avvio rapido illustra come usare Azure Dev Spaces e la riga di comando per sviluppare un'applicazione con un grafico Helm esistente nel servizio Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s
manager: gwallace
ms.openlocfilehash: c37ea0b04e99cf1bba555e098bdf33b8a8558cfa
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996672"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>Guida introduttiva: Sviluppare un'applicazione con un grafico Helm esistente in Kubernetes - Azure Dev Spaces
In questa guida si apprenderà come:

- Configurare Azure Dev Spaces con un cluster Kubernetes gestito in Azure.
- Eseguire un'applicazione con un grafico Helm esistente nel servizio Azure Kubernetes usando Azure Dev Spaces nella riga di comando.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
- [L'interfaccia della riga di comando di Azure installata](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Creare un cluster del servizio Azure Kubernetes

È necessario creare un cluster del servizio Azure Kubernetes in un'[area supportata][supported-regions]. I comandi seguenti creano un gruppo di risorse denominato *MyResourceGroup* e un cluster del servizio Azure Kubernetes denominato *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Abilitare Azure Dev Spaces nel cluster del servizio Azure Kubernetes

Usare il comando `use-dev-spaces` per abilitare Dev Spaces nel cluster del servizio Azure Kubernetes e seguire i prompt. Il comando seguente abilita Dev Spaces nel cluster *MyAKS* all'interno del gruppo *MyResourceGroup* e crea uno spazio di sviluppo denominato *dev*.

> [!NOTE]
> Il comando `use-dev-spaces` installerà anche l'interfaccia della riga di comando di Azure Dev Spaces se non è già installata. Non è possibile installare l'interfaccia della riga di comando Azure Dev Spaces in Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Ottenere il codice dell'applicazione di esempio

In questo articolo verrà usata l'[applicazione di esempio Azure Dev Spaces](https://github.com/Azure/dev-spaces) per dimostrare l'uso di Azure Dev Spaces.

Clonare l'applicazione da GitHub e passare alla directory *dev-spaces/samples/python/getting-started/webfrontend*:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Preparare l'applicazione

Per eseguire l'applicazione in Azure Dev Spaces, è necessario avere un Dockerfile e un grafico Helm. Per alcuni linguaggi, come [Java][java-quickstart], [.NET Core][netcore-quickstart] e [Node.js][nodejs-quickstart], gli strumenti client di Azure Dev Spaces possono generare tutti gli asset necessari. Per molti altri linguaggi, come Go, PHP e Python, gli strumenti client consentono di generare il grafico Helm purché venga specificato un Dockerfile valido. In questo caso l'applicazione di esempio include un Dockerfile e un grafico Helm esistente.

Per generare la configurazione per l'esecuzione dell'applicazione con Azure Dev Spaces con il grafico Helm esistente e Dockerfile, usare il comando `azds prep`:

```cmd
azds prep --enable-ingress --chart webfrontend/
```

È necessario eseguire il comando `prep` dalla directory *dev-spaces/samples/python/getting-started/webfrontend* e specificare il percorso del grafico Helm con l'opzione `--chart`.

> [!NOTE]
> Potrebbe essere visualizzato l'avviso: *AVVISO: Dockerfile could not be generated due to unsupported language (Dockerfile non generato a causa di un linguaggio non supportato)* durante l'esecuzione di `azds prep`. Il comando `azds prep` prova a generare [un Dockerfile e un grafico Helm](how-dev-spaces-works-prep.md#prepare-your-code) per il progetto, ma non sovrascrive gli eventuali Dockerfile o grafici Helm esistenti.

## <a name="build-and-run-code-in-kubernetes"></a>Compilare ed eseguire codice in Kubernetes

Compilare ed eseguire il codice nel servizio Azure Kubernetes usando il comando `azds up`:

```cmd
$ azds up
Using dev space 'dev' with target 'MyAKS'
Synchronizing files...14s
Installing Helm chart...2s
Waiting for container image build...3s
Building container image...
Step 1/7 : FROM python:3
Step 2/7 : WORKDIR /python/webfrontend
Step 3/7 : RUN pip install flask
Step 4/7 : COPY webfrontend.py webfrontend.py
Step 5/7 : COPY public/ public/
Step 6/7 : EXPOSE 80
Step 7/7 : CMD ["python", "./webfrontend.py"]
Built container image in 45s
Waiting for container...25s
Service 'azds-543eae-dev-webfrontend' port 'http' is available at http://dev.service.1234567890abcdef1234.eus.azds.io/
Service 'azds-543eae-dev-webfrontend' port 80 (http) is available via port forwarding at http://localhost:52382
Press Ctrl+C to detach
...
```

Per vedere il servizio in esecuzione, aprire l'URL pubblico, visualizzato nell'output del comando `azds up`. In questo esempio l'URL pubblico è `http://dev.service.1234567890abcdef1234.eus.azds.io/`.

> [!NOTE]
> Quando si passa al servizio durante l'esecuzione di `azds up`, nell'output del comando `azds up` vengono visualizzate anche le tracce delle richieste HTTP. Queste tracce possono essere utili per la risoluzione dei problemi e per il debug del servizio. È possibile disabilitarle usando `--disable-http-traces` durante l'esecuzione di `azds up`.

Se si arresta il comando `azds up` premendo *CTRL+C*, il servizio continuerà a essere eseguito nel servizio Azure Kubernetes e l'URL pubblico rimarrà disponibile.

## <a name="update-code"></a>Aggiornare il codice

Per distribuire una versione aggiornata del servizio, è possibile aggiornare qualsiasi file del progetto ed eseguire di nuovo il comando `azds up`. Ad esempio:

1. Se `azds up` è ancora in esecuzione, premere *CTRL+C*.
1. Aggiornare la [riga 13 di `webfrontend.py`](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13) in:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Salvare le modifiche.
1. Eseguire di nuovo il comando `azds up`:

    ```cmd
    $ azds up
    Using dev space 'dev' with target 'MyAKS'
    Synchronizing files...11s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Passare al servizio in esecuzione e osservare le modifiche.
1. Premere *CTRL+C* per arrestare il comando `azds up`.

## <a name="clean-up-your-azure-resources"></a>Pulire le risorse di Azure

```azurecli
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
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service