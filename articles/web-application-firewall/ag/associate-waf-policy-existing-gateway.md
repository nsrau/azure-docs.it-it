---
title: Associare un criterio di Web Application Firewall a un gateway di applicazione Azure esistente
description: Informazioni su come associare un criterio di Web Application Firewall a un gateway di applicazione Azure esistente.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: b455849c889c463fbda305e690f998d58fab0d8f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516877"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Associare un criterio WAF a un gateway applicazione esistente

È possibile usare Azure PowerShell per [creare un criterio WAF](create-waf-policy-ag.md), ma è possibile che si disponga già di un gateway applicazione e si voglia semplicemente associare un criterio WAF. In questo articolo viene eseguita solo questa operazione. si crea un criterio WAF e lo si associa a un gateway applicazione già esistente. 

1. Ottenere il gateway applicazione e i criteri firewall. Se non si dispone di un criterio firewall esistente, vedere il passaggio 2. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. Opzionale Creare un criterio del firewall.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Se si sta creando questo criterio WAF per eseguire la transizione da una configurazione WAF a un criterio WAF, il criterio deve essere una copia esatta della configurazione precedente. Ciò significa che tutte le esclusioni, le regole personalizzate, il gruppo di regole disabilitato e così via devono essere identiche a quelle della configurazione WAF.
3. Opzionale È possibile configurare i criteri di WAF in base alle esigenze. Sono incluse le regole personalizzate, la disabilitazione di regole/gruppi di regole, le esclusioni, l'impostazione dei limiti di caricamento dei file e così via. Se si ignora questo passaggio, verranno selezionate tutte le impostazioni predefinite. 
   
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
[Informazioni sulle regole personalizzate.](/configure-waf-custom-rules.md)
