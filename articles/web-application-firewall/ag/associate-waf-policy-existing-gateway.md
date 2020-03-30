---
title: Associare un criterio di Firewall applicazione Web a un gateway applicazione di Azure esistente
description: Informazioni su come associare un criterio di Firewall applicazione Web a un gateway applicazione di Azure esistente.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083898"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Associare un criterio WAF a un gateway applicazione esistente

È possibile usare Azure PowerShell per [creare un criterio WAF](create-waf-policy-ag.md), ma è possibile che si disponga già di un gateway applicazione e si desideri solo associarvi un criterio WAF. In questo articolo, si fa proprio questo; creare un criterio WAF e associarlo a un gateway applicazione già esistente. 

1. Ottenere il gateway applicazione e i criteri firewall. Se non si dispone di un criterio firewall esistente, vedere il passaggio 2. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. (Facoltativo) Creare un criterio firewall.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Se si sta creando questo criterio WAF per passare da una configurazione WAF a un criterio WAF, il criterio deve essere una copia esatta della configurazione precedente. Ciò significa che ogni esclusione, regola personalizzata, gruppo di regole disabilitato e così via deve essere esattamente lo stesso che è nella configurazione WAF.
3. (Facoltativo) È possibile configurare il criterio WAF in base alle proprie esigenze. Sono incluse le regole personalizzate, la disabilitazione di regole/gruppi di regole, le esclusioni, l'impostazione dei limiti di caricamento dei file e così via. Se si ignora questo passaggio, verranno selezionati tutti i valori predefiniti. 
   
4. Salvare il criterio e collegarlo al gateway applicazione. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>Passaggi successivi
[Ulteriori informazioni sulle regole personalizzate.](configure-waf-custom-rules.md)
