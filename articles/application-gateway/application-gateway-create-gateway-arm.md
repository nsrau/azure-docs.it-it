---
title: Creare un gateway applicazione - Azure PowerShell| Microsoft Docs
description: Informazioni su come creare un gateway applicazione usando Azure PowerShell.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurepowershell
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: 412ede9895e6684d039dcad4df4189c8b3774d76
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313821"
---
# <a name="create-an-application-gateway-using-azure-powershell"></a>Creare un gateway applicazione usando Azure PowerShell

È possibile usare Azure PowerShell per creare o gestire i gateway applicazione dalla riga di comando o in script. In questa guida introduttiva viene illustrato come creare risorse di rete, server back-end e un gateway applicazione.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, questa esercitazione richiede il modulo Azure PowerShell versione 1.0.0 o versione successiva. Per trovare la versione, eseguire `Get-Module -ListAvailable Az`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure mediante [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Creare risorse di rete 

Creare le configurazioni di subnet usando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Creare la rete virtuale usando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) con le configurazioni di subnet. Creare infine l'indirizzo IP pubblico usando [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Queste risorse vengono usate per fornire la connettività di rete al gateway applicazione e alle risorse associate.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```
## <a name="create-backend-servers"></a>Creare i server back-end

In questo esempio, vengono create due macchine virtuali da usare come server back-end per il gateway applicazione. È inoltre possibile installare IIS sulle macchine virtuali per verificare l'avvenuta creazione del gateway applicazione.

### <a name="create-two-virtual-machines"></a>Creare due macchine virtuali

Creare un'interfaccia di rete con [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Creare una configurazione di macchina virtuale con [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Quando si eseguono i comandi seguenti, viene chiesto di immettere le credenziali. Immettere *azureuser* per il nome utente e *Azure123456!* come password. Creare la macchina virtuale con [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $vnet.Subnets[1].Id
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2
  $vm = Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  $vm = Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  $vm = Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  $vm = Set-AzVMBootDiagnostics `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

### <a name="create-the-ip-configurations-and-frontend-port"></a>Creare le configurazioni IP e la porta front-end

Uso [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) per creare la configurazione che associa la subnet creata in precedenza con il gateway applicazione. Uso [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) per creare la configurazione che assegna l'indirizzo IP pubblico creato anche in precedenza al gateway applicazione. Uso [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) per assegnare la porta 80 da utilizzare per accedere al gateway applicazione.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$pip = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool"></a>Creare il pool back-end

Uso [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) per creare il pool back-end per il gateway applicazione. Configurare le impostazioni per il pool back-end usando [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>Creare il listener e aggiungere una regola

È necessario un listener per consentire al gateway applicazione di instradare il traffico in modo appropriato al pool back-end. Creare un listener con [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) con la configurazione front-end e la porta front-end creato in precedenza. È necessaria una regola per comunicare al listener quale pool back-end usare per il traffico in ingresso. Uso [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) per creare una regola denominata *rule1*.

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Creare il gateway applicazione

Ora che è stato creato le risorse di supporto necessarie, usare [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) per specificare i parametri per il gateway applicazione e quindi usare [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) per crearlo.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Uso [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) per ottenere l'indirizzo IP pubblico del gateway applicazione. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testare il gateway applicazione](./media/application-gateway-create-gateway-arm/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare la [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) comando per rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono stati creati un gruppo di risorse, le risorse di rete e i server back-end. Tali risorse sono state quindi usate per creare un gateway applicazione. Per altre informazioni sui gateway applicazione e sulle risorse associate, continuare con le procedure dettagliate.

