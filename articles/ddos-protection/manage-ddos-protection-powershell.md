---
title: Creare e configurare un piano di protezione DDoS di Azure usando Azure PowerShell
description: Informazioni su come creare un piano di protezione DDoS usando Azure PowerShell
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: ec2f2f572caa69a9017bfa6da0de404db5b4fd2a
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905475"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-powershell"></a>Guida introduttiva: creare e configurare la protezione DDoS di Azure standard usando Azure PowerShell

Iniziare a usare la protezione DDoS di Azure standard con Azure PowerShell. 

Un piano di protezione DDoS definisce un set di reti virtuali in cui è abilitata la protezione DDoS standard per le sottoscrizioni. È possibile configurare un piano di protezione DDoS standard per l'organizzazione e collegare reti virtuali da più sottoscrizioni allo stesso piano. 

In questa Guida introduttiva si creerà un piano di protezione DDoS che verrà collegato a una rete virtuale. 

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installazione di Azure PowerShell in locale o Azure Cloud Shell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ddos-protection-plan"></a>Creare un piano di protezione DDoS

In Azure, si allocano le risorse correlate a un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno nuovo.

Per creare un gruppo di risorse, usare [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In questo esempio si denominano il gruppo di risorse _MyResourceGroup_ e si usa la località _Stati Uniti orientali_ :

```azurepowershell-interactive
New-AzResourceGroup -Name MyResourceGroup -Location "East US"
```

A questo punto, creare un piano di protezione DDoS denominato _MyDdosProtectionPlan_ :

```azurepowershell-interactive
New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"
```

## <a name="enable-ddos-for-a-virtual-network"></a>Abilitare DDoS per una rete virtuale

### <a name="enable-ddos-for-a-new-virtual-network"></a>Abilitare DDoS per una nuova rete virtuale

È possibile abilitare la protezione DDoS quando si crea una rete virtuale. In questo esempio il nome della rete virtuale _MyVnet_ : 

```azurepowershell-interactive
New-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup -Location "East US" -AddressPrefix 10.0.0.0/16
```

### <a name="enable-ddos-for-an-existing-virtual-network"></a>Abilitare la protezione DDoS per una rete virtuale esistente

È possibile associare una rete virtuale esistente durante la creazione di un piano di protezione DDoS:

```azurepowershell-interactive
# Creates the DDoS protection plan
$ddosProtectionPlan = New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"

# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = New-Object Microsoft.Azure.Commands.Network.Models.PSResourceId

# Update the properties and enable DDoS protection
$vnet.DdosProtectionPlan.Id = $ddosProtectionPlan.Id
$vnet.EnableDdosProtection = $true
$vnet | Set-AzVirtualNetwork
``` 

## <a name="validate-and-test"></a>Convalida e verifica

Per prima cosa, verificare i dettagli del piano di protezione DDoS:

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan
```

Verificare che il comando restituisca i dettagli corretti del piano di protezione DDoS.

Eseguire test tramite simulazioni

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile salvare le risorse per l'esercitazione successiva. Se non è più necessario, eliminare il gruppo di risorse _MyResourceGroup_ . Quando si elimina il gruppo di risorse, viene eliminato anche il piano di protezione DDoS e tutte le risorse correlate. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

Per disabilitare la protezione DDoS per una rete virtuale: 

```azurepowershell-interactive
# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = $null
$vnet.EnableDdosProtection = $false
$vnet | Set-AzVirtualNetwork
```

Se si vuole eliminare un piano di protezione DDoS, è necessario innanzitutto annullare l'associazione di tutte le reti virtuali.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare e configurare i dati di telemetria per il piano di protezione DDoS, continuare con le esercitazioni.

> [!div class="nextstepaction"]
> [Visualizzare e configurare la telemetria di protezione DDoS](telemetry-monitoring-alerting.md)