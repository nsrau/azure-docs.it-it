---
title: Come contrassegnare una macchina virtuale con PowerShell
description: Informazioni sull'assegnazione di tag a una macchina virtuale tramite PowerShell
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 1efb512923caed97126bdb4ee6267c6a9b57f251
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594970"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>Come assegnare un tag a una macchina virtuale in Azure tramite PowerShell

Questo articolo descrive come assegnare un tag a una macchina virtuale in Azure usando PowerShell. I tag sono coppie chiave/valore definite dall'utente che possono essere inserite direttamente in una risorsa o un gruppo di risorse. Azure supporta attualmente fino a 50 tag per risorsa e gruppo di risorse. I tag possono essere posizionati su una risorsa al momento della creazione o aggiunti a una risorsa esistente. Per contrassegnare una macchina virtuale usando l'interfaccia della riga di comando di Azure, vedere [come assegnare un tag a una macchina virtuale in Azure tramite l'interfaccia della](tag-cli.md)riga di comando di Azure.


Usare il `Get-AzVM` cmdlet per visualizzare l'elenco corrente di tag per la macchina virtuale.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

Se la macchina virtuale contiene già tag, sarà possibile visualizzare tutti i tag nel formato elenco.

Per aggiungere tag, usare il `Set-AzResource` comando. Quando si aggiornano i tag tramite PowerShell, i tag vengono aggiornati nel suo complesso. Se si aggiunge un tag a una risorsa che dispone già di tag, sarà necessario includere tutti i tag che si desidera inserire nella risorsa. Di seguito è riportato un esempio di come aggiungere ulteriori tag a una risorsa tramite Cmdlets di PowerShell.

Assegnare tutti i tag correnti per la macchina virtuale alla `$tags` variabile usando la `Get-AzResource` `Tags` proprietà e.

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

Per visualizzare i tag correnti, digitare la variabile.

```azurepowershell-interactive
$tags
```

L'output potrebbe essere simile al seguente:

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

Nell'esempio seguente viene aggiunto un tag denominato `Location` con il valore `myLocation` . Usare `+=` per aggiungere la nuova coppia chiave/valore all' `$tags` elenco.

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

Usare `Set-AzResource` per impostare tutti i tag definiti nella variabile *$Tags* nella macchina virtuale.

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

Usare `Get-AzResource` per visualizzare tutti i tag nella risorsa.

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

L'output dovrebbe avere un aspetto simile al seguente, che ora include il nuovo tag:

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```


**Passaggi successivi**

- Per altre informazioni sull'uso dei tag nelle risorse di Azure, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md) e [Uso dei tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md).
- Per informazioni sull'utilità dei tag nella gestione dell'uso delle risorse di Azure, vedere [Comprendere la fattura per Microsoft Azure](../cost-management-billing/understand/review-individual-bill.md) e [Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure](../cost-management-billing/manage/usage-rate-card-overview.md).
