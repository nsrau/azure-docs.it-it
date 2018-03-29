---
title: "Script di Azure PowerShell di esempio: modificare l'intervallo di porte di Remote Desktop Protocol | Microsoft Docs"
description: "Script di Azure PowerShell di esempio: modifica l'intervallo di porte di Remote Desktop Protocol di un cluster distribuito."
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 83fb6cc03f605a60b06f31fa6ddd82cd4e3e899e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="update-the-rdp-port-range-values"></a>Aggiornare i valori dell'intervallo di porte RDP

Questo script di esempio modifica i valori dell'intervallo di porte RDP nelle macchine virtuali del nodo del cluster dopo la distribuzione del cluster stesso.  Azure PowerShell viene usato in modo che le macchine virtuali sottostanti non eseguano un ciclo.  Lo script ottiene la risorsa `Microsoft.Network/loadBalancers` nel gruppo di risorse cluster e aggiorna i valori `inboundNatPools.frontendPortRangeStart` e `inboundNatPools.frontendPortRangeEnd`. Personalizzare i parametri in base alle esigenze.

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Ottiene la risorsa `Microsoft.Network/loadBalancers`. |
|[Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource)|Aggiorna la risorsa `Microsoft.Network/loadBalancers`.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di Azure PowerShell per Azure Service Fabric sono disponibili in [Esempi di Azure PowerShell](../service-fabric-powershell-samples.md).
