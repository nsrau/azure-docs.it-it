---
title: Come usare Gestione API di Azure in una rete virtuale con un gateway applicazione | Microsoft Docs
description: Informazioni su come installare e configurare Gestione API di Azure in una rete virtuale interna con un gateway applicazione (WAF) come front-end
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2017
ms.author: sasolank
translationtype: Human Translation
ms.sourcegitcommit: e1c3e3cdf37c806fc1ecaf8d8603746804b28dd5
ms.openlocfilehash: 3fb838e2923300e60f576367811824c85e6562fb
ms.lasthandoff: 02/23/2017


---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrare Gestione API in una rete virtuale interna con un gateway applicazione 

##<a name="overview"> </a> Panoramica
 
Il servizio Gestione API può essere configurato in una rete virtuale in modalità interna, rendendolo così accessibile solo dall'interno della rete virtuale. Il gateway applicazione di Azure è un servizio PAAS con bilanciamento del carico di livello&7;. Funge da servizio proxy inverso e offre anche un Web application firewall (WAF).

La combinazione di Gestione API in una rete virtuale interna con il gateway applicazione come front-end consente gli scenari seguenti:

* Uso della stessa risorsa di Gestione API sia da parte degli utenti interni che di quelli esterni.
* Uso di una singola risorsa di Gestione API e disponibilità di un sottoinsieme di API definite in Gestione API per gli utenti esterni.
* Predisposizione di un modo veloce per attivare e disattivare l'accesso a Gestione API dalla rete Internet pubblica. 

