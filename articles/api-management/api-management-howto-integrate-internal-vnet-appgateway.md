---
title: Come usare Gestione API di Azure in una rete virtuale con un gateway applicazione | Microsoft Docs
description: Informazioni su come installare e configurare Gestione API di Azure in una rete virtuale interna con un gateway applicazione (WAF) come front-end
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: sasolank
ms.openlocfilehash: da195f414da032b5274a9dc1a184b66094f245f2
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55493446"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrare Gestione API in una rete virtuale interna con un gateway applicazione

## <a name="overview"></a> Panoramica

Il servizio Gestione API può essere configurato in una rete virtuale in modalità interna, per renderlo accessibile solo internamente alla rete virtuale. Il gateway applicazione di Azure è un servizio PAAS con bilanciamento del carico di livello 7. Funge da servizio proxy inverso e offre anche un Web application firewall (WAF).

La combinazione di Gestione API in una rete virtuale interna con il gateway applicazione come front-end consente gli scenari seguenti:

* Uso della stessa risorsa di Gestione API sia da parte degli utenti interni che di quelli esterni.
* Uso di una singola risorsa di Gestione API e disponibilità di un sottoinsieme di API definite in Gestione API per gli utenti esterni.
* Predisposizione di un modo veloce per attivare e disattivare l'accesso a Gestione API dalla rete Internet pubblica.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi descritti in questo articolo, è necessario quanto segue:

* Una sottoscrizione di Azure attiva.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certificati: PFX e CER per il nome host dell'API e PFX per il nome host del portale per sviluppatori.

## <a name="scenario"> </a> Scenario

