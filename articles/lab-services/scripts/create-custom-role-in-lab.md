---
title: 'Script di PowerShell: Creare un ruolo personalizzato in un lab in Azure DevTest Labs | Microsoft Docs'
description: Questo script di PowerShell aggiunge un utente esterno a un lab in Azure DevTest Labs.
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
ms.openlocfilehash: 4d0c795dffb40ab7efec9005660439f9baef9f3f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160583"
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
