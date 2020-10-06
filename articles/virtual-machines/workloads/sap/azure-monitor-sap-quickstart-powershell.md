---
title: Distribuire Monitoraggio di Azure per soluzioni SAP con Azure PowerShell
description: Distribuire Monitoraggio di Azure per soluzioni SAP con Azure PowerShell
author: sameeksha91
ms.author: sakhare
ms.topic: quickstart
ms.service: virtual-machines
ms.devlang: azurepowershell
ms.date: 09/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ab64f3a5ee00296ca7e1d53f8a4c84542e322d71
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89671050"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>Avvio rapido: Distribuire Monitoraggio di Azure per soluzioni SAP con Azure PowerShell

Questo articolo illustra come creare risorse di Monitoraggio di Azure per soluzioni SAP usando il modulo [Az.HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) di PowerShell.

> [!CAUTION]
> Monitoraggio di Azure per soluzioni SAP è attualmente disponibile in anteprima pubblica. Questa versione in anteprima viene fornita senza un contratto di servizio. Non è la scelta consigliata per carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Requisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Se si sceglie di usare PowerShell in locale, per questo articolo è necessario installare il modulo Az PowerShell e connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Per altre informazioni sull'installazione del modulo Az PowerShell, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps). Se si sceglie di usare Cloud Shell, vedere [Panoramica di Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) per altre informazioni.

> [!IMPORTANT]
> Durante la fase di anteprima del modulo **Az.HanaOnAzure** di PowerShell, è necessario installarlo separatamente usando il cmdlet `Install-Module`. Quando il modulo di PowerShell diventerà disponibile a livello generale, entrerà a far parte delle future versioni del modulo Az di PowerShell e sarà disponibile in modalità nativa all'interno di Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

Se si possiedono più sottoscrizioni di Azure, scegliere quella appropriata in cui verranno fatturate le risorse. Selezionare una sottoscrizione specifica usando il cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](../../../azure-resource-manager/management/overview.md) con il cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

Nell'esempio seguente viene creato un gruppo di risorse con il nome specificato e nella posizione specificata.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>Monitor SAP

Per creare un monitor SAP, usare il cmdlet [New-AzSapMonitor](/powershell/module/az.hanaonazure/new-azsapmonitor). Nell'esempio seguente viene creato un monitor SAP per la sottoscrizione, il gruppo di risorse e il nome di risorsa specificati.

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

Per recuperare le proprietà di un monitor SAP, usare il cmdlet [Get-AzSapMonitor](/powershell/module/az.hanaonazure/get-azsapmonitor). Nell'esempio seguente vengono recuperate le proprietà di un monitor SAP per la sottoscrizione, il gruppo di risorse e il nome di risorsa specificati.

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>Istanza del provider

Per creare un'istanza del provider, usare il cmdlet [New-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance). Nell'esempio seguente viene creata un'istanza del provider per la sottoscrizione, il gruppo di risorse e il nome di risorsa specificati.

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

Per recuperare le proprietà di un'istanza del provider, usare il cmdlet [Get-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance). Nell'esempio seguente vengono recuperate le proprietà di un'istanza del provider per la sottoscrizione, il gruppo di risorse, il nome di monitor SAP e il nome di risorsa specificati.

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse create in questo articolo non sono più necessarie, è possibile eliminarle usando gli esempi seguenti.

### <a name="delete-the-provider-instance"></a>Eliminare l'istanza del provider

Per rimuovere un'istanza del provider, usare il cmdlet [Remove-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance). Nell'esempio seguente viene eliminata un'istanza del provider per la sottoscrizione, il gruppo di risorse, il nome di monitor SAP e il nome di risorsa specificati.

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>Eliminare il monitor SAP

Per rimuovere un monitor SAP, usare il cmdlet [Remove-AzSapMonitor](/powershell/module/az.hanaonazure/remove-azsapmonitor). Nell'esempio seguente viene eliminato un monitor SAP per la sottoscrizione, il gruppo di risorse e il nome di monitor specificati.

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>Eliminare il gruppo di risorse.

> [!CAUTION]
> L'esempio seguente elimina il gruppo di risorse specificato e tutte le risorse al suo interno.
> Se nel gruppo di risorse specificato sono presenti anche risorse diverse da quelle usate in questo articolo, verranno eliminate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Vedere altre informazioni su [Monitoraggio di Azure per soluzioni SAP](azure-monitor-overview.md).
