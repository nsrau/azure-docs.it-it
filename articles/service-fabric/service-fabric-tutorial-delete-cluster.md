---
title: Eliminare un cluster di Service Fabric in Azure | Microsoft Docs
description: In questa esercitazione si apprenderà come eliminare un cluster di Service Fabric ospitato in Azure e tutte le risorse che contiene. È possibile eliminare il gruppo di risorse che contiene il cluster o eliminare le risorse in modo selettivo.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 0e5137a8183f378ee5960846e281222c6ecaaa47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66158083"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>Esercitazione: Rimuovere un cluster di Service Fabric in esecuzione in Azure

Questa esercitazione è la quinta di una serie e illustra come eliminare un cluster di Service Fabric in esecuzione in Azure. Per eliminare completamente un cluster di Service Fabric, è necessario eliminare anche le risorse usate dal cluster. Sono disponibili due opzioni: eliminare il gruppo di risorse che contiene il cluster (in modo da rimuovere la risorsa cluster e le altre risorse del gruppo) oppure eliminare la specifica risorsa cluster e le risorse associate (ma non le altre risorse del gruppo).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Eliminare un gruppo di risorse e tutte le risorse che contiene
> * Eliminare risorse da un gruppo di risorse in modo selettivo

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * Creare un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) protetto in Azure usando un modello
> * [Eseguire il monitoraggio di un cluster](service-fabric-tutorial-monitor-cluster.md)
> * [Aumentare o ridurre un cluster](service-fabric-tutorial-scale-cluster.md)
> * [Aggiornare il runtime di un cluster](service-fabric-tutorial-upgrade-cluster.md)
> * Eliminazione di un cluster


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure//install-Az-ps) o l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
* Creare un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) protetto in Azure

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>Eliminare il gruppo di risorse che contiene il cluster di Service Fabric
Il modo più semplice per eliminare il cluster e tutte le risorse che utilizza consiste nell'eliminare il gruppo di risorse.

Accedere ad Azure e selezionare l'ID della sottoscrizione da usare per rimuovere il cluster.  È possibile trovare l'ID della sottoscrizione accedendo al [portale di Azure](https://portal.azure.com). Eliminare il gruppo di risorse e tutte le risorse cluster usando il cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) o il comando [az group delete](/cli/azure/group?view=azure-cli-latest).

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $groupname -Force
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="selectively-delete-the-cluster-resource-and-the-associated-resources"></a>Eliminare la risorsa cluster e le risorse associate in modo selettivo
Se nel gruppo sono incluse solo le risorse correlate al cluster Service Fabric da eliminare, è più semplice eliminare l'intero gruppo di risorse. Se si vogliono eliminare le risorse del gruppo di risorse in modo selettivo, mantenendo le risorse non associate al cluster, seguire questa procedura.

Elencare le risorse nel gruppo di risorse:

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

Per ogni risorsa da eliminare, eseguire lo script seguente:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

Per eliminare la risorsa cluster, eseguire lo script seguente:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Eliminare un gruppo di risorse e tutte le risorse che contiene
> * Eliminare risorse da un gruppo di risorse in modo selettivo

Una volta completata questa esercitazione, è possibile continuare con il percorso seguente:
* Informazioni su come esaminare e gestire un cluster di Service Fabric con [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
* Informazioni su come [applicare patch al sistema operativo Windows](service-fabric-patch-orchestration-application.md) dei nodi del cluster.
* Informazioni su come aggregare e raccogliere eventi per i [cluster Windows](service-fabric-diagnostics-event-aggregation-wad.md) e su come [configurare Log Analytics](service-fabric-diagnostics-oms-setup.md) per il monitoraggio degli eventi dei cluster.
