---
title: "Script di PowerShell: Aggiungere un'immagine del marketplace a un lab in Azure DevTest Labs | Microsoft Docs"
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
ms.openlocfilehash: e099a29a198d43bf8d00487ab45e2648479aedbe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160589"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Usare PowerShell per aggiunge un'immagine del marketplace a un lab in Azure DevTest Labs

Questo script di esempio di PowerShell aggiunge un'immagine del marketplace a un lab in Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Prerequisiti
* **Un lab**. Per usare lo script è necessario disporre di un lab esistente. 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| Find-AzResource | Cerca le risorse in base a parametri specificati. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Ottiene le risorse. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modifica una risorsa. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Crea una risorsa. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script di PowerShell per Azure Lab Services sono disponibili in [Azure Lab Services PowerShell samples](../samples-powershell.md) (Esempi di PowerShell per Azure Lab Services).
