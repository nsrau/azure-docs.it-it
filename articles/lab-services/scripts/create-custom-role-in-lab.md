---
title: 'PowerShell: creare un ruolo personalizzato in un lab in Lab devTest di AzurePowerShell - Create a custom role in a lab in Azure DevTest Labs'
description: Questo articolo fornisce uno script di Azure PowerShell che aggiunge un utente esterno a un lab in Azure DevTest Labs.This article provides an Azure PowerShell script that adds an external user to a lab in Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 9605b3c63877335b8f180ae5fd2b06edc1601096
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760458"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Usare PowerShell per creare un ruolo personalizzato in un lab di Azure DevTest Labs

Questo script di esempio di PowerShell crea un ruolo personalizzato da usare in un lab di Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Prerequisiti
* **Un lab**. Per usare lo script è necessario disporre di un lab esistente. 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Ottiene le operazioni per un provider di risorse di Azure che sono entità a protezione diretta che usano il Controllo degli accessi in base al ruolo di Azure. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Elenca tutti i ruoli Controllo degli accessi in base al ruolo di Azure disponibili per l'assegnazione. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Crea un ruolo personalizzato. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script di PowerShell per Azure Lab Services sono disponibili in [Azure Lab Services PowerShell samples](../samples-powershell.md) (Esempi di PowerShell per Azure Lab Services).