##<a name="scenario"> </a> Scenario
Questo articolo illustra come usare un singolo servizio Gestione API per gli utenti sia interni che esterni e fare in modo che funga da singolo front-end per le API sia locali che cloud. Verrà anche descritto come esporre solo un sottoinsieme delle API (evidenziato in verde nell'esempio) per l'utilizzo esterno con la funzionalità PathBasedRouting disponibile nel gateway applicazione.

Nel primo esempio di configurazione, tutte le API sono gestite solo dall'interno della rete virtuale. Gli utenti interni (evidenziati in arancione) possono accedere a tutte le API interne ed esterne. Il traffico non esce mai in Internet e circuiti ExpressRoute garantiscono prestazioni elevate.

![route dell'URL](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell usando l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [Pagina di download](https://azure.microsoft.com/downloads/).
2. Creare una rete virtuale e subnet separate per Gestione API e il gateway applicazione. 
3. Se si intende creare un server DNS personalizzato per la rete virtuale, eseguire tale operazione prima di iniziare la distribuzione. Controllarne il funzionamento verificando che una macchina virtuale creata in una nuova subnet della rete virtuale possa risolvere tutti gli endpoint di servizio di Azure e accedervi.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Elementi necessari per creare un'integrazione tra Gestione API e il gateway applicazione

* **Pool di server back-end:** costituisce l'indirizzo IP virtuale interno del servizio Gestione API.
* **Impostazioni del pool di server back-end:** ogni pool ha impostazioni quali porta, protocollo e affinità basata sui cookie. Queste impostazioni vengono applicate a tutti i server nel pool.
* **Porta front-end:** porta pubblica aperta sul gateway applicazione. Il traffico che raggiunge questa porta viene reindirizzato a uno dei server back-end.
* **Listener** : ha una porta front-end, un protocollo (Http o Https, con distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL).
* **Regola:** associa un listener a un pool di server back-end.
* **Probe di integrità personalizzato:** per impostazione predefinita, il gateway applicazione usa probe basati su indirizzi IP per individuare i server attivi in BackendAddressPool. Il servizio Gestione API risponde solo alle richieste con l'intestazione host corretta, quindi i probe predefiniti hanno esito negativo. È necessario definire un probe di integrità personalizzato per consentire al gateway applicazione di determinare che il servizio è attivo e deve inoltrare le richieste.
* **Certificato di dominio personalizzato:** per accedere a Gestione API da Internet è necessario creare un mapping CNAME del nome host al nome DNS del front-end del gateway applicazione. Ciò garantisce che l'intestazione del nome host e il certificato inviati al gateway applicazione e inoltrati a Gestione API siano riconoscibili come validi da Gestione API.

## <a name="overview-steps"> </a> Passaggi necessari per l'integrazione di Gestione API e il gateway applicazione 

1. Creare un gruppo di risorse per Gestione risorse.
2. Creare una rete virtuale, una subnet e un indirizzo IP pubblico per il gateway applicazione. Creare un'altra subnet per Gestione API.
3. Creare un servizio Gestione API nella subnet della rete virtuale creata in precedenza e assicurarsi di usare la modalità interna.
4. Configurare il nome di dominio personalizzato nel servizio Gestione API.
5. Creare un oggetto di configurazione del gateway applicazione.
6. Creare una risorsa gateway applicazione.
7. Creare un mapping CNAME dal nome DNS pubblico del gateway applicazione al nome host proxy di Gestione API.

## <a name="create-a-resource-group-for-resource-manager"></a>Creare un gruppo di risorse per Gestione risorse

Assicurarsi di usare la versione più recente di Azure PowerShell. Altre informazioni sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passaggio 1

Accedere ad Azure

```powershell
Login-AzureRmAccount
```

Eseguire l'autenticazione con le proprie credenziali.<BR>

### <a name="step-2"></a>Passaggio 2

Controllare le sottoscrizioni per l'account e selezionarlo.

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>Passaggio 3

Creare un gruppo di risorse. Ignorare questo passaggio se si usa un gruppo di risorse esistente.

```powershell
New-AzureRmResourceGroup -Name apim-appGw-RG -Location "West US"
```
Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creare una rete virtuale e una subnet per il gateway applicazione

L'esempio seguente illustra come creare una rete virtuale usando Resource Manager.

### <a name="step-1"></a>Passaggio 1

Assegnare l'intervallo di indirizzi 10.0.0.0/24 alla variabile subnet da usare per il gateway applicazione durante la creazione di una rete virtuale.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name apim01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Passaggio 2

Assegnare l'intervallo di indirizzi 10.0.1.0/24 alla variabile subnet da usare per Gestione API durante la creazione di una rete virtuale.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name apim01 -AddressPrefix 10.0.1.0/24
```

### <a name="step-3"></a>Passaggio 3

Creare una rete virtuale denominata **appgwvnet** nel gruppo di risorse **apim-appGw-RG** per l'area Stati Uniti occidentali usando il prefisso 10.0.0.0/16 con le subnet 10.0.0.0/24 e 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName apim-appGw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Passaggio 4

Assegnare una variabile subnet per i passaggi successivi

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Creare un servizio Gestione API in una rete virtuale configurata in modalità interna

L'esempio seguente illustra come creare un servizio Gestione API in una rete virtuale configurata per il solo accesso interno.

### <a name="step-1"></a>Passaggio 1
Creare un oggetto rete virtuale di Gestione API usando la subnet $apimsubnetdata creata in precedenza.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>Passaggio 2
Creare un servizio Gestione API all'interno della rete virtuale.

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization Contoso -AdminEmail admin@contoso.com -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Premium"
```
Dopo il completamento del comando precedente, per l'accesso vedere la [configurazione DNS necessaria per accedere al servizio Gestione API su una rete virtuale interna](api-management-using-with-internal-vnet.md#apim-dns-configuration).

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Configurare un nome di dominio personalizzato in Gestione API

### <a name="step-1"></a>Passaggio 1
Caricare il certificato con chiave privata per il dominio. Per questo esempio sarà `*.contoso.net`. 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file> -PassThru
```

### <a name="step-2"></a>Passaggio 2
Dopo aver caricato il certificato, creare un oggetto di configurazione del nome host per il proxy con il nome host `api.contoso.net`, perché il certificato di esempio fornisce l'autorità per il dominio `*.contoso.net`. 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" –PortalHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creare un indirizzo IP pubblico per la configurazione front-end

Creare una risorsa IP pubblica denominata **publicIP01** nel gruppo di risorse **apim-appGw-RG** per l'area Stati Uniti occidentali.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName apim-appGw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

All'avvio del servizio viene assegnato un indirizzo IP al gateway applicazione.

## <a name="create-application-gateway-configuration"></a>Creare la configurazione del gateway applicazione

Prima di creare il gateway applicazione, è necessario impostare tutti gli elementi di configurazione. La procedura seguente consente di creare gli elementi di configurazione necessari per una risorsa del gateway applicazione.

### <a name="step-1"></a>Passaggio 1

Creare una configurazione IP del gateway applicazione denominata **gatewayIP01**. All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Tenere presente che ogni istanza ha un indirizzo IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Passaggio 2

Configurare la porta d indirizzo IP front-end con l'endpoint di indirizzo IP pubblico. Si tratta della porta a cui si connettono gli utenti finali.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```
### <a name="step-3"></a>Passaggio 3

Configurare l'indirizzo IP front-end con l'endpoint di indirizzo IP pubblico.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Passaggio 4

Configurare il certificato per il gateway applicazione, che viene usato per decrittografare e ricrittografare il traffico gestito.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>Passaggio 5

Creare il listener HTTP per il gateway applicazione. Assegnare la configurazione IP, la porta e il certificato SSL front-end da usare.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>Passaggio 6

Creare un probe personalizzato per l'endpoint del dominio proxy `ContosoApi` del servizio Gestione API. Il percorso `/status-0123456789abcdef` è un endpoint di integrità predefinito ospitato in tutti i servizi Gestione API. Il nome host `contosoapi.azure-api.net` è il nome host proxy predefinito configurato quando viene creato un servizio `contosoapi` nell'ambiente Azure pubblico.

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name apimproxyprobe -Protocol Https -HostName "contosoapi.azure-api.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Passaggio 7

Caricare il certificato da usare per le risorse del pool back-end abilitate per SSL.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>Passaggio 8

Configurare le impostazioni di back-end HTTP per il gateway applicazione. Questo passaggio include l'impostazione di un limite di timeout per la richiesta del back-end, raggiunto il quale verrà annullata. Questo valore è diverso dal timeout del probe.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name apimPoolSetting -Port 443 -Protocol Https -CookieBasedAffinity Disabled -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Passaggio 9:

Configurare il pool di indirizzi IP back-end denominato **apimbackend** con l'indirizzo IP virtuale interno del servizio Gestione API creato in precedenza.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name apimbackend -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>Passaggio 10
Configurare i percorsi della regola per gli URL per i pool back-end. È così possibile selezionare solo alcune API di Gestione API da esporre al pubblico. Se esistono `Echo API (/echo/), Calculator API (/calc/) etc.`, ad esempio, è possibile rendere accessibile da Internet solo `Echo API`. 

L'esempio seguente crea una semplice regola per il percorso "/echo/" che instrada il traffico al back-end "apimProxyBackendPool".

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting

$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule -DefaultBackendAddressPool $apimProxyBackendPool -DefaultBackendHttpSettings $apimPoolSetting
```

Il passaggio precedente assicura che solo le richieste per il percorso "/echo" siano consentite attraverso il gateway applicazione. Le richieste ad altre API configurate in Gestione API genereranno errori 404 del gateway applicazione in caso di accesso da Internet. 

### <a name="step-11"></a>Passaggio 11

Creare un'impostazione della regola per il gateway applicazione per usare il routing basato su percorso URL.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-12"></a>Passaggio 12

Configurare il numero di istanze e le dimensioni per il gateway applicazione. In questo caso si usa lo [SKU del WAF](../application-gateway/application-gateway-webapplicationfirewall-overview.md) per aumentare la sicurezza della risorsa di Gestione API.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2
```

### <a name="step-13"></a>Passaggio 13

Configurare il Web application firewall in modalità "prevenzione".
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Creare il gateway applicazione

Creare un gateway applicazione con tutti gli oggetti di configurazione illustrati nei passaggi precedenti.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName apim-appGw-RG -Location "West US" -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Creare un mapping CNAME del nome host proxy di Gestione API al nome DNS pubblico della risorsa gateway applicazione

Dopo avere creato il gateway, il passaggio successivo prevede la configurazione del front-end per la comunicazione. Quando si usa un IP pubblico, il gateway applicazione richiede un nome DNS assegnato in modo dinamico, che potrebbe non essere facile da usare. 

Il nome DNS del gateway applicazione dovrà essere usato per creare un record CNAME che associa il nome host proxy di Gestione API (negli esempi precedenti, `api.contoso.net`) a questo nome DNS. Per configurare il record CNAME per l'IP front-end, recuperare i dettagli del gateway applicazione e il nome DNS e l'IP associati usando l'elemento PublicIPAddress. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName apim-appGw-RG -Name publicIP01
```

##<a name="summary"> </a> Riepilogo
Il servizio Gestione API di Azure configurato in una rete virtuale offre un'interfaccia a gateway singolo per tutte le API, che siano ospitate in locale o nel cloud. L'integrazione del gateway applicazione con Gestione API offre la possibilità di rendere accessibili su Internet determinate API in modo selettivo, nonché di fornire un Web application firewall come front-end all'istanza di Gestione API.

##<a name="next-steps"> </a> Passaggi successivi
* Altre informazioni sul gateway applicazione di Azure
  * [Panoramica del gateway applicazione](../application-gateway/application-gateway-introduction.md)
  * [Web application firewall del gateway applicazione](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
* Altre informazioni su Gestione API e reti virtuali
  * [Usare Gestione API in una rete virtuale](api-management-using-with-vnet.md)



