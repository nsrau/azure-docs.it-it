---
title: "PowerShell: creare un'immagine personalizzata da un file VHD in Azure Lab Services"
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: ba522745aceba2b63ed8b71692f50f9a9e5ea676
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282603"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>Usare PowerShell per creare un'immagine personalizzata da un file VHD in Azure Lab Services

Questo script di esempio di PowerShell crea un'immagine personalizzata da un file VHD in Azure Lab Services

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Prerequisiti
* **Un lab**. Per usare lo script è necessario disporre di un lab esistente. 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Ottiene le risorse. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Ottiene le chiavi di accesso per l'account di Archiviazione di Azure. |
| [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) | Aggiunge una distribuzione di Azure a un gruppo di risorse. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/).

Altri esempi di script di PowerShell per Azure Lab Services sono disponibili in [Azure Lab Services PowerShell samples](../samples-powershell.md) (Esempi di PowerShell per Azure Lab Services).
