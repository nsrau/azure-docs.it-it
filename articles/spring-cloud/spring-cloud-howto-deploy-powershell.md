---
title: Come distribuire Azure Spring cloud con Azure PowerShell
description: Come distribuire Azure Spring cloud con Azure PowerShell
author: bmitchell287
ms.author: brendm
ms.topic: conceptual
ms.service: spring-cloud
ms.devlang: azurepowershell
ms.date: 11/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cb320a37818084f2fbcad22a3cc992655b19c3d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95550010"
---
# <a name="deploy-azure-spring-cloud-with-azure-powershell"></a>Distribuire Azure Spring cloud con Azure PowerShell

Questo articolo descrive come creare un'istanza di Azure Spring cloud usando il modulo [AZ. SpringCloud](/powershell/module/Az.SpringCloud) di PowerShell.

## <a name="requirements"></a>Requisiti

* Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Mentre il modulo di PowerShell **AZ. SpringCloud** è in anteprima, è necessario installarlo separatamente usando il `Install-Module` cmdlet. Quando il modulo di PowerShell diventerà disponibile a livello generale, entrerà a far parte delle future versioni del modulo Az di PowerShell e sarà disponibile per impostazione predefinita all'interno di Azure Cloud Shell.

  ```azurepowershell-interactive
  Install-Module -Name Az.SpringCloud
  ```

* Se si possiedono più sottoscrizioni di Azure, scegliere quella appropriata in cui verranno fatturate le risorse. Selezionare una sottoscrizione specifica usando il cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) con il cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

L'esempio seguente crea un gruppo di risorse con il nome specificato e nella posizione specificata.

```azurepowershell-interactive
New-AzResourceGroup -Name <resource group name> -Location eastus
```

## <a name="provision-a-new-instance-of-azure-spring-cloud"></a>Effettuare il provisioning di una nuova istanza di Azure Spring cloud

Per creare una nuova istanza di Azure Spring cloud, usare il cmdlet [New-AzSpringCloud](/powershell/module/az.springcloud/new-azspringcloud) . Nell'esempio seguente viene creato un servizio cloud Spring di Azure con il nome specificato nel gruppo di risorse creato in precedenza.

```azurepowershell-interactive
New-AzSpringCloud -ResourceGroupName <resource group name> -name <service instance name> -Location eastus
```

## <a name="create-a-new-azure-spring-cloud-app"></a>Creare una nuova app Azure Spring cloud

Per creare una nuova app, usare il cmdlet [New-AzSpringCloudApp](/powershell/module/az.springcloud/new-azspringcloudapp) . L'esempio seguente crea un'app Cloud Spring di Azure denominata `gateway` .

```azurepowershell-interactive
New-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="create-a-new-azure-spring-cloud-deployment"></a>Creare una nuova distribuzione di Azure Spring cloud

Per creare una nuova distribuzione, usare il cmdlet [New-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/new-azspringcloudappdeployment) . Nell'esempio seguente viene creata una distribuzione cloud di Azure Spring denominata `default` per l' `gateway` app.

```azurepowershell-interactive
New-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -Name <service instance name> -AppName gateway -DeploymentName default
```

## <a name="get-an-azure-spring-cloud-service"></a>Ottenere un servizio cloud Spring di Azure

Per ottenere un servizio cloud Spring di Azure e le relative proprietà, usare il cmdlet [Get-AzSpringCloud](/powershell/module/az.springcloud/get-azspringcloud) . Nell'esempio seguente vengono recuperate le informazioni sul servizio cloud Spring di Azure specificato.

```azurepowershell-interactive
Get-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

## <a name="get-an-azure-spring-cloud-app"></a>Ottenere un'app cloud di Azure Spring

Per ottenere un'app Cloud Spring di Azure e le relative proprietà, usare il cmdlet [Get-AzSpringCloudApp](/powershell/module/az.springcloud/get-azspringcloudapp) . Nell'esempio seguente vengono recuperate informazioni sull' `gateway` app Spring cloud.

```azurepowershell-interactive
Get-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="get-an-azure-spring-cloud-deployment"></a>Ottenere una distribuzione cloud di Azure Spring

Per ottenere una distribuzione cloud di Azure Spring e le relative proprietà, usare il cmdlet [Get-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/get-azspringcloudappdeployment) . Nell'esempio seguente vengono recuperate le informazioni sulla `default` distribuzione di Spring cloud.

```azurepowershell-interactive
Get-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse create in questo articolo non sono più necessarie, è possibile eliminarle eseguendo gli esempi seguenti.

### <a name="delete-an-azure-spring-cloud-deployment"></a>Eliminare una distribuzione cloud di Azure Spring

Per rimuovere una distribuzione cloud di Azure Spring, usare il cmdlet [Remove-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/remove-azspringcloudappdeployment) . Nell'esempio seguente viene eliminata una distribuzione di app cloud di Azure Spring denominata `default` per il servizio e l'app specificati.

```azurepowershell-interactive
Remove-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

### <a name="delete-an-azure-spring-cloud-app"></a>Eliminare un'app Azure Spring cloud

Per rimuovere un'app Spring cloud, usare il cmdlet [Remove-AzSpringCloudApp](/powershell/module/Az.SpringCloud/remove-azspringcloudapp) . Nell'esempio seguente viene eliminata l' `gateway` app nel servizio e nel gruppo di risorse specificati.

```azurepowershell
Remove-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

### <a name="delete-an-azure-spring-cloud-service"></a>Eliminare un servizio cloud Spring di Azure

Per rimuovere un servizio cloud Spring di Azure, usare il cmdlet [Remove-AzSpringCloud](/powershell/module/Az.SpringCloud/remove-azspringcloud) . Nell'esempio seguente viene eliminato il servizio cloud Spring di Azure specificato.

```azurepowershell
Remove-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

### <a name="delete-the-resource-group"></a>Eliminare il gruppo di risorse.

> [!CAUTION]
> L'esempio seguente elimina il gruppo di risorse specificato e tutte le risorse al suo interno.
> Se nel gruppo di risorse specificato sono presenti anche risorse diverse da quelle usate in questo articolo, verranno eliminate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <resource group name>
```

## <a name="next-steps"></a>Passaggi successivi

[Risorse per gli sviluppatori di Azure Spring cloud](spring-cloud-resources.md).
