---
title: Creare un cluster Kubernetes con Azure Dev Spaces abilitato-Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Informazioni su come creare in modo rapido un cluster Kubernetes abilitato per Azure Dev Spaces direttamente dal browser senza installare alcun componente.
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s
ms.openlocfilehash: 5e2e5cfd22eeedd3554737458caeca0b891b62fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77605295"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Creare un cluster Kubernetes con Azure Dev Spaces abilitato con Azure Cloud Shell

È possibile usare [Azure cloud Shell](/azure/cloud-shell) per creare un cluster del servizio Kubernetes di Azure usando il pulsante **prova** da questa pagina. Se non si è connessi, seguire le istruzioni per accedere con un account Azure, quindi digitare i comandi al prompt di Azure Cloud Shell, quando viene visualizzato.

## <a name="create-the-cluster"></a>Creare il cluster

Per prima cosa, creare il gruppo di risorse in un' [area che supporta Azure Dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Usare il comando seguente per creare un cluster Kubernetes:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

La creazione del cluster richiede alcuni minuti.  Al termine dell'esercitazione, viene visualizzato l'output in formato JSON. Cercare `provisioningState` e verificare il `Succeeded`.

## <a name="next-steps"></a>Passaggi successivi

Vedere [Azure Dev Spaces](/azure/dev-spaces/) per i collegamenti alle esercitazioni complete.

> [!IMPORTANT]
> Molti dei Azure Dev Spaces guide introduttive e le esercitazioni usano l'interfaccia della riga di comando Azure Dev Spaces per eseguire operazioni. Non è possibile installare l'interfaccia della riga di comando Azure Dev Spaces in Azure Cloud Shell.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service