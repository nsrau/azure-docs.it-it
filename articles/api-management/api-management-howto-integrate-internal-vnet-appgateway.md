---
title: Come usare Gestione API di Azure nella rete virtuale con il gateway applicazione | Documentazione Microsoft
description: Informazioni su come installare e configurare Gestione API di Azure in una rete virtuale interna con il gateway applicazione (WAF) come front-end
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
ms.sourcegitcommit: a87349518f7494dda49e52ed160509a4ffeb7775
ms.openlocfilehash: a58ec5f6d62d2b48d6cf85d997b2deac95310505


---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrare Gestione API in una rete virtuale interna con un gateway applicazione 

##<a name="overview"> </a> Panoramica
 
Il servizio Gestione API può essere configurato in una rete virtuale nella modalità interna che lo rende accessibile solo dall'interno della rete virtuale. Il gateway applicazione di Azure è un servizio PAAS con bilanciamento del carico di livello&7;. Funge da servizio proxy inverso e fornisce tra le altre cose un web application firewall (WAF).

La combinazione di Gestione API in una rete virtuale interna con il gateway applicazione come front-end consente gli scenari seguenti:

* Uso della stessa risorsa di Gestione API sia da parte degli utenti interni che di quelli esterni.
* Uso di una singola risorsa di Gestione API e disponibilità di un sottoinsieme di API definite in Gestione API per gli utenti esterni.
* Predisposizione di un modo veloce per attivare e disattivare l'accesso a Gestione API dalla rete Internet pubblica. 

##<a name="scenario"> </a> Scenario
In questo post vedremo come usare un singolo servizio Gestione API per gli utenti sia interni che esterni e fare in modo che operi come un singolo front-end per le API sia locali che cloud. Si potrà quindi scegliere di esporre solo un sottoinsieme di tali API (evidenziato in verde) anche per l'uso esterno, usando la funzionalità PathBasedRouting disponibile nel gateway applicazione.

In questa configurazione tutte le API sono gestite solo dall'interno della rete virtuale. Gli utenti interni (evidenziati in arancione) possano accedere a tutte le API interne ed esterne. Gli utenti interni hanno il vantaggio che il traffico non esce mai su Internet e viaggia ad alta velocità grazie ai circuiti Express Route.

