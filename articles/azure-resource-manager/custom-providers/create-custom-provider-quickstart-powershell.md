---
title: Creare un provider di risorse personalizzato con Azure PowerShell
description: Informazioni su come creare un provider di risorse personalizzato con Azure PowerShell
author: MSEvanhi
ms.author: evanhi
ms.topic: quickstart
ms.devlang: azurepowershell
ms.date: 09/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6912f7f6fdc88c5d611bfbfd78f15e5f7a949f70
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951843"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>Avvio rapido: Creare un provider di risorse personalizzato con Azure PowerShell

Questa guida di avvio rapido illustra come creare un provider di risorse personalizzato di Azure con il modulo [Az.CustomProviders](/powershell/module/az.customproviders) di PowerShell.

> [!CAUTION]
> I provider personalizzati di Azure sono attualmente disponibili in anteprima pubblica. Questa versione in anteprima viene fornita senza un contratto di servizio. Non è la scelta consigliata per carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Requisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Se si sceglie di usare PowerShell in locale, per questo articolo è necessario installare il modulo Az PowerShell e connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Per altre informazioni sull'installazione del modulo Az PowerShell, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps). Se si sceglie di usare Cloud Shell, vedere [Panoramica di Azure Cloud Shell](../../cloud-shell/overview.md) per altre informazioni.

> [!IMPORTANT]
> Durante la fase di anteprima del modulo **Az.CustomProviders** di PowerShell, è necessario installarlo separatamente usando il cmdlet `Install-Module`. Quando il modulo di PowerShell diventerà disponibile a livello generale, entrerà a far parte delle future versioni del modulo Az di PowerShell e sarà disponibile in modalità nativa all'interno di Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

Se si possiedono più sottoscrizioni di Azure, scegliere quella appropriata in cui verranno fatturate le risorse. Selezionare una sottoscrizione specifica usando il cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](../../azure-resource-manager/management/overview.md) con il cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

L'esempio seguente crea un gruppo di risorse con il nome specificato e nella posizione specificata.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>Creare un provider di risorse personalizzato

Per creare o aggiornare un provider di risorse personalizzato, usare il cmdlet [New-AzCustomProvider](/powershell/module/az.customproviders/new-azcustomprovider), come illustrato nell'esempio seguente.

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>Ottenere il manifesto del provider di risorse personalizzato

Per recuperare informazioni sul manifesto del provider di risorse personalizzato, usare il cmdlet [Get-AzCustomProvider](/powershell/module/az.customproviders/get-azcustomprovider), come illustrato nell'esempio seguente.

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>Creare un'associazione

Per creare o aggiornare un'associazione, usare il cmdlet [New-AzCustomProviderAssociation](/powershell/module/az.customproviders/new-azcustomproviderassociation), come illustrato nell'esempio seguente.

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>Ottenere un'associazione

Per recuperare informazioni su un'associazione, usare il cmdlet [Get-AzCustomProviderAssociation](/powershell/module/az.customproviders/get-azcustomproviderassociation), come illustrato nell'esempio seguente.

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse create in questo articolo non sono più necessarie, è possibile eliminarle eseguendo gli esempi seguenti.

### <a name="delete-an-association"></a>Eliminare un'associazione

Per rimuovere un'associazione, usare il cmdlet [Remove-AzCustomProviderAssociation](/powershell/module/az.customproviders/remove-azcustomproviderassociation). L'esempio seguente elimina un'associazione.

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>Eliminare un provider di risorse personalizzato

Per rimuovere un provider di risorse personalizzato, usare il cmdlet [Remove-AzCustomProvider](/powershell/module/az.customproviders/remove-azcustomprovider). L'esempio seguente elimina un provider di risorse personalizzato.

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>Eliminare il gruppo di risorse.

> [!CAUTION]
> L'esempio seguente elimina il gruppo di risorse specificato e tutte le risorse al suo interno.
> Se nel gruppo di risorse specificato sono presenti anche risorse diverse da quelle usate in questo articolo, verranno eliminate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Leggere altre informazioni sui [provider di risorse personalizzati di Azure](overview.md).