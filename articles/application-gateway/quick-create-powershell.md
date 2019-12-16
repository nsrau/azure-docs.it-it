---
title: 'Guida introduttiva: Indirizzare il traffico Web con PowerShell'
titleSuffix: Azure Application Gateway
description: Informazioni su come usare Azure PowerShell per creare un gateway applicazione di Azure che indirizza il traffico Web alle macchine virtuali in un pool back-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 9c3fac7aecaf37b5822ad6e8c655867f6f2c683c
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872707"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Guida introduttiva: Indirizzare il traffico Web con un gateway applicazione Azure mediante Azure PowerShell

Questa guida di avvio rapido illustra come usare Azure PowerShell per creare rapidamente un gateway applicazione.  Al termine della creazione, si testa il gateway applicazione per verificare che funzioni correttamente. Con il gateway applicazione di Azure si indirizza il traffico Web dell'applicazione a risorse specifiche assegnando listener alle porte, creando regole e aggiungendo risorse a un pool back-end. Per semplicità, questo articolo usa una configurazione semplice con un indirizzo IP pubblico front-end, un listener di base per ospitare un singolo sito nel gateway applicazione, due macchine virtuali usate per il pool back-end e una regola di routing delle richieste di base.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-powershell-module"></a>Modulo di Azure PowerShell

Se si sceglie di installare e usare Azure PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 1.0.0 o successiva.

1. Per trovare la versione, eseguire `Get-Module -ListAvailable Az`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). 
2. Eseguire `Login-AzAccount` per creare una connessione con Azure.

### <a name="resource-group"></a>Resource group

In Azure, si allocano le risorse correlate a un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno nuovo. In questo esempio si creerà un nuovo gruppo di risorse usando il cmdlet [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) come segue: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

### <a name="required-network-resources"></a>Risorse di rete necessarie

Per le comunicazioni tra le risorse create in Azure è necessaria una rete virtuale.  La subnet del gateway applicazione può contenere solo i gateway applicazione. Non sono consentite altre risorse.  È possibile creare una nuova subnet per il gateway applicazione o usarne una esistente. In questo esempio vengono create due subnet: una per il gateway applicazione e l'altra per i server back-end. L'IP front-end del gateway applicazione può essere configurato come pubblico o privato a seconda del caso d'uso. In questo esempio si sceglierà un indirizzo IP front-end pubblico.

