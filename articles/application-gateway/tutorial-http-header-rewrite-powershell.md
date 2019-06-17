---
title: Riscrivere le intestazioni HTTP in un gateway applicazione di Azure
description: Questo articolo fornisce informazioni su come creare un gateway applicazione di Azure e riscrivere le intestazioni HTTP usando Azure PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.openlocfilehash: ba74bb8970949a15425a66f7cd4475749fd183df
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64947099"
---
# <a name="create-an-application-gateway-and-rewrite-http-headers"></a>Creare un gateway applicazione e riscrivere intestazioni HTTP

È possibile usare Azure PowerShell per configurare le [regole per riscrivere le intestazioni di richiesta e risposta HTTP](rewrite-http-headers.md) quando si crea il nuovo [SKU del gateway applicazione con scalabilità automatica e ridondanza della zona](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

In questo articolo viene spiegato come:

> [!div class="checklist"]
>
> * Creare una rete virtuale con scalabilità automatica
> * Creare un indirizzo IP pubblico riservato
> * Configurare l'infrastruttura del gateway applicazione
> * Specificare la configurazione della regola di riscrittura dell'intestazione http
> * Specificare la scalabilità automatica
> * Creare il gateway applicazione
> * Testare il gateway applicazione

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo è necessario eseguire Azure PowerShell in locale. Deve essere installato il modulo Az 1.0.0 o versione successiva. Eseguire `Import-Module Az` e quindi `Get-Module Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Dopo avere verificato la versione di PowerShell, eseguire `Login-AzAccount` per creare una connessione ad Azure.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un gruppo di risorse in una delle località disponibili.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale con una subnet dedicata per un gateway applicazione con scalabilità automatica. Attualmente è possibile distribuire un solo gateway applicazione con scalabilità automatica in ogni subnet dedicata.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Creare un indirizzo IP pubblico riservato

Specificare il metodo di allocazione dell'indirizzo IP pubblico come **Statico**. L'indirizzo VIP di un gateway applicazione con scalabilità automatica può essere solo statico. Gli indirizzi IP dinamici non sono supportati. È supportata solo la SKU PublicIpAddress Standard.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Recuperare i dettagli

Recuperare i dettagli del gruppo di risorse, della subnet e dell'indirizzo IP in un oggetto locale per creare i dettagli di configurazione IP del gateway applicazione.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Configurare l'infrastruttura

Impostare la configurazione IP, la configurazione IP front-end, il pool back-end, le impostazioni HTTP, il certificato, la porta e il listener in un formato identico a quello del gateway applicazione Standard esistente. La nuova SKU segue lo stesso modello a oggetti della SKU Standard.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Specificare la configurazione della regola di riscrittura dell'intestazione HTTP

Configurare i nuovi oggetti necessari per riscrivere le intestazioni HTTP:

- **RequestHeaderConfiguration**: questo oggetto viene usato per specificare i campi di intestazione della richiesta che si intende riscrivere e il nuovo valore da sostituire alle intestazioni originali.
- **ResponseHeaderConfiguration**: questo oggetto viene usato per specificare i campi di intestazione della risposta che si intende riscrivere e il nuovo valore da sostituire alle intestazioni originali.
- **ActionSet**: questo oggetto contiene le configurazioni delle intestazioni di richiesta e risposta specificate in precedenza. 
- **RewriteRule**: questo oggetto contiene tutti gli *ActionSet* specificati in precedenza. 
- **RewriteRuleSet**: questo oggetto contiene tutte le *RewriteRule* e deve essere collegato a una regola di routing delle richieste, di base o basata sul percorso.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Specificare la regola di routing

Creare una regola di routing della richiesta. Dopo la creazione, questa configurazione di riscrittura viene collegata al listener di origine tramite la regola di routing. Quando si usa una regola di routing di base, la configurazione di riscrittura delle intestazioni viene associata a un listener di origine e la riscrittura è globale. Quando viene usata una regola di routing basata sul percorso, la configurazione di riscrittura delle intestazioni è definita sulla mappa del percorso URL. Pertanto, si applica solo all'area del percorso specifico di un sito. Di seguito viene creata una regola di routing di base e viene collegato il set di regole di riscrittura.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Specificare la scalabilità automatica

È ora possibile specificare la configurazione della scalabilità automatica per il gateway applicazione. Sono supportati due tipi di configurazione della scalabilità automatica:

* **Modalità con capacità fissa**. In questa modalità il gateway applicazione non si ridimensiona automaticamente e opera a una capacità di unità di scala fissa.

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Modalità di scalabilità automatica**. In questa modalità il gateway applicazione si ridimensiona automaticamente in base al modello di traffico dell'applicazione.

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

Creare il gateway applicazione e includere le zone di ridondanza e la configurazione di scalabilità automatica.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Usare Get-AzPublicIPAddress per ottenere l'indirizzo IP pubblico del gateway applicazione. Copiare l'indirizzo IP pubblico o il nome DNS e quindi incollarlo nella barra degli indirizzi del browser.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Pulire le risorse

Innanzitutto, esplorare le risorse che sono state create con il gateway applicazione. Successivamente, quando non servono più, è possibile usare il comando `Remove-AzResourceGroup` per rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Passaggi successivi

- [Creare un gateway applicazione con regole di routing basate su percorsi URL](./tutorial-url-route-powershell.md)
