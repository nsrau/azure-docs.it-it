---
title: 'Esempio di script di Azure PowerShell: configurare un dominio personalizzato | Microsoft Docs'
description: 'Esempio di script di Azure PowerShell: configurare un dominio personalizzato'
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.devlang: na
ms.topic: sample
ms.date: 12/14/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: b120f3d2731601652d46f71206637bacdda023cc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33933395"
---
# <a name="set-up-custom-domain"></a>Configurare un dominio personalizzato

Questo script di esempio configura un dominio personalizzato nell'endpoint proxy e del portale del servizio Gestione API.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 3.6 o successiva. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/api-management/setup-custom-domain/setup_custom_domain.ps1 "Set up custom domain")]

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse correlate non sono più necessari, è possibile usare il comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuoverli.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

[!INCLUDE [api-management-custom-domain](../../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Per altri esempi di Azure PowerShell per Gestione API di Azure, vedere l'articolo relativo agli [esempi di PowerShell](../powershell-samples.md).
