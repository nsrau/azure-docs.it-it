---
title: Come contrassegnare una risorsa di macchina virtuale Windows in Azure | Documentazione Microsoft
description: Informazioni sull’assegnazione di tag a una macchina virtuale Windows creata in Azure con il modello di distribuzione di Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: f6f3a4be21eee6a9e07a4ae11a530dd9dd50c81c
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749155"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Come assegnare un tag a una macchina virtuale Windows in Azure
Questo articolo descrive diversi modi per contrassegnare una macchina virtuale Windows in Azure tramite il modello di distribuzione Resource Manager. I tag sono coppie chiave/valore definite dall'utente che possono essere inserite direttamente in una risorsa o un gruppo di risorse. Attualmente, Azure supporta fino a 15 tag per ogni risorsa e gruppo di risorse. I tag possono essere posizionati su una risorsa al momento della creazione o aggiunti a una risorsa esistente. Si noti che i tag sono supportati solo per le risorse create tramite il modello di distribuzione Resource Manager. Se si desidera assegnare un tag a una macchina virtuale Linux, vedere l'articolo relativo a [come assegnare un tag a una macchina virtuale Linux in Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Assegnazione di tag tramite PowerShell
Per creare, aggiungere ed eliminare i tag tramite PowerShell, è prima necessario configurare l’ [ambiente PowerShell con Gestione risorse di Azure][PowerShell environment with Azure Resource Manager]. Dopo aver completato l'installazione, è possibile inserire tag su risorse di calcolo, rete e archiviazione al momento della creazione o dopo la creazione della risorsa tramite PowerShell. Questo articolo si concentrerà su come visualizzare o modificare tag inseriti nelle macchine virtuali.

 

Per prima cosa, spostarsi su una macchina virtuale tramite il `Get-AzVM` cmdlet.

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Se la macchina virtuale contiene già dei tag, verranno visualizzati tutti i tag per la risorsa:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Se si desidera aggiungere i tag tramite PowerShell, è possibile utilizzare il comando `Set-AzResource` . Nota: Quando si aggiornano i tag tramite PowerShell, i tag vengono aggiornati nel loro complesso. Se si aggiunge un tag a una risorsa che già dispone di tag, sarà pertanto necessario includere tutti i tag che si desidera inserire nella risorsa. Di seguito è riportato un esempio di come aggiungere ulteriori tag a una risorsa tramite Cmdlets di PowerShell.

Questo primo cmdlet imposta tutti i tag inseriti in *MyTestVM* sulla variabile *$tags* usando `Get-AzResource` e la proprietà `Tags`.

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Il secondo comando consente di visualizzare i tag per la variabile specificata.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

Il terzo comando aggiunge un altro tag alla variabile *$tags* . Si noti l'uso di **+=** per aggiungere la nuova coppia chiave/valore all'elenco *$tags* .

        PS C:\> $tags += @{Location="MyLocation"}

Il quarto comando imposta tutti i tag definiti nella variabile *$tags* sulla risorsa specificata. In questo caso, è MyTestVM.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Il quinto comando visualizza tutti i tag sulla risorsa. Come si può vedere, *Location* è ora definito come un tag con *MyLocation* come valore.

```
    PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
    Location      MyLocation
```

Per altre informazioni sull'assegnazione di tag tramite PowerShell, consultare i [cmdlet di Gestione risorse di Azure][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sull'uso dei tag nelle risorse di Azure, vedere [Panoramica di Azure Resource Manager][Azure Resource Manager Overview] e [Uso dei tag per organizzare le risorse di Azure][Using Tags to organize your Azure Resources].
* Per informazioni sull'utilità dei tag nella gestione dell'uso delle risorse di Azure, vedere [Comprendere la fattura per Microsoft Azure][Understanding your Azure Bill] e [Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
