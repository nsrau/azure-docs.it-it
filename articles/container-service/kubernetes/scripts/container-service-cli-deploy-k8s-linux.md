---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un cluster Kubernetes Linux del servizio contenitore di Azure | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un cluster Kubernetes Linux del servizio contenitore di Azure
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenitori, Micro-Service, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: nepeters
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 1a2672af6428b9db83f3c916897d25de38035531
ms.contentlocale: it-it
ms.lasthandoff: 09/09/2017

---

# <a name="create-an-azure-container-service-kubernetes-linux-cluster"></a>Creare un cluster Kubernetes Linux del servizio contenitore di Azure

Questo esempio consente di creare un cluster del servizio contenitore di Azure che esegue contenitori basati su Kubernetes per Linux.

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

```azurecli
az group create --name myResourceGroup --location eastus

az acs create \
  --orchestrator-type kubernetes \
  --resource-group myResourceGroup \
  --name myK8SCluster \
  --generate-ssh-keys
```

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az acs create](https://docs.microsoft.com/cli/azure/acs#az_acs_create) | Crea un cluster del servizio contenitore di Azure. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando del servizio contenitore di Azure sono disponibili nella [documentazione del servizio contenitore di Azure](../cli-samples.md).


