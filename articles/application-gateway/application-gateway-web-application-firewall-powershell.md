---
title: Configurare un Web application firewall - Gateway applicazione di Azure | Documentazione Microsoft
description: Questo articolo fornisce indicazioni su come iniziare a usare il firewall applicazione Web in un gateway applicazione nuovo o esistente.
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: bcff92a362c7f8ad1c69b93af07d77e0d03b6a92


---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Configurare un firewall applicazione Web su un gateway applicazione nuovo o esistente

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-web-application-firewall-powershell.md)

Il firewall applicazione Web (WAF) nel gateway applicazione di Azure protegge le applicazioni Web dai comuni attacchi basati sul Web, come ad esempio gli attacchi SQL injection, gli attacchi di scripting intersito e il controllo delle sessioni.

Il gateway applicazione di Azure è un dispositivo di bilanciamento del carico di livello&7;. Fornisce richieste HTTP con routing delle prestazioni e failover tra server diversi, sia nel cloud che in locale. L'applicazione offre numerose funzionalità di controller per la distribuzione di applicazioni (ADC, Application Delivery Controller), tra cui bilanciamento del carico HTTP, affinità di sessione basata su cookie, offload SSL (Secure Sockets Layer), probe di integrità personalizzati, supporto per più siti e molte altre. Per un elenco completo delle funzionalità supportate, vedere la panoramica del gateway applicazione

L'articolo seguente illustra come [aggiungere il firewall applicazione Web a un gateway applicazione esistente](#add-web-application-firewall-to-an-existing-application-gateway) e [creare un gateway applicazione che usa il firewall applicazione Web](#create-an-application-gateway-with-web-application-firewall).

![immagine dello scenario][scenario]

## <a name="waf-configuration-differences"></a>Differenze di configurazione dei WAF

Se è stato letto l'argomento relativo alla [creazione di un gateway applicazione con PowerShell](application-gateway-create-gateway-arm.md), si conoscono le impostazioni della SKU da configurare quando si crea un gateway applicazione. WAF fornisce impostazioni aggiuntive da definire quando si configura la SKU su un gateway applicazione. Non esistono altre modifiche aggiuntive da apportare sul gateway applicazione.

**SKU**: un gateway applicazione normale senza WAF supporta le dimensioni **Standard\_Small**, **Standard\_Medium** e **Standard\_Large**. Con l'introduzione di WAF sono disponibili due SKU aggiuntive, **WAF\_Medium** e **WAF\_Large**. WAF non è supportato sui gateway applicazione di piccole dimensioni.

**Livello**: i valori disponibili sono **Standard** o **WAF**. Quando si usa un firewall applicazione Web, è necessario scegliere **WAF** .

**Modalità** : questa impostazione è la modalità di WAF. I valori consentiti sono **Rilevamento** e **Prevenzione**. Quando il firewall WAF è configurato in modalità di rilevamento, tutte le minacce vengono archiviate in un file di log. In modalità di prevenzione, gli eventi vengono comunque registrati, ma l'autore dell'attacco riceve una risposta di mancata autorizzazione 403 dal gateway applicazione.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Aggiungere un firewall applicazione Web a un gateway applicazione esistente

Assicurarsi di usare la versione più recente di Azure PowerShell. Altre informazioni sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passaggio 1

Accedere all'account Azure.

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Passaggio 2

Selezionare la sottoscrizione da usare per questo scenario.

```powershell
Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-3"></a>Passaggio 3

Recuperare il gateway a cui si sta aggiungendo il firewall applicazione Web.

```powershell
$gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
```

### <a name="step-4"></a>Passaggio 4

Configurare la SKU del firewall applicazione Web. Le dimensioni disponibili sono **WAF\_Large** e **WAF\_Medium**. Quando viene usato Web application firewall, il livello deve essere **WAF** e la capacità deve essere confermata quando si imposta lo SKU.

```powershell
$gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
```

### <a name="step-5"></a>Passaggio 5

Configurare le impostazioni WAF nel modo definito nell'esempio seguente:

Per l'impostazione **WafMode** , i valori disponibili sono rilevamento e prevenzione.

```powershell
$gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
```

### <a name="step-6"></a>Passaggio 6

Aggiornare il gateway applicazione con le impostazioni definite nel passaggio precedente.

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

Questo comando aggiorna il gateway applicazione con il firewall applicazione Web. È consigliabile visualizzare [Diagnostica del gateway applicazione](application-gateway-diagnostics.md) per comprendere come visualizzare i log per il gateway applicazione. Per le implicazioni di sicurezza del WAF, i log devono essere esaminati a intervalli regolari per essere aggiornati sulle condizioni di sicurezza delle applicazioni web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Creare un gateway applicazione con il firewall applicazione Web

I passaggi seguenti illustrano l'intero processo di creazione di un gateway applicazione con il firewall applicazione Web.

Assicurarsi di usare la versione più recente di Azure PowerShell. Altre informazioni sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passaggio 1

Accedere ad Azure

```powershell
Login-AzureRmAccount
```

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.

### <a name="step-2"></a>Passaggio 2

Controllare le sottoscrizioni per l'account.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Passaggio 3

Scegliere le sottoscrizioni ad Azure da usare.

```powershell
Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-4"></a>Passaggio 4

Creare un gruppo di risorse. Ignorare questo passaggio se si usa un gruppo di risorse esistente.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "appgw-RG" e la località "West US".

> [!NOTE]
> Se è necessario configurare un probe personalizzato per il gateway applicazione, vedere [Creare un probe personalizzato per il gateway applicazione di Azure con PowerShell per Azure Resource Manager](application-gateway-create-probe-ps.md). Per altre informazioni, vedere l'articolo relativo a [probe personalizzati e monitoraggio dell'integrità](application-gateway-probe-overview.md) .

