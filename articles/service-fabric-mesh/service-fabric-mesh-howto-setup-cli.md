---
title: Configurare l'interfaccia della riga di comando di Azure Service Fabric Mesh | Microsoft Docs
description: Informazioni su come configurare l'interfaccia della riga di comando di Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/26/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 7e8a12a215c94102f6b08262f129faebf9cfcde9
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115625"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Configurare l'interfaccia della riga di comando di Service Fabric Mesh
L'interfaccia della riga di comando di Service Fabric Mesh è necessaria per distribuire e gestire risorse in Service Fabric Mesh. 

Per l'anteprima l'interfaccia della riga di comando di Azure Service Fabric Mesh viene scritta come estensione dell'interfaccia della riga di comando di Azure. È possibile installarla in Azure Cloud Shell oppure in un'installazione locale dell'interfaccia della riga di comando di Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-service-fabric-mesh-cli-locally"></a>Installare l'interfaccia della riga di comando di Service Fabric Mesh in locale
Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, è necessario installare l'interfaccia della riga di comando di Azure 2.0.43 o versione successiva. Eseguire `az --version` per trovare la versione. Per installare o eseguire l'aggiornamento all'ultima versione dell'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

Per installare il modulo dell'estensione dell'interfaccia della riga di comando di Azure Service Fabric Mesh, usare il comando seguente. 

```azurecli-interactive
az extension add --name mesh
```

Per aggiornare un modulo esistente dell'interfaccia della riga di comando di Azure Service Fabric Mesh, eseguire il comando seguente.

```azurecli-interactive
az extension update --name mesh
```

È anche possibile configurare l'[ambiente di sviluppo Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

[azure-cli-install]: /cli/azure/install-azure-cli