Questo articolo illustra come usare un singolo servizio Gestione API per gli utenti sia interni che esterni e fare in modo che funga da singolo front-end per le API sia locali che cloud. Verrà anche descritto come esporre solo un sottoinsieme delle API (evidenziato in verde nell'esempio) per l'utilizzo esterno con la funzionalità di routing disponibile nel gateway applicazione.

Nel primo esempio di configurazione, tutte le API sono gestite solo dall'interno della rete virtuale. Gli utenti interni (evidenziati in arancione) possono accedere a tutte le API interne ed esterne. Il traffico non esce mai in Internet e circuiti ExpressRoute garantiscono prestazioni elevate.

![route dell'URL](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a> Prima di iniziare

* Assicurarsi di usare la versione più recente di Azure PowerShell. Altre informazioni sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/powershell-azure-resource-manager).

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Elementi necessari per creare un'integrazione tra Gestione API e il gateway applicazione

* **Pool di server back-end:** costituisce l'indirizzo IP virtuale interno del servizio Gestione API.
* **Impostazioni del pool di server back-end:** Ogni pool ha impostazioni quali porta, protocollo e affinità basate sui cookie. Queste impostazioni vengono applicate a tutti i server nel pool.
* **Porta front-end:** questa è la porta pubblica aperta sul gateway applicazione. Il traffico che raggiunge questa porta viene reindirizzato a uno dei server back-end.
* **Listener:** ha una porta front-end, un protocollo (Http o Https, con distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL).
* **Regola:** associa un listener a un pool di server back-end.
* **Probe di integrità personalizzato:** il gateway applicazione, per impostazione predefinita, usa probe basati su indirizzi IP per individuare quali server di BackendAddressPool sono attivi. Poiché il servizio Gestione API risponde solo alle richieste dotate di intestazione host corretta, i probe predefiniti non riescono. È necessario definire un probe di integrità personalizzato per consentire al gateway applicazione di determinare che il servizio è attivo e deve inoltrare le richieste.
* **Certificati di dominio personalizzato:** per accedere a Gestione API da Internet è necessario creare un mapping CNAME del nome host al nome DNS del front-end del gateway applicazione. Ciò garantisce che l'intestazione del nome host e il certificato inviati al gateway applicazione e inoltrati a Gestione API siano riconoscibili come validi da Gestione API. In questo esempio vengono usati due certificati, uno per il back-end e uno per il portale per sviluppatori.  

## <a name="overview-steps"> </a> Passaggi necessari per l'integrazione di Gestione API e il gateway applicazione

1. Creare un gruppo di risorse per Gestione risorse.
2. Creare una rete virtuale, una subnet e un indirizzo IP pubblico per il gateway applicazione. Creare un'altra subnet per Gestione API.
3. Creare un servizio Gestione API nella subnet della rete virtuale creata in precedenza e assicurarsi di usare la modalità interna.
4. Configurare un nome di dominio personalizzato nel servizio Gestione API.
5. Creare un oggetto di configurazione del gateway applicazione.
6. Creare una risorsa gateway applicazione.
7. Creare un mapping CNAME dal nome DNS pubblico del gateway applicazione al nome host proxy di Gestione API.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Esposizione del portale per sviluppatori all'esterno tramite il gateway applicazione

In questa guida verrà esposto il **portale per sviluppatori** anche a destinatari esterni tramite il gateway applicazione. È necessario completare passaggi aggiuntivi per creare il listener, il probe, le impostazioni e le regole del portale per sviluppatori. Tutti i dettagli vengono forniti nei rispettivi passaggi.

> [!WARNING]
> Se si usa Azure AD o un'autenticazione di terze parti, attivare la funzionalità [affinità di sessione basata su cookie](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity) nel gateway applicazione.

## <a name="create-a-resource-group-for-resource-manager"></a>Creare un gruppo di risorse per Gestione risorse

### <a name="step-1"></a>Passaggio 1

Accedere ad Azure

```powershell
Login-AzureRmAccount
```

Eseguire l'autenticazione con le proprie credenziali.

### <a name="step-2"></a>Passaggio 2

Selezionare la sottoscrizione da usare.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzureRmSubscription -Subscriptionid $subscriptionId | Select-AzureRmSubscription
```

### <a name="step-3"></a>Passaggio 3

Creare un gruppo di risorse. Ignorare questo passaggio se si usa un gruppo di risorse esistente.

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzureRmResourceGroup -Name $resGroupName -Location $location
```

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creare una rete virtuale e una subnet per il gateway applicazione

L'esempio seguente illustra come creare una rete virtuale usando Resource Manager.

### <a name="step-1"></a>Passaggio 1

Assegnare l'intervallo di indirizzi 10.0.0.0/24 alla variabile subnet da usare per il gateway applicazione durante la creazione di una rete virtuale.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Passaggio 2

Assegnare l'intervallo di indirizzi 10.0.1.0/24 alla variabile subnet da usare per Gestione API durante la creazione di una rete virtuale.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Passaggio 3

Creare una rete virtuale denominata **appgwvnet** nel gruppo di risorse **apim-appGw-RG** per l'area Stati Uniti occidentali. Usare il prefisso 10.0.0.0/16 con le subnet 10.0.0.0/24 e 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Passaggio 4

Assegnare una variabile subnet per i passaggi successivi

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Creare un servizio Gestione API in una rete virtuale configurata in modalità interna

L'esempio seguente illustra come creare un servizio Gestione API in una rete virtuale configurata per il solo accesso interno.

### <a name="step-1"></a>Passaggio 1

Creare un oggetto rete virtuale di Gestione API usando la subnet $apimsubnetdata creata in precedenza.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location $location -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Passaggio 2

Creare un servizio Gestione API all'interno della rete virtuale.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzureRmApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Dopo il completamento del comando precedente, per l'accesso vedere la [configurazione DNS necessaria per accedere al servizio Gestione API su una rete virtuale interna](api-management-using-with-internal-vnet.md#apim-dns-configuration). Questo passaggio può richiedere più di mezz'ora.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Configurare un nome di dominio personalizzato in Gestione API

### <a name="step-1"></a>Passaggio 1

Caricare i certificati con chiavi private per i domini. In questo esempio vengono usati `api.contoso.net` e `portal.contoso.net`.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName $resGroupName -Name $apimServiceName -HostnameType "Proxy" -PfxPath $gatewayCertPfxPath -PfxPassword $gatewayCertPfxPassword -PassThru
$certPortalUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName $resGroupName -Name $apimServiceName -HostnameType "Proxy" -PfxPath $portalCertPfxPath -PfxPassword $portalCertPfxPassword -PassThru
```

### <a name="step-2"></a>Passaggio 2

Al termine del caricamento dei certificati, creare oggetti di configurazione dei nomi host per il proxy e il portale.  

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname $gatewayHostname
$portalHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certPortalUploadResult.Thumbprint -Hostname $portalHostname
$result = Set-AzureRmApiManagementHostnames -Name $apimServiceName -ResourceGroupName $resGroupName –PortalHostnameConfiguration $portalHostnameConfig -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creare un indirizzo IP pubblico per la configurazione front-end

Creare una risorsa IP pubblica denominata **publicIP01** nel gruppo di risorse.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

All'avvio del servizio viene assegnato un indirizzo IP al gateway applicazione.

## <a name="create-application-gateway-configuration"></a>Creare la configurazione del gateway applicazione

Prima di creare il gateway applicazione, è necessario impostare tutti gli elementi di configurazione. La procedura seguente consente di creare gli elementi di configurazione necessari per una risorsa del gateway applicazione.

### <a name="step-1"></a>Passaggio 1

Creare una configurazione IP del gateway applicazione denominata **gatewayIP01**. All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Tenere presente che ogni istanza ha un indirizzo IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Passaggio 2

Configurare la porta d indirizzo IP front-end con l'endpoint di indirizzo IP pubblico. Si tratta della porta a cui si connettono gli utenti finali.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Passaggio 3

Configurare l'indirizzo IP front-end con l'endpoint di indirizzo IP pubblico.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Passaggio 4

Configurare i certificati per il gateway applicazione, che verranno usati per decrittografare e ricrittografare il traffico gestito.

```powershell
$certPwd = ConvertTo-SecureString $gatewayCertPfxPassword -AsPlainText -Force
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortalPwd = ConvertTo-SecureString $portalCertPfxPassword -AsPlainText -Force
$certPortal = New-AzureRmApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Passaggio 5

Creare i listener HTTP per il gateway applicazione. Assegnare ai listener HTTP la configurazione IP, la porta e i certificati SSL del front-end.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Passaggio 6

Creare probe personalizzati per l'endpoint del dominio proxy `ContosoApi` del servizio Gestione API. Il percorso `/status-0123456789abcdef` è un endpoint di integrità predefinito ospitato in tutti i servizi Gestione API. Impostare `api.contoso.net` come nome host probe personalizzato per assicurare la protezione con il certificato SSL.

> [!NOTE]
> Il nome host `contosoapi.azure-api.net` è il nome host proxy predefinito configurato quando viene creato un servizio `contosoapi` nell'ambiente Azure pubblico.
>

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzureRmApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Passaggio 7

Caricare il certificato da usare per le risorse del pool back-end abilitate per SSL. È lo stesso certificato configurato nel passaggio 4.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Passaggio 8

Configurare le impostazioni di back-end HTTP per il gateway applicazione. Questo passaggio include l'impostazione di un limite di timeout per la richiesta del back-end, raggiunto il quale la richiesta verrà annullata. Questo valore è diverso dal timeout del probe.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Passaggio 9:

Configurare il pool di indirizzi IP back-end denominato **apimbackend** con l'indirizzo IP virtuale interno del servizio Gestione API creato in precedenza.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Passaggio 10

Creare regole per il gateway applicazione per l'uso del routing di base.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Modificare -RuleType e il routing per limitare l'accesso a determinate pagine del portale per sviluppatori.

### <a name="step-11"></a>Passaggio 11

Configurare il numero di istanze e le dimensioni per il gateway applicazione. In questo esempio si usa [SKU WAF](../application-gateway/application-gateway-webapplicationfirewall-overview.md) per aumentare la sicurezza della risorsa di Gestione API.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Passaggio 12

Configurare il Web application firewall in modalità "prevenzione".

```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Creare il gateway applicazione

Creare un gateway applicazione con tutti gli oggetti di configurazione illustrati nei passaggi precedenti.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Creare un mapping CNAME del nome host proxy di Gestione API al nome DNS pubblico della risorsa gateway applicazione

Dopo avere creato il gateway, il passaggio successivo prevede la configurazione del front-end per la comunicazione. Quando si usa un IP pubblico, il gateway applicazione richiede un nome DNS assegnato in modo dinamico, che potrebbe non essere facile da usare.

Il nome DNS del gateway applicazione dovrà essere usato per creare un record CNAME che associa il nome host proxy di Gestione API (negli esempi precedenti, `api.contoso.net`) a questo nome DNS. Per configurare il record CNAME per l'IP front-end, recuperare i dettagli del gateway applicazione e il nome DNS e l'IP associati usando l'elemento PublicIPAddress. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a> Riepilogo
Il servizio Gestione API di Azure configurato in una rete virtuale offre un'interfaccia a gateway singolo per tutte le API, che siano ospitate in locale o nel cloud. L'integrazione del gateway applicazione con Gestione API offre la possibilità di rendere accessibili su Internet determinate API in modo selettivo, nonché di fornire un Web application firewall come front-end all'istanza di Gestione API.

##<a name="next-steps"> </a> Passaggi successivi
* Altre informazioni sul gateway applicazione di Azure
  * [Panoramica del gateway applicazione](../application-gateway/application-gateway-introduction.md)
  * [Web application firewall del gateway applicazione](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Creare un gateway applicazione con il routing basato sul percorso](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Altre informazioni su Gestione API e reti virtuali
  * [Uso del servizio Gestione API di Azure con una rete virtuale interna](api-management-using-with-internal-vnet.md)
  * [Usare Gestione API in una rete virtuale](api-management-using-with-vnet.md)
