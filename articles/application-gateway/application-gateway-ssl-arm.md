---
title: Configurare un gateway applicazione per l&quot;offload SSL usando Azure Resource Manager | Microsoft Docs
description: Questa pagina fornisce istruzioni per creare un gateway applicazione con offload SSL usando Gestione risorse di Azure
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 3c3681e0-f928-4682-9d97-567f8e278e13
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: ee8cfffdbf054b4251ed269745f6b9ee5a5e6c64
ms.openlocfilehash: a6100af0ba746ea244a2ccc6e1ddf843a8be416e


---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Configurare un gateway applicazione per l'offload SSL con Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-ssl-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-ssl-arm.md)
> * [PowerShell per Azure classico](application-gateway-ssl.md)

Il gateway applicazione di Azure può essere configurato per terminare la sessione Secure Sockets Layer (SSL) nel gateway ed evitare costose attività di decrittografia SSL nella Web farm. L'offload SSL semplifica anche la configurazione e la gestione del server front-end dell'applicazione Web.

## <a name="before-you-begin"></a>Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell usando l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [Pagina di download](https://azure.microsoft.com/downloads/).
2. Creare una rete virtuale e una subnet per il gateway applicazione. Assicurarsi che nessuna macchina virtuale o distribuzione cloud stia usando la subnet. Il gateway applicazione deve essere da solo in una subnet di rete virtuale.
3. È necessario che i server configurati per l'uso del gateway applicazione esistano oppure che i relativi endpoint siano stati creati nella rete virtuale o con un indirizzo IP/VIP pubblico assegnato.

## <a name="what-is-required-to-create-an-application-gateway"></a>Elementi necessari per creare un gateway applicazione

* **Pool di server back-end:** elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP/VIP pubblici.
* **Impostazioni del pool di server back-end:** ogni pool ha impostazioni come porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
* **Porta front-end:** porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
* **Listener** : ha una porta front-end, un protocollo (Http o Https, queste impostazioni fanno distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL).
* **Regola** : associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico. È attualmente supportata solo la regola *basic* . La regola *basic* è una distribuzione del carico di tipo round robin.

**Note aggiuntive sulla configurazione**

Per la configurazione dei certificati SSL, il protocollo in **HttpListener** deve essere sostituito con *Https* (distinzione tra maiuscole e minuscole). L'elemento **SslCertificate** viene aggiunto ad **HttpListener** con il valore della variabile configurato per il certificato SSL. La porta front-end deve essere impostata su 443.

**Per abilitare l'affinità basata sui cookie**: è possibile configurare un gateway applicazione per fare in modo che una richiesta proveniente da una sessione client sia sempre diretta alla stessa macchina virtuale nella Web farm. Questo scenario viene realizzato aggiungendo un cookie di sessione che consente al gateway di indirizzare il traffico in modo appropriato. Per abilitare l'affinità basata sui cookie, impostare **CookieBasedAffinity** su *Enabled* nell'elemento **BackendHttpSettings**.

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

La differenza tra l'uso del modello di distribuzione classica di Azure e di Azure Resource Manager risiede nell'ordine in cui vengono creati un gateway applicazione e gli elementi da configurare.

Con Resource Manager, tutti gli elementi di un gateway applicazione vengono configurati singolarmente e quindi combinati per creare una risorsa gateway applicazione.

Per creare un gateway applicazione, seguire questa procedura:

1. Creare un gruppo di risorse per Gestione risorse
2. Creare una rete virtuale, una subnet e un indirizzo IP pubblico per il gateway applicazione
3. Creare un oggetto di configurazione gateway applicazione
4. Creare una risorsa del gateway applicazione

## <a name="create-a-resource-group-for-resource-manager"></a>Creare un gruppo di risorse per Gestione risorse

Assicurarsi di passare alla modalità PowerShell per usare i cmdlet di Gestione risorse di Azure. Altre informazioni sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passaggio 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Passaggio 2

Controllare le sottoscrizioni per l'account.

```powershell
Get-AzureRmSubscription
```

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.

### <a name="step-3"></a>Passaggio 3

