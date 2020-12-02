---
title: 'Avvio rapido: Creare un modulo HSM dedicato di Azure con Azure PowerShell'
description: Creare un modulo HSM dedicato di Azure con Azure PowerShell
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurepowershell
ms.date: 11/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eebfb257d0324cf2771bd3af979ddbebb8429fb7
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94905547"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-with-azure-powershell"></a>Avvio rapido: Creare un modulo HSM dedicato di Azure con Azure PowerShell

Questo articolo descrive come creare un modulo HSM dedicato di Azure usando il modulo [Az.DedicatedHsm](/powershell/module/az.dedicatedhsm) di PowerShell.

## <a name="requirements"></a>Requisiti

* Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Durante la fase di anteprima del modulo **Az.DedicatedHsm** di PowerShell, è necessario installarlo separatamente usando il cmdlet `Install-Module`. Quando il modulo di PowerShell diventerà disponibile a livello generale, entrerà a far parte delle future versioni del modulo Az di PowerShell e sarà disponibile per impostazione predefinita all'interno di Azure Cloud Shell.

  ```azurepowershell-interactive
  Install-Module -Name Az.DedicatedHsm
  ```

* Se si possiedono più sottoscrizioni di Azure, scegliere quella appropriata in cui verranno fatturate le risorse. Selezionare una sottoscrizione specifica usando il cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) con il cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

L'esempio seguente crea un gruppo di risorse con il nome specificato e nella posizione specificata.

```azurepowershell-interactive
New-AzResourceGroup -Name myRG -Location westus
```

## <a name="create-a-dedicated-hsm"></a>Creare un modulo HSM dedicato

Per creare un modulo HSM dedicato, usare il cmdlet [New-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/new-azdedicatedhsm). L'esempio seguente crea un modulo HSM dedicato nella sottoscrizione specificata.

```azurepowershell-interactive
$Params = @{
  Name  = 'MyHSM'
  ResourceGroupName = 'myRG'
  Location = 'westus'
  Sku = 'SafeNet Luna Network HSM A790'
  StampId = 'stamp1'
  SubnetId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myhsm-vnet/subnets/hsmsubnet'
  NetworkInterface = @{PrivateIPAddress = '10.2.1.120'}
}
New-AzDedicatedHsm @Params
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="get-a-dedicated-hsm"></a>Recuperare un modulo HSM dedicato

Per recuperare informazioni su un modulo HSM dedicato esistente, usare il cmdlet [Get-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/get-azdedicatedhsm). L'esempio seguente recupera il modulo HSM dedicato specificato.

```azurepowershell-interactive
Get-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="update-a-dedicated-hsm"></a>Aggiornare un modulo HSM dedicato

Per aggiornare un modulo HSM dedicato, usare il cmdlet [Update-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/update-azdedicatedhsm). L'esempio seguente aggiorna un modulo HSM dedicato nella sottoscrizione specificata.

```azurepowershell-interactive
Update-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG -Tag @{'key1' = '1'; 'key2' = 2; 'key3' = 3}
```

```Output
PS C:\>Update-AzDedicatedHsm -Name  hsm-n7wfxi -ResourceGroupName dedicatedhsm-rg-n359cz -Tag @{'key1' = '1';
'key2' = 2; 'key3' = 3}

Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse create in questo articolo non sono più necessarie, è possibile eliminarle eseguendo gli esempi seguenti.

### <a name="remove-a-dedicated-hsm"></a>Rimuovere un modulo HSM dedicato

Per rimuovere un modulo HSM dedicato, usare il cmdlet [Remove-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/remove-azdedicatedhsm). L'esempio seguente elimina il modulo HSM dedicato specificato.

```azurepowershell-interactive
Remove-AzDedicatedHsm -Name hsm-7t2xaf -ResourceGroupName lucas-manual-test
```

### <a name="delete-the-resource-group"></a>Eliminare il gruppo di risorse.

> [!CAUTION]
> L'esempio seguente elimina il gruppo di risorse specificato e tutte le risorse al suo interno.
> Se nel gruppo di risorse specificato sono presenti anche risorse diverse da quelle usate in questo articolo, verranno eliminate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myRG
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul modulo [HSM dedicato di Azure](overview.md).
