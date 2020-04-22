---
title: 'Guida introduttiva: Indirizzare il traffico Web con PowerShell'
titleSuffix: Azure Application Gateway
description: Informazioni su come usare Azure PowerShell per creare un gateway applicazione di Azure che indirizza il traffico Web alle macchine virtuali in un pool back-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 04/15/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 3e1ca14d967b0e88ea7eb559fd9962a3824ff9b0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406221"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Guida introduttiva: Indirizzare il traffico Web con un gateway applicazione Azure mediante Azure PowerShell

In questa guida di avvio rapido si userà Azure PowerShell per creare un gateway applicazione. Successivamente, verrà testato il gateway per verificare che funzioni correttamente. 

Il gateway applicazione indirizza il traffico Web dell'applicazione a risorse specifiche in un pool back-end. Verranno assegnati i listener alle porte, verranno create le regole e verranno aggiunte le risorse a un pool back-end. Per semplicità, questo articolo usa una configurazione semplice con un indirizzo IP front-end pubblico, un listener di base per ospitare un singolo sito nel gateway applicazione, una regola di routing delle richieste di base e due macchine virtuali nel pool back-end.

È anche possibile completare questa guida di avvio rapido usando l'[interfaccia della riga di comando di Azure](quick-create-cli.md) oppure il [portale di Azure](quick-create-portal.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Azure PowerShell versione 1.0.0 o successiva](/powershell/azure/install-az-ps) (se si esegue Azure PowerShell in locale).

## <a name="connect-to-azure"></a>Connettersi ad Azure

Per connettersi ad Azure, eseguire `Connect-AzAccount`.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

In Azure, si allocano le risorse correlate a un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno nuovo.

Per creare un nuovo gruppo di risorse, usare il cmdlet `New-AzResourceGroup`: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Creare risorse di rete

Per le comunicazioni tra le risorse create in Azure è necessaria una rete virtuale.  La subnet del gateway applicazione può contenere solo i gateway applicazione. Non sono consentite altre risorse.  È possibile creare una nuova subnet per il gateway applicazione o usarne una esistente. In questo esempio vengono create due subnet: una per il gateway applicazione e l'altra per i server back-end. L'IP front-end del gateway applicazione può essere configurato come pubblico o privato a seconda del caso d'uso. In questo esempio si sceglierà un indirizzo IP front-end pubblico.

1. Creare le configurazioni delle subnet usando `New-AzVirtualNetworkSubnetConfig`.
2. Crea la rete virtuale con le configurazioni delle subnet usando `New-AzVirtualNetwork`. 
3. Creare l'indirizzo IP pubblico usando `New-AzPublicIpAddress`. 

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

1. Usare `New-AzApplicationGatewayIPConfiguration` per creare la configurazione che associa la subnet creata al gateway applicazione. 
2. Usare `New-AzApplicationGatewayFrontendIPConfig` per creare la configurazione che assegna l'indirizzo IP pubblico creato in precedenza al gateway applicazione. 
3. Usare `New-AzApplicationGatewayFrontendPort` per assegnare la porta 80 per accedere al gateway applicazione.

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

1. Usare `New-AzApplicationGatewayBackendAddressPool` per creare il pool back-end per il gateway applicazione. Il pool back-end sarà vuoto per il momento. Quando verranno create le schede di interfaccia di rete del server back-end nella sezione successiva le si aggiungerà al pool back-end.
2. Configurare le impostazioni per il pool back-end con `New-AzApplicationGatewayBackendHttpSetting`.

```azurepowershell-interactive
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

1. Creare il listener usando `New-AzApplicationGatewayHttpListener` con la configurazione front-end e la porta front-end creata prima. 
2. Usare `New-AzApplicationGatewayRequestRoutingRule` per creare una regola denominata *rule1*. 

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

1. Usare `New-AzApplicationGatewaySku` per specificare i parametri per il gateway applicazione.
2. Usare `New-AzApplicationGateway` per creare il gateway applicazione.

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

1. Ottenere la configurazione del pool back-end del gateway applicazione creata di recente con il comando `Get-AzApplicationGatewayBackendAddressPool`.
2. Creare un'interfaccia di rete con `New-AzNetworkInterface`.
3. Creare una configurazione di macchina virtuale con `New-AzVMConfig`.
4. Creare la macchina virtuale con `New-AzVM`.

Quando si esegue l'esempio di codice seguente per creare le macchine virtuali, Azure richiede le credenziali. Immettere *azureuser* per il nome utente e la password:
    
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

1. Eseguire `Get-AzPublicIPAddress` per ottenere l'indirizzo IP pubblico del gateway applicazione. 
2. Copiare e incollare l'indirizzo IP pubblico nella barra degli indirizzi del browser. Quando si aggiorna il browser, dovrebbe apparire il nome della macchina virtuale. Una risposta valida verifica che il gateway applicazione sia stato creato correttamente e possa connettersi al back-end.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testare il gateway applicazione](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il gateway applicazione non sono più necessarie, eliminare il gruppo di risorse. L'eliminazione del gruppo di risorse comporta anche l'eliminazione del gateway applicazione e di tutte le risorse correlate. 

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire il traffico Web con un gateway applicazione tramite Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)

