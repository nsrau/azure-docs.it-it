---
title: Esempio di script di Azure PowerShell - Configurare i criteri relativi ai limiti di frequenza | Microsoft Docs
description: Esempio di script di Azure PowerShell - Configurare i criteri relativi ai limiti di frequenza
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.devlang: na
ms.topic: sample
ms.date: 11/16/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: b0badd62141cf425ca71fc850d6fe1346eaa238a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66141054"
---
# <a name="set-up-rate-limit-policy"></a>Configurare i criteri relativi ai limiti di frequenza

Questo script di esempio consente di configurare i criteri relativi ai limiti di frequenza. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 1.0 o successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/api-management/setup-rate-limit-policy/setup_rate_limit_policy.ps1 "Set up rate limit policy")]

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Per altri esempi di Azure PowerShell per Gestione API di Azure, vedere gli [esempi di PowerShell](../powershell-samples.md).
