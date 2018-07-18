---
title: "Script di PowerShell: creare un'immagine personalizzata da un file VHD in Azure Lab Services | Microsoft Docs"
description: Questo script di PowerShell crea un'immagine personalizzata da un file VHD in Azure Lab Services.
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
ms.openlocfilehash: 19b7c3c6018ec56b056761c336bc56c8b63b47a2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636403"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>Usare PowerShell per creare un'immagine personalizzata da un file VHD in Azure Lab Services

Questo script di esempio di PowerShell crea un'immagine personalizzata da un file VHD in Azure Lab Services

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>prerequisiti
* **Un lab**. Per usare lo script è necessario disporre di un lab esistente. 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Ottiene le risorse. |
| [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) | Ottiene le chiavi di accesso per l'account di Archiviazione di Azure. |
| [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) | Aggiunge una distribuzione di Azure a un gruppo di risorse. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script di PowerShell per Azure Lab Services sono disponibili in [Azure Lab Services PowerShell samples](../samples-powershell.md) (Esempi di PowerShell per Azure Lab Services).