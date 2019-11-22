---
title: Installare Azure Dev Spaces su AKS e gli strumenti lato client
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Informazioni su come installare Azure Dev Spaces in un cluster AKS e installare gli strumenti lato client.
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s
ms.openlocfilehash: c62fe38a12b5ec279bc51fe8bc0d340e2f439200
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280064"
---
# <a name="install-azure-dev-spaces-on-aks-and-the-client-side-tooling"></a>Installare Azure Dev Spaces su AKS e gli strumenti lato client

Questo articolo illustra diversi modi per installare Azure Dev Spaces in un cluster AKS e installare gli strumenti lato client.

## <a name="install-azure-dev-spaces-using-the-cli"></a>Installare Azure Dev Spaces usando l'interfaccia della riga di comando

Prima di poter installare gli spazi di sviluppo usando l'interfaccia della riga di comando, è necessario:
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito][az-portal-create-account].
* [Interfaccia della riga di comando di Azure installata][install-cli].
* [Un cluster AKS][create-aks-cli] in un' [area supportata][supported-regions].

Usare il comando `use-dev-spaces` per abilitare Dev Spaces nel cluster del servizio Azure Kubernetes e seguire i prompt.

```cmd
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Il comando precedente Abilita gli spazi di sviluppo nel cluster *myAKSCluster* nel gruppo *myResourceGroup* e crea uno spazio di sviluppo *predefinito* .

```cmd
$ az aks use-dev-spaces -g myResourceGroup -n myAKSCluster

'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

Il comando `use-dev-spaces` installa anche l'interfaccia della riga di comando di Azure Dev Spaces.

## <a name="install-azure-dev-spaces-using-the-azure-portal"></a>Installare Azure Dev Spaces usando il portale di Azure

Prima di poter installare gli spazi di sviluppo usando il portale di Azure, è necessario:
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito][az-portal-create-account].
* [Un cluster AKS][create-aks-portal] in un' [area supportata][supported-regions].

Per installare Azure Dev Spaces utilizzando il portale di Azure:
1. Accedere al [portale di Azure][az-portal].
1. Passare al cluster AKS.
1. Fare clic su *spazi di sviluppo*.
1. Impostare *Abilita Dev Spaces* su *Sì* e fare clic su *Salva*.

![Abilitare Dev Spaces nel portale di Azure](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

L'installazione di Azure Dev Spaces utilizzando **il portale di Azure non** installa gli strumenti lato client per Azure Dev Spaces.

## <a name="install-the-client-side-tooling"></a>Installare gli strumenti lato client

È possibile usare gli strumenti lato client Azure Dev Spaces per interagire con gli spazi di sviluppo in un cluster AKS dal computer locale. Sono disponibili diversi modi per installare gli strumenti lato client:

* In [Visual Studio Code][vscode]installare l' [estensione Azure Dev Spaces][vscode-extension].
* In [Visual Studio 2019][visual-studio]installare il carico di lavoro sviluppo di Azure.
* In Visual Studio 2017 installare il carico di lavoro sviluppo Web e [Visual Studio Tools per Kubernetes][visual-studio-k8s-tools].
* Scaricare e installare l'interfaccia della riga di comando di [Windows][cli-win], [Mac][cli-mac]o [Linux][cli-linux] .

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come Azure Dev Spaces consente di sviluppare applicazioni più complesse in più contenitori e su come è possibile semplificare lo sviluppo collaborativo usando versioni o rami diversi del codice in spazi diversi.

> [!div class="nextstepaction"]
> [Sviluppo in team in Azure Dev Spaces][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
