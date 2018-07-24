---
title: Configurare l'interfaccia della riga di comando di Azure Service Fabric Mesh | Microsoft Docs
description: Informazioni su come configurare l'interfaccia della riga di comando di Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/11/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: f56bcf0c844545e8883175da2bd3f22afdcd19ea
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089528"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Configurare l'interfaccia della riga di comando di Service Fabric Mesh
L'interfaccia della riga di comando di Service Fabric Mesh è necessaria per distribuire e gestire risorse in di Service Fabric Mesh. 

Per l'anteprima l'interfaccia della riga di comando di Azure Service Fabric Mesh viene scritta come estensione dell'interfaccia della riga di comando di Azure. È possibile installarla in Azure Cloud Shell oppure in un'installazione locale dell'interfaccia della riga di comando di Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, è necessario installare l'interfaccia della riga di comando di Azure 2.0.35 o versione successiva. Eseguire `az --version` per trovare la versione. Per installare o eseguire l'aggiornamento all'ultima versione dell'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure CLI 2.0][azure-cli-install].

Rimuovere eventuali installazioni precedenti del modulo dell'interfaccia della riga di comando di Azure Service Fabric Mesh.

```azurecli-interactive
az extension remove --name mesh
```

Per installare il modulo dell'estensione dell'interfaccia della riga di comando di Azure Service Fabric Mesh, usare il comando seguente. 

```azurecli-interactive
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

È anche possibile configurare l'[ambiente di sviluppo Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

[azure-cli-install]: /cli/azure/install-azure-cli