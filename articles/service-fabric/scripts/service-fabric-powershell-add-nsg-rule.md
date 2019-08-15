---
title: 'Script di Azure PowerShell di esempio: aggiungere una regola del gruppo di sicurezza di rete | Microsoft Docs'
description: 'Script di Azure PowerShell di esempio: aggiunge un gruppo di sicurezza di rete per consentire traffico in ingresso attraverso una porta specifica.'
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 11/28/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 8e3187ba399ffa4d6c0ca863380205f7fbbfaae8
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036112"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Aggiungere una regola del gruppo di sicurezza di rete di ingresso

Questo script di esempio consente di creare una regola del gruppo di sicurezza di rete per consentire traffico in ingresso sulla porta 8081.  Lo script ottiene la risorsa `Microsoft.Network/networkSecurityGroups` in cui si trova il cluster, crea una nuova regola di configurazione della sicurezza di rete e aggiorna il gruppo di sicurezza di rete. Personalizzare i parametri in base alle esigenze.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se necessario, installare Azure PowerShell seguendo le istruzioni presenti nella [guida di Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Ottiene la risorsa `Microsoft.Network/networkSecurityGroups`. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Ottiene il gruppo di sicurezza di rete in base al nome.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Aggiunge la configurazione di una regola di sicurezza di rete a un gruppo di sicurezza di rete. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Imposta lo stato dell'obiettivo per un gruppo di sicurezza di rete.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).