1. Creare le configurazioni di subnet chiamando [New-AzVirtualNetworkSubnetConfig](/powershell/module/Az.network/new-Azvirtualnetworksubnetconfig).
2. Creare la rete virtuale con le configurazioni di subnet chiamando [New-AzVirtualNetwork](/powershell/module/Az.network/new-Azvirtualnetwork). 
3. Creare l'indirizzo IP pubblico chiamando [New-AzPublicIpAddress](/powershell/module/Az.network/new-Azpublicipaddress). 

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```
## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

### <a name="create-the-ip-configurations-and-frontend-port"></a>Creare le configurazioni IP e la porta front-end

1. Usare [New-AzApplicationGatewayIPConfiguration](/powershell/module/Az.network/new-Azapplicationgatewayipconfiguration) per creare la configurazione che associa la subnet creata al gateway applicazione. 
2. Usare [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/Az.network/new-Azapplicationgatewayfrontendipconfig) per creare la configurazione che assegna l'indirizzo IP pubblico creato in precedenza al gateway applicazione. 
3. Usare [New-AzApplicationGatewayFrontendPort](/powershell/module/Az.network/new-Azapplicationgatewayfrontendport) per assegnare la porta 80 per l'accesso al gateway applicazione.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
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

1. Usare [New-AzApplicationGatewayBackendAddressPool](/powershell/module/Az.network/new-Azapplicationgatewaybackendaddresspool) per creare il pool back-end per il gateway applicazione. Il pool back-end sarà vuoto per il momento e quando verranno create le schede di interfaccia di rete del server back-end nella sezione successiva le si aggiungerà al pool back-end.
2. Configurare le impostazioni per il pool back-end con [New-AzApplicationGatewayBackendHttpSetting](/powershell/module/Az.network/new-Azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>Creare il listener e aggiungere una regola

Azure richiede un listener per consentire al gateway applicazione di instradare il traffico in modo appropriato al pool back-end. È necessaria inoltre una regola per comunicare al listener quale pool back-end usare per il traffico in ingresso. 

1. Creare un listener usando [New-AzApplicationGatewayHttpListener](/powershell/module/Az.network/new-Azapplicationgatewayhttplistener) con la configurazione front-end e la porta front-end creata prima. 
2. Usare [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/Az.network/new-Azapplicationgatewayrequestroutingrule) per creare una regola denominata *rule1*. 

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

Dopo aver creato le risorse di supporto necessarie, creare il gateway applicazione:

1. Usare [New-AzApplicationGatewaySku](/powershell/module/Az.network/new-Azapplicationgatewaysku) per specificare i parametri per il gateway applicazione.
2. Usare [New-AzApplicationGateway](/powershell/module/Az.network/new-Azapplicationgateway) per creare il gateway applicazione.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
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

### <a name="backend-servers"></a>Server back-end

Dopo aver creato il gateway applicazione, creare le macchine virtuali back-end che ospiteranno i siti Web. Il back-end può essere costituito da schede di interfaccia di rete, set di scalabilità di macchine virtuali, IP pubblici, IP interni, nomi di dominio completi (FQDN) e back-end multi-tenant come Servizio app di Azure. In questo esempio vengono create due macchine virtuali per Azure da usare come server back-end per il gateway applicazione. È anche possibile installare IIS nelle macchine virtuali per verificare l'avvenuta creazione del gateway applicazione in Azure.

#### <a name="create-two-virtual-machines"></a>Creare due macchine virtuali

1. Ottenere la configurazione del pool back-end del gateway applicazione creata di recente con il comando [Get-AzApplicationGatewayBackendAddressPool](/powershell/module/Az.network/get-Azapplicationgatewaybackendaddresspool)
2. Creare un'interfaccia di rete con [New-AzNetworkInterface](/powershell/module/Az.network/new-Aznetworkinterface). 
3. Creare una configurazione di macchina virtuale con [New-AzVMConfig](/powershell/module/Az.compute/new-Azvmconfig).
4. Creare la macchina virtuale con [New-AzVM](/powershell/module/Az.compute/new-Azvm).

Quando si esegue l'esempio di codice seguente per creare le macchine virtuali, Azure richiede le credenziali. Immettere *azureuser* per il nome utente e *Azure123456!* per la password:
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
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

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Nonostante l'installazione di IIS non sia necessaria per creare il gateway applicazione, è stata eseguita in questa guida introduttiva per verificare se il gateway applicazione è stato creato correttamente in Azure. Usare IIS per testare il gateway applicazione:

1. Eseguire [Get-AzPublicIPAddress](/powershell/module/Az.network/get-Azpublicipaddress) per ottenere l'indirizzo IP pubblico del gateway applicazione. 
2. Copiare e incollare l'indirizzo IP pubblico nella barra degli indirizzi del browser. Quando si aggiorna il browser, dovrebbe apparire il nome della macchina virtuale. Una risposta valida verifica che il gateway applicazione sia stato creato correttamente e possa connettersi al back-end.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testare il gateway applicazione](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il gateway applicazione non sono più necessarie, rimuovere il gruppo di risorse. La rimozione del gruppo di risorse comporta anche la rimozione del gateway applicazione e di tutte le risorse correlate. 

Per rimuovere il gruppo di risorse, chiamare il cmdlet [Remove-AzResourceGroup](/powershell/module/Az.resources/remove-Azresourcegroup) come segue:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire il traffico Web con un gateway applicazione tramite Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)

