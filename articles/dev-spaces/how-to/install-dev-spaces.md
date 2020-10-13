---
title: Abilitare Azure Dev Spaces nel servizio Azure Kubernetes e installare gli strumenti lato client
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Informazioni su come abilitare Azure Dev Spaces in un cluster del servizio Azure Kubernetes e installare gli strumenti lato client.
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s
ms.openlocfilehash: eb7c9b4d3d03b6f4f1f21e6fb2b2a60aa303b181
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212431"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Abilitare Azure Dev Spaces in un cluster del servizio Azure Kubernetes e installare gli strumenti lato client

Questo articolo illustra diversi modi per abilitare Azure Dev Spaces in un cluster del servizio Azure Kubernetes e installare gli strumenti lato client.

## <a name="enable-azure-dev-spaces-using-the-cli"></a>Abilitare Azure Dev Spaces usando l'interfaccia della riga di comando

Prima di poter abilitare Dev Spaces tramite l'interfaccia della riga di comando, è necessario disporre di:
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito][az-portal-create-account].
* [Interfaccia della riga di comando di Azure installata][install-cli].
* [Un cluster del servizio Azure Kubernetes][create-aks-cli] in un'[area supportata][supported-regions].

Usare il comando `use-dev-spaces` per abilitare Dev Spaces nel cluster del servizio Azure Kubernetes e seguire i prompt.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Il comando precedente abilita Dev Spaces nel cluster *MyAKSCluster* all'interno del gruppo *MyResourceGroup* e crea uno spazio di Dev Spaces *predefinito*.

```console
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

## <a name="install-the-client-side-tools"></a>Installare gli strumenti lato client

È possibile usare gli strumenti lato client di Azure Dev Spaces per interagire con le istanze di Dev Spaces in un cluster del servizio Azure Kubernetes dal computer locale. Per installare gli strumenti lato client sono disponibili diversi modi:

* Installare l'[estensione Azure Dev Spaces][vscode] in [Visual Studio Code][vscode-extension].
* Installare il carico di lavoro Sviluppo di Azure in [Visual Studio 2019][visual-studio].
* Scaricare e installare l'interfaccia della riga di comando di [Windows][cli-win], [Mac][cli-mac] o [Linux][cli-linux].

## <a name="remove-azure-dev-spaces-using-the-cli"></a>Rimuovere Azure Dev Spaces usando l'interfaccia della riga di comando

Per rimuovere Azure Dev Spaces dal cluster del servizio Azure Kubernetes, usare il comando `azds remove`.

```azurecli
azds remove -g MyResourceGroup -n MyAKS
```

L'output di esempio seguente mostra come rimuovere Azure Dev Spaces dal cluster *MyAKS* .

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Tutti gli spazi dei nomi creati con Azure Dev Spaces rimarranno associati ai carichi di lavoro, ma i nuovi carichi di lavoro in tali spazi dei nomi non verranno instrumentati con Azure Dev Spaces. Se si riavviano eventuali pod esistenti instrumentati con Azure Dev Spaces, è possibile che vengano visualizzati errori. Tali pod dovranno essere ridistribuiti senza strumenti di Azure Dev Spaces. Per rimuovere completamente Azure Dev Spaces dal cluster, eliminare tutti i pod in tutti gli spazi dei nomi in cui è stato abilitato Azure Dev Spaces.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul funzionamento di Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Funzionamento di Azure Dev Spaces](../how-dev-spaces-works.md)

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
