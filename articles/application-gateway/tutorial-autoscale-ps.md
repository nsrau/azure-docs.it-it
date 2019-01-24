---
title: 'Esercitazione: Creare un gateway applicazione con ridondanza della zona e scalabilità automatica con un indirizzo IP riservato - Azure PowerShell'
description: In questa esercitazione vengono fornite informazioni su come creare un gateway applicazione con ridondanza della zona e scalabilità automatica con un indirizzo IP riservato tramite Azure PowerShell.
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: dd6cc65fca98bc435a8cfea575ba10e3cff376be
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54424677"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>Esercitazione: creare un gateway applicazione per migliorare l'accesso alle applicazioni Web

Gli amministratori IT che desiderano migliorare l'accesso alle applicazioni Web possono ottimizzare il gateway applicazione in modo da ridimensionarsi in base alla richiesta dei clienti ed estendersi a più zone di disponibilità. Questa esercitazione aiuta a configurare funzioni del gateway applicazione di Azure per eseguire operazioni di scalabilità automatica, ridondanza della zona e indirizzi VIP riservati (indirizzi IP statici). Per risolvere il problema si useranno cmdlet di Azure PowerShell e il modello di distribuzione Azure Resource Manager.

> [!IMPORTANT] 
> Lo SKU del gateway applicazione con scalabilità automatica e ridondanza della zona è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una rete virtuale con scalabilità automatica
> * Creare un indirizzo IP pubblico riservato
> * Configurare l'infrastruttura del gateway applicazione
> * Specificare la scalabilità automatica
> * Creare il gateway applicazione
> * Testare il gateway applicazione

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Ai fini di questa esercitazione, è necessario eseguire Azure PowerShell in locale. Deve essere installato il modulo Azure PowerShell 6.9.0 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Dopo avere verificato la versione di PowerShell, eseguire `Login-AzureRmAccount` per creare una connessione ad Azure.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un gruppo di risorse in una delle località disponibili.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzureRmResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale con una subnet dedicata per un gateway applicazione con scalabilità automatica. Attualmente è possibile distribuire un solo gateway applicazione con scalabilità automatica in ogni subnet dedicata.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzureRmVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Creare un indirizzo IP pubblico riservato

Specificare il metodo di allocazione dell'indirizzo IP pubblico come **Statico**. L'indirizzo VIP di un gateway applicazione con scalabilità automatica può essere solo statico. Gli indirizzi IP dinamici non sono supportati. È supportata solo la SKU PublicIpAddress Standard.

```azurepowershell
#Create static public IP
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Recuperare i dettagli

Recuperare i dettagli del gruppo di risorse, della subnet e dell'indirizzo IP in un oggetto locale per creare i dettagli di configurazione IP del gateway applicazione.

```azurepowershell
$resourceGroup = Get-AzureRmResourceGroup -Name $rg
$publicip = Get-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Configurare l'infrastruttura

Impostare la configurazione IP, la configurazione IP front-end, il pool back-end, le impostazioni HTTP, il certificato, la porta, il listener e la regola in un formato identico a quello del gateway applicazione Standard esistente. La nuova SKU segue lo stesso modello a oggetti della SKU Standard.

```azurepowershell
$ipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzureRmApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzureRmApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "scrap" -AsPlainText -Force
$sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "D:\Networking\ApplicationGateway\scrap.pfx"
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>Specificare la scalabilità automatica

È ora possibile specificare la configurazione della scalabilità automatica per il gateway applicazione. Sono supportati due tipi di configurazione della scalabilità automatica:

* **Modalità con capacità fissa**. In questa modalità il gateway applicazione non si ridimensiona automaticamente e opera a una capacità di unità di scala fissa.

   ```azurepowershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Modalità di scalabilità automatica**. In questa modalità il gateway applicazione si ridimensiona automaticamente in base al modello di traffico dell'applicazione.

   ```azurepowershell
   $autoscaleConfig = New-AzureRmApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

Creare il gateway applicazione e includere le zone di ridondanza e la configurazione di scalabilità automatica.

```azurepowershell
$appgw = New-AzureRmApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Usare Get-AzureRmPublicIPAddress per ottenere l'indirizzo IP pubblico del gateway applicazione. Copiare l'indirizzo IP pubblico o il nome DNS e quindi incollarlo nella barra degli indirizzi del browser.

`Get-AzureRmPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>Pulire le risorse

Innanzitutto, esplorare le risorse che sono state create con il gateway applicazione. Successivamente, quando non servono più, è possibile usare il comando `Remove-AzureRmResourceGroup` per rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate.

`Remove-AzureRmResourceGroup -Name $rg`

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un gateway applicazione con regole di routing basate su percorsi URL](./tutorial-url-route-powershell.md)
