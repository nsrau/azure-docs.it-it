---
title: 'Script di Azure PowerShell di esempio: aggiungere una regola del gruppo di sicurezza di rete | Microsoft Docs'
description: 'Script di Azure PowerShell di esempio: aggiunge un gruppo di sicurezza di rete per consentire traffico in ingresso attraverso una porta specifica.'
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: fd3c648ee63c45bef305658832a4d31dfdb213be
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="add-an-inbound-network-security-group-rule"></a>Aggiungere una regola del gruppo di sicurezza di rete di ingresso

Questo script di esempio consente di creare una regola del gruppo di sicurezza di rete per consentire traffico in ingresso sulla porta 8081.  Lo script ottiene la risorsa `Microsoft.Network/networkSecurityGroups` in cui si trova il cluster, crea una nuova regola di configurazione della sicurezza di rete e aggiorna il gruppo di sicurezza di rete. Personalizzare i parametri in base alle esigenze.

Se necessario, installare Azure PowerShell seguendo le istruzioni presenti nella [guida di Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Ottiene la risorsa `Microsoft.Network/networkSecurityGroups`. |
|[Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)| Ottiene il gruppo di sicurezza di rete in base al nome.|
|[Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig)| Aggiunge la configurazione di una regola di sicurezza di rete a un gruppo di sicurezza di rete. |
|[Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)| Imposta lo stato dell'obiettivo per un gruppo di sicurezza di rete.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).
