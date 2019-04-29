---
title: Proteggere le app Web con il gateway applicazione Azure - PowerShell
description: Questo articolo offre indicazioni su come configurare le app Web come host back-end in un gateway applicazione nuovo o esistente.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
origin.date: 10/16/2018
ms.date: 03/12/2019
ms.author: v-junlch
ms.openlocfilehash: dcf21fe111ab742074ab4fe580a021338e1f7c43
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122218"
---
# <a name="configure-app-service-with-application-gateway"></a>Configurare il servizio app con il gateway applicazione

Il gateway applicazione consente di usare un'app Servizio app o un altro servizio multi-tenant come membro del pool back-end. Questo articolo descrive come configurare un'app Servizio app con il gateway applicazione. Il primo esempio illustra come configurare un gateway applicazione esistente per usare un'app Web come membro del pool back-end. Il secondo esempio illustra come creare un nuovo gateway applicazione con un'app Web come membro del pool back-end.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-web-app-behind-an-existing-application-gateway"></a>Configurare un'app Web dietro un gateway applicazione esistente

L'esempio seguente aggiunge un'app Web come membro del pool back-end in un gateway applicazione esistente. Per il funzionamento delle app Web, è necessario specificare sia l'opzione `-PickHostNamefromBackendHttpSettings` nella configurazione del probe che `-PickHostNameFromBackendAddress` nelle impostazioni HTTP del back-end.

```powershell
# FQDN of the web app
$webappFQDN = "<enter your webapp FQDN i.e mywebsite.chinacloudsites.cn>"

# Retrieve the resource group
$rg = Get-AzResourceGroup -Name 'your resource group name'

# Retrieve an existing application gateway
$gw = Get-AzApplicationGateway -Name 'your application gateway name' -ResourceGroupName $rg.ResourceGroupName

# Define the status codes to match for the probe
$match=New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399

# Add a new probe to the application gateway
Add-AzApplicationGatewayProbeConfig -name webappprobe2 -ApplicationGateway $gw -Protocol Http -Path / -Interval 30 -Timeout 120 -UnhealthyThreshold 3 -PickHostNameFromBackendHttpSettings -Match $match

# Retrieve the newly added probe
$probe = Get-AzApplicationGatewayProbeConfig -name webappprobe2 -ApplicationGateway $gw

# Configure an existing backend http settings
Set-AzApplicationGatewayBackendHttpSettings -Name appGatewayBackendHttpSettings -ApplicationGateway $gw -PickHostNameFromBackendAddress -Port 80 -Protocol http -CookieBasedAffinity Disabled -RequestTimeout 30 -Probe $probe

# Add the web app to the backend pool
Set-AzApplicationGatewayBackendAddressPool -Name appGatewayBackendPool -ApplicationGateway $gw -BackendFqdns $webappFQDN

# Update the application gateway
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="configure-a-web-application-behind-a-new-application-gateway"></a>Configurare un'applicazione Web dietro un nuovo gateway applicazione

Questo scenario distribuisce un'app Web con il sito Web introduttivo ASP.NET e un gateway applicazione.

```powershell
# Defines a variable for a dotnet get started web app repository location
$gitrepo="https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git"

# Unique web app name
$webappname="mywebapp$(Get-Random)"

# Creates a resource group
$rg = New-AzResourceGroup -Name ContosoRG -Location ChinaNorth

# Create an App Service plan in Free tier.
New-AzAppServicePlan -Name $webappname -Location ChinaNorth -ResourceGroupName $rg.ResourceGroupName -Tier Free

# Creates a web app
$webapp = New-AzWebApp -ResourceGroupName $rg.ResourceGroupName -Name $webappname -Location ChinaNorth -AppServicePlan $webappname

# Configure GitHub deployment from your GitHub repo and deploy once to web app.
$PropertiesObject = @{
    repoUrl = "$gitrepo";
    branch = "master";
    isManualIntegration = "true";
}
Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName $rg.ResourceGroupName -ResourceType Microsoft.Web/sites/sourcecontrols -ResourceName $webappname/web -ApiVersion 2015-08-01 -Force

# Creates a subnet for the application gateway
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Creates a vnet for the application gateway
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName $rg.ResourceGroupName -Location ChinaNorth -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Retrieve the subnet object for use later
$subnet=$vnet.Subnets[0]

# Create a public IP address
$publicip = New-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -name publicIP01 -location ChinaNorth -AllocationMethod Dynamic

# Create a new IP configuration
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

# Create a backend pool with the hostname of the web app
$pool = New-AzApplicationGatewayBackendAddressPool -Name appGatewayBackendPool -BackendFqdns $webapp.HostNames

# Define the status codes to match for the probe
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399

# Create a probe with the PickHostNameFromBackendHttpSettings switch for web apps
$probeconfig = New-AzApplicationGatewayProbeConfig -name webappprobe -Protocol Http -Path / -Interval 30 -Timeout 120 -UnhealthyThreshold 3 -PickHostNameFromBackendHttpSettings -Match $match

# Define the backend http settings
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name appGatewayBackendHttpSettings -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120 -PickHostNameFromBackendAddress -Probe $probeconfig

# Create a new front-end port
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 80

# Create a new front end IP configuration
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Create a new listener using the front-end ip configuration and port created earlier
$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Create a new rule
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Define the application gateway SKU to use
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# Create the application gateway
$appgw = New-AzApplicationGateway -Name ContosoAppGateway -ResourceGroupName $rg.ResourceGroupName -Location ChinaNorth -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -Probes $probeconfig -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="get-application-gateway-dns-name"></a>Ottenere il nome DNS del gateway applicazione

Dopo avere creato il gateway, il passaggio successivo prevede la configurazione del front-end per la comunicazione. Quando si usa un IP pubblico, il gateway applicazione richiede un nome DNS assegnato in modo dinamico, non descrittivo. Per assicurarsi che gli utenti finali possano raggiungere il gateway applicazione, è possibile usare un record CNAME per fare riferimento all'endpoint pubblico del gateway applicazione. Per creare l'alias, recuperare i dettagli del gateway applicazione e il nome DNS e l'IP associati usando l'elemento PublicIPAddress collegato al gateway applicazione. Questa operazione può essere eseguita con il servizio DNS di Azure o altri provider DNS, creando un record CNAME che punti all'[indirizzo IP pubblico](../dns/dns-custom-domain.md#public-ip-address). Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway applicazione.

```powershell
Get-AzPublicIpAddress -ResourceGroupName ContosoRG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : ContosoRG
Location                 : chinanorth
Id                       : /subscriptions/<subscription_id>/resourceGroups/ContosoRG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/ContosoAppGateway/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.chinacloudapp.cn"
                            }
```

## <a name="restrict-access"></a>Limitare l'accesso

L'app Web distribuita in questi esempi usa indirizzi IP pubblici a cui è possibile accedere direttamente da Internet. Questo facilita la risoluzione dei problemi quando si sta imparando una nuova funzionalità e si provano le novità. Ma se si prevede di distribuire una funzionalità in produzione, sarà necessario aggiungere altre restrizioni.

Un modo per limitare l'accesso alle app Web è usare le [Restrizioni IP statico del Servizio app di Azure](../app-service/app-service-ip-restrictions.md). Ad esempio, è possibile limitare l'app Web in modo che riceva solo il traffico del gateway applicazione. Usare la funzionalità di restrizione IP del servizio app per aggiungere l'indirizzo VIP di un gateway applicazione come unico indirizzo con accesso.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare il reindirizzamento, visitare la pagina: [Configurare il reindirizzamento nel gateway applicazione con PowerShell](redirect-overview.md).

<!-- Update_Description: code update -->