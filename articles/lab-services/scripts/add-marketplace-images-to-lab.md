---
title: "Script di PowerShell: aggiungere un'immagine del marketplace a un lab in Azure DevTest Labs | Microsoft Docs"
description: Questo script di PowerShell aggiunge un'immagine del marketplace a un lab in Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: 9a65237b3eba6c9878d73148f0143f20dd60dd79
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636549"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Usare PowerShell per aggiunge un'immagine del marketplace a un lab in Azure DevTest Labs

Questo script di esempio di PowerShell aggiunge un'immagine del marketplace a un lab in Azure DevTest Labs. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>prerequisiti
* **Un lab**. Per usare lo script è necessario disporre di un lab esistente. 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [Find-AzureRmResource](/module/azurerm.resources/find-azurermresource) | Cerca le risorse in base a parametri specificati. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Ottiene le risorse. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Modifica una risorsa. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Crea una risorsa. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script di PowerShell per Azure Lab Services sono disponibili in [Azure Lab Services PowerShell samples](../samples-powershell.md) (Esempi di PowerShell per Azure Lab Services).