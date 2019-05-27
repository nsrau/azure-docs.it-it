---
title: "Script di Azure PowerShell di esempio: aprire una porta dell'applicazione nel servizio di bilanciamento del carico | Microsoft Docs"
description: Esempio di script di Azure PowerShell - Aprire una porta nel servizio di bilanciamento del carico di Azure per un'applicazione Service Fabric.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 05/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 942dd995651f4c672d50163e5c380b62155b2227
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66161932"
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Aprire una porta dell'applicazione nel servizio di bilanciamento del carico di Azure

Il servizio di bilanciamento del carico di Azure è supportato da un'applicazione Service Fabric eseguita in Azure. Questo script di esempio apre una porta in un servizio di bilanciamento del carico di Azure in modo che un'applicazione Service Fabric possa comunicare con client esterni. Personalizzare i parametri in base alle esigenze. Se il cluster si trova in un gruppo di sicurezza di rete, [aggiungere anche una regola del gruppo di sicurezza di rete](service-fabric-powershell-add-nsg-rule.md) per consentire il traffico in ingresso.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se necessario, installare il modulo PowerShell in Service Fabric con il [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Ottiene una risorsa di Azure.  |
| [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) | Ottiene il servizio di bilanciamento del carico di Azure. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) | Aggiunge la configurazione di una porta probe per un servizio di bilanciamento del carico.|
| [Get-AzLoadBalancerProbeConfig](/powershell/module/az.network/get-azloadbalancerprobeconfig) | Ottiene la configurazione di un probe per un servizio di bilanciamento del carico. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) | Aggiunge la configurazione di una regola a un servizio di bilanciamento del carico. |
| [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) | Imposta lo stato dell'obiettivo per un servizio di bilanciamento del carico. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di PowerShell per Azure Service Fabric sono disponibili in [Esempi di Azure PowerShell](../service-fabric-powershell-samples.md).
