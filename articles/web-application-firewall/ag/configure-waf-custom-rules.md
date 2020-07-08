---
title: Configurare regole personalizzate v2 tramite PowerShell
titleSuffix: Azure Web Application Firewall
description: Informazioni su come configurare regole personalizzate di Web Application Firewall (WAF) v2 tramite Azure PowerShell. È possibile creare regole personalizzate valutate per ogni richiesta che passa attraverso il firewall.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/21/2020
ms.author: victorh
ms.openlocfilehash: 2572e30c02552859eb5c61915a9ef524c0c6cc70
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758963"
---
# <a name="configure-web-application-firewall-v2-on-application-gateway-with-a-custom-rule-using-azure-powershell"></a>Configurare Web Application Firewall v2 nel gateway applicazione con una regola personalizzata tramite Azure PowerShell

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

Le regole personalizzate consentono di creare le regole desiderate con cui valutare ogni richiesta che passa attraverso Web Application Firewall (WAF) v2. Queste regole hanno una priorità più elevata rispetto alle altre dei set di regole gestiti. Le regole personalizzate hanno un'azione (autorizzazione o blocco), una condizione di corrispondenza e un operatore che ne consentono la personalizzazione completa.

Questo articolo crea un firewall WAF v2 del gateway applicazione che usa una regola personalizzata. La regola personalizzata blocca il traffico se l'intestazione della richiesta contiene l'*evilbot* User-Agent.

Per altri esempi di regole personalizzate, vedere [Creare e usare regole di Web Application Firewall personalizzate](create-custom-waf-rules.md)

Se si vuole eseguire il codice Azure PowerShell in questo articolo in uno script continuo che sia possibile copiare, incollare ed eseguire, vedere [Esempi di Azure PowerShell per il gateway applicazione di Azure](powershell-samples.md).

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-powershell-module"></a>Modulo di Azure PowerShell

Se si sceglie di installare e usare Azure PowerShell in locale, per questo script è necessario il modulo Azure PowerShell versione 2.1.0 o successiva.

1. Per trovare la versione, eseguire `Get-Module -ListAvailable Az`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps).
2. Eseguire `Connect-AzAccount` per creare una connessione con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="example-script"></a>Script di esempio

### <a name="set-up-variables"></a>Configurare le variabili

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-vnet"></a>Creare una rete virtuale

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>Creare un VIP pubblico statico

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-frontend-port"></a>Creare il pool e la porta front-end

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>Creare un listener, un'impostazione HTTP, una regola e una scalabilità automatica

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled

$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3

$sku = New-AzApplicationGatewaySku -Name WAF_v2 -Tier WAF_v2
```

### <a name="create-two-custom-rules-and-apply-it-to-waf-policy"></a>Creare due regole personalizzate e applicarle ai criteri WAF

```azurepowershell
# Create WAF config
$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention" -RuleSetType "OWASP" -RuleSetVersion "3.0"
# Create a User-Agent header custom rule 
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent
$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  
$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block
 
# Create a geo-match custom rule
$var2 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition2 = New-AzApplicationGatewayFirewallCondition -MatchVariable $var2 -Operator GeoMatch -MatchValue "US"  -NegationCondition $False
$rule2 = New-AzApplicationGatewayFirewallCustomRule -Name allowUS -Priority 14 -RuleType MatchRule -MatchCondition $condition2 -Action Allow

# Create a firewall policy
$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafpolicyNew -ResourceGroup $rgname -Location $location -CustomRule $rule,$rule2
```

### <a name="create-the-application-gateway"></a>Creare il gateway applicazione

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname `
  -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection  $poolSetting01 `
  -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
  -FrontendPorts $fp01 -HttpListeners $listener01 `
  -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig `
  -WebApplicationFirewallConfig $wafConfig `
  -FirewallPolicy $wafPolicy
```

## <a name="update-your-waf"></a>Aggiornare WAF

Dopo aver creato WAF, è possibile aggiornarlo usando una procedura simile a quella del codice seguente:

```azurepowershell
# Get the existing policy
$policy = Get-AzApplicationGatewayFirewallPolicy -Name $policyName -ResourceGroupName $RGname
# Add an existing rule named $rule
$policy.CustomRules.Add($rule)
# Update the policy
Set-AzApplicationGatewayFirewallPolicy -InputObject $policy
```

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni su Web Application Firewall nel gateway applicazione](ag-overview.md)
