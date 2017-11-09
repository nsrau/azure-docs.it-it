---
title: 'Configurare un web application firewall: gateway applicazione di Azure | Microsoft Docs'
description: Questo articolo fornisce indicazioni su come iniziare a usare il firewall applicazione Web in un gateway applicazione nuovo o esistente.
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: e8106805d21b325e33fb3ab376db75cd783b9042
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Configurare un web application firewall su un gateway applicazione nuovo o esistente

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Interfaccia della riga di comando di Azure](application-gateway-web-application-firewall-cli.md)

Informazioni su come creare un gateway applicazione con web application firewall (WAF) abilitato. Leggere anche le informazioni su come aggiungere un WAF a un gateway applicazione esistente.

Il WAF nel gateway applicazione di Azure protegge le applicazioni Web dai comuni attacchi basati sul Web, come ad esempio gli attacchi SQL injection, gli attacchi di scripting intersito e il controllo delle sessioni.

 Il gateway applicazione è un servizio di bilanciamento del carico di livello 7. Fornisce richieste HTTP con routing delle prestazioni e failover tra server diversi, sia nel cloud che in locale. Il gateway applicazione offre numerose funzionalità di controller per la distribuzione di applicazioni (ADC, Application Delivery Controller):

 * Bilanciamento del carico HTTP
 * Affinità di sessione basata su cookie
 * Offload Secure Sockets Layer (SSL)
 * Probe di integrità personalizzati
 * Supporto per la funzionalità multisito
 
 Per un elenco completo delle funzionalità supportate, vedere [Panoramica del gateway applicazione](application-gateway-introduction.md).