### <a name="step-5"></a>Passaggio 5

Assegnare un intervallo di indirizzi che la subnet possa usare per il gateway applicazione.

```powershell
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
```

> [!NOTE]
> La subnet per un'applicazione deve disporre almeno di 28 bit di maschera. Con questo valore, 10 indirizzi nella subnet rimangono disponibili per le istanze del gateway applicazione. Con una subnet più piccola, potrebbe non essere possibile aggiungere istanze del gateway applicazione in futuro.


### <a name="step-6"></a>Passaggio 6

Assegnare un intervallo di indirizzi da usare per il pool di indirizzi di back-end.

```powershell
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
```

### <a name="step-7"></a>Passaggio 7

Creare una rete virtuale con le subnet precedenti nel gruppo di risorse creato nel passaggio: [Creare il gruppo di risorse](#create-the-resource-group)

```powershell
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="step-8"></a>Passaggio 8

Recuperare la risorsa di rete virtuale e le risorse della subnet da usare nei passaggi seguenti:

```powershell
$vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
```

### <a name="step-9"></a>Passaggio 9:

Creare una risorsa IP pubblica da usare per il gateway applicazione. Questo indirizzo IP pubblico viene usato in uno dei passaggi seguenti:

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Il gateway applicazione non supporta l'uso di un indirizzo IP pubblico creato con la definizione di un'etichetta di dominio. È supportato solo un indirizzo IP pubblico con etichetta di dominio creata in modo dinamico. Se è necessario un nome DNS intuitivo per il gateway applicazione, è consigliabile usare un record cname come alias.


### <a name="step-10"></a>Passaggio 10

È necessario impostare tutti gli elementi di configurazione prima di creare il gateway applicazione. La procedura seguente consente di creare gli elementi di configurazione necessari per una risorsa del gateway applicazione.

Creare una configurazione IP del gateway applicazione che consente di configurare la subnet usata dal gateway applicazione. All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Tenere presente che ogni istanza ha un indirizzo IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
```

### <a name="step-11"></a>Passaggio 11

Configurare il pool di indirizzi IP back-end con gli indirizzi IP dei server Web back-end. Questi indirizzi IP saranno quelli che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP seguenti e aggiungere gli endpoint di indirizzi IP dell'applicazione.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
```

### <a name="step-12"></a>Passaggio 12

Caricare il certificato da usare per le risorse del pool back-end SSL abilitato.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>
```

### <a name="step-13"></a>Passaggio 13

Configurare le impostazioni http di back-end del gateway applicazione. Assegnare il certificato caricato nel passaggio precedente alle impostazioni http.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
```

### <a name="step-14"></a>Passaggio 14

Configurare la porta d indirizzo IP front-end con l'endpoint di indirizzo IP pubblico. Si tratta della porta a cui si connettono gli utenti finali.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```

### <a name="step-15"></a>Passaggio 15

Creare una configurazione IP front-end che consente di eseguire il mapping di un indirizzo IP pubblico o privato al front-end del gateway applicazione. Il passaggio seguente consente di associare l'indirizzo IP pubblico del passaggio precedente alla configurazione IP front-end.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
```

### <a name="step-16"></a>Passaggio 16

Configurare il certificato per il gateway applicazione. Il certificato viene usato per crittografare e decrittografare il traffico sul gateway applicazione.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-17"></a>Passaggio 17

Creare il listener HTTP per il gateway applicazione. Assegnare la configurazione IP, la porta e il certificato SSL front-end da usare.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
```

### <a name="step-18"></a>Passaggio 18

Creare la regola di routing del servizio di bilanciamento del carico che configura il comportamento di bilanciamento del carico. In questo esempio viene creata una regola round robin di base.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-19"></a>Passaggio 19

Configurare le dimensioni dell'istanza del gateway applicazione.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2
```

> [!NOTE]
> È possibile scegliere tra **WAF\_Medium** e **WAF\_Large**: il livello quando si usa WAF è sempre **WAF**. La capacità è qualsiasi numero compreso tra 1 e 10.

### <a name="step-20"></a>Passaggio 20

Configurare la modalità per WAF: i valori accettabili sono **Prevenzione** e **Rilevamento**.

```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="step-21"></a>Passaggio 21

Creare un gateway applicazione con tutti gli elementi di configurazione illustrati nei passaggi precedenti. Nell'esempio il gateway applicazione è denominato "appgwtest".

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

## <a name="get-application-gateway-dns-name"></a>Ottenere il nome DNS del gateway applicazione

Dopo avere creato il gateway, il passaggio successivo prevede la configurazione del front-end per la comunicazione. Quando si usa un IP pubblico, il gateway applicazione richiede un nome DNS assegnato in modo dinamico, non descrittivo. Per assicurarsi che gli utenti finali possano raggiungere il gateway applicazione, è possibile usare un record CNAME per fare riferimento all'endpoint pubblico del gateway applicazione. [Configurazione di un nome di dominio personalizzato in Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). A questo scopo, recuperare i dettagli del gateway applicazione e il nome DNS e l'IP associati, usando l'elemento PublicIPAddress collegato al gateway applicazione. Il nome DNS del gateway applicazione dovrà essere usato per creare un record CNAME che associa le due applicazioni Web a questo nome DNS. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway applicazione.

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

Per informazioni su come configurare la registrazione diagnostica, per registrare gli eventi che vengono rilevati o bloccati con il firewall applicazione Web, visitare [Registrazione diagnostica per il gateway applicazione](application-gateway-diagnostics.md)

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png



<!--HONumber=Jan17_HO4-->