![route dell'URL](./media/api-management-using-with-vnet/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell usando l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [Pagina di download](https://azure.microsoft.com/downloads/).
2. Si crea una rete virtuale e subnet separate per Gestione di API e il gateway applicazione. 
3. Se si prevede di creare un server DNS personalizzato per la rete virtuale, è necessario crearlo prima di iniziare la distribuzione e assicurarsi che una macchina virtuale creata in una nuova subnet della rete virtuale possa risolvere tutti gli endpoint di servizio di Azure.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Cosa serve per ottenere integrazione tra Gestione API e il gateway applicazione?

* **Pool di server back-end:** questo è l'indirizzo IP virtuale interno del servizio Gestione API.
* **Impostazioni del pool di server back-end:** ogni pool ha impostazioni quali porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
* **Porta front-end:** porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
* **Listener** : ha una porta front-end, un protocollo (Http o Https, con distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL).
* **Regola** : associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico.
* **Probe di integrità personalizzato:** il gateway applicazione, per impostazione predefinita, usa probe basati su indirizzi IP per individuare quali server di BackendAddressPool sono attivi. Il servizio Gestione API risponde solo alle richieste che hanno l'intestazione host corretta, pertanto i probe predefiniti hanno esito negativo. È necessario definire un probe di integrità personalizzato per indicare al gateway applicazione che il servizio è attivo ed è possibile inoltrare la richiesta.
* **Certificato di dominio personalizzato:** per accedere a Gestione API da Internet è necessario eseguire il mapping CNAME del nome host al nome DNS del gateway applicazione front-end. A Gestione API vengono applicati la stessa intestazione del nome host e lo stesso certificato, in modo che, quando la richiesta arriva tramite il gateway applicazione, Gestione API la riconosca come valida e risponda.
## <a name="overview-steps"> </a> Passaggi necessari per l'integrazione di Gestione API e il gateway applicazione 

1. Creare un gruppo di risorse per Gestione risorse.
2. Creare una rete virtuale, una subnet e un indirizzo IP pubblico per il gateway applicazione. Creare un'altra subnet per Gestione API.
3. Creare un servizio Gestione API in modalità rete virtuale interna all'interno della subnet di rete virtuale creata in precedenza.
4. Aggiornare il servizio Gestione API con il nome di dominio personalizzato.
5. Creare un oggetto di configurazione del gateway applicazione.
6. Creare una risorsa del gateway applicazione.
7. Eseguire il mapping CNAME del nome host proxy di Gestione API al nome DNS pubblico della risorsa del gateway applicazione.

## <a name="create-a-resource-group-for-resource-manager"></a>Creare un gruppo di risorse per Gestione risorse

Assicurarsi di usare la versione più recente di Azure PowerShell. Altre informazioni sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passaggio 1

Accedere ad Azure

```powershell
Login-AzureRmAccount
```

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.<BR>

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

L'esempio seguente illustra come creare una rete virtuale usando Gestione risorse.

### <a name="step-1"></a>Passaggio 1

Assegnare l'intervallo di indirizzi 10.0.0.0/24 alla variabile subnet da usare per il gateway applicazione mentre si crea la rete virtuale.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgateway01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Passaggio 2

Assegnare l'intervallo di indirizzi 10.0.1.0/24 alla variabile subnet da usare per Gestione API mentre si crea la rete virtuale.

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
## <a name="create-an-api-management-service-in-internal-vnet-mode"></a>Creare un servizio Gestione API in modalità rete virtuale interna

L'esempio seguente illustra come creare un servizio Gestione API in una rete virtuale interna

### <a name="step-1"></a>Passaggio 1
Creare un oggetto rete virtuale di Gestione API usando la subnet $apimsubnetdata creata in precedenza.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>Passaggio 2
Creare un servizio Gestione API all'interno della rete virtuale

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization Contoso -AdminEmail admin@contoso.com -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Premium"
```
Dopo che il comando precedente è stato eseguito, vedere [Configurazione DNS richiesta per accedere al servizio di Gestione API sulla rete virtuale interna][api-management-using-with-internal-vnet.md#apim-dns-configuration] per l'accesso.

## <a name="update-api-management-service-with-custom-domain-name"></a>Aggiornare il servizio Gestione API con il nome di dominio personalizzato

Ora si applicherà il nome di dominio personalizzato al proxy dell'endpoint di servizio di Gestione API a cui si desidera accedere da Internet.

### <a name="step-1"></a>Passaggio 1
Caricare il certificato con la chiave privata che dimostra l'autorità di configurare un dominio personalizzato nel dominio, ad esempio `*.contoso.net`. 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file>
```

### <a name="step-2"></a>Passaggio 2
Dopo aver caricato il certificato, è possibile creare un oggetto configurazione nome host per il proxy con il nome host `api.contoso.net`, in quanto il certificato ha dimostrato l'autorità sul dominio `*.contoso.net`. 

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

Configurare il certificato per il gateway applicazione. Il certificato viene usato per crittografare e decrittografare il traffico sul gateway applicazione.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>Passaggio 5

Creare il listener HTTP per il gateway applicazione. Assegnare la configurazione IP, la porta e il certificato SSL front-end da usare.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>Passaggio 6

Qui si crea un probe personalizzato per l'endpoint del dominio proxy `ContosoApi` del servizio Gestione API. Il percorso `/status-0123456789abcdef` è un endpoint di integrità predefinito ospitato in tutti i servizi Gestione API. Il nome host `contosoapi.azure-api.net` è il nome host proxy predefinito configurato quando viene creato un servizio `contosoapi` in Azure pubblico.

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name apimproxyprobe -Protocol Https -HostName "contosoapi.azure-api.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Passaggio 7

Caricare il certificato da usare per le risorse del pool back-end SSL abilitato.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>Passaggio 8

Configurare l'impostazione **apimPoolSetting** del gateway applicazione per il traffico nel pool back-end. Questo passaggio prevede anche una configurazione di timeout per la risposta del pool back-end a una richiesta del gateway applicazione. Quando una risposta del back-end raggiunge un limite di timeout, il gateway applicazione annulla la richiesta. Questo valore è diverso dal timeout di un probe, che riguarda solo la risposta del back-end ai controlli del probe.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name apimPoolSetting -Port 443 -Protocol Https -CookieBasedAffinity Disabled -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Passaggio 9:

Configurare il pool di indirizzi IP back-end denominato **apimbackend** con un indirizzo IP virtuale interno per il servizio Gestione API creato in precedenza.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name apimbackend -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>Passaggio 10
Configurare i percorsi della regola per gli URL per i pool back-end. È possibile avere più API configurate in Gestione API, ad esempio `Echo API (/echo/), Calculator API (/calc/) etc.`, e si può scegliere di consentire l'accesso solo a `Echo API` da Internet. 

L'esempio seguente crea una semplice regola per il percorso "echo /" che instrada il traffico al back-end "apimProxyBackendPool".

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting

$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule -DefaultBackendAddressPool $apimProxyBackendPool -DefaultBackendHttpSettings $apimPoolSetting
```

Il passaggio precedente assicura che solo le richieste per il percorso "/ echo" siano consentite attraverso il gateway applicazione. Le richieste ad altre API configurate in Gestione API genereranno l'errore 404 dal gateway applicazione in caso di accesso da Internet. 

### <a name="step-11"></a>Passaggio 11

Creare un'impostazione della regola. Questo passaggio configura il gateway applicazione per l'uso del routing basato sul percorso dell'URL.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-12"></a>Passaggio 12

Configurare il numero di istanze e le dimensioni per il gateway applicazione. Qui si usa [WAF Sku][../application-gateway/application-gateway-webapplicationfirewall-overview.md] per una maggiore sicurezza della risorsa di Gestione API.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2
```

### <a name="step-13"></a>Passaggio 13

Si configura la modalità per WAF in modo che sia "Prevenzione"
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Creare il gateway applicazione

Creare un gateway applicazione con tutti gli oggetti di configurazione illustrati nei passaggi precedenti.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName apim-appGw-RG -Location "West US" -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-public-dns-name-of-application-gateway-resource"></a>Eseguire il mapping CNAME del nome host proxy di Gestione API al nome DNS pubblico della risorsa del gateway applicazione

Dopo avere creato il gateway, il passaggio successivo prevede la configurazione del front-end per la comunicazione. Quando si usa un IP pubblico, il gateway applicazione richiede un nome DNS assegnato in modo dinamico, non descrittivo. Per configurare il record CNAME per l'IP front-end, recuperare i dettagli del gateway applicazione e il nome DNS e l'IP associati usando l'elemento PublicIPAddress collegato al gateway applicazione. Il nome DNS del gateway applicazione dovrà essere usato per creare un record CNAME che associa il nome host proxy configurato in precedenza `api.contoso.net` a questo nome DNS. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway applicazione.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName apim-appGw-RG -Name publicIP01
```

##<a name="summary"> </a> Riepilogo
Il servizio Gestione API di Azure in una rete virtuale fornisce un'interfaccia a gateway singolo per gestire l'accesso a tutte le API che siano ospitate in locale o nel cloud. Si ottengono informazioni approfondite sui chi usa le API e come. L'integrazione del gateway applicazione con Gestione API offre una maggiore flessibilità sulla scelta della API a cui concedere l'accesso a Internet oltre a fornire un web application firewall come front-end all'istanza di Gestione API.

##<a name="next-steps"> </a> Passaggi successivi
* Altre informazioni sul gateway applicazione di Azure
  * [Panoramica del gateway applicazione](../application-gateway/application-gateway-introduction.md)
  * [Web application firewall del gateway applicazione](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
* Altre informazioni su Gestione API in una rete virtuale
  * [Usare Gestione API in una rete virtuale](api-management-using-with-vnet.md)





<!--HONumber=Feb17_HO1-->


