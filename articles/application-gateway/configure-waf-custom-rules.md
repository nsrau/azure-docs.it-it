---
title: Configurare le regole personalizzate di web application firewall V2 usando Azure PowerShell
description: Informazioni su come configurare le regole personalizzate di web application firewall V2 usando Azure PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 9e50b47e22f5760c213cd0cafad82ecca592dec8
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263737"
---
# <a name="configure-web-application-firewall-v2-custom-rules-by-using-azure-powershell"></a>Configurare le regole personalizzate di web application firewall V2 usando Azure PowerShell

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

Con le regole personalizzate è possibile creare regole personalizzate, che vengono valutate per ogni richiesta che passa attraverso il web application firewall (WAF). Queste regole hanno una priorità più elevata rispetto alle altre regole nei set di regole gestiti. Per consentire la personalizzazione completa, le regole personalizzate hanno un'azione (per consentire o bloccare), una condizione di corrispondenza e un operatore.

Questo articolo crea un applicazione Azure gateway WAF V2 che usa una regola personalizzata. La regola personalizzata blocca il traffico se l'intestazione della richiesta contiene l'*evilbot* User-Agent.

Per visualizzare altri esempi di regole personalizzate, vedere [creare e usare regole Web Application Firewall personalizzate](create-custom-waf-rules.md).

Per eseguire il codice Azure PowerShell in questo articolo in uno script continuo che è possibile copiare, incollare ed eseguire, vedere [esempi di PowerShell per applicazione Azure gateway](powershell-samples.md).

## <a name="prerequisites"></a>Prerequisiti
* [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* È necessario un modulo Azure PowerShell. Se si sceglie di installare e usare Azure PowerShell localmente, questo script richiede Azure PowerShell modulo versione 2.1.0 o successiva. Eseguire le operazioni seguenti:

  1. Per trovare la versione, eseguire `Get-Module -ListAvailable Az`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps).
  2. Eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="example-script"></a>Script di esempio

### <a name="set-up-variables"></a>Configurare le variabili

Eseguire il codice seguente:

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Eseguire il codice seguente:

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Eseguire il codice seguente:

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>Creare un indirizzo VIP pubblico statico

Eseguire il codice seguente:

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-a-pool-and-front-end-port"></a>Creare un pool e una porta front-end

Eseguire il codice seguente:

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>Creare un listener, un'impostazione HTTP, una regola e una scalabilità automatica

Eseguire il codice seguente:

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

### <a name="create-the-custom-rule-and-apply-it-to-waf-policy"></a>Creare la regola personalizzata e applicarla ai criteri di WAF

Eseguire il codice seguente:

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  

$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block

$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafPolicy -ResourceGroup $rgname -Location $location -CustomRule $rule

$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="create-an-application-gateway"></a>Creare un gateway applicazione

Eseguire il codice seguente:

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

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni su web application firewall](waf-overview.md)