Scegliere le sottoscrizioni ad Azure da usare.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Passaggio 4

Creare un gruppo di risorse. Ignorare questo passaggio se si usa un gruppo di risorse esistente.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure Resource Manager richiede che tutti i gruppi di risorse specifichino una località. Questa impostazione viene usata come località predefinita per le risorse presenti nel gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato **appgw-RG** e l'area **West US**.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creare una rete virtuale e una subnet per il gateway applicazione

L'esempio seguente illustra come creare una rete virtuale usando Gestione risorse:

### <a name="step-1"></a>Passaggio 1

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Questo esempio assegna l'intervallo di indirizzi 10.0.0.0/24 a una variabile di subnet da usare per creare una rete virtuale.

### <a name="step-2"></a>Passaggio 2

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

Questo esempio crea una rete virtuale denominata **appgwvnet** nel gruppo di risorse **appgw-rg** per l'area Stati Uniti occidentali usando il prefisso 10.0.0.0/16 con subnet 10.0.0.0/24.

### <a name="step-3"></a>Passaggio 3

```powershell
$subnet = $vnet.Subnets[0]
```

Questo esempio assegna l'oggetto subnet alla variabile $subnet per i passaggi successivi.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creare un indirizzo IP pubblico per la configurazione front-end

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Questo esempio crea una risorsa IP pubblica **publicIP01** nel gruppo di risorse **appgw-rg** per l'area Stati Uniti occidentali.

## <a name="create-an-application-gateway-configuration-object"></a>Creare un oggetto di configurazione gateway applicazione

### <a name="step-1"></a>Passaggio 1

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Questo esempio crea una configurazione IP del gateway applicazione denominata **gatewayIP01**. All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Tenere presente che ogni istanza ha un indirizzo IP.

### <a name="step-2"></a>Passaggio 2

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50
```

Questo esempio configura il pool di indirizzi IP back-end denominato **pool01** con gli indirizzi IP **134.170.185.46**, **134.170.188.221**, **134.170.185.50**. Tali valori saranno gli indirizzi IP che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP dell'esempio precedente con gli indirizzi IP degli endpoint dell'applicazione Web.

### <a name="step-3"></a>Passaggio 3

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled
```

Questo esempio configura l'impostazione **poolsetting01** del gateway applicazione per il traffico di rete con bilanciamento del carico nel pool back-end.

### <a name="step-4"></a>Passaggio 4

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443
```

Questo esempio configura la porta IP front-end denominata **frontendport01** per l'endpoint IP pubblico.

### <a name="step-5"></a>Passaggio 5

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password "<password>"
```

Questo esempio configura il certificato usato per la connessione SSL. Il certificato deve essere in formato PFX e la password deve essere compresa tra 4 e 12 caratteri.

### <a name="step-6"></a>Passaggio 6

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

Questo esempio crea la configurazione IP front-end denominata **fipconfig01** e associa l'indirizzo IP pubblico alla configurazione IP front-end.

### <a name="step-7"></a>Passaggio 7

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
```

Questo esempio crea il listener denominato **listener01** e associa la porta front-end al certificato e alla configurazione IP front-end.

### <a name="step-8"></a>Passaggio 8

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Questo esempio crea la regola di routing del servizio di bilanciamento del carico denominata **rule01** che configura il comportamento di bilanciamento del carico.

### <a name="step-9"></a>Passaggio 9:

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Questo esempio configura le dimensioni dell'istanza del gateway applicazione.

> [!NOTE]
> Il valore predefinito per *InstanceCount* è 2, con un valore massimo pari a 10. Il valore predefinito per *GatewaySize* è Medium. È possibile scegliere tra Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Creare un gateway applicazione usando New-AzureApplicationGateway

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert
```

Questo esempio crea un gateway applicazione con tutti gli elementi di configurazione illustrati nei passaggi precedenti. Nell'esempio il gateway applicazione è denominato **appgwtest**.

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

Per configurare un gateway applicazione da usare con un servizio di bilanciamento del carico interno, vedere [Creare un gateway applicazione con un servizio di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

* [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)




<!--HONumber=Nov16_HO3-->


