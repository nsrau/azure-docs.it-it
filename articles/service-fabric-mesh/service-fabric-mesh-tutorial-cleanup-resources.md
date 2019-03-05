---
title: Esercitazione - Risorse di pulizia in Azure Service Fabric Mesh | Microsoft Docs
description: Informazioni su come rimuovere le risorse di Azure Service Fabric Mesh in modo che non vengano addebitati costi per le risorse che non sono più in uso.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: a60c42310f0698b8290e7ba6195eeed44fe0b95e
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56815791"
---
# <a name="tutorial-remove-azure-resources"></a>Esercitazione: rimuovere le risorse di Azure

Questa esercitazione è parte di una serie di cinque e illustra come eliminare app e relative risorse in modo che non vengano addebitate.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Pulire le risorse usate dall'app in modo che non siano soggette ad addebito.

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Creare un'app Service Fabric Mesh in Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Eseguire il debug di un'app Service Fabric Mesh in esecuzione nel cluster di sviluppo locale](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Distribuire un'app Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Aggiornare un'app Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * Pulire le risorse di Service Fabric Mesh

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non è stata distribuita l'app to-do, seguire le istruzioni in [Pubblicare un'app Web Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="clean-up-resources"></a>Pulire le risorse

L'esercitazione è terminata. Dopo aver terminato con le risorse create, è possibile eliminarle in modo che non siano addebitati costi per le risorse non più in uso. Ciò è particolarmente importante poiché Mesh è un servizio senza server con fatturazione al secondo. Per altre informazioni sui prezzi di Mesh, consultare https://aka.ms/sfmeshpricing.

Uno dei vantaggi forniti da Azure è che quando si creano risorse associate a un determinato gruppo di risorse, eliminando il gruppo di risorse si eliminano tutte le risorse associate. In questo modo, non è necessario eliminare una risorsa alla volta.

Poiché è stato creato un nuovo gruppo di risorse per creare la to-do app, è possibile eliminare tranquillamente l'intero gruppo, in modo da eliminare tutte le risorse associate.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

In alternativa, è possibile eliminare il gruppo di risorse **sfmeshTutorial1RG**[dal portale](../azure-resource-manager/manage-resource-groups-portal.md#delete-resource-groups). 

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata completata la pubblicazione di un'applicazione Service Fabric Mesh in Azure, procedere come segue:

* Esplorare l'[app di esempio Voting](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) per un altro esempio di comunicazione da servizio a servizio
* Per altre informazioni sul modello di risorse di Service Fabric, vedere [Service Fabric Mesh Resource Model](service-fabric-mesh-service-fabric-resources.md) (Modello di risorsa mesh Service Fabric).
* Per altre informazioni su mesh Service Fabric, leggere la [panoramica di mesh Service Fabric](service-fabric-mesh-overview.md).
* Leggere l'articolo su [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).