---
title: Create a Kubernetes cluster with Azure Dev Spaces enabled - Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Informazioni su come creare in modo rapido un cluster Kubernetes abilitato per Azure Dev Spaces direttamente dal browser senza installare alcun componente.
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s
ms.openlocfilehash: ac825caa7aacbc6ac21dd1f9fe7acbb9cf12e900
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325765"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Create a Kubernetes cluster with Azure Dev Spaces enabled with Azure Cloud Shell

You can use [Azure Cloud Shell](/azure/cloud-shell) to create an Azure Kubernetes Service cluster by using the **Try It** button from this page. Se non si è connessi, seguire le istruzioni per accedere con un account Azure, quindi digitare i comandi al prompt di Azure Cloud Shell, quando viene visualizzato.

## <a name="create-the-cluster"></a>Creare il cluster

First, create the resource group in a [region that supports Azure Dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Usare il comando seguente per creare un cluster Kubernetes:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

La creazione del cluster richiede alcuni minuti.  Al termine dell'esercitazione, viene visualizzato l'output in formato JSON. Cercare `provisioningState` e verificare il `Succeeded`.

## <a name="next-steps"></a>Passaggi successivi

Vedere [Azure Dev Spaces](/azure/dev-spaces/) per i collegamenti alle esercitazioni complete.

> [!IMPORTANT]
> Many of the Azure Dev Spaces quickstarts and tutorials use the Azure Dev Spaces CLI to perform operations. Non è possibile installare l'interfaccia della riga di comando Azure Dev Spaces in Azure Cloud Shell.


[supported-regions]: ../about.md#supported-regions-and-configurations