Questo articolo mostra come [aggiungere un WAF a un gateway applicazione esistente](#add-web-application-firewall-to-an-existing-application-gateway). Illustra anche come [creare un gateway applicazione che usa un WAF](#create-an-application-gateway-with-web-application-firewall).

![Immagine dello scenario][scenario]

## <a name="waf-configuration-differences"></a>Differenze di configurazione dei WAF

Se è stato letto l'argomento [Creare un gateway applicazione con PowerShell](application-gateway-create-gateway-arm.md), si conoscono le impostazioni della SKU da configurare quando si crea un gateway applicazione. Il WAF fornisce impostazioni aggiuntive da definire quando si configura uno SKU in un gateway applicazione. Non esistono altre modifiche aggiuntive da apportare sul gateway applicazione.

| **Impostazione** | **Dettagli**
|---|---|
|**SKU** |Un gateway applicazione normale senza WAF supporta le dimensioni **Standard\_Small**, **Standard\_Medium** e **Standard\_Large**. Con l'introduzione di un WAF sono disponibili due SKU aggiuntive, **WAF\_Medium** e **WAF\_Large**. Un WAF non è supportato nei gateway applicazione Small.|
|**Livello** | I valori disponibili sono **Standard** o **WAF**. Quando si usa un WAF, è necessario scegliere **WAF**.|
|**Modalità** | Questa impostazione è la modalità del WAF. I valori consentiti sono **Rilevamento** e **Prevenzione**. Quando il firewall WAF è configurato in modalità **Rilevamento**, tutte le minacce vengono archiviate in un file di log. In modalità **Prevenzione**, gli eventi vengono comunque registrati, ma l'autore dell'attacco riceve una risposta di mancata autorizzazione 403 dal gateway applicazione.|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>Aggiungere un web application firewall a un gateway applicazione esistente

Assicurarsi di usare la versione più recente di Azure PowerShell. Per altre informazioni, vedere [Usare Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

1. Accedere all'account Azure.

    ```powershell
    Login-AzureRmAccount
    ```

2. Selezionare la sottoscrizione da usare per questo scenario.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. Recuperare il gateway dove si desidera aggiungere un WAF.

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

4. Configurare lo SKU WAF. Le dimensioni disponibili sono **WAF\_Large** e **WAF\_Medium**. Quando si utilizza un WAF, il livello deve essere **WAF**. Verificare la capacità quando si imposta la SKU.

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

5. Configurare le impostazioni WAF nel modo definito nell'esempio seguente. Per**FirewallMode**, i valori disponibili sono **Prevenzione** e **Rilevamento**.

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

6. Aggiornare il gateway applicazione con le impostazioni definite nel passaggio precedente.

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

Questo comando aggiorna il gateway applicazione con un WAF. Per informazioni su come visualizzare i log per il gateway applicazione, vedere l'argomento relativo alla [diagnostica del gateway applicazione](application-gateway-diagnostics.md). Per le implicazioni di sicurezza del WAF, esaminare i log a intervalli regolari per essere aggiornati sulle condizioni di sicurezza delle applicazioni Web.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Creare un gateway applicazione con un web application firewall

I passaggi seguenti illustrano l'intero processo di creazione di un gateway applicazione con un WAF.

Assicurarsi di usare la versione più recente di Azure PowerShell. Per altre informazioni, vedere [Usare Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

1. Accedi ad Azure eseguendo `Login-AzureRmAccount`. Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.

2. Controllare le sottoscrizioni per l'account eseguendo `Get-AzureRmSubscription`.

3. Scegliere quale sottoscrizione di Azure usare.

    ```powershell
    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
    ```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse per il gateway applicazione.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "appgw-RG" con la località "Stati Uniti occidentali".

> [!NOTE]
> Se è necessario configurare un probe personalizzato per il gateway applicazione, vedere [Creare un probe personalizzato per il gateway applicazione di Azure con PowerShell per Azure Resource Manager](application-gateway-create-probe-ps.md). Per altre informazioni, vedere l'articolo relativo a [probe personalizzati e monitoraggio dell'integrità](application-gateway-probe-overview.md).

### <a name="configure-a-virtual-network"></a>Configurare una rete virtuale

Il gateway applicazione richiede una propria subnet. In questo passaggio si crea una rete virtuale con uno spazio degli indirizzi di 10.0.0.0/16 e due subnet, una per il gateway applicazione e una per i membri del pool di back-end.

```powershell
# Create a subnet configuration object for the application gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for application gateway instances. With a smaller subnet, you might not be able to add more instances of your application gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the back-end pool members subnet.
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previously created subnets.
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="configure-the-public-ip-address"></a>Configurare l'indirizzo IP pubblico

Per gestire le richieste esterne, il gateway applicazione richiede un indirizzo IP pubblico. Questo indirizzo IP pubblico non deve avere un `DomainNameLabel` definito per essere usato dal gateway applicazione.

```powershell
# Create a public IP address for use with the application gateway. Defining the `DomainNameLabel` during creation is not supported for use with the application gateway.
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

### <a name="configure-the-application-gateway"></a>Configurare il gateway applicazione

```powershell
# Create an IP configuration to configure which subnet the application gateway uses. When the application gateway starts, it picks up an IP address from the configured subnet and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a back-end pool to hold the addresses or NIC handles for the application that the application gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authentication certificate to be used to communicate with the back-end servers.
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Configure the back-end HTTP settings to be used to define how traffic is routed to the back-end pool. The authentication certificate used in the previous step is added to the back-end HTTP settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a front-end port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a front-end IP configuration to associate the public IP address with the application gateway.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the application gateway. This certificate is used to decrypt and re-encrypt the traffic on the application gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the application gateway. Assign the front-end IP configuration, port, and SSL certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a load-balancer routing rule that configures the load balancer behavior. In this example, a basic round-robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the application gateway.
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

# Define the SSL policy to use.
$policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Configure the WAF configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the application gateway by using all the previously created configuration objects.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> I gateway applicazione creati con la configurazione di base del WAF sono configurati con CRS 3.0 per la protezione.

## <a name="get-an-application-gateway-dns-name"></a>Ottenere un nome DNS del gateway applicazione

Dopo avere creato il gateway, il passaggio successivo prevede la configurazione del front-end per la comunicazione. Quando si usa un IP pubblico, il gateway applicazione richiede un nome DNS assegnato in modo dinamico, non descrittivo. Per assicurarsi che gli utenti possano raggiungere il gateway applicazione, usare un record CNAME che faccia riferimento all'endpoint pubblico del gateway applicazione. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato per un servizio cloud di Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Per configurare un alias, recuperare i dettagli del gateway applicazione e il relativo nome DNS/indirizzo IP usando l'elemento PublicIPAddress collegato al gateway applicazione. Usare il nome DNS del gateway applicazione per creare un record CNAME che associ le due applicazioni Web a questo nome DNS. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway applicazione.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
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

Per informazioni su come configurare la registrazione diagnostica, per registrare gli eventi che vengono rilevati o bloccati con un WAF, visitare [Integrità back-end, log di diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md).

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png
