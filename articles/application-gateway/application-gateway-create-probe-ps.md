---
title: Creare un probe personalizzato - Gateway applicazione di Azure - PowerShell | Microsoft Docs
description: Informazioni su come creare un probe personalizzato per il gateway applicazione con PowerShell in Gestione risorse
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 68feb660-7fa4-4f69-a7e4-bdd7bdc474db
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: acd70bacd23755cd764bc782a297d80db3622424
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014041"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Creare un probe personalizzato per il gateway applicazione di Azure con PowerShell per Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-create-probe-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell per Azure classico](application-gateway-create-probe-classic-ps.md)

Questo articolo illustra come aggiungere un probe personalizzato a un gateway applicazione esistente con PowerShell. I probe personalizzati sono utili per le applicazioni che dispongono di una pagina di controllo dell'integrità specifica o per quelle che non rispondono in modo corretto all'applicazione Web predefinita.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>Creare un gateway applicazione con un probe personalizzato

### <a name="sign-in-and-create-resource-group"></a>Eseguire l'accesso e creare un gruppo di risorse

1. Usare `Connect-AzAccount` per eseguire l'autenticazione.

   ```powershell
   Connect-AzAccount
   ```

1. Acquisire le sottoscrizioni per l'account.

   ```powershell
   Get-AzSubscription
   ```

1. Scegliere quali sottoscrizioni Azure usare.

   ```powershell
   Select-AzSubscription -Subscriptionid '{subscriptionGuid}'
   ```

1. Creare un gruppo di risorse. Se si dispone di un gruppo di risorse, è possibile ignorare questo passaggio.

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location 'West US'
   ```

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato **appgw-RG** nella località **West US**.

### <a name="create-a-virtual-network-and-a-subnet"></a>Creare una rete virtuale e una subnet

L'esempio seguente crea una rete virtuale e una subnet per il gateway applicazione. Per il gateway applicazione è necessaria una propria subnet. Per questo motivo, la subnet creata per il gateway applicazione deve avere dimensioni minori rispetto allo spazio degli indirizzi della rete virtuale per consentire la creazione e l'uso di altre subnet.

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creare un indirizzo IP pubblico per la configurazione front-end

Creare una risorsa IP pubblica denominata **publicIP01** nel gruppo di risorse **appgw-rg** per l'area Stati Uniti occidentali. Questo esempio usa un indirizzo IP pubblico per l'indirizzo IP front-end del gateway applicazione.  Per il gateway applicazione è necessario che l'indirizzo IP pubblico disponga di un nome DNS creato dinamicamente e di conseguenza l'elemento `-DomainNameLabel` non può essere specificato durante la creazione dell'indirizzo IP pubblico.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>Creare un gateway applicazione

È necessario impostare tutti gli elementi di configurazione prima di creare il gateway applicazione. L'esempio seguente crea gli elementi di configurazione necessari per una risorsa del gateway applicazione.

| **Componente** | **Descrizione** |
|---|---|
| **Configurazione IP del gateway** | Configurazione IP per un gateway applicazione.|
| **Pool back-end** | Pool di indirizzi IP, nomi di dominio completi o schede di interfacce di rete per i server applicazioni che ospitano l'applicazione Web.|
| **Probe di integrità** | Probe personalizzato usato per monitorare l'integrità dei membri del pool back-end|
| **Impostazioni HTTP** | Raccolta di impostazioni quali porta, protocollo, affinità basata sui cookie, probe e timeout.  Tali impostazioni determinano il modo in cui il traffico viene indirizzato ai membri del pool back-end.|
| **Porta front-end** | Porta su cui è in ascolto il gateway applicazione|
| **Listener** | Combinazione di protocollo, configurazione IP front-end e porta front-end che consente di ascoltare le richieste in ingresso.
|**Regola**| Indirizza il traffico al back-end appropriato in base alle impostazioni HTTP.|

```powershell
# Creates an application gateway Frontend IP configuration named gatewayIP01
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

#Creates a back-end IP address pool named pool01 with IP addresses 134.170.185.46, 134.170.188.221, 134.170.185.50.
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Creates a probe that will check health at http://contoso.com/path/path.htm
$probe = New-AzApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/path.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Creates the backend http settings to be used. This component references the $probe created in the previous command.
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

# Creates a frontend port for the application gateway to listen on port 80 that will be used by the listener.
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01 -Port 80

# Creates a frontend IP configuration. This associates the $publicip variable defined previously with the front-end IP that will be used by the listener.
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Creates the listener. The listener is a combination of protocol and the frontend IP configuration $fipconfig and frontend port $fp created in previous steps.
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Creates the rule that routes traffic to the backend pools.  In this example we create a basic rule that uses the previous defined http settings and backend address pool.  It also associates the listener to the rule
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Sets the SKU of the application gateway, in this example we create a small standard application gateway with 2 instances.
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# The final step creates the application gateway with all the previously defined components.
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location 'West US' -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Aggiungere un probe a un gateway applicazione esistente

Il frammento di codice seguente aggiunge un probe a un gateway applicazione esistente.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Create the probe object that will check health at http://contoso.com/path/path.htm
$getgw = Add-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/custompath.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Set the backend HTTP settings to use the new probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Rimuovere un probe da un gateway applicazione esistente

Il frammento di codice seguente rimuove un probe da un gateway applicazione esistente.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Remove the probe from the application gateway configuration object
$getgw = Remove-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

# Set the backend HTTP settings to remove the reference to the probe. The backend http settings now use the default probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>Ottenere il nome DNS del gateway applicazione

Dopo avere creato il gateway, il passaggio successivo prevede la configurazione del front-end per la comunicazione. Quando si usa un IP pubblico, il gateway applicazione richiede un nome DNS assegnato in modo dinamico, non descrittivo. Per assicurarsi che gli utenti finali possano raggiungere il gateway applicazione, è possibile usare un record CNAME per fare riferimento all'endpoint pubblico del gateway applicazione. [Configurazione di un nome di dominio personalizzato in Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). A questo scopo, recuperare i dettagli del gateway applicazione e il nome DNS e l'IP associati, usando l'elemento PublicIPAddress collegato al gateway applicazione. Il nome DNS del gateway applicazione dovrà essere usato per creare un record CNAME che associa le due applicazioni Web a questo nome DNS. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway applicazione.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla configurazione dell'offload SSL, vedere: [Configurare l'Offload SSL](application-gateway-ssl-arm.md)

